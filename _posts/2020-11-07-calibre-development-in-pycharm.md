---
layout: post
title: How I set up a calibre plugin development environment in PyCharm
date: 2020-11-07 13:39:47 -0300
description: Wow, that was a pain. So I thought I’d spare you the same.
img: pycharm-remote-debugging-calibre-zoomed.png
img-caption: The PyCharm debugger paused on a breakpoint within my calibre plugin’s code
tags: [mac, pycharm, calibre, development]
categories: [books]
---
Wow, that was a pain. I’ve tried everything*:

- Using PyCharm’s “Attach to Process” ([docs](https://www.jetbrains.com/help/pycharm/attaching-to-local-process.html))
- Working with calibre’s built-in rpdb ([docs](https://manual.calibre-ebook.com/develop.html#using-the-python-debugger-as-a-remote-debugger))
- Attaching to a runnning process from VSCodium ([docs](https://code.visualstudio.com/docs/python/debugging#_basic-debugging))
- Setting up VSCodium’s `debugpy` server ([docs](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection))

*OK, not everything. I could have tried [debugging with Eclipse/PyDev](https://www.mobileread.com/forums/showthread.php?t=143208).

Nothing worked. Until I installed a 30-day trial of PyCharm Professional (I usually use their free Community Edition), which supports remote debugging. This is what I did to make that work:

## 1. Set up a PyCharm project

- Create a new project in PyCharm and point it to the directory that hold’s the plugin you’re working on as content root

## 2. Add remote debugging with path mappings to the project

- In Pycharm, go to “Run” > “Edit Configurations...”
- Click the “+” on the top left and choose “Python Debug Server”. This should give you something like this:

    ![Edit configuration for remote debugging in PyCharm]({{ site.images }}/pycharm-remote-debugging-configuration.png)

- Add path mappings for every single `.py` in your plugin like so:

    ![Path mappings for remote debugging in PyCharm]({{ site.images }}/pycharm-path-mappings.png)

    The “`the_eye`” in the remote path is based on the `plugin-import-name-the_eye.txt` in my plugin directory, so change that accordingly. As well as the local paths, of course. Thanks T P who figured out that these mappings need to be 1-on-1 and posting that finding on [the PyCharm forums](https://intellij-support.jetbrains.com/hc/en-us/community/posts/205816589-Debugging-Calibre-plugins-using-PyCharm) ages ago.

- Start listening for a debug server with “Run” > “Debug...” and pick the new configuration you just created.

See [the docs](https://www.jetbrains.com/help/pycharm/remote-debugging-with-product.html) for more information on PyCharm’s remote debugging.

## 3. Add the debug server to your plugin

- Figure out where your PyCharm is located and import `pydevd-pycharm` into your plugin from there:

  ```python
  sys.path.append('/Applications/PyCharm.app/Contents/debug-eggs/pydevd-pycharm.egg')
  import pydevd_pycharm
  ```

- Add the actual traces to wherever you want to inspect your code:

  ```python
  pydevd_pycharm.settrace('localhost', port=12345, stdoutToServer=True,stderrToServer=True)
  ```

- Don’t forget to save the files

## 4. Build your plugin and start calibre

- Open up your terminal and `cd` into your plugin’s directory
- `calibre-customize -b .`
- `calibre-debug -g`
- From within the interface do whatever action would trigger the function you added the trace to.

## 5. Ignore the warnings and enjoy stepping through your code:

  ![Remote debugging of a calibre plugin in PyCharm]({{ site.images }}/pycharm-remote-debugging-calibre.png)

## If you also want to step through calibre’s code, do this too:

- `git clone git://github.com/kovidgoyal/calibre.git`
- From PyCharm’s preferences go to “Project: ...” > “Project Structure” and add the `src` directory within the cloned repository as content root, like so:

    ![Add content root to project structure in PyCharm]({{ site.images }}/pycharm-project-structure.png)

- Add a path mapping from the absolute path to the `src` directory to “`.`” in the remote debugging configuration
- Start calibre with `export CALIBRE_DEVELOP_FROM="..."; calibre-debug -g` where `...` is the absolute path to that `src` directory

(Based on [the official documentation](https://manual.calibre-ebook.com/develop.html))
