---
title: "SAS Learning Notes"
tags: SAS
---

SAS Learning Notes

<!--more-->

- [SAS BASE](#sas-base)
  - [Setting System Options](#setting-system-options)
  - [**PROC IMPORT** - Good for reading CSV/XLSX/ACCESS](#proc-import---good-for-reading-csvxlsxaccess)
  - [**PROC CONTENTS** & **LABEL**](#proc-contents--label)
  - [SAS Format, Missing Values, Naming Rules and Path](#sas-format-missing-values-naming-rules-and-path)
    - [Comments](#comments)
    - [Missing Values](#missing-values)
    - [Naming Rules](#naming-rules)
    - [**LIBNAME** on SAS library path](#libname-on-sas-library-path)
    - [Date Format](#date-format)
    - [Numbers Format](#numbers-format)
  - [SAS Functions](#sas-functions)
    - [String Variables](#string-variables)



# SAS BASE

## Setting System Options
 - You can see a list of system options and their current values by opening the SAS system options window
 - Using the OPTIONS Procedure:

```SAS
    PROC OPTIONS; 
    RUN;
```
 - Options Statement: affect all following steps
    - General format: 
    
    **Options**   *options_1(=value_1)  options_2(=value_2)....;*

    **Keyword**   *a list of options*
  
```SAS
    OPTIONS LINESIZE=80 NODATE;
```
 - Common Options
   - **Center**/NOCenter, **DATE**/NODATE, **Number**/NoNumber (PageNumer)
   - Linesize=n (Max length of a row, range = 64 - 256)
   - Pagesize=n (Max number of rows, range = 15 - 32767)
   - Pageno=n (Page Number of the starting page, default Pageno = 1)
   - Rightmargin=n; Leftmargin=n; Topmargin=n; Bottommargin=n;
   - Yearcutoff=yyyy (Define the earliest year within a hundred years so that years can be written as 2 digit. Default Yearcuttoff = 1920. Won't affect if the year is 4 sigits.)
   - Orientation=**Portrait**/landscape
  

## **PROC IMPORT** - Good for reading CSV/XLSX/ACCESS
 - Scan first 20 rows of the dataset and assign variable type based on this by default.
 - Automatically assign string length, assign numeric with the format of **BEST32**
 - Two continuous delimiter will recognize the cell as missing
 - While a read-in row does not have enough data to assign to each of the variables, the following ones will berecognized as missing
  
 - General form: 
  
```SAS
    PROC IMPROT DATAFILE='filename' 
    OUT=dataset;
```
 - DBMS identifier & Replace: DBMS defines the dataset file type, ie. .csv(DBMS = CSV) or .txt(DSMB = TAB). If the file is not a csv file or txt file, then **DSMB = Option** is needed after **IMPORT**. Also in case duplicated filename, **DSMB = REPLACE** is used to cover the file with same names.

```SAS
    PROC IMPROT DATAFILE='filename' 
    OUT=dataset 
    DBMS=REPLACE;
```

 - Other common options:
  
```SAS
    /*options of PROC IMPORT*/
    Datarows=n  
    *Read in data from row = n, default Datarows = 1;
    Delimiters='delimiter'  *Used for situations that delimiter aren;t comma,tab,space. Default Delimiter = 'space';
    Getnames=NO  
    *Order SAS not to use first row as variable name, instead variable namees are VAR1,VAR2, etc. Default Getnames = YES;
    GUESSINROWS=N  
    *Use first N rows to define variable types, default GUESSINROWS = 20. Note: Not followed after PROC IMPORT but star another line.
```
```SAS
    PROC IMPORT 
    DATAFIL='/folders/myfolders/sasuser.v94/NHANES_sample.csv'
    OUT=DATA.NHANES 
    DBMS=CSV REPLACE;
    GUESSINGROWS=2000;
    RUN;
```
 - Import Excel file
     - **DSMB = Excel** uses first 8 rows to define variable type by default. **DSMB = XLS/XLSX** uses all lines. 
     - ex. **RANGE = "sheetname$UL:R2C1"**, indicates tange to certain cell.

```SAS 
    PROC IMPORT 
    DATAFILE='filename.xls'
    OUT=data-set
    DBMS=Excel/XLS
    SHEET=”sheetname“
    RANGE="sheetname$UL:LR"
    GETNAMES=NO REPLACE;
```  
 - Import Access file
   - Must have **DSMB = Opions** and **Database = ''**

```SAS
    PROC IMPORT 
    DATATABLE='tablename.mdb'
    OUT=dataset
    DBMS=ACCESS/ACCESS97 REPLACE;
    DATABASE=‘databasepath’;
```
## **PROC CONTENTS** & **LABEL**
 - **PROC CONTENTS** is used for checking the characteristics of the dataset

```SAS
    PROC CONTENTS 
    DATA=dataset;
    RUN;
```
 - **LABEL** 
   - There are two types of **LABEL**, both cannot exceed 256 characters.

```SAS
    LABEL=dataset *Label the dataset;
    LABEL statement *Label each variables;
    /* For each variables, if it is labeld at DATA step, then the label will be store in dataset and print during PROC CONTENTS. However, if it is labeled at PROC step, then it won't be stored in dataset but only plausible for current procedure.*/
```

## SAS Format, Missing Values, Naming Rules and Path
 
### Comments

```SAS
    *print the comments;
    /*print the comments*/
```
### Missing Values
 - Missing Strings are shown as empty spaces. Missing Numbers are shown as single dots **(.)**.

### Naming Rules
 - The dataset and variable name cannot exceed 32 character. SAS library name cannot exceed 8 character. Label length cannot exceed 256 characters.
 - All names have to start with letters or underscore **( _ )**.
 - All names can only contain letters, numbers or underscore.
 - SAS is not case-sensitive, names can include both upper and lower cases.
  
### **LIBNAME** on SAS library path

```SAS
    LIBNAME libname ‘your_sas_data_library_path’;
``` 
### Date Format
  - Input Format **(INFORMAT, Used in INPUT  = )**
  
  ```SAS 
    ANYDTDTE w. /*Read in date in any format (width:5-32;default:9) ex. 1jan1961:ANYDTDTE 10.*/
    DATE w. /*Read in ddmmmyy or ddmmmyyyy (width:7-32;default:7),ex. 1jan1961:DATE10.*/
    DDMMYY w. /*Read in ddmmyy or ddmmyyyy (width:6-32;default:6), ex.02/01/61:DDMMYY 8.*/
    MMDDYY w. /*Read in mmddyy or mmddyyyy (width:6-32;default:6), ex.02-01-61:MMDDYY 8.*/
    JULIAN w. /*Read in yyddd or yyyyddd (width:5-32;default:5), ex.61001:JULAN7.*/
  ```
  - Output Format **(FORMAT, Used in PUT = )**

```SAS
    DATE w. /*Output as ddmmmyy (width:5-9;default:7), ex.8966:DATE7.*/
    EURDFDD w. /*Output as dd.mm.yy (width:2-10;default:8) */
    MMDDYY w. /*Output as mmddyy or mmddyyyy (width:2-10;default:8) */
    WEEKDATE w. /*Output as day-of-week,month-name dd,yy/yyyy  (width:3-37;default:29) */
    WORDDATE w. /*Output as month-name dd,yyyy (width:2-10;default:8) */
    JULIAN w. /*Output as JULIAN (width:5-7;default:5) */
    DATETIME w.d /*Output as ddmmmyy:hh:mm:ss.ss (width:7-40;default:16) */
    TIME w.d  /*Output Ashh:mm:ss.ss (width:2-20;default:8) */
    DAY w. /*Output the day of the output months (width:2-32;default:2) */
```

### Numbers Format

 ```SAS
    BEST w. /*SAS choose the best format (default), width:1-32,default:12*/
    COMMA w.d /*Decimal numbers separarted by comma every other three digits width:2-32,default:6*/
    DOLLAR w.d /*Add Dollar sign($) in from, separarted by comma every other three digits, width:2-32,default:6*/
    Ew.d /*Scientific notation, width:7-32,default:12*/
    w.d /*Standard format, width:1-32,default:none*/
    PD w.d /*Packed decimal,w define numbers of bytes, width:1-16,default:1*/
 ```


## SAS Functions

### String Variables
 - Return to certain locations of a string 

```SAS
    ANYALNUM(arg,start)  /*Returns the first(if missing start) or from stat the location first showing Arabic Alphabet or Arabic numeral*/
    ANYALPHA(arg,start)   /*Returns the first(if missing start) or from stat the location first showing Arabic Alphabet*/
    ANADIGIT(arg,start)   /*Returns the first(if missing start) or from stat the location first showing Arabic numeral*/
    ANYSPACE(arg,start)   /*Returns the first(if missing start) or from stat the location first showing empty space*/
    INDEX(arg,'string')   /*Returns the starting location of the string in an argument*/
    LENTGH(arg) /*Returns the length of the argument(excluding space at the end, the length of missing = 1)*/
```

 - Connecting strings
  
```SAS
    CAT(arg-1,arg-2,...,arg-n) /*将两个或多个字符串连接起来，保留首位和中间的空格，两个是同"||"*/
    CATS(arg-1,arg-2,...,arg-n)  /*将两个或多个字符串连接起来，删掉首位和中间的空格*/
    CATX('separator-string',arg-1,arg-2,...,arg-n)  /*将两个或多个字符串连接起来，删掉首位和中间的空格，在参数中间插入指定的分隔符*/
```
