Glossary
#######################

..  glossary::

    Commit
        (1) [Git] A batch of changes to a repository, with a commit message.

        (2) [Phabricator] A batch of changes to a repository, after being
            :term:`landed` (a.k.a. in "post-commit" stage).

    Diff
        [Phabricator] A single update to a :term:`Revision`. Colloqually, a
        synonym for a Revision.

    land
    landed
    landing
        [Phabricator] When a :term:`Revision` is landed, it is committed to
        the repository, and the Revision is marked as "closed".

    Revision
        [Phabricator] A batch of Git Commits, before being landed (a.k.a. in
        "pre-commit" stage. Internally, a Git patch. A Revision is also tied to
        a single Git branch. Analogous to a GitHub "Pull Request". See also,
        :term:`Diff`.
