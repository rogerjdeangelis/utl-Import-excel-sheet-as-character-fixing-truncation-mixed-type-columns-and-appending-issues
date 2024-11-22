# utl-Import-excel-sheet-as-character-fixing-truncation-mixed-type-columns-and-appending-issues
Import excel sheet as character fixing truncation mixed type columns and appending issues
    %let pgm=utl-Import-excel-sheet-as-character-fixing-truncation-mixed-type-columns-and-appending-issues;

    %stop_submission;

    Import excel sheet as character fixing truncation mixed type columns and appending issues;

    github
    https://tinyurl.com/yb5fn5ns
    https://github.com/rogerjdeangelis/utl-Import-excel-sheet-as-character-fixing-truncation-mixed-type-columns-and-appending-issues

    Input excel workbook (you can use the code below if you do not want to download from github)
    https://tinyurl.com/5fztm574
    https://github.com/rogerjdeangelis/utl-Import-excel-sheet-as-character-fixing-truncation-mixed-type-columns-and-appending-issues/blob/main/iris.xlsx

     SOLUTIONS

        1 Given a template sas dataset, make sure next months excel import has the same attributes as template
          Fixes truncation issues, mixed type columns, date issues, data append issues restores labels and formats

        2 R program to create input if you don not want to download from github

        3 All macros in
          https://tinyurl.com/y9nfugth
          https://github.com/rogerjdeangelis/utl-macros-used-in-many-of-rogerjdeangelis-repositories
          debug command macro in sas_saspac.sas (uses mprint to get the gereated code)

     PROCESS

        1. USE R PACKAGE READXL TO IMPORT ALL EXCEL SHEET COLUMNS AS STRINGS
            col_types = "text", guess_max = 21474836)

        2. USE TEMPLATE DATASET AND GATHER2 MACRO
           TO GET CORRECT ATTRIBUTES TO CONVERT THE IMPORTED STRINGS
           %utl_gather2(sashelp.iris(obs=1),atr,val,,iris_atr,WithLabels=Y,SASFormats=Y);

        3  USE THE ARRAY AND DO_OVER MACROS TO GENERATE SQL CODE TO RESTORE ATTRIBUTES
           %do_over( _nam _lbl _fmt, phrase=%str(
              input(?_nam,?_fmt) as ?_nam label="?_lbl"
           ),between=comma)

    Perhaps this is not the best example dataset because there
    is no truncation of character variables, no mixed type columns.
    However the solution does provide the labels and template formats to change type.

    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

    /***********************************************************************************************************************************************************/
    /*                                                                |                                            |                                           */
    /*                                                                |                                            |                                           */
    /*      INPUT (TWO INPUTS EXCEL SHEET AND SAS TEMPLATE)           | IMPORT EXCEL SHEET AS MAX STRINGS          | FIANAL CORRECTED TABLE WITH               */
    /*                                                                | =================================          | THE SAME ATTRIBUTES AS THE TEMPLATE       */
    /* DOWNLOAD WORKBOOK FROM GITHUB OR USE CODE ON END OF THIS POST. |                                            |  ==================================       */
    /*                                                                | %utl_rbeginx;                              |                                           */
    /* INPUTS                                                         | parmcards4;                                | NOTE LABELS HAVE BEEN ADDED               */
    /*                                                                | library(readxl)                            | TEMPLATE FORMATS WAS USD TO CONVERT TYPEs */
    /*  1.  d:/xls/iris.xlsx sheet iris                               | source("c:/oto/fn_tosas9x.R")              |                                           */
    /*    ===========================                                 | want <- read_excel("d:/xls/iris.xlsx",     |                                           */
    /*                                                                |     col_types = "text",                    |  Variable     Type Len  Label             */
    /*      LENGTH $9              NUMERIC TYPES                      |     guess_max = 21474836)                  |                                           */
    /*     =========== ============================================== | str(want)                                  |  SPECIES      Char  10  Iris Species      */
    /*     +--------------------------------------------------------+ | fn_tosas9x(                                |  SEPALLENGTH  Num    8  Sepal Length (mm) */
    /*     |     A    |    B      |     C    |    D      |    E     | |       inp    = want                        |  SEPALWIDTH   Num    8  Sepal Width (mm)  */
    /*     +--------------------------------------------------------+ |      ,outlib ="d:/sd1/"                    |  PETALLENGTH  Num    8  Petal Length (mm) */
    /*   1 | SPECIES  |SEPALLENGTH|SEPALWIDTH|PETALLENGTH|PETALWIDTH| |      ,outdsn ="want"                       |  PETALWIDTH   Num    8  Petal Width (mm)  */
    /*     +----------+-----------+----------+-----------+----------+ |      )                                     |                                           */
    /*   2 | Setosa   |   60      |    33    |    14     |    2     | | ;;;;                                       |                                           */
    /*     +----------+-----------+----------+-----------+----------+ | %utl_rendx;                                |                                           */
    /*      ...                                                       |                                            |                                           */
    /*     +----------+-----------+----------+-----------+----------+ |                                            |                                           */
    /* 150 | Virginica|   44      |    25    |    22     |    3     | | IMPORTED IRIS SHEET TO SD1.WANT            |                                           */
    /*     +----------+-----------+----------+-----------+----------+ | ================================           |                                           */
    /*                                                                |                                            |                                           */
    /*  [IRIS]                                                        | SD1.WANT (LABELS MISSING)                  |                                           */
    /*                                                                |                                            |                                           */
    /*                                                                | #  Variable     Type Len                   |                                           */
    /* 2. TEMPLATE DATASET SASHELP.IRIS (HAS CORRECT ATTRIBUTES)      |                                            |                                           */
    /*  ======================================================        | 1  ROWNAMES     Num    4                   |                                           */
    /*                                                                |                                            |                                           */
    /*  SASHELP.IRIS (TEMPLATE DATASET HAS THE CORRECT ATTRIBUTES)    | 2  SPECIES      Char  10  -                |                                           */
    /*  WE WILL USE THIS TO CONVERT THE IMPORTED CHARACTER COLUMNS    | 3  SEPALLENGTH  Char   2  |                |                                           */
    /*                                                                | 4  SEPALWIDTH   Char   2  |  all strings   |                                           */
    /*                                                                | 5  PETALLENGTH  Char   2  |                |                                           */
    /*   Variable       Type    Len    Label                          | 6  PETALWIDTH   Char   2  -                |                                           */
    /*                                                                |                                            |                                           */
    /*   SPECIES        Char     10    Iris Species                   |                                            |                                           */
    /*   SEPALLENGTH    Num       8    Sepal Length (mm)              | GET ATTRIBUTES OF TEMPLATE DATASET         |                                           */
    /*   SEPALWIDTH     Num       8    Sepal Width (mm)               | ==================================         |                                           */
    /*   PETALLENGTH    Num       8    Petal Length (mm)              |                                            |                                           */
    /*   PETALWIDTH     Num       8    Petal Width (mm)               | %utl_gather2(                              |                                           */
    /*                                                                |      sashelp.iris(obs=1)                   |                                           */
    /*                                                                |     ,atr                                   |                                           */
    /*----------------------------------------------------------------|     ,val                                   |                                           */
    /*                                                                |     ,                                      |                                           */
    /* CODE TO CREATE INPUT EXCEL INPUT WORKBOOK                      |     ,iris_atr                              |                                           */
    /*                                                                |     ,WithLabels=Y                          |                                           */
    /* %utlfkil(d:/xls/iris.xlsx);                                    |     ,SASFormats=Y                          |                                           */
    /*                                                                |     );                                     |                                           */
    /* options validvarname=upcase;                                   |                                            |                                           */
    /* libname sd1 "d:/sd1";                                          | WORK.IRIS_ATR                              |                                           */
    /* data sd1.iris;                                                 | =============                              |                                           */
    /*   set sashelp.iris;                                            | ATR              _COLLAB      _COLFORMAT   |                                           */
    /* run;quit;                                                      |                                            |                                           */
    /*                                                                | SPECIES     Iris Species       $10.        |                                           */
    /* %utl_rbeginx;                                                  | SEPALLENGTH Sepal Length (mm)  BEST12.     |                                           */
    /* parmcards4;                                                    | SEPALWIDTH  Sepal Width (mm)   BEST12.     |                                           */
    /* library(openxlsx)                                              | PETALLENGTH Petal Length (mm)  BEST12.     |                                           */
    /* library(sqldf)                                                 | PETALWIDTH  Petal Width (mm)   BEST12.     |                                           */
    /* library(haven)                                                 |                                            |                                           */
    /* source("c:/oto/fn_tosas9x.R")                                  | BUILD SQL ARRAYS                           |                                           */
    /* iris<-read_sas("d:/sd1/iris.sas7bdat")                         | ================                           |                                           */
    /* iris                                                           |                                            |                                           */
    /* wb <- createWorkbook()                                         |%array(_nam,data=iris_atr,var=atr       );  |                                           */
    /* addWorksheet(wb, "iris")                                       |%array(_lbl,data=iris_atr,var=_collab   );  |                                           */
    /* writeData(wb, sheet = "iris", iris)                            |%array(_fmt,data=iris_atr,var=_colformat);  |                                           */
    /* saveWorkbook(wb,                                               |                                            |                                           */
    /* file="d:/xls/iris.xlsx",overwrite=TRUE)                        | %put &=_nam3;                              |                                           */
    /* ;;;;                                                           | %put &=_lbl3;                              |                                           */
    /* %utl_rendx;                                                    | %put &=_fmt3;                              |                                           */
    /*                                                                |                                            |                                           */
    /*                                                                |                                            |                                           */
    /*                                                                | FIX ATTRIBUTES USING TEMPLATE DATASET      |                                           */
    /*                                                                | =====================================      |                                           */
    /*                                                                |                                            |                                           */
    /*                                                                | proc sql;                                  |                                           */
    /*                                                                |  create                                    |                                           */
    /*                                                                |    table iris_2024_11 as                   |                                           */
    /*                                                                | select                                     |                                           */
    /*                                                                |  %do_over( _nam _lbl _fmt, phrase=%str(    |                                           */
    /*                                                                |  input(?_nam,?_fmt) as ?_nam label="?_lbl" |                                           */
    /*                                                                |  ),between=comma)                          |                                           */
    /*                                                                | from                                       |                                           */
    /*                                                                |  sd1.want                                  |                                           */
    /*                                                                | ;quit;                                     |                                           */
    /*                                                                |                                            |                                           */
    /*                                                                |                                            |                                           */
    /*                                                                | FIANAL CORRECTED TABLE WITH                |                                           */
    /*                                                                | THE SAME ATTRIBUTES AS THE TEMPLATE        |                                           */
    /*                                                                | STRINGS ARE NOW NUMERIC                    |                                           */
    /*                                                                |                                            |                                           */
    /*                                                                |  Variable     Type Len  Label              |                                           */
    /*                                                                |                                            |                                           */
    /*                                                                |  SPECIES      Char  10  Iris Species       |                                           */
    /*                                                                |  SEPALLENGTH  Num    8  Sepal Length (mm)  |                                           */
    /*                                                                |  SEPALWIDTH   Num    8  Sepal Width (mm)   |                                           */
    /*                                                                |  PETALLENGTH  Num    8  Petal Length (mm)  |                                           */
    /*                                                                |  PETALWIDTH   Num    8  Petal Width (mm)   |                                           */
    /*                                                                |                                            |                                           */
    /*                                                                |                                            |                                           */
    /*                                                                | GENERATE SQL CODE (USING DEBUG MACRO)      |                                           */
    /*                                                                | ======================================     |                                           */
    /*                                                                |                                            |                                           */
    /*                                                                | If you want the sql code just              |                                           */
    /*                                                                | highlight the code and type debug on       |                                           */
    /*                                                                | the classic 1980s DMS command line.        |                                           */
    /*                                                                | DEBUG shows the final code in the log      |                                           */
    /*                                                                |                                            |                                           */
    /*                                                                |                                            |                                           */
    /*                                                                | input(SPECIES,$10.)                        |                                           */
    /*                                                                |  as SPECIES label="Iris Species"           |                                           */
    /*                                                                |,input(SEPALLENGTH,BEST12.)                 |                                           */
    /*                                                                |  as SEPALLENGTH label="Sepal Length (mm)"  |                                           */
    /*                                                                |   ,input(SEPALWIDTH,BEST12.)               |                                           */
    /*                                                                |  as SEPALWIDTH label="Sepal Width (mm)" ,  |                                           */
    /*                                                                |,input(PETALLENGTH,BEST12.)                 |                                           */
    /*                                                                |  as PETALLENGTH label="Petal Length (mm)"  |                                           */
    /*                                                                |,input(PETALWIDTH,BEST12.)                  |                                           */
    /*                                                                |  as PETALWIDTH label="Petal Width (mm)"    |                                           */
    /*                                                                |                                            |                                           */
    /************************************************************************************************************************************************************/
    /*                   _
    (_)_ __  _ __  _   _| |_ ___
    | | `_ \| `_ \| | | | __/ __|
    | | | | | |_) | |_| | |_\__ \
    |_|_| |_| .__/ \__,_|\__|___/
            |_|
    */

    TWO INPUTS

    1. d:/xls/iris.xlsx sheet iris

    2. Template dataset sashelp.iris

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* TWO INPUTS                                                                                                             */
    /*                                                                                                                        */
    /*  1.  d:/xls/iris.xlsx sheet iris                                                                                       */
    /*      ===========================                                                                                        */
    /*                                                                                                                        */
    /*      LENGTH $9              NUMERIC TYPES                                                                              */
    /*     =========== ==============================================                                                         */
    /*     +--------------------------------------------------------+                                                         */
    /*     |     A    |    B      |     C    |    D      |    E     |                                                         */
    /*     +--------------------------------------------------------+                                                         */
    /*   1 | SPECIES  |SEPALLENGTH|SEPALWIDTH|PETALLENGTH|PETALWIDTH|                                                         */
    /*     +----------+-----------+----------+-----------+----------+                                                         */
    /*   2 | Setosa   |   60      |    33    |    14     |    2     |                                                         */
    /*     +----------+-----------+----------+-----------+----------+                                                         */
    /*      ...                                                                                                               */
    /*     +----------+-----------+----------+-----------+----------+                                                         */
    /* 150 | Virginica|   44      |    25    |    22     |    3     |                                                         */
    /*     +----------+-----------+----------+-----------+----------+                                                         */
    /*                                                                                                                        */
    /*  [IRIS]                                                                                                                */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /* 2. TEMPLATE DATASET SASHELP.IRIS (HAS CORRECT ATTRIBUTES)                                                              */
    /*  ======================================================                                                                */
    /*                                                                                                                        */
    /*  SASHELP.IRIS (TEMPLATE DATASET HAS THE CORRECT ATTRIBUTES)                                                            */
    /*  WE WILL USE THIS TO CONVERT THE IMPORTED CHARACTER COLUMNS                                                            */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*   Variable       Type    Len    Label                                                                                  */
    /*                                                                                                                        */
    /*   SPECIES        Char     10    Iris Species                                                                           */
    /*   SEPALLENGTH    Num       8    Sepal Length (mm)                                                                      */
    /*   SEPALWIDTH     Num       8    Sepal Width (mm)                                                                       */
    /*   PETALLENGTH    Num       8    Petal Length (mm)                                                                      */
    /*   PETALWIDTH     Num       8    Petal Width (mm)                                                                       */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*    __ _                          _   _                            _    _
    / |  / _(_)_  __   _____  _____ ___| | (_)_ __ ___  _ __   ___  _ __| |_ (_)___ ___ _   _  ___  ___
    | | | |_| \ \/ /  / _ \ \/ / __/ _ \ | | | `_ ` _ \| `_ \ / _ \| `__| __|| / __/ __| | | |/ _ \/ __|
    | | |  _| |>  <  |  __/>  < (_|  __/ | | | | | | | | |_) | (_) | |  | |_ | \__ \__ \ |_| |  __/\__ \
    |_| |_| |_/_/\_\  \___/_/\_\___\___|_| |_|_| |_| |_| .__/ \___/|_|   \__||_|___/___/\__,_|\___||___/
     _ __  _ __ ___   ___ ___  ___ ___                 |_|
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|                           _                     _        _
    (_)_ __ ___  _ __   ___  _ __| |_    __ _ ___   ___| |_ _ __(_)_ __   __ _ ___
    | | `_ ` _ \| `_ \ / _ \| `__| __|  / _` / __| / __| __| `__| | `_ \ / _` / __|
    | | | | | | | |_) | (_) | |  | |_  | (_| \__ \ \__ \ |_| |  | | | | | (_| \__ \
    |_|_| |_| |_| .__/ \___/|_|   \__|  \__,_|___/ |___/\__|_|  |_|_| |_|\__, |___/
                |_|                                                      |___/
    */

    %utl_rbeginx;
    parmcards4;
    library(readxl)
    source("c:/oto/fn_tosas9x.R")
    want <- read_excel("d:/xls/iris.xlsx",
        col_types = "text",
        guess_max = 21474836)
    str(want)
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="want"
         )
    ;;;;
    %utl_rendx;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* SD1.WANT (LABELS MISSING)                                                                                              */
    /*                                                                                                                        */
    /* #  Variable     Type Len                                                                                               */
    /*                                                                                                                        */
    /* 1  ROWNAMES     Num    4  from stattransfer                                                                            */
    /*                                                                                                                        */
    /* 2  SPECIES      Char  10  -                                                                                            */
    /* 3  SEPALLENGTH  Char   2  |                                                                                            */
    /* 4  SEPALWIDTH   Char   2  |  all strings                                                                               */
    /* 5  PETALLENGTH  Char   2  |                                                                                            */
    /* 6  PETALWIDTH   Char   2  -                                                                                            */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                       _       _              _   _        _ _           _
    | |_ ___ _ __ ___  _ __ | | __ _| |_ ___   __ _| |_| |_ _ __(_) |__  _   _| |_ ___  ___
    | __/ _ \ `_ ` _ \| `_ \| |/ _` | __/ _ \ / _` | __| __| `__| | `_ \| | | | __/ _ \/ __|
    | ||  __/ | | | | | |_) | | (_| | ||  __/| (_| | |_| |_| |  | | |_) | |_| | ||  __/\__ \
     \__\___|_| |_| |_| .__/|_|\__,_|\__\___| \__,_|\__|\__|_|  |_|_.__/ \__,_|\__\___||___/
                      |_|
    */

     %utl_gather2(
          sashelp.iris(obs=1)
         ,atr
         ,val
         ,
         ,iris_atr
         ,WithLabels=Y
         ,SASFormats=Y
         );

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* GET ATTRIBUTES OF TEMPLATE DATASET                                                                                     */
    /* ==================================                                                                                     */
    /*                                                                                                                        */
    /* WORK.IRIS_ATR.SAS7BDAT                                                                                                 */
    /*                                                                                                                        */
    /* ATR              _COLLAB      _COLFORMAT                                                                               */
    /*                                                                                                                        */
    /* SPECIES     Iris Species       $10.                                                                                    */
    /* SEPALLENGTH Sepal Length (mm)  BEST12.                                                                                 */
    /* SEPALWIDTH  Sepal Width (mm)   BEST12.                                                                                 */
    /* PETALLENGTH Petal Length (mm)  BEST12.                                                                                 */
    /* PETALWIDTH  Petal Width (mm)   BEST12.                                                                                 */
    /*                                                                                                                        */
    /* OTHER ATTRIBUTES                                                                                                       */
    /*                                                                                                                        */
    /* ATR            VAL            _COLLAB         _COLFORMAT    _COLTYP    _COLTYPVAR  _ISREALNUM    _SASFORMAT            */
    /*                                                                                                                        */
    /* SPECIES        Setosa    Iris Species          $10.          char          C            .         $10.                 */
    /* SEPALLENGTH    50        Sepal Length (mm)     BEST12.       num           N            1         BEST12.              */
    /* SEPALWIDTH     33        Sepal Width (mm)      BEST12.       num           N            1         BEST12.              */
    /* PETALLENGTH    14        Petal Length (mm)     BEST12.       num           N            1         BEST12.              */
    /* PETALWIDTH     2         Petal Width (mm)      BEST12.       num           N            1         BEST12.              */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*           _ _     _
    | |__  _   _(_) | __| |  _ __ ___   __ _  ___ _ __ ___    __ _ _ __ _ __ __ _ _   _ ___
    | `_ \| | | | | |/ _` | | `_ ` _ \ / _` |/ __| `__/ _ \  / _` | `__| `__/ _` | | | / __|
    | |_) | |_| | | | (_| | | | | | | | (_| | (__| | | (_) || (_| | |  | | | (_| | |_| \__ \
    |_.__/ \__,_|_|_|\__,_| |_| |_| |_|\__,_|\___|_|  \___/  \__,_|_|  |_|  \__,_|\__, |___/
                                                                                  |___/
    */

     %array(_nam,data=iris_atr,var=atr       );
     %array(_lbl,data=iris_atr,var=_collab   );
     %array(_fmt,data=iris_atr,var=_colformat);

     Sample Elements od arrays

     %put &=_nam3;
     %put &=_lbl3;
     %put &=_fmt3;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* Third elements of the macro arrays                                                                                     */
    /*                                                                                                                        */
    /* Sample Elements of arrays                                                                                              */
    /*                                                                                                                        */
    /* %put &=_nam3;   _NAM3 = SEPALWIDTH        name                                                                         */
    /* %put &=_lbl3;   _LBL3 = Sepal Width (mm)  label                                                                        */
    /* %put &=_fmt3;   _FMT3 = BEST12.           format                                                                       */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*         _    __ _              _   _        _ _           _
     ___  __ _| |  / _(_)_  __   __ _| |_| |_ _ __(_) |__  _   _| |_ ___  ___
    / __|/ _` | | | |_| \ \/ /  / _` | __| __| `__| | `_ \| | | | __/ _ \/ __|
    \__ \ (_| | | |  _| |>  <  | (_| | |_| |_| |  | | |_) | |_| | ||  __/\__ \
    |___/\__, |_| |_| |_/_/\_\  \__,_|\__|\__|_|  |_|_.__/ \__,_|\__\___||___/
            |_|
    */

    FIX ATTRIBUTES USING TEMPLATE DATASET
    =====================================

    proc sql;
     create
       table iris_2024_11 as
    select
     %do_over( _nam _lbl _fmt, phrase=%str(
     input(?_nam,?_fmt) as ?_nam label="?_lbl"
     ),between=comma)
    from
     sd1.want
    ;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  FIANAL CORRECTED TABLE WITH                                                                                           */
    /*  THE SAME ATTRIBUTES AS THE TEMPLATE                                                                                   */
    /*  STRINGS ARE NOW NUMERIC                                                                                               */
    /*  ==================================                                                                                    */
    /*                                                                                                                        */
    /*  Variable     Type Len  Label                                                                                          */
    /*                                                                                                                        */
    /*  SPECIES      Char  10  Iris Species                                                                                   */
    /*  SEPALLENGTH  Num    8  Sepal Length (mm)                                                                              */
    /*  SEPALWIDTH   Num    8  Sepal Width (mm)                                                                               */
    /*  PETALLENGTH  Num    8  Petal Length (mm)                                                                              */
    /*  PETALWIDTH   Num    8  Petal Width (mm)                                                                               */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                               _           _                 _
      __ _  ___ _ __   ___ _ __ __ _| |_ ___  __| |   ___ ___   __| | ___
     / _` |/ _ \ `_ \ / _ \ `__/ _` | __/ _ \/ _` |  / __/ _ \ / _` |/ _ \
    | (_| |  __/ | | |  __/ | | (_| | ||  __/ (_| | | (_| (_) | (_| |  __/
     \__, |\___|_| |_|\___|_|  \__,_|\__\___|\__,_|  \___\___/ \__,_|\___|
     |___/
    */

    If you want the sql code just
    highlight the code and type debug on
    the classic 1980s DMS command line.
    DEBUG shows the final code in the log

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* GERERATED SQL CODE                                                                                                     */
    /*                                                                                                                        */
    /*  input(SPECIES,$10.)        as SPECIES      label="Iris Species"                                                       */
    /*  input(SEPALLENGTH,BEST12.) as SEPALLENGTH  label="Sepal Length (mm)"                                                  */
    /*  input(SEPALWIDTH,BEST12.)  as SEPALWIDTH   label="Sepal Width (mm)"                                                   */
    /*  input(PETALLENGTH,BEST12.) as PETALLENGTH  label="Petal Length (mm)"                                                  */
    /*  input(PETALWIDTH,BEST12.)  as PETALWIDTH   label="Petal Width (mm)"                                                   */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___                        _         _                   _         _
    |___ \    ___ _ __ ___  __ _| |_ ___  (_)_ __  _ __  _   _| |_ __  _| |_____  __
      __) |  / __| `__/ _ \/ _` | __/ _ \ | | `_ \| `_ \| | | | __|\ \/ / / __\ \/ /
     / __/  | (__| | |  __/ (_| | ||  __/ | | | | | |_) | |_| | |_  >  <| \__ \>  <
    |_____|  \___|_|  \___|\__,_|\__\___| |_|_| |_| .__/ \__,_|\__|/_/\_\_|___/_/\_\
                                                  |_|
    */


    %utlfkil(d:/xls/iris.xlsx);

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.iris;
      set sashelp.iris;
    run;quit;

    %utl_rbeginx;
    parmcards4;
    library(openxlsx)
    library(sqldf)
    library(haven)
    source("c:/oto/fn_tosas9x.R")
    iris<-read_sas("d:/sd1/iris.sas7bdat")
    iris
    wb <- createWorkbook()
    addWorksheet(wb, "iris")
    writeData(wb, sheet = "iris", iris)
    saveWorkbook(wb,
    file="d:/xls/iris.xlsx",overwrite=TRUE)
    ;;;;
    %utl_rendx;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*     d:/xls/iris.xlsx sheet iris                                                                                        */
    /*     ===========================                                                                                        */
    /*                                                                                                                        */
    /*       LENGTH $9              NUMERIC TYPES                                                                             */
    /*      =========== ==============================================                                                        */
    /*      +--------------------------------------------------------+                                                        */
    /*      |     A    |    B      |     C    |    D      |    E     |                                                        */
    /*      +--------------------------------------------------------+                                                        */
    /*    1 | SPECIES  |SEPALLENGTH|SEPALWIDTH|PETALLENGTH|PETALWIDTH|                                                        */
    /*      +----------+-----------+----------+-----------+----------+                                                        */
    /*    2 | Setosa   |   60      |    33    |    14     |    2     |                                                        */
    /*      +----------+-----------+----------+-----------+----------+                                                        */
    /*       ...                                                                                                              */
    /*      +----------+-----------+----------+-----------+----------+                                                        */
    /*  150 | Virginica|   44      |    25    |    22     |    3     |                                                        */
    /*      +----------+-----------+----------+-----------+----------+                                                        */
    /*                                                                                                                        */
    /*  [IRIS]                                                                                                                */
    /*                                                                                                                        */
    /**************************************************************************************************************************/


    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
