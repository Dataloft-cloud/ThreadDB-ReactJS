# [ThreadDB-ReactJS](https://github.com/Dataloft-cloud/ThreadDB-ReactJS)

 Required packages
```js
import {Client} from "@textile/threads-client";
import {Database} from "@textile/threads-database";
import {ThreadID} from "@textile/threads-id";
import {collect} from "streaming-iterables";
```

Identity 
[Box3-ReactJS]([https://github.com/Dataloft-cloud/Box3-ReactJS](https://github.com/Dataloft-cloud/Box3-ReactJS))

Constants
```js
const keyinfo = {key: 'API KEY'}
const db = await create()
const startDb = await start(db ,identity)
const collection = await collectionFromObject(db)
const instance = await instances(db, obj)
const query = await createQuery(db, username)

```

 Set collection structure
```js
export const obj = {
	_id: '',
	account:'',
	user: '',
	pubKey:'',
	encryptedKeys:''
}
```
Get client token
```js
export async function authorize (identity) {
	const client = await Client.withKeyInfo(keyinfo)
	await client.getToken(identity)
	return client
}
```
Create Database
```js
export async function create () {
	const db = await Database.withKeyInfo(keyinfo, "DatabaseName", undefined, undefined)
	console.log(db)
	return db
	// await db.close()
	// This will actually "destroy" the database.
	// But it might NOT be want you want in a real-world app!
	// level.destroy("dbName")
}
```
Start Database
```js
export function start (Database, identity) {
	const threadID = ThreadID.fromRandom()
	const db = Database.start(identity)
	console.log("db " +db)
	return db
}
```
Create Collection From Object
```js
export async function collectionFromObject (db) {
	const {collections} = db
	const existing = collections.get('Users')
	if (existing) {
	  return existing
	} else {
	  return await db.newCollectionFromObject('Users', obj)
	}
}
```
```js
  export async function instances(db, data) {
  const Users = db.collections.get('Users')
  if (!Users) throw new Error('Collection does not exist')
  await Users.insert(
  { _id: '', account:'Dataloft', user: data.user, pubEncrypt: data.pubEncrypt, encryptedKeys:   data.encryptedKeys},
  )

  // // Create an instance for the Collection and then save it
  // const user = new users({ _id: '', name: "jim" }) // Not yet persisted
  // await user.save() // Persist changes to db
  //
  // // Modify the `beth` instance
  // user.points = 1
  // await user.save() // Save changes
  //
  // // Modify it again
  // user.team = 'Astronauts'
  // user.points = 2
  //
  // // Save it from the Collection
  // await users.save(user)
  //
  // // Delete it from the Collection
  // await users.delete(user._id)

  // etc!
}
```
```js
export async function createQuery (db, username) {
  const Users = db.collections.get('Users')
  if (!Users) throw new Error('Collection does not exist')

  // Setup a query
  const query = {
    $or: [
      { user: username },
    ]
  }
  // Get results
  const all = await Users.find(query)
  // Loop over AsyncIterableIterator result and log the names
  for (const { key, value } of await collect(all)) {
    console.log(`${key.toString()}: ${value.user}`)
  }
}
```
