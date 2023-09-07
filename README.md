# NAME

git-nullmerge - Find and merge a commit with a tree that matches an ancestor exactly.

# SYNOPSIS

`git nullmerge [OPTIONS] <commit>`

# DESCRIPTION

When a branch has undergone extensive history rewriting, attempting to merge it
can result in many false and unnecessary merge conflicts. If such history
rewriting has preserved the entire tree at some points along the history, a
so-called null merge can be justified. `git-nullmerge` can automatically find
the correct point in history and create a null merge. The message will include a
justification with an overview of the relavant part of the commit graph.

Technically, `git nullmerge` will:
* Find the latest commit `THEIR` such that
  * `THEIR` is in `<commit>`
  * `THEIR` is not in `OUR` (where `OUR` is `HEAD` at start of execution)
  * There exists a commit `IDENTICAL` such that
    * `IDENTICAL` is in `OUR`
    * `IDENTICAL` is not in `THEIR`
    * `IDENTICAL` and `THEIR` point to identical trees.
* Make a merge commit with `OUR` and `THEIR` as parents and with a tree identical to `OUR`.
* Optionally update `HEAD` to point to the newly created merge commit.

# INSTALLATION

Place `git-nullmerge` somewhere in your `$PATH`.

# EXAMPLE

Suppose a remote repository has altered all commit messages, and also diverged.
It will then appear like an unrelated history, and `git merge` will refuse to
merge it.

```
  * (remote) E
  * C'
  * B'
  * A'
*   (HEAD) D
*   C
*   B
*   A
```

Using `git nullmerge remote`, the actual fork point will be identified and
merged:


```
  * (remote) E
* | (HEAD) Null merge
|\|
| * C'
| * B'
| * A'
*   D
*   C
*   B
*   A
```

After this, `git merge` can do a correct merge.

```
*   (HEAD) Merge
|\
| * (remote) E
* | Null merge
|\|
* | D
| * C'
| * B'
| * A'
*   C
*   B
*   A
```

# SEE ALSO

`git replace`: Replace objects in the git graph at the interpretation level.

[`git-reparent`](https://github.com/MarkLodato/git-reparent): Create a commit with the same tree but different parents.
