# 🔥 JPA-PLUS
[![License](https://img.shields.io/badge/license-Apache%202-4EB1BA.svg)](https://www.apache.org/licenses/LICENSE-2.0.html)
## 💎 一、特性

- 简单、轻量级、无副作用的集成和使用，jar 包仅 `192 KB`；
- 作为 JPA 的扩展和增强，兼容 Spring Data JPA 原有功能和各种特性；
- 可以采用 Java 链式 `API` 来书写动态 SQL；
- 可以采用动态条件注解和Java 链式 `API` 来书写出动态的 `Specification`。
- 增强了更快速高效的 JPA 批量“增删改”的支持，支持非 `null` 属性的增量更新；
- 支持**雪花算法**的主键 ID 生成策略；
- 具有动态性、极致的可复用性的优点；

## 🏖️️ 二、支持场景

适用于 Java `Spring Data JPA` 项目，`JDK 1.8` 及以上，Spring Data JPA 的版本须保证 `2.1.8.RELEASE` 及以上；如果你是 Spring Boot 项目，则 Spring Boot 的版本须保证 `2.1.5.RELEASE` 及以上。因为后续版本的 Spring Data JPA 对其中 `QueryLookupStrategy` 的代码有较大改动。

## ☘️ 三、Spring Boot 项目集成

如果你是 Spring Boot 项目，那么直接集成 `jpa-plus` 库，并使用 `@EnableJpaPlus` 激活 Jpa-plus 的相关配置信息。

如果你**不是 Spring Boot 项目**，请参看[这里](https://blinkfox.github.io/jpa-plus/#/quick-install?id=not-spring-boot-project) 的配置方式。

> **注**：请确保你使用的 Spring Boot 版本是 **`v2.1.5.RELEASE` 及以上**，如果 Spring Boot 版本是 `v2.2.x.RELEASE` 及以上，则 Jpa-plus 版本必须是 `v2.0.0` 版本及以上。

### 🌾 1. Maven

```xml
<dependency>
    <groupId>top.openyuan</groupId>
    <artifactId>jpa-plus</artifactId>
    <version>1.0.4</version>
</dependency>
```

### 🌵 2. Gradle

```bash
implementation 'top.openyuan:jpa-plus:1.0.2'
```

### 🏕️ 3. 激活 Jpa-plus (@EnableJpaPlus)

然后需要在你的 Spring Boot 应用中使用 `@EnableJpaPlus` 激活 Jpa-plus 的相关配置信息。

```java
/**
 * 请在 Spring Boot 应用中标注 {code @EnableJpaPlus} 注解.
 *
 * @author lzy on 2020-02-01.
 */
@EnableJpaPlus
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

> **💡 注**： 
> 1. `@EnableJpaPlus` 注解中实质上是使用的是 `JpaRepositoryExtFactoryBean`。而 `JpaRepositoryExtFactoryBean` 继承自 Spring Data JPA 默认的 `JpaRepositoryFactoryBean`。所以，Jpa-plus 与 JPA 的各种注解和特性完全兼容，并提供了更加强大的 `@QueryJpa-plus` 注解和其他更多动态的能力。
> 2. 如果你是多数据源，则你可以根据自身情况，在需要的数据源中的 `@EnableJpaRepositories` 注解中单独设置 `repositoryFactoryBeanClass` 的值为：`Jpa-plusJpaRepositoryFactoryBean.class`。示例如：`@EnableJpaRepositories(repositoryFactoryBeanClass = Jpa-plusJpaRepositoryFactoryBean.class)`。

### 🏝️ 4. application.yml 配置（可选的）

> **注**：Jpa-plus 采用了**约定优于配置**的方式，所以通常情况下，你可以不用做任何的 Jpa-plus 配置。

如果你要修改 Jpa-plus 的配置信息，你需要在你的 Spring Boot 项目中，在 `application.yml` 或者 `application.properties` 中去修改配置信息。

以下通过 `application.yml` 文件来展示 Jpa-plus 中的几个配置项、默认值和说明信息，供你参考。

```yaml
# JpaPlus 的几个配置项、默认值及详细说明，通常情况下你不需要填写这些配置信息（下面的配置代码也都可以删掉）.
jpa:

```

## 🍔 四、示例概览

Jpa-plus 中支持四种方式书写动态 SQL，分别是：

- 基于 JPQL (或 SQL) 的 Java API 方式
- 基于 `Specification` 的 Java API 方式
- 基于 `Specification` 的 Java Bean 注解方式

以下的四种方式的示例均以博客信息数据作为示例，你可以根据自己的场景或喜欢的方式来选择动态查询的方式。关于详细的使用文档可以[参看文档](https://blinkfox.github.io/jpa-plus/#/)。

### 3. 🍭 基于 Specification 的 Java API 方式

基于 `Specification` 的方式，只须要 `BlogRepository` 接口继承 `Jpa-plusJpaSpecificationExecutor` 接口即可。

```java
// JpaRepository<Blog, String> 和 Jpa-plusJpaSpecificationExecutor<Blog> 可以混用，也可以只使用某一个.
public interface BlogRepository extends JpaRepository<Blog, String>, JpaSpecificationExecutor<Blog> {

}
```

基于 `Specification` 的方式，不需要定义额外的查询方法，也不需要写 `JPQL` (或 SQL) 语句，简单直接。下面是通过 Java 链式的 API 方式来做单元测试的使用方式示例：

```java
/**
 * 测试使用 Jpa-plus 中的  {@link JpaPlusSpecification} 的链式 Java API 来动态查询博客信息.
 */
@Test
public void queryBlogsWithSpecifition() {
    // 这一段代码是在模拟构造前台传递查询的相关 map 型参数，当然也可以使用其他 Java 对象，作为查询参数.
    Map<String, Object> params = new HashMap<>();
    params.put("ids", new String[]{"1", "2", "3", "4", "5", "6", "7", "8"});
    params.put("author", "ZhangSan");
    params.put("startTime", Date.from(LocalDateTime.of(2019, Month.APRIL, 8, 0, 0, 0)
            .atZone(ZoneId.systemDefault()).toInstant()));
    params.put("endTime", Date.from(LocalDateTime.of(2019, Month.OCTOBER, 8, 0, 0, 0)
            .atZone(ZoneId.systemDefault()).toInstant()));

    // 开始真正的查询，使用.
    Object[] ids = (Object[]) params.get("ids");
    List<Blog> blogs = blogRepository.findAll(builder ->
            builder.andIn("id", ids, ids != null && ids.length > 0)
                    .andLike("title", params.get("title"), params.get("title") != null)
                    .andLike("author", params.get("author"))
                    .andBetween("createTime", params.get("startTime"), params.get("endTime"))
            .build());

    // 单元测试断言查询结果的正确性.
    Assert.assertEquals(3, blogs.size());
    blogs.forEach(blog -> Assert.assertTrue(blog.getAuthor().endsWith("ZhangSan")));
}
```

### 4. 🥯 基于 Specification 的 Java Bean 注解方式

本方式是指通过将 Java Bean 作为参数传递，在 Java Bean 对象的属性中通过查询的条件注解来表明是何种查询匹配方式。当然，同第三种方式一样，`BlogRepository` 接口也须要继承 `Jpa-plusJpaSpecificationExecutor` 接口。

```java
// JpaRepository<Blog, String> 和 Jpa-plusJpaSpecificationExecutor<Blog> 可以混用，也可以只使用某一个.
public interface BlogRepository extends JpaRepository<Blog, String>, JpaSpecificationExecutor<Blog> {

}
```

然后，定义一个用于表示各种查询条件的普通 Java Bean 类 `BlogParam`，当然该类也可以是前台传递过来的对象参数，也可以单独定义。该类的各个属性对应某个查询字段，属性上的注解对应查询的匹配方式，某个字段是否生成查询条件的默认判断依据是该属性值是否为空。

```java
/**
 * 用于测试 {@code SpecificationPlus} 注解动态查询的博客 VO 类.
 *
 * @author blinkfox on 2020-01-28.
 */
@Getter
@Setter
@Accessors(chain = true)
public class BlogParam {

    /**
     * 用于 IN 范围查询的 ID 集合，{@link In} 注解的属性值可以是数组，也可以是 {@link java.util.Collection} 集合数据.
     */
    @In("id")
    private List<String> ids;

    /**
     * 模糊查询博客信息的作者名称关键字内容的字符串.
     */
    @Like
    private String author;

    /**
     * 用于根据博客创建时间 {@link Between} 区间查询博客信息的开始值和介绍值，
     * 区间查询的值类型建议是 {@link BetweenValue} 类型的.
     * 当然值类型也可以是二元数组，也可以是 {@link List} 集合，如果是这两种类型的值，元素的顺序必须是开始值和结束值才行.
     */
    @Between("createTime")
    private BetweenValue<Date> createTime;

}
```

下面是单元测试的使用方式示例：

```java
/**
 * 测试使用 Jpa-plus 中的  {@link SpecificationPlus} 的 Java Bean 条件注解的方式来动态查询博客信息.
 */
@Test
public void queryBlogsWithAnnotaion() {
    // 这一段代码是在模拟构造前台传递的或单独定义的 Java Bean 对象参数.
    Date startTime = Date.from(LocalDateTime.of(2019, Month.APRIL, 8, 0, 0, 0)
            .atZone(ZoneId.systemDefault()).toInstant());
    Date endTime = Date.from(LocalDateTime.of(2019, Month.OCTOBER, 8, 0, 0, 0)
            .atZone(ZoneId.systemDefault()).toInstant());
    BlogParam blogParam = new BlogParam()
            .setIds(Arrays.asList("1", "2", "3", "4", "5", "6", "7", "8"))
            .setAuthor("ZhangSan")
            .setCreateTime(BetweenValue.of(startTime, endTime));

    // 开始真正的查询.
    List<Blog> blogs = blogRepository.findAllOfBean(blogParam);

    // 单元测试断言查询结果的正确性.
    Assert.assertEquals(3, blogs.size());
    blogs.forEach(blog -> Assert.assertTrue(blog.getAuthor().endsWith("ZhangSan")));
}
```

## 📝 五、开源许可证

本 `Jpa-plus` 的 Spring Data JPA 扩展库遵守 [Apache License 2.0](http://www.apache.org/licenses/LICENSE-2.0) 许可证。

## 🙏 六、鸣谢
感谢 [Fenix 项目](https://github.com/blinkfox/fenix)