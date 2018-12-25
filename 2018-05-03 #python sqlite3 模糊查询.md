### python sqlite3 模糊查询
```
self.cursor.execute(
  "select string from stringtable where string like ? and type = ?",
  ('%'+searchstr+'%', type))
```