

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
