-   Install SQL Server

        1. If you don’t have SQL Server 2016 Developer (or above) installed, click here to download the SQL Server exe 
        2. Run it to start the SQL installer 
        3. Click Basic in *Select an installation type* 
        4. Click Accept after you have read the license terms 
        5. (Optional) if you need to, you can choose a custom installation location for SQL Server 
        6. Click Install to proceed with the installation

        You now have SQL Server installed and running locally on your Windows computer! Check out the next section to continue installing prerequisites.

    
-   Install Python

        Install Python and pip 
        Download and run the installer here.

        Add Python to your PATH environment variable 
        1. Press start 
        2. Search for "Advanced System Settings" 
        3. Click on the "Environment Variables" button 
        4. Add the location of the Python27 folder to the PATH variable in System Variables. The following is a typical value for the PATH variable C:\Python27

        Install virtualenv 
        Virtualenv enables you to create a virutal environment to isolate your application between your environments
        pip install virtualenv
        
 -  Install the ODBC Driver and sqlcmd Command Line Utility for SQL Server

        sqlcmd is a command line tool that enables you to connect to SQL Server and run queries. To do this you need to install, the ODBC Connecter and sqlcmd. Once you install both the msi's, open up cmd.exe and run the following command to connect and run a basic query.
    

-  Install Django, pyodbc and the SQL Server Django Adapter

        pip install virtualenv
        mkdir SqlServerSample
        cd SqlServerSample
        virtualenv venv
        venv\Scripts\activate
        pip install django pyodbc~=3.1.1 django-pyodbc-azure
