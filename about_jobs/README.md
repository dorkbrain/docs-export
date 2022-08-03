---
title: "about_Jobs"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Yep, ripped straight from Get-Help in PowerShell v2

TOPIC
    about\_Jobs

SHORT DESCRIPTION
    Provides information about how Windows PowerShell background jobs run a
    command or expression in the background without interacting with the 
    current session. 

LONG DESCRIPTION
    This topic explains how to run background jobs in Windows PowerShell on a 
    local computer. For information about running background jobs on remote 
    computers, see about\_Remote\_Jobs. 
    
    When you start a background job, the command prompt returns immediately,
    even if the job takes an extended time to complete. You can continue to 
    work in the session without interruption while the job runs. 

    Important: Background jobs that are started by using Start-Job or the
               AsJob parameter of Invoke-Command rely on the Windows PowerShell
               remoting infrastructure. To use these features, Windows 
               PowerShell must be configured for remoting, even if the 
               background job is run only on the local computer. 
               For more information, see about\_Remote\_Requirements.

 
        
 HOW TO START A JOB ON THE LOCAL COMPUTER
    To start a background job on the local computer, use the Start-Job 
    cmdlet. 

    To write a Start-Job command, enclose the command that the job runs in 
    braces ( { } ). Use the ScriptBlock parameter to specify the command.

    The following command starts a background job that runs a Get-Process 
    command on the local computer.

        start-job -scriptblock {get-process}

    The Start-Job command returns an object that represents the job. The job 
    object contains useful information about the job, but it does not contain 
    the job results.
    
    Save the job object in a variable, and then use it with the other Job 
    cmdlets to manage the background job. The following command starts a job 
    object and saves the resulting job object in the $job variable.

        $job = start-job -scriptblock {get-process}
          
    You can also use the Get-Job cmdlet to get objects that represent the jobs 
    started in the current session. Get-Job returns the same job object that 
    Start-Job returns.

 
 GETTING JOB OBJECTS

    To get object that represent the background jobs that were started in the 
    current session, use the Get-Job cmdlet. Without parameters, Get-Job 
    returns all of the jobs that were started in the current session.

    For example, the following command gets the jobs in the current session.

	get-job

        Id  Name  State      HasMoreData  Location   Command
        -- ---- ----- ----------- -------- -------
        1   Job1  Running    True         localhost  get-process

    You can also save the job object in a variable and use it to represent the 
    job in a later command. The following command gets the job with ID 1 and 
    saves it in the $job variable.

       $job = get-job -id 1  

    The job object contains the state of the job, which indicates whether the 
    job has finished. A finished job has a state of "Complete" or "Failed". A 
    job might also be blocked or running.

	get-job

        Id  Name  State      HasMoreData  Location   Command
        -- ---- ----- ----------- -------- -------
        1   Job1  Complete    True         localhost  get-process

 GETTING THE RESULTS OF A JOB

    When you run a background job, the results do not appear immediately. 
    Instead, the Start-Job cmdlet returns a job object that represents the 
    job, but it does not contain the results. To get the results of a 
    background job, use the Receive-Job cmdlet.

    The following command uses the Receive-Job cmdlet to get the results of 
    the job. It uses a job object saved in the $job variable to identify the 
    job.

	receive-job -job $job

     The Receive-Job cmdlet returns the results of the job. 

           Handles  NPM(K)    PM(K)      WS(K) VM(M)   CPU(s)    Id ProcessName
           ------- ------ ----- ----- ----- ------ -- -----------
               103       4    11328       9692    56           1176 audiodg
               804      14    12228      14108   100   101.74  1740 CcmExec
               668       7     2672       6168   104    32.26   488 csrss
	   ...

     You can also save the results of a job in a variable. The following 
     command saves the results of the job in the $job variable to the $results 
     variable.

	$results = receive-job -job $job

     And, you can save the results of the job in a file by using the redirection 
     operator (>) or the Out-File cmdlet. The following command uses the 
     redirection operator to save the results of the job in the $job variable in 
     the Results.txt file.

        receive-job -job $job > results.txt

    

 GETTING AND KEEPING PARTIAL JOB RESULTS

    The Receive-Job cmdlet returns the results of a background job. If the 
    job is complete, Receive-Job returns the complete results of the job. If 
    the job is still running, Receive-Job gets the results that have been
    generated thus far. You can run Receive-Job commands again to get the 
    remaining results.

    When Receive-Job returns results, by default, it deletes the results from 
    the cache where job results are stored. If you run another Receive-Job 
    command, you get only the results that were not yet received.

    The following commands show the results of Receive-Job commands run 
    before the job is complete.

	C:\\PS> receive-job -job $job

        Handles  NPM(K)    PM(K)      WS(K) VM(M)   CPU(s)     Id ProcessName
        ------- ------ ----- ----- ----- ------ -- -----------
            103       4    11328       9692    56            1176 audiodg
            804      14    12228      14108   100   101.74   1740 CcmExec

	C:\\PS> receive-job -job $job

        Handles  NPM(K)    PM(K)      WS(K) VM(M)   CPU(s)     Id ProcessName
        ------- ------ ----- ----- ----- ------ -- -----------
             68       3     2632        664    29     0.36   1388 ccmsetup
            749      22    21468      19940   203   122.13   3644 communicator
            905       7     2980       2628    34   197.97    424 csrss
           1121      25    28408      32940   174   430.14   3048 explorer

    To prevent Receive-Job from deleting the job results that it has
    returned, use the Keep parameter. As a result, Receive-Job returns all
    of the results that have been generated until that time.

    The following commands show the effect of using the Keep parameter on a job
    that is not yet complete. 

	C:\\PS> receive-job -job $job -keep

        Handles  NPM(K)    PM(K)      WS(K) VM(M)   CPU(s)     Id ProcessName
        ------- ------ ----- ----- ----- ------ -- -----------
            103       4    11328       9692    56            1176 audiodg
            804      14    12228      14108   100   101.74   1740 CcmExec

	C:\\PS> receive-job -job $job -keep

        Handles  NPM(K)    PM(K)      WS(K) VM(M)   CPU(s)     Id ProcessName
        ------- ------ ----- ----- ----- ------ -- -----------
            103       4    11328       9692    56            1176 audiodg
            804      14    12228      14108   100   101.74   1740 CcmExec
             68       3     2632        664    29     0.36   1388 ccmsetup
            749      22    21468      19940   203   122.13   3644 communicator
            905       7     2980       2628    34   197.97    424 csrss
           1121      25    28408      32940   174   430.14   3048 explorer

    
WAITING FOR THE RESULTS

    If you run a command that takes a long time to be completed, you can use 
    the properties of the job object to determine when the job is complete. 
    The following command uses the Get-Job object to get all of the background 
    jobs in the current session.

	get-job

    The results appear in a table. The status of the job appears in the State 
    column.

        Id  Name  State      HasMoreData  Location   Command
        -- ---- ----- ----------- -------- -------
        1   Job1  Complete    True         localhost  get-process
        2   Job2  Running     True         localhost  get-eventlog -log syst...
        3   Job3  Complete    True         localhost  dir -path c:\\\* -recurse

    In this case, the State property reveals that Job 2 is still running. If 
    you were to use the Receive-Job cmdlet to get the job results now, the 
    results would be incomplete. You can use the Receive-Job cmdlet 
    repeatedly to get all of the results. By default, each time you use it, 
    you get only the results that were not already received, but you can use 
    the Keep parameter of the Receive-Job cmdlet to retain the results, even 
    though they were already received.

    At this point, you can write the results to a file and then append the 
    newly received results as they arrive. Or, you can wait and check the 
    state of the job later.

    Or, you can use the Wait-Job cmdlet to wait for any or all of the results 
    of the job. Wait-Job lets you wait for a particular job, for all jobs, or 
    for any of the jobs to be completed.
  
    The following command uses the Wait-Job cmdlet to wait for a job with 
    ID 10.

	wait-job -ID 10

    As a result, the Windows PowerShell prompt is suppressed until the job 
    is completed.

    You can also wait for a predetermined period of time. This command uses 
    the Timeout parameter to limit the wait to 120 seconds. When the time 
    expires, the command prompt returns, but the job continues to run in the 
    background.

	wait-job -ID 10 -timeout 120

STOPPING A JOB

    To stop a background job, use the Stop-Job cmdlet. The following command 
    starts a job to get every entry in the System event log. It saves the job
    object in the $job variable.

	$job = start-job -scriptblock {get-eventlog -log system}
    
    The following command stops the job. It uses a pipeline operator (|) to
    send the job in the $job variable to Stop-Job.

	$job\[/td\]\[td\]stop-job

DELETING A JOB

    To delete a background job, use the Remove-Job cmdlet. The following 
    command deletes the job in the $job variable.

	remove-job -job $job 

INVESTIGATING A FAILED JOB

    To find out why a job failed, use the Reason subproperty of the job object.

    The following command starts a job without the required credentials. It
    saves the job object in the $job variable.

         $job = start-job -scriptblock {new-item -path HKLM:\\Software\\MyCompany}

         Id   Name  State    HasMoreData  Location   Command
         -- ---- ----- ----------- -------- -------
         1    Job1  Failed   False         localhost  new-item -path HKLM:\\S...

    The following command uses the Reason property to find the error that 
    caused the job to fail.

         $job.ChildJobs\[0\].JobStateInfo.Reason

    In this case, the job failed because the remote computer required explicit 
    credentials to run the command. The value of the Reason property is:

         Connecting to remote server failed with the following error 
         message : Access is denied.

THE JOB CMDLETS

    Start-Job        Starts a background job on a local computer.

    Get-Job          Gets the background jobs that were started in the current 
                     session.

    Receive-Job      Gets the results of background jobs.

    Stop-Job         Stops a background job.

    Wait-Job         Suppresses the command prompt until one or all jobs are 
                     complete.

    Remove-Job       Deletes a background job.

    Invoke-Command   The AsJob parameter runs any command as a background job on a
                     remote computer. You can also use Invoke-Command to run
                     any job command remotely, including a Start-Job command.

 
SEE ALSO
   about\_Remote\_Jobs
   about\_Job\_Details
   about\_Remote
   about\_PSSessions   
   Start-Job
   Get-Job
   Receive-Job
   Stop-Job
   Wait-Job
   Remove-Job
   Invoke-Command
