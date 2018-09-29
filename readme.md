# ndb/sqlite

A fork of [db_sqlite], Nim's standard library higher level SQLite database wrapper.
Latest supported Nim version is 0.19.

# Features

* Binding ``?`` parameters is done with native SQlite [`sqlite3_bind_*`][sqlite3_bind] functions instead of stringifying and then escaping every parameter.
  As a result:
  * In addition to ``?``, the ``?NNN`` syntax is supported. See [sqlite3_varparam].
  * Inserting binary blobs is handled in a proper way. See [Nim#5768].
  * It is possible to insert the `NULL` value.
* No more empty strings as a default placeholder value.
  Empty string, ``NULL``, and an absence of a row are distinguished.

[db_sqlite]: https://nim-lang.org/docs/db_sqlite.html
[sqlite3_bind]: https://www.sqlite.org/c3ref/bind_blob.html
[sqlite3_varparam]: https://www.sqlite.org/lang_expr.html#varparam
[Nim#5768]: https://github.com/nim-lang/Nim/issues/5768

# Example

```nim
import ndb/sqlite
let db = open(":memory:", "", "", "")

# Insert NULL
db.exec(sql"CREATE TABLE foo (a, b)")
db.exec(sql"INSERT INTO foo VALUES (?, ?)", 1, dbNilValue)

# Insert binary blob
db.exec(sql"CREATE TABLE blobs (a BLOB)")
db.exec(sql"INSERT INTO blobs VALUES (?)", dbBlobValue "\x00\x01\x02\x03")
let blobValue = db.getAllRows(sql"SELECT * FROM BLOBS")[0][0].b

db.close()
```