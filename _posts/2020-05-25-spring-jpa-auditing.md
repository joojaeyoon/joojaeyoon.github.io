---
title: "Spring 프로젝트에 JPA Auditing 적용하기"
excerpt: "JPA Auditing으로 created_at, updated_at 자동화하기"

header:
  teaser: /assets/images/teaser/spring-logo.png
  overlay_image: /assets/images/splash/book.jpg
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
  overlay_filter: 0.5

categories:
  - Spring
tags:
  - spring
  - jpa
---

> 책 [스프링 부트와 AWS로 혼자 구현하는 웹 서비스](http://www.yes24.com/Cooperate/Naver/welcomeNaver.aspx?pageNo=1&goodsNo=83849117)를 참고해 작성한 글 입니다.

# Auditing

Spring Data는 누군가 entity를 생성 또는 수정할 때 변화를 감지하고 그에 대한 시간 또는 사용자를 감지할 수 있는 기능을 제공한다.

## Annotation 기반 auditing

Spring Data에서 지원하는 annotation들은 어떤 사용자가 entity를 생성 또는 수정했는지 감지하는 `@CreatedBy`, `@LastModifiedBy`와 언제 생성 또는 수정되었는지 감지하는 `@CreatedDate`, `@LastModifiedDate`가 있다.

```java
// Example
class Customer {

  @CreatedBy
  private User user;

  @CreatedDate
  private DateTime createdDate;

  // … further properties omitted
}
```

# 프로젝트 적용

## Entity 생성

우선 JPA Auditing을 적용하기 위해 프로젝트를 생성하고, `Post` entity와 `PostRepository`를 생성해준다.

```java
// src/main/java/dev/jooz/springboot/domain/post/Post.java

import dev.jooz.springboot.domain.BaseTimeEntity;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

import javax.persistence.*;

@Getter
@NoArgsConstructor
@Entity
public class Post {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String title;

    @Column(nullable = false)
    private String content;

    @Builder
    public Post(String title,String content){
        this.title=title;
        this.content=content;
    }
}
```

```java
// src/main/java/dev/jooz/springboot/domain/post/PostRePository.java

import org.springframework.data.jpa.repository.JpaRepository;

public interface PostRepository extends JpaRepository<Post,Long> {
}

```

## AuditorEntity 생성

Auditing을 사용하기 위해 AuditorEntity를 생성하고, `@CreatedDate`, `@LastModifiedDate`를 사용하여 각각 LocalDateTime 필드를 생성해준다.

```java
// src/main/java/dev/jooz/springboot/domain/AuditorEntity.java

import lombok.Getter;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.EntityListeners;
import javax.persistence.MappedSuperclass;
import java.time.LocalDateTime;

@Getter
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public abstract class AuditorEntity {
    @CreatedDate
    private LocalDateTime createdDate;

    @LastModifiedDate
    private LocalDateTime modifiedDate;
}
```

> - `@MappedSuperClass` - 부모 클래스는 테이블과 매핑하지 않고 상속받는 자식 클래스에 매핑 정보만 제공하고 싶을 때 사용한다.
> - `@EntitiyListeners(AuditingEntitiyListner.class)` - Entity를 DB에 적용하기 이전 또는 이후에 커스텀 콜백을 요청할 수 있는 annotation.

## Auditing 설정

생성한 `AuditorEntity를` 상속받도록 `Post` 클래스를 수정해준다.

```java
//src/main/java/dev/jooz/springboot/domain/post/post.java

...
@Getter
@NoArgsConstructor
@Entity
public class Post extends AuditorEntity{
    ...
}

```

Auditing을 사용하기 위해 Application 클래스에 `@EnableJpaAuditing` 어노테이션을 추가해준다.

```java
// src/main/java/dev/jooz/springboot/Application.java

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.data.jpa.repository.config.EnableJpaAuditing;

@EnableJpaAuditing
@SpringBootApplication
public class Application {
    public static void main(String[] args){
        SpringApplication.run(Application.class,args);
    }
}

```

# 테스트

## 테스트 작성

Auditing 적용 확인을 위해 간단한 테스트를 작성해준다.

```java
// src/test/java/dev/jooz/springboot/PostRepository.java

import dev.jooz.springboot.domain.post.Post;
import dev.jooz.springboot.domain.post.PostRepository;
import org.junit.After;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import java.time.LocalDateTime;

import static org.assertj.core.api.Assertions.assertThat;

@RunWith(SpringRunner.class)
@SpringBootTest
public class PostRepositoryTest {

    @Autowired
    PostRepository postRepository;

    @After
    public void cleanUp() {
        postRepository.deleteAll();
    }

    @Test
    public void test_dateTime() {
        LocalDateTime now = LocalDateTime.of(2020, 05, 25, 13, 0, 0);
        postRepository.save(Post.builder()
                                    .title("test title")
                                    .content("test content")
                                    .build());

        Post post= postRepository.findAll().get(0);

        System.out.println("createdDate="+post.getCreatedDate()+", modifiedDate="+post.getModifiedDate());

        assertThat(post.getCreatedDate()).isAfter(now);
        assertThat(post.getModifiedDate()).isAfter(now);
    }
}

```

## 테스트 실행

테스트를 실행시키면 문제없이 통과되는 것을 확인할 수 있다.

![result](/assets/images/2020/05/25/jpa-auditing-result.png)

# Directory 구조

```
📦SpringJPAAuditingTutorial
 ┣ 📂src
 ┃ ┣ 📂main
 ┃ ┃ ┣ 📂java
 ┃ ┃ ┃ ┗ 📂dev
 ┃ ┃ ┃ ┃ ┗ 📂jooz
 ┃ ┃ ┃ ┃ ┃ ┗ 📂springboot
 ┃ ┃ ┃ ┃ ┃ ┃ ┣ 📂domain
 ┃ ┃ ┃ ┃ ┃ ┃ ┃ ┣ 📂post
 ┃ ┃ ┃ ┃ ┃ ┃ ┃ ┃ ┣ 📜Post.java
 ┃ ┃ ┃ ┃ ┃ ┃ ┃ ┃ ┗ 📜PostRepository.java
 ┃ ┃ ┃ ┃ ┃ ┃ ┃ ┗ 📜AuditorEntity.java
 ┃ ┃ ┃ ┃ ┃ ┃ ┗ 📜Application.java
 ┃ ┗ 📂test
 ┃ ┃ ┣ 📂java
 ┃ ┃ ┃ ┗ 📂dev
 ┃ ┃ ┃ ┃ ┗ 📂jooz
 ┃ ┃ ┃ ┃ ┃ ┗ 📂springboot
 ┃ ┃ ┃ ┃ ┃ ┃ ┗ 📜PostRepositoryTest.java
 ┣ 📜build.gradle
 ┣ 📜gradlew
 ┣ 📜gradlew.bat
 ┗ 📜settings.gradle
```
