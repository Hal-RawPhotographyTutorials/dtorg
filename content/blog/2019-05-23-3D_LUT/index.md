---
title: "A new module: lut3d"
author: phw
slug: New module-lut3d
date: 2019-05-23
lede: CollegialeSaint-Thiebaut.jpg
lede_author: <a href="http://ph-wd.com/piwigo/">Philippe Weyland</a>
tags:
  - announcement
aliases:
    - /2019/05/new-module-lut3d/  
---
The *lut3d* module will be introduced in darktable 3.0 and is designed to apply a *3D LUT* (LookUp Table) to an image.

##3D LUT
A *3D LUT* is a tridimensional table which allows to transform any RGB value into another RGB value.
The most common applications of LUTs are film simulation and color grading. But they can be used for any other technical transforms like LOG to REC.709, which are used in video edition for example.

The module accepts *cube* files, *haldclut* files and *compressed LUT* files.

###*Cube* files
*Cube* files are used by video editors and colorists. They are text files with the *cube* extension.
You can find such files through these links:

- [free-film-luts-for-editors-dits-and-colorists](https://jonnyelwyn.co.uk/film-and-video-editing/free-film-luts-for-editors-dits-and-colorists/)
- [print-film-emulation-luts-for-download](http://juanmelara.com.au/blog/print-film-emulation-luts-for-download)

###*Haldclut* files
*Haldclut* files are used in Rawtherapee and PhotoFlow.
The are usually coded as a png image.
Smaller than *cube* files, they are also more accurate (see **LUT dimension** below).
Here are some places to find them:

- [http://rawtherapee.com/shared/HaldCLUT.zip](http://rawtherapee.com/shared/HaldCLUT.zip)
- [http://gmic.eu/color_presets/index.shtml](http://gmic.eu/color_presets/index.shtml)
- [https://freshluts.com/](https://freshluts.com/)

###*Compressed LUT* files
*Compressed LUT* files have been created by the G'MIC team (see [Pixls.us - CLUT compression](https://discuss.pixls.us/t/clut-compression/11752)) which offers a ready to use library (gmic_cluts.gmz).

The compressed luts are actually 100 times smaller than *haldclut* files.
This small size allows dt to save the image transformation in its database and/or in the image xmp file.
A gmz file can hold multiple compressed luts.
Decompression time is mitigated using cache files (which can be shared with G'MIC).
See below some useful scripts to manage your compressed LUT library.

Note: except for compressed LUT, due to the size of the LUT file, LUT data are not saved with the parameters of the image. Instead the file path (inside the root folder) is saved. Then you need to backup properly the LUT files (+ folders) you have used.

> CAUTION: dt understands *Compressed LUT* files only if G'MIC library is installed on your machine.

---
##The module itself
Before using the module you must define the folder where you have stored the LUTs you like. This folder can have sub-folders. This is done selecting the correct *3D lut root folder* in *Configuration/Core options/Miscellaneous*.

{{< center >}}

![lut3d configuration folder](lut3d-configuration.png "lut3d configuration")

{{< /center >}}

Lut3d has three parameters:

* LUT file
* application color space
* interpolation

{{< center >}}

![lut3d module](lut3d-module.png "lut3d module")

{{< /center >}}

###LUT file
The different types of source file are described above.

The important point to remember is the difference between compressed and not compressed files.
If compressed, the LUT itself is saved (compressed) with the parameters of the image, and in the xmp file if activated as well.

Otherwise the parameters only hold the file name and path (relative to *3D lut root folder*).
As a consequence the module cannot recalculate the image without original LUT file.

###Color space
Each 3D LUT is built to be applied on a given color space. The module lut3d lets you select the appropriated one.
Usually *cube* files are built for REC.709 and *haldclut* for sRGB.

Note: The color space selection is effective when lut3d module is placed between input color profile and output color profile modules.
Otherwise the selected color profile is ignored, and the LUT is applied on the current profile in the pipeline.

###Interpolation
The interpolation method defines the way to calculate colors which are not exactly on a node of the RGB cube (described by the LUT).

There are three interpolation methods available: tetrahedral (the default one), trilinear and pyramid.
Usually you don't see the difference between the interpolation method except sometimes with small size LUTs.

**Cube dimension**: The RGB cube is characterized by its dimensions. It’s always below 256.
The larger it is, the more accurate the transform is.
It seems that above 48x48x48 one cannot see the difference any more.

Usually *haldclut* cubes (64x64x64 is common) are larger than *cube* cubes (33x33x33 is common).

{{< center >}}

![lut3d example](CollegialeSaint-Thiebaut-2.jpg "Applying the lut 'fuji_neopan_1600.png'")

{{< /center >}}
---
##G'MIC commands.
G'MIC offers some useful commands to manipulate LUT files.
To be able to run these commands you must have G'MIC installed on your machine.

See [https://gmic.eu/](https://gmic.eu/).

###*Cube* to *Haldclut*
Be careful, as an *haldclut* file is an image, the square of the image width must be equal to the cube of the cube size.
The below command first resizes the input cube and then transforms the result into a flat image.

`gmic -input_cube <filename1>.cube -r 64,64,64,3,3 -r 512,512,1,3,-1 -o <filename2>.png`

###Compression
Compression is a process which takes time.

* To compress an *haldclut* file:

`gmic -i <filename>.png -compress_clut 1.25,0.75,2048 -o <filename>.gmz`

* To compress a *cube* file:

`gmic -input_cube <filename>.cube -compress_clut 1.25,0.75,2048 -o <filename>.gmz`

* To add a *Compressed LUT* to a gmz library (the order is important):

`gmic -i <filename>.gmz -i <libraryname>.gmz -o <libraryname>.gmz`

* To remove a *Compressed LUT* from a gmz library:

`gmic -i <libraryname>.gmz -remove[<lutname>] -o <libraryname>.gmz`

# About this article

This article is licensed under the terms of the [Attribution 2.0
Generic (CC BY 2.0)](https://creativecommons.org/licenses/by/2.0/),
or, at your option, the [Creative Commons BY-NC-SA 3.0
License](https://creativecommons.org/licenses/by-nc-sa/3.0/).

**Contributors:** [phweyland](https://github.com/phweyland), [rabauke](https://github.com/rabauke).
