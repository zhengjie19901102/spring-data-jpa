## Spring Data JPA 之Repository相关子接口

Repository有以下三个子接口

- CrudRepository<实体类类名,主键ID类型>
- PagingAndSortingRepository<实体类类名,主键ID类型>
- JpaRepository<实体类类名,主键ID类型>


`CrudRepository接口封装了基本的增删改查相关方法。有以下几个方法: `

方法名 | 用途
---|---
 S save(S entity) | 将传入的实体持久化至数据库中，返回保存的实体类
 Iterable save(Iterable entities) | 将传入的实体类集合持久化到数据库中，`sql显示条为in`，返回持久化至数据库中的实体类集合
 findOne(ID id) | 通过主键ID查找对应的记录，如果存在则返回对应的实体，如果不存则返回null
 exists(ID id) | 通过传入的主键ID，判断是否存在该条记录，返回Boolean
 findAll() | 查询出数据库中所有的数据，返回实体集合。
 findAll(Iterable<ID> ids) | 通过集合查询是否集合中所有元素是否存在于数据库。
count() | 查询数据库中数据的记录数
delete(ID id) | 删除指定主键的数据
delete(T entity) | 删除传入的实体类，通过实体删除
delete(Iterable<? extends T> entities) | 删除指定集合中的实体
deleteAll() | 删除数据库中所有数据。

`PagingAndSortingRepository接口封装了分页和排序相关方法:`

方法名 | 用途
---|---
findAll(Sort sort) | 排序所有数据且进行排序
findAll(Pageable pageable) | 分页查询数据

**`PagingAndSortingRepository接口中相关的类[接口]:`*8

`Pageable接口` 该接口封装了分页的相关数据，通常使用该接口的`PageRequest实现类`。该实现类有三个重载的构造方法:

`PageRequest(int page, int size)`: 传入页码`从0开始计`，每页的显示数量。

`PageRequest(int page, int size, Direction direction, String... properties)`: 传入页码,每页显示数，排序方式**`Direction是一个枚举对象，有俩种选择: ASC升序和DESC降序`**，和通过哪些属性排序。

`PageRequest(int page, int size, Sort sort)`: 传入页码,每页显示数和排序对象Sort

**其中`PageRequest(int page, int size, Direction direction, String... properties)`和`PageRequest(int page, int size, Sort sort)`用到的其实是同一个构造器。**

`Sort类封装了排序方式和排序字段相关方法。具体使用方法查看API或源文件。`

通过分页`findALl`方法返回一个`Page<T>`对象，该对象封装了查询到的数据和分页相关信息:

方法名 | 解释
---|---
getNumber | 当前是第几页[0为第一页，所以要+1]
getSize | 一页显示的数量
getNumberOfElements | 当前页的条数
getTotalElements | 数据库中数据的总数
getContent | 查询到的所有数据
getTotalPages | 全部数据合起来可分的总页数

`JpaRepository接口封装了JPA相关的方法，例如批量删除等，有如下几个常用方法:`

方法名 | 解释
---|---
findAll(Sort sort) | 查询出所有数据并排序
save(Iterable\<S\> entities) | 批量保存数据
flush | 将缓存中的数据与数据库同步`其实就是把一级或二级缓存数据存入数据库中。单不会立即存入，当commit事务提交后才会正式持久化 `
saveAndFlush(S entity) | 类似于JPA中的merge方法`如果传入有ID的实体类则会先查询，如果存在则更新，如果不存在则存入。如果传入的没有ID的实体类，则直接存入。`
deleteInBatch | 批量删除集合中的实体
deleteAllInBatch | 批量删除所有数据
findAll(Example\<S\> example) | 待条件查询所有数据



 