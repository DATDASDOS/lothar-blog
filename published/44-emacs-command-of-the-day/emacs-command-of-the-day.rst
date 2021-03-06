:title: emacs command of the day
:date: 2011-04-12 12:09
:category: emacs
:slug: 44-emacs-command-of-the-day

C-x 4 c : clone-indirect-buffer-other-window
!END-SUMMARY!

I keep learning new tricks in emacs. Today I was studying an overstuffed
file, with two large classes, and I needed to navigate around both as I
followed the code paths bouncing back and forth between them. I frequently
use the "narrow-to-region" command (C-x n n) to temporarily clip the buffer
to a single class or method of interest, because then jumping to the
beginning/end of the buffer really takes me quickly to the beginning/end of
the class, and searching is limited to the class, etc. But this time, I
needed to narrow the buffer to two separate regions.

Enter today's interesting command: "clone-indirect-buffer-other-window",
reached by C-x 4 c . All the "C-x 4" commands put things in a new window
(which means a new region of the current "frame", where each frame gets a new
OS-level window). This one makes an "indirect buffer" that's a mirror of the
current one, but with a slightly different name (it gets a <2> tacked on to
the end). Both buffers are looking at the same file, so any changes you make
in one will also appear in the other. But you can narrow each one separately.
So I was able to narrow to the first class in the main buffer, and narrow to
the second class in the second buffer, and then search/study/explore as if
they were two entirely different files. When you're done, just close the
second buffer.
