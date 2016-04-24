---
layout: page
title: About
permalink: /about/
---
This project was created by [Fanout][fanout]. We believe in the power of APIs, and we advocate building APIs that are capable of pushing data in realtime.

Over the past few years, we've observed that realtime APIs are often difficult to consume due to the lack of client libraries. In fact, many API providers do not produce client libraries at all. This may be fine for conventional REST APIs, but it often presents a usability problem with APIs that push data.

We think the consumption of realtime APIs ought to be made easier, on par with frameworks like Firebase or Meteor (or at least as close as we can get). In order to achieve this, we need to define baseline standards that make it possible to create common client libraries.

The protocol was inspired by [RealCrowd's API][realcrowd-post] and [Zapier's REST Hooks][resthooks].

Credits:

* Protocol spec: Justin Karneges <justin@fanout.io>
* JavaScript client: Katsuyuki Ohmuro <harmony7@pex2.jp>

Special thanks:

* JD Conley (design)
* Andy Norborg (design)

Think the protocol should work differently? Feel free to post on the [issue tracker][issues].

[fanout]: https://fanout.io/
[realcrowd-post]: http://code.realcrowd.com/restful-realtime/
[resthooks]: http://resthooks.org/
[issues]: https://github.com/liveresource/protocol/issues
