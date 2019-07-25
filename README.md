# go-mongodb-example
go-mongodb-example

## How to
  #### run mongodb in container 
  ```
    docker run --name mongodb -v /docker/data/db:/data/db -p 2277:27017 -d mongo:3.2.12 --auth
  ```
  #### create user
  ```
    $ docker exec -it mongodb mongo admin
    
    > db.createUser({
      user: "admin", 
      pwd: "secure", 
      roles: [ { role: "root", db: "admin" } ]
    })
  ```
  #### go get 
```
  go get gopkg.in/mgo.v2
```
  #### coding 
  ```go
    package main

import (
	"fmt"
	"log"

	"gopkg.in/mgo.v2"
	"gopkg.in/mgo.v2/bson"
)

// Person model for Database
type Person struct {
	Name  string
	Phone string
}

func main() {
	Host := []string{
		"127.0.0.1:2277",
		// replica set addrs...
	}
	const (
		Username   = "admin"
		Password   = "secure"
		Database   = "test"
		Collection = "student"
	)
	session, err := mgo.DialWithInfo(&mgo.DialInfo{
		Addrs:    Host,
		Username: Username,
		Password: Password,
		// Database: Database,
		// DialServer: func(addr *mgo.ServerAddr) (net.Conn, error) {
		// 	return tls.Dial("tcp", addr.String(), &tls.Config{})
		// },
	})
	if err != nil {
		panic(err)
	}
	defer session.Close()

	// Collection
	c := session.DB(Database).C(Collection)

	// Insert
	err = c.Insert(&Person{"Ale", "+55 53 8116 9639"}, &Person{"Cla", "+55 53 8402 8510"})
	if err != nil {
		log.Fatal(err)
	}
	result := Person{}
	err = c.Find(bson.M{"name": "Ale"}).One(&result)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("Phone:", result.Phone)

}

  ```
