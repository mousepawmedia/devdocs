"Hawksnest" IRC Bot
###############################

The :code:`#mousepawgames` and :code:`##mousepawgames` Freenode IRC chatrooms
are patrolled by our bot, **LukeDMouse**.

..  NOTE:: You may remember the prior bot, Hawksnest. That bot was run by the
    Phabricator chatbot software, which has been discontinued due to a
    security flaw.

Commands
========================================

* :code:`!coin`: Flips a coin.

* :code:`!dice <n>d<sides>`: Rolls ``<n>`` dice with ``<sides>``. For example,
  `!dice 2d10` rolls two ten-sided dice.

* :code:`!news`: Read the lastest channel news.

* :code:`!seen <nick>`: Lists the last time the user ``<nick>`` was seen in the
  room by the bot, and what they last said.

* :code:`!tell <nick> <text>`: Tells the user ``<nick>`` whatever message you
  specify when they next log in.

Calculator
-----------------------------------------

* :code:`!base <fromBase> [<toBase>] <number>` converts ``<number>`` from
  the base ``<fromBase>`` to decimal or to the optional ``<toBase>``.

* :code:`!calc <expression>` calculates the given math expression (in Python
  math syntax).

* :code:`!convert <number> <fromUnit> <toUnit>` converts the number from one
  unit to another. See `!units` for a list of unit types, and `!units <type>`
  for a list of valid units.

Factoids
=========================================

We have multiple factoids - automatic responses - built in. Just for fun, many
are unlisted. Here are the important ones:

* :code:`!language`: List warning about offensive language.

* :code:`!paste`: List info about pasting.

* :code:`!standards`: List info about standards.

Learning
=========================================

**Voiced users** can train the bot to say other things with the :code:``learn``
command. For example::

    !learn 42 => The answer to life, the universe, and everything.

Running that command will create the factoid ``!42``, which responds with
"The answer to life, the universe, and everything."

Running::

    !forget 42

...will similarly cause the bot to forget about that command.

Logging
========================================

The bot also logs the rooms. We are working on a way to make these logs
publicly accessible.
