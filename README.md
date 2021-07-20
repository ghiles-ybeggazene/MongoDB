

### connect to mongo
 * ` use admin `
* ` db.auth('admin','SECRETPASSWORD') `
* `mongo -u admin -p XaWBY2r5RNEu4uJp `


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

### filter data with date from db
* ` db.getCollection('evses').find({  created_at: {
        $gte: ISODate("2010-04-29T00:00:00.000Z"),
        $lt: ISODate("2010-05-01T00:00:00.000Z")
    } }) `
    
    
 ###  Count and list top 20 most repeated values of a certain field in MongoDB

* `  db.tweets.aggregate([
  {
    $unwind: "$entities.hashtags"},
    {"$group" : {_id:"$entities.hashtags", count:{$sum:1}}},
    { $sort   : { count : -1 } },
    { $limit  : 20 }
])`
