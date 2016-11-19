## Resetting the Admin Password

These steps will reset your AEM6 passwords to the defaults.

1. Stop the AEM6 instance.
2. Place the package [reset\_admin\_pass\_to\_admin.zip](/assets/reset_admin_pass_to_admin.zip) in `<AEM_Home>/crx-quickstart/install` (create it, if there isn't one)
3. Start the AEM6 instance.
4. The above package should have installed. It contains `/home/users/a/admin/.content.xml` with `rep:password` set to text `admin` encrypted.
5. Login is now set to...<br>
   Username: ` admin`<br>
    Password: `admin`
