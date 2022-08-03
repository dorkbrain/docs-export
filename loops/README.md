---
title: "Loops"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

I'll be honest, I ripped these blocks straight from the MS PowerShell site and Get-Help...

**For**

TOPIC
    about\_For

SHORT DESCRIPTION
    Describes a language command you can use to run statements based on a
    conditional test.

LONG DESCRIPTION
    The For statement (also known as a For loop) is a language construct 
    you can use to create a loop that runs commands in a command block while a 
    specified condition evaluates to true. 

    A typical use of the For loop is to iterate an array of values and to 
    operate on a subset of these values. In most cases, if you want to 
    iterate all the values in an array, consider using a Foreach statement.

  Syntax
      The following shows the For statement syntax.
      
  
          for (<init>; <condition>; <repeat>) 
          {<statement list>}

      
      The <init> placeholder represents one or more commands, separated by 
      commas, that are run before the loop begins. You typically use the 
      <init> portion of the statement to create and initialize a variable with
      a starting value. Note that the comma syntax doesn't work with
      assignment statements, such as the following example:

          $ofs=",";$rs = "rs"; $cs = "cs"; for ($r = $rs, $c = $cs; $true;) 
          { "r is '$r' and c is '$c'"; break }

 
      This variable will then be the basis for the condition to be tested in 
      the next portion of the For statement.

        
      The <condition> placeholder represents the portion of the For statement 
      that resolves to a true or false Boolean value. Windows PowerShell 
      evaluates the condition each time the For loop runs. If the statement is 
      true, the commands in the command block run, and the statement is 
      evaluated again. If the condition is still true, the commands in the 
      statement list run again. The loop is repeated until the condition 
      becomes false.

     
      The <repeat> placeholder represents one or more commands, separated by 
      commas, that are executed each time the loop repeats. Typically, this 
      is used to modify a variable that is tested inside the <condition> part
      of the statement.

        
      The <statement list> placeholder represents a set of one or more commands
      that are run each time the loop is entered or repeated. The contents of
      the statement list are surrounded by braces.

  Examples
      At a minimum, a For statement requires the parenthesis surrounding the 
      <init>, <condition>, and <repeat> part of the statement and a command 
      surrounded by braces in the <statement list> part of the statement. 

      Note that the upcoming examples intentionally show code outside the 
      For statement. In later examples, code is integrated into the for 
      statement.

      For example, the following For statement continually displays the 
      value of the $i variable until you manually break out of the command by 
      pressing CTRL+C. 

          $i = 1
          for (;;){Write-Host $i}

      You can add additional commands to the statement list so that 
      the value of $i is incremented by 1 each time the loop is run, as the 
      following example shows.

          for (;;){$i++; Write-Host $i}

      Until you break out of the command by pressing CTRL+C, this statement 
      will continually display the value of the $i variable as it is 
      incremented by 1 each time the loop is run.

      Rather than change the value of the variable in the statement list 
      part of the For statement, you can use the <repeat> portion of the For 
      statement instead, as follows. 

          $i=1
          for (;;$i++){Write-Host $i}

      This statement will still repeat indefinitely until you break out of the 
      command by pressing CTRL+C.

      By setting a condition (using the <condition> portion of the For
      statement), you can end the For loop when the condition evaluates to 
      false. In the following example, the For loop runs while the value of 
      $i is less than or equal to 10.
 

          $i=1
          for(;$i -le 10;$i++){Write-Host $i}

      Instead of creating and initializing the variable outside the For 
      statement, you can perform this task inside the For loop by using 
      the <init> portion of the For statement.

          for($i=1; $i -le 10; $i++){Write-Host $i}

      You can use carriage returns instead of semicolons to delimit the 
      <init>, <condition>, and <repeat> portions of the For statement. The
      following example shows the For statement syntax in this alternative 
      form.

        
            for (<init>
          <condition>
          <repeat>){
          <statement list>
          }

      
      This alternative form of the For statement works in Windows PowerShell 
      script files and at the Windows PowerShell command prompt. However, it
      is easier to use the For statement syntax with semicolons when you enter
      interactive commands at the command prompt. 

        
      The For loop is more flexible than the Foreach loop because it allows 
      you to increment values in an array or collection by using patterns. In
      the following example, the $i variable is incremented by 2 in the 
      <repeat> portion of the for statement.

          for ($i = 0; $i -ile 20; $i += 2) {Write-Host $i}

SEE ALSO        
    about\_Comparison\_Operators
    about\_Foreach

 

**ForEach**

TOPIC
    about\_Foreach

SHORT DESCRIPTION
    Describes a language command you can use to traverse all the items in a 
    collection of items.

LONG DESCRIPTION
    The Foreach statement (also known as a Foreach loop) is a language 
    construct for stepping through (iterating) a series of values in a 
    collection of items. 

    The simplest and most typical type of collection to traverse is an array.
    Within a Foreach loop, it is common to run one or more commands against
    each item in an array. 

  Syntax        
      The following shows the Foreach syntax:

        
          foreach ($<item> in $<collection>){<statement list>}

  The Foreach Statement Outside a Command Pipeline
      The part of the Foreach statement enclosed in parenthesis represents a
      variable and a collection to iterate. Windows PowerShell creates the 
      variable ($<item>) automatically when the Foreach loop runs. Prior to 
      each iteration through the loop, the variable is set to a value in the 
      collection. The block following a Foreach statement {<statement list>}
      contains a set of commands to execute against each item in a collection.
 

  Examples
      For example, the Foreach loop in the following example displays the 
      values in the $letterArray array.

        
          $letterArray = "a","b","c","d"
          foreach ($letter in $letterArray)
          {
              Write-Host $letter
          }

      
      In this example, the $letterArray array is created and initialized with 
      the string values "a", "b", "c", and "d". The first time the Foreach 
      statement runs, it sets the $letter variable equal to the first item in 
      $letterArray ("a"). Then, it uses the Write-Host cmdlet to display the 
      letter a. The next time through the loop, $letter is set to "b", and so 
      on. After the Foreach loop displays the letter d, Windows PowerShell 
      exits the loop.

      The entire Foreach statement must appear on a single line to run it as a
      command at the Windows PowerShell command prompt. The entire Foreach 
      statement does not have to appear on a single line if you place the 
      command in a .ps1 script file instead. 

      Foreach statements can also be used together with cmdlets that 
      return a collection of items. In the following example, the Foreach 
      statement steps through the list of items that is returned by the 
      Get-ChildItem cmdlet.

          foreach ($file in Get-ChildItem)
          {
              Write-Host $file
          }

      You can refine the example by using an If statement to limit the results 
      that are returned. In the following example, the Foreach statement 
      performs the same looping operation as the previous example, but it adds
      an If statement to limit the results to files that are greater than 100 
      kilobytes (KB):

          foreach ($file in Get-ChildItem)
          {
              if ($file.length -gt 100KB) 
              {
                  Write-Host $file
              }
          }

      In this example, the Foreach loop uses a property of the $file variable
      to perform a comparison operation ($file.length -gt 100KB). The $file 
      variable contains all the properties in the object that is returned by 
      the Get-ChildItem cmdlet. Therefore, you can return more than just a 
      file name. In the next example, Windows PowerShell returns the length and
      the last access time inside the statement list:

          foreach ($file in Get-ChildItem)
          {
              if ($file.length -gt 100KB) 
              {
                  Write-Host $file
                  Write-Host $file.length
                  Write-Host $file.lastaccesstime
              }
          }

    
      In this example, you are not limited to running a single command in a 
      statement list. 

      
      You can also use a variable outside a Foreach loop and increment the 
      variable inside the loop. The following example counts files over 100 KB
      in size: 
      
  
          $i = 0
          foreach ($file in Get-ChildItem)
          {
              if ($file.length -gt 100KB) 
              {
                  Write-Host $file "file size:" ($file.length / 
          1024).ToString("F0") KB
                  $i = $i + 1
              }
          }

          if ($i -ne 0)
          {
              Write-Host
              Write-Host $i " file(s) over 100 KB in the current 
          directory."}
          else 
          {
              Write-Host "No files greater than 100 KB in the current 
          directory."
          }

      In the preceding example, the $i variable is set to 0 outside the loop,
      and the variable is incremented inside the loop for each file that is
      found that is larger than 100 KB. When the loop exits, an If statement
      evaluates the value of $i to display a count of all the files over 
      100 KB. Or, it displays a message stating that no files over 100 KB were
      found.

      The previous example also demonstrates how to format the file length 
      results:

          ($file.length / 1024).ToString("F0")

      The value is divided by 1,024 to show the results in kilobytes rather 
      than bytes, and the resulting value is then formatted using the 
      fixed-point format specifier to remove any decimal values from the 
      result. The 0 makes the format specifier show no decimal places. 

  The Foreach Statement Inside a Command Pipeline
      When Foreach appears in a command pipeline, Windows PowerShell uses the
      foreach alias, which calls the ForEach-Object command. When you use 
      the foreach alias in a command pipeline, you do not include 
      the ($<item> in $<collection>) syntax as you do with the Foreach 
      statement. This is because the prior command in the pipeline provides 
      this information. The syntax of the foreach alias when used in a command
      pipeline is as follows:
        

          <command> | foreach {<command\_block>}
     

      For example, the Foreach loop in the following command displays processes
      whose working set (memory usage) is greater than 20 megabytes (MB). 

      The Get-Process command gets all of the processes on the computer. The 
      Foreach alias performs the commands in the script block on each process
      in sequence. 

      The IF statement selects processes with a working set (WS) greater than
      20 megabytes. The Write-Host cmdlet writes the name of the process followed
      by a colon. It divides the working set value, which is stored in bytes
      by 1 megabyte to get the working set value in megabytes. Then it converts the
      result from a double to a string. It displays the value as a fixed point
      number with zero decimals (F0), a space separator (" "), and then "MB".

          Write-Host "Processes with working sets greater than 20 MB."
          Get-Process | foreach { 
             if ($\_.WS -gt 20MB)
             { Write-Host $\_.name ": " ($\_.WS/1MB).ToString("F0") MB -Separator ""}
          }

      The foreach alias also supports beginning command blocks, middle command
      blocks, and end command blocks. The beginning and end command blocks run
      once, and the middle command block runs every time the Foreach loop steps
      through a collection or array. 
        

      The syntax of the foreach alias when used in a command pipeline with a 
      beginning, middle, and ending set of command blocks is as follows:
        

          <command> | foreach {<beginning command\_block>}{<middle 
          command\_block>}{<ending command\_block>}

 
      The following example demonstrates the use of the beginning, middle, and
      end command blocks. 

          Get-ChildItem | foreach {
          $fileCount = $directoryCount = 0}{
          if ($\_.PsIsContainer) {$directoryCount++} else {$fileCount++}}{
          "$directoryCount directories and $fileCount files"}

      The beginning block creates and initializes two variables to 0:

          {$fileCount = $directoryCount = 0}
 

      The middle block evaluates whether each item returned by Get-ChildItem
      is a directory or a file:

          {if ($\_.PsIsContainer) {$directoryCount++} else {$fileCount++}}

      If the item that is returned is a directory, the $directoryCount 
      variable is incremented by 1. If the item is not a directory, 
      the $fileCount variable is incremented by 1. The ending block runs after
      the middle block completes its looping operation and then returns the 
      results of the operation: 

       
          {"$directoryCount directories and $fileCount files"}

      By using the beginning, middle, and ending command block structure and 
      the pipeline operator, you can rewrite the earlier example to find any 
      files that are greater than 100 KB, as follows:

          Get-ChildItem | foreach{
              $i = 0}{
              if ($\_.length -gt 100KB)
              {
                  Write-Host $\_.name "file size:" ($\_.length / 
          1024).ToString("F0") KB
                  $i++
              }
              }{
              if ($i -ne 0)
              {
                  Write-Host
                  Write-Host "$i file(s) over 100 KB in the current 
          directory."
              }
              else 
              {
              Write-Host "No files greater than 100 KB in the current 
          directory."}
              }

SEE ALSO
    about\_Automatic\_Variables
    about\_If
    Foreach-Object

 

**ForEach-Object**

NAME
    ForEach-Object

SYNOPSIS
    Performs an operation against each of a set of input objects.

SYNTAX
    ForEach-Object \[-Process\] <ScriptBlock\[\]> \[-Begin <scriptblock>\] \[-End <scriptblock>\] \[-InputObject <psobject>\] \[<C
    ommonParameters>\]

DESCRIPTION
    The ForEach-Object cmdlet performs an operation on each of a set of input objects. The input objects can be piped t
    o the cmdlet or specified by using the InputObject parameter.

    The operation to perform is described within a script block that is provided to the cmdlet as the value of the Proc
    ess parameter. The script block can contain any Windows PowerShell script.

    Within the script block, the current input object is represented by the $\_ variable.
    In addition to using the script block that describes the operations to be carried out on each input object, you can
     provide two additional script blocks. One, specified as the value of the Begin parameter, runs before the first in
    put object is processed. The other, specified as the value of the End parameter, runs after the last input object i
    s processed.

    The results of the evaluation of all the script blocks, including the ones specified with Begin and End, are passed
     down the pipeline.

PARAMETERS
    -Begin <scriptblock>
        Specifies a script block to run before processing any input objects.

        Required?                    false
        Position?                    named
        Default value
        Accept pipeline input?       false
        Accept wildcard characters?  false

    -End <scriptblock>
        Specifies a script block to run after processing all input objects.

        Required?                    false
        Position?                    named
        Default value
        Accept pipeline input?       false
        Accept wildcard characters?  false

    -InputObject <psobject>
        Accepts an object that the script block specified in the process parameter will act upon. Enter a variable that
         contains the objects, or type a command or expression that gets the objects.

        Required?                    false
        Position?                    named
        Default value
        Accept pipeline input?       true (ByValue)
        Accept wildcard characters?  false

    -Process <ScriptBlock\[\]>
        Specifies the script block that is applied to each incoming object.

        Required?                    true
        Position?                    1
        Default value
        Accept pipeline input?       false
        Accept wildcard characters?  false

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer and OutVariable. For more information, type,
        "get-help about\_commonparameters".

INPUTS
    System.Management.Automation.PSObject
        You can pipe any object to ForEach-Object.

OUTPUTS
    System.Management.Automation.PSObject
        The objects that ForEach-Object returns are determined by the input.

NOTES

    -------------------------- EXAMPLE 1 --------------------------

    C:\\PS>30000,56798,12432 | foreach-object -process {$\_/1024}

    Description
    -----------
    This command accepts an array of integers, divides each one of them by 1024, and displays the results.

    -------------------------- EXAMPLE 2 --------------------------

    C:\\PS>get-childitem C:\\ | foreach-object -process { $\_.length / 1024 }

    Description
    -----------
    This command retrieves the files and directories in the root of the C: drive, and it returns and displays the size
    of each of them. The zeros represent directories in which no file size was available.

    -------------------------- EXAMPLE 3 --------------------------

    C:\\PS>$events = get-eventlog -logname system -newest 1000

    C:\\PS> $events | foreach-object -begin {get-date} -process {out-file -filepath events.txt -append -inputobject $\_.m
    essage} -end {get-date}

    Description
    -----------
    This command retrieves the 1000 most recent events from the system log and stores them in the $events variable. It
    then pipes the events to the ForEach-Object cmdlet. The Begin parameter displays the current date and time. Next, t
    he Process parameter uses the Out-File cmdlet to create a text file named events.txt and stores the message propert
    y of each of the events in that file. Last, the End parameter is used to display the date and time after all of the
     processing has completed.

    -------------------------- EXAMPLE 4 --------------------------

    C:\\PS>get-itemproperty -path hkcu:\\Network\\\* | foreach-object {set-itemproperty -path $\_.pspath -name RemotePath -v
    alue $\_.RemotePath.ToUpper();}

    Description
    -----------
    This command changes the value of the RemotePath registry entry in all of the subkeys under the HKCU:\\Network key t
    o uppercase text. You can use this format to change the form or content of a registry entry value.

    Each subkey in the Network key represents a mapped network drive that will reconnect at logon. The RemotePath entry
     contains the UNC path of the connected drive. For example, if you map the E: drive to \\\\Server\\Share, there will b
    e an E subkey of HKCU:\\Network and the value of the RemotePath registry entry in the E subkey will be \\\\Server\\Shar
    e.

    The command uses the Get-ItemProperty cmdlet to get all of the subkeys of the Network key and the Set-ItemProperty
    cmdlet to change the value of the RemotePath registry entry in each key. In the Set-ItemProperty command, the path
    is the value of the PSPath property of the registry key. (This is a property of the Microsoft .NET Framework object
     that represents the registry key; it is not a registry entry.) The command uses the ToUpper() method of the Remote
    Path value, which is a string (REG\_SZ).

    Because Set-ItemProperty is changing the property of each key, the ForEach-Object cmdlet is required to access the
    property.

 

**Do**

TOPIC
    about\_Do

SHORT DESCRIPTION
    Runs a statement list one or more times, subject to a While or Until 
    condition.

LONG DESCRIPTION
    The Do keyword works with the While keyword or the Until keyword to run 
    the statements in a script block, subject to a condition. Unlike the 
    related While loop, the script block in a Do loop always runs at least 
    once.

    A Do-While loop is a variety of the While loop. In a Do-While loop, the 
    condition is evaluated after the script block has run. As in a While loop,
    the script block is repeated as long as the condition evaluates to true. 

    Like a Do-While loop, a Do-Until loop always runs at least once before 
    the condition is evaluated. However, the script block runs only while
    the condition is false. 

    The Continue and Break flow control keywords can be used in a Do-While 
    loop or in a Do-Until loop.  

  Syntax
      The following shows the syntax of the Do-While statement:

          do {<statement list>} while (<condition>)

      The following shows the syntax of the Do-Until statement:

          do {<statement list>} until (<condition>)

      The statment list contains one or more statements that run each time
      the loop is entered or repeated.

      The condition portion of the statement resolves to true or false. 

  Example
      The following example of a Do statement counts the items in an 
      array until it reaches an item with a value of 0.

          C:\\PS> $x = 1,2,78,0
          C:\\PS> do { $count++; $a++; } while ($x\[$a\] -ne 0) 
          C:\\PS> $count
          3

      The following example uses the Until keyword. Notice that
      the not equal to operator (-ne) is replaced by the 
      equal to operator (-eq).

          C:\\PS> $x = 1,2,78,0
          C:\\PS> do { $count++; $a++; } until ($x\[$a\] -eq 0) 
          C:\\PS> $count
          3

      The following example writes all the values of an array, skipping any
      value that is less than zero.

          do
          {
              if ($x\[$a\] -lt 0) { continue }
              Write-Host $x\[$a\]
          } 
          while (++$a -lt 10)

SEE ALSO
    about\_While
    about\_Operators
    about\_Assignment\_Operators
    about\_Comparison\_Operators
    about\_Break
    about\_Continue

 

**While**

TOPIC
    about\_While

SHORT DESCRIPTION
    Describes a language statement that you can use to run a command block 
    based on the results of a conditional test.

LONG DESCRIPTION
    The While statement (also known as a While loop) is a language construct
    for creating a loop that runs commands in a command block as long as a
    conditional test evaluates to true. The While statement is easier to 
    construct than a For statement because its syntax is less complicated.
    In addition, it is more flexible than the Foreach statement because you
    specify a conditional test in the While statement to control how many times
    the loop runs.
 

    The following shows the While statement syntax:

        while (<condition>){<statement list>}

    When you run a While statement, Windows PowerShell evaluates 
    the <condition> section of the statement before entering the 
    <statement list> section. The condition portion of the statement resolves
    to either true or false. As long as the condition remains true, Windows 
    PowerShell reruns the <statement list> section.

    The <statement list> section of the statement contains one or more commands
    that are run each time the loop is entered or repeated.

    For example, the following While statement displays the numbers 1 
    through 3 if the $val variable has not been created or if the $val variable
    has been created and initialized to 0.

        while($val -ne 3)
        {
            $val++
            Write-Host $val
        }

    In this example, the condition ($val is not equal to 3) is true while 
    $val = 0, 1, 2. Each time through the loop, $val is incremented by 1 
    using the ++ unary increment operator ($val++). The last time through 
    the loop, $val = 3. When $val equals 3, the condition statement 
    evaluates to false, and the loop exits.
 

    To conveniently write this command at the Windows PowerShell command 
    prompt, you can enter it in the following way:

        while($val -ne 3){$val++; Write-Host $val} 

    Notice that the semicolon separates the first command that adds 1 to 
    $val from the second command that writes the value of $val to the 
    console.
 

SEE ALSO
    about\_Comparison\_Operators
    about\_Do
    about\_Foreach
    about\_For
    about\_Language\_Keywords
