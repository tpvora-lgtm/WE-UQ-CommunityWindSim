
.. _advancedCFD:

Community level wind simulation: WE-UQ coupled with BRAILS++
------------------------------------------------------------

**Tanmay Vora, Jieling Jiang, Abiy F. Melaku, Seymour Spence, Ann Jeffers, Frank McKenna**

Introduction
^^^^^^^^^^^^

CommunityWindSim provides a workflow for simulating wind flow within a community of buildings, defined by those buildings within the region of interest. This workflow has been integrated into `WE-UQ <https://github.com/NHERI-SimCenter/WE-UQ/tree/master>`_ and can be run using its GUI. Additionally, the workflow can be run by the user executing the `Python script <https://github.com/tpvora-lgtm/WE-UQ-CommunityWindSim/tree/main/src/CommunityWindSim>`_ that orchestrates the full pre-processing chain required for a computational fluid dynamics (CFD) simulation of urban wind flow. Specifically, the script performs the following **two** steps: **1**) Building footprints for the community of interest and its surrounding region are first generated using BRAILS++ **2**) Using user-provided inputs defining the parameters of the computational simulation, specifically the computational domain, boundary conditions, turbulence modeling, the script finishes by generating all the input files required for an OpenFOAM CFD simulation.  Once the input files have been generated, the user can run the wind simulation in OpenFOAM, and finally view the results in Paraview. The following modules are required to use this framework:

1. Python3

2. `BRAILS++ <https://nheri-simcenter.github.io/BrailsPlusPlus/rst-doc/install.html>`_

3. OpenFOAM v10

4. Paraview

5. trimesh, geopandas and shapely

6. `TINF <https://nheri-simcenter.github.io/TinF-Documentation/building.html>`_

.. _fig-advanced-cfd-1:

.. figure:: figures/Picture1.png
   :align: center
   :width: 75%

   The WE-UQ and BRAILS++ integration workflow.

Running with WE-UQ
^^^^^^^^^^^^^^^^^^
The desktop application of WE-UQ is available `here <https://www.designsafe-ci.org/data/browser/tapis/designsafe.storage.community/SimCenter%2FSoftware%2FWE_UQ>`_. Documentation and installation instructions are provided `here <https://nheri-simcenter.github.io/WE-UQ-Documentation/>`_. Case folder entry will the name of the OpenFOAM case folder. Wind direction is the angle (in degrees) measured from the West-to-East direction, and the number of processors are required to run the simulation in parallel. The inputs to GUI of WE-UQ consists of three parts:

1. Geographic Extent - Total region is defined by center point and bounding radius. Region of interest (ROI) is defined by a bounding box with minimum and maximum latitudes and longitudes.

.. _fig-f2:

.. figure:: figures/GeogExtent.png
   :align: center
   :width: 75%

2. Footprint Scraper - Three footprint scrapers are available, namely, USA, MS, and OSM. Default height represents the height of a building whose information is missing.

.. _fig-f3:

.. figure:: figures/BRAILS++.png
   :align: center
   :width: 75%

3. CFD Parameters - The Boundary Mesh Cell Size represents the size of the cells in the background (blockMesh). Refinement levels are relative to this cell size, and the number of cells between layers define the minimum number of cells between two levels. CFD parameters are further divided into four sub parts:

a. Domain Extents - These represent the distance of the boundaries from the nearest building in terms of the maximum height of the buildings (Hmax).  

.. _fig-f4:

.. figure:: figures/CD1.png
   :align: center
   :width: 75%

b. Regional Refinement - This widget allows the users to define refinement levels inside the domain to resolve the flow field with more accuracy near the buildings. Each region is defined as a box and each level leads to a twofold decrease in the cell size from the previous level (for example level 1 cell size will be background cell/2 and level 2 cell size will be level 1 cell/2 and so forth).

.. _fig-f5:

.. figure:: figures/CD2.png
   :align: center
   :width: 75%

c. Boundary Conditions - The boundary condition for each boundary patch must be defined along with the turbulence framework and its corresponding submodel. To use a turbulent inflow at the inlet, a CSV file must be provided (example of a CSV file can be found `here <https://github.com/tpvora-lgtm/WE-UQ-CommunityWindSim/blob/main/src/CommunityWindSim/wind_profile.csv>`_). The parameters Uref, Href, and z0 are used to calibrate the mean logarithmic inlet velocity.

.. _fig-f6:

.. figure:: figures/CD3.png
   :align: center
   :width: 75%

d. Control Dictionary - In this widget, solver and time parameters are required. Additionally, multiple section planes and profile lines for storing the time histories of variables can be defined.

.. _fig-f7:

.. figure:: figures/CD4.png
   :align: center
   :width: 75%

Running the Python Script on its own
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The Python script in the src/CommunityWindSim can alternatively run without the WE-UQ GUI. An example input JSON file and TINF CSV file are provided in the directory to illustrate the inputs to the script. Before running the script, some dictionaries must be installed using the following commands:

``pip install git+https://github.com/NHERI-SimCenter/BrailsPlusPlus``

``pip install geopandas shapely pyproj trimesh rtree mapbox-earcut``

The script can then run using the following command:

``python community_wind_simulation.py input.json``


Example
^^^^^^^^
This example provides a step-by-step guide for performing a community-level wind simulation using the RANS approach, following the workflow outlined above.


Target region for the simulation
"""""""""""""""""""""""""""""""""
In this example, the coordinate information for both the target region and the ROI is provided in Table 1 below using longitude and latitude. The target region is defined as a circular area centered on the given coordinate with a radius of 100 meters, while the ROI is specified by its bounding coordinates.

.. _tbl-advanced-cfd-1:
.. table:: Input coordinates for the Target region 
   :align: center
    
   +---------------------+----------------------------------------------+---------------------
   |Region               |Longitude                                     |Latitude            | 
   +=====================+==============================================+====================+
   |Total region(center) |-118.1366968745362                            | 34.19605960623262  |
   +---------------------+----------------------------------------------+--------------------+
   |ROI-min extent       |-118.13720649423749                           | 34.196219338557086 | 
   +---------------------+----------------------------------------------+--------------------+
   |ROI-max extent       |-118.13619261925281                           | 34.19641900353716  |
   +---------------------+----------------------------------------------+--------------------+


The user interface for inputting the given data is shown in :numref:`fig-advanced-cfd-8` and the output creating the geojson files is provided in :numref:`fig-advanced-cfd-9`.


.. _fig-advanced-cfd-8:

.. figure:: figures/Picture8.png
   :align: center
   :width: 50%

   Inputs for generating the building footprints.

.. _fig-advanced-cfd-9:

.. figure:: figures/Picture9.png
   :align: center
   :width: 50%

   Output generating the building footprints.

As illustrated in :numref:`fig-advanced-cfd-9`, the total region includes 37 building footprints, while the ROI contains 2 building footprints—consistent with geojson output shown in :numref:`fig-advanced-cfd-10`.


.. _fig-advanced-cfd-10:

.. figure:: figures/Picture10.png
   :align: center
   :width: 50%

   Visualization of the generated geojson files.

Based on the geojson files, STL files for both the surrounding region and the region of interest (ROI) required for the simulation are generated, as illustrated in :numref:`fig-advanced-cfd-11`.

.. _fig-advanced-cfd-11:

.. figure:: figures/Picture11.png
   :align: center
   :width: 50%

   Visualization of the generated STL file.

Mesh
""""""

**Background mesh**

The wind direction is taken as 225 degrees counterclockwise from East (i.e. in the SW direction). The side boundaries were set to slip for this simulation. An example input snapshot is shown in :numref:`fig-advanced-cfd-12`.

.. _fig-advanced-cfd-12:

.. figure:: figures/Picture12.png
   :align: center
   :width: 50%

   Inputs for generating background mesh.


The domain extents were defined as shown in :numref:`fig-advanced-cfd-13`. The inlet was a distance of 7.5Hmax from the total region, the outlet was 20Hmax from the total region, the sides were 10Hmax, and the top was 20Hmax from the total region.


.. _fig-advanced-cfd-13:

.. figure:: figures/Picture13.png
   :align: center
   :width: 50%

   The domain extents.

The computational cell size for the background mesh was 5 meters in all directions. The output is shown in :numref:`fig-advanced-cfd-14`. The script also outputs the domain extents for the ease of providing mesh refinement regions.


.. _fig-advanced-cfd-14:

.. figure:: figures/Picture14.png
   :align: center
   :width: 50%

   Output for successfully generating the blockMeshDict and the domain extents.


**Regional refinements**
Three refinement boxes were defined to get a good mesh resolution. The extents and the levels of refinement are presented in :numref:`fig-advanced-cfd-15`.

**Surface refinements**

The surface refinement level was set to 5 for the region of interest (ROI) and to 4 for the surrounding buildings. The number of cells between each refinement level was 10. With these settings, the input configuration for generating the snappyHexMeshDict is complete, as shown in :numref:`fig-advanced-cfd-15` and :numref:`fig-advanced-cfd-16`

.. _fig-advanced-cfd-15:

.. figure:: figures/Picture15.png
   :align: center
   :width: 50%

   Inputs to define regional refinement bounding boxes.

.. _fig-advanced-cfd-16:

.. figure:: figures/Picture16.png
   :align: center
   :width: 50%

   Output for successfully generating the ``snappyHexMeshDict`` and the mesh.

Transport property
"""""""""""""""""""""

The default kinematic viscosity is used in this example.


.. _fig-advanced-cfd-17:

.. figure:: figures/Picture17.png
   :align: center
   :width: 50%

   Output for successfully generating the ``transportProperties``.


Numerical setup
""""""""""""""""

**Wind characteristic**

A wind speed of 20 m/s at a reference height of 50 m, with a terrain roughness length of 1 m is prescribed as shown below in :numref:`fig-advanced-cfd-18`. 

.. _fig-advanced-cfd-18:

.. figure:: figures/Picture18.png
   :align: center
   :width: 50%

   Inputs to select the turbulence model and define the wind characteristics.


Boundary conditions
"""""""""""""""""""""
At the ground surface, a smooth wall boundary condition is applied whereas, on the building surfaces, a rough wall boundary condition is applied. With these settings, the turbulenceProperties and boundary field files were generated, as illustrated in Figure 19.

.. _fig-advanced-cfd-19:

.. figure:: figures/Picture19.png
   :align: center
   :width: 50%

   Inputs and outputs for generating the boundary field file.


Simulation time setup
"""""""""""""""""""""
The simulation duration was 10,000 with a time step of 1, indicating that the RANS simulation will run for 10,000 iterations. The output data was written every 1,000 iterations. With these inputs, the controlDict file was generated, as shown in :numref:`fig-advanced-cfd-20`.

Ten processors were used to run the simulation in parallel. This will automatically generate the decomposeParDict file using the scotch method, allowing the simulation to run in parallel, as :numref:`fig-advanced-cfd-20` shows.

.. _fig-advanced-cfd-20:

.. figure:: figures/Picture20.png
   :align: center
   :width: 50%

   Snapshot for generating ``controlDict`` and ``decomposeParDict``.

Visualization of the CFD output
"""""""""""""""""""""""""""""""

**Mesh**

:numref:`fig-advanced-cfd-4` shows the perspective view of the computational domain used in the example and :numref:`fig-advanced-cfd-5` shows the mesh refinement levels. It can be seen that the mesh is finer near the buildings and even finer near the buildings in the ROI. A cross section of the mesh levels along the flow direction can be viewed in :numref:`fig-advanced-cfd-21`.

.. _fig-advanced-cfd-21:

.. figure:: figures/Picture21.png
   :align: center
   :width: 50%

   Typical cross section along the flow direction.

**Wind profile**

:numref:`fig-advanced-cfd-22` shows the mean velocity profile at the inlet at the end of the simulation. The OpenFOAM wind profile is almost the same as the Target wind input.

.. _fig-advanced-cfd-22:

.. figure:: figures/Picture22.png
   :align: center
   :width: 50%

   Typical cross section along the flow direction.


**Pressure and velocity field slices**

:numref:`fig-advanced-cfd-23` shows the pressure and velocity fields at a height of z = 3m at the end of the simulation.  We can see that the boundaries are not much affected by the buildings which shows that the boundaries are far enough to not cause any significant changes to the wind flow in the vicinity of the region.

.. _fig-advanced-cfd-23:

.. figure:: figures/Picture23.png
   :align: center
   :width: 50%

   Velocity and pressure field at z=3m.

Manipulating the OpenFOAM files for miscellaneous simulations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The above workflow produces an OpenFOAM workflow specifically for the ABL flow in an urban environment. The same workflow can be used for other types of wind simulations, such as wind flow in a wind tunnel or wind flow over user-defined structures (any geometry). Here’s a breakdown of the parameters and files that can be modified to run any kind of wind simulation.

Domain Extents and boundaries
"""""""""""""""""""""""""""""

The ``blockMeshDict`` file contains the details of the domain extents, the number of cells in each direction, and the boundary type. The users can change the vertices of the domain as per their choice, and also the number of cells in each direction in the blocks section of the file. The boundary patches can be modified in the boundary section. If the user wishes to define faces other than sides as cyclic, they can change the type to cyclic and add another argument as ``neighbourPatch`` with the patch that it’s cyclic with. If the user wishes to make another patch as a wall other than ground, they can just change the type from patch to wall. Make sure to also change the boundary conditions in the ``case/0/field`` variables files. Additionally, users can add more blocks and also define different mesh grading in all directions.

User-defined obstacles
""""""""""""""""""""""

If the user wants to define the geometry of the obstacles, they need to provide the STL file/s and move them to the ``case/constant/triSurface`` folder. The user needs to modify the surfaceFeaturesDict and the ``snappyHexMeshDict`` files. The user needs to remove the ROI.stl and Surrounding.stl files and put in the name of the user-defined STL file and the user-defined region name. The user can also modify the ``snappyHexMeshDict`` file to change the extent of refinement regions and add more regions if required. Additionally, the level of refinement can also be changed. The user is required to also modify the boundary conditions in the field variables in the ``case/0`` folder. The region of obstacles needs to be added in the boundaryField section.


User-defined initialization and inflow
""""""""""""""""""""""""""""""""""""""
The workflow provides options between a logarithmic wind profile and a TINF wind profile. However, if the user requires a different wind profile, they can modify the case/0/U, k, epsilon files for RANS and the case/0/U file for LES. In the boundaryField section, at the inlet, the user can input the profile of choice. If OpenFOAM v10 has standard profiles available, the user can visit the website and apply the condition as shown on the website. Alternatively, the user can assign the value of a variable at each face of the inlet boundary. This can be done in the following way:

1. In the case folder, after creating the mesh (blockMesh and snappyHexMesh), run the postProcess -func writeCellCentres command to get the coordinates of each face at the boundary and each cell in the domain. The coordinates are saved in the files “C”, “Cx”, “Cy”, and “Cz” files inside the case/0 folder. 

2. Extract the y and z coordinates for the inlet face and then calculate the variables at each of those coordinates using a Python script or a MATLAB script. 

3. The following format can then be used to input into the inlet patch of the boundaryField section of a field variable:

.. code-block:: boundaryField section 

   type     fixedValue;

   value    nonuniform  List<scalar or vector>

   Number of inlet faces

   (
       Values (if vector then (value1 value2 value3)
   );


A similar procedure can be used to input a user-defined initial profile inside the domain. The change would be made in the internalField section. Instead of a uniform, a nonuniform value would have to be described. All three coordinates would be required to calculate the profile values.

Mapping fields
""""""""""""""""
It is common to run a coarser or a RANS simulation before running an LES simulation to initialize the variables for faster convergence. A “mapFieldsDict” file is required to do that. An example of such a file is shown in :numref:`fig-advanced-cfd-3`. The user can modify the dict according to the requirements. The user can then map fields from one folder to another using the following command: 

``mapFields path_to_source_folder -sourceTime -latestTime``. 

Type ``mapFields -help`` for more options. 

.. _fig-advanced-cfd-7:

.. figure:: figures/Picture7.png
   :align: center
   :width: 50%

   An example of the mapFieldsDict file.

Turbulence Modeling and wall functions
""""""""""""""""""""""""""""""""""""""""

If the user wishes to use different models, such as DES (Detached Eddy Simulations), RANS , or LES dynamic Smagorinsky, then the user would need to modify the turbulenceProperties file and add or remove field variables depending on the needs of the model. The usage for other turbulence models can be found in the OpenFOAM documentation.

The workflow described above uses standard ABL wall functions. However, different wall functions can be used if the user needs. The nut, k, epsilon files must be modified to implement the wall function. The modification needs to be made in the wall boundaries in the boundaryField section.


.. [Franke2007] Franke, J., Hellsten, A., Schlünzen, K.H. and Carissimo, B., 2007. COST Action 732: Best practice guideline for the CFD simulation of flows in the urban environment.
.. [Spalding1974] B.E. Launder and D.B. Spalding. Computer methods in applied mechanics and engineering, 3(2):269–289, 1974.
.. [Smagorinsky1963] Smagorinsky, J.  General Circulation Experiments with the Primitive Equations I: the Basic Experiment. Monthly Weather Review, 91(3):99-164, 1963..
