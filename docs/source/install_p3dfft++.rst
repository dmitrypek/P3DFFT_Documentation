Installing P3DFFT++
===================
The latest version of P3DFFT++ can be found `here <https://github.com/sdsc/p3dfft.3/releases>`_. Once you have extracted the package, you must take the following steps to complete the setup:

1. Run the ``configure`` script
2. Run ``make``
3. Run ``make install``

How to compile P3DFFT++
-----------------------
P3DFFT++ uses a ``configure`` script to create ``Makefile`` for compiling the library as well as several examples. This page will step you through the process of running the ``configure`` script properly.

Run ``configure --help`` for complete list of options. For Cray XT platforms also recommended ``--enable-useeven``.

Currently the package supports four compiler suites: PGI, Intel, IBM and GNU. Some examples of compiling on several systems are given below. Users may need to customize as needed. If you wish to share more examples or to request or contribute in support for other compilers, please write to `dmitry@sdsc.edu <mailto:dmitry%40sdsc%2eedu>`_. If you give us an account on your system we will work with you to customize the installation.

.. csv-table::
        :header: "Argument", "Notes", "Description", "Example"
        :widths: auto
        :escape: '

        "``--prefix=PREFIX``", "Mandatory for users without access to ``/usr/local``", "This argument will install P3DFFT++ to ``PREFIX`` when you run ``make install``. By default, ``configure`` will install to ``/usr/local``.", "``--prefix=$HOME/local/``."
        "``--enable-gnu``, ``--enable-ibm``, ``--enable-intel``, ``--enable-pgi``, ``--enable-cray``", "Mandatory", "These arguments will prepare P3DFFT++ to be built by a specific compiler. You must only choose one option.", "``--enable-pgi``"
        "``--enable-fftw``, ``--enable-essl``", "Mandatory", "These arguments will prepare P3DFFT++ to be used with either the FFTW or ESSL library. You must only choose one option.", "``--enable-fftw``"
        "``--with-fftw-lib=FFTW_LIB``", "Mandatory if ``--enable-fftw`` is used", "This argument specifies the path location for the FFTW library's lib directory; it is mandatory if you are planning to use P3DFFT++ with the FFTW library.", "``--with-fftw-lib=$FFTW_HOME/lib``"
        "``--with-fftw-inc=FFTW_INC``", "Mandatory if ``--enable-fftw`` is used", "This argument specifies the path location for the FFTW library's include directory; it is mandatory if you are planning to use P3DFFT++ with the FFTW library.", "``--with-fftw-inc=$FFTW_HOME/include``"
        "``--enable-openmpi``", "Optional", "This argument uses the OpenMPI implementation of MPI.", "``--enable-openmpi``"
        "``--enable-estimate``", "Optional, use only with ``--enable-fftw``", "If this argument is passed, the FFTW library will not use run-time tuning to select the fastest algorithm for computing FFTs.", "``--enable-estimate``"
        "``--enable-measure``", "Optional, enabled by default, use only with ``--enable-fftw``", "For search-once-for-the-fast algorithm (takes more time on ``p3dfft_setup()``).", "``--enable-measure``"
        "``--enable-patient``", "Optional, use only with ``--enable-fftw``", "For search-once-for-the-fastest-algorithm (takes much more time on ``p3dfft_setup()``).", "``--enable-patient``"
        "``--enable-dimsc``", "Optional", "To assign processor rows and columns in the Cartesian processor grid according to C convention. The default is Fortran convention which is recommended. This option does not affect the order of storage of arrays in memory.", "``--enable-dimsc``"
        "``FC=<Fortran compiler>``", "Strongly recommended", "Fortran compiler", "``FC=mpif90``"
        "``FCFLAGS='"<Fortran compiler flags>'"``", "Optional, recommended", "Fortran compiler flags", "``FCFLAGS='"-O3'"``"
        "``CC=<C compiler>``", "Strongly Recommended", "C compiler", "``CC=mpicc``"
        "``CFLAGS='"<C compiler flags>'"``", "Optional, recommended", "C compiler flags", "``CFLAGS='"-O3'"``"
        "``CXX=<C++ compiler>``", "Strongly Recommended", "C++ compiler", "``CXX=mpicxx``"
        "``CXXFLAGS='"<C++ compiler flags>'"``", "Optional, recommended", "C++ compiler flags", "``CXXFLAGS='"-O3'"``"
        "``LDFLAGS='"<linker flags>'"``", "Optional", "Linker flags", ""

Compiling on Comet (XSEDE/SDSC)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. csv-table::
        :header: "Compiler", "Modules", "Arguments"
        :widths: auto

        "Intel", "intel, fftw", "./configure --enable-intel --enable-fftw --with-fftw=$FFTWHOME FC=mpif90 CC=mpicc CXX=mpicxx"
        "GNU", "gnu, fftw", "./configure --enable-gnu --enable-fftw --with-fftw=$FFTWHOME FC=mpif90 CC=mpicc CXX=mpicxx"
        "PGI", "pgi, fftw", "./configure --enable-pgi --enable-fftw --with-fftw=$FFTWHOME FC=mpif90 CC=mpicc CXX=mpicxx"

Compiling on Stampede2 (XSEDE/TACC)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. csv-table::
        :header: "Compiler", "Modules", "Arguments"
        :widths: auto

        "Intel", "intel, fftw3", "./configure --enable-intel --enable-fftw --with-fftw=$TACC_FFTW3_DIR FC=mpif90 CC=mpicc CXX=mpicxx"
        "GNU", "gcc, fftw3", "./configure --enable-gnu --enable-fftw --with-fftw=$TACC_FFTW3_DIR FC=mpif90 CC=mpicc CXX=mpicxx"

Compiling on Bridges (PSC)
^^^^^^^^^^^^^^^^^^^^^^^^^^
.. csv-table::
        :header: "Compiler", "Modules", "Arguments"
        :widths: auto

        "Intel", "intel, fftw3", "./configure --enable-intel --enable-fftw --with-fftw=$FFTW3_LIB/.. FC=mpiifort CC=mpiicc CXX=mpiicpc"
        "GNU", "gcc, fftw3", "./configure --enable-gnu --enable-fftw --with-fftw=$FFTW3_LIB/.. FC=mpif90 CC=mpicc CXX=mpicxx"
        "PGI", "pgi, fftw3", "./configure --enable-pgi --enable-fftw --with-fftw=$FFTW3_LIB/.. FC=mpif90 CC=mpicc CXX=mpicxx"
