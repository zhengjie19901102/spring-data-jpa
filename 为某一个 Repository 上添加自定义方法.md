## 为某一个 Repository 上添加自定义方法

步骤：

- 定义一个接口: 声明要添加的, 并自实现的方法

- 提供该接口的实现类: 类名需在要声明的 Repository 后添加 Impl, 并实现方法
声明 Repository 接口, 并继承声明的接口

- 使用. 

注意: 默认情况下, Spring Data 会在 **`base-package`** 中查找 "接口名Impl" 作为实现类. 也可以通过　repository-impl-postfix　声明后缀。

`自定义接口:`

```java
public interface TestDao {
	public void testDao();
}
```

`自定义接口实现类:`

```java
public class TestJPARepositoryImpl implements TestDao {
	@Override
	public void testDao() {
		System.out.println("testDao 方法");
	}
}
```

**`按照步骤上说明: 实现类命名以自定义Dao层上的Repository名为前缀，以Impl为后缀。且实现类和接口放在同一个包下。`**

`测试代码:`

```java
	....简单的测试
```

