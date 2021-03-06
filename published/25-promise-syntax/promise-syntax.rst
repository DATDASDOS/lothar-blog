:title: promise syntax
:date: 2006-09-25 23:32
:category: twisted
:slug: 25-promise-syntax

Zooko's in town, and already I feel 20% smarter. I roped him into a
discussion about the Promise syntax I'm developing for Foolscap, and he
suggested an alternative that has some good properties.

I'll illustrate with an example where promise-pipelining actually does you
some good. (many of the use cases I've been thinking of involve some sort of
publish/subscribe scheme, and in those cases you win almost nothing with
pipelining). I'm imagining a theoretical Buildbot status interface using
newpb, and a tools that wants to connect to the buildmaster and retrieve the
results of the latest build for a given Builder. The oldpb code would look
like this::

    # Example 1
    def checkResults(results):
        if results == SUCCESS:
            print "yay!"
    def oops(failure):
        print "boo"
    #
    s = getStatus()
    d = s.callRemote("getBuilder", "python-2.4-full")
    d.addCallback(lambda builder: builder.callRemote("getBuild", -1))
    d.addCallback(lambda build: build.callRemote("getResults"))
    d.addCallback(checkResults)
    d.addErrback(oops)

The syntax I've currently got in Foolscap would make it look like this::

    # Example 2
    s = getStatus()
    b = send(s).getBuilder("python-2.4-full")
    b1 = send(b).getBuild(-1)
    r = send(b1).getResults()
    when(r).addCallback(checkResults).addErrback(oops)

The big win with the promise pipelining is that all 3 calls (4 if you include
getStatus) take place in one round trip, whereas the oldpb approach requires
3 or 4 separate roundtrips. As MarkM has said, the pipes are getting wider
but not shorter, and eventually the round-trip latency will be the biggest
bottleneck.

The syntax that Zooko suggested would make this all look much more like the
(blocking) synchronous form::

    # Example 3
    s = getStatus()
    b = s.getBuilder("python-2.4-full")
    b1 = b.getBuild(-1)
    r = b1.getResults()
    r._then(checkResults)._except(oops)

Or you could chain it all into a single column, which my editor wouldn't like
(you'd have to add some outer parenthesis to keep it indenting happily) but
which python will still accept::

    # Example 4
    getStatus().getBuilder("python-2.4-full")
      .getBuild(-1)
      .getResults()
      ._then(checkResults)
      ._except(oops)

which is a lot easier to read than the same collapsed form with my send()
syntax::

    # Example 5
    when(send(send(send(getStatus()).getBuilder("python-2.4-full")).getBuild(-1)).getResults()).addCallback(checkResults).addErrback(oops)

Now, a syntax which looks synchronous is great for programmers who aren't
familiar with asynchronous control flows: they can look at example 3 or 4
and, except for the funny _then clause, it all looks exactly like what they
expect from xmlrpclib or other blocking RPC mechanisms. The problem with this
syntax is that they might forget that they're actually dealing with Promises,
and try to do something like::

    results = b.getResults()
    if results == SUCCESS:
        print "yay!"

and forget that 'results' is actually a Promise, and the only things you can
do with a promise is to send messages to it, or invoke _then or _except. In
some cases this could just raise an exception::

    counter = b.getCounter()
    print counter + 1
    # TypeError: unsupported operand types(s) for +: 'instance' and int

And in other cases (like 'results is SUCCESS') it might fail silently, always
returning False. Whereas the send() syntax would make it obvious that you're
dealing with a Promise.

One thing I like about Zooko's approach is that I can have the _then and
_except methods be simplified wrappers for the more general purpose _when or
_when_resolved method, the one that returns a Deferred::

    results = b.getResults()
    d = results._when()
    d.addCallback(checkResults)

That way *I* can use Deferreds for my control flow, while the newcomers for
whom Deferreds still seem magical can use a somewhat-familiar _then(callback)
approach. (without this, we'd be walking backwards in time to the beginning
of the evolutionary path that has resulted in Deferreds as a general-purpose
callback management tool).

In addition, these two syntaxes aren't necessarily mutually exclusive. I
could have one kind of Promise that implements the __getattr__ magic
necessary to make Zooko's syntax work, but if you call send() on one, it sets
a flag to disable that magic, so that you end up using the send/when syntax.

There was more to the discussion but it's all in a notebook in the other room
and I'm too sleepy to express it all right now.
