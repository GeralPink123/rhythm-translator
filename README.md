# Rhythm Translator

Paste a rhythm pattern in one genre. Get it back in another genre. With rules you can read.

There is no tool that does this. Search GitHub for "drum pattern translator" and you get zero results. The topic is empty. Everything close to it is a neural network that will not tell you why it changed a note, or a generator that will not take your pattern at all.

This one is different on purpose. No AI in the cloud. No black box. Every translation is a set of rules you can read, edit, and argue with. Free, in the browser, no account, nothing to install.

## Where it is now

This is early. What exists today:

- The paper, in Spanish: [PAPER.md](PAPER.md). It explains the gap, the rules, and what is still missing. Written by the author, in first person.
- A working demo: [demo/index.html](demo/index.html). Trap at 140 BPM translated to dembow at 110 BPM. One file, no dependencies, sound made in the browser. The author has not listened to it yet with a fresh ear, so nobody is claiming it sounds right.

## What it will do

You paste or draw a pattern, pick a destination genre, and get the pattern translated, with a plain-language explanation of every change. Dembow, salsa, plena, bachata, merengue, cumbia, bomba, and more. Grids change with the genre, 16 steps in 4/4 and 12 in 6/8.

## Why Latin genres first

Most rhythm tools stop at rock, house, and trap. The genres this project starts with are the ones no tool covers as a destination: dembow, salsa, plena, bomba, bachata, merengue, cumbia, soca. The rules come from real grooves, checked against real players, not from a dataset.

---

By Geral Pink, from Puerto Rico. I build free micro apps. This one is open because I want it to leave a mark, not just to work.
