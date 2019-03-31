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

Money

```java
Money plus(Money addend){
  return new Money(amount + addend.amount,currency);
}
```

