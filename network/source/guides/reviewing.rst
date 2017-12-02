.. _greview:

Guide: Reviewing Code
##############################

We have two means of reviewing code: **pre-commit** and **post-commit**.
In general, we try to emphasize the *pre-commit* review, to avoid landing
broken code.

**Differential** is used for pre-commit review (see :ref:`phab_differential`),
while **Audit** is used for post-commit review (see :ref:`phab_audit`). Both
follow a very similar workflow, which is covered in the linked documentation.

Who Can Review?
==============================

It can sometimes be really daunting to review someone else's code, especially
if that person has more experience, expertise, or seniority than you do.
But don't be afraid!

First of all, everyone makes mistakes, and we know it! When a coder knows
he or she will be code reviewed, it's like a safety net: they can more easily
relax and code, knowing that another set of eyes will be reading this code
before it's considered "done".

Second, everyone learns from a code review. The author gains additional
insight on how to improve their code; the reviewer can learn new techniques
and ideas from the code they're reviewing; the bystanders get the benefits
of *both*.

This is the main reason why Differential Revisions (for programming, anyway)
are almost always open to the public. Anyone can contribute valuable feedback,
and anyone can learn code reviews!

In short, **don't be afraid to contribute feedback**! Code reviewing can be
one of the most valuable contributions you can make to MousePaw Media's
development work.

Principles of a Good Review
==============================

Principle #1
-------------------------------

The first and foremost principle of a good review is this: **if you commit
to review code, review it thoroughly!** *Read* the code, don't just skim it.
Research things you don't understand. Ask questions.

Principle #2
-------------------------------

This goes hand-in-hand with the second principle: **aim to understand every
changed line**.

Principle #3
-------------------------------

**Don't assume the code works - build and test it yourself!**
You should actually pull down the code (via `arc patch`) and test it out.
Take note of the Test Plan described at the top of the Revision.

Principle #4
-------------------------------

**Commenting matters.** MousePaw Media follows the Commenting Showing Intent
[CSI] standard, which means that roughly *every logical statement should have a
comment describing the programmer's intention for it.* If you don't see
a CSI comment, request one to be added into the code, and look for it before
you approve.

Principle #5
-------------------------------

**Review temporary code as strictly as production code.** It can be shocking
just how often temporary "patch" code and workarounds make it into production,
and how much of it is never actually replaced. This is just a reality of
real-world programming. Thus, we should hold *all* code to the same standards
and expectations.

In other words, even if the code's solution isn't ideal, the implementation
should be clean, maintanable, and reasonably efficient.

To put it yet another way, **there is never an excuse for kludgy code.**

Principle #6
-------------------------------

**Consider how the code will work in production.** Design is important, and
integration matters. How will this code function in the real world? How will
it handle bad input and user error? Will it play well with the rest of the
code base? In short, be demanding of the code.

This ties in with Principle #5. It can be tempting to request (as the author)
or grant (as the reviewer) grace for "unfinished" code, but therein lies
a serious danger of shipping broken code!

If the code *is* broken, the user generally should not have easy access to it!
An unfinished class may be marked as "experimental" and documented as such,
thereby preventing a user from mistakening it for finished code. By contrast,
a broken function should not be exposed in a non-experimental class.

Another way to look at this matter is this: **if the code was shipped to
end-users on the next commit, it may be *functionally incomplete*, but
it should NOT be *broken*.** In reality, this goal is rarely achieved, but the
perspective will help prevent bad code from landing to the repository.

Principle #7
-------------------------------

**Check documentation, tests, and build files.** Good code doesn't just
include code, it includes all of the trappings that go with it.

A finished Differential Revision should contain all of the following:

* **Tests covering the new code.** Review these as strictly as you do the code
  itself, to ensure the test will fail if there is a problem.

* **Documentation for the new code.** The best documentation is written in
  tandem with the code itself. Don't accept documentation *later*; it should
  be present within the Revision itself!

* **Build files updated for the changes.** Any time code files are added,
  removed, or renamed, the build files need to reflect those changes.
  Similarly, if any dependencies have changed, the build files should reflect
  that too. This is one more reason why you should build the changes yourself
  (Principle #3)

* **README changes.** The markdown files, including `README.md`, `BUILDING.md`,
  `CHANGELOG.md`, and so forth should reflect the latest changes. In reality,
  these rarely need to be changed, 

Principle #8
-------------------------------



Principle #9
-------------------------------

**Follow up on reviews.** After suggesting changes to a Revision, or after
Raising Concerns to a Commit, you should be prepared to review it again.
Ensure the necessary changes were made, and any problems you found were
reasonably resolved.

Principle #10
-------------------------------

Reviewing can be daunting, so it helps to remember that
**reviewers are not perfect!** Issues may slip past you, bugs may evade
detection, performance flaws may make it to production...in short, broken
code happens!

If you do realize you've made a mistake in a review, the best thing you can
do is own up to it. Raise a Concern on the commit if appropriate, or else
file a Bug Report.

Differential Checklist
==============================

Every Differential Revision is expected to meet all the criteria of the
`**Differential Checklist** <https://phabricator.mousepawmedia.net/P1>`_
before it can be landed.

Reviewers are encouraged to help ensure compliance. It doesn't matter *who*
you are, if you see a problem on a Revision, or even a Commit, speak up!

Every Revision must...

(1) Accomplish the feature(s) it was designed to accomplish.
    [In some cases, the feature itself may be dropped, and only bugfixes and/or
    optimizations landed instead.]

(2) Have merged all changes from `master` into itself, and all conflicts
    resolved. (:code:`$ git pull origin master`)

(3) Have binaries and unnecessary cruft untracked and removed. (Keep an eye on
    `.gitignore`!)

(4) Compile and run properly - this should be confirmed via
    Harbormaster/Jenkins (if available).

(5) Be free of compiler errors and warnings (must compile with
    `-Wall -Wextra -Werror`).

(6) Be Valgrind pure (no memory leaks detected).

(7) Comply with Coding and Technical standards.

(8) Be free of linter errors. ($ arc lint --lintall)

(9) Be fully CSI commented.

(10) Have an up-to-date build script (generally CMake) if relevant.

(11) Contain relevant LIT tests, if the project is Goldilocks capable.

(12) Have a Test Plan, generally containing a list of Goldilocks tests
     the reviewer should run.

(13) Be reviewed, built, tested, and approved by at least one trusted
     reviewer (Staff or Trusted Contributor).

(14) Have up-to-date Sphinx documentation, which compiles with no warnings.

(15) Have all reviewer comments processed and marked "Done".
