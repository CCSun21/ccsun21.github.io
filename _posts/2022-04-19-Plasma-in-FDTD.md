---
layout: post
title:  "Drude model in FDTD solutions"
date:   2022-04-19 18:12:58 +0800
categories: [FDTD]
---

# Drude model in FDTD solutions
[Plasma(Drude)](https://support.lumerical.com/hc/en-us/articles/360034394634-Standard-optical-permittivity-material-models-in-FDTD-and-MODE#:~:text=model%20described%20below.-,Plasma%20(Drude),-The%20Plasma%20model)

The Plasma model is used to create a material with the the following relative permittivity.
$$
\varepsilon_{total}(f)=\varepsilon-\frac{\omega_p^2}{2\pi \cdot f(i\nu_c+2\pi \cdot f)}
$$

- $\varepsilon$ : permittivity
- $\omega_p$ : plasma resonance in units of rad/s
- $\nu_c$ : plasma collision in units of rad/s


## Drude model
The dielectric function:
$$
\varepsilon(\omega)=\varepsilon_1(\omega)+i\varepsilon_2(\omega)
$$
where:
$$
\varepsilon_1 = \varepsilon_\infty - \frac{\omega_p^2}{\omega^2+\gamma^2}
$$
$$
\varepsilon_2 = \frac{\omega_p^2 \gamma}{\omega(\omega^2+\gamma^2)}
$$
In where:
$\varepsilon_\infty$ is the high frequency dielectric permittivity, and $\omega_p$ is the plasma frequency, and $\gamma$ is the plasma damping.

## Deformation of Drude model equation in FDTD

$$
\begin{aligned}
    \varepsilon_{total}(f) & =\varepsilon-\frac{\omega_p^2}{2\pi \cdot f(i\nu_c+2\pi \cdot f)}\\ & = \varepsilon-\frac{\omega_p^2}{2\pi \cdot f}\cdot \frac{1}{i\nu_c+2\pi \cdot f}\\ & = \varepsilon-\frac{\omega_p^2}{2\pi \cdot f}\cdot \frac{i\nu_c-2\pi \cdot f}{-\nu_c^2-4\pi^2\cdot f^2}\\ & = \varepsilon-\frac{\omega_p^2}{2\pi \cdot f}\cdot \frac{-2\pi \cdot f}{-\nu_c^2-4\pi^2\cdot f^2}-\frac{\omega_p^2}{2\pi \cdot f}\cdot \frac{i\nu_c}{-\nu_c^2-4\pi^2\cdot f^2}\\ & = \varepsilon - \frac{\omega_p^2}{\nu_c^2+4\pi^2\cdot f^2}+i\frac{\omega_p^2 \nu_c}{2\pi\cdot f(\nu_c^2+4\pi^2\cdot f^2)}
\end{aligned}
$$

## Drude model and Drude model in FDTD
I.e.: 
$$
    \varepsilon_{total}(f)=\varepsilon - \frac{\omega_p^2}{4\pi^2\cdot f^2+\nu_c^2}+i\frac{\omega_p^2 \nu_c}{2\pi\cdot f(4\pi^2\cdot f^2+\nu_c^2)} \tag{1}
$$

$$
    \varepsilon(\omega)=\varepsilon_\infty - \frac{\omega_p^2}{\omega^2+\gamma^2}+i\frac{\omega_p^2 \gamma}{\omega(\omega^2+\gamma^2)} \tag{2}
$$

compared (1) and (2), we will get that:
- Permittivity $\varepsilon$ in FDTD equals to the $\varepsilon_\infty$ in Drude model.
- Plasma frequency $\omega_p$ (unit: rad/s) in FDTD equals to the $\omega_p / 2\pi$ in Drude model.
- Plasma collision $\nu_c$ (unit: rad/s) in FDTD equals to the $\gamma$ in Drude model.
- Frequency $f$ (unit: rad/s) in FDTD equals to the $\omega/2\pi$ in Drude model.