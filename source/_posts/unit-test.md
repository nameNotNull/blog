---
title: yaf的单元测试
date: 2018-08-17 10:46:02
tags: PHP
categories: php
---
单元测试的目的是为了验证系统中某一部分逻辑的功能是否正确。在多人开发的过程中，各个功能模块可能会互相调用，编写单元测试和执行单元测试能有效提高代码的健壮性、可靠性。
单元测试能够屏蔽其他模块的依赖，mock可以解决其他模块的依赖问题。
PHP的单元测试实际项目很少有人有耐心去写，今天总结一下，简单的单元测试其实很容易编写，由于yaf的自动加载的问题比较麻烦，今天重点记录yaf的单元测试常用写法。

## PHPUnit
PHPUnit是php的一个测试框架，封装了大量测试php的类和方法，这里我打算用这个组件进行单元测试。
PHPUnit可以由composer进行安装，
在yaf目录中配置composer.json文件
```$xslt
"require": {
    "phpunit/phpunit": "6.0.8"
    }
```
执行composer install即可

PHPUnit 的测试类继承于 TestCase 类，该类的父类实现了断言assertEquals和模拟数据createMock

断言就是验证某个接口或者对象的值是否和预期一致，执行单元测试命令时，在结果里会显示断言的数量以及断言验证的结果正确数

```断言
$this->assertEquals(1,XX_Model::getInstance()->getListByUserId('123'));

```
模拟数据就是解决其他模块的依赖，生成mock数据

```mock
$stub = $this->createMock(XX_Model::class);
$stub->method('get')->willReturn(3); 		
$this->assertEquals(3,$stub->get(1));
		
```

上述stub的get方法就必然返回3，更复杂的依赖解决参见 more info


在yaf项目目录下新建一个test目录，创建单元测试文件test.php

创建测试类，测试类的成员方法必须是以test开头，执行单元测试命令时，会顺序执行这些test方法

yaf的自动加载需要配合yaf的bootstrap指定加载方法

```
$application = new Yaf\Application(ROOT_PATH . "/config/application.ini",'develop');
$application->bootstrap();
Yaf\Registry::set('application', $application);
```

整个单元测试的样例如下

```
<?php
/**
 * 1、composer 安装Monolog日志扩展，安装phpunit单元测试扩展包
 * 2、引入autoload.php文件
 * 3、测试案例
 *
 *
// */
//namespace App\tests;
ini_set('yaf.name_separator', '_');
require_once __DIR__ . '/../vendor/autoload.php';
define("ROOT_PATH", dirname(__DIR__) . "/");

use Monolog\Logger;
use Monolog\Handler\StreamHandler;

use PHPUnit\Framework\TestCase;
use Yaf\Bootstrap_Abstract;
use Yaf\Dispatcher;
use Yaf\Loader;
use Yaf\Registry;

define('APPLICATION_PATH', ROOT_PATH . '/application');


class YafTest extends TestCase
{
    public function testSearch()
    {
        $application = new Yaf\Application(ROOT_PATH . "/config/application.ini",'develop');
        $application->bootstrap();
        Yaf\Registry::set('application', $application);
        $this->assertEquals(1,XX_Model::getInstance()->getListByUserId('123'));
        $stub = $this->createMock(XX_Model::class);
        $stub->method('get')->willReturn(3); 					//2
        $this->assertEquals(3,$stub->get(1));
    }

    public function Log()
    {
        // create a log channel
        $log = new Logger('Tester');
        $log->pushHandler(new StreamHandler(ROOT_PATH . 'storage/logs/app.log', Logger::WARNING));
        return $log;
    }
}

```
执行单元测试

```$xslt
php ./vendor/bin/phpunit tests/test.php 
```

实际效果

![单元测试](/blog/resource/img/phpunit.png)


额外说明一下，php的xdebug扩展和phpunit是不兼容的，执行单元测试，需要关闭xdebug扩展。

More info: [phpunit依赖解决](http://www.phpunit.cn/manual/current/zh_cn/writing-tests-for-phpunit.html#writing-tests-for-phpunit.test-dependencies)





