Installation
============

Obtaining the code
~~~~~~~~~~~~~~~~~~

BeAR is hosted on the NewStrangeWorlds GitHub Teams page:
https://github.com/newstrangeworlds/bear. If ``git`` is available,
the repository can be simply cloned with

.. code:: bash

   git clone https://github.com/newstrangeworlds/bear

Prerequisites
~~~~~~~~~~~~~

BeAR is written in C++ and CUDA. It uses features of the C++17
standard and, therefore, requires a compiler that implements this
standard. Furthermore, it needs the CUDA compiler and framework
from NVIDIA.
Additional libraries and codes
(FastChem 4, MultiNest, DisORT++, the Adding-Doubling radiative
transfer library, Eigen, Boost Math, toml++, and pybind11)
will be downloaded from their respective repositories and be compiled as well.

The complete list of prerequisites for a ``CMake`` installation
is:

-  a C++ compiler (e.g. ``g++``)

-  NVIDIA's CUDA framework and drivers

- ``CMake``, at least version 3.15

-  an OpenMP library

- ``Python`` 3 with development headers (BeAR is built as a Python
  extension module)

The C++ compiler will be detected by the CMake script when it
generates the make files. The codes and libraries downloaded and compiled during
the installation of BeAR have additional requirements.
This includes, in particular,

- a FORTRAN compiler (e.g. ``gfortran``) for the MultiNest library

- the BLAS and LAPACK linear algebra libraries for MultiNest (e.g. ``OpenBLAS``);
  ``LAPACK`` is located by CMake via ``find_package(LAPACK REQUIRED)``

BeAR is used through its Python interface, which allows the forward model
and retrieval calculations to be performed in Python. The interface additionally requires

 - Python 3.9 or higher
 - the PyMultiNest package, which drives the MultiNest sampler that BeAR builds as a
   shared library (``libmultinest.so``). Alternative Python samplers such as
   dynesty are also supported.

 Some combinations of the CUDA, g++, and Python compiler versions seem to produce issues during either
 the compilation or when executing the code. Symptoms include, for example, segmentation faults when 
 the BeAR C++ code is called from within Python. This seems to be caused by compatibility issues within 
 the PyBind11 library and are, thus, not directly related to BeAR and also not fixable within BeAR itself.
 The following combinations have been tested and are known to work with the PyBind11 version used in BeAR:

- g++/gcc/gfortran 12.3
- CUDA 12.0
- Python 3.10


.. _sec:install_config:

Configuration and compilation of BeAR with CMake
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

| Before BeAR can be compiled, ``CMake`` is required to
  configure the compilation files, locate libraries, and write the
  make files that will perform the actual compilations. If required
  libraries are missing, ``CMake`` will report a corresponding error
  message. In this case, the missing libraries or compilers need to be
  installed before the configuration can be completed.
| To run the ``CMake`` configuration, first create the ``build`` folder
  inside the BeAR source code folder and switch to the folder:

.. code:: bash

   mkdir build
   cd build

Within the folder run ``CMake``:

.. code:: bash

   cmake ..

BeAR is built as a Python extension module, so the Python interface is compiled by
default (the ``USE_PYTHON`` option is ``ON``). After ``CMake`` successfully configured
the compilation files, BeAR can be compiled by running:

.. code:: bash

   make

Upon successful compilation, the compiled Python module named ``bear`` is placed in
the ``python/lib`` folder of the BeAR root directory, together with the MultiNest
shared library ``libmultinest.so`` and a small auto-generated helper file
``bear_multinest_path.py`` that records its location. There is no longer a stand-alone
C++ executable; all functionality is accessed through the Python interface.


Running a retrieval
~~~~~~~~~~~~~~~~~~~~~

A retrieval is run through a small Python driver script located in the retrieval
folder. The provided examples use ``run_multinest.py``, which loads
``libmultinest.so``, builds the ``bear.Config``, ``bear.Retrieval``, and
``bear.PostProcess`` objects, and hands the prior and likelihood callbacks to
``pymultinest.run(...)``. To start such a retrieval, run the script from within the
retrieval folder:

.. code:: bash

   python run_multinest.py

An equivalent example driver based on the dynesty sampler (``run_dynesty.py``) is also
provided. The sampler output files are written to the folder configured in
``retrieval.toml``.