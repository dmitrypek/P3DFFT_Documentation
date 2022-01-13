P3DFFT++ C Reference
********************

.. contents::

Setup and Grid Layout
=====================
p3dfft_setup
------------
.. code-block:: c

        void p3dfft_setup()

**Function**: Called once in the beginning of use to initialize P3DFFT++.

p3dfft_cleanup
--------------
.. code-block:: c

        void p3dfft_cleanup()

**Function**: Called once before exit and after the use to free up P3DFFT++ structures.

p3dfft_init_proc_grid
----------------
.. code-block:: c

        int p3dfft_init_proc_grid(int pdims[3],MPI_Comm mpicomm)

**Function**: Initializes a new grid with specified parameters.

.. csv-table::
        :header: "Argument", "Description"
        :widths: auto
	   
	   "*pdims*", "The dimensions of the 3D processor grid. Value of 1 implies the corresponding dimension is local. These are stored in Fortran (row-major) order, i.e. adjacent MPI tasks are mapped onto the lowest index of the processor grid."
	   "*mpicomm*", "The MPI communicator this processor grid is living on. The library makes it own copy of the communicator, in order to avoid interference with the user program communication."

p3dfft_init_data_grid
----------------
.. code-block:: c

        Grid *p3dfft_init_data_grid(int gdims[3],int dim_conj_sym,int pgrid,int dmap[3],int mem_order[3])

**Function**: Initializes a new data grid with specified parameters.

.. csv-table::
        :header: "Argument", "Description"
        :widths: auto

	"*gdims*", "Three global grid dimensions (logical order - X, Y, Z)."
        "*dim_conj_sym*", "Dimension of the array in which a little less than half of the elements are omitted due to conjugate symmetry. This argument should be non-negative only for complex-valued arrays resulting from real-to-complex FFT in the given dimension."
        "*pgrid*", "The processor grid ID, on which this data grid is living on."
        "*dmap*", "A permutation of the 3 integers: ``0``, ``1`` and ``2``. Specifies mapping of data dimensions onto processor grid dimensions. For example, dmap=(1,0,2) implies second data dimension being spanned by the first processor grid dimension, first data dimension being spanned by the second processor grid dimension, and the third data dimension is mapped onto third processor dimension."
        "*mem_order*", "A permutation of the 3 integers: 0, 1 and 2. Specifies mapping of the logical dimension and memory storage dimensions for local memory for each MPI task. ``mem_order[i0] = 0`` means that the i0's logical dimension is stored with ``stride=1`` in memory. Similarly, ``mem_order[i1] = 1`` means that i1's logical dimension is stored with ``stride=ldims[i0]`` etc."

**Return value**: A pointer to the newly initialized ``DataGrid`` structure that can later be used for grid operations and to get information about the grid.

The ``DataGrid`` structure is defined as follows:

.. code-block:: c

        struct Grid_struct {
            int nd; //number of dimensions the volume is split over
            int Gdims[3]; //Global dimensions

            int dim_conj_sym; // Dimension of conjugate symmetry where we store N/2+1 of the data after Real-to-complex transform due to conjugate symmety;(-1 for none)

            int MemOrder[3]; //Memory ordering inside the data volume
            int Ldims[3]; //Local dimensions on THIS processor
            int pgrid; //Processor grid
            int dmapr[3]; //Mapping of the data grid dimensions onto processor grid
            int D[3]; //Ranks of Dimensions of physical grid split over rows and columns correspondingly
            int L[3]; //Rank of Local dimension (p=1)
            int grid_id[3]; //Position of this pencil/cube in the processor grid
	    int ProcDims[3]; // Processor grid dimensions, as mapped onto data dimensions
        int GlobStart[3]; // Starting coords of this cube in the global grid
        } ;
        typedef struct Grid_struct Grid;

p3dfft_free_proc_grid
----------------
.. code-block:: c

        void p3dfft_free_proc_grid(int pgrid)

**Function**: Frees up a processor grid, specified by its handle.

.. csv-table::
        :header: "Argument", "Description"
        :widths: auto

        "*pgrid*", "Handle of the ``ProcGrid`` structure to be freed."

	p3dfft_free_grid
----------------
.. code-block:: c

        void p3dfft_free_data_grid(Grid *gr)

**Function**: Frees up a data grid.

.. csv-table::
        :header: "Argument", "Description"
        :widths: auto

        "*gr*", "pointer to ``DataGrid`` structure."

One-dimensional transforms
==========================
1D transforms can be done with or without data exchange and/or memory reordering. In general, combining a transform with an exchange/reordering can be beneficial for performance due to cache reuse, compared to two separate calls to a transform and an exchange.

The following predefined 1D transforms are available:

.. csv-table::
        :header: "Transform", "Description"
        :widths: auto

        "``P3DFFT_EMPTY_TYPE``", "Empty transform."
        "``P3DFFT_R2CFFT_S``, ``P3DFFT_R2CFFT_D``", "Real-to-complex forward FFT (as defined in FFTW manual), in single and double precision respectively."
        "``P3DFFT_C2RFFT_S``, ``P3DFFT_C2RFFT_D``", "Complex-to-real backward FFT (as defined in FFTW manual), in single and double precision respectively."
        "``P3DFFT_CFFT_FORWARD_S``, ``P3DFFT_CFFT_FORWARD_D``", "Complex forward FFT (as defined in FFTW manual), in single and double precision respectively."
        "``P3DFFT_CFFT_BACKWARD_S``, ``P3DFFT_CFFT_BACKWARD_D``", "Complex backward FFT (as defined in FFTW manual), in single and double precision respectively."
        "``P3DFFT_DCT<x>_REAL_S``, ``P3DFFT_DCT1_REAL_D``", "Cosine transform for real-numbered data, in single and double precision, where *<x>* stands for the variant of the cosine transform, such as ``DCT1``, ``DCT2``, ``DCT3``, or ``DCT4``."
        "``P3DFFT_DST<x>_REAL_S``, ``P3DFFT_DST1_REAL_D``", "Sine transform for real-numbered data, in single and double precision, where *<x>* stands for the variant of the cosine transform, such as ``DST1``, ``DST2``, ``DST3``, or ``DST4``."
        "``P3DFFT_DCT<x>_COMPLEX_S``, ``P3DFFT_DCT1_COMPLEX_D``", "Cosine transform for complex-numbered data, in single and double precision, where *<x>* stands for the variant of the cosine transform, such as ``DCT1``, ``DCT2``, ``DCT3``, or ``DCT4``."
        "``P3DFFT_DST<x>_COMPLEX_S``, ``P3DFFT_DST1_COMPLEX_D``", "Sine transform for complex-numbered data, in single and double precision, where *<x>* stands for the variant of the cosine transform, such as ``DST1``, ``DST2``, ``DST3``, or ``DST4``."

1D transform planning
---------------------
.. code-block:: c

        int p3dfft_plan_1Dtrans(Grid *gridIn, Grid *gridOut, int type1D, int dim, int inplace) 

**Function**: Defines and plans a 1D transform of a 3D array. This planning stage must precede execution of 3D transform.

.. csv-table::
        :header: "Argument", "Description"
        :widths: auto

        "*gridIn*, *gridOut*", "Pointers to the C equivalent of P3DFFT++ ``DataGrid`` object (initial and final)"
        "*dim*", "The logical dimension of the transform (``0``, ``1`` or ``2``). Note that this is the logical dimension rank (``0`` for X, ``1`` for Y, ``2`` for Z), and may not be the same as the storage dimension, which depends on ``mem_order`` member of **gridIn** and **gridOut**. The transform dimension of the grid is assumed to be MPI task-local."
        "*inplace*", "Indicates that this is not an in-place transform (a non-zero argument would indicate in-place)."

**Return value**: The function returns a handle for the transform that can be used in other function calls.

.. note:: This initialization/planning needs to be done once per transform type.

1D transform execution
----------------------
.. code-block:: c
        
        void p3dfft_exec_1Dtrans_double(int mytrans, double *IN, double *OUT)

        void p3dfft_exec_1Dtrans_single(int mytrans, float *IN, float *OUT) 

**Function**: Executes double or single precision 1D transform, respectively, of a 3D array

.. csv-table::
        :header: "Argument", "Description"
        :widths: auto

        "*mytrans*", "The handle for the 1D transform."
        "*IN*, *OUT*", "Pointers to one-dimensional input and output arrays containing the 3D grid stored contiguously in memory based on the local grid dimensions and storage order of **gridIn** and **gridOut**."

.. note::

        1) The execution can be performed many times with the same handle and same or different input and output arrays.
        2) In case of out-of-place transform the input and output arrays must be non-overlapping.
        3) Both input and output arrays must be local in the dimension of transform

Three-dimensional Transforms
============================
p3dfft_init_3Dtype
------------------
.. code-block:: c

        int p3dfft_init_3Dtype(int type_ids[3])

**Function**: Defines a 3D transform type.

.. csv-table::
        :header: "Argument", "Description"
        :widths: auto

        "*type_ids*", "An array of three 1D transform types."

**Return value**: A handle for 3D transform type.

Example:

.. code-block:: c

        int type_rcc, type_ids[3];

        type_ids[0] = P3DFFT_R2CFFT_D;
        type_ids[1] = P3DFFT_CFFT_FORWARD_D;
        type_ids[2] = P3DFFT_CFFT_FORWARD_D;

        type_rcc = p3dfft_init_3Dtype(type_ids);

In this example ``type_rcc`` will describe the real-to-complex (R2C) 3D transform (R2C in 1D followed by two complex 1D transforms).

3D transform planning
---------------------
.. code-block:: c

        int p3dfft_plan_3Dtrans(Grid *gridIn, Grid *gridOut, int type3D, int inplace, int overwrite) 

**Function**: Plans a 3D transform. This planning stage must precede execution of 3D transform.

.. csv-table::
        :header: "Argument", "Description"
        :widths: auto

        "*gridIn*, *gridOut*", "Pointers to initial and final ``DataGrid`` objects"
        "*type3D*", "The 3D transform type defined as above"
        "*inplace*", "An integer indicating an in-place transform if it's non-zero, out-of-place otherwise."
        "*overwrite* (optional)", "Non-zero when it is OK to overwrite the input array (optional argument, default is ``0``)"

**Return value**: The function returns an integer handle to the 3D transform that can be called multiple times by an execute function.

.. note::

        1) This initialization/planning needs to be done once per transform type. 
        2) The final grid may or may not be the same as the initial grid. First, in real-to-complex and complex-to-real transforms the global grid dimensions change for example from (n0,n1,n2) to (n0/2+1,n1,n2), since most applications attempt to save memory by using the conjugate symmetry of the Fourier transform of real data. Secondly, the final grid may have different processor distribution and memory ordering, since for example many applications with convolution and those solving partial differential equations do not need the initial grid configuration in Fourier space. The flow of these applications is typically 1) transform from physical to Fourier space, 2) apply convolution or derivative calculation in Fourier space, and 3) inverse FFT to physical space. Since forward FFT's last step is 1D FFT in the third dimension, it is more efficient to leave this dimension local and stride-1, and since the first step of the inverse FFT is to start with the third dimension 1D FFT, this format naturally fits the algorithm and results in big savings of time due to elimination of several extra transposes.

3D Transform Execution
----------------------
.. code-block:: c

        void p3dfft_exec_3Dtrans_single(int mytrans, float *In, float *Out)

        void p3dfft_exec_3Dtrans_double(int mytrans, double *In, double *Out)

**Function**: Execute 3D transform in single or double precision, respectively.

.. csv-table::
        :header: "Argument", "Description"
        :widths: auto

        "*In*, *Out*", "Pointers to input and output arrays, assumed to be the local portion of the 3D grid array, stored contiguously in memory, consistent with definition of ``DataGrid`` in planning stage."

.. note::

        1) Unless ``inplace`` was defined in the planning stage of ``mytrans``, **In** and **Out** must be non-overlapping
        2) These functions can be used multiple times after the 3D transform has been defined and planned.

3D Spectral Derivative
----------------------
.. code-block:: c

        void p3dfft_exec_3Dtrans_single(int mytrans, float *In, float *Out, int idir)

        void p3dfft_exec_3Dtrans_double(int mytrans, double *In, double *Out, int idir)

**Function**: Execute 3D real-to-complex FFT, followed by spectral derivative calculation, i.e. multiplication by (ik), where i is the complex imaginary unit, and k is the wavenumber; in single or double precision, respectively.

.. csv-table::
        :header: "Argument", "Description"
        :widths: auto

        "*In*, *Out*", "Pointers to input and output arrays, assumed to be the local portion of the 3D grid array stored contiguously in memory, consistent with definition of ``DataGrid`` in planning stage."
        "*idir*", "The dimension where derivative is to be taken in (this is logical dimension, NOT storage mapped). Valid values are ``0`` - ``2``."

.. note::

        1) Unless ``inplace`` was defined in the planning stage of ``mytrans``, **In** and **Out** must be non-overlapping
        2) These functions can be used multiple times after the 3D transform has been defined and planned.
