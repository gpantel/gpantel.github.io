---
layout: single
References:
title:  "MolPainter and MolSolvator"
date:   2020-08-16 01:00:00 -0500
categories: computational-method
---

Tools for building and solvating complex, planar molecular systems of arbitrary molecular composition and placement via painting.

![MolPainterGraphic]({{ site.url }}/assets/MolPainter/MolPainterGraphic.png)<br/>

Within this past month, I and my friend Aaron with whom is shared many shared hobbies, including Riichi Mahjong, released a GUI for "painting" complex, planal molecular systems. This lovely project went from the simple idea "What if you could draw a layer of molecules onto a lattice?" had during a morning shower to production over several weekends of collaborative work during the  COVID-19 pandemic.



With MolPainter, the user defines a "palette" of molecules defined by PDB files, each associated to a color. The user also defines "layers" defined at any point along the z-axis. These layers are made of a number of columns and rows of cells to which the user paints their palette of molecules, making a multi-layered painting, which is exported into one large PDB file. MolSolvator rapidly adds solvent to systems built with MolPainter using a three-dimensional grid, which can be faster and more simple to use than other methods which involve making random trials at off-lattice placements.



Detailed descriptions of MolPainter are available at the [PyPI page](https://pypi.org/project/MolPainter/) and on the [project GitHub](https://github.com/gpantel/MolPainter/tree/master/tutorial).



Please let us know what you do with it! We're very interested in hearing about unique applications of MolPainter beyond our somewhat simple lipid bilayer phase separation example presented in the tutorial.