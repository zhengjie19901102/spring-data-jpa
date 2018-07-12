## Spring Data JPA之Query注解与Modifying注解

Spring Data支持JPQL语句。
通过@Query注解可以轻松使用JPQL语句。

```java
@Query("SELECT t FROM TestEntity t WHERE t.name LIKE %?1%")
List<TestEntity> selectLikeName(@Param("name")String name);
```
Spring Data JPA的JPQL支持占位符形式的条件和命名空间条件。

`占位符形式:`

```java
@Query("UPDATE TestEntity t SET t.name = ?1 WHERE t.id = ?2")
void updateDataById(String name,Integer id);
```

`命名空间形式:`

```java
@Query("SELECT t FROM TestEntity t WHERE t.name LIKE %:name%")
List<TestEntity> selectLikeName(@Param("name")String name);
```
使用命名空间形式的条件，则需要在声明的接口参数指定处添加`@Param`注解并指定对应的命名空间。

Spring Data的@Query注解同时支持原生SQL查询:

```java
@Query(value="SELECT name,age,id FROM text_entity WHERE id = ?",nativeQuery=true)
TestEntity selectByNativeSql(Integer id);
```
通过@Query注解的nativeQuery属性指定为true通知此处使用原生SQL语句。

**JPQL并不支持INSERT语句，但可以使用UPDATE和DELETE语句，要想使用UPDATE或DELETE语句则需要在`@Query`注解上`@Modifying`注解，以通知该JPQL为更新或删除操作。**

`注:使用JPQL语句来更新或删除操作，会使用事务。此时需要开启事务来使用。否则会报不支持DML错误。此时可以采用Spring的基于AOP的XML配置的声明式事务或@Transactional注解的事务[使用注解方式需spring开启事务注解驱动]。`

```xml
<!--配置事物管理器-->
<bean id="transactionManager" class="org.springframework.orm.jpa.JpaTransactionManager">
    <property name="entityManagerFactory" ref="entityManagerFactory"/>
</bean>
    
<!-- 基于注解的事务支持 -->
<!-- <tx:annotation-driven transaction-manager="transactionManager"/> -->
    
<!-- 基于aop配置的事务 -->
<aop:config>
	<aop:pointcut expression="execution(* com.ieb.service..*(..))" id="aopPoint"/>
	<!-- 将配置好的切点和事物通知配置到该通知器其中 -->
	<aop:advisor advice-ref="txAdvice" pointcut-ref="aopPoint"/>
</aop:config>
	
<!-- 事物通知,默认:transactionManager -->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
	<tx:attributes>
		<tx:method name="*"/>
		<tx:method name="get*" read-only="true"/>
		<tx:method name="find*" read-only="true"/>
	</tx:attributes>
</tx:advice>
```
