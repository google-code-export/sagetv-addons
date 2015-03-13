# Introduction #

These instructions describe how to directly connect to the SJQv4 database.  This should only be done as a last resort.  Directly modifying the database opens up the possibility that you may corrupt your database!  Follow these directions at your own risk!

# Details #

  * Go to http://192.168.0.1:8082/ (substitute IP address with the IP of your SageTV server)
  * For the JDBC URL, enter the following: `jdbc:h2:tcp://localhost/plugins/sjq/sjq4`
  * The id and password are both `admin`
  * Click Connect

Once you're connected you can directly modify the database as needed.  Usually you'll run queries as provided in the support forum.  To execute a query simply enter it in the input box and click `Run` or press Ctrl-Enter.