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
        sudo chmod -R 777 quickstart
        cd quickstart
        php artisan serve --port=8080
        
-   Prepping The Database

     Edit the .env file with your database credentials
     
        vim .env

        DB_CONNECTION=sqlsrv
        DB_HOST=yourservername
        DB_PORT=1433
        DB_DATABASE=yourdatabasename
        DB_USERNAME=yourusername
        DB_PASSWORD=yourpassword



-   Edit the config/database.php file with your database settings
    
        cd config
        vim database.php
        
      Paste the following below mysql section
      
        ............
      
        'pgsql' => [
                'driver'   => 'pgsql',
                'host'     => env('DB_HOST', 'localhost'),
                'database' => env('DB_DATABASE', 'forge'),
                'username' => env('DB_USERNAME', 'forge'),
                'password' => env('DB_PASSWORD', ''),
                'charset'  => 'utf8',
                'prefix'   => '',
                'schema'   => 'public',
         ],
        
        
        'sqlsrv' => [
                'driver'   => 'sqlsrv',
                'host'     => env('DB_HOST'),
                'database' => env('DB_DATABASE'),
                'username' => env('DB_USERNAME'),
                'password' => env('DB_PASSWORD'),
                'prefix'   => '',
        ],
        
        .............
        
-   Create a migration

        php artisan make:migration create_tasks_table --create=tasks
        
     Add the following to database/migrations/*_create_tasks_table.php file
      
        vim database/migrations/2017_05_02_052414_create_tasks_table.php

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

     Run a migration to create the necessary tables in your database
     
        php artisan migrate
        
 -  Create a model
 
        php artisan make:model Task
        
-   Create your routes
 
        vim routes/web.php
      
     Paste the following in web.php. This creates the routes for add task, view task and delete task

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
        
-   Create views for the routes we created

     Paste the following in resources/views/layouts/app.blade.php. This creates the base layout for your Laravel app 
    
        cd resources/views
        mkdir layouts
        vim app.blade.php
        
    
        <!DOCTYPE html>
        <html lang="en">
        <head>
            <meta charset="utf-8">
            <meta http-equiv="X-UA-Compatible" content="IE=edge">
            <meta name="viewport" content="width=device-width, initial-scale=1">

            <title>Laravel Quickstart - Basic</title>

            <!-- Fonts -->
            <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.4.0/css/font-awesome.min.css" rel='stylesheet' type='text/css'>
            <link href="https://fonts.googleapis.com/css?family=Lato:100,300,400,700" rel='stylesheet' type='text/css'>

            <!-- Styles -->
            <link href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css" rel="stylesheet">
            {{-- <link href="{{ elixir('css/app.css') }}" rel="stylesheet"> --}}

            <style>
                body {
                    font-family: 'Lato';
                }

                .fa-btn {
                    margin-right: 6px;
                }
            </style>
        </head>
        <body id="app-layout">
            <nav class="navbar navbar-default">
                <div class="container">
                    <div class="navbar-header">

                        <!-- Branding Image -->
                        <a class="navbar-brand" href="{{ url('/') }}">
                            Task List
                        </a>
                    </div>

                </div>
            </nav>

            @yield('content')

            <!-- JavaScripts -->
            <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/2.1.4/jquery.min.js"></script>
            <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/js/bootstrap.min.js"></script>
            {{-- <script src="{{ elixir('js/app.js') }}"></script> --}}
        </body>
        </html>
        
       Paste the following in resources/views/tasks.blade.php. This creates the layout for your tasks page where you can add, view and delete your tasks
  
          cd resources/views
          vim tasks.blade.php
 
          @extends('layouts.app')
          
          @section('content')
              <div class="container">
                  <div class="col-sm-offset-2 col-sm-8">
                      <div class="panel panel-default">
                          <div class="panel-heading">
                              New Task
                          </div>

                        <div class="panel-body">
                              <!-- Display Validation Errors -->
                              @include('common.errors')

                            <!-- New Task Form -->
                              <form action="{{ url('task')}}" method="POST" class="form-horizontal">
                                  {{ csrf_field() }}

                                <!-- Task Name -->
                                  <div class="form-group">
                                      <label for="task-name" class="col-sm-3 control-label">Task</label>

                                    <div class="col-sm-6">
                                          <input type="text" name="name" id="task-name" class="form-control" value="{{ old('task') }}">
                                      </div>
                                  </div>

                                <!-- Add Task Button -->
                                  <div class="form-group">
                                      <div class="col-sm-offset-3 col-sm-6">
                                          <button type="submit" class="btn btn-default">
                                              <i class="fa fa-btn fa-plus"></i>Add Task
                                          </button>
                                      </div>
                                  </div>
                              </form>
                          </div>
                      </div>

                    <!-- Current Tasks -->
                      @if (count($tasks) > 0)
                          <div class="panel panel-default">
                              <div class="panel-heading">
                                  Current Tasks
                              </div>

                            <div class="panel-body">
                                  <table class="table table-striped task-table">
                                      <thead>
                                          <th>Task</th>
                                          <th>&nbsp;</th>
                                      </thead>
                                      <tbody>
                                          @foreach ($tasks as $task)
                                              <tr>
                                                  <td class="table-text"><div>{{ $task->name }}</div></td>

                                                <!-- Task Delete Button -->
                                                  <td>
                                                      <form action="{{ url('task/'.$task->id) }}" method="POST">
                                                          {{ csrf_field() }}
                                                          {{ method_field('DELETE') }}

                                                        <button type="submit" class="btn btn-danger">
                                                              <i class="fa fa-btn fa-trash"></i>Delete
                                                          </button>
                                                      </form>
                                                  </td>
                                              </tr>
                                          @endforeach
                                      </tbody>
                                  </table>
                              </div>
                          </div>
                      @endif
                  </div>
              </div>
          @endsection

    
     Paste the following in resources/views/common/errors.blade.php. This creates the layout error conditions
     
        cd resources/views
        
        mkdir common
        vim errors.blade.php
        
        @if (count($errors) > 0)
            <!-- Form Error List -->
            <div class="alert alert-danger">
                <strong>Whoops! Something went wrong!</strong>

                <br><br>

                <ul>
                    @foreach ($errors->all() as $error)
                        <li>{{ $error }}</li>
                    @endforeach
                </ul>
            </div>
        @endif
