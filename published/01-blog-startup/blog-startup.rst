:title: blog startup
:date: 2005-04-18 01:47
:category: weblog
:slug: 1-blog-startup

I've been trying to get my project notes online for years now, and I finally
realized that I need to start smaller. After a week of intermittent activity,
I finally got `PyBlosxom <http://pyblosxom.sourceforge.net>`__ set up and
behaving fairly well.

In the process, I discovered that the CGI specification doesn't actually
require the web server to close the child process' stdin: the child is
supposed to read exactly CONTENT_LENGTH bytes and then stop. Pyblosxom
violates this (it just reads until EOF), but it usually doesn't matter
because most web servers are nice enough to close it. It turns out that
Twisted's CGI handling module does not. I fixed it upstream (at least in the
old 'web' module.. web2 is another matter), and filed a pyblosxom bug. For
now I have to monkeypatch Twisted in my web setup program, at least until
Twisted-2.0.1 comes out.

My goal for this web log is twofold. The first is to contain an archive of
useful things I come across, cool stuff, new ideas, things I've learned, the
usual blog fodder. The second is to publish the diaries that I keep on each
of my projects, diaries that I use to help remember what I was thinking when
I last put energy into that project, in the hopes of reducing the
context-switching penalty that comes about from having a dozen active
projects at once. I have something like 1.5MB of diary entries on these
projects, going back to mid-2002 when I started keeping them. By getting
these projectlogs online (and enabling comments on them), I hope to give the
rest of the world a chance to look at my workbench and tell me what they find
interesting. Maybe someone else will pick up on an idea that I haven't had
time to pursue, maybe they will leave a note with useful directions to go or
tools to use. Perhaps by letting others in, and hopefully forming a bit of a
community here, I'll be more encouraged to work on the projects that have a
chance of forming communities of their own.

I plan to use pyblosxom's categories to put all the projects in places like
Projects/BuildBot . Non-project related things like site-setup notes or
personal rants can go in other categories next to Projects.

The blog is pretty basic for now, and not necessarily pleasant to look at,
but it's a start, and I've learned that waiting for perfection is the best
way to never get anything finished at all.

Welcome!

 -Brian
