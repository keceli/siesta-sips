# Sample input file for SIESTA
```

##################################################
# Main options
##################################################
# NumberOfAtoms 2040
# NumberOfSpecies 5
xc.functional GGA
xc.authors PBE
PAO.BasisSize SZ
MeshCutoff 300.0 Ry
SystemName test
SystemLabel test
UseSaveData true
SolutionMethod  qetsc #slepc0 for the older method
#BlockSize 23 #Do not set for qetsc
##################################################
# System info
##################################################
%block ChemicalSpeciesLabel
  1   6  C
  2   1  H
  3   7  N
  4   8  O
  5   16 S
%endblock ChemicalSpeciesLabel

# ZM.UnitsLength Ang

# %block Zmatrix
#  cartesian
#    1  1.65    2.29    7.70   0 0 0
#    2  1.16    2.50    8.50   1 1 1
#    2  1.00    1.87    7.13   0 0 0
# %endblock Zmatrix

%block LatticeVectors
  1.0  0.0  0.0
  0.0  1.0  0.0
  0.0  0.0  1.0
%endblock LatticeVectors

AtomicCoordinatesFormat NotScaledCartesianAng
%block AtomicCoordinatesAndAtomicSpecies
    1.65    2.29    7.70     1
    1.16    2.50    8.50     2
    1.00    1.87    7.13     2
    5.18    9.45    8.69     1
    4.40    9.59    8.15     2
    5.13    8.53    8.95     2
    6.85    4.72    2.72     1
    6.83    4.42    1.81     2
    7.65    4.34    3.08     2
%endblock AtomicCoordinatesAndAtomicSpecies

# LatticeConstant 1. Ang

%block SuperCell
1 0 0
0 1 0
0 0 1
%endblock SuperCell

%block LatticeVectors
  1.0  0.0  0.0
  0.0  1.0  0.0
  0.0  0.0  1.0
%endblock LatticeVectors
##################################################


# I/O options
##################################################
LongOutput .false.
# UseTreeTimer .true.
# WriteEigenvalues True
WriteCoorInital  true
WriteCoorStep    true
WriteForces      true
WriteCoorXmol    true
WriteMDXmol      true
WriteMDhistory   true
WriteDM          true
WriteCoorCerius  false
WriteDM.NetCDF .false.
WriteDMHS.NetCDF .false.
##################################################
# SCF convergence options
##################################################
MaxSCFIterations 50
SCFMustConverge .false.
MixHamiltonian  .true.
DM.MixSCF1 .true.
# DM.MixingWeight     0.2        # New DM amount for next SCF cycle
# DM.NumberPulay    14
# SCF.PulayMinimumHistory 12
# SCF.Pulay.Damping 0.5
# SCF.PulayDmaxRegion 20.0
# DM.NumberBroyden        3
# DM.PulayOnFile      false
# DM.NumberKick       20
# DM.KickMixingWeight 0.5
# DM.Pulay.Avoid.First.After.Kick .true.
# DM.Tolerance 0.01
# ElectronicTemperature 10000 K # [300 K]
##################################################
# MD options
##################################################
# MD.TypeOfRun         cg
# MD.NumCGsteps       100
# MD.MaxCGDispl         0.15  Ang
# ZM.MaxDisplLength         0.01  Ang
# ZM.ForceTolLength     0.4 eV/Ang
# MD.MaxForceTol        0.01 eV/Ang
##################################################

```
