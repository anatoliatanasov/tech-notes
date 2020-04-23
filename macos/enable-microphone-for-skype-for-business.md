# Enable Microphone Usage for Skype for Business on Mac OS #

| Version | Created by                  | Created on |
|--------:|:----------------------------|-----------:|
| 1.0     | anatoli.atanasov@expleo.pro | 23.04.2020 | 


## Table of Contents ##
[Proces Overview](#user-content-process-overview)

[More Details](#user-content-more-details)

[Resources](#user-content-resources)

## Process Overview ##
After installing Skype For Business on Mac OS Catalina (10.15.4) one can join meetings. Audio works fine, but only in inbound mode, i.e. the integration with speakers is OK. On the contrary **Microphone is not working**, i.e. the outbound audio mode is disabled.

In the section "System Preferences > Security & Privacy > Privacy > Microphone" the Skype for Business (S4B) application is not listed.

To make the S4B use the microphone on the MacOS one need to insert a record in the TCC.db which is the settings database for the OS **The Transparency Consent and Control** system.

Thanks to Fabiano Goncalves on [community.spiceworks.com](https://community.spiceworks.com/topic/2158310-mic-not-working-on-skype-for-business-calls-on-mac), here is the solution:
1. [Disable](#user-content-disable-macos-sip) the [Mac OS Security Integrity Protection](https://support.apple.com/en-us/HT204899) mode, so one can access and modify the TCC database;
2. [Insert a new record](#user-content-insert-a-new-record-in-the-tcc-access-table) in the **access** table to instruct the TCC system that S4B is to use the microphone;
3. [Enable back](#user-content-enable-macos-sip) the Mac OS Security Integrity Protection mode.

After this the S4B shall be available under the "System Preferences > Security & Privacy > Privacy > Microphone".

## More Details ##
This section provides more details into each of the high level steps identified above.

### Disable macOS SIP ###
To disable the Security Integration Protection mode of the OS, restart the computer and while it boots press multiple times/hold the **Command + R** key combination. The computer shall be started in MacOS Recovery Mode.

From the Tools menu, click the Terminal. Once the terminal is opened run the following command:
```bash
#> csrutil disable
```

This shall disable the SIP mode.

Now restart the computer once more. This time normally.

### Insert a new record in the TCC ACCESS table ###

**IMPORTANT:** Before any modifications of the *~/Library/Application Support/com.apple.TCC/TCC.db* file make of copy of it, so all changes can be reverted in case of problems. Example
```bash
#> sudo cp ~/Library/Application\ Support/com.apple.TCC/TCC.db ~/Library/Application\ Support/com.apple.TCC/TCC.db.backup
```

Using the SQLite command line client, open the database:
```bash
#> sudo sqlite3 ~/Library/Application\ Support/com.apple.TCC/TCC.db
```

Now use the DML to insert the new setting:
```sql
INSERT INTO access VALUES( 'kTCCServiceMicrophone', 'com.microsoft.SkypeForBusiness', 0, 1, 1, NULL, NULL, NULL, 'UNUSED', NULL, 0, 1541440109);
```

After successful insertion type:
```sql
> .exit
```

You can now check if S4B is available under the "System Preferences > Security & Privacy > Privacy > Microphone" applications.

### Enable macOS SIP ###

To enable the Security Integration Protection mode of the OS, restart the computer and while it boots press multiple times/hold the **Command + R** key combination. The computer shall be started in MacOS Recovery Mode.

From the Tools menu, click the Terminal. Once the terminal is opened run the following command:
```bash
#> csrutil enable
```

This shall enable the SIP mode.

Restart the computer once more. 

## Resources ## 

This guide is developed after consulting with the following articles / how-tos:
 - https://community.spiceworks.com/topic/2158310-mic-not-working-on-skype-for-business-calls-on-mac

Additional details:
 - Details on TCC - https://techzone.vmware.com/blog/vmware-workspace-one-uem-apple-macos-mojave-user-consent-data-access
 - Details on SIP - https://support.apple.com/en-us/HT204899
 