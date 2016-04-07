---
layout: page
---

![dog](/dog.jpg){: .right} We all work with web resources. What if we had a nice way to know when they change?

LiveResource is a **protocol specification** and **JavaScript reference library** for receiving live updates of web resources. It has the following principles:

* Blend naturally with RESTful API design.
* Optionally support WebSockets, but do so without abandoning established API principles.
* Keep it simple. It should be possible to `curl` for updates.

LiveResource differs from other realtime push solutions by providing a client-side interface modeled around synchronization rather than messaging or sockets. Here's an example using the JavaScript API:

{% highlight js %}
var resource = new LiveResource('http://example.com/path/to/object');
resource.on('value', function (data) {
    // the resource's value has been initially retrieved or changed
});
{% endhighlight %}

How it works
------------

The client makes a GET or HEAD request and discovers capabilities via `Link` headers in the response from the server:

{% highlight http %}
HTTP/1.1 200 OK
ETag: "b1946ac9"
Link: </path/to/object>; rel=value-wait
Link: </ws/>; rel=multiplex-socket
Content-Type: application/json
{% endhighlight %}

The `value-wait` link indicates that you can make a GET request that hangs open until the value changes (this is known as long-polling). The `multiplex-socket` link is for WebSocket connectivity. Versioning is handled using ETags.

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

Of course, LiveResource's WebSocket mechanism can be used for greater network efficiency.

If a resource doesn't advertise any push capabilities, then the client will resort to plain polling. This means that a LiveResource client is compatible with any resource on the Internet, and automatically becomes more efficient if push capabilities are discovered.

Full protocol [here]({{ site.protocol_url }}). Background [here](/about/).

Test server
-----------

There is a test server at `test.liveresource.org`. You can use it to play around with the protocol or test client code against. For example, there is a resource that reports the [current time][test-resource].

Q & A
-----

1. What about WebSockets?

   The LiveResource protocol supports WebSockets as one of its push mechanisms.

2. What about HTTP/2?

   LiveResource is intended to be efficiently usable with HTTP/2. In fact, we believe LiveResource's SSE mechanism in combination with HTTP/2 could eventually supplant the need for WebSockets.

3. Can I use LiveResource to send data to the server?

   Nope. But you weren't having this problem anyway, were you? Just use normal HTTP for that.

4. Is this like Meteor's DDP?

   LiveResource reuses HTTP rather than replacing it. The process always starts with a GET or HEAD request to an HTTP resource. An upgrade to WebSockets is used only when necessary.

5. Why use HTTP at all for this? Why not just do a specialized communication over WebSockets?

   We believe the tendency for many realtime frameworks to throw HTTP and resource-oriented API design out the window is unfortunate as well as completely overkill in the common case. Custom RPC-over-WebSocket systems have merit but their usage is not in any way a prerequisite for pushing data in realtime.

6. Does LiveResource require a certain database backend?

   No. LiveResource doesn't involve the database at all.

7. Does LiveResource require building the front end as a Single Page App?

   Of course not. Navigating pages is fine. That's how the web works. You won't lose data.

8. Does LiveResource require me to write all of my code in JavaScript?

   No! We aim to have libraries in many languages, both server and client.

9. Does LiveResource come with a package management system?

   Go home. You're drunk.

[realcrowd-post]: http://code.realcrowd.com/restful-realtime/
[resthooks]: http://resthooks.org/
[test-resource]: http://test.liveresource.org/test
