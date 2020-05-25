---
title: "Spring 프로젝트에 JPA Auditing 적용하기"
excerpt: "JPA Auditing으로 created_at, updated_at 자동화하기"

header:
  teaser: /assets/images/2020/04/spring-logo.png
  overlay_image: /assets/images/splash/book.jpg
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
  overlay_filter: 0.5

categories:
  - Spring
tags:
  - spring
  - jpa
---

우선 JPA Auditing 테스트를 위해 Post 엔티티와 PostRepository를 생성해준다.

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

# Jpa Auditing

Auditing을 사용하기 위해 BaseTimeEntity를 생성해준다.

```java
// src/main/java/dev/jooz/springboot/domain/BaseTimeEntity.java

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
public abstract class BaseTimeEntity {
    @CreatedDate
    private LocalDateTime createdDate;

    @LastModifiedDate
    private LocalDateTime modifiedDate;
}
```

> - `@MappedSuperClass` - JPA Entity 클래스들이 BaseTimeEntity를 상속할 경우 필드들도 칼럼으로 인식하도록 해준다.
> - `@EntitiyListeners(AuditingEntitiyListner.class)` - BaseTimeEntity 클래스에 Auditing 기능을 포함시킨다.
> - `@CreatedDate` - Entity가 생성되어 저장될 때 시간이 자동으로 저장된다.
> - `@LastModifiedDate` - Entity의 값이 변경될 때 시간이 자동으로 저장된다.

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

# 테스트 작성

Jpa Auditing 적용 확인을 위해 간단한 테스트를 작성해준다.

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

# 테스트 결과

![result](/assets/images/2020/05/25/jpa-auditing-result.png)

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
 ┃ ┃ ┃ ┃ ┃ ┃ ┃ ┗ 📜BaseTimeEntity.java
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
