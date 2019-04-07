> Task
>
> 1. **当瑞士法郎与美元的兑换率为2:1时，5美元+10瑞士法郎=10美元**
> 2. **5美元+5美元=10美元**



加法测试：

```java
@Test public void testSimpleAddition(){
  Money sum = Money.dollar(5).plus(Money.dollar(5));
  assertEquals(Money.dollar(10),sum);
}
```

添加plus方法：

Money

```java
Money plus(Money addend){
  return new Money(amount + addend.amount,currency);
}
```

​	当你所拥有的对象并没有按照你的要求行事时，那么就创建另外一个具有相同外部协议，但具体实现不同的对象。

​	Money对象是表达式中无法再继续细分的元素。通过操作形成表达式，其中之一就是Money对象的和Sum。一旦操作完成，在给定一组汇率后，运算的结果就能够化归为某种单一的货币。

```java
public void testSimpleAddition(){
  Money five = Money.dollar(5);
  Expression sum = five.plus()5;//两个Money对象和表达式
  Bank bank = new Bank();
  Money reduced = bank.reduce(sum,"USD");
	assertEquals(Money.dollar(10),reduced);  
}
```

为了使代码通过编译增加如下：

s

Expression

`interface Expression`

Money.plus() 需要返回一个表达式(Expression),Money实现Expression接口::

Money

```java
class Money implements Expression{
  Expression plus(Money addend){
    return new Money(amount + addend.amount,currency);
  }
}
```

Bank

```java
class Bank{
  Money reduce(Expression source ,String to){
    return Money.dollar(10);
  }
}
```

> Task
>
> 1. **当瑞士法郎与美元的兑换率为2:1时，5美元+10瑞士法郎=10美元**
> 2. **5美元+5美元=10美元**
> 3. **从5美元+5美元返回一个Money对象**

​	首先，Money.plus()需要返回的是一个真正的表达式(Expression)对象—Sum，而不仅仅是一个Money对象。

两个Money对象的和应该是一个Sum对象：

```java
public void testPlusReturnSum(){
  Money five = Money.dollar(5);
  Expression result = five.plus(five);
  Sum sum = (Sum) result;
  assertEquals(five,sum.augend);
  assertEquals(five,sum.addend);
}
```

添加Sum：

Sum

```java
class Sum implements Expression{
  Money augend;
  Money addend;
  Sum(Money augend,Money addend){
    this.augend = augend;
    this.addend = addend;
  }
}
```

此时运行会产生一个ClassCaseException，因为Money.plus()返回的是Money对象，而不是Sum对象。

Money

```java
Expression plus(Money addend){
  return new Sum(this,addend);
}
```

此时可以向Bank.reduce()传递一个Sum对象了。若Sum中的货币全都一样，而且目标货币也一样，则结果应该是一个Money对象，它的数目是各数目的总和：

```java
public void testReduceSum(){
  Expression sum = new Sum(Money.dollar(3),Money.dollar(4));
  Bank bank = new Bank();
  Money result = bank.reduce(sum,"USD");
  assertEquals(Money.dollar(7),result);
}
```

Bank

```java
Money reduce(Expression source,String to){
  Sum sum = (Sum) source;
  int amount = sum.augend + sum.addend.amount;
  return new Money(amount,to);
}
```

这立刻使两方面：

- 强制类型转换。这段代码应该对任何表达式都适用。
- 公共域以及对它的两级引用。

首先，可可以把方法的主体移至Sum类且去掉某些可见域。

Bank

```java
Money reduce(Expression source,String to){
  Sum sum = (Sum) source;
  return Sum.reduce(to);
}
```

Sum

```java
public Money reduce(String to){
  int amount = augend.amount + addend.amount;
  return new Money(amount,to);
}
```

> Task
>
> 1. **当瑞士法郎与美元的兑换率为2:1时，5美元+10瑞士法郎=10美元**
> 2. **5美元+5美元=10美元**
> 3. **从5美元+5美元返回一个Money对象**
> 4. **Bank.reduce(Money)**

测试Bank.reduce():

```java
public void testReduceMoney(){
  Bank bank = new Bank;
  Money result = bank.reduce(Money.dollar(1),"USD");
  assertEquals(Money.dollar(1),result);
}
```

Bank

```java
Money reduce(Expression source,String to){
  if(source instanceof Money) return (Money)source;
  Sum sum = (Sum) source;
  return sum.reduce(to);
}
```

由于Sum实现了reduce(String),所以如果Money也实现了它，name我们就可以把它添加到Expression接口中：

Bank

```java
Money reduce(Expression source,String to){
  if(source instanceof Money) 
    return (Money)source.reduce(to);
  Sum sum = (Sum) source;
  return sum.reduce(to);
}
```

Money

```java
public Money reduce(String to){
  return this;
}
```

将reduce(String)添加到Expression接口中就能消除所有强制类型转换：

Expression

`Money reduce(String to);`

Bank

```java
Money reduce(Expression source,String to){
  return source.reduce(to);
}
```



> Task
>
> 1. **当瑞士法郎与美元的兑换率为2:1时，5美元+10瑞士法郎=10美元**
> 2. **5美元+5美元=10美元**
> 3. **从5美元+5美元返回一个Money对象**
> 4. ~~**Bank.reduce(Money)**~~
> 5. **带换算的Reduce Money**
> 6. **Reduce(Bank,Stirng)**
> 7. 

两法郎，得到一美元：

```java
public void testRedeuceMoneyDifferentCurrency(){
  Bank bank = new Bank();
  bank.addRate("CHF","USD",2);
  Money result = bank.reduce(Money.franc(2),"USD");
  assertEquals(Money.dollar(1),result);
}
```

兑换率与Bank类相关，将Bank作为一个参数传递给Expression.reduce()：

Bank

```java
Money reduce(Expression source,String to){
  return source.reduce(this,to);
}
```

Expression

`Money reduce(Bank bank,Stirng to);`

Sum

```java
public Money reduce(Bank bank,String to){
  int amount = augend.amount + addend.amount;
  return new Money(amount,to);
}
```

Money

```java
public Money reduce(Bank bank,String to){
  int rate = (currency.equals("CHF") && to.equals("USD")) ? 2 : 1;
  return new Money(amount / rate,to);
}
```

计算Bank中的兑换率：

Bank

```java
int rate(String from,String to){
  return (from.equals("CHF") && to.equals("USD")) ? 2 : 1;
}
```

向bank询问正确的兑换率：

Money

```java
public Money reduce(Bank bank,String to){
  int rate = bank.rate(currency, to);
  return new Money(amount / rate, to);
}
```

















--------

