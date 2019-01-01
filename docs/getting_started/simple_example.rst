.. index::
    single: Getting Started; Simple Example

示例
==============

假设我们有一个 ``Temperature`` 类， 它在报告一个平均温度之前对一个地区的温度进行. 它的数据可以来自web或者任何其他类型的数据，但我们目前没有这样的数据.
然而，我们可以根据与 ``Temperature`` 类的交互来假设与这类的一些基本交互：

.. code-block:: php

    class Temperature
    {
        private $service;

        public function __construct($service)
        {
            $this->service = $service;
        }

        public function average()
        {
            $total = 0;
            for ($i=0; $i<3; $i++) {
                $total += $this->service->readTemp();
            }
            return $total/3;
        }
    }

Even without an actual service class, we can see how we expect it to operate.
When writing a test for the ``Temperature`` class, we can now substitute a
mock object for the real service which allows us to test the behaviour of the
``Temperature`` class without actually needing a concrete service instance.

.. code-block:: php

    use \Mockery;

    class TemperatureTest extends PHPUnit_Framework_TestCase
    {
        public function tearDown()
        {
            Mockery::close();
        }

        public function testGetsAverageTemperatureFromThreeServiceReadings()
        {
            $service = Mockery::mock('service');
            $service->shouldReceive('readTemp')
                ->times(3)
                ->andReturn(10, 12, 14);

            $temperature = new Temperature($service);

            $this->assertEquals(12, $temperature->average());
        }
    }

We create a mock object which our ``Temperature`` class will use and set some
expectations for that mock — that it should receive three calls to the ``readTemp``
method, and these calls will return 10, 12, and 14 as results.

.. note::

    PHPUnit integration can remove the need for a ``tearDown()`` method. See
    ":doc:`/reference/phpunit_integration`" for more information.
