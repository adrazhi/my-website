---
layout: post
title: An example of use of the Gengetopt
---

[Gengetopt](http://www.gnu.org/software/gengetopt/gengetopt.html) is a tool to generate C code to parse the command line arguments argc and argv that are part of every C or C++ program.

The command line options, which have to be handled by gengetopt generated function, are specified in a file (typically with .ggo extension).

This post contains an example of use of the "modes options". Please pay attention that options belonging to a mode are considered in mutual exclusion with options of a different mode.

{% gist 636eb4cc6281179ca26f  %}
