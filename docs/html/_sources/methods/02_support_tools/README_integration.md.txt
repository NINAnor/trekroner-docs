# Task 2 | Integration of Municipal Tree points and Laser-detected Tree Crown polygons

This repository provides a workflow for preparing municipal tree data for i-Tree Eco analysis and extrapolating the results to full the study area extent, using the lidar-segmented tree crowns and auxiliary GIS datasets.

------------

Code is provided for the following tasks:

1. **i-Tree Eco Data Preparation:** preparing an input dataset for i-Tree Eco analysis by supplementing existing municipal tree inventories with crown geometry from the ALS data and auxiliary spatial datasets following the workflow by *Cimburova and Barton (2020).*  

2. **i-Tree Eco Extrapolation:** extrapolating the outputs from i-Tree Eco analysis to all trees in the study area.    

The repository is applied on the Norwegian municipalities: *Bærum, Bodø, Kristiansand* and *Oslo.* 

------------






## Workflow | i-Tree Eco Data Preparation

Detailed description of the workflow is provided in the [project note (in prep)](docs/data_preparation.md).

1. Prepare Data
    **entry point:** `prepare_data.py`
    **tasks:**
        (i) load the lidar-segmented tree crown polygons from the ALS data per neighbourhood
        (ii) load the in situ tree stems from the municipal tree inventory
        (iii) clean the in situ tree stems
            - manual municipality-specific cleaning tasks (see REF) 
            - automatic cleaning tasks:
                - set standard field design
                - translate tree species
                - ensure that each tree stem contains: stem_id, dbh, height, crown_diameter 
        (iv) group tree stem points by neighbourhood
        
2. Join the in situ tree stems with the lidar-segmented tree crowns
    **entry point:** `join_data.py`
    **tasks:** 
        (i) classify the geometrical relationship
        (ii) split lidar-segmented tree crowns that overlap with multiple tree stems
        (iii) model the crown geometry of tree stems that do not overlap with lidar-segmented trees
        (iv) quality control wether each crown polygon is assigned to a single tree stem
        (v) join the in situ tree stems with the lidar-segmented tree crowns
        
    **Geometrical Relations:**
    - **Case 1:** one polygon contains one point (1:1), simple join.  
    - **Case 2:** one polygon contains more than one point (1:n), split crown with voronoi tesselation.
    - **Case 3:** a point is not overlapped by any polygon (0:1), model tree crown using oslo formula.
    - **Case 4:** a polygon does not contain any point (1:0), not used to train i-tree eco/dataset for extrapolation.

3. Compute tree attributes and auxillary attributes
    
    **entry point:** `compute_attributes.py`
    **tasks:** 
        (i) compute tree crown attributes (all trees in thes study area)
            - overlay attributes (pollution zone, neighbourhood code)
            - crown_id (based on neighbourhood code and objectid)
            - tree height, crown area 
        (ii) compute tree stem attributes (in-situ trees)
            - overlay attributes (e.g. pollution zone, neighbourhood code, land use) 
            - tree attributes (e.g. dbh, height, crown diameter)
            - join crown attributes (e.g. crown_id, crown area, crown volume, crown shape)
            - building related attributes (e.g. building distance, building direction)
            - crown condition (e.g. crown light exposure)
        
    **IMPORTANT NOTES**: do not run building related attr. and crown condition attr. within pipeline. Run them separatly and cosely check the results. 

    **STEP 3 NEEDS CLEANING, BUILDING CROWN CONDITION SUPER SLOW (e.g. +12h runtime)**
----------------
### Workflow | i-Tree Eco Extrapolation

Detailed description of the workflow is provided in the [project note](docs/extrapolation.md). 




----------------

### References 
- Cimburova, Z., & Barton, D. N. (2020). The potential of geospatial analysis and Bayesian networks to enable i-Tree Eco assessment of existing tree inventories. Urban Forestry & Urban Greening, 55, 126801. https://doi.org/10.1016/j.ufug.2020.126801


### Acknowledgments

*This repository is part of the project:*

**TREKRONER Prosjektet** | Trærs betydning for klimatilpasning, karbonbinding, økosystemtjenester og biologisk mangfold. 

This repository uses code adapted fromt the repository [i-Tree-Eco](https://github.com/zofie-cimburova/i-Tree-Eco) by Cimburova, Z. 2022, this repository is licensed under the GNU General Public License (GPL).