## Creating A Digital Directory Disk Image
Historically, Westfield Labs has been responsible for creating and distributing OS images with the Digital Directory loader pre-intalled to centres.  The centres would then just take the disk image provided and install them onto the designated machines and get the Digital Directory running out of the box.

Due to the various hardware decisions made by centres, and lack of a singular hardware platform, ownership and creation of the disk images is being handed to the respective IT entities.  Production Operations is documenting the steps and requirements for the creation of the **Windows 10** images. The respective corporate IT entity will then be able to create the necessary disk image on the hardware of their choosing.

### Prerequisites
- [Windows 10](https://www.microsoft.com/en-us/software-download/windows10ISO)
- [NSSM](https://nssm.cc/)
- [NodeJS](https://nodejs.org/en/download/)
- [NirCmd](http://www.nirsoft.net/utils/nircmd.html)
- [Google Chrome](https://www.google.com/chrome/)

### Setup the Windows 10 Environment
#### Install Windows 10
1. Install and setup **Windows 10**.
2. Two accounts will be needed for this process.  One account being an admin account (**westfieldadmin**), and the second account being a Standard User account (**wayfinding**).
3. Log into Windows 10 as the **westfieldadmin** user.
4. Disable **Windows Updates**.
    1. Open the Windows **Services**.
    2. Navigate to **Windows Update** and view its **Properties**.
    3. Change **Startup Type** to **Disabled**.
    4. Under **Service Status** click **Stop**.
    5. Click **OK**.
5. Adjust the **Power Options** so nothing turns off.
    1. Open up the **Control Panel**.
    2. Navigate to **Hardware and Sound**, then select **Power Options**.
    3. Select the **High Performance** radio button, then click the link to the right of it to **Change plan settings**.
    4. Make sure the following are set:
        * Hard Disk → Turn off hard disk after → **Never**
        * Sleep → Sleep after → **Never**
        * Sleep → Hibernate after → **Never**
        * Display → Turn off display after → **Never**
        * Display → Enable adaptive brightness  → **Off**
6. Adjust the **Performance** options for visual effects.
    1. Open up the **Control Panel**
    2. Navigate to **System and Security**, then select **System**.
    3. In the lefthand menu, select **Advanced System Settings** to open up a separate window.
    4. Under **Performance**, click **Settings**.
    5. Select the **Adjust for best performance** radio button.
7. Set it so the **wayfinding** user automatically logs in when the machine boots.
    1. In Windows, open the **netplwiz** tool.  This will open a User Acounts dialogue box.
    2. Uncheck the box next to **Users must enter a user name and password to use this computer**.
    3. You will be prompted to enter the **User Name** and **Password** of the user to automatically log in as.  Enter the credentials for the **wayfinding** user.
7. Login as the **wayfinding** user.
8. Disable **System Notifications** for the user account.
    1. Open the Windows **Settings**.
    2. Click on **System**.
    3. Under **Notifications & actions**, make sure all notifications and alerts are toggled to **Off**.
9. Disable **Visual Feedback** when touching the screen.
    1. Open the Windows **Settings**.
    2. Select **Other Options**.
    3. Make sure all features under **Touch Feedback** are set in the **Off** position.

#### Install Google Chrome
1. Log into Windows 10 as the **westfieldadmin** user.
2. Download and install [Google Chrome](https://www.google.com/chrome/)
3. Disable Google Chrome automatic updates.
    1. Grab the Google Chrome Administrative Template (ADM) from [here](/assets/GoogleUpdate.adm) or the [internet](https://www.chromium.org/administrators/policy-templates).
    2. Open the Windows **Local Group Policy Editor**.
    3. Under **Computer Configuration**, right click on **Administrative Templates**.
    4. Select **Add/Remove Templates...**.
    5. Select the [GoogleUpdate.adm](/assets/GoogleUpdate.adm) file to install.
    6. You'll notice the ADM install created the following:
        * Computer Configuration → Administrative Templates → Classic Administrative Templates (ADM) → Google → Google Update → Applications → Google Chrome
    7. Edit the **Update policy override**.
    8. Set the radio button to **Disabled** and click **OK** to save your changes.
4. Log into Windows 10 as the **wayfinding** user.
5. Open Google Chrome and set it as the user's **Default** browser.
6. As the **wayfinding** user, create a registry entry for Chrome shell
    1. Open up **regedit**.
    2. Navigate down the tree to `HKEY_CURRENT_USER\Software\Microsoft\Windows NT\CurrentVersion\Winlogon`.
    3. Within the `Winlogon` area, create a new **String Value** entry called `Shell`.
    4. **Modify** the **Data** value of `Shell`.  Set the **Value data** to the **Chrome** executable with the following flags:
`C:\Program Files (x86)\Google\Chrome\Application\chrome.exe http://directory.westfield.io --kiosk --incognito --disable-pinch --overscroll-history-navigation=0 --touch-events=enabled`

#### Install NodeJS
1. Log into Windows 10 as the **westfieldadmin** user.
2. Download and install [NodeJS v5.0.0](https://nodejs.org/download/release/v5.0.0/node-v5.0.0-x64.msi).  The installation directory should be `C:\Program Files\nodejs`. 


#### Install NSSM
1. Log into Windows 10 as the **westfieldadmin** user.
2. Download and extract NSSM from [here](https://nssm.cc/download).
3. Copy `nssm.exe` to the `C:\Windows\System32` directory.
4. Open up the **Command Prompt** and run:
`nssm.exe install digital_directory`
5. In the **NSSM Service Installer**, open the **Application** tab and set:
    * Path: `C:\Program Files\nodejs\node.exe`
    * Startup Directory: `C:\Program Files\Digital Directory`
        * This folder needs to be created and the content place manually.
    * Arguments: `index.js`
6. Navigate to the **Details** tab and set:
    * Display Name: `Digital Directory`
    * Description: `node.js application for the Digital Directory App`
    * Startup Type: `Automatic`
7. Once complete, click **Install Service**

#### Install NirCmd and Setup the Task Scheduler
1. Log into Windows 10 as the **westfieldadmin** user.
2. Download NirCmd 64-bit from [here](http://www.nirsoft.net/utils/nircmd-x64.zip).
3. Extract the contents, and move **NirCmd** to `C:\Windows\System32`.  This will allow you to run `nircmd` from anywhere.
4. Open the Windows 10 **Task Scheduler**.
6. Under the **Task Scheduler Library**, right-click and **Create New Task** and fill in the following:
    * General Tab
        * Name: `Shut Off Display`
        * Select the radio button for **Run whether the user is logged on or not**.
        * Under **When running the task, use the following user account:**, select `SYSTEM`.
        * Check the box for **Run with the highest privileges**.
    * Triggers Tab
        * New Trigger with the following:
            * Settings: **Daily** at **11:59:00 PM**
    * Actions Tab
        * New Action with the following settings:
            * Action: **Start a program**
            * Program/script: `C:\Windows\System32\nircmd.exe monitor off`
7. Once done, right-click and **Create New Task** once more.
    * General Tab
        * Name: `Auto Restart`
        * Select the radio button for **Run whether the user is logged on or not**.
        * Under **When running the task, use the following user account:**, select `SYSTEM`.
        * Check the box for **Run with the highest privileges**.
    * Triggers Tab
        * New Trigger with the following:
            * Settings: **Daily** at **4:00 AM**
    * Actions Tab
        * New Action with the following settings:
            * Action: **Start a program**
            * Program/script: `C:\Windows\System32\shutdown.exe -r -t 0 -f`

### Create the Disk Image

_A PDF version of these instructions with images can be found [here](/assets/Imaging_A_Directory_PC.pdf)_.

1. Plug in your external USB drive to write the image to.
2. As the machine is starting, press **F11** repeatedly until the **Boot Menu** is shown.
3. Select the external drive that you've inserted into the USB port.
4. The machine will boot to a screen asking for you to select the **keyboard layout**.
5. On the next screen, select **Troubleshoot**.
6. Under the **Troubleshoot** option, select **Advanced Options**.
7. Within **Advanced Options**, selet the **System Image Recovery** option.
8. On the dialogue box that says **Select A System Image Backup**, use the recommended latest available system image and proceed.
9. Check the box for **Format And Repartition Disks** and proceed.
10. After clicking **Finish**, the Restore process will begin.
11. Restarting the machine will boot the Windows 10 image with the **wayfinding** user account and automatically start the **Digital Directory** software.

**If there are problems booting or beginning the restore process, this may be an indication of some changes to the BIOS settings.  Do the following:**

1. As the machine is starting, press F11 repeatedly until the Boot Menu is shown.
2. Select **Configuration**.
3. Navigate to the **Advanced** tab.
4. Select **Storage Configuration**.
5. Ensure that **SATA Mode Selection** is set to **AHCI**.
6. Hit **ESC** once and navigate over to the **Boot** tab.
7. Navigate down to and select **CSM (Compatibility Support Module)**.
8. Make sure **CSM** is **Enabled** and all options below it are **Legacy Only**.
9. Once complete, hit **F10** to save the changes and exit to start the boot process over again.
