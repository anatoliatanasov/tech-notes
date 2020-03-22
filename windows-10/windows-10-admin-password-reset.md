# Microsoft Windows 10 (Home Edition) Forgotten Local Admin User Password Reset #

| Version | Created by                  | Created on |
|--------:|:----------------------------|-----------:|
| 1.0     | anatoli.atanasov@expleo.pro | 22.03.2020 | 


## Table of Contents ##
[Proces Overview](#user-content-process-overview)

[More Details](#user-content-more-details)

[Resources](#user-content-resources)

## Process Overview ##
As there are prethora of Windows password reset tools out there, all of them are either paid or introduce risks of infecting the machine where they are installed with different *-ware applications. Though paying for software is not wrong (I do encourage this) as the teams / individuals sitting behind these products have the right to earn money, paying for one shot effort (mosf of the people will do this either once or twice) I think could be skipped.

Now to the process... It is basically based on replacing some OS util applications that can be invoked before you establish OS login session with the command line shell (i.e. the command prompt - CMD.exe).

The high level steps are:
1. Prepare Windows 10 bootable USB drive - preferable using the Microsoft provided tools for that;
2. Boot from the created USB drive;
3. Using the command prompt (before the installation begins) replace some of the OS utility apps;
4. Restart the computer making it boot from the default boot loader (i.e. boot normally the OS);
5. Before establishing any sessions (logins) invoke the command promtp and using commands either reset the lost password or create new (local) account;
6. Revert the replaced files in step 3 to their original state.

**IMPORTANT:** During step 3, do not forget to make a copy of the replaced utilities as they have to be reverted after the procedure is successfully executed.

## More Details ##
This section provides more details into each of the high level steps identified above.

### Prepare Windows 10 Bootable Drive ###
In order to avoid any risks from infecting the system that is about to be change, it is recommended to use the Microsoft provided Media Creation Tool, available here - https://www.microsoft.com/en-us/software-download/windows10.

Using the application is pretty straightforward and even users with minimum experience can easily create a bootable USB drive with MS Windows 10 installation. 

**IMPORTANT**: The USB drive that is to be used shall be at least 8GB, altohough the actual space consumed after the creation is less than 4GB.

### Boot From the Created USB Drive ###
In order to boot the from the USB drive most probably one will need access to the system BIOS in order to configure the boot order. For some systems pressing F12 during system start may bring boot order menu where user can pick which drive to use to boot from.

In either case the created bootable USB driver shall be configured as the first devide to boot from.

**IMPORTANT:** Changing settings in BIOS may harm your system, so beware of the actions you make.

**IMPORTANT:** If your BIOS is locked with password and F12 does not bring boot order menu while system is starting, then the process stops here and You must obtain BIOS password.

### Replace the OS Utilities that can be Started Without OS Login Session ###
After the boot order is changed to use the USB drive, restart the system. Wait until the very first screen from the Windows 10 installation wizzard appears. Then use the Shift + F10 key combination key to launch the command prompt. 

**IMPORTANT:** Do not click on any buttons on the first window from the Windows 10 installation wizzard.

**IMPORTANT:** For some systems You might use functional key before pressing the F10.

Once the command prompt application (cmd.exe) is brought up run the following commands:

```batch
@REM Creates a backup of the Utilman.exe file.
X:\Sources>copy C:\Windows\System32\Utilman.exe C:\Windows\System32\Utilman.exe.backup
@REM Replaces the Utilman.exe with the cmd.exe
X:\Sources>copy C:\Windows\System32\cmd.exe C:\Windows\System32\Utilman.exe

@REM Creates a backup of the sethc.exe file.
X:\Sources>copy C:\Windows\System32\sethc.exe C:\Windows\System32\sethc.exe.backup
@REM Replaces the sethc.exe with the cmd.exe
X:\Sources>copy C:\Windows\System32\cmd.exe C:\Windows\System32\sethc.exe
```

These commands replaces two of the utilities that can be started without Windows logon session. 

Do not close the command prompt window.

**IMPORTANT:** The commands first make copy of the original executable files and then replace the former with cmd.exe.

### Reboot the Machine with the Installed Windows 10 ###
To gracefully reboot the system run the following command in the command prompt window where the default utilities have been replaced:

```batch
X:\Sources>wpeutil reboot
```

During the system start reconfigure the system to use the local drive where Windows 10 has been installed (as opposed to the USB drive). To do so You might need access to the system BIOS.

### Update User's Lost Password ###
After the Windows 10 boots normally on the login screen either:
1. Hit Shift key 5 times (to trigger cmd.exe instead of sethc.exe) or
2. Click on the Ease of Access icon in the lower-right corner. This will bring up a Command Prompt window (instead the Utilman.exe).

After the command prompt is brought up use the following command to change the lost password:

```batch
C:\Windows\system32>net user <user_name> <new_password>
```

where:
- *<user_name>* shall be replaced with the username whose password has been lost
- *<new_password>* shall be replaced with the new user password.

Using the same command prompt window another local Windows 10 user can be created:
```batch
@REM Creates new user
C:\Windows\system32>net user <new_username> /add

@REM Sets password for the new user
C:\Windows\system32>net user <new_username> <new_password>

@REM Makes the user administrator of the system
C:\Windows\system32>net localgroup administrators <new_username> /add

@REM Closes the CMD window
C:\Windows\system32>exit
```

where:
- *<new_username>* shall be replaced with the username of the new user to be created;
- *<new_password>* shall be replaced with the new user password.

Once the CMD window is closed the new password can be used to login the Windows 10 system.

### Revert the Replaced Utilities with Their Original Copies ###
Once regular Windows 10 logon session is established, start a new instance of command prompt as administrator and run:

```batch
@REM Replaces the Utilman.exe with the original one.
X:\Sources>copy C:\Windows\System32\Utilman.exe.backup C:\Windows\System32\Utilman.exe

@REM Replaces the sethc.exe with the original one.
X:\Sources>copy C:\Windows\System32\sethc.exe.backup C:\Windows\System32\sethc.exe
```

## Resources ## 

This guide is developed after consulting with the following articles / how-tos:
 - https://www.passcue.com/windows-10-password-reset.html#Method6
 - https://www.isumsoft.com/windows-10/reset-local-admin-password-with-command-prompt.html

Additional details:
 - what sethc.exe is - utility that controls usage of Windows Sticky Keys - https://en.wikipedia.org/wiki/Sticky_keys
 - what Utilman.exe is - controls the Ease of Access methods in the Windows 10 logon screen - https://support.microsoft.com/en-gb/help/17180/windows-10-make-your-pc-easier-to-use.
 
