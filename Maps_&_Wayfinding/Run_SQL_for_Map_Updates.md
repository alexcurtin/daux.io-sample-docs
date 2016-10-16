Currently, Westfield Labs has to run SQL commands on-server to make any floor updates/changes to the Jibestream Maps.  This is temporary, and _should_ be managed by the [UXM](https://maps.westfield.io) platform on or by Q4 of 2016.

## SQL Steps for Maps

In order to manually install .sql requests from wayfinding team to UAT and Production PG databases follow these instructions:

1. Login to the USW AWS account where the PG db's are located.
2. Under RDS select the UAT PG database and then select "instance actions" and "take snapshot"
3. Name the snapshot after the JIRA ticket for the work, e.g. "POT-1480" or "POT-1480-PROD" for Prod.
4. Make sure you've taken backups for both UAT and PROD dbs.
5. Install PGadmin3 for the Mac and add a new connection to each DB. The connection string is available under each instance details but here they are anyways for ease of use:
       
        UAT
        jibe-uat-pg.cfaperozhgkr.us-west-1.rds.amazonaws.com:5432
        username: novomap8080
        password: xxxxxxxxxx
        
        PROD
        jibe01-pgdb.cfaperozhgkr.us-west-1.rds.amazonaws.com:5432 
        username: novomap8080
        password: xxxxxxxxxx
 
6. After connecting to the DB instance in PGAdmin3 navigate using the tree dropdowns to Databases -> novomap8080.
7. Select the Sherlock Holmes style magnifying glass from the top nav bar on PGAdmin3 (execute arbitrary sql) button.
8. Select "open" and navigate to the downloaded .sql that was attached to the ticket.
9. After selecting and openining the .sql verify it looks correct (syntax) and then click the second playbutton (that has the little letters PGS).
10. The script should execute and the changes (usually inserts) will have been ran. Verify the changes on UAT or ask Wayfinding to verify before moving onto Prod.
11. Do the same exact thing but taking a backup of Prod PG DB and executing/connecting in PGAdmin3 to the production database second time around. 

# Magellan Server Proxy
The Magellan Server Proxy is a node app that serves as a workaround/fix to remove ADA levels from World Trade Center maps.  You can find relevant code documentation [here](https://github.com/westfield/magellan-server-proxy).


