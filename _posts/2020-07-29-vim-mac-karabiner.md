---
layout: post
title: Want Vim's navigation on your entire Mac? Get Karabiner!
date: 2020-07-29 13:45:48 -0400
description: I only recently discovered the speed of Vim's navigation. And now I use it everywhere.
img: vintage-typewriter.jpg
img-caption: Close-up of a vintage typewriter
tags: [mac, vim, karabiner]
categories: [mac]
---
It has been a couple of months since I tumbled down the Karabiner rabbit hole. And it has been glorious ever since: My keyboard becomes more and more efficient every day because of little tweaks to my Karabiner setup. Let me walk you through it. But first, install Karabiner-Elements through [Homebrew](https://brew.sh/):

```shell
brew cask install karabiner-elements
```

When you then run `/Applications/Karabiner-Elements.app` (or just search for “karabiner” in Spotlight or Alfred), you'll be greeted with an empty slate:

![An empty Karabiner-Elements window]({{ site.images }}/karabiner-empty.png)

## I've mapped <kbd>caps lock</kbd> to <kbd>esc</kbd> on my Mac

Let's start off easy: map <kbd>caps lock</kbd> to <kbd>esc</kbd>. You're probably familiar with the concept of a keyboard's home row: the row where your fingers will sit when at rest—<kbd>A</kbd> through <kbd>L</kbd>... But what is <kbd>caps lock</kbd> doing there? Who needs it, in this day and age? On the other hand, <kbd>esc</kbd>—which you use intensively, especially when working with Vim—is about as far away from the home row as you can get. Let's fix that:

![Remap Caps Lock to Escape on a Mac through Karabiner]({{ site.images }}/karabiner-caps-lock-to-escape.png)

And to teach you to actually use this instead of the original <kbd>esc</kbd> key, disable the latter:

![Disable the Escape key on a Mac through Karabiner]({{ site.images }}/karabiner-disable-escape.png)

OK, that was easy. But that was just the “Simple modifications” tab. Let's move on to the good stuff.

## Importing complex modifications into Karabiner-Elements

Karabiner stores it's current setup in `~/.config/karabiner/karabiner.json`, but edits directly in that file may get overwritten if you change settings from the interface. Better to add JSON files in the `~/.config/karabiner/assets/complex_modifications` folder instead. The easiest way to do that is through [Karabiner's complex modifications repository](https://ke-complex-modifications.pqrs.org/). One configuration that I liked from there is the one that disables all right-handed keys when holding the right <kbd>shift</kbd> key and the left-handed keys with the left <kbd>shift</kbd>, forcing you to use the proper <kbd>shift</kbd>:

![“Force the user to make key combination with two hands (rev 2)” in the Karabiner complex modifications repository]({{ site.images }}/karabiner-complex-modifications-repository.png)

Click the blue “Import” button to go straight to Karabiner:

![“Force the user to make key combination with two hands (rev 2)” imported into Karabiner]({{ site.images }}/karabiner-import-complex-modifications.png)

Et voila, you have your first complex modification set up. To get a sense of what this looks like on the inside, open the JSON file in the `~/.config/karabiner/assets/complex_modifications` folder:

```json
{
  "title": "Force the user to make key combination with two hands (rev 2)",
  "rules": [{
      "description": "Block left-handed shift + left-handed key (rev 2)",
      "manipulators": [{
          "type": "basic",
          "from": {
            "key_code": "grave_accent_and_tilde",
            "modifiers": {
              "mandatory": [
                "left_shift"
              ],
              "optional": [
                "any"
              ]
            }
          },
          "to": [{
            "key_code": "vk_none"
          }]
        },
        {
          "type": "basic",
          "from": {
            "key_code": "1",
            ...
```

## Writing your own complex modifications

You have options on how to write your complex modifications:
1. Just write them straight up into the JSON files read by Karabiner, although you'll probably get frustrated by missing curly brackets very fast;
2. Use the ERB setup in [the official complex modifications repository](https://github.com/pqrs-org/KE-complex_modifications), using `make` to convert your `.erb` to `.json`;
3. Do as I do and write them in YAML and use [a simple Python script](https://git.sr.ht/~harmtemolder/karabiner-vim-mode-plus/tree/master/yml-to-json.py) to convert these `.yml` files into `.json` files.

Compare this YAML...

```yaml
title: (Atom, iTerm, Crusader Kings II) Use function keys as function keys
maintainers:
  - harmtemolder
rules:
  - description: (Atom, iTerm, Crusader Kings II) Use function keys as function keys
    manipulators:
      - type: basic
        from:
          key_code: f1
          modifiers:
            optional:
              - any
        to:
          - key_code: f1
        conditions:
          - type: frontmost_application_if
            file_paths:
              - ck2\.app
            bundle_identifiers:
              - com.github.atom
              - com.googlecode.iterm2
      - type: basic
        from:
          key_code: f2
          ...
```

... to the resulting JSON:

```json
{
  "title": "(Atom, iTerm, Crusader Kings II) Use function keys as function keys",
  "maintainers": [
    "harmtemolder"
  ],
  "rules": [{
    "description": "(Atom, iTerm, Crusader Kings II) Use function keys as function keys",
    "manipulators": [{
        "type": "basic",
        "from": {
          "key_code": "f1",
          "modifiers": {
            "optional": [
              "any"
            ]
          }
        },
        "to": [{
          "key_code": "f1"
        }],
        "conditions": [{
          "type": "frontmost_application_if",
          "file_paths": [
            "ck2\\.app"
          ],
          "bundle_identifiers": [
            "com.github.atom",
            "com.googlecode.iterm2"
          ]
        }]
      },
      {
        "type": "basic",
        "from": {
          "key_code": "f2",
          ...
```

If you dive into writing your own complex modifications, [the Karabiner-Elements Documentation](https://karabiner-elements.pqrs.org/docs/) will be your new best friend.

## Add my Vim Mode Plus to your Karabiner

1. Import my vim_mode_plus.json through this link: [karabiner://karabiner/assets/complex_modifications/import?url=https://git.sr.ht/~harmtemolder/karabiner-vim-mode-plus/blob/master/vim_mode_plus.json](karabiner://karabiner/assets/complex_modifications/import?url=https://git.sr.ht/~harmtemolder/karabiner-vim-mode-plus/blob/master/vim_mode_plus.json)
1. Open the “Complex modifications” tab of your Karabiner preferences
1. Click “Add rule”
1. Click “Enable All” next to “Vim Mode Plus”:

![Import Vim Mode Plus into Karabiner-Elements]({{ site.images }}/karabiner-import-vim-mode-plus.png)

You now have Vim's navigation wherever you want—except in Atom, iTerm and a couple of other apps, because I use those apps' own Vim modes instead. Feel free to play around in my JSON to add or remove apps from the exceptions. With my Vim Mode Plus you now have these powers at your fingertips:

<kbd>caps lock</kbd> switches to `NORMAL` mode, where you:

1. Navigate using <kbd>H</kbd>, <kbd>J</kbd>, <kbd>K</kbd>, <kbd>L</kbd>, <kbd>E</kbd>, <kbd>B</kbd>, <kbd>0</kbd>, <kbd>shift</kbd>+<kbd>^</kbd>, <kbd>shift</kbd>+<kbd>$</kbd>, <kbd>G</kbd><kbd>G</kbd>, <kbd>shift</kbd>+<kbd>G</kbd>, <kbd>shift</kbd>+<kbd>{</kbd>, <kbd>shift</kbd>+<kbd>}</kbd> (check [your favorite Vim Cheat Sheet](https://vimsheet.com/) to see what each one does)
1. Cut, copy and change using <kbd>D</kbd>, <kbd>Y</kbd> and <kbd>C</kbd> respectively, followed by a direction (<kbd>E</kbd>, <kbd>B</kbd>, <kbd>0</kbd>, <kbd>shift</kbd>+<kbd>^</kbd>, <kbd>shift</kbd>+<kbd>$</kbd>, <kbd>G</kbd><kbd>G</kbd>, <kbd>shift</kbd>+<kbd>G</kbd>, <kbd>shift</kbd>+<kbd>{</kbd>, <kbd>shift</kbd>+<kbd>}</kbd>). <kbd>D</kbd><kbd>D</kbd>, <kbd>Y</kbd><kbd>Y</kbd> and <kbd>C</kbd><kbd>C</kbd> cut, copy and change the current line
1. Delete 1 character forward and back with <kbd>X</kbd> and <kbd>shift</kbd>+<kbd>X</kbd> respectively
1. Paste the clipboard with <kbd>P</kbd>
1. Undo and redo with <kbd>U</kbd> and <kbd>ctrl</kbd>+<kbd>R</kbd> respectively
1. Switch to `VISUAL` mode with <kbd>V</kbd>, in which you can select text with the navigation keys. Press <kbd>V</kbd> again to go back to `NORMAL` mode or <kbd>D</kbd>, <kbd>Y</kbd>, <kbd>C</kbd> or <kbd>X</kbd> to cut, copy, change or delete the selection.

To leave `NORMAL` mode you can use any of these:
* <kbd>I</kbd> and <kbd>A</kbd> leave `NORMAL` mode at the cursor
* <kbd>shift</kbd>+<kbd>I</kbd> leaves `NORMAL` mode at the beginning of the line
* <kbd>shift</kbd>+<kbd>A</kbd> leaves `NORMAL` mode at the end of the line
* <kbd>O</kbd> and <kbd>shift</kbd>+<kbd>O</kbd> leave normal mode on a new line below and above the current line respectively
* <kbd>caps lock</kbd>, <kbd>esc</kbd> and <kbd>control</kbd>+<kbd>[</kbd> leave `NORMAL` mode
* I've also added a couple of other interactions that leave `NORMAL` mode, to avoid confusion when switching apps:
  * When you switch to iTerm, Atom or any of my other pre-defined apps (because those have their own Vim modes)
  * When you have fingers on your trackpad and press a key (i.e. you don't have both hands on your keyboard)
  * When you click a mouse button (although my MacBook does not support this for the built-in trackpad)

Seeing that I keep tweaking my Karabiner setup, there might be even more when you read this. I'll try to keep this list up-to-date though.

(If you want to easily switch Vim Mode Plus on and off, you can add a profile in the “Profiles” tab without any modifications.)
