# 02-assert 断言详细使用

## 前言

- 与 unittest 不同，pytest 使用的是 python 自带的 assert 关键字来进行断言
- assert 关键字后面可以接一个表达式，只要表达式的最终结果为 True，那么断言通过，用例执行成功，否则用例执行失败

## assert 小栗子

想在抛出异常之后输出一些提示信息，执行之后就方便查看是什么原因了

```
# 异常信息
def f():
    return 3
def test_function():
    a = f()
    assert a % 2 == 0, "判断 a 为偶数，当前 a 的值为：%s" % a
```

### 执行结果

[![img](https://img2020.cnblogs.com/blog/1896874/202004/1896874-20200406130902312-172338387.png)](https://img2020.cnblogs.com/blog/1896874/202004/1896874-20200406130902312-172338387.png)

## 常用断言

pytest 里面断言实际上就是 python 里面的 assert 断言方法，常用的有以下几种

- assert xx ：判断 xx 为真
- assert not xx ：判断 xx 不为真
- assert a in b ：判断 b 包含 a
- assert a == b ：判断 a 等于 b
- assert a != b ：判断 a 不等于 b

## 异常断言

可以使用 pytest.raises 作为上下文管理器，当抛出异常时可以获取到对应的异常实例

```
# 断言异常
def test_zero_division():
    with pytest.raises(ZeroDivisionError):
        1 / 0
```

**断言场景：**断言它抛的异常是不是预期想要的

**代码执行：**1/0

**预期结果：**抛的异常是 ZeroDivisionError: division by zero

**如何断言：**通常是断言异常的 type 和 value 值了

**具体方式：**这里 1/0 的异常类型是 ZeroDivisionError，异常的 value 值是 divisionby zero

```
# 详细断言异常
def test_zero_division_long():
    with pytest.raises(ZeroDivisionError) as excinfo:
        1 / 0

    # 断言异常类型 type
    assert excinfo.type == ZeroDivisionError
    # 断言异常 value 值
    assert "division by zero" in str(excinfo.value)
```

excinfo ：是一个异常信息实例

**主要属性：** .type 、 .value 、 .traceback

**注意：**断言 type 的时候，异常类型是不需要加引号的，断言 value 值的时候需转 str

## 拓展一：match

可以将 match 关键字参数传递给上下文管理器，以测试正则表达式与异常的字符串表示形式是否匹配

**注意：**这种方法只能断言 value，不能断言 type

```
# 自定义消息
def test_zero_division_long():
    with pytest.raises(ZeroDivisionError, match=".*zero.*") as excinfo:
        1 / 0
```

该 match 方法的 regexp 参数与 re.search 函数匹配，因此在上面的示例中 match='zero' 也可以使用

[![img](https://img2020.cnblogs.com/blog/1896874/202004/1896874-20200406135355943-1838438116.png)](https://img2020.cnblogs.com/blog/1896874/202004/1896874-20200406135355943-1838438116.png)

[![img](https://img2020.cnblogs.com/blog/1896874/202004/1896874-20200406135407511-1382697695.png)](https://img2020.cnblogs.com/blog/1896874/202004/1896874-20200406135407511-1382697695.png)

## 拓展二：检查断言装饰器

```
# 断言装饰器
@pytest.mark.xfail(raises=ZeroDivisionError)
def test_f():
    1 / 0
```

### 执行结果

[![img](https://img2020.cnblogs.com/blog/1896874/202004/1896874-20200406140623462-429848886.png)](https://img2020.cnblogs.com/blog/1896874/202004/1896874-20200406140623462-429848886.png)

### 知识点

- 代码抛出异常，但是和 raises 指定的异常类相匹配，所以不会断言失败
- 它相当于一个**检查异常装饰器\*\***，功能：\*\*检查是否有异常，不确定是否有异常
- with pytest.raise(ZeroDivisionError) 对于故意测试异常代码的情况，使用可能会更好
- `而@pytest.mark.xfail(raises=ZeroDivisionError) `对于检查未修复的错误（即，可能会发生异常），使用检查断言可能会更好
