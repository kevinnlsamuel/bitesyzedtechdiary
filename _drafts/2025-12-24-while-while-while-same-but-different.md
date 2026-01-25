---
description: The `while` loop is not as simple as i thought!
# image:
  # path:
  # alt:
---

I was trying to run a `while` loop in my shell. Easy enough. I do it all the time.
Here's a simulation of what I ran

```console
$ ls -1
list

$ cat -n list
1   http://example.com/url1
2   http://example.com/url2

$ while read url < list; do
>    wget $url
> done

... a perpetual loop ...
... interrupted with C-c ...

$ ls -1
list
url1.html
url1.html.2
url1.html.3
url1.html.4
url1.html.5
...
```

url2 did not get downloaded. So the loop ran multiple times on the same item. Hmmm.

Experience informed me immediately that the looping construct was incorrect.

```console
$ while read url; do
>   wget $url
> done < list
```

I should have caught that sooner. I've written loops like this a lot of times.
Sure I haven't made any recently… But still I should have known the right form.

So why didn't I?

Well I've been studying the
[POSIX spec for the shell language](https://pubs.opengroup.org/onlinepubs/9799919799/utilities/V3_chap02.html).
And so I went through this train of thought.

1. `while` executes its body as long as the return code of the **condition** (also a command) is `0`
2. `read` reads from the standard input and assigns fields to variables
3. The input redirection operator `<` sends a file to the stdin of a command
4. `while read x < file` does all the above

Except it didn't. The loop was executed repeatedly on the same line. And so starts my rambling
blog post on all the forms of `while`.

# while forever

```shell
while : ; do
    ...
done
```

`while` executes the command after it (up till the line break, or `;`) until the
command exits with a failure (exit code > 0). As long as the exit code is 0, the
body of the loop is also executed.

`:` is a shell built-in that always exits with 0: a <abbr title="no operation">noop</abbr>
command. Using `true` would have the same effect.

<aside class="weird-aside">
    <span class="l1">
        Conversely,
    </span>
    <pre class="language-shell highlighter-rouge highlight">
        <code>
            while false ; do
                ...
            done
        </code>
    </pre>
    <span class="or">
        or
    </span>
    <pre class="language-shell highlighter-rouge highlight">
        <code>
        while ! : ; do
            ...
        done
        </code>
    </pre>
    <span class="l3">
        would never run.
    </span>
</aside>


<style>
.weird-aside {
    display: grid;
    align-items: center;
    gap: 5px;
    grid-template-columns: auto 3em auto;
    grid-template-areas:
        "l1 l1 l1"
        "code1 or code2"
        "l3 l3 l3";
}
.weird-aside pre {
    margin: 0px;
}
.l1 { grid-area: l1; }
.l3 { grid-area: l3; }
.or { grid-area: or; justify-self: center; }
</style>
<!--
- vim: spell spelllang=en
-->
