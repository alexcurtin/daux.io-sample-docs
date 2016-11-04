## Fix A Corrupt TARMk Repository

#### Disable online compaction

- Go to CRXDE Lite path - /libs/granite/operations/config/maintenance/granite:daily/granite:RevisionGC
  - Changed run mode from crx3 to crx3-disabled
  - Save All
  - stop aem `sudo -u aem /mnt/aem/crx-quickstart/bin/stop` wait for the process to finish.

## FIX ALL THE THINGS

- SSH to author
- switch to the aem user `sudo su - aem`
- check ulimit for aem user: `ulimit -n`
	- if it is default: 1024
	- edit `/etc/security/limits.conf` and add the following lines:

			aem soft nofile 4096
			aem hard nofile 10240
	
- Disconnect from shell and reconnect to get updated ulimits, check with `ulimit -n`
- Change directory to `/mnt/aem/` for all following commands

#### Count nodes

	$ java -Xmx4096m -jar oak-run-1.2.18.jar console crx-quickstart/repository/segmentstore
	$ :load https://gist.githubusercontent.com/stillalex/e7067bcb86c89bef66c8/raw/d7a5a9b839c3bb0ae5840252022f871fd38374d3/childCount.groovy
	$ countNodes(session.workingNode, true)


- close the console session
- copy out the terminal output
- strip just the node paths
- put in format: rmNode(session, "path to corrupt node")

#### Remove nodes

- Open the console and load the rmNode class; paste your rmNode lines into the console

		$ java -Xmx4096m -jar oak-run-1.2.18.jar console crx-quickstart/repository/segmentstore
		$ :load https://gist.githubusercontent.com/stillalex/43c49af065e3dd1fd5bf/raw/9e726a59f75b46e7b474f7ac763b0888d5a3f0c3/rmNode.groovy
		$ rmNode(session, "path to corrupt node")

- close the console
- count nodes again; should get no errors  
 

		$ java -Xmx4096m -jar oak-run-1.2.18.jar console crx-quickstart/repository/segmentstore
		$ :load https://gist.githubusercontent.com/stillalex/e7067bcb86c89bef66c8/raw/d7a5a9b839c3bb0ae5840252022f871fd38374d3/childCount.groovy
		$ countNodes(session.workingNode, true)

- delete tar.bak files from the repository directory: 

		rm -rf crx-quickstart/repository/segmentstore/*tar.bak

### Remove async index

- Open the console and load the rmNode class; paste your rmNode lines into the console

		$ java -Xmx4096m -jar oak-run-1.2.18.jar console crx-quickstart/repository/segmentstore
		$ :load https://gist.githubusercontent.com/stillalex/43c49af065e3dd1fd5bf/raw/9e726a59f75b46e7b474f7ac763b0888d5a3f0c3/rmNode.groovy
		$ rmNode(session, "/:async")

- Start aem
- Wait for it to fully boot
- Stop AEM

#### Run tar compaction

- Create the directory for compaction logs: `mkdir /mnt/aem/dumps`
- Create file: /mnt/aem/logback-compaction.xml

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

- Run the compaction steps

		$ java -jar oak-run-1.2.18.jar checkpoints crx-quickstart/repository/segmentstore
		$ java -jar oak-run-1.2.18.jar checkpoints crx-quickstart/repository/segmentstore rm-unreferenced  
		$ nohup java -Dtar.memoryMapped=true -Doak.compaction.eagerFlush=true -server -Xmx40g -Dcompaction-progress-log=5000000 -Dlogback.configurationFile=logback-compaction.xml -Dcompress-interval=150000000 -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/mnt/aem/dumps/compaction.log -jar oak-run-1.2.18.jar compact crx-quickstart/repository/segmentstore > oak-tar-compact-a0.log 2>oak-tar-error-a0.log &


- Tail the logs to see progress: `tail -f dumps/compaction.log oak-tar-*`