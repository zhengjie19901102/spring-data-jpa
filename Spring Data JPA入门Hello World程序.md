## Spring Data JPA入门Hello World程序

Spring Data JPA需要的jar包:

JPA实现相关jar包、spring data jpa和spring data commons俩个jar包，还需要sel4j的相关实现包。

spring配置文件中内容如下(加入了基于aop事务的相关配置):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:jpa="http://www.springframework.org/schema/data/jpa"
	xmlns:repository="http://www.springframework.org/schema/data/repository"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd
		http://www.springframework.org/schema/data/jpa http://www.springframework.org/schema/data/jpa/spring-jpa-1.8.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.3.xsd
		http://www.springframework.org/schema/data/repository http://www.springframework.org/schema/data/repository/spring-repository-1.11.xsd">

	<!-- 加载外部配置文件 -->
	<context:property-placeholder
		location="classpath:db.properties" />

	<!-- 设置数据源 -->
	<bean id="dataSource"
		class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="driverClass" value="${mysql.driverClass}"></property>
		<property name="user" value="${mysql.username}"></property>
		<property name="password" value="${mysql.password}"></property>
		<property name="jdbcUrl" value="${mysql.url}"></property>
	</bean>
	<!-- 设置JPA的EntityManagerFactory -->
	<bean id="entityManagerFactory"
		class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
		<property name="dataSource" ref="dataSource"></property>
		<property name="jpaVendorAdapter">
			<bean
				class="org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter"></bean>
		</property>

		<!--配置bean所在包 -->
		<property name="packagesToScan" value="com.ieb.bean" />
		<!-- 配置实现的属性 -->
		<property name="jpaProperties">
			<props>
				<prop key="hibernate.show_sql">true</prop>
				<prop key="hibernate.format_sql">true</prop>
				<prop key="hibernate.hbm2ddl.auto">update</prop>
			</props>
		</property>
	</bean>

	<!--配置事物管理器-->
    <bean id="transactionManager" class="org.springframework.orm.jpa.JpaTransactionManager">
        <property name="entityManagerFactory" ref="entityManagerFactory"/>
    </bean>
    
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
	
	<!--配置Spring Data JPA相关的
	测试证明: 扫描的是dao接口(声明的接口层)-->
	<jpa:repositories base-package="com.ieb.dao" entity-manager-factory-ref="entityManagerFactory"></jpa:repositories>
</beans>
```
`声明的spring data 接口需要继承Repository<T, ID extends Serializable>接口`


