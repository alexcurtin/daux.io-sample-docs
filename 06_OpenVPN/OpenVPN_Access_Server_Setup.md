## How To Setup an OpenVPN Access Server Node

Once you've run the install script with defaults, login to the admin panel at: https://ipofserver:943/admin with the openvpn user you created. Make sure firewall routes 443 to this box and that dynect is pointing remote.wflops.net to the public IP.



### Server Network Settings
- Change hostname to remote.wflops.net
- Set protocol to TCP, port number: 443
- Service forwarding: admin web server and client web server
- We do this so that users can download the configuration file and the connect client for linux and windows
- Set admin web UI to 943
- VPN mode: NAT / layer 3
- VPN settings: [VPN Settings Here](https://wiki.westfieldlabs.com/display/POT/OpenVPN+Access+Server+Admin+UI+settings)
- Advanced VPN: [Advanced VPN Settings](https://wiki.westfieldlabs.com/display/POT/OpenVPN+Access+Server+Admin+UI+settings)
- Web server: Upload the the *.wflops.net cert, key and CA bundle. Key is in Lastpass.

### Client Settings
- Tick the box to enable Google Authenticator Support to enable 2FA

### User Permissions
- For users who need to be Admins, add their names here and tick the box for admin.

### PAM Authentication
- Enroll the server in FreeIPA as outlined here: [Linux Server Authenication](https://wiki.westfieldlabs.com/display/POT/Linux+Server+Authentication)
- Enable PAM and update server settings here: https://remote.wflops.net/admin/pam_configuration
- All users with FreeIPA accounts will now be able to login to remote.wflops.net with their IPA credentials

### Remove OpenVPN User
- SSH to the VPN node and edit the as.conf file 

		sudo vi /usr/local/openvpn_as/etc/as.conf
- comment out the following line like so:

		#boot_pam_users.0=openvpn
- Now prevent the OpenVPN user from loging using:

		sudo passwd -l openvpn
		
- as they will be exempt from using the google authenticator.