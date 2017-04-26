-   Install SQL Server

        docker pull microsoft/mssql-server-linux
        docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 -d microsoft/mssql-server-linux
    
-   Install PHP
    
        brew install python
        brew tap
        brew tap homebrew/dupes
        brew tap homebrew/versions
        brew tap homebrew/homebrew-php
        brew install php71 --with-pear --with-httpd24 --with-cgi 
        brew install php71-mcrypt
        echo 'export PATH="/usr/local/sbin:$PATH"' >> ~/.bash_profile
        echo 'export PATH="/usr/local/bin:$PATH"' >> ~/.bash_profile
    
-   Install the ODBC Driver and sqlcmd Command Line Utility for SQL Server

        brew tap microsoft/mssql-preview https://github.com/Microsoft/homebrew-mssql-preview
        brew update
        ACCEPT_EULA=y brew install msodbcsql mssql-tools
    
-   Install  Microsoft PHP Driver

        brew install llvm --with-clang --with-clang-extra-tools
        brew install autoconf
        sudo pecl install sqlsrv-4.1.7preview pdo_sqlsrv-4.1.7preview
        sudo echo "extension= pdo_sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
        sudo echo "extension= sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`

-   Install Composer and setup your first Laravel Project

        curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer
        sudo composer create-project laravel/laravel quickstart --prefer-dist
        sudo chmod -R 777 todoapp
        cd todoapp
        php artisan serve --port=8080
        
-   Prepping The Database

        php artisan make:migration create_tasks_table --create=tasks
        
     Add the following to /database/*_create_tasks_table.php file

        <?php
        use Illuminate\Database\Schema\Blueprint;
        use Illuminate\Database\Migrations\Migration;
        class CreateTasksTable extends Migration
        {
            public function up()
            {
                Schema::create('tasks', function (Blueprint $table) {
                    $table->increments('id');
                    $table->string('name');
                    $table->timestamps();
                });
            }
            public function down()
            {
                Schema::drop('tasks');
            }
        }

     Edit the database.config file and then from your project directory run a migration
     
        php artisan migrate
        
 -  Create a model
 
        php artisan make:model Task
        
-   Create a route

        <?php
        use App\Task;
        use Illuminate\Http\Request;

        Route::group(['middleware' => ['web']], function () {
            /**
             * Show Task Dashboard
             */
            Route::get('/', function () {
                return view('tasks', [
                    'tasks' => Task::orderBy('created_at', 'asc')->get()
                ]);
            });
            /**
             * Add New Task
             */
            Route::post('/task', function (Request $request) {
                $validator = Validator::make($request->all(), [
                    'name' => 'required|max:255',
                ]);

                if ($validator->fails()) {
                    return redirect('/')
                        ->withInput()
                        ->withErrors($validator);
                }

                $task = new Task;
                $task->name = $request->name;
                $task->save();

                return redirect('/');
            });
            /**
             * Delete Task
             */
            Route::delete('/task/{id}', function ($id) {
                Task::findOrFail($id)->delete();

                return redirect('/');
            });
        });
        ?>
