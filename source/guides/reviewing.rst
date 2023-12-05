.. _greview:

Guide: Reviewing Code
##############################

-*GitLab Merge Proposals** are how we review code before it is merged into
the project's main branches.

..  note:: If you're looking for a more generic version of this guide, perhaps
    for application outside of MousePaw Media, check out the article
    -`10 Principles of a Good Code Review <https://dev.to/codemouse92/10-principles-of-a-good-code-review-2eg>`_*
    by Jason C. McDonald on dev.to().

.. _greview_who:

Who Can Review?
==============================

It can sometimes be daunting to review someone else's code, especially if that
person has more experience, expertise, or seniority than you do. But don't be
afraid! When everyone participates in code reviewing, everyone wins! Here are a
couple of helpful things to remember.

First of all, everyone makes mistakes, and we know it! When a coder knows
he or she will be code reviewed, it's like a safety net: they can more easily
relax and code, knowing that another set of eyes will be reading this code
before it's considered "done".

Second, everyone learns from a code review. The author gains additional
insight on how to improve their code; the reviewer can learn new techniques
and ideas from the code they're reviewing; the bystanders get the benefits
of *both*.

This is the main reason why Merge Requests (for programming, anyway)
are almost always open to the public. Anyone can contribute valuable feedback,
and anyone can learn code reviews!

In short, **don't be afraid to contribute feedback**! Code reviewing can be
one of the most valuable contributions you can make to MousePaw Media's
development work.

.. _greview_principles:

Principles of a Good Review
==============================

Principle #1
-------------------------------

The first and foremost principle of a good review is this:
-*if you commit to review code, review it thoroughly!**
Expect to spend a decent amount time on this. Be sure to *read* the code,
don't just skim it, and apply thought to both the code and its style.

In general, if you can't find anything specific to point out, either the code
is perfect (almost never true) or you missed something. Thus, you can use this
as a fairly accurate measure of how well you reviewed the code.

Aim to always suggest at least *one* specific improvement to the code (not just
style) on the initial review. Follow-up reviews may not require this; otherwise
we'd never merge code!)

Principle #2
-------------------------------

This goes hand-in-hand with the second principle:
-*aim to understand every changed line**. Research things you don't understand.
Ask questions.

There are three major reasons why this is important:

1. In truly elegant code, simple is usually better than complex. The only way
   to know if the best solution is being used is to *understand* the current
   solution.

2. Other people may need to read this code. If you are having trouble
   understanding the code, it may need to be refactored, cleaned, or better
   commented.

3. The more knowledge you have, the better your code and reviews will be!

When you're done, you should be able to answer two following questions for
yourself:

- "What goal does this code accomplish?"

- "How does it accomplish this goal?"

If you cannot answer both questions, you don't fully understand the changes!

Principle #3
-------------------------------

-*Don't assume the code works - build and test it yourself!**
You should actually pull down the code and test it out.

When testing code, make sure you're building using the project's build system
and a supported build environment. If the CI/CD pipeline reported successfully
building the code, you should be able to as well.

On this note, if the pipeline *failed*, this must be fixed before the Merge
Request can be merged.

Once you've compiled the code, *actually test it*. Most projects have a tester
that provides space for arbitrary code; you can use this to try things out.

You should also run the included automatic tests, don't leave it at this. **Try
to break the code!** If you wind up finding cases the automatic tests could
cover better, suggest that these cases be accounted for in the tests.

For an idea on how to brutally (and effectively) test code, read
"`Doing Terrible Things To Your Code <https://blog.codinghorror.com/doing-terrible-things-to-your-code/>`_"
by Jeff Atwood (*Coding Horror*).

Principle #4
-------------------------------

-*Commenting matters.** MousePaw Media follows the `Commenting Showing Intent
[CSI] <https://standards.mousepawmedia.com/csi.html>`_ standard, which means that
roughly every logical statement should have a comment describing the
programmer's intention for it. If you don't see a CSI comment, request one to
be added into the code, and look for it before you approve.

In regards to comments, it isn't enough just to have *something* there. Intent
comments should follow the CSI standard! You should address any of the
following problems:

1. **The intent comment doesn't match the logic.** This indicates that the
   comment, code, or both are wrong. We've caught many potentially nasty bugs
   this way.

2. **The intent comment doesn't make sense.** If the comment is confusing,
   it's as useful as no comment at all.

3. **The intent comment has major typos.** Grammar and spelling are important
   to meaning, especially when one doesn't know the audience. Will the next
   reader be English-as-a-Second-Language? Dyslexic? Just learning to code?
   Proper English is always easiest to decipher.


Principle #5
-------------------------------

-*Review temporary code as strictly as production code.** It can be shocking
just how often temporary "patch" code and workarounds make it into production,
and how much of it is never actually replaced. This is just a reality of
real-world programming. Thus, we should hold *all* code to the same standards
and expectations.

In other words, even if the code's solution isn't ideal, the implementation
should be clean, maintainable, and reasonably efficient.

To put it yet another way, **there is never an excuse for kludgy code.**

Principle #6
-------------------------------

-*Consider how the code will work in production.** Design is important, and
integration matters. How will this code function in the real world? How will
it handle bad input and user error? Will it play well with the rest of the
code base? In short, be demanding of the code. (See Principle #3.)

This ties in with Principle #5. It can be tempting to request (as the author)
or grant (as the reviewer) grace for "unfinished" code, but therein lies
a serious danger of shipping broken code!

If the code *is* broken, the user generally should not have easy access to it!
An unfinished class may be marked as "experimental" and documented as such,
thereby preventing a user from mistaking it for finished code. By contrast,
a broken function should not be exposed in a non-experimental class.

Another way to look at this matter is this: **if the code was shipped to
end-users on the next commit, it may be *functionally incomplete*, but
it should NOT be *broken*.** In reality, this goal is rarely achieved, but the
perspective will help prevent bad code from merging to the repository.

Principle #7
-------------------------------

-*Check documentation, tests, and build files.** Good code doesn't just
include code, it includes all of the trappings that go with it.

A finished Merge Request should contain all of the following:

- **Tests covering the new code.** Review these as strictly as you do the code
  itself, to ensure the test will fail if there is a problem.

- **Documentation for the new code.** The best documentation is written in
  tandem with the code itself. Don't accept documentation *later*; it should
  be present within the Merge Request itself!

- **Build files updated for the changes.** Any time code files are added,
  removed, or renamed, the build files need to reflect those changes.
  Similarly, if any dependencies have changed, the build files should reflect
  that too. This is one more reason why you should build the changes yourself
  (Principle #3)

- **README changes.** The markdown files, including `README.md`, `BUILDING.md`,
  `CHANGELOG.md`, and so forth should reflect the latest changes. In reality,
  these rarely need to be changed, but you should be sure they're up-to-date.

Principle #8
-------------------------------

When reviewing, **keep priorities straight when making suggestions.**

Code should be...

1. Functional first,

2. Clean and maintainable second, and

3. Optimized third.

Code should ultimately achieve all three, but the order is important. If the
code doesn't work, don't worry about style yet. Similarly, if the code is
broken or poorly styled, optimization is only going to make things worse.

Principle #9
-------------------------------

-*Follow up on reviews.** After suggesting changes to a Merge Request, or after
Raising Concerns to a Commit, you should be prepared to review it again.
Ensure the necessary changes were made, and any problems you found were
reasonably resolved.

Be sure to devote just as much attention to the follow up review as to the
original one! Apply all ten principles anew.

Principle #10
-------------------------------

Reviewing can be daunting, so it helps to remember that
-*reviewers are not perfect!** Issues may slip past you, bugs may evade
detection, performance flaws may make it to production...in short, broken
code happens!

If you are not familiar with the code or concepts, you may want to request
that an additional reviewer provide feedback, but don't shy away from doing
the review yourself! Ultimately, four eyes are always better than two.

If you do realize you've made a mistake in a review, the best thing you can
do is own up to it. Raise a Concern on the commit if appropriate, or else
file a Bug Report.

.. _greview_checklist:

Merge Checklist
==============================

Every Merge Request is expected to meet all the following criteria before
it can be merged:

Reviewers are encouraged to help ensure compliance. It doesn't matter *who*
you are, if you see a problem on a Merge Request, or even a Commit, speak up!

Every Merge Request must...

(1) Accomplish the goals(s) it was designed to accomplish.

(2) Comply with Conventional Commits for all commit messages.

(3) Be rebased against the latest version of  ``devel`` (or whatever branch
    is targeted), and all conflicts resolved (:code:`$ git pull origin devel `).
    (We do NOT use the "squash" or "merge" Git strategies.)

(4) Have binaries and unnecessary cruft untracked and removed. (Keep an eye on
    `.gitignore`!)

(5) Compile and run properly. (Confirmed via the CI/CD Pipeline.)

(6) Be free of compiler errors and warnings; for C++, must compile with
    `-Wall -Wextra -Werror`. (Confirmed via the CI/CD Pipeline.)

(7) For C++, be Valgrind pure, meaning no memory leaks are detected.
    (Confirmed via the CI/CD Pipeline.)

(8) Comply with Coding and Technical standards.

(9) Include tests validating the accomplishment of goals in (1). These tests
    must be written in the project's test framework, if relevant.

(10) Be fully Commenting-Showing Intent commented.

(11) Have an up-to-date build script (generally CMake) if relevant.

(12) Be reviewed, built, tested, and approved by at least one trusted
     reviewer.

(13) Have up-to-date Sphinx documentation, which compiles with no warnings.

(14) Have all reviewer comments processed and marked "Done".

Don't worry! Although the list looks long, you can stay on top of most of these
items as you code, while some others require minimal effort.
