-   Install SQL Server

        sudo su
        curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
        curl https://packages.microsoft.com/config/ubuntu/16.04/mssql-server.list > /etc/apt/sources.list.d/mssql-server.list
        exit
        sudo apt-get update
        sudo apt-get install mssql-server
        sudo /opt/mssql/bin/mssql-conf setup
    
-   Install PHP

        sudo apt-get update
        sudo apt-get -y install php7.0 libapache2-mod-php7.0 mcrypt php7.0-mcrypt php-mbstring php-pear php7.0-dev apache2
    
 -  Install the ODBC Driver and sqlcmd Command Line Utility for SQL Server

        sudo su
        curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql-tools.list
        exit
        sudo apt-get update
        sudo ACCEPT_EULA=Y apt-get install msodbcsql mssql-tools
        sudo apt-get install unixodbc-dev gcc g++ build-essential
        echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
        echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
        source ~/.bashrc

-  Install Composer and the PHP Driver for SQL Server

        sudo pecl install sqlsrv pdo_sqlsrv
        sudo echo "extension= pdo_sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
        sudo echo "extension= sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
        curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer
