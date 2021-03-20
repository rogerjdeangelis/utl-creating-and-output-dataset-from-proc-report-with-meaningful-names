# utl-creating-and-output-dataset-from-proc-report-with-meaningful-names
Creating and output dataset from proc report with meaningful names

    Creating and output dataset from proc report with meaningful names

      Two solutions

          a. Using proc report ods output dataset
          b. using proc report listing (more flexible captures formatting and is an image of the listing)


    GitHub
    https://tinyurl.com/ayexzsyd
    https://github.com/rogerjdeangelis/utl-creating-and-output-dataset-from-proc-report-with-meaningful-names


    Related to
    see this post for atypical problem with tabulate (painting yourself into a corner)
    https://tinyurl.com/y6juu9zx
    https://communities.sas.com/t5/SAS-Programming/Convert-proc-tabulate-to-proc-report-and-show-each-value-of/m-p/679656

    https://tinyurl.com/y5fog6kq
    https://github.com/rogerjdeangelis/utl_ods-output-datasets-for-a-very-complex-proc-tabulate

    macros
    https://tinyurl.com/y9nfugth
    https://github.com/rogerjdeangelis/utl-macros-used-in-many-of-rogerjdeangelis-repositories


    *_                   _
    (_)_ __  _ __  _   _| |_
    | | '_ \| '_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    ;

    proc report data=sashelp.cars(where=(make in ('Dodge' ,'Ford' ,'GMC'))) nowd missing;
    column Make drivetrain, (MSRP=MSRPN MSRP=MSRPmean MSRP=MSRPstd);
    define Make / Group;
    define drivetrain / across;
    define MSRPN / analysis N "N" format=2.;
    define MSRPmean / analysis mean "Mean";
    define MSRPstd / analysis std "Std";
    run;quit;

    /*
                            All                    Front                    Rear
    MAKE            N      Mean       Std   N      Mean       Std   N      Mean       Std
    Dodge           2   $32,448      $301   7   $19,495    $3,991   4   $34,985   $31,231
    Ford            4   $31,800    $7,903  12   $21,585    $7,873   7   $23,736    $5,891
    GMC             3   $33,661   $11,091   2   $33,808    $2,712   3   $22,629    $5,282
    */

    *
     _ __  _ __ ___   ___ ___  ___ ___
    | '_ \| '__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|                  _           _       _                 _
      __ _      ___   __| |___    __| | __ _| |_ __ _ ___  ___| |_
     / _` |    / _ \ / _` / __|  / _` |/ _` | __/ _` / __|/ _ \ __|
    | (_| |_  | (_) | (_| \__ \ | (_| | (_| | || (_| \__ \  __/ |_
     \__,_(_)  \___/ \__,_|___/  \__,_|\__,_|\__\__,_|___/\___|\__|

    ;


    %array(old,values=c2-c10);
    %array(new,values=allN allAvg allStd frontN frontAvg frontStd rearN rearAvg rearStd);

    * if you prefer you can generate the rename phrase instead of using do_over;
    data _null_;
         %do_over(old new,phrase= %str(put "_?old_ = ?new" ;  ))
    run;quit;

    /*
    _c2_ = allN
    _c3_ = allAvg
    _c4_ = allStd
    _c5_ = frontN
    _c6_ = frontAvg
    _c7_ = frontStd
    _c8_ = rearN
    _c9_ = rearAvg
    _c10_ = rearStd
    */

    ods select all;;
    ods output report=want_ods(rename=(
         %do_over(old new,phrase= %str(_?old_ = ?new  ))
         ))
    ;
    proc report data=sashelp.cars(where=(make in ('Dodge' ,'Ford' ,'GMC'))) nowd missing;
    column Make drivetrain, (MSRP=MSRPN MSRP=MSRPmean MSRP=MSRPstd);
    define Make / Group;
    define drivetrain / across;
    define MSRPN / analysis N "N" format=2.;
    define MSRPmean / analysis mean "Mean";
    define MSRPstd / analysis std "Std";
    run;quit;

    * if you want to clear the macro arrays;
    %arraydelete(old);
    %arraydelete(new);

    *_        _ _     _   _
    | |__    | (_)___| |_(_)_ __   __ _
    | '_ \   | | / __| __| | '_ \ / _` |
    | |_) |  | | \__ \ |_| | | | | (_| |
    |_.__(_) |_|_|___/\__|_|_| |_|\__, |
                                  |___/
    ;

    %utl_odsrpt(setup);

    proc report data=sashelp.cars(where=(make in ('Dodge' ,'Ford' ,'GMC'))) nowd missing formchar="|" noheader box;
    title "|make|allN|allAvg|allStd|frontN|frontAvg|frontStd|rearN|rearAvg|rearStd|";
    column Make drivetrain, (MSRP=MSRPN MSRP=MSRPmean MSRP=MSRPstd);
    define Make / Group;
    define drivetrain / across;
    define MSRPN / analysis N "N" format=2.;
    define MSRPmean / analysis mean "Mean";
    define MSRPstd / analysis std "Std";
    run;quit;

    %utl_odsrpt(outdsn=want_lst);

    *            _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| '_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|  _           _       _                 _
      __ _      ___   __| |___    __| | __ _| |_ __ _ ___  ___| |_
     / _` |    / _ \ / _` / __|  / _` |/ _` | __/ _` / __|/ _ \ __|
    | (_| |_  | (_) | (_| \__ \ | (_| | (_| | || (_| \__ \  __/ |_
     \__,_(_)  \___/ \__,_|___/  \__,_|\__,_|\__\__,_|___/\___|\__|

    ;


    WANT_ODS total obs=3

       MAKE     ALLN     ALLAVG      ALLSTD     FRONTN    FRONTAVG    FRONTSTD    REARN     REARAVG     REARSTD    _BREAK_

       Dodge      2     32447.50      300.52       7      19495.00     3990.67      4      34985.00    31231.26
       Ford       4     31800.00     7903.19      12      21584.58     7873.39      7      23735.71     5890.64
       GMC        3     33660.67    11090.86       2      33807.50     2711.75      3      22629.00     5282.03


    *_        _ _     _   _
    | |__    | (_)___| |_(_)_ __   __ _
    | '_ \   | | / __| __| | '_ \ / _` |
    | |_) |  | | \__ \ |_| | | | | (_| |
    |_.__(_) |_|_|___/\__|_|_| |_|\__, |
                                  |___/
    ;

    Up to 40 obs from WANT_LST total obs=3

    Obs    MAKE     ALLN    ALLAVG    ALLSTD    FRONTN    FRONTAVG    FRONTSTD    REARN    REARAVG    REARSTD

     1     Dodge      2      32448       301       7        19495       3991        4       34985      31231
     2     Ford       4      31800      7903      12        21585       7873        7       23736       5891
     3     GMC        3      33661     11091       2        33808       2712        3       22629       5282
