### 기능 구현

AOP와 annotation을 이용해서 구현

mysql보단 redis나 memcached같은 것들을 사용해도 됩니다.  

entity
```java
package org.sangyunpark99.common.idempotency.repository.entity;

import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import org.sangyunpark99.common.idempotency.Idempotency;
import org.sangyunpark99.common.utils.ResponseObjectMapper;

@Entity
@Table(name = "community_idempotency")
@NoArgsConstructor
@AllArgsConstructor
public class IdempotencyEntity {

    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(unique = true, nullable = false)
    private String idempotencyKey;

    @Getter
    @Column(nullable = false)
    private String response;

    public IdempotencyEntity(Idempotency idempotency) {
        this.idempotencyKey = idempotency.getKey();
        this.response = ResponseObjectMapper.toStringResponse(idempotency.getResponse());
    }
}
```

ObjectMapper 
```java
package org.sangyunpark99.common.utils;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.sangyunpark99.common.ui.Response;

public class ResponseObjectMapper {

    private static final ObjectMapper objectMapper = new ObjectMapper();

    private ResponseObjectMapper() {

    }

    public static Response toResponseObject(String response) {
        try {
            return objectMapper.readValue(response, Response.class);
        } catch (JsonProcessingException e) {
            return null;
        }
    }

    public static String toStringResponse(Response<?> response) {
        try {
            return objectMapper.writeValueAsString(response);
        }catch (JsonProcessingException e) {
            return null;
        }
    }

}
```

repository 구현
```java
package org.sangyunpark99.common.idempotency.repository;

import lombok.RequiredArgsConstructor;
import org.sangyunpark99.common.idempotency.IdemPotencyRepository;
import org.sangyunpark99.common.idempotency.Idempotency;
import org.sangyunpark99.common.idempotency.JpaIdempotencyRepository;
import org.sangyunpark99.common.idempotency.repository.entity.IdempotencyEntity;
import org.springframework.stereotype.Repository;

import java.util.Optional;

@Repository
@RequiredArgsConstructor
public class JpaIdempotencyRepositoryImpl implements IdemPotencyRepository {

    private final JpaIdempotencyRepository jpaIdempotencyRepository;

    @Override
    public Idempotency getByKey(String key) {
        Optional<IdempotencyEntity> idempotencyEntity = jpaIdempotencyRepository.findByIdempotencyKey(key);
        return idempotencyEntity.map(IdempotencyEntity::toIdempotency).orElse(null);
    }

    @Override
    public void save(Idempotency idempotency) {
        jpaIdempotencyRepository.save(new IdempotencyEntity(idempotency));
    }
}

```

### AOP를 사용해서 API가 호출될때마다 사용을 해줍니다.
```java
package org.sangyunpark99.common.idempotency;

import jakarta.servlet.http.HttpServletRequest;
import lombok.RequiredArgsConstructor;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.sangyunpark99.common.ui.Response;
import org.springframework.stereotype.Component;

@Aspect
@Component
@RequiredArgsConstructor
public class IdempotencyAspect {

    private final IdemPotencyRepository idemPotencyRepository;
    private final HttpServletRequest request;

    @Around("@annotation(Idempotent)")
    public Object checkIdempotency(ProceedingJoinPoint joinPoint) throws Throwable {
        String idempotencyKey =request.getHeader("Idempotency-Key");
        if(idempotencyKey == null) {
            return joinPoint.proceed();
        }

        Idempotency idempotency = idemPotencyRepository.getByKey(idempotencyKey);

        if(idempotency != null) {
            return idempotency.getResponse(); // 로직을 수행하지 않고, 저장된 응답값 반환
        }

        Object result = joinPoint.proceed(); // 로직을 수행

        // 아래 부분은 Controller가 응답이 된 후, 실행된다.
        Idempotency newIdempotency = new Idempotency(idempotencyKey, (Response<?>) result);

        idemPotencyRepository.save(newIdempotency);

        return result;
    }
}
```

커스텀 어노테이션을 사용하기 위해 아래와 같이 선언해줍니다.
```java
    @PostMapping("/like")
    @Idempotent
    public Response<Void> likePost(@RequestBody LikePostRequestDto dto) {
        postService.likePost(dto);
        return Response.ok(null);
    }
```

같은 멱등성 키를 가진 api를 두번 호출하면, 서비스 로직이 실행되는 것이 아닌, 기존 api응답을 반환해준다.  
같은 결과가 나오는 api는 굳이 자원 낭비할 필요 없이 이전에 보냈던 값을 다시 보내면 된다.