Installation
-------------

Symlink the script to a location visible by `PATH`. I have mine set to `~/bin`.

An optional global variable `GIT_CACHED_DIR` can be set. This is where the cached objects are stored. The default is shown below.

I also have a shell alias set so it is called instead of `git` itself.

```bash
export PATH=$HOME/bin:$PATH
export GIT_CACHED_DIR=$HOME/.git_objects

alias git=gitc
```

Usage
------

`gitc` accepts all `git` commands. For the ones it does not know of, it will simply pass-through to git.

```bash
gitc clone --branch 8.x git://git.drupal.org/project/drupal.git
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

Notes
------

  - The cached clone is set with `--reference` pointing to the cache repository. See the man page for git-clone.
  - Removing projects from the cache repo does not remove their objects. This allows the referenced clones to continue to work. Removing the whole cache repo for the domain on the other hand will invalidate them. Just remember to repair.
  - If you move the clone to another machine it may invalidate the path pointing to the cache. Changing `GIT_CACHE_DIR` will do the same. Simply call `gitc cache-repair` in your working directory to fix it.
  - This is the first bash script I’ve created. Consider this a fair warning. Tested on Mac OSX Lion.
  - This new development version is incompatible with master.
