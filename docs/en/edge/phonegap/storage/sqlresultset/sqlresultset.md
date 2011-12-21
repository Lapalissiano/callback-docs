SQLResultSet
=======

When the executeSql method of a SQLTransaction is called it will invoke it's callback with a SQLResultSet.

Properties
-------

- __insertId__: the row ID of the row that the SQLResultSet object's SQL statement inserted into the database
- __rowsAffected__: the number of rows that were changed by the SQL statement.  If the statement did not affect any rows then it is set to 0. 
- __rows__: a SQLResultSetRowList representing the rows returned.  If no rows are returned the object will be empty.

Details
-------

When you call the SQLTransaction executeSql method it's callback methods will be called with a SQLResultSet object.  The result object has three properties. The first is the `insertId` which will return the row number of a success SQL insert statement.  If the statement inserted multiple rows, the ID of the last row will be the one returned. If the statement did not inserted a row, then calling the property will raise an INVALID_ACCESS_ERR exception. The `rowsAffected` is always 0 for a SQL select statement. For insert or update statements it returns the number of rows that have been modified.  The final property is of type SQLResultSetList and it contains the data returned from a SQL select statement.

Supported Platforms
-------------------

- Android
- BlackBerry WebWorks (OS 6.0 and higher)
- iPhone

Execute SQL Quick Example
------------------

	function queryDB(tx) {
		tx.executeSql('SELECT * FROM DEMO', [], querySuccess, errorCB);
	}

	function querySuccess(tx, results) {
		// the number of rows returned by the select statement
		console.log("Returned rows = " + results.rows.length);
		// this will be false since it was a select statement
		if (!resultSet.rowsAffected) {
			console.log('No rows affected!');
			return false;
		}
		// for an insert statement, this property will return the ID of the last inserted row
		console.log("Last inserted row ID = " + results.insertId);
	}
	
	function errorCB(err) {
		alert("Error processing SQL: "+err.code);
	}
	
	var db = window.openDatabase("Database", "1.0", "PhoneGap Demo", 200000);
	db.transaction(queryDB, errorCB);

Full Example
------------

    <!DOCTYPE html>
    <html>
      <head>
        <title>Contact Example</title>

        <script type="text/javascript" charset="utf-8" src="phonegap-1.3.0.js"></script>
        <script type="text/javascript" charset="utf-8">

        // Wait for PhoneGap to load
        //
        document.addEventListener("deviceready", onDeviceReady, false);

		// Populate the database 
		//
		function populateDB(tx) {
			tx.executeSql('DROP TABLE IF EXISTS DEMO');
			tx.executeSql('CREATE TABLE IF NOT EXISTS DEMO (id unique, data)');
			tx.executeSql('INSERT INTO DEMO (id, data) VALUES (1, "First row")');
			tx.executeSql('INSERT INTO DEMO (id, data) VALUES (2, "Second row")');
		}

		// Query the database
		//
		function queryDB(tx) {
			tx.executeSql('SELECT * FROM DEMO', [], querySuccess, errorCB);
		}

		// Query the success callback
		//
		function querySuccess(tx, results) {
			// the number of rows returned by the select statement
			console.log("Returned rows = " + results.rows.length);
			// this will be false since it was a select statement
			if (!resultSet.rowsAffected) {
				console.log('No rows affected!');
				return false;
			}
			// for an insert statement, this property will return the ID of the last inserted row
			console.log("Last inserted row ID = " + results.insertId);
		}

		// Transaction error callback
		//
		function errorCB(err) {
			console.log("Error processing SQL: "+err.code);
		}

		// Transaction success callback
		//
		function successCB() {
			var db = window.openDatabase("Database", "1.0", "PhoneGap Demo", 200000);
			db.transaction(queryDB, errorCB);
		}

		// PhoneGap is ready
		//
		function onDeviceReady() {
			var db = window.openDatabase("Database", "1.0", "PhoneGap Demo", 200000);
			db.transaction(populateDB, errorCB, successCB);
		}
	
        </script>
      </head>
      <body>
        <h1>Example</h1>
        <p>Database</p>
      </body>
    </html>
