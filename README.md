# utl-using-dosubl-to-avoid-macros-and-add-an-error-checking-log
Using dosubl to avoid macros and add an error checking log
    Using dosubl to avoid macros and add an error checking log

    github
    https://tinyurl.com/wjjtxyq
    https://github.com/rogerjdeangelis/utl-using-dosubl-to-avoid-macros-and-add-an-error-checking-log

    other dosub repos
    https://github.com/rogerjdeangelis?tab=repositories&q=in%3Aname+dosubl&type=&language=

    SAS Forum
    https://communities.sas.com/t5/SAS-Programming/Macro/m-p/634837

    Problem
       Create table "BMW' and "AUDI" from SASHELP.CARS

    *_                   _
    (_)_ __  _ __  _   _| |_
    | | '_ \| '_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    ;

     data have;
        set sashelp.cars(keep=m:);
     run;quit;


     WORK.HAVE total obs=428

                                                                          MPG_
      MAKE     MODEL                                MSRP    MPG_CITY    HIGHWAY

      Acura    MDX                                 36945       17          23
      Acura    RSX Type S 2dr                      23820       24          31
      Acura    TSX 4dr                             26990       22          29
      Acura    TL 4dr                              33195       20          28
      Acura    3.5 RL 4dr                          43755       18          24
      Acura    3.5 RL w/Navigation 4dr             46100       18          24
      Acura    NSX coupe 2dr manual S              89765       17          24
      Audi     A4 1.8T 4dr                         25940       22          31
      Audi     A41.8T convertible 2dr              35940       23          30
      Audi     A4 3.0 4dr                          31840       20          28
      Audi     A4 3.0 Quattro 4dr manual           33430       17          26
      Audi     A4 3.0 Quattro 4dr auto             34480       18          25
      Audi     A6 3.0 4dr                          36640       20          27
      Audi     A6 3.0 Quattro 4dr                  39640       18          25
     ...

    *            _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| '_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    ;
    WORK.LOG total obs=2

      CAR     RC    CAROBS                  STATUS

      Audi     0      19      table Audi created with 19 records
      BMW      0      20      table BMW created with 20 records


    Up to 40 obs from BMW total obs=20

                                                                     MPG_
    Obs    MAKE    MODEL                       MSRP    MPG_CITY    HIGHWAY

      1    BMW     X3 3.0i                    37000       16          23
      2    BMW     X5 4.4i                    52195       16          22
      3    BMW     325i 4dr                   28495       20          29
      4    BMW     325Ci 2dr                  30795       20          29
      5    BMW     325Ci convertible 2dr      37995       19          27
     ...


    Up to 40 obs from AUDI total obs=19

                                                                              MPG_
    Obs    MAKE    MODEL                                MSRP    MPG_CITY    HIGHWAY

      1    Audi    A4 1.8T 4dr                         25940       22          31
      2    Audi    A41.8T convertible 2dr              35940       23          30
      3    Audi    A4 3.0 4dr                          31840       20          28
      4    Audi    A4 3.0 Quattro 4dr manual           33430       17          26
      5    Audi    A4 3.0 Quattro 4dr auto             34480       18          25
      6    Audi    A6 3.0 4dr                          36640       20          27
      7    Audi    A6 3.0 Quattro 4dr                  39640       18          25
    ...
    *
     _ __  _ __ ___   ___ ___  ___ ___
    | '_ \| '__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    ;

    data log;

      length car $32;

      do car='Audi','BMW';

         call symputx('car',car);

         rc=dosubl('
             proc sql;
                create
                    table &car as
                select
                    *
                from
                   have
                where
                   make = "&car";
             quit;

             %let carobs=&sqlobs;
         ');

         carobs=symgetn("carobs");

         if symgetn("carobs") > 0 then
               status = catx(" ","table", car, "created with", carobs, "records");
         else  status=catx(" ","Error table", car, "not created");
         output;
      end;

    run;quit;


