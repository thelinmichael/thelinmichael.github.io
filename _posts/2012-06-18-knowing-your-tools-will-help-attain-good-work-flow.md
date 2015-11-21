---
layout: post
title: Knowing your tools will help attain good work flow
date: 2012-06-18 20:29:55.000000000 +02:00
type: post
published: true
status: publish
categories:
- Agile
- Tools
tags: []
---
The most obvious benefit of knowing your tools is that you save time. It also makes it easier to keep a good work flow, especially if you're working as a pair. It is understandable that the pair navigator has a harder time to focus on the task at hand if the driver is clicking around in menus instead of using key short-cuts.

So here are a few things that I find very helpful, both when working alone or when acting as a navigator in a pair programming session.

**General environment short-cuts**

*   **Ctrl + ← and Ctrl + →    **Making the cursor jump between words works in most environments I know of, and is applicable everywhere. After starting to use this, I tend to use the mouse much less. Ctrl + Delete and Ctrl + Backspace are useful as well.

**IDE short-cuts**

A lot of the functionality below can be reached via right-clicking on the code editing window. Still, using the short-cuts is something everyone should move towards..

Short-cut values are Eclipse IDE defaults.

*   **Remove line**. Ctrl + D. Probably the short-cut I use the most.
*   **Create and jump to the next line**. Shift + Return. Saves a fraction of a second if you're in the middle of a line. Very similarly, Ctrl + Return pushes down the text on the current line .
*   **Search for resources and types**.  Ctrl + Shift + R. You can definitely do fine without removing lines quickly, but searching for resources (or restricted to type -- Ctrl + Shift + T), you can't.
*   **Copy lines**. Ctrl + Alt + Down/Up. Both this one, and move line below, is hugely convenient during refactoring.
*   **Move lines**. Alt + Down/Up.
*   **Move to next compilation error or warning**. Ctrl + . (dot). Not as important, but helpful. When you're editing a file that is larger than it probably should be.
*   **Run test**. While in test window,  Shift + Alt + X (release keys) + T. Probably the  most complicated, and one that is rarely used in my experience. Still helpful. Pressing J instead of T runs the file as a Java Application.
*   **Refactor**. Shift + Alt + R. Can be used on filenames, classnames and all members, and saves a lot of name refactoring grief.
*   **Correct indentation**. Ctrl + I. Corrects the indentation on selected lines.
*   **See where method is called**. Ctrl + Alt + H. Very good to have around when getting to know someone else's code (or maybe your own if it was some time ago).
*   **See declaration of type or member**.  F3\. Basically jumps to the top of chosen type or class member. Extremely helpful.
*   **Type hierarchy**. F4.

You can bind short-cuts to everything you can imagine, just check out _Windows > Preferences > General > Keys_.