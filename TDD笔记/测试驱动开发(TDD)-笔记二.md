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

1



















--------









o