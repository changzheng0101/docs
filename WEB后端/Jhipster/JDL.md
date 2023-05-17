# JDL

Jhipster Domain Language

Jhipster用于构建各个domain的语言，也就是构建实体，构建完成之后会自动生成基本的增删改查工作。

>`[]`代表该参数可选，`*`代表可以选择多个参数

## 注释

与java相同`/** */`和`//`

## 实体Entity

```java
[<entity javadoc>]
[<entity annotation>*]
entity <entity name> [(<table name>)] {
  [<field javadoc>]
  [<field annotation>*]
  <field name> <field type> [<validation>*]
}
```

Example:

```java
/**
 * This is customer entity Javadoc comment
 * @author Foo
 */
@dto(mapstruct)
entity Customer {
  /** Name field */
  name String required,
  age Integer,
  address String maxlength(100) pattern(/[a-Z0-9]+/)
}
```

>没有主键，主键自动生成 。

## 枚举 Enumeration

```java
enum [<enum name>] {
  <ENUM KEY> ([<enum value>])
}
```

```java
enum Language {
  ENGLISH, DUTCH, FRENCH
}
```

## 关系 Relationship

```java
relationship <type> {
  <from entity>[{<relationship name>[(<display field>)] <validation>*}]
  to
  <to entity>[{<relationship name>[(<display field>)] <validation>*}]
}
```

>关系包括: OneToMany,ManyToOne,OneToOne,ManyToManys-----------描述了<from entity> 和<to entity>之间对的关系。

display field是可选的，构建生成的网页中展示的就是这个名字。

多个关系可以同时生成，用逗号分隔。

```java
entity Book
entity Author
entity Tag
relationship OneToMany {
  Author{book} to Book{writer(name) required},
  Book{tag} to Tag
}
```

>Author{book} to Book{writer(name) required}           Author实体和Book实体有一对多关系，book中的字段对应Book中的writer字段，在界面Book的writer显示为name，并且用required进行约束。

## DTO、service、pagination

```java
<OPTION> <ENTITIES | * | all> [with <VALUE>] [except <ENTITIES>]. 
```

其中OPTION可以是以下这些值。

### service

Jhipster自动生成的代码中，Controller层直接和数据库打交道，如果想增加逻辑，就需要加入一层“service”层，可以选择`serviceClass`和`serviceImpl`，后者会同时生成接口和实现，前者直接生成实现。

### DTO

不想直接将数据库中的数据库展示给前端，可以进行一定的处理之后再展示给前端，可使用mapstruct作为值。

### filter

启用JPA过滤器功能，只有在启用了`service`的情况下才能使用这个功能。

### pagination

启用分页功能

----------------

Example Code:

```java
@service(serviceImpl)
entity A
entity B
...
entity Z
dto * with mapstruct
service B with serviceClass
paginate * with pagination except B, C
paginate B, C with infinite-scroll
filter A, B
```

JDL文件可以在线编写和导出：

[在线JDL地址]([JDL-Studio (jhipster.tech)](https://start.jhipster.tech/jdl-studio/))

