## Okta YubiKeys
Some users within the Okta space are designated to use YubiKeys rather than personal devices for 2FA authentication.  These individuals are typically centre employees that require Okta authentication but are not restricted to IT managed devices.

The Okta Seed files for YubiKeys are stored in LastPass for reference and recovery.

### <a name="how_to_program_yubikeys_for_okta"></a>How To Program YubiKeys for Okta
Okta's full documentation for programming new YubiKeys to your Okta instance can be found [here](https://help.okta.com/en/prod/Content/Topics/Security/Security_Authentication.htm?cshid=Security_Authentication_Yubikey#Security_Authentication_YubiKey).  Yubico also has supplemental Okta documentation located [here](https://help.okta.com/en/prod/Content/Resources/Images/Programming_YubiKeys_for_Okta.pdf).

Once you have the CSV file of the newly programmed Okta YubiKeys, upload the CSV file as a Seed file to the [Okta YubiKey Authentication](https://westfieldlabs-admin.okta.com/admin/access/yubikey) portal.


### How to Assign the Programmed YubiKey to a User

_A PDF version of these instructions can be found [here](/assets/yubikey_setup_instructions.pdf) to share to new users_.

1. Log into [Okta](https://westfieldlabs.okta.com).
2. Click on **Settings** under your Name at the top of the menu.
3. Click on **Edit Profile** (Okta may prompt you to re-enter your password at this stage.  Please do so.)
4. Scroll down to **Extra Verification**.  Click on **Setup** next to **YubiKey**.
5. Insert the **YubiKey** into a USB port on your computer.
6. Tap or press the circle on the **Yubikey**.  Doing so will fill in the blank text line and confirm that the **YubiKey** is recognized.
7. Click Done.  Your account is now tied to your **YubiKey**.

### How to Reset or Remove YubiKeys from Okta

_You will need **Administrator** privileges in Okta_.

1.  If you know the **Serial Number** of the key you wish to remove/reset, skip to step 4.
2.  Go to [Okta YubiKey Reports](https://westfieldlabs-admin.okta.com/reports/user/yubikey).
3.  Search for the **User** you're trying to remove/reset, then make note of their **Serial Number**.
4.  Go to the [Okta YubiKey Authentication](https://westfieldlabs-admin.okta.com/admin/access/yubikey) portal.
5.  Under **Revoke YubiKey Seed**, enter ther User's **Serial Number**.
6.  Confirm the details of the **User**.  If this is correct, go ahead and remove them.
7.  To re-add that **YubiKey**, you will need to upload a Seed file containing that key's details.  Refer to [How To Program YubiKeys for Okta](#how_to_program_yubikeys_for_okta).
