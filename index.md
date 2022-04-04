---
layout: page
---

![dog](/dog.jpg){: .right} We all work with web resources. What if we had a nice way to know when they change?

LiveResource is a **protocol specification** and **JavaScript reference library** for receiving live updates of web resources. It has the following principles:

* Blend naturally with RESTful API design.
* Optionally support WebSockets, but do so without abandoning established API principles.
* Keep it simple. It should be possible to `curl` for updates.

LiveResource differs from other realtime push solutions by providing a client-side interface modeled around synchronization rather than messaging or sockets. Here's an example using the JavaScript client library:

{% highlight js %}
var resource = new LiveResource('http://example.com/path/to/object');
resource.on('value', function (data) {
    // the resource's value has been initially retrieved or changed
});
{% endhighlight %}

How it works
------------

The client makes a GET or HEAD request to discover the capabilities of a resource:

{% highlight http %}
HTTP/1.1 200 OK
ETag: "b1946ac9"
Link: </path/to/object>; rel=value-wait
Link: </ws/>; rel=multiplex-socket
Content-Type: application/json
{% endhighlight %}

For example, the `value-wait` link indicates that it's possible to make a GET request that hangs open until the value changes (this is known as long-polling). The `multiplex-socket` link is for WebSocket connectivity. Versioning is handled using ETags.

What's nice about LiveResource's long-polling mechanism is that it is simple and stateless. It's just a GET request with `If-None-Match` and [Prefer][prefer-rfc] headers:

{% highlight http %}
GET /path/to/object HTTP/1.1
Host: example.com
If-None-Match: "b1946ac9"
Prefer: wait=60
{% endhighlight %}

There is no complicated socket session emulation. This means you can even use `curl`:

{% highlight sh %}
curl -i -H 'If-None-Match: "b1946ac9"' -H 'Prefer: wait=60' \
    http://example.com/path/to/object
{% endhighlight %}

Of course, LiveResource's WebSocket mechanism can be used for greater network efficiency.

If a resource doesn't advertise any push capabilities, then the client will resort to plain polling. This means that a LiveResource client is compatible with any resource on the Internet, and automatically becomes more efficient if push capabilities are discovered.

Protocol
--------

See the full spec [here]({{ site.protocol_url }}).

Test server
-----------

There is a test server at `test.liveresource.org`. You can use it to play around with the protocol or test client code against. For example, there is [a resource][test-resource] that reports the current time.

Q & A
-----

1. What about WebSockets?

   The LiveResource protocol supports WebSockets as one of its push mechanisms.

2. What about Webhooks?

   The LiveResource protocol supports Webhooks as one of its push mechanisms, following the recommendations outlined by [REST Hooks][resthooks].

3. What about HTTP/2?

   LiveResource is intended to be efficiently usable with HTTP/2. In fact, we believe LiveResource's SSE mechanism in combination with HTTP/2 could eventually supplant the need for WebSockets.

4. Can I use LiveResource to send data to the server?

   Nope. But you weren't having this problem, were you? Just use normal HTTP for that.

5. Does LiveResource require a certain database backend?

   No. LiveResource doesn't involve the database at all.

6. Does LiveResource require building the front end as a Single Page App?

   Of course not.

7. Does LiveResource require me to write all of my code in JavaScript?

   No! We aim to have libraries in many languages, both server and client.

8. Does LiveResource come with a package management system?

   Go home. You're drunk.

[prefer-rfc]: https://datatracker.ietf.org/doc/html/rfc7240
[resthooks]: http://resthooks.org/
[test-resource]: http://test.liveresource.org/test
