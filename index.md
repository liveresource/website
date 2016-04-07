---
layout: page
---

![dog](/dog.jpg){: .right} We all work with web resources. What if we had a nice way to know when they change?

LiveResource is a **protocol specification** and **JavaScript reference library** for receiving live updates of web resources. It has the following principles:

* WebSockets are great, but reuse HTTP where possible.
* Keep it simple. It should be possible to `curl` for updates.
* The protocol should blend naturally with RESTful API design.

LiveResource differs from other "realtime push" solutions by providing a client-side interface modeled around synchronization rather than messaging or sockets. Here's an example using the JavaScript API:

{% highlight js %}
var resource = new LiveResource('http://example.com/path/to/object');
resource.on('value', function (data) {
    // the resource's value has been initially retrieved or changed
});
{% endhighlight %}

How it works
------------

The client makes a GET or HEAD request and discovers capabilities via `Link` headers.

{% highlight http %}
HTTP/1.1 200 OK
ETag: "b1946ac9"
Link: </path/to/object>; rel=value-wait
Link: </updates/>; rel=multiplex-ws
Content-Type: application/json
{% endhighlight %}

The `value-wait` link indicates that you can make a GET request that hangs open until the value changes (this is known as long-polling). The `multiplex-ws` link is for WebSocket connectivity. Versioning is handled using ETags.

What's nice about LiveResource's long-polling mechanism is that it is simple and stateless. It's just a GET request with `If-None-Match` and `Wait` headers:

{% highlight http %}
GET /path/to/object HTTP/1.1
Host: example.com
If-None-Match: "b1946ac9"
Wait: 60
{% endhighlight %}

There is no complicated socket session emulation. This means you can even use `curl`:

{% highlight sh %}
curl -i -H 'If-None-Match: "b1946ac9"' -H 'Wait: 60' \
    http://example.com/path/to/object
{% endhighlight %}

Of course, WebSockets are preferred for maximum network efficiency.

Full protocol details [here](https://github.com/fanout/liveresource/blob/master/protocol.md).

The protocol was inspired by [RealCrowd's API](http://code.realcrowd.com/restful-realtime/) and [Zapier's REST Hooks](http://resthooks.org/).

Code
----

Check out the [code repository](https://github.com/fanout/liveresource).

Q & A
-----

1. What about WebSockets?

   The LiveResource protocol supports WebSockets as one of its push mechanisms.

2. What about HTTP/2?

   LiveResource's HTTP-based push mechanism is intended to be reusable within HTTP/2.

3. Can I use LiveResource to send data to the server?

   Nope. But you weren't having this problem anyway, were you? Use XHR for that.

4. Is this like Meteor's DDP?

   LiveResource reuses HTTP rather than replacing it. The process always starts with a GET or HEAD request to an HTTP resource and introduces WebSockets only when necessary.

5. Does LiveResource require a certain database backend?

   No. LiveResource doesn't involve the database at all.

6. Does LiveResource require building the front end as a Single Page App?

   Of course not. Navigating pages is fine. That's how the web works. You won't lose data.

7. Does LiveResource come with a package management system?

   Go home. You're drunk.
