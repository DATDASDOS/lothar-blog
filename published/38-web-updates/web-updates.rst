:title: web updates
:date: 2008-05-29 18:59
:category: web
:slug: 38-web-updates

I finally updated the system that hosts http://buildbot.net and
http://foolscap.lothar.com (a dedicated VM that just runs apache for CGIs,
needed to make trac and mod_python work well). Upgrading it from edgy to
anything newer was a hassle, because the "update-manager" package that I
wanted to use wasn't installed, and because edgy is now too old to appear on
most Ubuntu mirrors. It does appear on http://old-releases.ubuntu.com ,
though, but unfortunately the update-manager package doesn't work unless both
the "from" and the "to" releases are available on the same APT repository.
Since feisty isn't old enough for old-releases yet, there's nothing you can
put in your /etc/apt/sources.list that will appease update-manager.

So I had to do it the old-fashioned way: change sources.list, apt-get update,
apt-get dist-upgrade . That worked, but then trac broke: the default version
of python switched from 2.4 to 2.5, and none of the trac plugins I was using
had eggs that were built for 2.5 . I decided to upgrade all the way to hardy
before trying to fix anything else.

After fixing the eggs, it turned out that python-clearsilver in hardy is just
broken: it doesn't include a 2.5 version, and I guess it was trying to make
do with a 2.4 version, because I was getting errors about missing symbols. I
finally found https://bugs.launchpad.net/ubuntu/+source/trac/+bug/114930 and
followed the advice to rebuild the python-clearsilver package with the right
version of python.

I also had to upgrade the trac databases in the process, but that's an easy
"trac-admin TRACDIR upgrade".

And now everything is working again, with only an hour of unexpected
downtime.
