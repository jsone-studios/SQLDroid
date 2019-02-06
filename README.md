# SQLDroid

[![Build Status](https://travis-ci.org/jsone-studios/SQLDroid.svg?branch=master)](https://travis-ci.org/jsone-studios/SQLDroid)

SQLDroid is a JDBC driver for Android's sqlite database (android.database.sqlite.SQLiteDatabase) originally conceived by Kristian Lein-Mathisen. See http://sqldroid.org/.

SQLDroid lets you access your app's database through JDBC. Android ships with the necessary interfaces needed to use JDBC drivers, but it does not officially ship with a driver for its built-in SQLite database engine.  When porting code from other projects, you can conveniently replace the JDBC url to jdbc:sqlite to access an SQLite database on Android.

## Notes about my fork

The developer of the original [SQLDroid](https://github.com/SQLDroid/SQLDroid) library is very inactive. So I started looking into it, but I personally do not like his usage of ruby tools for building an android library.
So I started this fork and first moved to gradle as build tool. I stripped out the feature of building a SQLDroid version for non android projects and dropped support for the roboto version.

## Download

The first version 1.1.0 of my SQLDroid fork will soon be published. A snapshot of my current work is already available:

```groovy
repositories {
    maven {
        url 'https://nexus.jsone-studios.de/repository/maven-public'
    }
}

dependencies {
    compile 'jsone_studios.sqldroid:sqldroid:1.1.0-SNAPSHOT'
}
```

## Usage

Here is a minimal example of an Android Activity implemented in Java with SQLDroid.

```java
import java.sql.DriverManager;
import java.sql.Connection;
import java.sql.Driver;
import java.sql.SQLException;

public class MainActivity extends AppCompatActivity {

    private Connection connection;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        try {
            DriverManager.registerDriver((Driver) Class.forName("org.sqldroid.SQLDroidDriver").newInstance());
        } catch (Exception e) {
            throw new RuntimeException("Failed to register SQLDroidDriver");
        }
        String jdbcUrl = "jdbc:sqldroid:" + "/data/data/" + getPackageName() + "/my-database.db";
        try {
            this.connection = DriverManager.getConnection(jdbcUrl);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }

    @Override
    public void onDestroy() {
        if (connection != null) {
            try {
                connection.close();
            } catch (SQLException e) {
                throw new RuntimeException(e);
            }
        }
        super.onDestroy();
    }
}
```

## Debug output

You can set the SQLDroid log output level like this

    org.sqldroid.Log.LEVEL = android.util.Log.VERBOSE;

You can turn on resultset dumps like this

    org.sqldroid.SQLDroidResultSet.dump = true;

