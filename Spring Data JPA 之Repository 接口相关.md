## Spring Data JPA 之Repository 接口相关
`Repository 接口是 Spring Data 的一个核心接口，它不提供任何方法，开发者需要在自己定义的接口中声明需要的方法。`

引自尚硅谷`Spring Data`ppt文件。

Repository接口定义如下:
 `Repository<T, ID extends Serializable>`

**Spring Data可以让我们只定义接口，只要遵循 Spring Data的规范，就无需写实现类。**

与继承 `Repository接口` 等价的一种方式，就是在持久层接口上使用 `@RepositoryDefinition` 注解，并为其指定 `domainClass` 和 `idClas` 属性。如下两种方式是完全等价的

即spring Data 提供两种方式来声明`Repository`：

- 继承`Repository`接口[`org.springframework.data.repository.Repository`包下]。
- 在目标`Dao`上添加注解`@RepositoryDefinition`并给该注解添加`domainClass`和`idClas`属性的值。

```text
基础的 Repository 提供了最基本的数据访问功能，其几个子接口则扩展了一些功能。它们的继承关系如下： 
Repository： 仅仅是一个标识，表明任何继承它的均为仓库接口类
CrudRepository： 继承 Repository，实现了一组 CRUD 相关的方法 
PagingAndSortingRepository： 继承 CrudRepository，实现了一组分页排序相关的方法 
JpaRepository： 继承 PagingAndSortingRepository，实现一组 JPA 规范相关的方法 
自定义的 XxxxRepository 需要继承 JpaRepository，这样的 XxxxRepository 接口就具备了通用的数据访问控制层的能力。
JpaSpecificationExecutor： 不属于Repository体系，实现一组 JPA Criteria 查询相关的方法

---------引自尚硅谷PPT
```

#### Spring Data JPA 查询规范

按照 Spring Data 的规范，查询方法以 `findBy` | `readBy` | `getBy` 开头

```java
//例如：定义一个 Entity 实体类
class User｛ 
	private String firstName; 
	private String lastName; 
｝ 

//使用And条件连接时，应这样写： 
findByLastNameAndFirstName(String lastName,String firstName); 
//条件的属性名称与个数要与参数的位置与个数一一对应 
```
**直接在接口中定义查询方法，如果是符合规范的，可以不用写实现，目前支持的关键字写法如下：**

关键字 | 简单示例 | JPQL片段示例
-----|------|--------
AND|	findByLastNameAndFirstName | WHERE Entity.lastName = ?1 AND Entity.firstName = ?2
OR | readByLastNameOrFirstName | WHERE Entity.lastName = ?1 OR Entity.firstName = ?2
Between | getByStartDateBetween | WHERE Entity.startDate BETWEEN ?1 AND ?2
LessThan | findByAgeLessThan | WHERE Entity.age < ?1
GreaterThan | readByAgeGreaterThan | WHERE Entity.age > ?1
After`只作用在时间` | findByStartDateAfter | WHERE Entity.startDate > ?1
Before`只作用在时间` | findByStartDateBefore | WHERE Entity.startDate < ?1
LessThanEqual | findByAgeLessThenEqual | WHERE Entity.age <= ?1
GreaterThanEqual | readByAgeGreaterThenEqual | WHERE Entity.age >= ?1
Is | findByLastNameIs | WHERE Entity.lastName = ?1
Equal | getByFirstNameEqual | WHERE Entity.firstName = ?1
IsNull | findByAddressIsNull | WHERE Entity.address is NULL
IsNotNull | readByAddressIsNotNull | WHERE Entity.address NOT NULL
NotNull | readByAddressNotNull | WHERE Entity.address NOT NULL
Like | findByNameLike | WHERE Entity.name LIKE ? `不包括'%'符号`
Not Like | findByNameNotLike | WHERE Entity.name not like ?1 `不包括'%'符号`
StartingWith | readByNameStartingWith | WHERE Entity.name like '?1%'`条件以'%'符号结尾`
EndingWith | readByName EndingWith | WHERE Entity.name like '%?1'`条件以'%'符号开头`
Containing | getByNameContaining | WHERE Entity.name like '%?1%' `包含'%'符号`
OrderBy | findByAgeOrderByAddressDesc | WHERE Entity.age = ? Order By Entity.address DESC
Not | readByAgeNot | WHERE Entity.age <> ?1 `不等于`
In | findByNameIn(Collection<T> name) | WHERE Entity.name IN (?1 ,?2, ?3)
NotIn | getByNameNotIn(Collection<T> name)| WHERE Entity.name NOT IN (?1 ,?2, ?3)
True | readByFloagTrue() | WHERE Entity.floag = true
False | readByFloagFalse() | WHERE Entity.floag = false
IgnoreCase | findByNameIgnoreCase | WHERE UPPER(Entity.Name) = UPPER(?1)




**以下引自尚硅谷PPT文档:**

假如创建如下的查询：findByUserDepUuid()，框架在解析该方法时，首先剔除 findBy，然后对剩下的属性进行解析，假设查询实体为Doc

- 先判断 userDepUuid （根据 POJO 规范，首字母变为小写）是否为查询实体的一个属性，如果是，则表示根据该属性进行查询；如果没有该属性，继续第二步；

- 从右往左截取第一个大写字母开头的字符串(此处为Uuid)，然后检查剩下的字符串是否为查询实体的一个属性，如果是，则表示根据该属性进行查询；如果没有该属性，则重复第二步，继续从右往左截取；最后假设 user 为查询实体的一个属性；

- 接着处理剩下部分（DepUuid），先判断 user 所对应的类型是否有depUuid属性，如果有，则表示该方法最终是根据 “ Doc.user.depUuid” 的取值进行查询；否则继续按照步骤 2 的规则从右往左截取，最终表示根据 “Doc.user.dep.uuid” 的值进行查询。

- 可能会存在一种特殊情况，比如 Doc包含一个 user 的属性，也有一个 userDep 属性，此时会存在混淆。可以明确在属性之间加上 "_" 以显式表达意图，比如 "findByUser_DepUuid()" 或者 "findByUserDep_uuid()"

- 特殊的参数： 还可以直接在方法的参数上加入分页或排序的参数，比如：
Page<UserModel> findByName(String name, Pageable pageable);
List<UserModel> findByName(String name, Sort sort);