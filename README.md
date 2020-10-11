# MongoDB

-Connect db
$ mongo
> use admin
switched to db admin
> db.auth('admin','SECRETPASSWORD');

-create user
 db.createUser(
   {
     user: "mongodb",
     pwd: "dogmeatsubparflavour1337",
     roles: [{role: "dbOwner", db: "db_name"} ]
   }
 )
