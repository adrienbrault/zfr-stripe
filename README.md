ZfrStripe
=========

[![Latest Stable Version](https://poser.pugx.org/zfr/zfr-stripe/v/stable.png)](https://packagist.org/packages/zfr/zfr-stripe)

ZfrStripe is a modern PHP library based on Guzzle for [Stripe payment system](https://www.stripe.com).

> Note : this library does not contain tests, mainly because I'm not sure about how to write tests for an API
wrapper. Don't hesitate to help on this ;-).

## Dependencies

* [Guzzle](http://www.guzzlephp.org): >= 3.5

## Installation

Installation of ZfrStripe is only officially supported using Composer:

```sh
php composer.phar require zfr/zfr-stripe:1.*
```

## Tutorial

First, you need to instantiate the Stripe client, passing your private API key (you can find it in your Stripe
settings):

```php
$client = new StripeClient('my-api-key');
```

The currently supported version of the API is version 1.0.

### How to use it?

Using the client is easy. For instance, here is how you'd create a new offer:

```php
$details = $client->createCharge(array(
    'amount'   => 500,
    'currency' => 'EUR',
    'customer' => 'cus_37EGGf4LMGgYy8'
));
```

The parameters have a direct one-to-one mapping with the official documentation (for any reference, you can also
check the `ZfrStripe\Client\ServiceDescription\Stripe-1.0.php` file). To know what the responses look like, please
refer to the [official API reference](https://stripe.com/docs/api).

### Exceptions

ZfrStripe tries its best to throw useful exceptions. Two kinds of exceptions can occur:

* Guzzle exceptions: by default, Guzzle automatically validate parameters according to rules defined in the
 service description **before** sending the actual request. If you encounter those exceptions, you likely have broken
 code.
* ZfrStripe exceptions: those exceptions are thrown if an error occurred on Stripe side. Each exception implement
`ZfrStripe\Exception\ExceptionInterface`.

Here are all the exceptions:

* `ZfrPaymill\Exception\UnauthorizedException`: your API key is likely to be wrong...
* `ZfrPaymill\Exception\TransactionErrorException`: occurs for 402 errors. According to Stripe, this error happen when
parameters were valid but the request failed (for instance if a card CVC is invalid).
* `ZfrPaymill\Exception\NotFoundException`: is thrown whenever client receives a 404 exception.
* `ZfrPaymill\Exception\ValidationErrorException`: some errors on your sent data.
* `ZfrPaymill\Exception\ServerErrorException`: any errors where Stripe is likely to be doing something wrong...

Usage:

```php
try {
    $client->createTransaction(array(
        'amount'   => 4000,
        'currency' => 'EUR',
        'token'    => '1234'
    ));
} catch (\ZfrStripe\Exception\TransactionErrorException $exception) {
    // Seems the transaction failed, let's see why:
    $why = $exception->getMessage();

    // Let's also get the response to have more info:
    $response = $exception->getResponse();
} catch (\Exception $exception) {
    // Catch any other exception...
}
```
