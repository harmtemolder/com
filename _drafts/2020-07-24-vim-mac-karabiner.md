---
layout: post
title: Want Vim's navigation on your entire Mac? Get Karabiner!
date: 2020-07-24 09:46:00 -0400
description: I only recently discovered the speed of Vim's navigation. And now I use it everywhere.
img: oura-screenshot.png
img-caption: Screenshot of my Oura app
tags: [Mac, Vim, Karabiner]
categories: [Mac]
---
It has been a couple of months since I tumbled down the Karabiner rabbit hole. And it has been glorious ever since: My keyboard becomes more and more efficient every day because of little tweaks to my Karabiner setup. Let me walk you through it. But first, install Karabiner-Elements through [Homebrew](https://brew.sh/):

```shell
brew cask install karabiner-elements
```

When you then run `/Applications/Karabiner-Elements.app`, you'll be greated with an empty slate:

![An empty Karabiner-Elements window]({{ site.images }}/karabiner-empty.png)

## I've mapped Caps Lock to Escape on my Mac

Let's start off easy: map Caps Lock to Escape. You're probably familiar with the concept of a keyboard's home row: the row where your fingers will sit when at rest, ASDFGHJKL... But what is Caps Lock doing there? Who needs it, in this day and age? On the other hand, Escape—which you use intensively, especially when working with Vim—is about as far away from the home row as you can get. Let's fix that:

![Remap Caps Lock to Escape on a Mac through Karabiner]({{ site.images }}/karabiner-caps-lock-to-escape.png)

And to teach you to actually use this instead of the original Escape key, disable the latter:

![Disable the Escape key on a Mac through Karabiner]({{ site.images }}/karabiner-disable-escape.png)

OK, that was easy. But that was just the "Simple modifications" tab. Let's move on to the good stuff.

## Complex modifications in Karabiner-Elements

Karabiner stores it's current setup in `~/.config/karabiner/karabiner.json`, but edits directly in that file may get overwritten if you change settings from the interface. Better to add `json` files in the `~/.config/karabiner/assets/complex_modifications` folder instead. The easiest way to do that is through [Karabiner's complex modifications repository](https://ke-complex-modifications.pqrs.org/). One configuration that I liked from there is the one that disables all right-handed keys when holding the right Shift key and the left-handed keys with the left Shift, forcing you to use the proper Shift:

!["Force the user to make key combination with two hands (rev 2)" in the Karabiner complex modifications repository]({{ site.images }}/karabiner-complex-modifications-repository.png)

Click the blue "Import" button to go straight to Karabiner:

!["Force the user to make key combination with two hands (rev 2)" imported into Karabiner]({{ site.images }}/karabiner-import-complex-modifications.png)

Et voila, you have your first complex modification set up. To get a sense of what this looks like on the inside, open the `json` file in the `~/.config/karabiner/assets/complex_modifications` folder:

![The JSON behind "Force the user to make key combination with two hands (rev 2)"]({{ site.images }}/karabiner-complex-modifications-json.png)
