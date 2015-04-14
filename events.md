# Events

- [Basic Usage](#basic-usage)
- [Queued Event Handlers](#queued-event-handlers)
- [Event Subscribers](#event-subscribers)

<a name="basic-usage"></a>
## Basic Usage

The Lumen and Laravel event facilities provides a simple observer implementation, allowing you to subscribe and listen for events in your application.

#### Subscribing To An Event

> **Note:** If you intend to use the `Event` facade, be sure to uncomment the `$app->withFacades()` call in your `bootstrap/app.php` file.

To subscribe to an event, you may use the `Event::listen` method:

	Event::listen(
		'PodcastWasPurchased', 'EmailPurchaseConfirmation@handle'
	);

> **Note:** You may place these event registrations in a [service provider](/docs/providers).

When the event is fired, the event object will be passed to the `handle` method of the listener:

	class EmailPurchaseConfirmation {

		public function handle(PodcastWasPurchased $event)
		{
			//
		}

	}

Of course, you are free to place your event and listener classes wherever you want in your application, such as an `app/Events` directory.

#### Firing An Event

Now we are ready to fire our event using the `Event` facade:

	$response = Event::fire(new PodcastWasPurchased($podcast));

The `fire` method returns an array of responses that you can use to control what happens next in your application.

You may also use the `event` helper to fire an event:

	event(new PodcastWasPurchased($podcast));

#### Closure Listeners

You can even listen to events without creating a separate handler class at all. For example, in the `boot` method of your `EventServiceProvider`, you could do the following:

	Event::listen('App\Events\PodcastWasPurchased', function($event) {
		// Handle the event...
	});

#### Stopping The Propagation Of An Event

Sometimes, you may wish to stop the propagation of an event to other listeners. You may do so using by returning `false` from your handler:

	Event::listen('App\Events\PodcastWasPurchased', function($event) {
		// Handle the event...

		return false;
	});

<a name="queued-event-handlers"></a>
## Queued Event Handlers

If you would like your event listener to be [queued](/docs/queues), you may mark it with the `Illuminate\Contracts\Queue\ShouldBeQueued` interface:

	use Illuminate\Contracts\Queue\ShouldBeQueued;

	class SendPurchaseConfirmation implements ShouldBeQueued {

		public function (PurchasePodcast $event)
		{
			//
		}

	}

That's it! Now when this listener is called for an event, it will be queued automatically by the event dispatcher.

> **Note:** Of course, you will need to configure your [queue settings](/docs/queues) before using this feature.

If no exceptions are thrown when the handler is executed by the queue, the queued job will be deleted automatically after it has processed. If you need to access the queued job's `delete` and `release` methods manually, you may do so. The `Illuminate\Queue\InteractsWithQueue` trait, which is included by default on queued handlers, gives you access to these methods:

	public function handle(PodcastWasPurchased $event)
	{
		if (true) {
			$this->release(30);
		}
	}
