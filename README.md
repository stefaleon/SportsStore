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
It implements the *IProductRepository* interface by returning a fixed collection of *Product* objects as the value of the *Products* property. The *AsQueryable* method is used to convert the fixed collection of objects to an *IQueryable<Product>*, which is required to implement the *IProductRepository* interface and allows to create a compatible fake repository without having to deal with real queries. 



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
* Calling the *View8 method without specifying a view name, tells MVC to render the default view for the action method.
* Passing the collection of *Product* objects from the repository to the *View* method
provides the framework with the data with which to populate the Model object in a strongly typed view.
