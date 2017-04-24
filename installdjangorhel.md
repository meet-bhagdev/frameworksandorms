-   Install SQL Server

        sudo su
        curl https://packages.microsoft.com/config/rhel/7/mssql-server.repo > /etc/yum.repos.d/mssql-server.repo
        exit
        sudo yum install mssql-server
        sudo /opt/mssql/bin/mssql-conf setup
    
-   Install Python

        sudo yum install python python-pip python-wheel python-devel
        sudo yum group install "Development tools"
        sudo pip install virtualenv
    
 -  Install the ODBC Driver and sqlcmd Command Line Utility for SQL Server

        sudo su
        curl https://packages.microsoft.com/config/rhel/7/prod.repo > /etc/yum.repos.d/mssql-tools.repo
        exit
        sudo ACCEPT_EULA=Y yum install mssql-tools
        sudo yum install unixODBC-devel
        echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
        echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
        source ~/.bashrc
    

-  Install Django, pyodbc and the SQL Server Django Adapter

        pip install virtualenv
        mkdir SqlServerSample
        cd SqlServerSample
        virtualenv venv
        source venv/bin/activate
        pip install django pyodbc~=3.1.1 django-pyodbc-azure 
