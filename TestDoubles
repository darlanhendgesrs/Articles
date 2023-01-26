## Test doubles

Test doubles are a way to replace the real dependencies of a class or system under test with a simplified version that allows you to control the inputs and outputs of the dependency. This makes it easier to test your code in isolation, without being affected by the behavior of the real dependencies.

There are several types of test doubles, including:

-   **Dummy objects**    
-   **Stub objects**
-   **Mock objects**
-   **Fake objects**

## Dummy objects

Dummy objects are objects that are passed as arguments to a method or constructor, but are not used by the code under test. They are often used to fill in required arguments when invoking a method or constructor. They are usually used when the method or constructor requires a non-null argument, but the value of that argument is not important for the test.

For example, let's say you have a class called `Calculator` that has a method called `Add` that takes two numbers and returns their sum. The `Calculator` class has a constructor that takes an `ILogger` interface, but the logger is not used in the `Add` method. In this case, you could use a dummy object for the `ILogger` parameter in the constructor when creating an instance of `Calculator` for testing.

Here is an example in C#:

    class Calculator
    {
        private readonly ILogger _logger;
    
        public Calculator(ILogger logger)
        {
            _logger = logger;
        }
    
        public int Add(int a, int b)
        {
            return a + b;
        }
    }
    
    class DummyLogger : ILogger { }
    
    [Test]
    public void Add_ShouldReturnSumOfTwoNumbers()
    {
        // Arrange
        var calculator = new Calculator(new DummyLogger());
    
        // Act
        var result = calculator.Add(1, 2);
    
        // Assert
        Assert.AreEqual(3, result);
    }

As you can see, the `DummyLogger` class is a simple implementation of the `ILogger` interface, which is used to instantiate the `Calculator` object. Since the `Add` method does not use the `ILogger` object, it does not matter what the value of this object is.

> It's important to note that dummy objects should not be used when the
> code under test does use the argument, in that case, it would be
> better to use a stub or a mock object.

## Stub objects

Stub objects are test doubles that return predefined results when their methods are called. They are used to control the inputs to the code under test, which makes it easier to test the behavior of the code in different scenarios.

For example, let's say you have a class called `WeatherService` that has a method called `GetTemperature` that takes a city name and returns the current temperature for that city. In order to test the behavior of a class that depends on the `WeatherService`, you would want to be able to control the temperature that is returned for a given city. You could create a stub object for the `WeatherService` that always returns a predefined temperature when the `GetTemperature` method is called.

Here is an example in C#:

    class WeatherService
    {
        public int GetTemperature(string city)
        {
            // Make a call to a weather API to get the temperature
        }
    }
    
    class WeatherServiceStub : WeatherService
    {
        private readonly int _temperature;
    
        public WeatherServiceStub(int temperature)
        {
            _temperature = temperature;
        }
    
        public override int GetTemperature(string city)
        {
            return _temperature;
        }
    }
    
    [Test]
    public void GetTemperature_ShouldReturnTemperature()
    {
        // Arrange
        var weatherService = new WeatherServiceStub(20);
    
        // Act
        var temperature = weatherService.GetTemperature("London");
    
        // Assert
        Assert.AreEqual(20, temperature);
    }

As you can see, the `WeatherServiceStub` class is a subclass of the `WeatherService` class that overrides the `GetTemperature` method to always return a predefined temperature. This allows you to test the behavior of the class that depends on the `WeatherService` in different scenarios by passing in different instances of the `WeatherServiceStub` with different temperatures.

> Stubs are very useful in situations where a class or method depends on
> external services or APIs that are hard or impossible to test
> directly. By creating stubs for those services, you can test the
> behavior of your code in isolation, without having to rely on the
> actual services or APIs.

## Mock objects

Mock objects are test doubles that track whether or not their methods were called, and with what arguments. They are used to verify the behavior of the code under test, which makes it possible to test that the code interacts with its dependencies in the way that you expect.

For example, let's say you have a class called `OrderService` that has a method called `PlaceOrder` that takes an `Order` object and sends it to an external service for processing. In order to test that the `PlaceOrder` method sends the order to the external service, you could create a mock object for the service that tracks whether or not the `Send` method was called with the correct order.

Here is an example in C#:

    interface IExternalService
    {
        void Send(Order order);
    }
    
    class OrderService
    {
        private readonly IExternalService _externalService;
    
        public OrderService(IExternalService externalService)
        {
            _externalService = externalService;
        }
    
        public void PlaceOrder(Order order)
        {
            _externalService.Send(order);
        }
    }
    
    class ExternalServiceMock : IExternalService
    {
        public bool SendCalled { get; private set; }
        public Order SentOrder { get; private set; }
    
        public void Send(Order order)
        {
            SendCalled = true;
            SentOrder = order;
        }
    }
    
    [Test]
    public void PlaceOrder_ShouldSendOrderToExternalService()
    {
        // Arrange
        var externalService = new ExternalServiceMock();
        var orderService = new OrderService(externalService);
        var order = new Order();
    
        // Act
        orderService.PlaceOrder(order);
    
        // Assert
        Assert.IsTrue(externalService.SendCalled);
        Assert.AreEqual(order, externalService.SentOrder);
    }

As you can see, the `ExternalServiceMock` class is an implementation of the `IExternalService` interface that tracks whether or not the `Send` method was called, and with what order. This allows you to test that the `PlaceOrder` method calls the `Send` method with the correct order.

Mocks are very useful in situations where you want to test that the code under test interacts with its dependencies in a specific way. By creating mocks for those dependencies, you can test that the code calls the right methods with the right arguments, and that it does so in the right order.

> It's worth noting that there are some libraries like `Moq`,
> `NSubstitute`, `FakeItEasy` that make it easier to create mock objects
> and set expectations on them.

## Fake objects

Fake objects are test doubles that are implemented as full-fledged implementations of the class or interface that they are replacing. They are used to replace external dependencies that are hard or impossible to test directly, but that the code under test interacts with in a complex way.

For example, let's say you have a class called `FileService` that has a method called `Save` that takes a file and saves it to a remote file server. In order to test this class you would want to be able to control the saving process without depending on an actual file server. You could create a fake object for the `FileService` that saves the file to the local file system instead.

Here is an example in C#:

    class FileService
    {
        public void Save(string filePath, byte[] fileData)
        {
            // Save the file to a remote file server
        }
    }
    
    class FileServiceFake : FileService
    {
        public List<Tuple<string, byte[]>> SavedFiles { get; } = new List<Tuple<string, byte[]>>();
    
        public override void Save(string filePath, byte[] fileData)
        {
            SavedFiles.Add(Tuple.Create(filePath, fileData));
        }
    }
    
    [Test]
    public void Save_ShouldSaveFileToFileServer()
    {
        // Arrange
        var fileService = new FileServiceFake();
        var filePath = "file.txt";
        var fileData = Encoding.ASCII.GetBytes("Hello World!");
    
       //Act 
       fileService.Save(filePath, fileData);
      
      // Assert
      var savedFile = fileService.SavedFiles.Single();
      Assert.AreEqual(filePath, savedFile.Item1);
      Assert.AreEqual(fileData, savedFile.Item2);
    
    }

As you can see, the `FileServiceFake` class  is a subclass of the `FileService` class that overrides the `Save` method to save the file to a local list instead of a remote file server. This allows you to test the behavior of the `FileService` class  in isolation, without having to rely on an actual file server. 

> Fakes are similar to stubs, but they often have more complex
> implementations that simulate the behavior of the real class  or 
> interface that they are replacing. They are often used in situations
> where a class  or method depends on external services or APIs that are
> hard or impossible to test directly, but that the code under test
> interacts with  in a complex way.

## Third-libraries to help us

Here are a few popular third-party libraries for creating test doubles in C#:

-   Moq: A popular and widely used library for creating mock objects in C#. It provides a simple and fluent API for setting up expectations on mock objects and asserting that those expectations were met.
    
-   NSubstitute: Another popular library for creating mock objects, similar to Moq, but with a more fluent and natural syntax.
    
-   FakeItEasy: A library for creating fake objects and faking the behavior of interfaces and classes. It provides a simple and easy-to-use API for creating test doubles and setting up expectations on them.
    
-   RhinoMocks: A library for creating mock objects that is similar to Moq, but with a slightly more complex API.
    
-   Microsoft Fakes: A library that is included with Visual Studio Enterprise edition and allows to create shims.
    
-   Autofake: A library that helps you create test doubles by analyzing the code under test and automatically generating the appropriate fakes and mocks for you.


**To sum up, now you are aware of each of kind of tests exists. Also knowing the properly lib will be easier your life.**
