SPheno [![Build Status](https://travis-ci.org/JP-Ellis/SPheno.svg?branch=master)](https://travis-ci.org/JP-Ellis/SPheno)
======

SPheno stands for S(upersymmetric) Pheno(menology).

The code calculates the SUSY spectrum using low energy data and a user supplied
high scale model as input.  The spectrum is then used for calculating the
following:

- Two- and three-body decay modes of supersymmetric particle as well
as of Higgs bosons;
- The production cross sections for supersymmetric particle and Higgs bosons in e^+ e^- annihilation;
- The branching of the decay $b \to s \gamma$
- Contributions to anomalous magnetic moment of the muon;
- Contributions to the $\rho$ parameter due to sfermions.

The code is written in F90 with an emphasis on easy generalisability.  The
structure is set such that complex phases as well as the extension to include
the flavour structure can be done in a straight forward way.  The 2-loop
renormalization group equations as well as the one-loop finite corrections a la
Bagger, Matchev, Pierce and Zhang are included.  In addition the two-loop
corrections to the neutral Higgs boson masses (a la Brignole, Degrassi, Slavich
and Zwirner) and to the mu-parameter (a la Dedes and Slavich) are included.
Starting with version 2.2.2 the SUSY Les Houches Accord is supported as well as
the SPA conventions (for details see hep-ph/0511344).

Starting with version 3.0 the complete flavour and CP structure of the MSSM as
well as bilinear R-parity violation has been implemented.  The input and output
is done via he SUSY Les Houches Accord 2 .  Moreover, starting with version
3.1.10 also parts of the proposal to include seesaw models has been implemented,
see the corresponding chapter of the Les Houches Proceedings 2011 and with
version 3.1.10 also parts of the Flavour Les Houches Accord have been
implemented.

If you use SPheno to write a paper, please cite the SPheno manuals:
- W. Porod, Comput. Phys. Commun. 153 (2003) 275
  [arXiv:hep-ph/0301101](http://de.arxiv.org/abs/hep-ph/0301101); and
- W. Porod and F. Staub, Comput. Phys. Commun. 183 (2012) 2458
  [arXiv:1104.1573](http://arxiv.org/abs/1104.1573)

More information is provided in the manuals in the `doc/` folder.

In case of problems, please send an email to
[porod@physik.uni-wuerzburg.de](mailto:porod@physik.uni-wuerzburg.de) explaining
the problem.  Useful informations is in general contained in the file
Messages.out; moreover, you should provide me with the input files used so that
I can try to repeat the run and to decect the source of the problem.


Installation
------------

SPheno can be download from [Hepforge](https://spheno.hepforge.org/).  After
download and extracting the archive, it can be compiled with:

```sh
mkdir build
cd build
cmake ..
make
make install
```

By default, `make install` installs the library and binary into the same
directory as the source; however, this can be customized by adding
`-DCMAKE_INSTALL_PREFIX=/foo/bar` to the `cmake` command.


Running
-------

To run SPheno, it simply needs to be given an input file:
```sh
./bin/SPheno input/LesHouches.in
```
This will generate two files:
- `SPheno.spc`: contains all information about masses, mixing matrices, decay
  widths, branching ratios and production cross sections.  The numbers should be
  identical to the ones given in the file SPheno.spc.mSugra provided you did not
  change the file `LesHouches.in`;
- `Messages.out` containing warnings and error messages.

Some example LesHouches files are provided in the `input/` directory.

Package Content
---------------
 
The package consists of the following files fortran 90 modules:

- `SPheno3.f90`: main program containing also the input/output routines
- `BranchingRatios.f90`: module for the calculation of the decay widths and
  branching ratios of SUSY particles and Higgs bosons
- `Chargino3.f90`: module, containing routines for the calculation of
  three-body decays of charginos
- `Control.f90`: module containing routines for warning and error handling
- `Couplings.f90`: module, containing routines for the calculation of the
  couplings
- `DecayFunctions.f90`: module, containing generic routines for the
  calculation of two-body decays
- `EplusEminusProduction.f90`: module, containing routines for the calculation
  of the production cross sections in e+ e- annihilation
- `Gluino3.f90`: module, containing routines for the calculation of three-body
  decays of the gluino
- `InputOutput.f90`: module, containing routines for input and output
- `LHC_observables.f90`: module, containing routines for LHC observables,
  e.g. edge variables
- `LoopCouplings.f90`: module, containing routines for the calculation of the
  running couplings
- `LoopFunctions.f90`: module, contining 1-loop and 2-loop functions
- `LoopMasses.f90`: module, containing the routines for the calculation of the
  loop masses
- `LowEnergy.f90`: module, containing routines for the calculation of low
  energy observables
- `Mathematics.f90`: module, containing numerical routines
- `MathematicsQP.f90`: module, containing numerical routines with quadrupole
  precision
- `Model_data.f90`: module containing variables concerning SUSY parameters,
  masses, decay widths and branching ratios
- `Neutralino3.f90`: module, containing routines for the calculation of
  three-body decays of neutralinos
- `NMSSM_tools.f90`: module, containing first modules for the NMSSM
- `RGEs.f90`: module, containing the RGEs
- `RPtools.f90`: module, containing additional modules for R-parity violation
- `Slepton3Body.f90`: module, containing routines for the calculation of
  three-body decays of sleptons
- `SPheno3.f90`: The main program for the package.
- `StandardModel.f90`: module, storing the Standard Model parameters
- `Stop3BodyDecays.f90`: module, containing routines for the calculation of
  three-body decays of the lighter stop
- `SugraRuns.f90`: module, containing the routines for setting the boundary
  conditions, controlling the running of the parameters as well as calculating
  the spectrum in case of high scale models
- `SusyDecays.f90`: module, containing routines for the calculation of all
  two-body decays of supersymmetric particles and Higgs bosons
- `SusyMasses.f90`: module, containing the routines for the calculation of
  supersymmetric masses at tree level
- `ThreeBodyPhaseSpace.f90`: module, containing routines for the calculation
  of the phase space integrals for three-body decays of fermions
- `ThreeBodyPhaseSpaceS.f90`: module, containing routines for the calculation
  of the phase space integrals for three-body decays of scalars
- `TwoLoopHiggsMass.f90`: module, containing the 2-loop routines for the
  calculation of neutral Higgs boson masses and mu

In addition the following set of input files are provided:

- `LesHouches.in`: mSUGRA input file (same as LesHouches.in.mSugra)
- `LesHouches.in.mSUGRA_NUHM`: mSUGRA input file but with non-universal Higgs
  mass parameters
- `LesHouches.in.AMSB`: AMSB input file
- `LesHouches.in.GMSB`: GMSB input file
- `LesHouches.in.mNUHM`: mSUGRA scenario but fixing Higgs sector at the
  electroweak scale using the pole mass of the pseudoscalar Higgs boson and the
  mu parameters
- `LesHouches.in.SeesawI`: example of an mSUGRA scenario combined with seesaw
  type I
- `LesHouches.in.SeesawII`: example of an mSUGRA scenario combined with seesaw
  type II, requires the compilation with option `-DSEESAWIII`
- `LesHouches.in.SeesawIII`: example of an mSUGRA scenario combined with
  seesaw type III, requires the compilation with option `-DSEESAWIII`

The package includes also sample output data corresponding to the
input files specified above, denoted by `SPheno.out.xyz` and
`SPheno.spc.xyz`, where `xyz` stands for any of the models above in the
directory `input/`.
