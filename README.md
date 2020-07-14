# utl-hash-of-hashes-left-join-four-tables
Hash of hashes dynamically left join four tables
    Hash of hashes dynamically left join four tables                                                                                         
                                                                                                                                             
        Two Solutions                                                                                                                        
            a. proc sql                                                                                                                      
            b. HOH hash of hashes                                                                                                            
                                                                                                                                             
    github                                                                                                                                   
    https://tinyurl.com/yc8vx99b                                                                                                             
    https://github.com/rogerjdeangelis/utl-hash-of-hashes-left-join-four-tables                                                              
                                                                                                                                             
    related repos                                                                                                                            
    https://tinyurl.com/ybx47ogz                                                                                                             
    https://github.com/rogerjdeangelis?tab=repositories&q=hash+in%3Aname&type=&language=                                                     
                                                                                                                                             
    SAS Forum                                                                                                                                
    https://tinyurl.com/y7gxrexg                                                                                                             
    https://communities.sas.com/t5/SAS-Programming/Dynamic-Multi-Table-Merge-Using-Hash-of-Hashes/m-p/668886                                 
                                                                                                                                             
    Nice explanation by (see link for original explanation)                                                                                  
    Mark Keintz                                                                                                                              
    https://communities.sas.com/t5/user/viewprofilepage/user-id/31461                                                                        
                                                                                                                                             
    'But first, I think you are assuming that the result_measure variables are not                                                           
    retained in the datastep as it iterates through have5 (i.e. they are result                                                              
    to missing values automatically).  But this is wrong, for two reasons:                                                                   
                                                                                                                                             
    Variables named in any SET statement are retained.  One rarely notices this                                                              
    because ordinarily ever iteration of the data step has a SET statement which                                                             
    would overwrite the preceding values of variables like result_measure1,                                                                  
    rm2, and rm3.  But you have no such SET statement.                                                                                       
    Instead you have  IF 0 then SET haveKey have1 have2 have3                                                                                
    which (because it is a SET) tells SAS to retain the variables in the datasets, but                                                       
    (because it is "IF 0 THEN ..." it is never acted on, so the values are not replaced.                                                     
                                                                                                                                             
    Variables retrieved in a hash FIND method are not modified until the next SUCCESSFUL                                                     
    find (or a SET or other explicit modification of the variables).   So if a given                                                         
    member_id has successful find's only for HAVE1 and HAVE3, it will be updated for                                                         
    rm1 and m3, but keep the old (previous successful) value for m2.                                                                         
    This means that, before you iterate through the hash objects for each table value,                                                       
    you should set the result_measures to a default value (I use missing values below,                                                       
    because it's easy to code "call missing ...").'                                                                                          
                                                                                                                                             
                                                                                                                                             
                      Left Join on ID                              | RULES  Match on haveKey ID                                              
                                                                   | Output                                                                  
            HavKey                                                 |                                                                         
            Table        HAVE1          HAVe2          HAVE3       |                                                                         
                      ============   ============   ============   |   ID     RM_1  RM_2  RM_3                                               
              ID        ID    RM_1     ID    RM_2     ID    RM_3   |                                                                         
                                                                   | 164-111   8     1                                                       
            164-111   164-111  8      164-111  1                   | 297-888   8     1                                                       
            297-888   297-888  8      297-888  1                   | 495-111   1                                                             
            495-111   495-111  1                                   | 562-888   7     2     5                                                 
            562-888   562-888  7      562-888  2     562-888  5    | 697-111   7     7     9                                                 
            697-111   697-111  7      697-111  7     697-111  9    | 833-888   5                                                             
            833-888   833-888  5                     344-111  1    | 344-111               1                                                 
            344-111                                                |                                                                         
                                                                                                                                             
    /*                   _                                                                                                                   
    (_)_ __  _ __  _   _| |_                                                                                                                 
    | | `_ \| `_ \| | | | __|                                                                                                                
    | | | | | |_) | |_| | |_                                                                                                                 
    |_|_| |_| .__/ \__,_|\__|                                                                                                                
            |_|                                                                                                                              
    */                                                                                                                                       
                                                                                                                                             
    data tbls;                                                                                                                               
       input table $ ;                                                                                                                       
       datalines;                                                                                                                            
    have1                                                                                                                                    
    have2                                                                                                                                    
    have3                                                                                                                                    
    ;                                                                                                                                        
    run;                                                                                                                                     
                                                                                                                                             
    data  havkey(drop=rm_1) have1 have2(rename=rm_1=rm_2) have3(rename=rm_1=rm_3);                                                           
       input tbl id $ rm_1 $;                                                                                                                
       select (tbl);                                                                                                                         
         when (1) output have1;                                                                                                              
         when (2) output have2;                                                                                                              
         when (3) output have3;                                                                                                              
         when (4) output havkey;                                                                                                             
       end;                                                                                                                                  
       drop tbl;                                                                                                                             
    cards4;                                                                                                                                  
    1 164-111 8                                                                                                                              
    1 297-888 8                                                                                                                              
    1 495-111 1                                                                                                                              
    1 562-888 7                                                                                                                              
    1 697-111 7                                                                                                                              
    1 833-888 5                                                                                                                              
    2 164-111 1                                                                                                                              
    2 297-888 1                                                                                                                              
    2 562-888 2                                                                                                                              
    2 697-111 7                                                                                                                              
    3 344-111 1                                                                                                                              
    3 562-888 5                                                                                                                              
    3 697-111 9                                                                                                                              
    4 164-111 .                                                                                                                              
    4 297-888 .                                                                                                                              
    4 495-111 .                                                                                                                              
    4 562-888 .                                                                                                                              
    4 697-111 .                                                                                                                              
    4 833-888 .                                                                                                                              
    4 344-111 .                                                                                                                              
    ;;;;                                                                                                                                     
    run;quit;                                                                                                                                
                                                                                                                                             
                                                                                                                                             
    Tables with data                                                                                                                         
                                                                                                                                             
    Up to 40 obs WORK.TBLS total obs=3                                                                                                       
                                                                                                                                             
    Obs    TABLE                                                                                                                             
                                                                                                                                             
     1     have1                                                                                                                             
     2     have2                                                                                                                             
     3     have3                                                                                                                             
                                                                                                                                             
                                                                                                                                             
    Key Table              Three SAS Tables                                                                                                  
    HavKey                                                                                                                                   
    Table        HAVE1          HAVe2          HAVE3                                                                                         
    ========= ============   ============   ============                                                                                     
      ID        ID    RM_1     ID    RM_2     ID    RM_3                                                                                     
                                                                                                                                             
    164-111   164-111  8      164-111  1     562-888  5                                                                                      
    297-888   297-888  8      297-888  1     697-111  9                                                                                      
    495-111   495-111  1      562-888  2     344-111  1                                                                                      
    562-888   562-888  7      697-111  7                                                                                                     
    697-111   697-111  7                                                                                                                     
    833-888   833-888  5                                                                                                                     
    344-111                                                                                                                                  
                                                                                                                                             
    /*          _                                                                                                                            
     _ __ _   _| | ___  ___                                                                                                                  
    | `__| | | | |/ _ \/ __|                                                                                                                 
    | |  | |_| | |  __/\__ \                                                                                                                 
    |_|   \__,_|_|\___||___/                                                                                                                 
                                                                                                                                             
    */                                                                                                                                       
                                                                                                                                             
    MATCH ON HAVKEY ID COLUMN                                                                                                                
                                                                                                                                             
                                                                                                                                             
                                                                                                                                             
               Left Join on ID                              | RULES  Match on haveKey ID                                                     
                                                            | Output                                                                         
     HavKey                                                 |                                                                                
     Table        HAVE1          HAVe2          HAVE3       |                                                                                
               ============   ============   ============   |   ID     RM_1  RM_2  RM_3                                                      
       ID        ID    RM_1     ID    RM_2     ID    RM_3   |                                                                                
                                                            | 164-111   8     1                                                              
     164-111   164-111  8      164-111  1                   | 297-888   8     1                                                              
     297-888   297-888  8      297-888  1                   | 495-111   1                                                                    
     495-111   495-111  1                                   | 562-888   7     2     5                                                        
     562-888   562-888  7      562-888  2     562-888  5    | 697-111   7     7     9                                                        
     697-111   697-111  7      697-111  7     697-111  9    | 833-888   5                                                                    
     833-888   833-888  5                     344-111  1    | 344-111               1                                                        
     344-111                                                |                                                                                
                                                                                                                                             
    /*           _               _                                                                                                           
      ___  _   _| |_ _ __  _   _| |_                                                                                                         
     / _ \| | | | __| `_ \| | | | __|                                                                                                        
    | (_) | |_| | |_| |_) | |_| | |_                                                                                                         
     \___/ \__,_|\__| .__/ \__,_|\__|                                                                                                        
                    |_|                                                                                                                      
    */                                                                                                                                       
    WORK.WANT total obs=7                                                                                                                    
                                                                                                                                             
        ID       RM_1    RM_2    RM_3                                                                                                        
                                                                                                                                             
      164-111     8       1                                                                                                                  
      297-888     8       1                                                                                                                  
      344-111                     1                                                                                                          
      495-111     1                                                                                                                          
      562-888     7       2       5                                                                                                          
      697-111     7       7       9                                                                                                          
      833-888     5                                                                                                                          
                                                                                                                                             
    /*         _       _   _                                                                                                                 
     ___  ___ | |_   _| |_(_) ___  _ __  ___                                                                                                 
    / __|/ _ \| | | | | __| |/ _ \| `_ \/ __|                                                                                                
    \__ \ (_) | | |_| | |_| | (_) | | | \__ \                                                                                                
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|___/                                                                                                
               _                                                                                                                             
     ___  __ _| |                                                                                                                            
    / __|/ _` | |                                                                                                                            
    \__ \ (_| | |                                                                                                                            
    |___/\__, |_|                                                                                                                            
            |_|                                                                                                                              
    */                                                                                                                                       
                                                                                                                                             
                                                                                                                                             
    proc sql;                                                                                                                                
      create                                                                                                                                 
           table want as                                                                                                                     
      select                                                                                                                                 
          l.id                                                                                                                               
          ,m.rm_1                                                                                                                            
          ,n.rm_2                                                                                                                            
          ,o.rm_3                                                                                                                            
      from                                                                                                                                   
          havkey as l                                                                                                                        
          left join have1 as m on l.id = m.id                                                                                                
          left join have2 as n on l.id = n.id                                                                                                
          left join have3 as o on l.id = o.id                                                                                                
      order                                                                                                                                  
          by id                                                                                                                              
    ;quit;                                                                                                                                   
                                                                                                                                             
    /*           _                                                                                                                           
    | |__   ___ | |__                                                                                                                        
    | `_ \ / _ \| `_ \                                                                                                                       
    | | | | (_) | | | |                                                                                                                      
    |_| |_|\___/|_| |_|                                                                                                                      
                                                                                                                                             
    */                                                                                                                                       
                                                                                                                                             
                                                                                                                                             
    proc datasets lib=work;                                                                                                                  
      delete wanthh;                                                                                                                         
    run;quit;                                                                                                                                
                                                                                                                                             
    data wanthh;                                                                                                                             
      if 0 then set havkey have1 have2 have3:;                                                                                               
                                                                                                                                             
      if _n_ = 1 then do;                                                                                                                    
                                                                                                                                             
        dcl hash HoH();                                                                                                                      
          HoH.definekey("table");                                                                                                            
          HoH.definedata("h","table");                                                                                                       
          HoH.definedone();                                                                                                                  
        dcl hiter HoHi("HoH");                                                                                                               
                                                                                                                                             
        do until (eof);                                                                                                                      
          set tbls end=eof;                                                                                                                  
          dcl hash h(dataset:table);                                                                                                         
            h.definekey("id");                                                                                                               
            h.definedata(all:"yes");                                                                                                         
            h.definedone();                                                                                                                  
          HoH.add();                                                                                                                         
       end;                                                                                                                                  
      end;                                                                                                                                   
                                                                                                                                             
      set havkey;                                                                                                                            
      call missing(of rm_:); /*Add this: Initialize these variables */                                                                       
      do while (HoHi.next() = 0);                                                                                                            
          rc_h = h.find();                                                                                                                   
      end;                                                                                                                                   
                                                                                                                                             
      /** HoH.output(dataset:"test");  Drop this **/                                                                                         
      /** h.output(dataset:"test_h");  Drop this **/                                                                                         
    run;                                                                                                                                     
                                                                                                                                             
                                                                                                                                             
