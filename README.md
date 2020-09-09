# utl-remove-first-occurances-of-the-match-normalization
Remove first occurances of the match normalization
    SAS-L: Remove first occurances of the match normalization                                                  
                                                                                                               
    GitHub                                                                                                     
    https://tinyurl.com/y6y8d794                                                                               
    https://github.com/rogerjdeangelis/utl-remove-first-occurances-of-the-match-normalization                  
                                                                                                               
    * Not sure I totally understand this.                                                                      
                                                                                                               
    Basically I remove first occurances of the match in                                                        
    the string text?                                                                                           
                                                                                                               
    My output dataset is slighty different.                                                                    
    The data structure is normalized, and depending on what comes next,                                        
    may be a more useful structure since future analysis does not require scanning                             
    a string of variable number of words?                                                                      
                                                                                                               
    /*                   _                                                                                     
    (_)_ __  _ __  _   _| |_                                                                                   
    | | `_ \| `_ \| | | | __|                                                                                  
    | | | | | |_) | |_| | |_                                                                                   
    |_|_| |_| .__/ \__,_|\__|                                                                                  
            |_|                                                                                                
    */                                                                                                         
                                                                                                               
    * I normalized for easier processing;                                                                      
    data have;                                                                                                 
      retain rec wrd match;                                                                                    
      infile datalines truncover dlm=',';                                                                      
      length text match $255;                                                                                  
      input text match $;                                                                                      
      rec=_n_;                                                                                                 
      do i=1 to countw(text);                                                                                  
         wrd=scan(text,i);                                                                                     
         output;                                                                                               
      end;                                                                                                     
      drop text i;                                                                                             
      datalines;                                                                                               
      jane sally, jane                                                                                         
      fred sally fred sally, fred                                                                              
      bill bill fred, bill                                                                                     
       jim bill bill, bill                                                                                     
      ;                                                                                                        
    run;                                                                                                       
                                                                                                               
    WORK.HAVE total obs=9                                                                                      
                                |                                                                              
      REC     WRD        MATCH  | REC  WANT                                                                    
                                |                                                                              
       1     jane        jane   |               * remove jane first match                                      
       1     sally       jane   | 1     sally                                                                  
                                                                                                               
       2     fred        fred   |               * remove fred first match                                      
       2     sally       fred   | 2     sally                                                                  
       2     fred        fred   | 2     fred                                                                   
       2     sally       fred   | 2     sally                                                                  
                                |                                                                              
       3     bill        bill                   * remove only the first bill                                   
       3     bill        bill   | 3     bill                                                                   
       3     fred        bill   | 3     fred                                                                   
                                |                                                                              
       4     jim         bill   | 4     jim                                                                    
       4     bill        bill                   * remove only the first bill                                   
       4     bill        bill   | 4     bill                                                                   
                                |                                                                              
                                                                                                               
     /*           _               _                                                                            
      ___  _   _| |_ _ __  _   _| |_                                                                           
     / _ \| | | | __| `_ \| | | | __|                                                                          
    | (_) | |_| | |_| |_) | |_| | |_                                                                           
     \___/ \__,_|\__| .__/ \__,_|\__|                                                                          
                    |_|                                                                                        
    */                                                                                                         
                                                                                                               
                                                                                                               
     WORK.WANT total obs=8                                                                                     
                                                                                                               
      REC     WRD                                                                                              
                                                                                                               
       1     sally                                                                                             
                                                                                                               
       2     sally                                                                                             
       2     fred                                                                                              
       2     sally                                                                                             
                                                                                                               
       3     bill                                                                                              
       3     fred                                                                                              
                                                                                                               
       4     jim                                                                                               
       4     bill                                                                                              
                                                                                                               
    /*                                                                                                         
     _ __  _ __ ___   ___ ___  ___ ___                                                                         
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|                                                                        
    | |_) | | | (_) | (_|  __/\__ \__ \                                                                        
    | .__/|_|  \___/ \___\___||___/___/                                                                        
    |_|                                                                                                        
    */                                                                                                         
                                                                                                               
    data want;                                                                                                 
      retain beenthere 0 rec ;                                                                                 
      set have;                                                                                                
      by rec;                                                                                                  
      if wrd=match and beenthere=0 then do;                                                                    
         beenthere=1;                                                                                          
         delete;                                                                                               
      end;                                                                                                     
      if last.rec then beenthere=0;                                                                            
      drop beenthere match;                                                                                    
    run;quit;                                                                                                  
                                                                                                               
                                                                                                               
                                                                                                               
