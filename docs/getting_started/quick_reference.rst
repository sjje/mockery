.. index::
    single: Quick Reference

快速参考
===============

本页面的目的是简要概述一些最常见的 Mockery 功能。

查阅这里 :文档:`../reference/index` ，来学习所有的 Mockery 功能.

集成 Mockery 到 PHPUnit, 通过扩展 ``MockeryTestCase``:

.. code-block:: php

    use \Mockery\Adapter\Phpunit\MockeryTestCase;

    class MyTest extends MockeryTestCase
    {
    }

或者使用 ``MockeryPHPUnitIntegration`` trait:

.. code-block:: php

    use \PHPUnit\Framework\TestCase;
    use \Mockery\Adapter\Phpunit\MockeryPHPUnitIntegration;

    class MyTest extends TestCase
    {
        use MockeryPHPUnitIntegration;
    }

 创建一个 test double:

.. code-block:: php

    $testDouble = \Mockery::mock('MyClass');

创建一个 test double 实现 MyClass 的接口 MyInterface:

.. code-block:: php

    $testDouble = \Mockery::mock('MyClass, MyInterface');

期望 test double 可以有 foo 的方法可调用:

.. code-block:: php

    $testDouble = \Mockery::mock('MyClass');
    $testDouble->shouldReceive('foo');

期望 test double **没有** foo 方法可以调用:

.. code-block:: php

    $testDouble = \Mockery::mock('MyClass');
    $testDouble->shouldNotReceive('foo');

期望 test double 可以调用 foo 使用 $arg 做参数，可被调用一次，并返回 $returnValue 作为返回值。

.. code-block:: php

    $mock = \Mockery::mock('MyClass');
    $mock->shouldReceive('foo')
        ->once()
        ->with($arg)
        ->andReturn($returnValue);

期望 test double 多次调用 foo 方法返回 andReturn 设定的值:

.. code-block:: php

    $mock = \Mockery::mock('MyClass');
    $mock->shouldReceive('foo')
        ->andReturn(1, 2, 3);

    $mock->foo(); // int(1);
    $mock->foo(); // int(2);
    $mock->foo(); // int(3);
    $mock->foo(); // int(3);

创建一个运行时 partial test double ( partial mock 是说一个类的方法有些是实际调用，有些是使用 mock 的 stub ):

.. code-block:: php

    $mock = \Mockery::mock('MyClass')->makePartial();

创建一个 spy:
( spy 的原理是，如果不打 stub 默认都会执行真实的方法，如果打 stub 则返回 stub 实现。)

.. code-block:: php

    $spy = \Mockery::spy('MyClass');

期望 spy 可以接受 foo 方法的调用:

.. code-block:: php

    $spy = \Mockery::spy('MyClass');

    $spy->foo();

    $spy->shouldHaveReceived()->foo();

稍微复杂点的例子
^^^^^^^^^^^^^^^^^^^^^^

从一个数组中创建 Mock Object , 用数组的 key 作为 Mock Object 的方法名, value 作为 Mock Object 的返回值:

.. code-block:: php

    use \Mockery\Adapter\Phpunit\MockeryTestCase;

    class SimpleTest extends MockeryTestCase
    {
        public function testSimpleMock()
        {
            $mock = \Mockery::mock(array('pi' => 3.1416, 'e' => 2.71));
            $this->assertEquals(3.1416, $mock->pi());
            $this->assertEquals(2.71, $mock->e());
        }
    }

创建一个 mock 对象，定义调用 divideBy 将返回一个 \Mockery\Undefined 的实例:

.. code-block:: php

    use \Mockery\Adapter\Phpunit\MockeryTestCase;

    class UndefinedTest extends MockeryTestCase
    {
        public function testUndefinedValues()
        {
            $mock = \Mockery::mock('mymock');
            $mock->shouldReceive('divideBy')->with(0)->andReturnUndefined();
            $this->assertTrue($mock->divideBy(0) instanceof \Mockery\Undefined);
        }
    }

创建一个 mock 对象拥有两个可调用的方法:

.. code-block:: php

    use \Mockery\Adapter\Phpunit\MockeryTestCase;

    class DbTest extends MockeryTestCase
    {
        public function testDbAdapter()
        {
            $mock = \Mockery::mock('db');
            $mock->shouldReceive('query')->andReturn(1, 2, 3);
            $mock->shouldReceive('update')->with(5)->andReturn(NULL)->once();

            // ... test code here using the mock
        }
    }

使用 ordered() 期望所有的查询总是在更新之前执行:

.. code-block:: php

    use \Mockery\Adapter\Phpunit\MockeryTestCase;

    class DbTest extends MockeryTestCase
    {
        public function testQueryAndUpdateOrder()
        {
            $mock = \Mockery::mock('db');
            $mock->shouldReceive('query')->andReturn(1, 2, 3)->ordered();
            $mock->shouldReceive('update')->andReturn(NULL)->once()->ordered();

            // ... test code here using the mock
        }
    }

创建一个 mock 对象 ，定义 query() 方法，在传入不同参数的时候返回不同的值，
并且使用 ordered() 和 ordered('params') 来规定他们执行的时间:

.. code-block:: php

    use \Mockery\Adapter\Phpunit\MockeryTestCase;

    class DbTest extends MockeryTestCase
    {
        public function testOrderedQueries()
        {
            $db = \Mockery::mock('db');
            $db->shouldReceive('startup')->once()->ordered();
            $db->shouldReceive('query')->with('CPWR')->andReturn(12.3)->once()->ordered('queries');
            $db->shouldReceive('query')->with('MSFT')->andReturn(10.0)->once()->ordered('queries');
            $db->shouldReceive('query')->with(\Mockery::pattern("/^....$/"))->andReturn(3.3)->atLeast()->once()->ordered('queries');
            $db->shouldReceive('finish')->once()->ordered();

            // ... test code here using the mock
        }
    }
