Installation
-------------

Open ~/.bash_profile and add the following. If your shell is using a different file, then use that. Modify as needed:

    export GIT_CACHE_DIR=$HOME/.git_cache
    export PATH=/path/to/these/scripts:$PATH

Then make them executable from the terminal:

    cd /path/to/these/scripts
    chmod u+x git-cache*

The above assumes you left the names intact. It will make `git-cache-control`, `git-cache-parse-url`, `git-cache-repo` and `git-cached` executable. git-cache-parse-url and git-cache-repo are helpers and should not be called directly.

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

Notes
------

  - You can pass any git command to git-cached and it should handle it just fine since it will simply pass through all commands unless you are cloning or repairing.
  - To remove all dependencies to the cache, run `git repack -a` from the clone. There is also a ‘.git/objects/info/alternates’ you can remove which points to the cache. I'll update the script to automate this in the future.
  - The cached clone is set with `--reference` pointing to the cache repository. See the man page for git-clone.
  - Removing projects from the cache repo does not remove their objects. This allows the referenced clones to continue to work. Removing the whole cache repo for the domain on the other hand will invalidate them. Just remember to repair.
  - If you move the clone to another machine it may invalidate the path pointing to the cache. Changing `GIT_CACHE_DIR` will do the same. Simply call `git-cached repair` in your working directory to fix it.
  - This is the first bash script I’ve created. Consider this a fair warning. Tested on Mac OSX Lion.
  - This will eventually be moved to its own project.
  - Idea taken from Randy Fay’s post which is very specific to drupal. http://randyfay.com/node/93

