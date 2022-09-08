---
layout: post
title:  "The DFT Process of calculating the optical properties of a material"
date:   2022-08-22 14:50:23 +0800
categories: [DFT,VASP]
---

# 1-CellOPt

To Do cell optimization here. USing PBE xc and IVDW tag to do vdw correction.

**POSCAR:**

$MoO_3$ and $WO_3$ Primecell

**KPOINTS:**

`echo -e '102\n1\n0.04\n'| vaspkit`

0.04 Kmesh-Resolved Value (in Units of 2*$\pi$/Angstrom) Monkhorst-Pack Scheme

**INCAR:**

```
Global Parameters
ISTART =  1            (Read existing wavefunction, if there)
ISPIN  =  2            (Non-Spin polarised DFT)
# ICHARG =  11         (Non-self-consistent: GGA/LDA band structures)
LREAL  = .FALSE.       (Projection operators: automatic)
ENCUT  =  520        (Cut-off energy for plane wave basis set, in eV)
PREC   =  Accurate     (Precision level)
LWAVE  = .F.        (Write WAVECAR or not)
LCHARG = .F.        (Write CHGCAR or not)
ADDGRID= .TRUE.        (Increase grid, helps GGA convergence)
# LVTOT  = .TRUE.      (Write total electrostatic potential into LOCPOT or not)
# LVHAR  = .TRUE.      (Write ionic + Hartree electrostatic potential into LOCPOT or not)
# NELECT =             (No. of electrons: charged cells, be careful)
# LPLANE = .TRUE.      (Real space distribution, supercells)
# NPAR   = 4           (Max is no. nodes, don't set for hybrids)
# NWRITE = 2           (Medium-level output)
# KPAR   = 2           (Divides k-grid into separate groups)
# NGX    = 500         (FFT grid mesh density for nice charge/potential plots)
# NGY    = 500         (FFT grid mesh density for nice charge/potential plots)
# NGZ    = 500         (FFT grid mesh density for nice charge/potential plots)
 
Lattice Relaxation
NSW    =  300          (number of ionic steps)
ISMEAR =  0            (gaussian smearing method )
SIGMA  =  0.05         (please check the width of the smearing)
IBRION =  2            (Algorithm: 0-MD, 1-Quasi-New, 2-CG)
ISIF   =  3            (optimize atomic coordinates and lattice parameters)
EDIFFG = -2E-02      (Ionic convergence, eV/A)
PREC   =  Accurate     (Precision level)
EDIFF =  1.0E-06

IVDW = 11
```

**POTCAR:**

`echo -e '103\n' | vaspkit`

PBE POTCAR

# 2-SCF

SCF calculation to get the total energy, and write WAVECAR and CHGCAR for further calculations(HSE06 and GW).

**POSCAR:**

`cp ../1-CellOPt/CONTCAR POSCAR`

`echo -e '303' | vaspkit`

vaspkit 303 to generate the KPATH and Primcell for Band calculation.

Use the Primcell as POSCAR:

`cp PRIMCELL.vasp POSCAR`

**INCAR:**

```
Global Parameters
ISTART =  1            (Read existing wavefunction, if there)
ISPIN  =  1            (Non-Spin polarised DFT)
# ICHARG =  11         (Non-self-consistent: GGA/LDA band structures)
LREAL  = .FALSE.       (Projection operators: automatic)
ENCUT  =  520        (Cut-off energy for plane wave basis set, in eV)
# PREC   =  Accurate   (Precision level: Normal or Accurate, set Accurate when perform structure lattice relaxation calculation)
LWAVE  = .TRUE.        (Write WAVECAR or not)
LCHARG = .TRUE.        (Write CHGCAR or not)
ADDGRID= .TRUE.        (Increase grid, helps GGA convergence)
LVTOT  = .TRUE.      (Write total electrostatic potential into LOCPOT or not)
LVHAR  = .TRUE.      (Write ionic + Hartree electrostatic potential into LOCPOT or not)
# NELECT =             (No. of electrons: charged cells, be careful)
# LPLANE = .TRUE.      (Real space distribution, supercells)
# NWRITE = 2           (Medium-level output)
# KPAR   = 2           (Divides k-grid into separate groups)
# NGXF    = 300        (FFT grid mesh density for nice charge/potential plots)
# NGYF    = 300        (FFT grid mesh density for nice charge/potential plots)
# NGZF    = 300        (FFT grid mesh density for nice charge/potential plots)
 
NPAR = 4

Static Calculation
ISMEAR =  0            (gaussian smearing method)
SIGMA  =  0.05         (please check the width of the smearing)
LORBIT =  11           (PAW radii for projected DOS)
NEDOS  =  2001         (DOSCAR points)
NELM   =  60           (Max electronic SCF steps)
EDIFF  =  1E-05        (SCF energy convergence, in eV)

ALGO = Exact           (Algorithm: Normal or Exact, set Exact when perform structure lattice relaxation calculation)
IVDW = 11
```

The spin polarized calculation is not executeable.

**KPOINTS:**

use KPATH.in as KPOINTS:

`cp KPATH.in KPOINTS`

**POTCAR:**

`echo -e '103\n' | vaspkit`

PBE POTCAR

**sub job**

# 3-PBE

Here to calculate the band structure and DOS using PBE functional.

Used calculated CHGCAR

`cp ../2-SCF/CHGCAR CHGCAR`

**POSCAR:**

`cp ../2-SCF/POSCAR POSCAR`

**INCAR:**

please notice that the `NGXF`, `NGYF`, `NGZF` should be the same as the SCF calculation.
The `NBANDS` Tag should be set manually, usually equals to half of the number of electrons plus half of the number of ions.

```
Global Parameters
ISTART =  0            (Read existing wavefunction, if there)
ISPIN  =  1            (Non-Spin polarised DFT)
ICHARG =  11         (Non-self-consistent: GGA/LDA band structures)
LREAL  = .FALSE.       (Projection operators: automatic)
ENCUT  =  520        (Cut-off energy for plane wave basis set, in eV)
PREC   =  Accurate   (Precision level: Normal or Accurate, set Accurate when perform structure lattice relaxation calculation)
LWAVE  = .F.        (Write WAVECAR or not)
LCHARG = .F.        (Write CHGCAR or not)
ADDGRID= .TRUE.        (Increase grid, helps GGA convergence)
LVTOT  = .TRUE.      (Write total electrostatic potential into LOCPOT or not)
LVHAR  = .TRUE.      (Write ionic + Hartree electrostatic potential into LOCPOT or not)
# NELECT =             (No. of electrons: charged cells, be careful)
# LPLANE = .TRUE.      (Real space distribution, supercells)
# NWRITE = 2           (Medium-level output)
# KPAR   = 2           (Divides k-grid into separate groups)
# NGXF    = 300        (FFT grid mesh density for nice charge/potential plots)
# NGYF    = 300        (FFT grid mesh density for nice charge/potential plots)
# NGZF    = 300        (FFT grid mesh density for nice charge/potential plots)
 
NPAR = 4

Static Calculation
ISMEAR =  0            (gaussian smearing method)
SIGMA  =  0.05         (please check the width of the smearing)
LORBIT =  11           (PAW radii for projected DOS)
NEDOS  =  2001         (DOSCAR points)
NELM   =  60           (Max electronic SCF steps)
EDIFF  =  1E-05        (SCF energy convergence, in eV)

# ALGO = Exact           (Algorithm: Normal or Exact, set Exact when perform structure lattice relaxation calculation)
IVDW = 11

Band calculation

EMAX = 20             (Max energy for plotting DOS)
EMIN = -20             (Min energy for plotting DOS)

Optical properties
LOPTICS = .TRUE.      (Calculate optical properties)
NBANDS = 96        *modifiy (Number of bands to be calculated)
# ALGO = Exact           (Algorithm: Normal or Exact, set Exact when perform structure lattice relaxation calculation)
CSHIFT = 0.1          (Shift the Fermi level)
NOMEGA = 50          (No. of points for DOS)
```

**KPOINTS:**

`cp ../2-SCF/KPOINTS KPOINTS`

`cp ../2-SCF/KPATH.in KPATH.in`

`cp ../2-SCF/HIGH_SYMMETRY_POINTS HIGH_SYMMETRY_POINTS`

**POTCAR:**

`echo -e '103\n' | vaspkit`

PBE POTCAR

**sub job**

**Maybe increase the NBANDS and retry calculation**

# 4-HSE06

Calculate the band structure and DOS and optical properties using HSE06 functional.

Use calculated CHGCAR and WAVECAR

`cp ../2-SCF/CHGCAR CHGCAR`

`cp ../2-SCF/WAVECAR WAVECAR`

**POSCAR:**

`cp ../2-SCF/POSCAR POSCAR`

**INCAR:**<br />Notice that the `NGZF` `NGYF` `NGXF` should be assigned manually.<br />`grep NGXF ../2-PBE/OUTCAR`to see the number.
```
Global Parameters
ISTART =  1            (Read existing wavefunction, if there)
ISPIN  =  1            (Non-Spin polarised DFT)
ICHARG =  11         (Non-self-consistent: GGA/LDA band structures)
LREAL  = .FALSE.       (Projection operators: automatic)
ENCUT  =  520        (Cut-off energy for plane wave basis set, in eV)
PREC   =  Accurate   (Precision level: Normal or Accurate, set Accurate when perform structure lattice relaxation calculation)
LWAVE  = .F.        (Write WAVECAR or not)
LCHARG = .F.        (Write CHGCAR or not)
ADDGRID= .TRUE.        (Increase grid, helps GGA convergence)
LVTOT  = .TRUE.      (Write total electrostatic potential into LOCPOT or not)
LVHAR  = .TRUE.      (Write ionic + Hartree electrostatic potential into LOCPOT or not)
# NELECT =             (No. of electrons: charged cells, be careful)
# LPLANE = .TRUE.      (Real space distribution, supercells)
# NWRITE = 2           (Medium-level output)
# KPAR   = 2           (Divides k-grid into separate groups)
# NGXF    = 300        (FFT grid mesh density for nice charge/potential plots)
# NGYF    = 300        (FFT grid mesh density for nice charge/potential plots)
# NGZF    = 300        (FFT grid mesh density for nice charge/potential plots)
 
NPAR = 4

Static Calculation
ISMEAR =  0            (gaussian smearing method)
SIGMA  =  0.05         (please check the width of the smearing)
LORBIT =  11           (PAW radii for projected DOS)
NEDOS  =  2001         (DOSCAR points)
NELM   =  60           (Max electronic SCF steps)
EDIFF  =  1E-05        (SCF energy convergence, in eV)

# ALGO = Exact           (Algorithm: Normal or Exact, set Exact when perform structure lattice relaxation calculation)

Van der Waals correction
IVDW = 11
VDW_S8 = 0.7220
VDW_SR = 1.2170

Band calculation
EMAX = 20             (Max energy for plotting DOS)
EMIN = -20             (Min energy for plotting DOS)

Optical properties
LOPTICS = .TRUE.      (Calculate optical properties)
NBANDS = 96
# ALGO = Exact           (Algorithm: Normal or Exact, set Exact when perform structure lattice relaxation calculation)
CSHIFT = 0.1          (Shift the Fermi level)
# NOMEGA = 96          (No. of points for DOS)

HSE06 calculations
LHFCALC = .TRUE.       (LHFCALC specifies whether Hartree-Fock/DFT hybrid functional type calculations are performed.)
HFSCREEN = 0.2         (HFSCREEN (in Å-1) specifies the range-separation parameter in range separated hybrid functionals.)
TIME = 0.3
ALGO = Damped
```

**KPOINTS:**<br />KPOINTS of HSE06 calculations should use the explicit mode of KPOINTS, wihch can be generated by vaspkit:<br />`echo -e '303\n'| vaspkit`<br />`echo -e '251\n2\n0.04\n\n0.04'| vaspkit`

**POTCAR:**

`echo -e '103\n' | vaspkit`

PBE POTCAR

**sub job**

# 5-GW

Calculate the band structure and DOS and optical properties using GW functional.<br />Refer to GW in one go:<br />[Practical guide to GW calculations - Vaspwiki](https://www.vasp.at/wiki/index.php/Practical_guide_to_GW_calculations)

**POSCAR:**

`cp ../2-SCF/POSCAR POSCAR`

**INCAR:**<br />Notice that the `NGZF` `NGYF` `NGXF` should be assigned manually.<br />`grep NGXF ../2-PBE/OUTCAR`to see the number.
```
Global Parameters
ISTART =  1            (Read existing wavefunction, if there)
ISPIN  =  1            (Non-Spin polarised DFT)
# ICHARG =  11         (Non-self-consistent: GGA/LDA band structures)
LREAL  = .FALSE.       (Projection operators: automatic)
ENCUT  =  520        (Cut-off energy for plane wave basis set, in eV)
# PREC   =  Accurate   (Precision level: Normal or Accurate, set Accurate when perform structure lattice relaxation calculation)
LWAVE  = .F.        (Write WAVECAR or not)
LCHARG = .F.        (Write CHGCAR or not)
ADDGRID= .TRUE.        (Increase grid, helps GGA convergence)
# LVTOT  = .TRUE.      (Write total electrostatic potential into LOCPOT or not)
# LVHAR  = .TRUE.      (Write ionic + Hartree electrostatic potential into LOCPOT or not)
# NELECT =             (No. of electrons: charged cells, be careful)
# LPLANE = .TRUE.      (Real space distribution, supercells)
# NWRITE = 2           (Medium-level output)
# KPAR   = 2           (Divides k-grid into separate groups)
# NGXF    = 300        (FFT grid mesh density for nice charge/potential plots)
# NGYF    = 300        (FFT grid mesh density for nice charge/potential plots)
# NGZF    = 300        (FFT grid mesh density for nice charge/potential plots)
 
Electronic Relaxation
ISMEAR =  0
SIGMA  =  0.05
EDIFF  =  1E-06

ALGO = GW0R           (GW0R: GW0R algorithm)
NELMGW = 3
LOPTICS = .TRUE.

NEDOS = 2001
LORBIT = 11
EMIN = -20
EMAX = 20

IVDW = 11
```

**KPOINTS:**

`cp ../2-SCF/KPOINTS KPOINTS`

`cp ../2-SCF/KPATH.in KPATH.in`

`cp ../2-SCF/HIGH_SYMMETRY_POINTS HIGH_SYMMETRY_POINTS`

**POTCAR:**

`echo -e '103\n' | vaspkit`

PBE POTCAR

**sub job**

# 6-BSE

Calculate the optical properties using BSE equation.

**POSCAR:**

`cp ../2-SCF/POSCAR POSCAR`

**INCAR:**<br />Notice that the `NGZF` `NGYF` `NGXF` should be assigned manually.<br />`grep NGXF ../2-PBE/OUTCAR`to see the number.
```
Global Parameters
ISTART =  1            (Read existing wavefunction, if there)
ISPIN  =  1            (Non-Spin polarised DFT)
# ICHARG =  11         (Non-self-consistent: GGA/LDA band structures)
LREAL  = .FALSE.       (Projection operators: automatic)
ENCUT  =  520        (Cut-off energy for plane wave basis set, in eV)
# PREC   =  Accurate   (Precision level: Normal or Accurate, set Accurate when perform structure lattice relaxation calculation)
LWAVE  = .F.        (Write WAVECAR or not)
LCHARG = .F.        (Write CHGCAR or not)
ADDGRID= .TRUE.        (Increase grid, helps GGA convergence)
# LVTOT  = .TRUE.      (Write total electrostatic potential into LOCPOT or not)
# LVHAR  = .TRUE.      (Write ionic + Hartree electrostatic potential into LOCPOT or not)
# NELECT =             (No. of electrons: charged cells, be careful)
# LPLANE = .TRUE.      (Real space distribution, supercells)
# NWRITE = 2           (Medium-level output)
# KPAR   = 2           (Divides k-grid into separate groups)
# NGXF    = 300        (FFT grid mesh density for nice charge/potential plots)
# NGYF    = 300        (FFT grid mesh density for nice charge/potential plots)
# NGZF    = 300        (FFT grid mesh density for nice charge/potential plots)
 
Electronic Relaxation
ISMEAR =  0
SIGMA  =  0.05
EDIFF  =  1E-05

ALGO = BSE

LOPTICS = .TRUE.
NBANDS  = 192
NBANDSO = 10
NBANDSV = 20
CSHIFT = 0.1
NOMEGA = 50

NEDOS = 2001
LORBIT = 11
EMIN = -20
EMAX = 20

IVDW = 11
```

**KPOINTS:**

`echo -e '102\n2\n0.04\n'| vaspkit`

0.04 Kmesh-Resolved Value (in Units of 2*![](https://g.yuque.com/gr/latex?%5Cpi#card=math&code=%5Cpi&id=hVUr3)/Angstrom) Gamma centered Scheme

**POTCAR:**

`echo -e '103\n' | vaspkit`

PBE POTCAR

**sub job**

?? maybe should read WAVEDER from PBE LOPTICS calculation??
