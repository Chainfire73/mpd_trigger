mpd_trigger
===========

:Author: Ted Yin (ted.sybil@gmail.com)

Execute whatever you want when MPD (Music Player Daemon) changes its state

What Is This?
-------------
I'd like to let a notification pop up whenever MPD is playing or paused.
However, disappointing enough, I failed to find any existing programs/scripts
to achieve this little but convenient functionality. Although there is
mpd-hiss_, it has quite a number of dependencies and is written in Python with
several separated files. I think such a simply little desire should not be as
complicated as this. Even though I love programming in Python, that project may
still need to polished to be flexible and easy to deploy (but it is a good
project nevertheless). Being different from it, this project tends to be
lightweight and simple but as extensible as it can, but in the end, should be
simple. After all, a simple task deserves a simple solution. Now the code is
written in C in a single file and highly portable. It uses "patterns" to
generate a command once MPD state is being changed and pipes it into a
pre-selected shell. During the execution, it consumes few resources and imposes
little overhead.

How to Use?
-----------
First, compile it by invoking ``make``. The only executable file is
``mpd_trigger``. You could run ``./mpd_trigger -h`` to read the help
information. A typical example is as follow (executed in bash):

::

    ./mpd_trigger 192.168.248.130 -p 6600 -e "echo 'Hey, {title} is {state}!'"

Then play a song. Besides diagnostic outputs, you should find the output of
executing that ``echo`` command.

You may have noticed something like ``{title}`` which is actually a pattern
representing the title of the song is to be filed in that place.
``mpd_trigger`` currently supports two kinds of patterns:

- Information patterns: ``{title}``, ``{artist}``, ``{album}``, ``{track}``,
  ``{state}``, ``{elapsed_time}``, ``total_time``, ``{elapsed_pct}``
- Conditional pattern: ``{str_to_check?stringa:stringb}`` which checks whether
  ``str_to_check`` is an empty string, if it is ``stringa`` is chosen,
  otherwise ``stringb``, note that the surrounding braces are removed.


For Mac OS X users, after installing a tool called ``terminal-notifier``, try
the following command:

::

    ./mpd_trigger -e 'terminal-notifier -title "{title}: {state} ({elapsed_pct}%)" -subtitle "{artist}" -message "{album} @ {track?{track}:unknown track}" -sender com.apple.iTunes'

.. image:: https://raw.githubusercontent.com/Determinant/mpd_trigger/master/screenshot.png

If you have no idea about how to run this program as a user daemon, try to copy
the accompanied file ``com.ted.mpd_trigger.plist`` (*you should modify the path
to the correct location of the compiled mpd_trigger binary* and may adjust
parameters) to ``path_to_your_home/Library/LaunchAgents`` and then execute the
following commands:

::

    cd path_to_your_home/Library/LaunchAgents
    launchctl load com.ted.mpd_trigger.plist

Finally, it is worth mentioning that patterns can be nested, for example a part
of the pattern in last example ``{track?{track}:unkown track}`` makes use of
such technique. Have fun! :)

.. _mpd-hiss: https://github.com/ahihi/mpd-hiss

Dependencies
------------

- The canonical mpd library: ``libmpdclient`` (on which famous mpd clients such as
  mpc and ncmpcppp also depend)
- Some basic POSIX syscalls (ubiquitous on current unix-like systems)
- For Mac OS X users, they may want to install ``terminal-notifier`` if they'd
  like to, but this is not mandatory since the triggered command is up to you.
- Nothing else.
