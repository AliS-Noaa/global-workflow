GFS V16.3.11 RELEASE NOTES

-------
PRELUDE
-------

This upgrade to the GFS includes updates to both the GSI and WAFS packages:

GSI:

  Enable the monitoring of NOAA-21 and GOES-18 radiances in the GFS. A new version of CRTM with new coefficient files is required and included in this change.

WAFS:

  At GFS v16.3 implementation, UPP and WAFS package were updated to be ready to produce high resolution WAFS output to meet 2023 ICAO milestone with one switch.
  The switch was turned off at GFS v16.3. Now that UKMO has started to produce their corresponding high resolution WAFS output, EMC will work with NCO to turn on the switch.

  Additionally, EMC is updating WAFS ecf definition file to:
  1. trigger WAFS blending job 5 min later from 4:25 to 4:30 to compensate for 5-10 min delay in receiving UKMO high resolution data;
  2. stop producing blended 1.25 deg WAFS file per ICAO milestone.

  Both sides agreed that we sill stop waiting for data from the other side at T+4:45.

IMPLEMENTATION INSTRUCTIONS
---------------------------

The NOAA VLab and the NOAA-EMC and NCAR organization spaces on GitHub .com are used to manage the GFS code.  The SPA(s) handling the GFS implementation need to have permissions to clone VLab Gerrit repositories and private NCAR UPP_GTG repository. All NOAA-EMC organization repositories are publicly readable and do not require access permissions.  Please proceed with the following steps to install the package on WCOSS2:

```bash
cd $PACKAGEROOT
mkdir gfs.v16.3.11
cd gfs.v16.3.11
git clone -b EMC-v16.3.11 https://github.com/NOAA-EMC/global-workflow.git .
cd sorc
./checkout.sh -o
```

The checkout script extracts the following GFS components:

| Component | Tag         | POC               |
| --------- | ----------- | ----------------- |
| MODEL     | GFS.v16.3.1   | Jun.Wang@noaa.gov |
| GLDAS     | gldas_gfsv16_release.v.2.1.0 | Helin.Wei@noaa.gov |
| GSI       | gfsda.v16.3.10 | Andrew.Collard@noaa.gov |
| UFS_UTILS | ops-gfsv16.3.0 | George.Gayno@noaa.gov |
| POST      | upp_v8.3.0 | Wen.Meng@noaa.gov |
| WAFS      | gfs_wafs.v6.3.2 | Yali.Mao@noaa.gov |

To build all the GFS components, execute:
```bash
./build_all.sh
```
The `build_all.sh` script compiles all GFS components. Runtime output from the build for each package is written to log files in directory logs. To build an individual program, for instance, gsi, use `build_gsi.sh`.

Next, link the executables, fix files, parm files, etc in their final respective locations by executing:
```bash
./link_fv3gfs.sh nco wcoss2
```

Lastly, link the ecf scripts by moving back up to the ecf folder and executing:
```bash
cd ../ecf
./setup_ecf_links.sh
```

Additional release notes for `upp/8.3.0` installation on WCOSS2:
https://docs.google.com/document/d/18bUYWmsN7FuweyA2CQTBW1rk3FNRMHKPlYHZlbS8ODs/edit?pli=1

VERSION FILE CHANGES
--------------------

* `versions/build.ver` - change `crtm_ver=2.4.0.1`
* `versions/run.ver` - change `version=v16.3.11`, `gfs_ver=v16.3.11` and `crtm_ver=2.4.0.1`

SORC CHANGES
------------

* No changes from GFS v16.3.10

JOBS CHANGES
------------

* No changes from GFS v16.3.10

PARM/CONFIG CHANGES
-------------------

* No changes from GFS v16.3.10

SCRIPT CHANGES
--------------

* Changes to sorc/gsi.fd/scripts/exglobal_atmos_analysis.sh
* Update ecf/scripts/gfs/atmos/post_processing/grib2_wafs/jgfs_atmos_wafs_blending_0p25.ecf to ICAO2023=yes
* Update ecf/scripts/gfs/atmos/post_processing/grib2_wafs/jgfs_atmos_wafs_grib2.ecf to ICAO2023=yes
* Update ecf/scripts/gfs/atmos/post_processing/grib2_wafs/jgfs_atmos_wafs_grib2_0p25.ecf to ICAO2023=yes
* Update ecf/scripts/gfs/atmos/post_processing/jgfs_atmos_wafs_gcip.ecf to ICAO2023=yes
* In ecf/defs/gfs_v16_3.def, remove jgfs_atmos_wafs_blending task, delay trigger time of jgfs_atmos_wafs_blending_0p25 by 5 minutes
* Update scripts/exgfs_atmos_wafs_grib2_0p25.sh to add forecast hour window to dbn_alert for awf and WAFS unblended hazard data

FIX CHANGES
-----------

* Changes to `fix/fix_gsi/global_anavinfo.l127.txt`, `fix/fix_gsi/global_satinfo.txt` and add `fix/fix_gsi/Rcov_crisn21`

MODULE CHANGES
--------------

* No changes from GFS v16.3.10

CHANGES TO FILE SIZES
---------------------

* WAFS - There will be file size increases.  Please see page 2 of the following Google slide:
https://docs.google.com/presentation/d/1VtPhyYXTe_PS9gXZGMrMPlQ32ELl-JJem-Vq8vO4j_U/edit#slide=id.g134dd9cf8ea_0_107

ENVIRONMENT AND RESOURCE CHANGES
--------------------------------

* jgfs_atmos_wafs_grib2_0p25.ecf changes from mpiprocs=11:ompthreads=1:ncpus=11:mem=80GB to mpiprocs=39:ompthreads=1:ncpus=39:mem=200GB because there will be 39 forecast hours and each forecast hour uses one processor through MPMD.

PRE-IMPLEMENTATION TESTING REQUIREMENTS
---------------------------------------

* Which production jobs should be tested as part of this implementation?
  * GSI & WAFS
* Does this change require a 30-day evaluation?
  * No

DISSEMINATION INFORMATION
-------------------------

* No changes from GFS v16.3.10

HPSS ARCHIVE
------------

* No changes from GFS v16.3.10

JOB DEPENDENCIES AND FLOW DIAGRAM
---------------------------------

* No changes from GFS v16.3.10

DOCUMENTATION
-------------

* No changes from GFS v16.3.10

PREPARED BY
-----------
Kate.Friedman@noaa.gov
Andrew.Collard@noaa.gov
Wen.Meng@noaa.gov
Jun.Wang@noaa.gov
Yali.Mao@noaa.gov
Hui-Ya.Chuang@noaa.gov
