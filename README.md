# Laravel APM

This guide will walk you through the process of installing and configuring our Laravel apm package in your project.

## Prerequisites

- Laravel project (version 8.x or higher recommended)
- Composer
- PHP 7.4 or higher

## Installation

To install the package, follow these steps:

1. Install Middleware Host Agent (If not already installed), see our [Installation Guide.](https://docs.middleware.io/agent-installation/overview)


2. Install the package using Composer:

   ```bash
   composer require Middleware/laravel-apm
   ```

3. Install Opentelemetry extension:
    ```bash
    sudo pecl install channel://pecl.php.net/opentelemetry-1.0.0beta3
    ```

4. Add the extension to your php.ini file.
    - To find loaded php.ini file, run:
        ```bash
        php --ini
        ```
    - You'll see file path under "Loaded Configuration File:" attribute.
    - Add following lines in the file:
        ```bash
        [opentelemetry]
        extension=opentelemetry.so
        ``` 
    - Verify if extension is installed:
        ```bash
        php -m | grep opentelemetry
        ```

5. Add the service provider to the `providers` array in `config/app.php`:
    
    ```bash
    'providers' => [
        // ...
        Middleware\LaravelApm\LaravelApmServiceProvider::class,
    ],
    ```

6. Publish the package configuration:

    ```bash
    php artisan vendor:publish --provider="Middleware\LaravelAPM\LaravelAPMServiceProvider"
    ```
    This will create a config/opentelemetry.php file in your project.

## Configuration

1. Open `config/laravel-apm.php` and adjust the settings as needed:

    ```bash
    return [
        'endpoint' => env('APM_EXPORTER_OTLP_ENDPOINT', 'http://localhost:9320'),
        'service_name' => env('APM_SERVICE_NAME', 'laravel-app'),
        'content_type' => 'application/x-protobuf',
        'headers' => [
            'Content-Type' => 'application/x-protobuf'
        ],
    ];
    ```

2. Update your `.env` file with the appropriate values:

    ```bash
    APM_SERVICE_NAME=your-app-name
    ```
Make sure to set the appropriate values for your OpenTelemetry collector setup.

## Tracing

Laravel APM provides a middleware class to enable tracing. 
To register the tracing middleware, follow these steps:

1. Open `app/Http/Kernel.php` file.

2. Add the middleware to `$middleware` array:
    ```bash
    protected $middleware = [
        // ...
        \Middleware\LaravelApm\Middleware\TracingMiddleware::class,
    ];
    ```

## Logging

The package integrates with Laravel's logging system to capture and export logs. The logs will be sent to the configured OpenTelemetry collector.
To enable logging, make sure you have configured the appropriate log driver and settings in your Laravel application. The package will automatically capture and export the logs to the OpenTelemetry collector.

## Metrics

As of now, there's very little support for metrics, We'll be adding more metrics in future.
For enabling traces-related metrics, follow these steps:

1. Open `app/Http/Kernel.php` file.

2. Add the middleware to `$middleware` array:
    ```bash
    protected $middleware = [
        // ...
        \Middleware\LaravelApm\Middleware\MetricsMiddleware::class,
    ];
    ```

3. If you have enabled tracing, then add this middleware after tracing.