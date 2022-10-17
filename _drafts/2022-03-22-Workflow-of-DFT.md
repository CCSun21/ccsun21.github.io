---
layout: post
title:  "Workflow of DFT"
date:  2022-03-22 19:53:07 +0800
categories: [post]
author: Congcong Sun
---
# Workflow of DFT
## Build Cell
To do a DFT calculation, firstly we need to build a cell.
The most common process is to use a crystal builder software such as Materials studio, VESTA et al. Or you can just start from a empty file. The Structure file including the position of atoms in VASP is POSCAR. We can start from observing the structure of POSCAR:

Here's a POSCAR temple.
```
example POSCAR
1
1 0 0
0 1 0
0 0 1
Mo
1
0.5 0.5 0.5
```

## Cell relaxation