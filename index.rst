.. vb_index documentation master file, created by
   sphinx-quickstart on Wed Dec  8 16:15:51 2021.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.


*****************************************
**Welcome to VB Index's documentation!**
*****************************************

.. contents:: 
====================================

.. toctree::
   :maxdepth: 2
   :caption: Contents:

**Vogt-Bailey index toolbox in Python**
#######################################

**Installation**
****************
It is possible to simply copy the folder vb_toobox to your project folder and proceed from there. If this is the case, be sure you have the following packages installed:

1. multiprocess
2. nibabel
3. numpy
4. scipy
5. pillow
6. psutil

The preferred way to install is through pip. It is as easy as

``pip install vb_toolbox``

If your pip is properly configured, you can now use the program vb_tool from your command line, and import any of the submodules in the vb_toolbox in your python interpreter.

**Usage of vb_tool CLI**
************************

If VBIndex was installed via pip, the command line program vb_tool should be available in your terminal. You can test if the program is correctly installed by typing

``vb_tool -h``

In your terminal, if you see the following output, the program has been properly installed.

``usage: vb_tool [-h] [-j N] [-n norm] [-fb] [-m file] [-c file] -s file -d file -o file``

Calculate the Vogt-Bailey index of a dataset. For more information, check
https://github.com/VBIndex/py_vb_toolbox.

**Optional Arguments:**
  -h, --help            show this help message and exit
  -j N, --jobs N        Maximum number of jobs to be used. If absent, one job
                        per CPU will be spawned
  -n norm, --norm norm  Laplacian normalization to be used. Possibilities are
                        "geig", "unnorm", "rw" and "sym". Defaults to geig.
  -fb, --full-brain     Calculate full brain feature gradient analyses.
  -m file, --mask file  File containing the labels to identify the cortex,
                        rather than the medial brain structures. This flag
                        must be set for normal analyses and full brain
                        analyses.
  -c file, --clusters file
                        File containing the surface clusters. Cluster with
                        index 0 are expected to denote the medial brain
                        structures and will be ignored.

**Required Named Arguments:**
  -s file, --surface file
                        File containing the surface mesh
  -d file, --data file  File containing the data over the surface
  -o file, --output file
                        Base name for the output files

**Authors**
**************
Lucas da Costa Campos (*lqccampos (at) gmail.com*) and Claude J Bajada
(*claude.bajada (at) um.edu.mt*).

**Copyright**
**************
This program is free software: you can redistribute it and/or modify it under
the terms of the GNU General Public License as published by the Free Software
Foundation, either version 3 of the License, or (at your option) any later
version.

This program is distributed in the hope that it will be useful, but WITHOUT
ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS
FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with
this program. If not, see <https://www.gnu.org/licenses>.
If you copied the program source code, the executable is found in vb_toolbox/app.py. You can test the program using

``python3 vb_toolbox/app.py``
which should yield the results shown above.

There are three main uses for the vb_tool

1. Searchlight analyses
2. Whole brain feature gradient analyses
3. Feature gradient analyses in a specified set of regions of interest

**Searchlight analyses**

The per vertex VB-index analyses can be carried with the following command

``vb_tool --surface input_data/surface.surf.gii  --data input_data/data.func.gii --mask input_data/cortical_mask.shape.gii --output search_light``

The number of vertices in the surface mesh must match the number of entries in the data and in the mask.

The cortical mask must contain a logical array, with True values in the region on which the analyses will be carried out, and False in the regions to be left out. This is most commonly used to mask out midbrain structures which would otherwise influence the analysis of the cortical regions.

**Whole brain analyses**

To perform full brain feature gradient analyses and the associated VB-index, the flag -fb or --full-brain must be set. Otherwise, the flags are the same as in the searchlight analysis.

``vb_tool --surface input_data/surface.surf.gii  --data input_data/data.func.gii --mask input_data/cortical_mask.shape.gii --full-brain --output full_brain_gradient``

Be warned, however, that this analysis can take long, use a large amount of RAM. In systems with 32k vertices, upwards of 30GB of RAM were used.

**Regions of Interest analyses**

Sometimes, one is interested only in a small set of ROIs. In this case, the feature gradient maps and the associated VB-index value for each ROI will be extracted. The way of calling the program is as follows:

``vb_tool --surface input_data/surface.surf.gii  --data input_data/data.func.gii  -c input_data/clusters.shape.gii --output clustered_analyses``

The cluster file works similarly to the cortical mask in the previous modalities. However, its structure is slightly different. Instead of an array of logical values, the file must contain an array of integers, where each integer corresponds to a different cluster. The 0th cluster is special, and denotes an area which will not be analyzed. In these regards, it has a similar use to the cortical mask.

**Usage of vb_tool GUI**

The VB toolbox can be run through a graphical interface. To do this, simply call If you have installed the software through pip, then all that needs to be done is to run the following command:

``vb_gui``

**General Notes**

**Note on the data file**

``vb_tool`` can handle two separate cases. If there is a single structure in the file, ``vb_tool`` will read it as a matrix on which each row relates to each vertex. If there are two or more structures, it will read them as a series of column vectors, on which each entry relates to a vertex. It will then coalesce them into a single matrix, and run the analyses of all quantities concurrently.

**Notes on parallelism**

``vb_tool`` uses a high level of parallelism. How many threads are spawned by vb_tool itself can be controlled using the ``-j/--jobs`` flag. By default, it will try to use all the CPUs in your computer at the same time to perform the analyzes. Depending on the BLAS installation in your computer, this might not be the best fastest approach, but rarely will be the slowest. If you are unsure, leave the number of jobs at the default level.

Due to job structure of the ``vb_tool``, the level of parallelism it can achieve on its own depends on the specific analyses being carried out.

**Searchlight analyses**: High level of parallelism. Will spawn as many jobs are there are CPUs
**Whole brain analyses**: Low lever of parallelism. Will only spawn one job
**Region of Interest analyses**: Medium level of parallelism. Will spawn as many jobs as there are ROIs, or number of CPUS, whichever is the lowest.
Specially in the whole brain analyses, having a well optimized BLAS installation will grandly accelerate the process, and allow for a further paralelism. Both MKL and OpenBLAS have been shown to offer fast analyses. If you are using the Anaconda distribution, you will have a good BLAS pre-configured.

**Developer Information**
*************************
**Build**

The following information is only useful for individuals who are actively contributing to the program.

We use setuptool and wheel to build the distribution code. The process is described next. More information can be found here.

Be sure that setuptools, twine, and wheel are up-to-dated
python3 -m pip install --user --upgrade setuptools wheel twine
Run the build command
python3 setup.py sdist bdist_wheel
Upload the package to pip
python3 -m twine upload dist/*


