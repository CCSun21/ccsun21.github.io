---
layout: post
title: NEB Calculations in vasp
date:   2023-08-31 19:10:58 +0800
categories: [DFT]
---

## Steps

### GO for ini and fin structure

mind that IBRION = 2, and the atom index should be same.

```bash
diff ini/POSCAR fin/POSCAR
```

relax INCAR

```bash
Global Parameters
ISTART =  1            (Read existing wavefunction, if there)
ISPIN  =  1            (Non-Spin polarised DFT)
LREAL  = .FALSE.       (Projection operators: automatic)
ENCUT  =  600        (Cut-off energy for plane wave basis set, in eV)
LWAVE  = .False.        (Write WAVECAR or not)
LCHARG = .False.        (Write CHGCAR or not)
ADDGRID= .TRUE.        (Increase grid, helps GGA convergence)

Electronic Relaxation
ISMEAR =  0            (Gaussian smearing, metals:1)
SIGMA  =  0.05         (Smearing value in eV, metals:0.2)
NELM   =  90           (Max electronic SCF steps)
NELMIN =  6            (Min electronic SCF steps)
EDIFF  =  1E-05        (SCF energy convergence, in eV)
NCORE = 4

Ionic Relaxation
NSW    =  100          (Max ionic steps)
IBRION =  2            (Algorithm: 0-MD, 1-Quasi-New, 2-CG)
ISIF   =  2            (Stress/relaxation: 2-Ions, 3-Shape/Ions/V, 4-Shape/Ions)
EDIFFG = -2E-02        (Ionic convergence, eV/AA)

# Add VDW
IVDW = 11
```

for graphite, set `IVDW = 11`
`vaspkit -task 102 -kps G -kpr 0`

### Check the ini and fin structure

```bash
perl ./dist.pl ./ini/POSCAR ./fin/POSCAR
```

the structure should less than 5 A

### Interpolate images

using the `nebmake.pl` script.

```bash
perl nebmake.pl ./ini/CONTCAR ./fin/CONTCAR 1
```

### Prepare INCAR for Standard Relax

```bash
ISTART = 0
ICHARG = 2
ENCUT = 600
PREC = Medium
ISIF  = 2
LREAL = .FALSE.

ISMEAR = 0
SIGMA = 0.05
ISPIN = 2
NSW = 500
EDIFF = 1E-2
IBRION = 3
POTIM = 0
EDIFFG = -0.1

IOPT = 1
IVDW = 11
ICHAIN = 0
LCLIMB = .TRUE. 
IMAGES = 3 # the number set in nebmake
SPRING = -5
```

### Sub calculation

check the convergence with `nebef.pl`

### Results

use `nebresults.pl` to get the result

## Reference

[vasp做过渡态经验<一>](http://bbs.keinsci.com/thread-7489-1-1.html)

[vasp-vtst计算过渡态(NEB方法)具体过程 - 知乎](https://zhuanlan.zhihu.com/p/375723525)
