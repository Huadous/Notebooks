# [What does “branching is free” mean in Git?](https://softwareengineering.stackexchange.com/questions/202432/what-does-branching-is-free-mean-in-git)

The claim that "branching is *free* in git" is a simplification of facts because it isn't "free" per se. Looking under the hood a more correct claim would be to say that branching is *[redonkulously](http://www.urbandictionary.com/define.php?term=redonkulous) cheap* instead, because [branches are basically references to commits](http://git-scm.com/book/en/Git-Internals-Git-References). I define "cheapness" here as the less overhead the cheaper.

Lets dig in to why Git is so "cheap" by examining what kinds of overhead it has:

## How are branches implemented in git?

The git repository, `.git` mostly consists of directories with files that contain metadata that git uses. Whenever you create a branch in git, with e.g. `git branch {name_of_branch}`, a few things happen:

-   A reference is created to the local branch at: `.git/refs/heads/{name_of_branch}`
-   A history log is created for the local branch at: `.git/logs/refs/heads/{name_of_branch}`

That's basically it, a couple of text files are created. If you open the reference as a textfile the contents will be the id-sha of the commit the branch is pointing at. Note that **branching does not require you to make any commits** as they're another kind of object. Both branches and commits are "first-class citizens" in git and one way is to think about the branch-to-commit relationship as an aggregation rather than a composition. If you remove a branch, the commits will still exist as "dangling". If you accidentally removed a branch you can always try to find the commit with [`git-lost-found`](https://www.kernel.org/pub/software/scm/git/docs/git-lost-found.html) or [`git-fsck --lost-found`](https://www.kernel.org/pub/software/scm/git/docs/git-fsck.html) and create a branch on the sha-id you find left hanging (and as long as git hasn't done any garbage collection yet).

So how does git keep track of which branch you're working on? The answer is with the `.git/HEAD` file, that looks sort of like this if you're on the `master` branch.

```
ref: refs/heads/master
```

Switching branches simply changes the reference in the `.git/HEAD` file, and then proceeds to change the contents of your workspace with the ones defined in the commit.

## How does this compare in other version control systems?

[In **Subversion**, branches are virtual directories in the repository](http://svnbook.red-bean.com/en/1.7/svn.branchmerge.using.html). So the easiest way to branch is to do it remotely, with a one-liner `svn copy {trunk-url} {branch-url} -m "Branched it!"`. What SVN will do is the following:

-   Copy the source directory, e.g. `trunk`, to to a target directory,
-   Commit the changes to finalize the copy action.

You will want to do this action remotely on the server, because making that copy locally is a linear-time operation, with files being copied and symlinked. This is a **very slow** operation, whereas doing it on the server is a constant time operation. Note that even when performing the branch on the sever, subversion **requires a commit** when branching while git does not, which is a key difference. That is one kind of overhead that makes SVN marginally less cheap than Git.

The command for [switching branches in SVN](http://svnbook.red-bean.com/en/1.6/svn.branchmerge.switchwc.html), i.e. `svn switch`, is really the `svn update` in disguise. Thanks to the virtual directory concept the command is a bit more flexible in svn than in git. Sub directories in your workspace can be switched out to mirror another repository url. The closest thing would be to use [`git-submodule`](http://git-scm.com/book/en/Git-Tools-Submodules) but using that is semantically quite different from branching. Unfortunately this is also a design decision that makes switching a bit slower in SVN than in Git as it has to check every workspace directory which remote-url it is mirroring. In my experience, Git is quicker to switch branches than SVN.

SVN's branching comes with a cost as it copies files and always need to be made publicly available. In git, as explained above, branches are "just references" and can be kept in your local repository and be published to your discretion. In my experience however SVN is still remarkably cheaper and more performant than e.g. ClearCase.

It's only a bummer that SVN is not decentralized. You can have multiple repositories as mirrored to some source repo but synching differing changes multiple SVN-repositories is not possible as SVN does not have uniquely identifiers for commits (git has hashed identifiers that are based on the contents of the commit). The reason why I personally started using git over SVN though is because [initiating a repository is remarkably easier and cheaper in git](https://softwareengineering.stackexchange.com/a/69314/1683). Conceptually in terms of software configuration management, each divergent copy of a project (clone, fork, workspace or whatever) is a "branch", and given this terminology creating a new copy in SVN is not as cheap as Git, where the latter has branches "built-in".

As another example, in **Mercurial**, branching started out a bit different as a DVCS and creating/destroying named branches required seperate commits. Mercurial developers implemented later in development [bookmarks](http://mercurial.selenic.com/wiki/Bookmarks/) to mimic git's same branching model though `heads` are called `tips` and `branches` are `bookmarks` instead in mercurial terminology.