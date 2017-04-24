-   Install SQL Server

        1. If you don’t have SQL Server 2016 Developer (or above) installed, click here to download the SQL Server exe 
        2. Run it to start the SQL installer 
        3. Click Basic in *Select an installation type* 
        4. Click Accept after you have read the license terms 
        5. (Optional) if you need to, you can choose a custom installation location for SQL Server 
        6. Click Install to proceed with the installation

        You now have SQL Server installed and running locally on your Windows computer! Check out the next section to continue installing prerequisites.

    
-   Install PHP

        Install PHP
        Download and run the installer here.

        You can download PHP using the Web Platform Installer. Once you download Web PI, open it up search for PHP 7 x64 for IIS. Download the entry which says 'PHP 7.0.9 (x64) for IIS Express'.

        
 -  Install the ODBC Driver and sqlcmd Command Line Utility for SQL Server

        sqlcmd is a command line tool that enables you to connect to SQL Server and run queries. To do this you need to install, the ODBC Connecter and sqlcmd. Once you install both the msi's, open up cmd.exe and run the following command to connect and run a basic query.
    

-  Install Laravel and the Microsoft PHP Driver for SQL Server
        wget "http://aka.ms/php_sqlsrv_7_nts.dll"
        wget "https://aka.ms/php_pdo_sqlsrv_7_nts.dll"
        echo extension=php_sqlsrv_7_nts.dll >> C:\Program^ Files\iis^ express\PHP\v7.0\php.ini
        echo extension=php_pdo_sqlsrv_7_nts.dll >> C:\Program^ Files\iis^ express\PHP\v7.0\php.ini
        wget https://getcomposer.org/Composer-Setup.exe
        Composer-Setup.exe
