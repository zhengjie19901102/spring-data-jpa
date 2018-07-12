## Spring Data JPA之JpaSpecificationExecutor接口

`Spring Data JPA中Repository接口已经可以完成大部分数据库的操作，但是更复杂的操作其Respository相关接口就无法进行。此时可以继承JpaSpecificationExecutor接口来扩充Repository接口的功能。`

**`因为Spring Data JPA中因为只有继承了Repository接口才能被扫描到Spring容器中，所以JpaSpecificationExecutor<对应操作的实体类>只是对Repository接口的功能扩充，如果单独用JpaSpecificationExecutor接口则不会被纳入到Spring容器中。`**

简单使用测试代码:

```java
Specification<TestEntity> spec = new Specification<TestEntity>() {

	/**
	 * root代表对应操作的实体导航图[通过导航图的方式定位到对应的属性]
	 * query为JPA的Criteria查询条件对象
	 * CriteriaBuilder为查询条件的创建对象，该对象封装了Predicate接口
	 */
	@Override
	public Predicate toPredicate(Root<TestEntity> root,
			CriteriaQuery<?> query, CriteriaBuilder cb) {
		//
		Path<Object> path = root.get("id");
		Predicate notEqual = cb.notEqual(path,29);
		return notEqual;
	}
};
Pageable pageable = new PageRequest(0,5);
Page<TestEntity> findAll = jpaRes.findAll(spec, pageable);
System.out.println(findAll.getContent());
```

`接口代码:`

```java
public interface TestJPARepository extends 
		JpaRepository<TestEntity, Integer>, JpaSpecificationExecutor<TestEntity> {
}
```

