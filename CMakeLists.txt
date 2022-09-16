cmake_minimum_required(VERSION 3.16.3...3.19.7 FATAL_ERROR)

project(SkinMouldGenerator)

#-----------------------------------------------------------------------------
# Extension meta-information
set(EXTENSION_HOMEPAGE "https://www.slicer.org/wiki/Documentation/Nightly/Extensions/SkinMouldGenerator")
set(EXTENSION_CATEGORY "Radiotherapy")
set(EXTENSION_CONTRIBUTORS "Mark Schumacher (PerkLab, Queen's University), Jacob Andreou (PerkLab, Queen's University), Ian Cumming (PerkLab, Queen's University), Andras Lasso (PerkLab, Queen's University)")
set(EXTENSION_DESCRIPTION "3D Slicer extension for generating 3D-printable HDR Brachytherapy applicator for treatment of skin cancer.")
set(EXTENSION_ICONURL "https://github.com/PerkLab/SlicerSkinMouldGenerator/raw/master/SkinMouldGenerator.png")
set(EXTENSION_SCREENSHOTURLS "https://github.com/PerkLab/SlicerSkinMouldGenerator/raw/master/Doc/MouldInputs.png https://github.com/PerkLab/SlicerSkinMouldGenerator/raw/master/Doc/MouldResult.png")
set(EXTENSION_DEPENDS "NA") # Specified as a list or "NA" if no dependencies

#-----------------------------------------------------------------------------
# Extension dependencies
find_package(Slicer REQUIRED)
include(${Slicer_USE_FILE})

#-----------------------------------------------------------------------------
# Extension modules
add_subdirectory(HDRMould)
## NEXT_MODULE

#-----------------------------------------------------------------------------
include(${Slicer_EXTENSION_GENERATE_CONFIG})
include(${Slicer_EXTENSION_CPACK})
