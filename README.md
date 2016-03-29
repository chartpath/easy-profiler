# simple-profiler
Simple customizable profiler for your node.js application. Debug and find out which part of your code is taking more time, more cpu in an organized way.

## How to install?
    npm install simple-profiler

## API List
##### &lt;returns Null&gt; SP.begin(&lt;key&gt;);
 Call this function whenever you want to begin a task
##### &lt;returns Object&gt; SP.end(&lt;key&gt;, &lt;doPrintInConsole&gt;);
 You just need to call SP.end(key) once your task ends. You can call this function only after calling SP.begin(key). You can pass second params as true/false if you want to print the report immediately.
 
##### &lt;returns Array of Object&gt; SP.report(true);
 If you plan to get consolidated profiling report at one once then call SP.report(true) once everything ends

## How to use?

#### Basic example
 In the example below we will find out how much time it takes to read the file. We need to call SP.begin(String) at the beginning and call SP.end(String) once reading completes is done
    
    require('simple-profiler');
    
    SP.begin("READ-FILE");
        
    fs.readFile('/etc/hosts', 'utf8', function (err,data) {
        if (err) {
         return console.log(err);
        }
        
        console.log(data);
        SP.end("READ-FILE", true); // second param is true that means it will print the info immediatly
    });
 
#### Nice way
In the example below we will find out how much time it takes to read the file.
     
    require('simple-profiler');
    var fs = require('fs');
    
    // Keep all the keys at one place, plan what are the functions you are going to profile
    SP.keys.add({
       READ_FROM_HOST_FILE: "Read from host file",
       HTTP_CALL_TO_WIKI_PAGE: "Http call to wiki page"
    })
    
    // SAVE_IN_ORACLE_DB job starts here logically right?
    SP.begin(SP.keys.READ_FROM_HOST_FILE);
    fs.readFile('/etc/hosts', 'utf8', function (err,data) {
        if (err) {
         return console.log(err);
        }
        
        console.log(data);
        
        // SAVE_IN_ORACLE_DB job ends here
        SP.end(SP.keys.READ_FROM_HOST_FILE, true);
    });
    
#### Want profiling report at the end only
 In the example below we will find out how much time it takes to read the file. Also find out how much time is taken by two consecutive for loop
 
    require('simple-profiler');
    var fs = require('fs');
    
    // Keep all the keys at one place, plan what are the functions you are going to profile
    SP.keys.add({
      READ_FROM_HOST_FILE: "Read from host file",
      JUST_FOR_LOOP_1: "Just a forloop for testing 1",
      JUST_FOR_LOOP_2: "Just a forloop for testing 2"
    });
    
    // SAVE_IN_ORACLE_DB job starts here logically right?
    SP.begin(SP.keys.READ_FROM_HOST_FILE);
    
    fs.readFile('/etc/hosts', 'utf8', function (err, data) {
      if (err) {
        return console.log(err);
      }
    
      console.log(data);
    
      // SAVE_IN_ORACLE_DB job ends here
      SP.end(SP.keys.READ_FROM_HOST_FILE);
    
    
      SP.begin(SP.keys.JUST_FOR_LOOP_1);
      for (var i = 0; i < 1000000; i++) {
        var j = 0 + 9; // Just time pass
      }
      SP.end(SP.keys.JUST_FOR_LOOP_1);
    
      SP.begin(SP.keys.JUST_FOR_LOOP_2);
      for (var i = 0; i < 1000000; i++) {
        var j = 0 + 9; // Just time pass
      }
      SP.end(SP.keys.JUST_FOR_LOOP_2);
    
      // Yes final report is here
      SP.report(true);
    });
###### Output
    ******************** Profiling Summery ********************
    0> {"name":"Read from host file","hitIndex":1,"duration":"22ms","start":1459235204120,"end":1459235204142}
    1> {"name":"Just a forloop for testing 1","hitIndex":1,"duration":"2ms","start":1459235204143,"end":1459235204145}
    2> {"name":"Just a forloop for testing 2","hitIndex":1,"duration":"2ms","start":1459235204146,"end":1459235204148}
    ***********************************************************
    