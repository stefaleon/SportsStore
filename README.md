## SportsStore

### An ASP.NET Core MVC Web Application
### from [Pro ASP.NET Core MVC 2](https://www.apress.com/gp/book/9781484231494) by Adam Freeman

Freeman A. (2017) Pro ASP.NET Core MVC 2. Apress, Berkeley, CA



&nbsp;
### 00 Create an empty MVC Project

* Create an empty project using the ASP.NET Core Web Application (.NET Core) template. Ensure that .NET Core and ASP.NET Core 2.0
are selected in the menus at the top of the dialog window.


&nbsp;
### 01 Basic MVC boilerplate

* Add the *Models*, *Controllers* and *Views* folders.
* Edit *Startup.cs*.
* Add -> New Item -> Razor View Imports and edit the *_ViewImports.cshtml*.


&nbsp;
### 02 Add the unit testing project

* Add -> New Project -> xUnit Test Project (.NET Core) -> SportsStore.Tests 
* In SportsStore.Tests dependencies, add a reference to SportsStore.csproj.
* Add the *Moq* package.


&nbsp;
### 03 Start the Domain Model

* Add the *Product* class.
* Add the *IProductRepository* interface. It uses `IQueryable<T>` to allow a caller to obtain a sequence of Product objects. The `IQueryable<T>` interface is derived from the more familiar `IEnumerable<T>` interface and represents a collection of objects that can be queried, such as those managed by a database.


&nbsp;
### 04 Create a Fake Repository

* Add the *FakeProductRepository* class, which inherits from *IProductRepository*.
It implements the *IProductRepository* interface by returning a fixed collection of *Product* objects as the value of the *Products* property.
* The *AsQueryable* method is used to convert the fixed collection of objects to an `IQueryable<Product>`, which is required to implement the *IProductRepository* interface and allows to create a compatible fake repository without having to deal with real queries. 



&nbsp;
### 05 Register the Repository Service

* Create a repository service, which allows controllers to get objects that implement the *IProductRepository* interface without knowing which class is being used.
* Start developing the application using the simple *FakeProductRepository* class and then replace it with a real repository later without having to make changes in all of the classes that need access to the repository.
* Services are registered in the *ConfigureServices* method of the *Startup* class.
* The *AddTransient* method specifies that a new *FakeProductRepository* object should be created each time the *IProductRepository* interface is needed.



&nbsp;
### 06 Add the ProductController, use dependency injection

* Add the *ProductController.cs*.
* When MVC needs to create a new instance of the *ProductController* class to handle an HTTP request, it will inspect the constructor and see that it requires an object that implements the *IProductRepository* interface.
* To determine what implementation class should be used, MVC consults the configuration in the *Startup* class, which tells it that *FakeRepository* should be used and that a new instance should be created every time.
* MVC creates a new *FakeRepository* object and uses it to invoke the *ProductController* constructor in order to create the controller object that will process the HTTP request.
* This is known as **dependency injection**, and its approach allows the *ProductController* constructor to access the application’s repository through the *IProductRepository* interface without having any need to know which implementation class has been configured. Later, the fake repository will be replaced with the real
one, and the controller will continue to work without changes.
* Add an action method, called *List*, which will render a view showing the complete list of the products in the repository. 
* Calling the *View* method without specifying a view name, tells MVC to render the default view for the action method.
* Passing the collection of *Product* objects from the repository to the *View* method
provides the framework with the data with which to populate the Model object in a strongly typed view.


&nbsp;
### 07 Add Views

* Create a shared layout that will define common content that will be included in all HTML responses sent to clients. Created the *Views/Shared* folder and add `_Layout.cshtml`.
* Configure the application so that the `_Layout.cshtml` file is applied by default. Add an MVC View Start Page file called `_ViewStart.cshtml` to the Views folder.
* Add the view that will be displayed when the *List* action method is used to handle a request. Create the *Views/Product* folder and add to it a Razor view file called * List.cshtml*. 
* The *@model* expression at the top of the file specifies that the view will receive a sequence of *Product* objects from the action method as its model data. A *@foreach* expression  works through the sequence and generates a simple set of HTML elements for each *Product* object that is received.
* The *Price* property is converted to a string using the `ToString("c"`) method, which renders numerical values as currency according to the culture settings that are in effect on the server.
* The view deals only with how details of each *Product* is displayed using HTML elements, which is consistent with the **separation of concerns**. 



&nbsp;
### 08 Set the Default Route

* MVC should send requests that arrive for the root URL of the application *(http://
mysite/)* to the *List* action method in the *ProductController* class. Edit the statement in the *Startup* class that sets up the MVC classes that handle HTTP requests.
* The *Configure* method of the *Startup* class is used to set up the request pipeline, which consists of classes (known as middleware) that will inspect HTTP requests and generate responses. The *UseMvc* method sets up the MVC middleware, and one of the configuration options is the scheme that will be used to map URLs to controllers and action methods.
* The default route is set so that MVC sends requests to the *List* action method of the *Product* controller unless the request URL specifies otherwise.
* Now the app presents the fake data when run.