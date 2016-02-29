---
title: Lessons for TXR
author: James P. Ascher
email: jpa4q@virginia.edu
date: Feb 29, 2016
---

# Tutorial for TXR

This is a rough draft of a tutorial for
[TXR](http://www.nongnu.org/txr/). Comments welcome and this is a work
in progress.

## Lesson 1: Basics

The metaphor that txr uses is one of input by line going to output by
line. While it's far more flexible than that, the basic mode is to
read a bunch of lines in and spit a bunch of lines out.

The basic tool for telling the program what to do are
[*directives*](http://www.nongnu.org/txr/txr-manpage.html#N-018A0A19)
which are an '@' sign followed by parentheses enclosing the
command. I.e. "@(output)" indicates a section that should write to the
stdout.

Many directives apply to the sections below them and must be closed by
an "@(end)" directive.

See the file in Lesson 1 for a "hello world" program. After installing
txr, you run it by calling "txr hello.txr".

## Lesson 2: Reading files

The language is made primarily for "munging" data by inputting a file
and outputting some variant of it, so lines that are intended to match
don't have any special directives associated with them.

The program "matchline.txr" operates on "test.csv" and merely returns
each line. txr syntax is insensitive to space between the @-sign and
the variable name or directive. If the @-sign is followed by words,
the program treats it as a variable. If it unbound, then it matches
the file being read.

Look at "man txr" and try "txr matchline.txr test.csv"; what should be
output? Try changing the spacing and see what happens. Try removing
"@(repeat)" (along with "@(end)") and see what happens.

### Part 2

The matching is intended to pull data out of some sort of
structure. Examine "first-col.txr" which *collects* the five columns of
"test.csv" into variables @col1, @col2, etc. and then *outputs* only
the values collected into @col1. Try "txr first-col.txr test.csv" and
see what happens. Can you alter the program to give the second column?
Or change the order of the columns?

## Lesson 3: Working with Real Data

The BLS provides tab-separated files of data that can be used to
understand consumer behavior, but the tables are big. This directory
includes some txr programs and the associated data to extract a
smaller table of the prices of just one commodity. Look at the files,
which are all quite similar. Try running "txr G-USAvg-extract.txr
ap.data.2.Gasoline.txt" or one of the others. What's the output? Can
you change them to get different output? Why are there extra spaces in
the txr file?

It's important to note that the @\t symbol tells txr to insert a tab
character. Also, the @{variable} syntax can be used to tell txr that
the variable ends at the closing curly bracket. This is useful---and
probably just good coding practice---because @col1, would match up to
the comma but @col1a would make a variable named "col1a" not match up
to the letter 'a'. Confusion can be avoided by consistently using the
@{} notation.

## Lesson 4: Making a command line tool

txr supports hash-bang syntax that lets you turn your script into an
executable, assuming txr is in "/usr/local/bin" (OS X brew installs it
there), this script should run by typing "data-extract.txr". Now, it
won't do anything without a data file. Try typing "./data-extract.txr
ap.data.1.HouseholdFuels.txt".

Now, look at the code, there are a few features to notice. First, @;
indicates the beginning of a comment. In an earlier version of the
script, we used @(bind) to set a year and apunum. In this version,
those lines are commented out and have no effect.

Second, note the "@/\s+/" in the matching line. The @-sign indicates
some sort of command to the processor rather than something to match
(incidentally, "@@" gives a literal @-sign) and the @/ / syntax
introduces a regular expression match, "\s" matches any whitespace
character and '+' says to match one or more whitespace
character. Essentially, this matches the trailing whitespace on the
apunum, including the tab, before matching year.

The "-f" option tells txr to pass any command line options to the
program. So, for example, type "./data-extract.txr
-Dapunum=APU000074714 -Dyear=2014 ap.data.2.Gasoline.txt" The "-D"
option passes a variable binding to the script and this returns only
that apu number and that year.
