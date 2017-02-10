# Authorization

## Introduction

In addition to providing [authentication](/docs/{{version}}/authentication) services out of the box, Lumen also provides a simple way to organize authorization logic and control access to resources. There are a variety of methods and helpers to assist you in organizing your authorization logic.

In general, authorization can be used in Lumen the same way it is used in Laravel. We will cover a few differences here, but you should refer to the [full Laravel documentation](https://laravel.com/docs/authorization) for additional details.

## Differences From Laravel

#### Defining Abilities

The primary difference when using authorization in Lumen compared to Laravel is in regards to how abilities are defined. In Lumen, you may simply use the `Gate` facade in your `AuthServiceProvider` to define abilities:

    Gate::define('update-post', function ($user, $post) {
        return $user->id === $post->user_id;
    });

#### Defining Policies

Unlike Laravel, Lumen does not have a `$policies` array on its `AuthServiceProvider`. However, you may still call the `policy` method on the `Gate` facade from within the provider's `boot` method:

    Gate::policy(Post::class, PostPolicy::class);

Again, to learn more about policies, you should consult the [full Laravel documentation](https://laravel.com/docs/authorization).

#### Checking Abilities

You may "check" abilities just as you would in the full Laravel framework. First, you may use the `Gate` facade. If you choose to use the facade, be sure to enable facades in your `bootstrap/app.php` file. Remember, we don't need to pass the `User` instance into the `allows` method since the currently authenticated user will automatically be passed to your authorization callback:

    if (Gate::allows('update-post', $post)) {
        //
    }

    if (Gate::denies('update-post', $post)) {
        abort(403);
    }

Of course, you may also check if a given `User` instance has a given ability:

    if ($request->user()->can('update-post', $post)) {
        // user is allowed to update the post
    }

    if ($request->user()->cannot('update-post', $post)) {
        abort(403);
    }
