## How To Reset Google Authenticator for a User

- SSH to vpn.wflops.net and sign in using your FreeIPA credentials

		$ cd /usr/local/openvpn_as/scripts
		$ sudo ./confdba -us -p <username> # Retrive current user properties
		$ sudo ./confdba -u -m -k pvt_google_auth_secret_locked -v false -p <username>  # Disable Google Auth for User		
After executing the above, the user should be able to login to the web connect at https://vpn.wflops.net to retrieve their google auth app, or rescan and re-setup their QR code and two-factor authentication.