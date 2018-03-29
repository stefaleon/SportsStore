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


&nbsp;
### 09 Create the database context class

* Install the Nuget package *Microsoft.EntityFrameworkCore.Tools.DotNet*.
* The database context class is the bridge between the application and Entity Framework Core and provides access to the application’s data using model objects. Add *ApplicationDbContext.cs* to the Models folder.
* The *DbContext* base class provides access to the Entity Framework Core’s underlying functionality, and the *Products* property will provide access to the *Product* objects in the database. The *ApplicationDbContext* class is derived from *DbContext* and adds the properties that will be used to read and
write the application’s data. There is only one property at the moment, which will provide access to *Product* objects.


&nbsp;
### 10 Create the Repository Class

* Add *EFProductRepository.cs* to the Models folder.
* The repository implementation just maps the *Products* property defined by the *IProductRepository* interface onto the *Products* property defined by the *ApplicationDbContext* class. The *Products* property in the context class returns a `DbSet<Product>` object, which implements the `IQueryable<T>` interface and makes it easy to implement the *IProductRepository* interface when using Entity Framework Core. This ensures that queries to the database will retrieve only the objects that are required.


&nbsp;
### 11 Define the Connection String

* A connection string specifies the location and name of the database and provides configuration settings for how the application should connect to the database server. Connection strings are stored in a JSON file called *appsettings.json*.
* Add New Item -> App Settings file -> create and edit *appsettings.json*.
* Within the *Data* section of the configuration file, set the name of the connection string to *SportsStoreProducts*. The value of the *ConnectionString* item specifies that the LocalDB feature should be used for a database called *SportsStore*.


&nbsp;
### 12 Configure the database Connection

* Read the connection string and configure the application to use it to connect to the database.
* Apply changes to the *Startup* class required to receive details of the configuration data contained in the *appsettings.json* file and use it to configure Entity Framework Core.
* Add a constructor to the *Startup* class. The constructor receives the configuration data loaded from the *appsettings.json* file, which is presented through an object that implements the *IConfiguration* interface.
* The constructor assigns the *IConfiguration* object to a property called *Configuration* so that it can be used by the rest of the *Startup* class.
* The *AddDbContext* extension method sets up the services provided by Entity Framework Core for the database context class.
* Many of the methods that are used in the *Startup* class allow services and middleware features to be configured using options arguments. The argument to the *AddDbContext* method is a lambda expression that receives an options object that configures the database for the context class with the *UseSqlServer*
method and specifies the connection string, which is obtained from the *Configuration* property.
* Replace the fake repository with the real one. The components in the application that use the *IProductRepository* interface, which is just the *Product*
controller at the moment, will receive an *EFProductRepository* object when they are created, which will provide them with access to the data in the database. The fake data will be seamlessly replaced by the real data in the database without having to change the *ProductController* class.


&nbsp;
### 13 Disable Scope Verification

* The *Program* class is responsible for starting and configuring ASP.NET Core before handing control to the *Startup* class. Apply a configuration change to the dependency injection feature, so that an exception thrown with the creation of the database schema is avoided.



&nbsp;
### 14 Create the Database Migration

* Entity Framework Core is able to generate the schema for the database using the model classes through a feature called migrations. When you prepare a migration, EF Core creates a C# class that contains the SQL commands required to prepare the database. If you need to modify your model classes, then you can create a new migration that contains the SQL commands required to reflect the changes. In this way, you don’t have to worry about manually writing and testing SQL commands and can just focus on the C# model classes in the application.
* Add a *DotNetCliToolReference* to *Microsoft.EntityFrameworkCore.Tools.DotNet* in *SportStore.csproj* and build the project.
* Navigate to the SportsStore project folder and run `$ dotnet ef migrations add Initial`.
* A *Migrations* folder is created. The *Product* model class has been used to create the schema.



&nbsp;
### 15 Create the Seed Data

* To populate the database and provide some sample data, added and edit *SeedData.cs*.
* The static *EnsurePopulated* method receives an *IApplicationBuilder* argument, which is the interface used in the *Configure* method of the *Startup* class to register middleware components to handle HTTP requests, and ensures that the database has content. It obtains an *ApplicationDbContext* object through the *IApplicationBuilder* interface and calls the *Database.Migrate* method to ensure that the migration has been applied, which means that the database will be created and prepared so that it can store *Product* objects.
* Next, the number of Product objects in the database is checked. If there are no objects in the database, then the database is populated using a collection of *Product* objects using the *AddRange* method and then written to the database using the *SaveChanges* method.
* The final change is to seed the database when the application starts, by adding a call to the *EnsurePopulated* method from the Startup class.

### Display seed data
* Start the application, and the database will be created and seeded and used to provide the application with its data.
* When the browser requests the default URL for the application, the application configuration tells MVC that it needs to create a *Product* controller to handle the request. Creating a new *Product* controller means invoking the *ProductController* constructor, which requires an object that implements the
*IProductRepository* interface, and the new configuration tells MVC that an *EFProductRepository* object should be created and used for this. The *EFProductRepository *object taps into the Entity Framework Core functionality that loads data from SQL Server and converts it into *Product* objects. All of this is hidden from the *ProductController* class, which just receives an object that implements the *IProductRepository* interface and works with the data it provides. The result is that the browser window shows the sample data in the database.



&nbsp;
### 16 Add Pagination

* Add support for pagination so that the view displays a smaller number of products on a page and the user can move from page to page to view the overall catalog. Add a parameter to the List method in the Product controller.
* The *PageSize* field specifies that four products will be displayed per page. With the optional parameter *productPage* in the *List* method, the action
method displays the first page of products when MVC invokes it without an argument.
* The body of the action method gets the Product objects, orders them by the primary key, skips over the products that occur before the start of the current page, and takes the number of products specified by the *PageSize* field.



&nbsp;
### 17 Unit test Pagination

* Unit test the pagination feature by creating a mock repository, injecting it into the constructor of the *ProductController* class, and then calling the *List* method to request a specific page. Compare the resulting *Product* objects with what I would be expected from the test data in the mock implementation.
* The result is a *ViewResult* object, and the value of its *ViewData.Model* property has to be casted to the expected data type.


&nbsp;
### 18 Tag helper

* Create a tag helper in order to render some page links at the bottom of each list of products, so that customers can navigate between pages.
* Create the *Models/ViewModels* folder and add *PagingInfo.cs*.
* Create the *Infrastructure* folder and add *PageLinkTagHelper.cs*. The tag helper populates a div element with elements that correspond to pages of products.
* Most MVC components, such as controllers and views, are discovered automatically, but tag helpers have to be registered. Add a statement to *_ViewImports.cshtml* that tells MVC to look for tag helper classes in the *SportsStore.Infrastructure* namespace. Also add a `@using` expression so tha the view model classes can be referred in views without having to qualify their names with the namespace.


&nbsp;
### 19 Unit test Tag helper

* In order to test the *PageLinkTagHelper* tag helper class, call the *Process* method with test data and provide a *TagHelperOutput* object in *PageLinkTagHelperTests.cs* in *SportsStore.Tests*.
* The complexity in this test is in creating the objects that are required to create and use a tag helper. Tag helpers use *IUrlHelperFactory* objects to generate Urls that target different parts of the application. *Moq* is used to create an implementation of this interface and the related *IUrlHelper* interface that provides test data.
* The core part of the test verifies the tag helper output by using a literal string value that contains double quotes. C# is perfectly capable of working with such strings, as long as the string is prefixed with `@` and uses two sets of double quotes ( "" ) in place of one set of double quotes. Remember not
to break the literal string into separate lines unless the string you are comparing to is similarly broken.



&nbsp;
### 20 Add the View Model Data

* Provide an instance of the *PagingInfo* view model class to the view.
* Add *ProductsListViewModel.cs* to the *Models/ViewModels* folder.
* Update the *List* action method in the *ProductController* class to use the *ProductsListViewModel* class in order to provide the view with details of the products to display on the page and details of the pagination.

### Unit test the View Model Data

* Add the *Can_Send_Pagination_View_Model* unit test to *ProductControllerTests*.
* Modify the earlier pagination unit test, contained in the *Can_Paginate* method, so that it makes use of *ProductsListViewModel*.



&nbsp;
### 20 Update the view

* Now a *ProductsListViewModel* object is passed as the model data to the view. Update the *List.cshtml* file.
* Add an HTML element that the tag helper will process to create the page links.




&nbsp;
### 21 Improve the URLs

* Create URLs that are more appealing by creating a scheme that follows the pattern of composable URLs. URLs like `http://localhost/Products/Page2` make more sense to the user than `http://localhost/?productPage=2`.
* Uses the ASP.NET Core routing feature, which is responsible for processing URLs to figure out what part of the application they target. Add a new route when registering the MVC middleware in the *Configure* method of the *Startup* class.
* It is important that the new route is added before the *Default* one that is already in the method, in order to take precedence over the existing one. The routing system processes routes in the order they are listed.


&nbsp;
### 22 Style with Bootstrap

* Style the main layout and the links with Bootstrap 4 classes.
* Add the partial view *ProductSummary.cshtml* in the *Views/Shared* folder and use it in *List.cshtml*.



&nbsp;
### 23 Filter the Product List by Category

* Add the *CurrentCategory* property in *ProductsListViewModel .cs*.
* Update the *Product* controller so that the *List* action method will filter *Product* objects by category.
* Add a parameter called *category*. If *category* is not null, only those *Product* objects with a matching *Category* property are selected.
* Set the value of the *CurrentCategory* property to *category*.
* Currently the value of *PagingInfo.TotalItems* is incorrectly calculated because it doesn’t take the category filter into account.
* Update the existing unit tests in order to reflect the change in the signature of the *List* action method. Pass null as the first parameter to the *List* method in those unit tests that work with the controller.



&nbsp;
### 24 Unit Test Category Filtering

* Add the *Can_Filter_Products* method to the *ProductControllerTests* class.
* Create a mock repository containing *Product* objects that belong to a range of categories. One specific category is requested using the action method, and the results are checked to ensure that the results are the right objects in the right order.



&nbsp;
### 25 Refine the URL Scheme

* Edit the *Configure* method of the *Startup* class. Apply new routes in the proper order.
* The URL scheme that these routes represent is:
  * `/` Lists the first page of products from all categories
  * `/Page2` Lists the specified page (in this case, page 2), showing items from all categories
  * `/Soccer` Shows the first page of items from a specific category (in this case, the Soccer category)
  * `/Soccer/Page2` Shows the specified page (in this case, page 2) of items from the specified category (in this case, Soccer)

### Preserve Filtering

* The ASP.NET Core routing system is used by MVC to handle incoming requests from clients, but it also generates outgoing URLs that conform to the URL scheme and that can be embedded in web pages.  
* The *IUrlHelper* interface provides access to the URL-generating functionality.
* In *PageLinkTagHelper.cs*, add the *PageUrlValues* tag helper property and decorate it with the *HtmlAttributeName* attribute. Specify a prefix for attribute names on the element, which in this case will be *page-url-*. The value of any attribute whose name begins with this prefix will be added to the dictionary that is assigned to the *PageUrlValues* property, which is then passed to the *IUrlHelper.Action* method to generate the URL for the href attribute of the `a` elements that the tag helper produces.
* In *List.cshtml*, add a new attribute to the div element that is processed by the tag helper, specifying the category that will be used to generate the URL.
* By adding the current category, taken from the view model, URLs that contain the category are generated. When the user clicks this kind of link, the current category will be passed to the *List* action method, and the filtering will be preserved.


&nbsp;
### 26 Create the Navigation View Component

* ASP.NET Core MVC has the concept of **view components**, which are perfect for creating items such as a reusable navigation control. A view component is a C# class that provides a small amount of reusable application logic with the ability to select and display Razor partial views.
* Create the *Components* folder and add *NavigationMenuViewComponent.cs*.
* The view component’s *Invoke* method is called when the component is used in a Razor view, and the result of the *Invoke* method is inserted into the HTML sent to the browser.
* Use the component in `_Layout.cshtml` with a call to the `Component.InvokeAsync` method.


&nbsp;
### 27 Generate Category Lists

* The view component can be used to generate the list of components and then the more expressive Razor syntax can be used to render the HTML that will display them.
* Update the view component.
* The constructor defines an *IProductRepository* argument. When MVC needs to create an instance of the view component class, it will note the need to provide this argument and inspect the configuration in the *Startup* class to determine which implementation object should be used. This is
the same dependency injection feature used in the controller, and it has the same effect, which is to allow the view component to access data without knowing which repository implementation will be used.
* In the *Invoke method*, *LINQ* is used to select and order the set of categories in the repository and pass them as the argument to the *View* method, which renders the default Razor partial view, details of which are returned from the method using an *IViewComponentResult* object.

&nbsp;
### 28 Unit test Generating the Category List

* Create *NavigationMenuViewComponentTests.cs*.
* Create a list that is sorted in alphabetical order and contains no duplicates. Supply some test data that does have duplicate categories and that is not in order, pass this to the tag helper class, and assert that the data has been properly cleaned up.
