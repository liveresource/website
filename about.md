---
layout: page
title: About
permalink: /about/
---
LiveResource is produced by [Fanout, Inc][fanout].

At Fanout, we believe in the power of APIs, and we advocate building APIs that are capable of pushing data in realtime. Over the past few years, what we've noticed is that realtime APIs are often difficult to consume on the client-side due to the lack of client libraries. Many API providers do not produce client libraries at all, and the API community tends to be skeptical about generic/generated clients.

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
