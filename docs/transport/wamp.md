# Web Application Messaging Protocol (WAMP) Transport

A transport for [Web Application Messaging Protocol](https://wamp-proto.org/).
WAMP is an open standard WebSocket subprotocol.
It uses internally Thruway PHP library [voryx/thruway](https://github.com/voryx/Thruway) 

* [Installation](#installation)
* [Start the WAMP router](#start-the-wamp-router)
* [Create context](#create-context)
* [Consume message](#consume-message)
* [Subscription consumer](#subscription-consumer)
* [Send message to topic](#send-message-to-topic)

## Installation

```bash
$ composer require enqueue/wamp
```

## Start the WAMP router

```bash
$ php vendor/voryx/thruway/Examples/SimpleWsRouter.php
```

Thruway is now running on 127.0.0.1 port 9090 


## Create context

```php
<?php
use Enqueue\Wamp\WampConnectionFactory;

$connectionFactory = new WampConnectionFactory();

$context = $connectionFactory->createContext();

// if you have enqueue/enqueue library installed you can use a factory to build context from DSN 
$context = (new \Enqueue\ConnectionFactoryFactory())->create('wamp:')->createContext();
```

## Consume message:

Start message consumer before send message to the topic

```php
<?php
/** @var \Enqueue\Wamp\WampContext $context */

$fooTopic = $context->createTopic('foo');

$consumer = $context->createConsumer($fooQueue);

while (true) {
    if ($message = $consumer->receive()) {
        // process a message
    }
}
```

## Subscription consumer

```php
<?php
use Interop\Queue\Message;
use Interop\Queue\Consumer;

/** @var \Enqueue\Wamp\WampContext $context */
/** @var \Enqueue\Wamp\WampDestination $fooQueue */
/** @var \Enqueue\Wamp\WampDestination $barQueue */

$fooConsumer = $context->createConsumer($fooQueue);
$barConsumer = $context->createConsumer($barQueue);

$subscriptionConsumer = $context->createSubscriptionConsumer();
$subscriptionConsumer->subscribe($fooConsumer, function(Message $message, Consumer $consumer) {
    // process message
    
    return true;
});
$subscriptionConsumer->subscribe($barConsumer, function(Message $message, Consumer $consumer) {
    // process message
    
    return true;
});

$subscriptionConsumer->consume(2000); // 2 sec
```

## Send message to topic

```php
<?php
/** @var \Enqueue\Wamp\WampContext $context */

$fooTopic = $context->createTopic('foo');
$message = $context->createMessage('Hello world!');

$context->createProducer()->send($fooTopic, $message);
```

[back to index](../index.md)