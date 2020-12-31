---
layout: post
title:  "Git kommandoer"
date:   2020-12-31 12:00:00 +0200
categories: git
tags: [git]
excerpt_separator: <!--more-->
---
Note to self... Jeg glemmer konstant at fjerne de latterlige .DS_Store filer og så skal jeg senere fjerne dem og hver eneste gang, har jeg glemt hvad jeg gjorde sidste gang og må i gang med Google lortet.

Så nu skriver jeg dem ned her, så jeg har dem til næste gang.

## Fjern eksisterende fil fra repo
```
echo ".DS_Store" >> .gitignore
git add .
git commit -m "Hello Kitty"
git rm -r --cached .
git add .
git commit -m "Hello Kitty"
git push
```
