# Elastic Beanstalk middleware for Laravel and HTTPS
This middlware will ensure that your Laravel app will correctly recognise **secure** requests when running on Elastic Beanstalk with a Load Balancer.
**NOTE:** make sure your web server is not publicly accessible and that the Load Balancer only have access (you can manage that through AWS security groups).

There is also a [gist](https://gist.github.com/peppeocchi/4f522663d7e88029daeba833c835df3d) that does the exact same thing.

## Installation
You can install this middleware through [Composer](https://getcomposer.org/)
```
composer require peppeocchi/laravel-elb-middleware
```

## Usage
The simplest way to use the middleware is to add it as a global middleware in `app/Http/Kernel.php`
```php
...
class Kernel extends HttpKernel
{
    /**
     * The application's global HTTP middleware stack.
     *
     * These middleware are run during every request to your application.
     *
     * @var array
     */
    protected $middleware = [
        \Illuminate\Foundation\Http\Middleware\CheckForMaintenanceMode::class,
        \GO\ElasticBeanstalkHttps::class
    ];
...
```
but you are free to add it to a middleware group or directly into your controllers.

### TL;DR
On Elastic Beanstalk (with a load balancer), all the requests are being "proxied" to port 80. The load balancer will add the `x-forwarded-*` headers to the request.

Laravel `Request` inherits from `Symfony\Component\HttpFoundation\Request`, so it already supports the `x-forwarded-*` headers, but it needs to be configured to look at those headers or you are going to get incorrect informations about the request (eg. `$request->isSecure()` will always return false).

The Amazon ELB don't have a static IP or a range to target, so you'll need to trust all proxies.
Of course you need to make sure your web server will respond only to the [load balancer](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.managing.elb.html).

#### Read also
[Symfony documentation](https://symfony.com/doc/current/request/load_balancer_reverse_proxy.html)
