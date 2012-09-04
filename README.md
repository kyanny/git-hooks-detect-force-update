# How to detect forced update or not from in Git hooks script

This is a sample git pre-receive hook script for learning about how to detect forced update.

## Conclusion

```
$ git rev-list oldrev ^newrev
```

## How to test

```
$ rake -T
rake forced_push  # git hooks test detect forced update
rake normal_push  # git hooks test
```

## Step-by-Step introduction

Firstly, I describe a syntax of `[git-rev-list(1)](http://git-scm.com/docs/git-rev-list)`.

In this case, we assume within a Git working repository that has this straight history.

```
   1 --- 2 --- O --- X --- 3 --- 4 --- N
```

General usage of `git-rev-list` is below.

```
$ git rev-list N
```

This command will show all of commits reachable from commit N (note: `git-rev-list` shows commits *reverse chronological order*)

`git-rev-list` accepts multiple arguments.

```
$ git rev-list N O
```

This command will show same output as `git rev-list N`, because commit O is an ancestor of commit N.

Then, `git-rev-list` allows you to exclude commits from output.

```
$ git rev-list N ^O
```

^O means that to exclude commits reachable from O, so this command will show N, 4, 3, X (note: O is excluded)

---



```
   * --- B --- * --- O ($oldrev)
          \
           * --- X --- * --- N ($newrev)
```

## See Also

 * [git-rev-list(1)](http://git-scm.com/docs/git-rev-list)
 * [Git hook example: post-receive-email](http://git.gnus.org/gnus.git/hooks/post-receive-email)
 * [git - How to detect a forced update - Stack Overflow](http://stackoverflow.com/questions/10319110/how-to-detect-a-forced-update)
 * [githooks - Git hook to detect push --mirror - Stack Overflow](http://stackoverflow.com/questions/9210957/git-hook-to-detect-push-mirror)
