# Router

A Simple PHP Router for mapping loaded Class methods based on matching URI string or pattern

*This script only routes classes, methods or closures. It assumes that your library of classes has already been included or autoloaded*

### Basic Usage Example

Below shows a simple example of routing the index to a specific class and method.

    require 'router.php';
   
    $router = new Router;
   
    $router->actions(array(
      '/' => 'myclass.mymethod'    
    ));
   
    $router->execute();
  
## URI Patterns

There are three available parameters for matching URLS

- :string (Strings)
- :int (Integers)
- :any (Anything!)

### Basic Usage Example of Patterns

Example of URI: *'/blog/article/test-post/1'*

    require 'router.php';
    
    $router = new Router;
    
    $router->actions(array(
      '/blog/article/:string/:int' => 'blog.article'
    ));
    
    $router->execute();
  
Then, in your class the patterns are passed through as accessible paramaters.

For Example:

    class Blog {
  
      function article ($title, $id) {
        // fetch data with title and id
      }
  
    }
  
By using specific patterns for strings and integers you can filter and sanitise so data is valid.

## Request types

By default all requests are found using GET. However, if you need to use an alternative request method such as POST, PUT or DELETE. All you need to do is prepend your method with the request type.

For example:

	class Blog {
	    
	    function POST_article () {
	        // send through post data here
	        $post = $_POST;
	    }                    
	    
	}
        
## Anonymous Functions / Closures

If you are running PHP >= 5.3 you are now able to pass through an anonymous function instead of linking to a specific class or controller. It can be helpful for creating some basic functions for an api or debugging some code.

For example:

    require 'router.php';
   
    $router = new Router;
   
    $router->actions(array(
      '/debug/:string/:int' => function ($title, $id) {
      	echo $title . ' - ' . $id;
      	exit;
      }
    ));
   
    $router->execute();
    
## Direct requests

If you do not need to include this class inside a framework or full on project; you can optionally use the router as a basic framework for small tasks.

For example:

	require 'router.php';
	
	$app = new Router;
	
	$app->request('/test', function () {
		echo 'Test!';
	});
	
	$app->execute();
	
By using the magic method __call() inside the class you can create specific request types too.

For example:

	require 'router.php';
	
	$app = new Router;
	
	$app->get('/test', function () {
		echo 'Test via get!';
	});
	
	$app->post('/test', function () {
		echo 'Test via post!';
	});
	
	$app->execute();
	
If you need to group actions with the direct request approach, you can still use the actions method along with it.

For example:

	require 'router.php';
	
	$app = new Router;
	
	$app->get('/test', function () {
		echo 'Test via get!';
	});
	
	$app->post('/test', function () {
		echo 'Test via post!';
	});
	
	$app->actions(array(
		'/testing' => function () {
			echo 'testing'
		},
		'/another-test/:string/:int' => function ($title, $id) {
			echo $title . ' ' . $id;
		}
	));
	
	$app->execute();
	
If you are choosing to use this inside a project, but you are also using the router as the source of the framework, you should still be able to route to exisiting classes that have been included / autoloaded.

For example:

require 'router.php';
	
	$app = new Router;
	
	$app->get('/test', function () {
		echo 'Test via get!';
	});

	$app->request('/blog/article/:int', 'blog.article');
	
	$app->execute();
	
Something to remember is if you are using this as a framework, you'll have to use a .htaccess file and run these actions from a new file like an index.php. Below is a basic .htaccess code you can use to get started.

	AddDefaultCharset utf-8
	Options FollowSymLinks
	Options +Indexes
	RewriteEngine On
	RewriteRule ^(.*)$ index.php?$1 [QSA,L]
