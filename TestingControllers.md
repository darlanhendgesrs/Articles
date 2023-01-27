# Testing Controllers in ASPNET Core

Testing controllers refers to the process of testing the logic of the controller actions in the application. Controllers are responsible for handling incoming HTTP requests, validating user input, and returning appropriate HTTP responses.

When testing controllers, we typically want to test the following aspects:

-   The controller returns the correct HTTP status code and response content
-   The controller calls the appropriate methods on any dependencies (e.g. services or repositories)
-   The controller properly handles invalid input or other error conditions

## Important points to take into account:

> **-   Test the controller's methods, not the framework:** 
> The goal of testing controllers is to ensure that the controller's methods are
> working as expected, not to test the framework itself. The framework
> is already thoroughly tested, so you don't need to repeat that work.
>  
> **- Test the controller's behavior, not its implementation:** 
> The controller's methods should be tested based on their behavior and the
> results they produce, not on their implementation details. For
> example, you should test that a controller action returns the expected
> view and view model, not that it calls a specific method on a specific
> service.
>     
> **-  Use test doubles for dependencies:** 
> Controllers often have dependencies on other services or repositories, 
> such as a database context or a logging service. 
> These dependencies should be replaced with test doubles, 
> such as mocks or fakes, so that you can test the controller in isolation.
>     
> **-   Test the controller's input validation:** 
> Controllers often include input validation logic to ensure that incoming 
> requests have the correct format and contain valid data. 
> This logic should be tested to ensure that it works as expected.
>     
> **-   Test the controller's error handling:** 
> Controllers should handle errors gracefully and return appropriate responses, such as HTTP error
> codes or error views. These error handling scenarios should be tested
> to ensure that the controller behaves correctly in these cases.

## How to test

To test controllers in ASPNET Core, you can use a variety of testing frameworks such as XUnit, NUnit, or MSTest. Additionally, you can use libraries such as Moq or NSubstitute to create test doubles for any dependencies that the controller relies on. If you want more details regarding libraries and test doubles please click [here](https://github.com/darlanhendgesrs/Articles/blob/main/TestsDoubles.md).

Step 1: In your project, create a new controller with a route that accepts a view model as a parameter. Example:

    [Route("api/[controller]")]
    [ApiController]
    public class MyController : ControllerBase
    {
        [HttpPost]
        public IActionResult Post([FromBody] MyViewModel viewModel)
        {
            // Do something with the view model
            return Ok();
        }
    }

Step 2: Create a new xUnit project in the same solution as your ASP.NET Core project.

Step 3: Add the necessary dependencies to the xUnit project. You will need to add the following packages:

-   Microsoft.AspNetCore.Mvc.Testing
-   Microsoft.AspNetCore.TestHost
-   Newtonsoft.Json

Step 4: Create a new test class and add the necessary using statements.

    using System.Net;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using Xunit;

Step 5: Create a test method that sends a POST request to the controller's route with a view model as the request body, and assert that the response status code is OK (200).

    [Fact]
    public async Task Post_ReturnsOk()
    {
        // Arrange
        var viewModel = new MyViewModel { /* Set properties */ };
        var content = new StringContent(JsonConvert.SerializeObject(viewModel), Encoding.UTF8, "application/json");
    
        // Act
        var response = await _client.PostAsync("/api/my", content);
    
        // Assert
        Assert.Equal(HttpStatusCode.OK, response.StatusCode);
    }

Step 6: Make sure that you have a way to instantiate the HttpClient, you can use the WebApplicationFactory class, which is provided by the Microsoft.AspNetCore.Mvc.Testing package, to create an instance of your ASPNET Core application, and an HttpClient that can be used to send requests to it.

    public class MyControllerTests : IClassFixture<WebApplicationFactory<Startup>>
    {
        private readonly HttpClient _client;
    
        public MyControllerTests(WebApplicationFactory<Startup> factory)
        {
            _client = factory.CreateClient();
        }
    }

That's it, you have now created a test that sends a POST request to the controller's route, and asserts that the response status code is OK (200). 
