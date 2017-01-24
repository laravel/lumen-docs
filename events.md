# Events

<a name="introduction"></a>
## Introduction

Lumen's events provides a simple observer implementation, allowing you to subscribe and listen for events in your application. Event classes are typically stored in the `app/Events` directory, while their listeners are stored in `app/Listeners`.

## Differences From Laravel

In general, events in Lumen function exactly like they do in the full-stack Laravel framework, so please review the [full Laravel documentation](https://laravel.com/docs/events). Event broadcasting is even supported in Lumen, which allows you to listen for your server side events in your client-side JavaScript. However, there are a few minor differences which warrant discussion.

#### Generators

In Lumen, there are no generator commands to generate events and listeners for you, so you should simply copy the `ExampleEvent` or `ExampleListener` classes to define your own events and listeners. These example classes provide the basic structure of every event and listener.

#### Registering Events / Listeners

Like the full Laravel framework, the `EventServiceProvider` included with your Lumen application provides a convenient place to register all event listeners. The `listen` property contains an array of all events (keys) and their listeners (values). Of course, you may add as many events to this array as your application requires:

    /**
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [
        'App\Events\ExampleEvent' => [
            'App\Listeners\ExampleListener',
        ],
    ];

#### Firing Events

You may use the `event` helper function or `Event` facade to fire events throughout your Lumen application. Again, these functions behave exactly like their full Laravel framework equivalent:

    event(new ExampleEvent);

    Event::dispatch(new ExampleEvent);
