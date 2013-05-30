Installation
-------------

Symlink the script to a location visible by `PATH`. I have mine set to `~/bin`.

An optional global variable `GIT_CACHED_DIR` can be set. This is where the cached objects are stored. The default is shown below.

```bash
export PATH=$HOME/bin:$PATH
export GIT_CACHED_DIR=$HOME/.gitobjectstore
```

Usage
------

`gitc` accepts all `git` commands. For the ones it does not know of, it will simply pass-through to git.

When cloning, use the `cclone` (two c's as in cached clone).

```bash
gitc cclone --branch 8.x git://git.drupal.org/project/drupal.git
```

If at anytime you need to remove all reference to the cache (runs `git repack -a -d -l` and removes pointer in the alternates file.:

```bash
gitc cache-detach
```

It can be added back at any time (runs `git gc` garbage collection to remove local objects):

```bash
gitc cache-attach
```

To repair it (it simply detaches and reattaches):

```bash
gitc cache-repair
```

If you need to run git commands on the cache store itself, use the `cache` sub-command:

```bash
gitc cache remote
```

When cloning, attaching or repairing, you can pass in a `--store-group` option to tell it to use a separate object store. It defautls to `default`.

```bash
gitc cclone --store-group foobar --branch 8.x git://git.drupal.org/project/drupal.git
```

The above command will point the object store to `$HOME/.gitobjectstore/foobar`. (defaults to `$HOME/.gitobjectstore/default`)

Notes
------

  - The cached clone is set with `--reference` pointing to the object storage. See the man page for git-clone.
  - If you move the clone to another machine it may invalidate the path pointing to the object store. Changing `GIT_CACHE_DIR` will do the same. Simply call `gitc cache-repair` in your working directory to fix it.
  - This is the first bash script I’ve created. Consider this a fair warning. Tested on Mac OSX Lion.
  - I personally don't use this for critical work. I'm using this to reduce the bandwidth and disk space on multiple versions of checked out repos that are mostly read only. It does work fine from what I've tested.
  - This new development version is incompatible with the master branch.
