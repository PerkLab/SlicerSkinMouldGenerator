# SlicerSkinMouldGenerator

3D Slicer extension for generating 3D-printable HDR Brachytherapy applicator to be used in the treatment of skin cancer.

The module receives a CT scan of the treatment area as well as the optimized catheter paths for the delivery of the radiation as input, and outputs a "mask" that holds the catheters in the optimized path during treatment. The mask has an inner and outer surface identical to the surface of the area to be treated and has open slots that guide the catheters along the optimized radiation treatment paths.

Publication: [Mark Schumacher, Andras Lasso, Ian Cumming, Adam Rankin, Conrad B. Falkson, John Schreiner, Chandra Joshi, Gabor Fichtinger, "3D-printed surface mould applicator for high-dose-rate brachytherapy", 9415, SPIE Medical Imaging 2015](http://perk.cs.queensu.ca/contents/3d-printed-surface-mould-applicator-high-dose-rate-brachytherapy)

## Research goals

The use of brachytherapy for the treatment of skin cancers is an important alternative to and complement of External Beam Radiotherapy (EBRT), orthovoltage X-rays and chemotherapy. It involves placing a very small radiation source, such as iridium radioisotopes in protective capsules, very close to the area containing the cancer. This greatly reduces the radiation that healthy tissue is exposed to, and allows for very precise and direct treatment of the cancer. HDR brachy is useful for complex geometries where other modalities would be difficult to use, such as nose, scalp, fingers, ankle, ear, orbit. In poorer countries HDR brachy is much more prevalent than alternative treatments. HDR disadvantages are: that it cannot be used on large surfaces (as it leads to large accumulate dose deep in the tissue); dose planning is not as sophisticated as in other modalities; 150-250% doses are commonly occurring in certain locations in the plan (and people who got used to external beam do not like to see such values); fabricating surface mould takes time.

Today these procedures are done using a universal applicator for all patients, such as the "Freiburg Flap" and the Valencia applicator. The Valencia applicator is used as a point source of radiation and is unsuitable for the treatment of large areas. The Freiburg Flap is a flexible mat like applicator that can conform to the area it is applied to, however its catheters can only bend in one direction and their distance from the area to be irradiated cannot be easily adjusted.

In contrast, the use of a completely customized applicator for each patient allows for careful optimization to be done on the radiation dose planning, such as placing the catheters an optimal distance from the area to be irradiated so that the radiation does not penetrate too deeply and harm healthy tissue. Due to the increasing popularity of 3D printing and other such technologies it is now feasible to create such customized applicators for every patient.

The goal of this project is to create a module in the medical imaging program 3D Slicer that creates such a custom applicator. Using this module a "mask" of the area to be irradiated is created based on a CT scan of the patient. Catheter paths are then carved out of the mask based on optimized radiation treatment paths defined by a medical professional. The resulting mask is then output in a format that can be used by a 3D printer.

## Workflow

### Current clinical workflow for surface mould treatment

- Radiation Oncologist (RO) in consultation with physicist decide if surface mould brachytherapy is most optimal for the patient (or can be treated with electron?)
- RO marks/outlines the treatment area to be treated on the patient skin
- A Mask (thermoplastic) is made for the patient (mostly at the CT couch itself)
- Radiation Therapist (RT) transfers the outlines the treatment area (from skin) to the mask
- Catheter tracks decided, the surface mould prepared with the catheters embedded in it
- Patient is called back, planning CT is obtained with radiopaque CT marker inside the catheters
- CT images transferred to the planning system (Oncentra)
- Target volumes outlined on CT images; other structures of interest also outlined
- Catheters tracked on the CT images
- Dwell positions selected for each catheter
- Dose-point or dose-volume parameters defined for dose optimization and calculations
- Once satisfied, treatment plan is approved
- The treatment plans is transferred to the brachytherapy treatment delivery unit
- A dry run of the treatment plan with the surface mould is executed (with live source)
- Other Plan specific QA and second dose calc check performed
- Patient treatment executed

### Proposed workflow

- CT image is loaded
- User specifies a region of interest
- User specifies start and end points for each catheter
- The software draws a line (potentially spline, if a catheter is defined by more than points) on the mask surface (a few mm distance from the mask) and creates a channel (hole) that will guide the catheter - for each catheter; the channel can be closed or partially open (then it may require wax or other material to fix the catheters there). Maybe print left/right and superior/inferior markers, maybe patient name and/or id.
- The software creates an STL model of the catheter guide block that conforms to the mask surface on one side and contains all the catheter channels.
- A fake CT volume is created that contains the original CT and the needle channels so that a plan can be created in the treatment planning system. If the plan is not good enough then it is possible to change the channel positions (before it gets printed).
- The guide block is printed
- Catheters are inserted and fixed into the guide block
- Continue the same way as the current workflow

### Risks

- The guide block may be displaced on the mask (especially if the surface is quite flat). Potential solution: put metallic posts on the mask (they can also be used as fiducials), print the negative of the posts in the guide block and use it for alignment.
- The catheters may be accidentally pulled back from the channel. Potential solution: have a hole at the end of each channel where a button can be inserted where the catheter tip snaps into. We could just have an opening to expose the catheter tip and put some glue on it but then catheter may not be reusable (as it cannot be removed). Using gel or glue gun may be gentle enough so that the catheter can be removed without damaging the catheter.
- Printing of the channel: the filling material might be difficult to remove from the channel (a solid block is printed, the holes contain water-soluble material that should be removed after printing), if the clearance between the catheter and channel wall is too small then the catheter may stuck, if clearance is too big then it may move. Need to experiment with this. The channel may be partially open or have openings to make it easier to remove the filling material, etc.
- Insertion of the catheters may be difficult due to high curvature. Proposed solution: the software should optimize/minimize the curvature, give warning if exceeded a predefined threshold.

## Requirements

- Auto-generate fiducials at equal distance between a start and end line
- Add possibility to define simple breathing holes
- Specify catheter trajectory smoothness with minimum mm curvature parameter. The brachytherapy system manufacturer specification prescribes minimum curvature is 10mm radiusbut we should not go below 15mm radius to be safe.
- Add a user-defined amount of clearance between the skin surface and the printed phantom surface to account for printing inaccuracies
- It should be possible to print some more material above the channels to provide some backscattering (as HDR brachy dose computation engines assume everything is water, not in air; the printed material is more similar to water than air)

- Main challenges in the 3d printing of surface moulds:
    - Optimal tube diameter: if diameter is too small then the catheter may stuck, and the support material that is used during 3D printing may not be possible to wash out; if diameter is too large then the path is not defined accurately. The diameter of the currently used catheters is 1.7 mm. Potential solutions: experiment with different diameters and cut holes or make the tubes partially open channels (cut a thin slit on top to allow dissolving the support material)
    - Catheter fixing in the channel: the catheter should not slide in/out of the surface mould. Potential solutions: use catheters with a button at the tip and print a hole at the end of each channel where the button snaps into; use wax or glue to prevent sliding (could be pushed in at the end of the channel or through the slits or holes)
    - Accurate placement of the surface mould on the patient. Potential solutions: if the geometry is appropriate then the mould should snap to the correct position (for example on the nose), but for smoother regions (such as the scalp) some additional small sticks or spheres should be attached to the thermoplactic mesh mask to make sure the mould only fits in the correct position.
