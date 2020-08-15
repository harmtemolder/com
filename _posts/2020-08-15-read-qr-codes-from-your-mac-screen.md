---
layout: post
title: Read QR codes from your Mac's screen straight into your terminal
date: 2020-08-15 15:15:28 -0400  # use get-date in your terminal
description: This simple shell alias will let you scan QR codes without having to grab your phone
img: qr-code-in-iterm.png
img-caption: A QR code displayed in an iTerm2 window
tags: [Mac, Terminal, QR]
categories: [Mac]
---
A lot of tweaks to my Mac start with the question “How can I do this more efficiently?”. And then I spend an afternoon setting something up that will only shave a couple of minutes off my workflow. But now that I've figured this one out, it should only cost you a minute or so to set it up:

## `zbarimg`

Install `zbarimg` to read QR codes from image files from your command line:

```shell
brew install zbarimg
```

(If you don't have Homebrew installed, [do so first](https://docs.brew.sh/Installation).)

From now on, you can read QR codes (and other types of bar codes) from image files. For example:

```shell
wget https://upload.wikimedia.org/wikipedia/commons/8/8d/QR_Code_Damaged.jpg
zbarimg QR_Code_Damaged.jpg
```

will return:

```shell
QR-Code:http://en.m.wikipedia.org
scanned 1 barcode symbols from 1 images in 0.33 seconds
```

## `screencapture`

The `screencapture` command should already be part of your mac, so no need to install anything here. See [the screencapture man page](https://ss64.com/osx/screencapture.html) for more info on its parameters and some examples.

## `screencapture && zbarimg`

So, together, these two commands let you capture your screen to a file and then read a QR code from that file:

```shell
screencapture -i $TMPDIR/screencapture.bmp && zbarimg -q --raw $TMPDIR/screencapture.bmp
```

* `screencapture`'s `-i` parameter tells it to run in interactive mode, letting you select  a portion of the screen;
* `zbarimg`'s `-q` parameter tells it to not output any extra lines of information;
* and `--raw` tells it to omit the symbology prefix as well.

I've added this as alias to my `.zshrc`:

```shell
alias read-qr='screencapture -i $TMPDIR/screencapture.bmp && zbarimg -q --raw $TMPDIR/screencapture.bmp; shred -u $TMPDIR/screencapture.bmp'
```

As you can see I've added a `shred` command to make sure the temporary QR code is removed after reading.

The result:

![Encoding and decoding a QR code in Mac's terminal]({{ site.images }}/qr-encode-and-read-in-iterm.gif)

And the beauty is that I can now pipe this straight into `pass-otp`:

```shell
read-qr | pass otp append example.org/example@example.org
```

Or wherever you want to use the contents of a QR code displayed on your Mac's screen.

Have fun with your new toy!
