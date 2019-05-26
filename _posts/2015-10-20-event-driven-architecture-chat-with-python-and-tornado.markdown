---
title: 'Event-driven architecture: Chat with Python and Tornado'
date: 2015-10-20 16:28:12 +0000
tags: tornado python chat event-driven
---
At [Selltag][selltag_project] we built a simple chat service based on events, keeping It as simple as possible.

The message flow is simple. A message arrives, we emit an event, different listeners launch different tasks on background depending on the type of the message.

The service is an HTTP Rest API listening on the private network, except the websocket, It has to be listening through SSL to the Internet for the web clients to be able to connect. In the example I don't distinguish between these two cases, just an application listening to `0.0.0.0:8888`.

We have different kind of events, all defined in the `Event` class, `NEW_MESSAGE`, `NEW_DEVICE`, `USER_GONE`, `MESSAGE_READ`, etc. Those events are emitted in the [Tornado][tornado] handlers and are connected to different listeners with a concrete purpose. If the action requires calling an external service (Android Push, iOS push, analytics, email), the listener will send a background task to execute the action. We were using `Celery` for that.

[I made a repository with an example of how could that be][repo], It doesn't have everything developed but you could get an idea of how would It work and how to extend It to your needs.

It's not rocket science, It's a good approach when you want to perform *completely isolated* tasks, talk to third-party services, or separate logic. It's important to notice this solution is not always a good idea because the flow of the application can be very easily messed up and difficult to maintain or follow.

## Get to the code

[You can clone the repository][repo].

We have an application file `app.py`, the *entrance* of our chat. The `apps/chat/router` defines our urls and the handlers of each of those urls.

We send a message through a websocket using `websocket.send` from the WebSocket JS client, our `WebSocketHandler` inside the handlers of the chat app will get It through the `on_message` method, we prepare the message the way we want (ideally It would be a Message model and you could add the attributes you want to be sent there), and we emit an event using the [blinker][blinker] package.

<pre><code class="language-python">from blinker import signal

signal(Event.NEW_MESSAGE).send(data)
</code></pre>

We have initialized the active listeners in the app.

<pre><code class="language-python">def __init__(self, router):
    # ...
    self.listeners = [AndroidListener, IosListener, PersistListener]

def start_listeners(self):
    for listener in self.listeners:
        self.started_listeners.append(listener(self.options))

# ...
</code></pre>

In those listeners, we can connect with the `Event.NEW_MESSAGE` event to get the message and do something with It. For example in the Android listener(`apps/chat/listeners.py`) we'd have a connection like:

<pre><code class="language-python">from blinker import signal

class AndroidListener():
    def __init__(self, options):
        # ...
        signal(Event.NEW_MESSAGE).connect(self.send)

    def send(self, message):
        # Send push notification
        logging.info('SENDING ANDROID PUSH')
</code></pre>

That's basically how could we use events to perform different separate actions, in the example we have also an `Event.NEW_DEVICE`. At Selltag we were using that event when a `X-DEVICE-ID` HTTP header was present in the request.

If the header was present, we emitted the event and persisted the device id for this user to be able to send him/her push notifications using Android/iOS.

## Install It

The easiest way of checking out the application is using **Vagrant** and **Ansible**, make sure you have them installed.

<pre><code class="language-bash">cd ansible
ansible-galaxy install -r requirements.yml
cd ..
vagrant up
</code></pre>

Once It's finished you can go to `localhost:8888` in your browser and send yourself a message. ;-)

## Bonus track

It's important to notice I am using Redis as a backend, but if you'd want to use a different one, `MongoDB` for example, you could implement the example abstract classes from `base_backend.py` as I did. We should have added more abstract classes and methods to have a platform agnostic skeleton of the application, you can do It if you feel the urge. :-)

## Next

In a following article I will write about how we improved our microservices architecture using rpc and [Nameko][nameko].

[selltag_project]: http://javaguirre.net/2015/01/01/selltag-2/
[nameko]: https://github.com/onefinestay/nameko
[repo]: https://github.com/javaguirre/event-driven-chat-tornado-example
[blinker]: https://pypi.python.org/pypi/blinker
[tornado]: http://www.tornadoweb.org/en/stable/
