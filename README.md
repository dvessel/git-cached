Installation
-------------

Open ~/.bash_profile and add the following. If your shell is using a different file, then use that.

To call the commands without prefixing the paths:

    export PATH=/path/to/scripts:$PATH
    
    # or set as a shortcut:
    alias gitc=/path/to/scripts/git-cached
    alias gitcc=/path/to/scripts/git-cache-control

This is where the cached references are stored. It is optional:

    export GIT_CACHED_DIR=$HOME/.git_cache

Then make them executable from the terminal (if they are not executable already):

    cd /path/to/scripts/
    chmod u+x git-cache*


Usage
------

When cloning, use `git-cached` instead of `git` and the cache will be generated on demand.  When cloning a second time, the cache repo will be fetched again updating the local cache repository with the remote. For big projects, it’s a lot faster.

    $ git-cached clone --branch 7.x-2.x git://git.drupal.org/project/foobar.git

The cache can be managed with `git-cache-control`. You can add, update, remove or list. When adding, just supply a url to the project.

    $ git-cache-control add git://git.drupal.org/project/foobar.git

The rest of the commands need the repository domain and the project name (except `list`). The domain and project is automatically parsed out from the url on initial add but the project url is also valid. It will parse out the needed arguments.

    $ git-cache-control update @git.drupal.org foobar

The above command is optional. The cached repository is updated every time `git-cached` is used to clone the project. `--all` is also an option or you can omit it which will update all projects for the repository. It will cleanup unnecessary files and optimize the cache repository automatically with `git gc —auto`.

To remove a specific project from cache:

    $ git-cache-control remove @git.drupal.org foobar

To remove the cache for the entire domain, use `--all` or omit the third argument. You will get a confirmation and a listing of all projects for the domain held in the cache repository. Note that it may contain objects from removed projects but it can be rebuilt with little hassle.

    $ git-cache-control remove @git.drupal.org --all

The above commands pointing to the `@git.drupal.org` must be prefixed with your user name if the initial clone was for a user specific account. User specific accounts are separated from public ones.

    $ git-cached clone --branch 7.x-2.x username@git.drupal.org:project/foobar.git
    $ git-cache-control list username@git.drupal.org    # prints foobar
    $ git-cache-control remove username@git.drupal.org  # removes all caches
    $ git-cached status                                 # prints errors
    $ git-cached repair                                 # rebuilds the cache
    $ git-cached status                                 # prints without errors

If the cache goes missing for any reason, you can rebuild it from scratch with `git-cached repair` while you are in the working directory. The list command is self explanatory and it does not need a third argument.

To remove the dependency from the cache, use `no-cache`. It will repack the local repo with `git repack -a -f -d` and remove the pointer in the alternates file.

    $ git-cached no-cache

Notes
------

  - You can pass any git command to git-cached and it should handle it just fine since it will simply pass through all commands unless you are cloning or repairing.
  - The cached clone is set with `--reference` pointing to the cache repository. See the man page for git-clone.
  - Removing projects from the cache repo does not remove their objects. This allows the referenced clones to continue to work. Removing the whole cache repo for the domain on the other hand will invalidate them. Just remember to repair.
  - If you move the clone to another machine it may invalidate the path pointing to the cache. Changing `GIT_CACHE_DIR` will do the same. Simply call `git-cached repair` in your working directory to fix it.
  - This is the first bash script I’ve created. Consider this a fair warning. Tested on Mac OSX Lion.
