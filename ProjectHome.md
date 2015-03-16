Here I'm going to make available the latest sqlite3 with some enhancements to the android platform.

I'm using it as a straight wrapper for sqlite3:

```

import SQLite3.Database;
import SQLite3.Stmt;
import SQLite3.Exception;
import android.os.Environment;

public final class Globals {
	public static Database mdb = null;

	public static Database getDB() {
		if (mdb == null) {
			String databaseName = Environment.getExternalStorageDirectory()
					+ "/dadbiz.db";
			mdb = new Database();
			try {
				mdb.open(databaseName, 0666);
			} catch (Exception e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
		return mdb;
	}

	public void do_search(String what){
		try {
			Stmt stmt = getDB().prepare("select id, name from mytable where id=?"); 
			stmt.bind(1, what);
			if(stmt.step()){
				int id = stmt.column_int(0);
				String name = stmt.column_string(1);
				//do whateve is needed
			}
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	
	public static String _tr(String str){
		return str;
	}
	
	public static void onExit(){
		if(mdb != null)
			try {
				mdb.close();
			} catch (Exception e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
	}

}

```

Drop the folders "SQLite3" and "libs" on your eclipse project and you are read to use.

Look at this thread http://code.google.com/p/android/issues/detail?id=191

And also at http://www.ch-werner.de/javasqlite/

Compiled with:

```
#	These flags mimick that found in Android's source
	
SQLITE3_ANDROID_ADDITIONAL_FLAGS := -DHAVE_USLEEP=1 \
	-DSQLITE_ENABLE_MEMORY_MANAGEMENT=1
	-DSQLITE_DEFAULT_AUTOVACUUM=1 \
	-DTEMP_STORE=3
	
SQLITE3_OPT_DEFINES := -DSQLITE_THREADSAFE=1 \
	-DSQLITE_DEFAULT_FILE_FORMAT=4 \
	-DSQLITE_OMIT_TCL_VARIABLE=1 \
	-DSQLITE_HAS_CODEC=1 \
	-DNDEBUG=1 \
	-DSQLITE_OMIT_LOAD_EXTENSION=1 \
	-DSQLITE3_ANDROID=1 \
	-DSQLITE_ENABLE_COLUMN_METADATA=1 \
	-DSQLITE_DEFAULT_FOREIGN_KEYS=1 \
	-DSQLITE_DEFAULT_JOURNAL_SIZE_LIMIT=1048576 \
	-DSQLITE_ENABLE_RTREE=1 \
	-DSQLITE_ENABLE_FTS3=1 \
	-DSQLITE_ENABLE_FTS3_PARENTHESIS=1
```