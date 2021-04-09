# Git

## 2.2 [Git Basics - Recording Changes to the Repository](https://git-scm.com/book/en/v2/Git-Basics-Recording-Changes-to-the-Repository)

### The lifecycle of the status of your files

|  Untracked   |                   |    Unmodified     |                     |      Modified       |                   |   Staged   |
| :----------: | :---------------: | :---------------: | :-----------------: | :-----------------: | :---------------: | :--------: |
| Add the file | $\Longrightarrow$ | $\Longrightarrow$ | $$\Longrightarrow$$ | $$\Longrightarrow$$ | $\Longrightarrow$ | $\Uparrow$ |
|              |                   |   Edit the file   |  $\Longrightarrow$  |     $\Uparrow$      |                   |            |
|              |                   |                   |                     |   Stage the file    | $\Longrightarrow$ | $\Uparrow$ |
|  $\Uparrow$  | $\Longleftarrow$  |  Remove the file  |                     |                     |                   |            |
|              |                   |    $\Uparrow$     |  $\Longleftarrow$   |  $\Longleftarrow$   | $\Longleftarrow$  |   Commit   |



### .gitignore

The rules for the patterns you can put in the `.gitignore` file are as follows:

-   Blank lines or lines starting with `#` are ignored.
-   Standard glob patterns work, and will be applied recursively throughout the entire working tree.
-   You can start patterns with a forward slash (`/`) to avoid recursivity.
-   You can end patterns with a forward slash (`/`) to specify a directory.
-   You can negate a pattern by starting it with an exclamation point (`!`).

Glob patterns are like simplified regular expressions that shells use. An asterisk (`*`) matches zero or more characters; `[abc]` matches any character inside the brackets (in this case a, b, or c); a question mark (`?`) matches a single character; and brackets enclosing characters separated by a hyphen (`[0-9]`) matches any character between them (in this case 0 through 9). You can also use two asterisks to match nested directories; `a/**/z` would match `a/z`, `a/b/z`, `a/b/c/z`, and so on.

Here is another example `.gitignore` file:

```
# ignore all .a files
*.a

# but do track lib.a, even though you're ignoring .a files above
!lib.a

# only ignore the TODO file in the current directory, not subdir/TODO
/TODO

# ignore all files in any directory named build
build/

# ignore doc/notes.txt, but not doc/server/arch.txt
doc/*.txt

# ignore all .pdf files in the doc/ directory and any of its subdirectories
doc/**/*.pdf
```

>   **Tip**
>
>   GitHub maintains a fairly comprehensive list of good `.gitignore` file examples for dozens of projects and languages at https://github.com/github/gitignore if you want a starting point for your project.
>
>   **Note**
>
>   In the simple case, a repository might have a single `.gitignore` file in its root directory, which applies recursively to the entire repository. However, it is also possible to have additional `.gitignore` files in subdirectories. The rules in these nested `.gitignore` files apply only to the files under the directory where they are located. The Linux kernel source repository has 206 `.gitignore` files.
>
>   It is beyond the scope of this book to get into the details of multiple `.gitignore` files; see `man gitignore` for the details.

## git diff

There are three status：

`unstaged`  $\Rightarrow$ `staged` $\Rightarrow$ `last commit`

1.  `git diff` : This command compares what is in your working directory with what is in your staging area.(`unstaged` vs `staged`)
2.  `git diff --staged` : This command compares your staged changes to your last commit.(`staged` vs `last commit`)
3.  `--staged`  and `--cached` are synonyms.

>   **Note** 
>
>   For an even more explicit reminder of what you’ve modified, you can pass the `-v` option to `git commit`. Doing so also puts the diff of your change in the editor so you can see exactly what changes you’re committing.

