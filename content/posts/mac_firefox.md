---
title: "Configuring my new Mac II: Firefox"
date: 2021-01-30
draft: false
tags: ["programming", "technology"]
---
# Introduction
For most users, the web browser connects the computer to _everything else_. For compatibility, availability, and reliability, more and more apps are moving into the web browser. You, or at least I, send messages, edit documents, and watch videos from a browser.
# Choosing a browser
Unless you have a niche use case, most browsers will work well enough. In practical terms, it suffices to only consider switching browsers when you notice something wrong with your browser. Otherwise, you might switch browsers if you discover some features you didn't know about before. For the latter,[^1] I've compiled a list of features/extensions not built into browsers.
[^1]: As well as to spread the word on useful extensions
# Tab manager - [Tree Style Tab](https://addons.mozilla.org/en-US/firefox/addon/tree-style-tab/)
Somewhat embarrassingly, I use Firefox not because of some thorough use case analysis. Instead, I vastly prefer the presentation of this tab manager Firefox extension to its equivalents in other browsers. No matter the browser you use, however, consider a tab manager which: 
1. Grows vertically - you read horizontally, so for readability your tabs should grow in a different direction
2. Groups hierarchically - you use a browser for many purposes, your tabs should group based on those purposes

All tabs managers I've seen base their hierarchy off how you open tabs: opening a new tab from Tab T spawns a child tab of Tab T. By using behavior to approximate a tab's purpose, these hierarchies pretty accurately group tabs by purpose without user intervention.
# Password[^1] Manager - [LastPass](https://www.lastpass.com/)
You should use a password manager. Once again: you should use a password manager.
[^1]: Some (generally security-minded) others and I will use the term "passphrase" to encourage longer, easier-to-remember credentials.

A password manager has more convenience and security than your memory. After the initial hump of setting up a password manager, the password manager fills in logins and generates secure new passwords. A password manager also makes it feasible to have unique passwords for each of your accounts, so if one gets hacked the rest stay safe. 

If you have concerns about password leaks, know that any password manager worth its salt will use your password manager's password to encrypt your other passwords. Therefore, without knowing/guessing your password manager's password, no one can decrypt your stored passwords.

Consider the hour or so you spend setting up a password manager an investment in your long-term convenience and security.
# Keyboard shortcuts - [Vim Vixen](https://github.com/ueokande/vim-vixen)
Think of the basic actions you do frequently in your browser, such as scrolling, opening links in new tabs, and closing tabs. For efficiency, if you haven't already, consider making them keyboard shortcuts.
# Video speed controller - [Video Speed Controller](https://addons.mozilla.org/en-US/firefox/addon/videospeed/)
If you watch videos on websites which lack speed controls, an extension can do that for you. I used to watch videos at 2.5x speed; now I only watch them at 1.5x to 2x speed.
# Conclusion
Extensions can significantly upgrade your browser experience. Once in a while, perhaps each time you get a new computer, shop around for useful extensions.
