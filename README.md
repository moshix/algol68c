Algol68C Release 1.3039 for IBM System/370-compatible mainframes
================================================================

***********************************************************************
* (C) 2012 Copyright C.J. Cheney, S.R. Bourne and others              *
*                                                                     *
* Copyright is asserted for the Algol68C Release 1.3039 software.     *
* Permission is granted for this software to be downloaded, installed *
* and used, subject to the conditions                                 *
* (1) that all use of this software is at the user's own risk and     *
* (2) that no person or organisation involved in the development or   *
* distribution of this software has any liability whatsoever arising  *
* from its supply or use.                                             *
***********************************************************************

The software is supplied either:

a) as a zipped file containing an AWS tape file and this README file;
the tape has volume serial 01A68C and standard labels (SL); the first
dataset on the tape contains the JCL for a job to install from the tape
to a 3330-1 DASD having volume serial A68C01;

b) as a zipped file containing an emulated 3330-1 (i.e. 404 cylinders)
DASD in CCKD format and this README file; the emulated DASD has volume
serial A68C01 and the contents are as if installed from (a) above
(however, in contrast to (a), the datasets on the DASD will obviously
not be catalogued).

Approximately 39 cylinders of the 3330 volume are used, whether (a) or
(b).

Before use, the files, (a) or (b), should be unzipped and, before use
on real mainframe hardware, the AWS tape file should be converted to
a real tape;

The person installing the Algol68C system should have sufficient
experience with the use of the relevant IBM operating system to be able
to diagnose and rectify the common problems that arise in using the
operating system, such as exhausting space on a DASD.

The installed PDS A68CLIB.MISC.DOC contains various documents:

Member   | Description
---------+------------------------------------------------------------
BUGS     | List of known bugs
CHANGES  | Lists of changes from earlier versions - users of previous
         | versions of Algol68C are recommended to study this document
CHARSET  | Character set details - in particular changes from previous
         | distributions to take into account the default Hercules
         | EBCDIC-ASCII mappings
CPYRIGHT | Copyright notice and liability disclaimer
DIFFS    | List of differences between Algol 68 (Revised) and Algol68C
IMPLEM   | Implementation notes for help in low-level debugging
INSTALL  | Installation notes
README   | This document
TESTDISC | Discrepancies to be expected between different runs of the
         | test suite
UNIMPLEM | List of currently unimplemented features
---------+------------------------------------------------------------


Notes on installing from the AWS tape 01A68C
--------------------------------------------

The DCB characteristics of all datasets on the tape are

    RECFM=FB,LRECL=80,BLKSIZE=3200

The first dataset, DSNAME=INSTJOB, on the tape is the JCL for the job 
which installs the Algol68C system from the tape. The JCL for the install
job can be read off the tape by a job with a simple IEBGENER job step:

  //A JOB
  // EXEC PGM=IEBGENER,REGION=100K
  //SYSPRINT DD SYSOUT=A
  //SYSIN    DD DUMMY,DCB=BLKSIZE=80
  //SYSUT1   DD UNIT=TAPE,VOL=SER=01A68C,DISP=(OLD,PASS),
  //            LABEL=(1,SL),DSN=INSTJOB
  //SYSUT2   DD SYSOUT=A or whatever you need
  //

The install job installs the Algol68C distribution to the namespace A68CLIB
on the DASD volume A68C01. So that the job can be rerun, e.g. after correcting
errors, it first deletes any datasets that it creates. Check that this will
not inadvertently delete any datasets that you would rather not have deleted
and, if this would happen, change the namespace appropriately.

It may be necessary to customise the installation job for the local
environment, e.g.

a) Make the job card appropriate for your installation.

b) The installation job installs the Algol68C system into the
   following datasets, which will be catalogued in the last step
   (EPILOG) of the job, if it succeeds:

      A68CLIB.MISC.DOC      miscellaneous documentation
      A68CLIB.SYS           initialisation and library environments
      A68CLIB.MOD           load modules
      A68CLIB.ZLIB.TXT3039  assembler source code for the run-time system
      A68CLIB.USER.TXT3039  Algol68C source code for the Standard Prelude
      A68CLIB.TEST.TXT3039  test suite source code (Algol68C)
      A68CLIB.TEST.OUT3039  comparison output from the test suite
      A68CLIB.MISC.SRC      miscellaneous Algol68C source code
      A68CLIB.MISC.JCL      catalogued procedures and other JCL
      A68CLIB.ALGOL68C.LANGREF Algol68C Language Reference Manual

   Additionally, the following object module datasets are created and are,
   by default, deleted in the last step (EPILOG) of the job:

      A68CLIB.ZLIB.OBJ3039
      A68CLIB.USER.OBJ3039
      A68CLIB.Z370.OBJ3039
      A68CLIB.A68C.OBJ3039

   Various temporary datasets are also created and deleted during the job.

   The blocksizes for the DASD datasets are optimised for 3330 discs
   and could be changed if other discs are used.  Similarly, the space
   allocations may need changing for other discs.

   Any such changes are best made in the ALLOC step of the ALLOC inline
   JCL procedure; other jobsteps use JCL referback to this step. In
   particular, the symbolic parameter PFX, defaulting to A68CLIB, may
   be changed to prevent namespace clashes with existing usage at the
   installation. Similarly, the volume serial for the target DASD is
   given by the symbolic parameter DISCVOL, defaulting to A68C01.

Of course, various problems may be encountered in running the install job,
depending on the characteristics of the particular OS installation and
perhaps on other jobs running simultaneously - e.g. insufficient DASD
space for the allocation or extension of (probably temporary) datasets,
clashes of dataset names (unlikely). The person installing the Algol68C
system should have sufficient experience with the use of the relevant IBM
operating system to be able to diagnose and rectify such problems.

In particular, it is advisable that all SYSDA volumes mounted as STORAGE
or PUBLIC (on which temporary datasets may be allocated by unspecific
requests - i.e. without specifying the volume serial) have adequate spare
space.

If the installation job succeeds, all the installed datasets will have
been catalogued; otherwise, all the datasets created by the job will
have been deleted.

Except for the first dataset on the tape, which is a card-image PS
dataset, the other datasets on the tape are packed using a simple
modification of the AWS format in which the members of a PDS
(other than a load module PDS) can be stored in a single sequential
dataset. For the avoidance of doubt, the AWS tape uses the standard
AWS format, whereas individual datasets (other than the first) on the
distribution tape are packed using the modified AWS format.

