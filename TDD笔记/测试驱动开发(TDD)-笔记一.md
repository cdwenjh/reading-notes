2019/3/24 周日-2019/3/31周日

## 测试驱动开发(TDD)-笔记一

​	TDD是以测试作为开发过程的中心，在编写任何产品代码之前，首先编写用于定义产品代码行为的测试，而编写的产品代码又要以使测试通过为目标。

##### 	要求：测试可以完全自动化地运行，在对代码重构前后必须运行测试。

### TDD目标

​	代码整洁可用（clean code that works）

### TDD开发所经历的阶段

​	(1)不可运行--写一个不能工作的测试程序，一开始这个测试程序甚至不能编译。

​	(2)可运行--尽快让这个测试程序工作，为此可以在程序中使用一些不合情理的方法。

​	(3)重构--消除在让测试程序工作的过程中产生的重复设计，优化设计结构。

### TDD过程

​	(1)快速增加一个测试

​	(2)运行所有测试，发现最新的测试不通过

​	(3)做一些小小的改动

​	(4)运行所有测试，全部通过

​	(5)重构代码，消除重复设计，优化设计结构

### 计划清单(to-do list)

​	将工作任务细分，划入清单

​	**任务1** 完成后 ~~**任务1**~~

### 资金实例

当瑞士法郎与美元的兑换率为2:1的时候，5美元+10瑞士法郎=10美元  

> #### 	Task
>
> 1. **当瑞士法郎与美元的兑换率为2:1时，5美元+10瑞士法郎=10美元**
> 2. **5美元 * 2 = 10 美元****  
> 3. **将"amount"定义为私有**  
> 4. **Dollar类有副作用吗？**
> 5. **钱数必须为整数？**
>

乘法如下：

```java
@Test 
public void testMultiplication(){
  Dollar five = new Dollar(5);
  five.times(2);
  assertEquals(five.amount,10);
}
```

​	测试不通过，缺少Dollar类、缺少构造函数、times(int)方法、没有amount域，一一加上

**Dollar**

```java
public class Dollar{
  int amount;
  Dollar(int amount){  
  }
  void times(int multiplier){
  }
}
```

​	继续测试，失败。 amount未初始化

**Dollar**

`int amount = 10;`

测试通过

#### 依赖关系与重复性设计

> ​	测试程序与代码所存在的问题不在于重复设计，而在于代码与测试程序之间的依赖关系—--你不可能只改动其中一个而不改动另外一个。
>
> ​	如果问题出在依赖关系上，那么其表现就是重复性设计。重复性设计通常表现为逻辑上的重复设计——相同的表达式在代码的多个地方出现。利用各种对象可以很好地抽象出逻辑上的重复设计。

这里重复性设计出现在测试中的数据与代码的数据之间

**Dollar**

`int amount = 5 * 2;`

一步无法消除5和2，可以不在对象初始化时给amount赋值，通过times方法赋值

**Dollar**

```java
int amount;
Dollar(int amount){
	this.amount = amount;
}

void times(int multiplier){
	amount *=  multiplier;
}
```



> ​	Task
>
> 1. **~~5美元 * 2 = 10 美元~~**  
> 2. **将"amount"定义为私有**  
> 3. **Dollar类有副作用吗？**
> 4. **钱数必须为整数？**

假设在Dollar对象上施加一个操作，Dollar对象改变，测试连续乘，代码如下：

```java
@Test public void testMultiplication(){
 Dollar five = new Dollar(5);
 five.times(2);
 assertEquals(five.amount,10);
 five.times(3);
 assertEquals(five.amount,15);
}
```

当第一次调用times()后，five变为10，第二个断言测试失败。

新增一个product中间对象

```java
@Test public void testMultiplication(){
  Dollar five = new Dollar(5);
  Dollar product = five.times(2);
  assertEquals(10,product.amount);
  product = five.times(3);
  assertEquals(15,product.mount);
}
```

改变Dollar.times()函数的声明,返回一个新的带有正确amount值的Dollar 对象

Dollar

```java
Dollar times(int multiplier){
	return new Dollar(amount * multiplier);
}
```



> ​	Task
>
> 1. **当瑞士法郎与美元的兑换率为2:1时，5美元+10瑞士法郎=10美元**
> 2. **~~5美元 * 2 = 10 美元~~**  
> 3. **将"amount"定义为私有**  
> 4. ~~**Dollar类有副作用吗？**~~
> 5. **钱数必须为整数？**
> 6. **实现equals()函数**
> 7. **实现hashCode()函数**

测试相等性，首先5美元等于5美元

```java
public void testEquality(){
  assertTrue(new Dollar(5).equals(new Dollar(5)));
}
```

测试没有运行通过。equals()的伪实现直接返回true:

Dollar

```java
public boolean equals(Object object){
	return true;
}
```

采用三角法，5美元！= 6美元

```java
@Test public void testEquality(){
  assertTrue(new Dollar(5).equals(new Dollar(5)));
  assertFalse(new Dollar(5).equals(new Dollar(6)));
}
```

现在使判等函数equals()一般化：

Dollar

```java
public voidequals(Object object){
  Dollar dollar = (Dollar)object;
  return amount == dollar.amount;
}

```

由于判等问题已解决，可以直接在Dollar对象之间作比较，所以amount可以设为私有

Dollar

`private int amount;`

> ​	Task
>
> 1. **当瑞士法郎与美元的兑换率为2:1时，5美元+10瑞士法郎=10美元**
> 2. **~~5美元 * 2 = 10 美元~~**  
> 3. ~~**将"amount"定义为私有**~~  
> 4. ~~**Dollar类有副作用吗？**~~
> 5. **钱数必须为整数？**
> 6. **~~实现equals()函数~~**
> 7. **实现hashCode()函数**
> 8. **与空对象判等**
> 9. **与非同类对象判等**
> 10. **5瑞士法郎 * 2 = 10 瑞士法郎**

从概念上讲Dollar.times()操作应该返回一个Dollar对象，这个对象的值是原对象的值乘以乘数，所以重写第一第二个断言，让Dollar对象之间进行比较，由于中间对象product作用不大，可采用内联方式：

```java
@Test public void testMultiplication(){
  Dollar five = new Dollar(5);
  assertEquals(new Dollar(10),five.times(2));
  assertEquals(new Dollar(15),five.times(3));
}
```

接下来写一个类似Dollar的对象，法郎。测试和代码类似Dollar：

```java
@Test public void testFrancMultiplication(){
  Franc five = new Franc(5);
  assertEquals(new Franc(10),five.times(2));
  assertEquals(new Franc(15),five.times(3));
}
```

Franc

```java
public class Franc{
  private int amount;
  public Franc(int mount){
  	this.amount = amount;
  }
  Franc times(int multiplier){
  	return new Franc(amount * multiplier);
  }
  public boolean equals(Object object){
  	Franc franc = (Franc) object;
  	return amount == franc.amount;
  }
}
```



> ​	Task
>
> 1. **当瑞士法郎与美元的兑换率为2:1时，5美元+10瑞士法郎=10美元**
> 2. **~~5美元 * 2 = 10 美元~~**  
> 3. ~~**将"amount"定义为私有**~~  
> 4. ~~**Dollar类有副作用吗？**~~
> 5. **钱数必须为整数？**
> 6. **~~实现equals()函数~~**
> 7. **实现hashCode()函数**
> 8. **与空对象判等**
> 9. **与非同类对象判等**
> 10. **~~5瑞士法郎 * 2 = 10 瑞士法郎~~**
> 11. **美元Dollar/瑞士法郎Franc之间的重复设计**
> 12. **普通判等**
> 13. **普通相乘**

由于Dollar对象和Franc对象存在大量的重复性设计，可以利用抽象的方式，让他们继承同一个父类，将共同变量，方法上移至父类Money中：

Money

```java
class Money{
  protected int amount;//amount的可见性必须由private改为protected，以便子类能看见
}
```

将Dollar和Franc的equals方法进行修改，并上移至父类Money中：

Dollar

```java
public boolean equals(Object object){
  Money money = (Money)object;
  return amount == money.amount;
}
```

法郎的判等测试：

```java
@Test public void testEquality(){
  assertTrue(new Dollar(5).equals(new Dollar(5)));
  assertFalse(new Dollar(5).equals(new Dollar(6))); 
  assertTrue(new Franc(5).equals(new Franc(5)));
  assertFalse(new Franc(5).equals(new Franc(6)));
  assertFalse(new Franc(5).equals(new Dollar(5)));//测试5美元==5法郎？？？
}
```

通过比较两个Money对象相等当且仅当他们的数值和类均相同：

Money

```java
public boolean equals(Object object){
  Money money = (Money) object;
  return amount == money.amount && getClass().equals(money.getClass());
}
```



> ​	Task
>
> 1. **当瑞士法郎与美元的兑换率为2:1时，5美元+10瑞士法郎=10美元**
> 2. **~~5美元 * 2 = 10 美元~~**  
> 3. ~~**将"amount"定义为私有**~~  
> 4. ~~**Dollar类有副作用吗？**~~
> 5. **钱数必须为整数？**
> 6. **~~实现equals()函数~~**
> 7. **实现hashCode()函数**
> 8. **与空对象判等**
> 9. **与非同类对象判等**
> 10. **~~5瑞士法郎 * 2 = 10 瑞士法郎~~**
> 11. **美元Dollar/瑞士法郎Franc之间的重复设计**
> 12. **~~普通判等~~**
> 13. **普通相乘**
> 14. **~~比较法郎对象与美元对象~~**
> 15. **货币？**
> 16. **删除testFrancMultiplication？**

接下来去除共同的times()函数，以实现多币种计算。

首先将time()函数都返回一个Money对象：

Dollar

```java
Money times(int multiplier){
  return new Dollar(amount * multiplier);
}
```

Franc

```java
Money times(int multiplier){
  return new Franc(amount * multiplier);
}
```

在Money类中引入一个返回Dollar对象的工厂方法，就像这样：

```java
@Test public void testMultiplication(){
  Dollar five = Money.dollar(5);
  assertEquals(new Dollar(10),five.times(2));
  assertEquals(new Dollar(15),five.times(3));
}
```

创建并返回一个Dollar对象：

Money

```java
public static Dollar dollar(int amount){
  return new Dollar(amount);
}
```

将引用Dollar的地方修改如下：

```java
@Test public void testMultiplication(){
  Money five = Money.dollar(5);
  assertEquals(new Dollar(10),five.times(2));
  assertEquals(new Dollar(15),five.times(3));
}
```

此时测试出错，编译器指出times()函数并不是Money类中定义的方法，所以将Money类改为抽象类，并声明Money.times()函数,在子类中实现times()函数,(Franc类也仿照Dollar的修改思路):

Money

```java
abstract class Money{
  abstract Money times(int multiplier);
	static Money dollar(int amount){
    return new Dollar(amount);
	}
	static Money franc(int amount){
		return new Franc(amount);
	}
}
```

将工厂方法用到各个测试中去：

```java
@Test public void testMultiplication(){
  Money five = Money.dollar(5);
  assertEquals(Money.dollar(10),five.times(2));
  assertEquals(Money.dollar(15),five.times(3));
}
```

```java
@Test public void testEquality(){
	assertTrue(Money.dollar(5).equals(Money.dollar(5)));
	assertFalse(Money.dollar(5).equals(Money.dollar(6)));
	assertTrue(Money.franc(5).equals(Money.franc(5)));
	assertFalse(Money.franc(5).equals(Money.franc(6)));
	assertFalse(Money.franc(5).equals(Money.dollar(5)));//测试5美元==5法郎？？？
}
```

```java
@Test public testFrancMultication(){
  Money five = Money.franc(5);
  assertEquals(Money.franc(10),five.times(2));
  assertEquals(Money.franc(15),five.times(3));
}
```

为了消除多且无用的子类，引入货币的概念：

```java
@Test public void testCurrency(){
  assertEquals("USD",Money.dollar(1).currency());
  assertEquals("CHF",Money.franc(1).currency());
}
```

在Money声明currency()方法：

Money

`abstract String currency();`

由于货币变量的声明，获得货币返回值，在两个子类中都有，所以都上移至父类Money中：

Money

```java
protected String currency;
String currency(){
  return currency;
}
```

构造函数中货币的初始化:

Dollar

```java
Dollar(int amount){
  this.amount = amount;
  currency = "USD";
}
```

Franc

```java
Franc(int amount){
  this.amount = amount;
  currency = "CHF";
}
```

如果将字符串常量"USD"和"CHF"移至静态工厂方法中，那么两个构造函数将完全相同,将构造方法上移至Money中：

Dollar

```java
Dollar(int amount,String currency){
 super(amount,currency);
}
```

Franc

```java
Franc(int amount,String currency){
  super(amount,currency);
}
```

这次改动导致构造函数的调用者无法工作：

Money

```java
Money(int amount,String currency){
  this.amount = amount;
  this.currency = currency;
}
static Money franc(int amount){
  return new Franc(amount,"CHF");
}
static Money dollar(int amount){
  return new Dollar(amount,"USD");
}
```

此时Dollar和Franc类的times()方法可以直接改为用工厂方法创建：

Dollar

```java
public Money times(int multiplier){
  return Money.dollar(amount * multiplier);
}
```

Franc

```java
public Money times(int multiplier){
  return Money.franc(amount * multiplier);
}
```

这里出现一个问题，两个子类的times()方法，使用工厂方法后，没法继续抽象，所以还原回去：

Dollar

```java
public Money times(int multiplier){
  return new Dollar(amount * multiplier,"USD");
}
```

Franc

```java
public Money times(int multiplier){
  return new Franc(amount * multiplier,"CHF");
}
```

此时如果两个子类返回的是new Money呢？货币常量用currency代替呢？

Dollar

```java
public Money times(int multiplier){
  return new Money(amount * multiplier,currency);
}
```

Franc

```java
public Money times(int multiplier){
  return new Money(amount * multiplier,currency);
}
```

到这一步，几乎可以删除两个子类了，但是子类的times()方法不可能返回一个抽象类的实例，所以讲Money类的abstract去掉，同时times()方法也改为一个实际方法：

Money

```java
class Money{
  public Money times(int multiplier){
    return new Money(amount * multiplier,currency);
	}
}
```

测试：

```java
@Test testDifferentClassEquality(){
  assertTrue(new Money(10,"CHF").equals(new Franc(10,"CHF")));
}
```



此时运行测试会报错，给Money类加上toString()方法，错误信息为：10 CHF 期望的是 10 CHF

由于之间测试equals()方法时，比较的是类，而不是货币种类，所以：

Money

```java
public boolean equals(Object object){
  Money money = (Money) object;
  return amount == money.amount && currency.equals(money.currency());
}
public static Money dollar(int amount){
  return new Money(amount,"USD");
}
public static Money franc(int amount){
  return new Money(amount,"CHF");
}

```

到现在已经不需要Dollar和Franc类了,关于Franc的引用和测试都可以删除了



> ​	Task
>
> 1. **当瑞士法郎与美元的兑换率为2:1时，5美元+10瑞士法郎=10美元**
> 2. **~~5美元 * 2 = 10 美元~~**  
> 3. ~~**将"amount"定义为私有**~~  
> 4. ~~**Dollar类有副作用吗？**~~
> 5. **钱数必须为整数？**
> 6. **~~实现equals()函数~~**
> 7. **实现hashCode()函数**
> 8. **与空对象判等**
> 9. **与非同类对象判等**
> 10. **~~5瑞士法郎 * 2 = 10 瑞士法郎~~**
> 11. ~~**美元Dollar/瑞士法郎Franc之间的重复设计**~~
> 12. **~~普通判等~~**
> 13. **~~普通相乘~~**
> 14. **~~比较法郎对象与美元对象~~**
> 15. **~~货币？~~**
> 16. ~~**删除testFrancMultiplication？**~~
> 17. **5美元 + 5美元 = 10美元**

