An implementation of sqlite3\_close that finalize all active prepared statements.

On the sqlite developers mail list there is some discussion about an abi modification of sqlite3\_close that actually doesn't close the database if exists any prepared statement not finalized.

On the same mail list another developer asked for a way to retrieve the opened statements to debug/finalize.

Looking at sqlite3 internal code I created a function that close the database even if there is any prepared statement still open.


```
int sqlite3_close_v2(sqlite3 *db){
  if( !db ){
    return SQLITE_OK;
  }
  if( !sqlite3SafetyCheckSickOrOk(db) ){
    return SQLITE_MISUSE_BKPT;
  }
  /* If there are any outstanding VMs, finalize then. */
  if(db->pVdbe) {
	sqlite3_mutex_enter(db->mutex);
	while( db->pVdbe ){
		sqlite3_finalize((sqlite3_stmt*)db->pVdbe);
	}
	sqlite3_mutex_free(db->mutex);
  }
  return sqlite3_close(db);
}
```