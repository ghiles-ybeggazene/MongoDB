

### connect to mongo
 * ` use admin `
* ` db.auth('admin','SECRETPASSWORD') `

### create user
* ` db.createUser(
   {
     user: "mongodb",
     pwd: "dogmeatsubparflavour1337",
     roles: [{role: "dbOwner", db: "db_name"} ]
   }
 ) `
 
### show dbs
* ` show dbs `

### export collection from db
 * ` mongoexport --host localhost --port 27017 -u admin -p password --db ocpp --collection ipmappings --type=json --out /home/gybeggazene/ipmappings.json --fields all_the_fields --authenticationDatabase admin `
