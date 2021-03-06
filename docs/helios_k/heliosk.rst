Using HELIOS-K
==============

Before HELIOS-K can be used, the molecular or atomic line-lists must be
downloaded and pre-processed. HELIOS-K provides some scripts for that,
which are described later in in this manual.

All necessary parameters for HELIOS-K are set in the file ``param.dat``,
which is also described later. Some parameters can be set also by
console arguments.

HELIOS-K can be started with

::

   ./heliosk

followed by optional console arguments, which are listed below.

HELIOS-K Input parameters
=========================

The input parameters can be specified in the ``param.dat`` file. The
used parameters are listed here, the order can not be changed.

-  name: This name will appear in the output filenames.
-  T: Temperature in Kelvin
-  P: Pressure in Atmospheres
-  PFile: A '-' ignores this option, otherwise this option specifies a
   filename which contains multiple values for P
-  Species Name: The name of the molecule or atomic param file. e.g.
   01_hit16 or 1H2-16O__BT2
-  SpeciesFile: A '-' ignores this option, otherwise this option
   specifies a filename which contains multiple species for gas mixtures
-  ciaSystem: A '-' ignores this option, otherwise a cia system is read
   here. supported are H2-H2, H2-H2_eq, H2_H2_norm, H2-He, H2-He_eq,
   H2-He_norm, H2-CH4_eq, H2-CH4_norm and H2-H.
-  pathToData: The location where the HITRAN or HITEMP data files are
   located, e.g. pathToData = ../HITEMP/ , pathToData = /data/HITEMP/ or
   empty when the files are in the same directory pathToData =
-  numin: minimum wavenumber in 1/cm
-  numax: maximum wavenumber in 1/cm
-  dnu: spatial resolution in wavenumber in 1/cm
-  Nnu per bin: The number of points in nu per bin. When this is set to
   zero, then dnu is used, otherwise the value of dnu is overwritten.
-  cutMode: Set the voigt profile cutting mode:

   -  0: cut at absolute wavenumbers
   -  1: cut at multiple values of Lorentz widths
   -  2: cut at multiple values of Doppler widths

-  cut: value where to cut, according to the cutMode, if cut = 0, then
   no cutting is performed
-  doResampling: When set to one, then all the sorted opacity functions
   per bin are resampled with a Chebyshev polynomial, and the
   coefficients are written to the file ``Out_<name>_cbin.dat``. \*nC:
   Number of Chebyshev coefficients use for the resampling
-  doTransmission: When set to one, then the transmission function per
   bin is computed for various numbers of m, and written to the file
   ``Out_<name>_tr.dat``.
-  nTr: nummber of points used for the transmission function
-  dTr: spacing of the points used for the transmission function in exp
   space: m_i = exp((i - nTr/2) \* dTr)
-  doStoreFullK: When set to one, then the full unsorted opacity
   function is written to the file ``Out_<name>.dat``.
-  doStoreSK: When set to 1, then the per bin sorted opacity function is
   written to the file ``Out_<name>_bin.dat``. When set to 2, then the
   per bin sorted opacity function is written to different bin files
   ``Out_<name>_bin< bin number >.dat``.
-  nbins: number of bins
-  binsfile: A '-' ignores this option, otherwise this option specifies
   a filename which contains the edges of the bins, which can be
   irregular. This option overrides the numin, numax and nbins
   arguments.
-  OutputEdgesFile: A '-' ignores this option, otherwise this option
   specifies a file name which contains the edges of the output
   locations in y for each bin.
-  kmin: minimal value for the opacity function
-  qalphaL: q value in the Lorentzian half width q = Pself / P
-  gammaF: scaling factor for the Lorentzian half width gamma factor,
   gamma = gamma \* gammaF
-  doMean: Calculate the Planck and Rosseland opacity means
-  Units: The units of the opacities. 0: cm^2 / g, 1: cm^2 / molecule
-  ReplaceFile: When set to 1, then all output files are overwritten,
   when set to 0 then the data is appended to the existing files.
-  RLOW: When this is set to 1, then the line wings are computed with a
   10 times coarser resolution.
-  profile: 1 = Voigt, 2 = Lorentz, 3 = Gauss, 4 = cross section

Console Arguments
=================

Instead of using the parameter file, some arguments can also be passed
as console arguments. The console arguments have the highest priority
and are overwriting the arguments of the ``param.dat`` file. The options
are:

-  -name ``<c>``: name
-  -T ``<double>`` : T
-  -P ``<double>`` : P
-  -M ``<int>`` : Molecule Name
-  -path ``<c>`` : pathToData
-  -pathK ``<c>`` : pathToK
-  -numin ``<double>`` : numin
-  -numax ``<double>`` : numax
-  -dnu ``<double>`` : dnu
-  -cutM ``<int>`` : cutMode
-  -cut ``<double>`` : cut
-  -dR ``<int>`` : doResampling
-  -nC ``<int>`` : nC
-  -dT ``<int>`` : doTRansmission
-  -nTr ``<int>`` : nTr
-  -dTr ``<double>`` : dTr
-  -dSF ``<int>`` : doStoreFullK
-  -dSS ``<int>`` : doStoreSK
-  -nbins ``<int>`` : nbins
-  -kmin ``<double>`` : kmin
-  -dev ``<int>`` : Device number (For multiple GPU systems)
-  -q ``<double>`` : qalphaL
-  -gammaF ``<double>`` : gammaF
-  -Mean ``<int>`` : doMean

where ``<c>``\ is a string, ``<double>`` a floating point number, and
``<int>``\ an integer.

Code parameters
===============

The file define.h contains the physical parameters and some code
parameters. After changing some entry, the Code needs to be recompiled.
The code parameters are:

-  def_TOL: Tolerance parameter in the Voigt function. See Algorithm 916
-  def_nthmax: Maximum number of threads per kernel launch. In 2.0 GPUs
   it can not be larger than 32768.
-  def_nlmax: Maximum number of molecular lines per kernel launch.
   Setting a lower number prevents from a time-out on Desktop machines.
-  def_maxlines: Maximum number of lines stored on the GPU.
-  def_maxfiles: Maximum number of files per molecule.
-  def_NmaxSample: Maximum Number of resample coefficients for K(y)

When using a Desktop GPU running an x session, the runtime of a single
kernel launch can be limited to a few seconds. Choosing smaller values
for nlmax and nthmax splits the kernel into smaller parts. But it makes
the code a bit slower.

The binsfile options
====================

When a ``binsFile`` name is given in the ``param.dat`` file, then this
file is used to generate the edges of the bins, which can be irregular.
Note that this option does no support the doResampling and
doTransmission options. The binsfile must contain line by line the edges
of the bins in cm^-1.

For example:

::

   0.5
   50.0
   100.0
   200.0

The output edges option
=======================

When a ``outputedgesFile`` name is given in the ``pram.dat`` file, then
this file is used to specify the averaged output positions of the
``Out_<name>_bin.dat`` files. The file must contain line by line the
positions in y.

For example:

::

   0.0
   0.1
   0.45
   0.78
   1.0

The P file option
=================

When a ``PFile`` name is given in the ``param.dat`` file, then this file
is used to read multiple values for P. This option is useful to speed up
the performance, because multiple reads from the data files can be
avoided. Too many entries in the Pfile can lead to a memory shortage.

For example:

::

   1.0
   10.0
   100.0

The Species file option
=======================

This option must be used to calculate opacities for gas mixtures,
containing multiple species. The File contains in the two columns the
species name, and the number fraction.

For example:

::

   01_hit16    0.9
   05_hit16    0.1

This example will produce an opacitiy with 90% H2O and 10% CO.

Output Files
============

Different Output files are written, depending to the set values in the
``param.dat`` file

.. _info_<name>.dat:

``Info_<name>.dat``
===================

Contains the used parameters, and timing information

.. _out_<name>.dat:

``Out_<name>.dat``
==================

It contains nu and K(nu), where nu are the wavenumbers and K(nu) is the
full opacity function. When the ``PFile`` option is used, then the files
contain also the values of T and P.

.. _out_<name>_bin.dat:

``Out_<name>_bin.dat``
======================

It contains the values of y and K(y) per bin. y goes from 0 to 1. K(y)
is the per bin sorted opacity function. The bins are separated by two
blank lines, starting with the bin with the lowest wavenumber and ending
with the bin with the highest wavenumber.

When ``doResampling`` is set to one, then this file contains the sorted
opacity functions, recomputed from the Chebyshev coefficients. When the
``PFile`` option is used, then the files contains also the values of T,
P and point index.

When the ``OutputEdgesFile`` option is used, then the file contains not
all points in y, but the averaged values between the edges, given in the
``OutputEdgesFile``.

When ``doStoreSK`` is set to 2, then the bins are stored in different
files with names ``Out_<name>_bin< bin index>.dat`` .

.. _out_<name>_cbin.dat:

``Out_<name>_cbin.dat``
=======================

It contains the Chebyshev coefficients of the per bins sorted natural
logarithm of the opacity functions in the format

``kmin_i ystart_i C0_i C1_i ... C(nC - 1)_i``, where i refers to the bin
index, and ``C`` are the Chebyshev coefficients.

``kmin`` is the minimal value of ``K(y)``, used e.g. in holes in the
opacity funtion.

``ystart`` is the position in y when the value of\ ``K(y)`` starts to be
larger than ``kmin``.

``K(y)`` can be recomputed as

``K(y) = sum_(0 <= j < nC) (C[j] * T[j](yy))``,

where ``T(y)`` are the Chebyshev polynomials and
``yy = (2.0 * y - 1.0 - ystart) / (1.0 - ystart)``, for y in the range
``[ystart, 1]``. The bins are separated with a blank line, starting with
the bin with the lowest wavenumber and ending with the bin with the
highest wavenumber. When the ``PFile`` option is used, then the files
contains also the values of T and P. When ``doResampling`` is set to 2,
then the bins are stored in different files with names
``Out_<name>_cbin< bin index>.dat`` .

The following python script can be used to reconstruct the per bin
sorted opacity function from the Chepyshev coefficients:

::

   import numpy as np
   from numpy.polynomial.chebyshev import chebval

   #change here the name of the file
   data_c = np.loadtxt('Out_name_cbin.dat')

   #change here the bin index and the bin size:
   binIndex = 0
   binSize = 300

   #extract Chebyshev coefficients
   c = data_c[binIndex,2:]
   #extract starting point in x of opacity function
   xs = data_c[binIndex,1]

   #rescale x to the standard Chebychev polynomial range [-1:1]
   x1 = x * 2.0 - 1.0
   k_res = chebval(x1,c,tensor=False)
   x2 = x * (1.0 - xs) + xs

   #result is in k_res for x values in x2
   k_res = np.exp(k_res)

.. _out_<name>_tr.dat:

``Out_<name>_tr.dat``
=====================

It contains m and T. m is the column mass, m_i = exp((i - nTr/2) \* dTr)
T is the Transmission function Int_0^1 exp(-K(y)m) dy When the PFile is
used then the files contains also the values of T, P and point index.
When doTransmission is set to 2, then the bins are stored in different
files with names ``Out_<name>_tr< bin index>.dat``

.. _out_<name>_mean.dat:

``Out_<name>_mean.dat``
=======================

| When the argument doMean is set to one, this file contains the Planck
  and Rosseland means. They are computed over the entire range in
  wavenumbers from numin to numax with spacing dnu. The first line is
  the Planck mean: kappa_P = Int_0^infty (kappa \* B_nu \* dnu) /
  Int_0^infty (B_nu \* dnu). The second line is the Rosseland mean:
  kappa_R = (Int_0^infty (kappa^-1 \* del(B_nu)/del(T) \* dnu) /
  Int_0^infty ( del(B)/del(T)_nu \* dnu))^-1
| The third line is the numerical integral Int_0^infty (B_nu \* dnu)
| The fourth line is the analytic integral Int_0^infty (B_nu \* dnu) =
  sigma \* T^4 / pi The fifth line is the numerical integral Int_0^infty
  ( del(B)/del(T)_nu \* dnu)
| The sixth line is the analytic integral Int_0^infty ( del(B)/del(T)_nu
  \* dnu) = 4 \* sigma \* T^3 / pi

The value of the numerical integrals should converge to the analytic
expressions for high resolutions dnu, numin -> 0 and numax -> infinity.
