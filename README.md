# CBT693
Converted to GitHub via [cbt2git](https://github.com/wizardofzos/cbt2git)

This is still a work in progress. 
Due to amazing work by Alison Zhang and Jake Choi repos are no longer deleted.

```
//***FILE 693 is from Terry K. Miller and Richard L. Rice, and      *   FILE 693
//*           contains a modernized version of the PDSCLEAN program *   FILE 693
//*           from CBT Tape File 040.  This version of PDSCLEAN     *   FILE 693
//*           can be used to empty either a PDS or a PDS/E of all   *   FILE 693
//*           its members, and it can also be used to expand the    *   FILE 693
//*           number of directory blocks in a PDS, and it can be    *   FILE 693
//*           used to compress a PDS dataset.                       *   FILE 693
//*                                                                 *   FILE 693
//*           Various help members and three REXX execs to invoke   *   FILE 693
//*           the PDSCLEAN program in the foreground, are included, *   FILE 693
//*           as well as sample JCL to run PDSCLEAN in the          *   FILE 693
//*           background.                                           *   FILE 693
//*                                                                 *   FILE 693
//*           email:  tkmille@conocophillips.com                    *   FILE 693
//*                                                                 *   FILE 693
//*           Last Revision: 06/09/2010 V01.01.09                   *   FILE 693
//*                                                                 *   FILE 693
//*> * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * <*   FILE 693
//*                                                                 *   FILE 693
//*                     Important note  !!!!                        *   FILE 693
//*                     --------- ----  ----                        *   FILE 693
//*                                                                 *   FILE 693
//*  ==> Program PDSCLEAN must be linked with AC=1 and must reside  *   FILE 693
//*  ==> in an APF-Authorized Library or program PDSCLEAN will      *   FILE 693
//*  ==> terminate with a Condition Code 016.                       *   FILE 693
//*  ==>                                                            *   FILE 693
//*  ==> Program PDSCLEAN should also reside in the IKJTSO00 member *   FILE 693
//*  ==> in the AUTHPGM Section to Authorize its being called in    *   FILE 693
//*  ==> Rexx Execs ALTERDIR, COMPRESS, and ZERODIR. Otherwise,     *   FILE 693
//*  ==> Program PDSCLEAN will terminate with a Condition Code 016. *   FILE 693
//*                                                                 *   FILE 693
//*> * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * <*   FILE 693
//*                                                                 *   FILE 693
//*     This PDS contains source and documentation for the          *   FILE 693
//*     'PDSCLEAN' program.                                         *   FILE 693
//*                                                                 *   FILE 693
//*     This 'PDSCLEAN' program was picked up and improved from     *   FILE 693
//*     CBT FILE 040 (PEOPLES NATIONAL BANK of WASHINGTON) and      *   FILE 693
//*     improved (I hope) by TERRY MILLER and RICHARD RICE at       *   FILE 693
//*     ConocoPhillips.  The WTOS were changed to a SYSPRINT        *   FILE 693
//*     report and code was added to empty PDS/E'S and to use       *   FILE 693
//*     IEBCOPY to UNLOAD and RELOAD members of a PDS when          *   FILE 693
//*     altering the number of directory blocks (ALTERDIR Function).*   FILE 693
//*                                                                 *   FILE 693
//*     The 'PDSCLEAN' module should reside in an APF-Authorized    *   FILE 693
//*     library, and it should be linkedited with SETCODE AC(1).    *   FILE 693
//*     This is so that IEBCOPY can be called in an APF authorized  *   FILE 693
//*     state, which is very critical, as indicated above.          *   FILE 693
//*                                                                 *   FILE 693
//*     For more documentation on program 'PDSCLEAN', see           *   FILE 693
//*     member 'HELPPDS' included in this pds.                      *   FILE 693
//*                                                                 *   FILE 693
//*     For a Change Log, review member 'CHANGES' included in this  *   FILE 693
//*     pds.                                                        *   FILE 693
//*                                                                 *   FILE 693
//*     Please report any bugs to Terry Miller at                   *   FILE 693
//*     tkmille@conocophillips.com                                  *   FILE 693
//*                                                                 *   FILE 693
//*          PDSCLEAN - SOURCE MEMBER FOR PROGRAM 'PDSCLEAN'.       *   FILE 693
//*                     CLEANS OUT A PDS WITHOUT REQUIRING A        *   FILE 693
//*                     DELETE/DEFINE.  WORKS WITH BOTH PDS         *   FILE 693
//*                     FILES AND PDS/E FILES.  ALSO, THE NUMBER    *   FILE 693
//*                     OF DIRECTORY BLOCKS CAN BE CHANGED FOR      *   FILE 693
//*                     PDS FILES. CAN BE EXECUTED IN TSO           *   FILE 693
//*                     FOREGROUND AS WELL (SEE ALTERDIR AND        *   FILE 693
//*                     ZERODIR BELOW).  THIS PROGRAM MUST BE       *   FILE 693
//*                     ASSEMBLED WITH THE HIGH-LEVEL ASSEMBLER     *   FILE 693
//*                     DUE TO THE LENGTH OF THE DATA FIELDS.       *   FILE 693
//*                                                                 *   FILE 693
//*                     THIS PROGRAM SHOULD BE LINKLISTED AND       *   FILE 693
//*                     APF-AUTHORIZED AND LINKEDITED USING         *   FILE 693
//*                     SETCODE AC(1).                              *   FILE 693
//*                                                                 *   FILE 693
//*          ALTERDIR - REXX EXEC TO ALTER THE NUMBER OF PDS        *   FILE 693
//*                     DIRECTORIES FOR A PDS FILE. CALLS           *   FILE 693
//*                     PROGRAM 'PDSCLEAN' TO PERFORM THE TASK.     *   FILE 693
//*                     THIS EXEC ASSUMES THAT PROGRAM 'PDSCLEAN'   *   FILE 693
//*                     RESIDES IN THE LINKLIST.                    *   FILE 693
//*                                                                 *   FILE 693
//*          CHANGES  - CHANGE LOG SHOWING THE CHANGES APPLIED ON   *   FILE 693
//*                     EACH VERSION LEVEL.                         *   FILE 693
//*                                                                 *   FILE 693
//*          COMPRESS - REXX EXEC TO COMPRESS A PDS OR A PDS/E.     *   FILE 693
//*                     CALLS PROGRAM 'PDSCLEAN' TO PERFORM THE     *   FILE 693
//*                     COMPRESS TASK.  THIS EXEC ASSUMES THAT      *   FILE 693
//*                     PROGRAM 'PDSCLEAN' RESIDES IN THE LINKLIST. *   FILE 693
//*                                                                 *   FILE 693
//*          ZERODIR  - REXX EXEC TO EMPTY THE DIRECTORY FOR PDS    *   FILE 693
//*                     FILES AND PDS/E FILES.  CALLS PROGRAM       *   FILE 693
//*                     'PDSCLEAN' TO PERFORM THE TASK.  THIS       *   FILE 693
//*                     EXEC ASSUMES THAT PROGRAM 'PDSCLEAN'        *   FILE 693
//*                     RESIDES IN THE LINKLIST.                    *   FILE 693
//*                                                                 *   FILE 693
//*          PROC     - PDSCLEAN BATCH PROCEDURE (PROC).            *   FILE 693
//*                                                                 *   FILE 693
//*          HELPALT  - HELP SCREEN FOR THE 'ALTERDIR' EXEC         *   FILE 693
//*                                                                 *   FILE 693
//*          HELPCOM  - HELP SCREEN FOR THE 'COMPRES'  EXEC         *   FILE 693
//*                                                                 *   FILE 693
//*          HELPPDS  - HELP DOCUMENTATION FOR PROGRAM 'PDSCLEAN'.  *   FILE 693
//*                                                                 *   FILE 693
//*          HELPZER  - HELP SCREEN FOR THE 'ZERODIR'  EXEC         *   FILE 693
//*                                                                 *   FILE 693
//*                                                                 *   FILE 693
//*                                                                 *   FILE 693
//*  CERTIFICATION                                                  *   FILE 693
//*  -------------                                                  *   FILE 693
//*                                                                 *   FILE 693
//*  BATCH:                                                         *   FILE 693
//*                                                                 *   FILE 693
//*    THESE UTILITIES HAVE BEEN RUN IN A Z/OS ENVIRONMENT.         *   FILE 693
//*    THE SOURCE CODE FOR "PDSCLEAN" MUST BE ASSEMBLED WITH THE    *   FILE 693
//*    HIGH-LEVEL ASSEMBLER.                                        *   FILE 693
//*                                                                 *   FILE 693
//*    THE 'PDSCLEAN' MODULE MUST RESIDE IN AN APF-AUTHORIZED       *   FILE 693
//*    LIBRARY.                                                     *   FILE 693
//*                                                                 *   FILE 693
//*  TSO REXX EXECS (ALTERDIR, COMPRESS, ZERODIR):                  *   FILE 693
//*                                                                 *   FILE 693
//*    THE REXX EXECS INCLUDED ASSUME THE THE 'PDSCLEAN' MODULE     *   FILE 693
//*    RESIDES IN A LINKLISTED LIBRARY.                             *   FILE 693
//*                                                                 *   FILE 693
//*    AN IKJTSO00 ENTRY FOR 'PDSCLEAN' SHOULD ALSO RESIDE IN THE   *   FILE 693
//*    MVS PARMLIB MEMBER UNDER THE 'AUTHPGM' SECTION OR EQUIVALENT *   FILE 693
//*    AUTHORIZATION) TO AUTHORIZE IT (PROGRAM PDSCLEAN) TO BE      *   FILE 693
//*    CALLED FROM THE TSO/E ENVIRONMENT.                           *   FILE 693
//*                                                                 *   FILE 693
//*    THE 'PDSCLEAN' MODULE MUST RESIDE IN AN APF-AUTHORIZED       *   FILE 693
//*    LIBRARY.                                                     *   FILE 693
//*                                                                 *   FILE 693
```
