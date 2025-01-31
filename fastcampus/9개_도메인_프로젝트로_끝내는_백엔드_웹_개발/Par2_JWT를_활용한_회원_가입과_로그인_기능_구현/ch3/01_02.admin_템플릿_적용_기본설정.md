### admin 페이지는 타임리프를 사용해서 해줍니다.  

많은 양의 데이터를 조회하는 기능을 만들 때에는 데이터의 범위를 한정적으로 잡아주는 것이 중요합니다.  
GROUP BY사용시 전체 데이터에 미리 범위를 한정적으로 잡아주어야 한다. 안그러면 전체 데이터가 GROUP BY되면서 HAVING절에 조건이 잡히면,
이미 전체 데이터가 GROUP BY 되었기 때문에, 성능이 느려집니다.  

AdminController
```java
package org.sangyunpark99.admin.ui;

import lombok.RequiredArgsConstructor;
import org.sangyunpark99.admin.ui.query.UserStatsQueryRepository;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.servlet.ModelAndView;

@RestController
@RequestMapping("/admin")
@RequiredArgsConstructor
public class AdminController {

    private final UserStatsQueryRepository userStatsQueryRepository;

    @GetMapping("/index")
    public ModelAndView index() {
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.setViewName("index");

        modelAndView.addObject("result", userStatsQueryRepository.getDailyRegisterUserStats(100));

        return modelAndView;
    }

}

```

UserStatsQueryRepositoryImpl
```java
package org.sangyunpark99.admin.repository;

import com.querydsl.core.types.Projections;
import com.querydsl.jpa.impl.JPAQueryFactory;
import lombok.RequiredArgsConstructor;
import org.sangyunpark99.admin.ui.dto.GetDailyRegisterUserResponseDto;
import org.sangyunpark99.admin.ui.query.UserStatsQueryRepository;
import org.sangyunpark99.common.TimeCalculator;
import org.sangyunpark99.user.repository.entity.QUserEntity;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
@RequiredArgsConstructor
public class UserStatsQueryRepositoryImpl implements UserStatsQueryRepository {

    private final JPAQueryFactory queryFactory;
    private static final QUserEntity userEntity = QUserEntity.userEntity;


    @Override
    public List<GetDailyRegisterUserResponseDto> getDailyRegisterUserStats(int beforeDays) {
        return queryFactory
                .select(
                        Projections.fields(
                                GetDailyRegisterUserResponseDto.class,
                                userEntity.regDt.as("date"),
                                userEntity.count().as("count")
                        )
                ).from(userEntity)
                .where(userEntity.regDt.after(TimeCalculator.getDateDaysAgo(beforeDays)))
                .groupBy(userEntity.regDt)
                .orderBy(userEntity.regDt.asc())
                .fetch();
    }
}
```


