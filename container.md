# Service Container

## Introduction

The Laravel service container is a powerful tool for managing class dependencies and performing dependency injection. Dependency injection is a fancy phrase that essentially means this: class dependencies are "injected" into the class via the constructor or, in some cases, "setter" methods.

## Differences From Laravel

Lumen uses the exact same service container as the Laravel framework, so you have access to all of its powerful features. For complete documentation on the container, check out the [full Laravel container documentation](https://laravel.com/docs/container).

#### Accessing The Container

The `Laravel\Lumen\Application` instance is an extension of `Illuminate\Container\Container`, so it may be treated as the service container for your application.

Typically, you will register bindings into the container within your [service providers](/docs/{{version}}/providers). Of course, you may use the `bind`, `singleton`, `instance`, and other container methods provided by the container. **Remember, all of these methods are documented in the [full Laravel container documentation](https://laravel.com/docs/container).**

#### Resolving Instances

To resolve things out of the container, you may either type-hint the dependency you need on a class that is already automatically resolved by the container, such as a route Closure, controller constructor, controller method, middleware, event listener, or queued job. Or, you may use the `app` function from anywhere in your application:

    $instance = app(Something::class);
