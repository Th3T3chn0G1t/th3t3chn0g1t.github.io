# Glean

## About

So I made the foolish decision to write a text editor, primarily to learn a GUI framework (in this case the ***wonderful*** GTK+3), partially because a guy in the Cherno's Discord was also writing one - and I wanted to show him up :3

## Development

### A Retrospective of Work So Far
#### 12th June, 2021

The process of getting glean to the point where I could open files and edit text was a surprisingly arduous one, mainly my own fault in trying to add a file/project tree first from which to open files.
Looking back at the first while, you might see that everything was in one big-ass `main.c` file - this was because, since I was just messing about with GTK at that point, I didn't really give much of a hoot about how maintainable the application would be. When I later realised how much work I was likely to end up putting into the damned thing I cleaned up my act into separate files and a centralised internal API header.

![image](https://user-images.githubusercontent.com/80696589/121787011-8542c300-cbbb-11eb-929e-49020202cbf7.png)

*Clearly, design is my passion*

Part of my initial strife, a problem I still have as I am reluctant to let Glade handle my callback assignation and give in to Glib, is that I was sharing callbacks across the sole API header and ended up with messy sharing of bits and pieces across the code base, to combat this I centralised each "segment" of the editor as it stood to its own file - the file tree in `files.c`, the editor tabs in `editor.c` etc. This, at least at the time of writing, still leaves glean as being:
1. Horribly tied to GTK in a mingled mess
2. Extremely inextensible due to vital editor aspects being entirely centralised (though I am in the process of remedying this)
3. 90% bloody callbacks
Given that one of my goals is to make the logic of the editor independant from GTK, this poses a problem. My planned solution to remedy this is to essentially move all the implementations of logic out of callbacks and into separate, generic operations on the editor state - that way eventually maybe I could pry apart GTK from glean from their death-grip embrace and possibly try porting glean to another framework to test the robustness of the backend.

Moving onto the next hell I faced: Inotify. I wanted to watch for changes to the current directory glean was operating on to keep the directory tree in sync with the FS. Sounded easy enough - after all its a pretty commonly needed feature one would think. And, in fairness, inotify itself is a nice enough interface - although very UNIX-y in nature (not neccesarily a bad thing, but a point to be made all the same, no the real issue was with GTK. Turns out that X11 and GTK tend to have a hissy fit when you start messing about with threading, and valgrind was none-to-pleased with me either, so I had to set up the spaghetti mess of pipes that the inotify watcher thread uses to communicate with the main GTK thread. For anyone looking now and thinking its bad, I assure you it was far worse when I first implemented it - including a pipe whose sole purpose was to close the thread on write.
Code-style fiascoes aside, the logic itself had a *load* of lovely, segfaulting flaws. This is mainly on account of my lack of overall experience both with the pipes API, and multithreading. With the help of Discord, Manpages and StackOverflow, I ended up with a semi-robust system for setting watchers with callbacks on files and folders.

Since this, there has mostly just been feature cramming - a toolbar, key-combinations, an undo-stack, and a bunch of cleanups, which brings us to the time of writing. Glean could reasonably be called a semi-featureful text editor - if a tad unstable - and has a few big changes down the road: more generalised extensibility, programming-specific features like linting and code completion - maybe using YCM 👀

In any case, you'll see them popping up the top of the page very soon all things well, and thank you for reading!

