-   Install SQL Server

        docker pull microsoft/mssql-server-linux
        docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 -d microsoft/mssql-server-linux
    
-   Install Python
    
        brew install python
    
-   Install the ODBC Driver and sqlcmd Command Line Utility for SQL Server

        brew tap microsoft/mssql-preview https://github.com/Microsoft/homebrew-mssql-preview
        brew update
        ACCEPT_EULA=y brew install msodbcsql mssql-tools
    
-   Install Django, pyodbc and the SQL Server Django Adapter

         mkdir SqlServerSample
         cd SqlServerSample
         virtualenv venv
         source venv/bin/activate
         pip install django pyodbc~=3.1.1 django-pyodbc-azure

