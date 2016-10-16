## Tar Compaction

- remove dispatcher from ELB, wait 60 seconds for traffic to stop
- downtime publisher in nagios
- ssh to publisher using free-ipa credentials
- stop monit so it doesn't alert `sudo service monit stop`
- stop AEM: `sudo -u aem /mnt/aem/crx-quickstart/bin/stop`
- run compaction with the following commands as the `aem` user in `screen`:


		$ sudo -u aem /usr/bin/java -jar /mnt/aem/oak-run-1.2.2.jar checkpoints /mnt/aem/crx-quickstart/repository/segmentstore
		$ sudo -u aem /usr/bin/java -jar /mnt/aem/oak-run-1.2.2.jar checkpoints /mnt/aem/crx-quickstart/repository/segmentstore rm-unreferenced
		$ sudo -u aem /usr/bin/java -jar /mnt/aem/oak-run-1.2.2.jar compact /mnt/aem/crx-quickstart/repository/segmentstore


### Once tar compaction is complete 
- start AEM as user aem `sudo -u aem /mnt/aem/crx-quickstart/bin/start`
- `sudo service monit start`
- tail logs at `/mnt/aem/crx-quickstart/logs` and ensure AEM starts up
- run test suite that is in the aem_deploy repo against the publisher
- if successful, add publisher back to ELB and move onto next publisher

### If compaction breaks the publisher, restore from another publisher:
- `mv /mnt/aem/crx-quickstart /mnt/aem/crx-quickstart.bak`
- generate an ssh key pair. add private key to /root/.ssh/id_rsa on the good publisher; add the public key to bad pub under /root/.ssh/authorized_keys
- on the good publisher, edit `/etc/ssh/sshd_config` to and set `PermitRootLogin yes`
- `sudo service sshd restart`
- rsync AEM:

		rsync -Pavh /mnt/aem/crx-quickstart/ root@nameofbadpublisher:/mnt/aem/crx-quickstart/

- once done, remove ssh key and public key
- restore sshd_config change and restart sshd again
- start AEM as user aem `sudo -u aem /mnt/aem/crx-quickstart/bin/start`
- `sudo service monit start`
- confirm AEM starts, remove crx-quickstart.bak