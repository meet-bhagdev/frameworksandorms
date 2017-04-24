-   Install SQL Server

    sudo su
    curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
    curl https://packages.microsoft.com/config/ubuntu/16.04/mssql-server.list > /etc/apt/sources.list.d/mssql-server.list
    exit
    sudo apt-get update
    sudo apt-get install mssql-server
    sudo /opt/mssql/bin/mssql-conf setup
    
-   Install Python

    sudo apt-get update
    sudo apt-get install python python-pip
    pip install virtualenv
    
 -  Install the ODBC Driver and sqlcmd Command Line Utility for SQL Server

    sudo su
    curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql-tools.list
    exit
    sudo apt-get update
    sudo ACCEPT_EULA=Y apt-get install msodbcsql mssql-tools
    sudo apt-get install unixodbc-dev
    echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
    echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
    source ~/.bashrc
    

-  Install Django, pyodbc and the SQL Server Django Adapter

    mkdir SqlServerSample
    cd SqlServerSample
    virtualenv venv
    source venv/bin/activate
    pip install django pyodbc~=3.1.1 django-pyodbc-azure
    
