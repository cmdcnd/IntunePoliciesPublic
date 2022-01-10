### Title: Office 365 Intune  
### Purpose: Document Implementing DoD STIGs using Intune   
The goal of this document is to help implement the DoD/NIST security settings for Windows clients on a non-DoD network using Intune.  While many people become concerned with the use of DoD security settings, they are in fact the same controls from NIST, but in an easy to follow checklist.  Along with this documentation are the Intune Backups that can be imported, and implemented in almost any organization.  The Intune Policies have been built from the templates at the Defense Information Systems Agency site(DISA).  The reason for using the DISA site is the STIG Viewer and benchmarks.  These provide an easy to use tool for viewing and documenting the DoD/NIST settings.  Using this tool one can import the specific catalog of settings for a wide range of systems, applications and other products.  DoD has now also released the SCC SCAP Tool which lets you scan a local/remote system for compliance.  See links below in the [References Section](#references).  This is primarily designed for organizations that mange endpoints using Intune MDM and are Azure registered or joined.  
##### Section 1: [Microsoft Security Baseline](#section-1-microsoft-security-baseline)  
##### Section 2: [Configuration Profiles](#section-2-configuration-profiles)  
##### Section 3: [Importing Policies](#section-3-importing-policies)  

#### Section 1: Microsoft Security Baseline [HOME](#title-office-365-intune)
Microsoft has [Security Baselines](https://docs.microsoft.com/en-us/mem/intune/protect/security-baseline-settings-mdm-all?pivots=november-2021) that cover a significant amount of settings.  So the idea is to apply a security baseline first, then create additional configuration policies for anything else required.  

* Create Security Baseline using the November 2021 version  
  * Login to [Microsoft Endpoint Manager admin center](https://endpoint.microsoft.com)
  * Select `Endpoint security` -> `Security baselines` -> `Security Baseline for Windows 10 and later`
  ![alt text](img/security-baselines.png "Security Baselines")
  * Create a new profile  
  Note: There are a few settings that have been removed from the Security Baseline to improve functionality in a business environment.  Each organization will have to determine what works best for them and test before applying to production environments.  Here are the items that have been changed in the Security Baseline:
    * `BitLocker` -> `BitLocker removable drive policy` -> `Not configured`  This is a good policy to leave on, but depends on how much the organization uses removable media.  
    ![alt text](img/bitlocker.png "BitLocker Removable Drive Policy")  
    * `Local Policies Security Options` -> `Standard user elevation prompt behavior` -> `Prompt for credentials on the secure desktop`  
    ![alt text](img/uac.png "UAC")
    * `Microsoft Defender` -> `Block all Office applications from creating child processes` -> `Audit mode`  This is actually a great setting, but have found it to be problematic with normal application operation.  
    ![alt text](img/defender.png "Microsoft Defender child processes")

#### Section 2: Configuration Profiles [HOME](#title-office-365-intune)  
Now that the Security Baseline has been applied, the rest of the STIG settings need to be applied using Configuration profiles.  The provided templates are in 2 different formats.  `Setting Catalog` is the newest format that allows combining more settings into a single policy but only works for Windows settings and applications.  In order to configure applications such as Chrome and Adobe, a `custom profile` must be used.  This repository contains a total of three policies:  
* Windows Business Computer Configuration Policy:  This policy contains a lot of settings, primarily for Office.  While using `Setting Catalog` policies makes it easier to manage everything in one policy, it is a little clunky trying to add new settings.  Meaning you have to be patient when adding new items and try not to add too many at once.   
  * Microsoft Office Settings: The main changes organizations tend to make is allowing macros.  The current template has them set to disallow all macros with no notification to the user.  While this is the safest setting, it may not work in all organizations.  If the organization still requires macros, at least keep the setting `Block macros from running in Office files from the Internet (User)`.  
  ![alt text](img/macro.png "Office Macros")
  * Logon Banner: Change the logon banner to meet the organization requirements:  
  `Local Policies Security Options` -> `Interactive Logon Message Text For Users Attempting To Log On` and `Interactive Logon Message Title For Users Attempting To Log On`  
* Windows Business Adobe Acrobat Reader DC Policy: This policy comes from the [DoD GPO Download](https://public.cyber.mil/stigs/gpo/).  In it you will find the Chrome template here: `Intune Policies/Device Configuration`.  The following settings were removed:
  * DisplayName: `STIG ID DTBC-0004` Description: `Sites ability to show pop-ups must be disabled`  
  * DisplayName: `STIG ID DTBC-0005` Description: `Extensions installation must be blocklisted by default`  
  * DisplayName: `STIG ID DTBC-0006` Description: `Extensions that are approved for use must be allowlisted`   
  * DisplayName: `STIG ID DTBC-0020` Description: `Google Data Synchronization must be disabled`  
  * DisplayName: `STIG ID DTBC-0030` Description: `Incognito mode must be disabled`  
  * DisplayName: `STIG ID DTBC-0068` Description: `Chrome development tools must be disabled`  
  * DisplayName: `STIG ID DTBC-0074` Description: `Use of the QUIC protocol must be disabled`  
* Windows Business Adobe Acrobat Reader DC Policy: This policy comes from the [DoD GPO Download](https://public.cyber.mil/stigs/gpo/).  In it you will find the Adobe template here: `Intune Policies/Device Configuration`.  No settings were removed.

#### Section 3: Importing Policies [HOME](#title-office-365-intune)  
Importing/Restoring the policies is accomplished by using the `Intune Backup & Restore` module, see links in the [References Section](#References) for detailed instructions on all the modules options.  This document will only provide examples of required commands to import templates.  Once the policies are imported they will need assignment to groups that contain computer objects.  
* Download policies using either by cloning the repository or downloading as a zip and extract the contents.   
* Installing required modules  
Note: Open PowerShell as an admin to perform the following tasks  
  * Install `Intune Backup & Restore` module
  ```
  Install-Module -Name IntuneBackupAndRestore
  ```

  * Install `Microsoft Graph Intune` module  
  ```
  Install-Module -Name Microsoft.Graph.Intune
  ```

  * Import both modules  
  ```
  Import-Module -Name IntuneBackupAndRestore
  Import-Module -Name Microsoft.Graph.Intune
  ```

  * Connect to MSGraph.  This will open a browser window to login with credentials that has permissions to manage Intune.  Generally, these will be global admin credentials.  
  ```
  Connect-MSGraph
  ```

  * Import/Restore the templates to Intune  
  Note: See screenshot below of what the root folder should look like.  
  ```
  Start-IntuneRestoreConfig -Path path\to\root\folder\of\templates  
  ```
  ![alt text](img/rootdirectory.png "Root Folder")


#### References [HOME](#title-office-365-intune)  
* [SCAP Tool](https://dl.dod.cyber.mil/wp-content/uploads/stigs/zip/scc-5.4.2_Windows_bundle.zip)
* [STIG Viewer](https://dl.dod.cyber.mil/wp-content/uploads/stigs/zip/U_STIGViewer_2-15_Win64.zip)
* [Intune Backup and Restore module(GitHub)](https://github.com/jseerden/IntuneBackupAndRestore)  
* [Intune Backup and Restore module(PowerShell Gallery)](https://www.powershellgallery.com/packages/IntuneBackupAndRestore)  
