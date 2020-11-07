---
layout: post
title: How I set up a calibre plugin development environment in PyCharm
date: 2020-11-07 13:39:47 -0300
description:
img:
img-caption:
tags: [mac, pycharm, calibre, development]
categories: [books]
---
Wow, that was a pain. I’ve tried everything:

- Using PyCharm’s “Attach to Process” ([docs](https://www.jetbrains.com/help/pycharm/attaching-to-local-process.html))
- Working with calibre’s built-in rpdb ([docs](https://manual.calibre-ebook.com/develop.html#using-the-python-debugger-as-a-remote-debugger))
- Attaching to a runnning process from VSCodium ([docs](https://code.visualstudio.com/docs/python/debugging#_basic-debugging))
- Setting up VSCodium’s `debugpy` server ([docs](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection))

Nothing worked. Until I installed a 30-day trial of PyCharm Professional (I usually use their free Community Edition), which supports remote debugging. This is what I did to make that work:

## 1. Set up a PyCharm project

1. Create a new project in PyCharm and point it to the directory that hold’s the plugin you’re working on as content root

## 2. Add remote debugging with path mappings to the project

1. In Pycharm, go to “Run” > “Edit Configurations...”
2. Click the “+” on the top left and choose “Python Debug Server”. This should give you something like this:

  ![Edit configuration for remote debugging in PyCharm]({{ site.images }}/pycharm-remote-debugging-configuration.png)

3. Add path mappings for every single `.py` in your plugin like so:

  ![Path mappings for remote debugging in PyCharm]({{ site.images }}/pycharm-path-mappings.png)

  The `the_eye` in the remote path is based on the `plugin-import-name-the_eye.txt` in my plugin directory, so change that accordingly. As well as the local paths, of course. Thanks T P who figured out that these mappings need to be 1-on-1 and posting that finding on [the PyCharm forums](https://intellij-support.jetbrains.com/hc/en-us/community/posts/205816589-Debugging-Calibre-plugins-using-PyCharm) ages ago.

4. Start listening for a debug server with “Run” > “Debug...” and pick the new configuration you just created.

See [the docs](https://www.jetbrains.com/help/pycharm/remote-debugging-with-product.html) for more information on PyCharm’s remote debugging.

## 3. Add the debug server to your plugin

1. Figure out where your PyCharm is located and import `pydevd-pycharm` from there:

  ```python
  sys.path.append('/Applications/PyCharm.app/Contents/debug-eggs/pydevd-pycharm.egg')
  import pydevd_pycharm
  ```

2. Add the actual traces to wherever you want to inspect your code:

  ```python
  pydevd_pycharm.settrace('localhost', port=12345, stdoutToServer=True,stderrToServer=True)
  ```

3. Don’t forget to save the file

## 4. Build your plugin and start calibre

1. Open up your terminal and `cd` into your plugin’s directory
2. `calibre-customize -b .`
3. `calibre-debug -g`
4. From within the interface do whatever action would trigger the function you added the trace to.

## If you also want to step through calibre’s code, do this too:

1. `git clone git://github.com/kovidgoyal/calibre.git`
2. From PyCharm’s preferences go to “Project: ...” > “Project Structure” and add the `src` directory within the cloned repository as content root, like so:

  ![Add content root to project structure in PyCharm]({{ site.images }}/pycharm-project-structure.png)

3. Start calibre with `export CALIBRE_DEVELOP_FROM="..."; calibre-debug -g` where `...` is the absolute path to that `src` directory

(Based on [the official documentation](https://manual.calibre-ebook.com/develop.html))
