struct-create
=============

Creates Go source file of structs for use in some MySQL database packages. It uses [go-sql-driver/mysql](https://github.com/go-sql-driver/mysql) for querying the information_schema database. I created this for personal use, so it's not written for extensibility, but shouldn't be difficult to adapt for your own use.


## ADDITION:
This struct-create also adds comments at the bottom of each struct that contain: 
* all the possible SELECT statements by indexes 
* UPDATE by primary key
* a basic INSERT statemet.

The purpose of these basic SQL statements is for you to copy/paste them into the 
string, template engine or framework of choice to save you time.

There is also 3 annotations for each column: original database datatype, db and json.
##

Configuration may be set in the source file:
```
var defaults = Configuration{
	DbUser: "db_user",
	DbPassword: "db_pw",
	DbName: "bd_name",
	// PKG_NAME gives name of the package using the stucts
	PkgName: "DbStructs",
	// TAG_LABEL produces tags commonly used to match database field names with Go struct
	//members. This will be skipped if the string is empty.
	TagLabel: "db",
}
```

Or by a JSON file using the json flag `struct-create --json=test.json`
```
{
	"db_user": "db_user",
	"db_password": "db_pass",
	"db_name": "db_name",
	"pkg_name": "JsonTest",
	"tag_label": "db"
}
```

Sample output file:
```
package DbStructs

import (
	"encoding/json"
	"database/sql"
	"time"
)

type ADMINUSER struct{
        ID int64     `origin:"bigint(20)" db:"ID" json:"ID"`
        EMAIL sql.NullString    `origin:"varchar(256)" db:"EMAIL" json:"EMAIL"`
        NAME sql.NullString     `origin:"varchar(256)" db:"NAME" json:"NAME"`
        PASSWORD sql.NullString `origin:"varchar(256)" db:"PASSWORD" json:"PASSWORD"`

// Select all columns: SELECT ID,EMAIL,NAME,PASSWORD FROM ADMINUSER
// Select all by key: SELECT * FROM ADMINUSER WHERE EMAIL = ?
// Select all by key: SELECT * FROM ADMINUSER WHERE ID = ?
// Insert with all columns: INSERT INTO ADMINUSER(ID,EMAIL,NAME,PASSWORD) VALUES (?,?,?,?)
// Update all columns by primary key: UPDATE ADMINUSER SET ID=?,EMAIL=?,NAME=?,PASSWORD=? WHERE ID = ?

}

```
