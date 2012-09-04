# How to detect forced update or not from in Git hooks script

https://github.com/kyanny/git-hooks-detect-force-update

This is a sample git pre-receive hook script for learning about how to detect forced update.

## Conclusion

    $ git rev-list oldrev ^newrev

## How to test

    $ rake -T
    rake forced_push  # git hooks test detect forced update
    rake normal_push  # git hooks test

## Step-by-Step introduction

Firstly, I describe a syntax of [git-rev-list(1)](http://git-scm.com/docs/git-rev-list).

In this case, we assume within a Git working repository that has this straight history.

    1 --- 2 --- O --- X --- 3 --- 4 --- N

General usage of `git-rev-list` is below.

    $ git rev-list N

This command will show all of commits reachable from commit N (note: `git-rev-list` shows commits *reverse chronological order*)

`git-rev-list` accepts multiple arguments.

    $ git rev-list N O

This command will show same output as `git rev-list N`, because commit O is an ancestor of commit N.

Then, `git-rev-list` allows you to exclude commits from output.

    $ git rev-list N ^O

^O means that to exclude commits reachable from O, so this command will show N, 4, 3, X (note: O is excluded)

---

Since we are learned about `git-rev-list`, I describe a case about forced update occured.

In this case, we assume within a Git working repository that has this complex history.

    * --- B --- * --- O ($oldrev)
           \
            * --- X --- * --- N ($newrev)

 1. In old tree, we had 4 commits (*, B, *, O) and pushed them to remote.
 2. We checkout a new branch from commit B, it's new tree.
 3. In new tree, we had 4 commits (*, X, *, N) and pushed them to remote with --force option!

When pushed, hook pre-receive script invoked with standard input. The format of stdin parameter is described at [githooks(5)](http://git-scm.com/docs/githooks).

Typically, we extract two commit object sha1 from stdin - oldrev and newrev. oldrev is *HEAD of old tree*, newrev is *HEAD of new tree*.

In this situation, we can detect forced push by `git-rev-list` output.

`git rev-list oldrev ^newrev` shows the commits reachable from oldrev but not reachable from newrev. This shows the commits *existed only old tree*.
If this command show any commits, old tree was replaced with new tree, so forced update was occured. This is what we want!

If this command show none of commits, new tree was normally updated, so forced update was not occured. Simply.

## See Also

 * [git-rev-list(1)](http://git-scm.com/docs/git-rev-list)
 * [Git hook example: post-receive-email](http://git.gnus.org/gnus.git/hooks/post-receive-email)
 * [git - How to detect a forced update - Stack Overflow](http://stackoverflow.com/questions/10319110/how-to-detect-a-forced-update)
 * [githooks - Git hook to detect push --mirror - Stack Overflow](http://stackoverflow.com/questions/9210957/git-hook-to-detect-push-mirror)
