## Tar Compaction

- remove dispatcher from ELB, wait 60 seconds for traffic to stop; if author publish fastly version 30 for maintenance page
- downtime instance in nagios
- ssh to instance using free-ipa credentials
- stop monit so it doesn't alert `sudo service monit stop`
- stop AEM: `sudo -u aem /mnt/aem/crx-quickstart/bin/stop`
- Make a backup of the AEM folder. Realisically it only needs to be the repository folder, but for the sake of sanity. Take a copy of the entire workspace. You need to make sure there's no more than 33% of the current disk utilised before doing this. This is because a tar compaction will need at times, up to 100% of the current repository size as it writes out the new repo. This is important, do not be the guy that breaks AEM and has to explain to the business that they've caused a massive outage for the CMS. You should also pay attention and ensure that we have enough inodes available.  
    
      $ df -H /mnt/aem
      $ df -i /mnt/aem

If there is enough available space, and inodes - continue.

Create the directory and then rsync only the repository over. We don't need the entire CRX filesystem as this will just slow down the process.

    $ sudo -u aem mkdir /mnt/aem/crx-quickstart_$(date +%Y-%m-%d)
    $ sudo -u aem rsync -Pav /mnt/aem/crx-quickstart{,_$(date +%Y-%m-%d)}/repository/

#### Run tar compaction

- Change directory: `cd /mnt/aem/`
- (if not created) Create the directory for compaction logs: `sudo -u aem mkdir /mnt/aem/dumps`
- (if not created) Create file: /mnt/aem/logback-compaction.xml

      <?xml version="1.0" encoding="UTF-8"?>
      <configuration debug="true">
       
        <appender name="STDERR" class="ch.qos.logback.core.FileAppender">
          <file>/mnt/aem/dumps/compaction.log</file>
          <encoder>
            <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
          </encoder>
        </appender>
       
        <logger name="org.apache.jackrabbit.oak.plugins.segment.Compactor" level="DEBUG"/>
       
        <root level="warn">
          <appender-ref ref="STDERR" />
        </root>
      </configuration>

- Delete past `sudo -u aem rm -f /mnt/aem/dumps/compaction.log` if it exists 
- Run the compaction steps (change the memory option `-Xmx` as needed for type of EC2 instance)

        $ sudo -u aem java -jar oak-run-1.2.18.jar checkpoints crx-quickstart/repository/segmentstore
        $ sudo -u aem java -jar oak-run-1.2.18.jar checkpoints crx-quickstart/repository/segmentstore rm-unreferenced  
        $ sudo -u aem nohup java -Dtar.memoryMapped=true -Doak.compaction.eagerFlush=true -server -Xmx20g -Dcompaction-progress-log=5000000 -Dlogback.configurationFile=logback-compaction.xml -Dcompress-interval=150000000 -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/mnt/aem/dumps/compaction.log -jar oak-run-1.2.18.jar compact crx-quickstart/repository/segmentstore > oak-tar-compact-a0.log 2>oak-tar-error-a0.log &

- Tail the logs to see progress: `tail -f dumps/compaction.log oak-tar-*`

### Once tar compaction is complete 
- start AEM as user aem `sudo -u aem /mnt/aem/crx-quickstart/bin/start`
- `sudo service monit start`
- tail logs at `/mnt/aem/crx-quickstart/logs` and ensure AEM starts up
- if publisher, run test suite that is in the aem_deploy repo against the instance
- if successful, add publisher back to ELB and move onto next publisher or if author, reactivate last active fastly version.

### If compaction breaks the AEM instance, restore from the backup you made earlier
This will (re) stop monit and AEM. Restore the repository to your backup state (including deleting any new files that were created) and start AEM/monit once more back to your pre-compaction state. This decision should not be taken lightly, as you will have to schedule another compaction pretty soon to ensure the most optimal performance of the AEM Author.

    $ sudo service monit stop
    $ sudo -u aem /mnt/aem/crx-quickstart/bin/stop
    $ sudo -u aem rsync --delete -Pav /mnt/aem/crx-quickstart{_$(date +%Y-%m-%d),}/repository/
    $ sudo -u aem /mnt/aem/crx-quickstart/bin/start
    $ sudo service monit start

### Tar Compaction Completed - Clean up old backups
In order to be a good Operations team member, you do not want to leave old backups around on disk. You're just going to create an alert that will fire, and wake up one of your colleagues at a bad hour. Don't be that guy.

    $ sudo -u aem echo rm -rf -- /mnt/aem/crx-quickstart_*
