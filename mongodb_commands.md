# Insert
doc = { "name": "Smith", "age": 30, "profession": "hacker"}

    db.people.insert(doc)
# Select 
## Find, FindOne
Retrouver un seul enregistrement dans la collection *users* où le *username* est "dwight". N'afficher que le champ *email*.
    
    db.users.findOne({'username': "dwight"}, {'email': true, '_id':false})

Retrouver tous les documents de la collection *scores* de type *essay* et de *score*= 50. N'afficher que le champ *student*.
    db.scores.find( {type:"essay", score:50}, {student:true, _id:false}) 
## $gt/e, $lt/e
Retrouver les scores compris entre 50 et 60 inclus.

    db.scores.find({ score:{$gte: 50, $lte: 60} })

Retrouver les noms compris entre A et B Inclus. (Attention MongoDB est case sensitive)
    db.scores.find({ name:{$gte: "A", $lt: "C"} })

## $type, $exists, $regex
Retourne les documents de la collection *users* pour les-quel le type du champ *name* est un **String** (cf [$type](http://docs.mongodb.org/manual/reference/operator/query/type/))

    db.users.find( {name: {$type: 2}} )

Retrouve les documents de la collection *users* pour les-quels le *name* contient "q" et a un champ *email*

    db.users.find( {name: {$regex: "q"}, email: {$exists: true} } )

## $or, $and
Retrouve les documents de la collection *scores* où score est < 50 ou > 90

    db.scores.find({$or: [{score: {$lt: 50}}, {score: {$gt: 90}}]})

## $in, $all
$all permet de retourner les documents dont le champ *friends* contient "Joe" **et** "Bob".
$in permet de retourner les documents dont le champ *favorites* contient "running" **ou** "pickles"
    
    >>> db.users.find( { friends : { $all : [ "Joe" , "Bob" ] }, favorites : { $in : [ "running" , "pickles" ] } } )
    { name : "Cliff" , friends : [ "Pete" , "Joe" , "Tom" , "Bob" ] , favorites : [ "pickles", "cycling" ] }

## Sort, Limit, Skip
Retrouve les personnes de type masculin par nom en ordre décroissant. Passe les 2 premiers et affiche les 5 suivants

    db.people.find({type: "masculin"}).sort({name: -1}).limit(5).skip(2)

## Count
Compte le nombre de document dont le *type* est un *essay* et le *score* superieur à 90

    db.scores.count({type: "essay", "score": {$gt: 90}})

# Update
## Basic
{ "_id" : "Texas", "population" : 2500000, "land_locked" : 1 }

    >>> db.foo.update({_id:"Texas"},{population:30000000})
    { "_id" : "Texas", "population" : 30000000 } # /!\ Le reste du document est modifié/supprimé

## $set, $inc
{ "_id" : "myrnarackham", "phone" : "301-512-7434", "country" : "US", "count": 1 }

    >>> db.users.update({ _id: "myrnarackham" }, { $set: {country: "RU"} })
    { "_id" : "myrnarackham", "phone" : "301-512-7434", "country" : "RU", "count": 1 }
    >>> db.users.update({ _id: "myrnarackham" }, { $inc: {count: 2} })
    { "_id" : "myrnarackham", "phone" : "301-512-7434", "country" : "RU", "count": 3 }

## $unset
{ "_id" : "jimmy" , "favorite_color" : "blue" , "interests" : [ "debating" , "politics" ] }

    >>> db.users.update({_id: "jimmy"}, {$unset: {interests: 1}})
    { "_id" : "jimmy" , "favorite_color" : "blue" }

## $push, $pushAll, $pop, $pull, $pullAll, $addToSet
{ _id : "Mike", interests : [ "chess", "botany" ] }

    >>> db.friends.update( { _id : "Mike" }, { $push : { interests : "skydiving" } } );
    { _id : "Mike", interests : [ "chess", "botany", "skydiving" ] }
    >>> db.friends.update( { _id : "Mike" }, { $pop : { interests : -1 } } );
    { _id : "Mike", interests : [ "botany", "skydiving" ] }
    >>> db.friends.update( { _id : "Mike" }, { $addToSet : { interests : "skydiving" } } );
    { _id : "Mike", interests : [ "botany", "skydiving" ] }
    >>> db.friends.update( { _id : "Mike" }, { $pushAll: { interests : [ "skydiving" , "skiing" ] } } );
    { _id : "Mike", interests : [ "botany", "skydiving", "skydiving", "skiing" ] }

## Upsert
Argument utilisé pour informer qu'un update permet l'enregistrement si non existant

    >>> db.foo.update({username:'bar'}, {'$set':{'interests':['cat', 'dog']}}, {upsert: true} );
    { "_id" : ObjectId("507b78232e8dfde94c149949"), "interests" : [ "cat", "dog" ], "username" : "bar" }

## Multi
Affecte plusieurs documents d'un coup

    >>> db.scores.update({ score: {$lt: 70}}, {$inc: {score: 20} }, {multi: true})
    # Augmente de 20 le score de tous les document dont le score est < 70

# Remove

    >>> db.scores.remove({ score: {$lt: 60}}) 
    # supprime tous les documents dont le score est < 60
    >>> db.scores.drop() 
    # supprme tous les enregistrement (mieux que db.score.remove())

# GetLastError

    >>> dp.runCommand( { getLastError: 1 })

# Index 
(permet d'augmenter considérablement la vitesse d'accès)
## ensureIndex
Indexer une collection sur une clé ascendante et une autre descendante

    >>> db.students.ensureIndex({ 'student_id':1, 'class':-1 })
### Option: unique
Indexer une clé unique

    >>> db.students.ensureIndex({ 'name': 1 }, { 'unique': true })
### Option: dropDups
Supprime les documents en conflit

    >>> db.students.ensureIndex({ 'name': 1 }, { 'unique': true, 'dropDup': true })
### Option: sparse
Permet d'indexer une collection sans tenir compte des champs n’existant pas.

    { "_id" : ObjectId("50a464fb0a9dfcc4f19d6271"), "name" : "Andrew", "title" : "Jester" }
    { "_id" : ObjectId("50a4650c0a9dfcc4f19d6272"), "name" : "Dwight", "title" : "CEO" }
    { "_id" : ObjectId("50a465280a9dfcc4f19d6273"), "name" : "John" }
    >>> db.people.ensureIndex({title:1}, {sparse:1}) 
    >>> db.people.find({title:null})
Cette requête ne va retourner aucun enregistrement car il n'y a aucun `title:null`

## system.indexes.find()
    >>>  db.system.indexes.find()
    { "v" : 1, "key" : { "_id" : 1 }, "ns" : "school.students", "name" : "_id_" }
    { "v" : 1, "key" : { "student_id" : 1, "class" : -1 }, "ns" : "school.students", "name" : "student_id_1_class_-1" }

## getIndexes
Affiche les index de la collection

    >>> db.students.getIndexes()
        [
      {
        "v" : 1,
        "key" : {
          "_id" : 1
        },
        "ns" : "school.students",
        "name" : "_id_"
      },
      {
        "v" : 1,
        "key" : {
          "student_id" : 1,
          "class" : -1
        },
        "ns" : "school.students",
        "name" : "student_id_1_class_-1"
      }
    ]
## dropIndex
Supprime un index de la collection

    >>> db.posts.dropIndex({'tags':1})
    { "nIndexesWas" : 4, "ok" : 1 }

## explain
Affiche le résultat de la requête en expliquant si oui ou non les index ont été utilisés et si oui, comment.

    {
      "cursor" : "BtreeCursor _id_",
      "isMultiKey" : false,
      "n" : 1,
      "nscannedObjects" : 1,
      "nscanned" : 1,
      "nscannedObjectsAllPlans" : 1,
      "nscannedAllPlans" : 1,
      "scanAndOrder" : false,
      "indexOnly" : false,
      "nYields" : 0,
      "nChunkSkips" : 0,
      "millis" : 0,
      "indexBounds" : {
        "start" : {
          "_id" : 0
        },
        "end" : {
          "_id" : 0
        }
      },
      "server" : "alain-p6715fr:27017"
    }
## hint
Permet de choisir l'index utilisé lors de la requête.

Par exemple ici, la requête retourne tous les documents car on force l'utilisation de l'index `naturel`. Si l'on
avait laissé l'utilisation de l'index `title`, la requête n'aurait pas retourné les documents sans titre.

    >>> db.people.find().sort({'title':1}).hint({$natural:1})

### pymongo hint

    doc = foo.find(query).hint([('c', pymongo.ASCENDING)]).explain()

## Profiling
Voir le trafic de la BD. Il y a 3 niveaux disponibles : 0 (off), 1 (slow query), 2 (all). 

    >>> db.getProfilingLevel()
    0
    >>> db.setProfilingLevel(1, 1)
    { "was" : 0, "slowms" : 100, "ok" : 1 }
    >>> db.getProfilingLevel()
    { "was" : 1, "slowms" : 1 }

# Drop de database

    >>> use blog
    switched to db blog
    >>> db.dropDatabase();
    { "dropped" : "blog", "ok" : 1 }

