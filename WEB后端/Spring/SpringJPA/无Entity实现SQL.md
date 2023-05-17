# Spring无Entity实现SQL查询

Spring Data JPA在与数据库交互的过程中，一般是定义一个interface，然后这个interface继承已经有的接口，就可以自动进行查询了。

在多对多关系中，通常会生成一张表，这个表中的每一项对应是没有实体的，如何不创建`Entity`但是可以完成对数据库的访问，整体的思路是创建一个类可以执行特定的SQL语句。

## 简单示例

看一个简单的示例，主要完成了插入和删除两个操作。用groupId和clientId作为参数填充对应的SQL语句，SQL语句直接用字符串生成即可。

```java
@Repository
@Transactional
public class RelationshipGroupClientRepository {
    @PersistenceContext
    private EntityManager entityManager;

    public boolean saveGroupClientRelationship(Long groupId, Long clientId) {
        String sqlScript = "INSERT INTO `rel_group_client` (group_id,client_id) VALUES (:groupId,:clientId)";
        try {
            entityManager.createNativeQuery(sqlScript)
                    .setParameter("groupId", groupId)
                    .setParameter("clientId", clientId)
                    .executeUpdate();
            return true;
        } catch (Exception e) {
            return false;
        }
    }

    public boolean deleteGroupClientRelationship(Long groupId, Long clientId) {
        String sqlScript = "DELETE FROM `rel_group_client` WHERE group_id = :groupId AND client_id = :clientId";
        try {
            entityManager.createNativeQuery(sqlScript)
                    .setParameter("groupId", groupId)
                    .setParameter("clientId", clientId)
                    .executeUpdate();
            return true;
        } catch (Exception e) {
            return false;
        }
    }
}
```

1. 注入entityManager，entityManager的注解必须要用@PersistenceContext，不能用@Autowired，否则会报错。
2. @Transactional 可以不用管事务的具体细节。
3. 通过 `entityManager.createNativeQuery(sqlString)..executeUpdate()`执行具体的代码。

## 代码改进点

在上述代码中其实无法很好的得到SQL语句的执行结果，一般报错之后（例如我插入的时候插入了相同的项，主键冲突了，但是上述代码无法捕捉 ，只会报错），catch无法捕捉到异常，被别的类捕获了。