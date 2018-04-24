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



&nbsp;
### 29 Create the View for the NavigationMenuViewComponent

* Razor uses different conventions for dealing with views that are selected by view components. Both the default name of the view and the locations that are searched for the view are different from those used for controllers.
* Create the *Views/Shared/Components/NavigationMenu* folder and add to it a view file called *Default.cshtml*.



&nbsp;
### 30 Highlight the Current Category

* ASP.NET Core MVC components such as controllers and view components can receive information about the current request by asking for a context object. Most of the time, you can rely on the **base** classes that you use to create components to take care of getting the context object for you, such as when you use the *Controller* base class to create controllers.
* The *ViewComponent* base class is no exception and provides access to context objects through a set of properties. One of the properties is called *RouteData*, which provides information about how the request URL was handled by the routing system.
* Use the *RouteData* property to access the request data in order to get the value for the currently selected category. Passing the category to the view could be done by creating another view model class, but for variety, use the **ViewBag** feature.
* In *NavigationMenuViewComponent.cs*, inside the *Invoke* method, dynamically assign a *SelectedCategory* property to the ViewBag object and set its value to be the current category, which is obtained through the context object returned by the *RouteData* property.
* Test that the view component correctly adds details of the selected category by reading the value of the ViewBag property in a unit test added to *NavigatioMenuViewComponentTests*.
* Update the *Default.cshtml* view which is selected by the view component and vary the CSS classes used to style the links to make the one representing the current category distinct from the others. Use a Razor expression within the class attribute to apply the *btn-primary* class to the element that represents the selected category and the *btn-secondary* class otherwise.



&nbsp;
### 31 Correct the Page Count

* Currently, the number of page links is determined by the total number of products in the repository and not the number of products in the selected category. This means that the customer can click the link for page 2 of the *Chess* category and end up with an empty page because there are not enough chess products to fill two pages.
* Update the *List* action method in the *Product* controller so that the pagination information takes the categories into account.
* Add a unit test method to the *ProductControllerTests* class to test the ability to generate the current product count for different categories. Create a mock repository that contains known data in a range of categories and then call the *List* action method requesting each category in turn.



&nbsp;
### Building the Shopping Cart


&nbsp;
### 32 Define the Cart Model

* Add a class file called *Cart.cs* to the *Models* folder.
* The *Cart* class uses the *CartLine* class, defined in the same file, to represent a product selected by the customer and the quantity the user wants to buy.
* There are methods to add an item to the cart (*AddItem*), remove a previously added item from the cart (*RemoveLine*), calculate the total cost of the items in the cart (*ComputeTotalValue*), and reset the cart by removing all the items (*Clear*).
* There is also the *Lines* property that gives access to the contents of the cart using an `IEnumerable<CartLine>`.


&nbsp;
### 33 Unit test the Cart Model

* Create *CartTests.cs* in *SportsStore.Tests*.
* The first time that a given product is added to the cart, a new *CartLine* must be added. Test with *Can_Add_New_Lines*.
* If a product is already added to the cart, the quantity of the corresponding *CartLine* must be incremented instead of creating a new *CartLine*. Test with *Can_Add_Quantity_For_Existing_Lines*.
* Users can change their mind and remove products from the cart. Test with *Can_Remove_Line*.
* Calculate the total cost of the items in the cart. Test with *Calculate_Cart_Total*.
* Ensure that the contents of the cart are properly removed when reset. Test with *Can_Clear_Contents*.


&nbsp;
### 34 Add the Add to Cart Buttons

* Add a class file called *UrlExtensions.cs* to the *Infrastructure* folder and define the *PathAndQuery* extension method.
* The *PathAndQuery* extension method operates on the *HttpRequest* class and generates the URL that the browser will be returned to after the cart has been updated, taking into account the query string if there is one.
* Add the namespace that contains the extension method to the view imports file (`_ViewImports.cshtml`) so that *PathAndQuery* can be used in the partial view.
* Add a form element that contains two hidden input elements. They specify the *ProductID* value from the view model and the URL that the browser should be returned to after the cart has been updated.
* The form element and one of the input elements are configured using built-in tag helpers, which are a useful way of generating forms that contain model values and that target controllers and actions in the application.
* The other input element uses the *PathAndQuery* extension method to set the return URL.
* Add a button element that will submit the form to the application.



&nbsp;
### 35 Enable Sessions

* Details of a user’s cart will be stored using session state, which is data that is stored at the server and associated with a series of requests made by a user. ASP.NET provides a range of different ways to store session state, including storing it in memory, which is the approach used here. The session data is lost when the application is stopped or restarted.
* Enable sessions by adding the *AddMemoryCache* and *AddSession* services and the *UseSession* middleware in the *Startup* class.
* The *AddMemoryCache* method call sets up the in-memory data store.
* The *AddSession* method registers the services used to access session data.
* The *UseSession* method allows the session system to automatically associate requests with sessions when they arrive from the client.


&nbsp;
### 36 Implement the Cart Controller

* Add *CartController.cs* to the *Controllers* folder.
* The ASP.NET session state feature is used to store and retrieve *Cart* objects, which is the purpose of the *GetCart* method. The registered middleware uses cookies or URL rewriting to associate multiple requests from a user together to form a single browsing session. A related feature is session state, which associates data with a session. This is an ideal fit for the *Cart* class: each user have their own cart, and the cart is persistent between requests.
* Data associated with a session is deleted when a session expires (typically because a user has not made a request for a while), which means that there is no need to manage the storage or life cycle of the *Cart* objects.
* For the *AddToCart* and *RemoveFromCart* action methods, the parameter names match the input elements in the HTML forms created in the *ProductSummary.cshtml* view. This allows MVC to associate incoming form POST variables with those parameters. This is known as **model binding** and is a powerful tool for simplifying controller classes.


&nbsp;
### 37 Define Session State Extension Methods

* The session state feature in ASP.NET Core stores only *int*, *string*, and *byte[]* values. To store a *Cart* object, extension methods must be defined in the *ISession* interface, which provides access to the session state data to serialize *Cart* objects into JSON and convert them back.
* Add *SessionExtensions.cs* to the *Infrastructure* folder and define the *SetJson* and *GetJson* extension methods.
* The extension methods make it easy to store and retrieve *Cart* objects. Use `HttpContext.Session.SetJson("Cart", cart);` in the controller to add a Cart to the session state. The *HttpContext* property is provided by the Controller base class and returns an *HttpContext* object that provides context data about the request that has been received and the response that is being prepared. The *HttpContext.Session* property returns an object that implements the *ISession* interface, which is the type on which the *SetJson* method is defined, which accepts arguments that specify a key and an object that will be added to the session state. The extension method serializes the object and adds it to the session state using the underlying functionality provided by the *ISession* interface.
* To retrieve the Cart again, use the *GetJson* extension method, specifying the same key: `Cart cart = HttpContext.Session.GetJson<Cart>("Cart");`. The type parameter specifies the expected type, which is used in the deserialization process.


&nbsp;
### 38 Display the Contents of the Cart

* Both the *AddToCart* and *RemoveFromCart* methods call the *RedirectToAction* method. This has the effect of sending an HTTP redirect instruction to the client
browser, asking the browser to request a new URL. In this case, the browser requests a URL that will call the *Index* action method of the *CartController*.
* Two pieces of information must be passed to the view that will display the contents of the cart: the *Cart* object and the URL to display if the user clicks the *Continue Shopping* button. Create a new class file called *CartIndexViewModel.cs* in the Models/ViewModels folder of the SportsStore project and used it to define the *CartIndexViewModel* class.
* Implement the *Index* action method in the *CartController* class. The *Index* action retrieves the *Cart* object from the session state and uses it to create a *CartIndexView* Model object, which is then passed to the *View* method to be used as the view model.
* Create the *Views/Cart* folder and add to it a Razor view file called *Index.cshtml*. The view enumerates the lines in the cart and adds rows for each of them to an HTML table, along with the total cost per line and the total cost for the cart.




&nbsp;
### Refine the Cart Model with a Service

* Use the **services** feature that sits at the heart of ASP.NET Core to simplify the way that *Cart* objects are managed, freeing individual components such as the *CartController* from needing to deal with the details directly.




&nbsp;
### 39 Create a Storage-Aware Cart Class

* Add *SessionCart.cs* to the *Models* folder.
* The *SessionCart* class subclasses the *Cart* class and overrides the *AddItem*, *RemoveLine*, and *Clear* methods so they call the base implementations and then store the updated state in the session using the extension methods on the *ISession* interface.
* The static *GetCart* method is a factory for creating *SessionCart* objects and providing them with an *ISession* object so they can store themselves. The obtained instance of the *IHttpContextAccessor* service provides access to an *HttpContext* object that, in turn,
provides the *ISession*. This indirect approach is required because the session isn’t provided as a regular service.



&nbsp;
### 40 Register the Service

* Create the *Cart Service* in *Startup.cs*.
* The *AddScoped* method specifies that the same object should be used to satisfy related requests for *Cart* instances. How requests are related can be configured, but by default, it means that any *Cart* required by components handling the same HTTP request will receive the same object. Rather than provide the *AddScoped* method with a type mapping, a lambda expression is invoked to satisfy *Cart* requests. The expression receives the collection of services that have been registered and passes the collection to the *GetCart* method of the
*SessionCart* class. The result is that requests for the *Cart* service will be handled by creating *SessionCart* objects, which will serialize themselves as session data when they are modified.
* The *AddSingleton* method specifies that the same object should always be used. The service tells MVC to use the *HttpContextAccessor* class when implementations of the *IHttpContextAccessor* interface are required. This service is required so that the current session in the *SessionCart* class can be accessed.


&nbsp;
### 41 Simplify the Cart Controller

 * Rework the *CartController* class to take advantage of the new service.
 * The *CartController* class indicates that it needs a *Cart* object by declaring a constructor argument, which has allowed the removal of the methods that read and write data from the session and the steps required to write updates. The result is a controller that is simpler and remains focused on its role in the application without having to worry about how *Cart* objects are created or persisted. And, since services are available throughout the application, any component can get hold of the user’s cart using the same technique.





&nbsp;
### Complete the Cart Functionality

* After introducing the *Cart service*, the cart functionality can be completed by allowing the customer to remove an item from the cart and by displaying a summary of the cart at the top of the page.

&nbsp;
### 42 Remove Items from the Cart

* The *RemoveFromCart* action method in the controller has already been defined and tested, so letting the customer remove items is just a matter of exposing this method in a view.
* Add a *Remove* button in each row of the cart summary.
* Add a new column to each row of the table that contains a form with hidden input elements that specify the product to be removed and the return URL, along with a button that submits the form.


&nbsp;
### 43 Add the Cart Summary Widget

* Add a widget that summarizes the contents of the cart and that can be clicked to display the cart contents throughout the application. This can be achieved by adding a view component whose output will be included in the Razor shared layout.
* Add *CartSummaryViewComponent.cs* in the *Components folder* and use it to define the view component. This view component is able to take advantage of the *Cart service* in order to receive a *Cart* object as a constructor argument. The result is a simple view component class that passes on the *Cart* object to the *View* method in order to generate the fragment of HTML that will be included in the layout.
* Create the *Views/Shared/Components/CartSummary* folder and add *Default.cshtml*. The view displays a button with the Font Awesome cart icon and, if there are items in the cart, provides a snapshot that details the number of items and their total value.
* Modify the shared layout in `_Layout.cshtml` so that the cart summary is included.


&nbsp;
### 44 Add CSS and JS dependencies locally

* Add the *lib* folder inside the *wwwroot* folder and inside it add the *css* and *js* folders.
* Add local copies of *bootstrap.min.css* and *fontawesome.js* and link to these instead of linking to the CDN links in `_Layout.cshtml`.






&nbsp;
### Submitting Orders


&nbsp;
### 45 Create the Order Model Class

* Add a class file called *Order.cs* to the *Models* folder to represent the shipping details for a customer.
* Use validation attributes from the *System.ComponentModel.DataAnnotations* namespace.
* Use the *BindNever* attribute, which prevents the user from supplying values for these properties in an HTTP request.


&nbsp;
### 46 Add the Checkout Process

* Add a *Checkout* button to the cart summary view in the *Views/Cart/Index.cshtml* file.
* Add *OrderController.cs* to the *Controllers* folder. The *Checkout* method returns the default view and passes a new *Order* object as the view model.
* Create the *Views/Order* folder and add a Razor view file called *Checkout.cshtml*. For each of the properties in the model, create a label element and an input element to capture the user input, formatted with Bootstrap. The *asp-for* attribute on the input elements is handled by a built-in tag helper that generates the *type*, *id*, *name*, and *value* attributes based on the specified model property.



&nbsp;
### Implementing Order Processing

* Process orders by writing them to the database.

&nbsp;
### 47 Extend the Database

* Add the *Orders* property to the database context class. This change is enough for Entity Framework Core to create a database migration that will allow *Order* objects to be stored in the database.
* Create the migration: `$ dotnet ef migrations add Orders`. This command tells Entity Framework Core to take a new snapshot of the application data model, work out how it differs from the previous database version, and generate a new migration called *Orders*. The new
migration will be applied automatically when the application starts because *SeedData* calls the Migrate method provided by Entity Framework Core.

&nbsp;
### Resetting the database

* When frequent changes are made to the model, the migrations and database schema may get out of sync. During development, the easiest thing to do is delete the database and start over:
```
$ dotnet ef database drop --force
```
* Once the database has been removed, re-create the database and apply the migrations:
```
$ dotnet ef database update
```


&nbsp;
### 48 Create the Order Repository

* Add a class file called *IOrderRepository.cs* to the *Models* folder and use it to define the *IOrderRepository* interface.
* To implement the order repository interface, add a class file called *EFOrderRepository.cs* to the *Models* folder and define the class *EFOrderRepository*.
* This class implements *IOrderRepository* using Entity Framework Core, allowing the set of *Order* objects that have been stored to be retrieved and allowing orders to be created or changed.
* Entity Framework Core requires instruction to load related data if it spans multiple tables. The *Include* and *ThenInclude* methods specify that when an *Order* object is read from the database, the collection associated with the *Lines* property should also be loaded along with each *Product* object associated with each collection object. This ensures that all the needed data objects are received without having to perform the queries and the data is assembled directly.
* When the user’s cart data is deserialized from the session store, the JSON package creates new objects that are not known to
Entity Framework Core, which then tries to write all the objects into the database. For the *Product* objects, this means that EFC tries to write objects that have already been stored, which causes an error. To avoid this problem, EFC is notified that the objects exist and shouldn’t be stored in the database unless they are modified, by use of *AttachRange*. This ensures that EFC won’t try to write the deserialized *Product* objects that are associated with the *Order* object.
* Register the order repository as a service in the *ConfigureServices* method of the *Startup* class.



&nbsp;
### 49 Complete the Order Controller

* Modify the constructor so that it receives the services it requires to process an order. The constructor takes two parameters, one for an *IOrderRepository* and one for a *Cart*.
* Add a new action method that will handle the HTTP form POST request when the user clicks the *Complete Order* button.
* The *Checkout* action method is decorated with the **HttpPost** attribute, which means that it will be invoked for a POST request — in this case, when the user submits the form. By use of the *model binding* system, the *Order* object is being received, completed using data from the Cart and then stored in the repository.
* MVC checks the validation constraints applied to the *Order* class using the *data annotation* attributes, and any validation problems are passed to the action method through the *ModelState* property. The *ModelState.AddModelError* method registers an error message if there are no items in the cart and the *ModelState.IsValid* property checks if there are any problems.




&nbsp;
### 50 Unit Testing for the OrderController class

* Add *OrderControllerTests.cs* in the *SportsStore.Tests* project.
* The *Cannot_Checkout_Empty_Cart* test ensures that check out cannot be performed with an empty cart. This is checked by ensuring that *SaveOrder* of the mock *IOrderRepository* implementation is never called, that the view the method returns is the default view (which will redisplay the data entered by customers and give them a chance to correct it), and that the model state being passed to the view has been marked as invalid.
* The *Cannot_Checkout_Invalid_ShippingDetails* test method works in much the same way but injects an error into the view model to simulate a problem reported by
the model binder (which would happen in production when the customer enters invalid shipping data).
* The *Can_Checkout_And_Submit_Order* test ensures that orders are processed normally when appropriate.
* There is no need to test the identification of valid shipping details. This is handled automatically by the model binder using the attributes applied to the properties of the Order class.




&nbsp;
### 51 Display Validation Errors

* MVC will use the validation attributes applied to the *Order* class to validate user data.
* Use the built-in tag helper that inspects the validation state of the data provided by the user and adds warning messages for each problem that has been discovered.
* Add an HTML element that will be processed by the tag helper to the *Checkout.cshtml* file.

*The data submitted by the user is sent to the server before it is validated, which is known as server-side validation and for which MVC has excellent support. The problem with server-side validation is that the user isn’t told about errors until after the data has been sent to the server and processed and the result page
has been generated — something that can take a few seconds on a busy server. For this reason, server-side validation is usually complemented by client-side validation, where JavaScript is used to check the values that the user has entered before the form data is sent to the server.*




&nbsp;
### 52 Display a Summary Page

* To complete the checkout process, create the view that will be shown when the browser is redirected to the *Completed* action on the *Order* controller.
* Add a Razor view file called *Completed.cshtml* to the Views/Order folder.
* The required statements to integrate this view into the application have already been added in the definition of the *Completed* action method.
*  Now customers can go through the entire process, from selecting products to checking out. If they provide valid shipping details (and have items in their cart), they will see the summary page when they click the *Complete Order* button.






&nbsp;
&nbsp;
### Managing Orders

* Create a simple administration tool to view the orders that have been received and mark them as shipped.



&nbsp;
### 53 Enhance the Model

* Enhance the model so that it can record which orders have been shipped. Add the *Shipped* property to the *Order* class, which is defined in the *Order.cs* file in the Models folder.
* To update the database to reflect the addition of the *Shipped* property to the *Order* class, open a new command prompt or PowerShell window, navigate to the *SportsStore* project folder (which is the one that contains the *Startup.cs* file) and run the migration:
```
$ dotnet ef migrations add ShippedOrders
```
* The migration will be applied automatically when the application is started and the *SeedData* class calls the *Migrate* method provided by Entity Framework Core.




&nbsp;
### 54 Add the Actions and View

* The functionality required to display and update the set of orders in the database is relatively simple because it builds on the features and infrastructure created previously.
* Add two new action methods to the *Order* controller.
  * The *List* method selects all the *Order* objects in the repository that have a *Shipped* value of false and passes them to the default view. This is the action method that will display a list of the unshipped orders to the administrator.
  * The *MarkShipped* method will receive a POST request that specifies the ID of an order, which is used to locate the corresponding *Order* object from the repository so that the *Shipped* property can be set to true and saved.

* To display the list of unshipped orders, add a Razor view file called *List.cshtml* to the *Views/Order* folder. A table element is used to display some of the details from each order, including details of which products have been purchased.
* Each order is displayed with a *Ship* button that submits a form to the *MarkShipped* action method.
* Create *_AdminLayout.cshtml* in the *Views/Shared* folder.
* Use *_AdminLayout.cshtml* to specify a different layout for the *List* view using the *Layout* property, which overrides the layout specified in the *_ViewStart.cshtml* file.

* To see and manage the orders in the application, start the application, select some products, and then check out. Then navigate to the */Order/List* URL and see a summary of the created order.By clicking the *Ship* button, the database will be updated, and the list of pending orders will be empty.

■ Note: at the moment, there is nothing to stop customers from requesting the */Order/List* URL and administering their own orders. Additional code to restrict access to action methods will be added later.



&nbsp;
### Add Catalog Management

* The convention for managing more complex collections of items is to present the user with two types of pages: a list page and an edit page. Together, these pages allow a user to create, read, update, and delete items in the collection. Collectively, these actions are known as *CRUD*.


&nbsp;
### 55 Create a CRUD Controller

* Create a separate controller for managing the product catalog. Add *AdminController.cs* to the *Controllers* folder.
* The controller constructor declares a dependency on the *IProductRepository* interface, which will be resolved when instances are created.
* The controller defines a single action method, *Index*, that calls the *View* method to select the default view for the action, passing the set of products in the database as the view model.


&nbsp;
### 56 Unit test the index action

* The behavior for the *Index* method of the *Admin* controller is that it correctly returns the *Product* objects that are in the repository.
* Test this by creating a mock repository implementation and comparing the test data with the data returned by the action method. Create a new unit test file called *AdminControllerTests.cs* in the *SportsStore.UnitTests* project.
* Add a *GetViewModel* method to the test to unpack the result from the action method and get the view model data.


&nbsp;
### 57 Implement the List View

* Add a view for the *Index* action method of the *Admin* controller. Create the *Views/Admin* folder and add a Razor file called *Index.cshtml*.
* This view contains a table that has a row for each product with cells that contain the name of the product, the price, and buttons that will allow the product to be edited or deleted by sending requests to *Edit* and *Delete* actions. In addition to the table, there is an *Add* Product button that targets the *Create* action.
* See how the products are displayed by starting the application and requesting the */Admin/Index* URL.

■ Tip: the *Edit* button is inside the form element so that the two buttons sit next to each other, working around the spacing that Bootstrap applies. The *Edit* button will send an HTTTP GET request to the server to get the current details of a product; this doesn’t require a form element. However, since the *Delete* button will make a change to the application state, an http POST request must be used—and that does require the form element.



&nbsp;
### 58 Create the Edit Action Method

* Add the *Edit* action method to the *Admin* controller. It will receive the HTTP request sent by the browser when the user clicks an *Edit* button.
* This simple method finds the product with the ID that corresponds to the *productId* parameter and passes it as a view model object to the *View* method.

### Unit test the Edit Action Method

* Test for two behaviors in the *Edit* action method. The first is that we get the correct product when a valid ID value is provided, to make sure that the expected product is being edited . The second behavior to test is that no product is returned at all when an ID value that is not in the repository is requested. Add two test methods to the *AdminControllerTests.cs* class file.



&nbsp;
### 59 Create the Edit View

* Create a view for the *Edit* action method to display. Add a Razor view file called *Edit.cshtml* to the *Views/Admin folder*.
* The view contains an HTML form that uses tag helpers to generate much of the content, including setting the target for the `form` and `a` elements, setting the content of the `label` elements, and producing the *name*, *id*, and *value* attributes for the `input` and `textarea` elements.
* See the HTML produced by the view by starting the application, navigating to the */Admin/Index* URL, and clicking the *Edit* button for one of the products.

■ Tip: A hidden input element has been used for the *ProductID* property for simplicity. The value of the *ProductID* is generated by the database as a primary key when a new object is stored by Entity Framework Core, and safely changing it can be a complex process. For most applications, the simplest approach is to prevent the user from changing the value.



&nbsp;
### 60 Update the Product Repository

* Enhance the product repository so that it is able to save changes. Add the *SaveProduct* method to the *IProductRepository* interface.
* Then add the new method to the Entity Framework Core implementation of the repository, which is defined in the *EFProductRepository.cs* file.
* The implementation of the *SaveChanges* method adds a product to the repository if the *ProductID* is 0; otherwise, it applies any changes to the existing entry in the database.
* An update needs to be performed when a *Product* parameter that has a non zero *ProductID* is received. This is done by getting a *Product* object from the repository with the same *ProductID* and updating each of the properties so they match the parameter object. This can be done because Entity Framework Core keeps track of the objects that it creates from the database.
* The object passed to the *SaveChanges* method is created by the MVC model binding feature, which means that Entity Framework Core does not know anything about the new Product object and will not apply an update to the database when it is modified. The simplest way of resolving this issue is to locate the corresponding object that Entity Framework Core does know about and update it explicitly.
* The addition of a new method in the *IProductRepository* interface has broken the fake repository class. *FakeProductRepository* has been used to kick-start
the development process and demonstrate how services can be used to seamlessly replace interface implementations without needing to modify the components that rely on them. It is not needed any further, so the interface is removed from the class declaration so that there will be no need to keep modifying the class as repository features are being added.




&nbsp;
### 61 Handle Edit POST Requests

* Implement an overload of the *Edit* action method in the *Admin* controller that will handle POST requests when the administrator clicks the *Save* button.
* Check if the model binding process has been able to validate the data submitted by the user by reading the value of the *ModelState.IsValid* property. If everything is OK, save the changes to the repository and redirect the user to the *Index* action so they see the modified list of products.
* If there is a problem with the data, render the default view again so that the user can make corrections.
* After the changes have been saved in the repository, store a message using the *temp data* feature, which is part of the ASP.NET Core **session state** feature. This is a key/value dictionary similar to the session data and view bag features. The key difference from *session data* is that *temp data* persists until it is read.
* ViewBag cannot be used in this situation because it passes data between the controller and view and it cannot hold data for longer than the current HTTP request. When an edit succeeds, the browser is redirected to a new URL, so the ViewBag data is lost.
* The session data feature could be used, but then the message would be persistent until explicitly removed.
* So, the *temp data* feature is the perfect fit. The data is restricted to a single user’s session (so that users do not see each other’s *TempData*) and will persist long enough to be read in the view rendered by the action method the user has been redirected to.


&nbsp;
### 62 Unit test Edit submissions

* For the POST-processing *Edit* action method, we need to make sure that valid updates to the *Product* object, which is received as the method argument, are passed to the product repository to be saved. We also want to check that invalid updates (where a model validation error exists) are not passed to the repository.
* Add the *Can_Save_Valid_Changes* and *Cannot_Save_Invalid_Changes* test methods to the *AdminControllerTests.cs* file.


&nbsp;
### 63 Display a Confirmation Message

* Use the message stored using *TempData* in the *_AdminLayout.cshtml* layout file.
* By handling the message in the template, messages can be created in any view that uses the template without needing to create additional Razor expressions.

■ Tip: The benefit of dealing with the message in the template like this is that users will see it displayed on whatever page is rendered after they have saved a change. At the moment they are returned to the list of products, but the workflow could be changed in order to render some other view, and the users will still see the message (as long as the next view also uses the same layout). Now all the pieces are in place to edit products. To see how it all works, start the application, navigate to the */Admin/Index* URL, click the *Edit* button, and make a change. Click the *Save button*. You will be redirected to the */Admin/Index* URL, and the *TempData* message will be displayed. The message will disappear if you reload the product list screen because *TempData* is deleted when it is read. That is convenient since we do not want old messages hanging around.
