- dotnet-install: Script used to install the .NET SDK and the shared runtime.
- [crank](https://github.com/dotnet/crank): the benchmarking infrastructure used by the .NET team to run benchmarks including (but not limited to) scenarios from the TechEmpower Web Framework Benchmarks.
- servicestack
- abp
- NUnit vs xunit
  - differences
    - xUnit has compact API. NUnit has much more methods, attributes and syntax for fluent assertions.
    - xUnit calls constructor of test-class for each test method. In contrast, NUnit calls constructor once for entire test class.
    - Difference in nuget structure
  - NUnit
    ```c#
    int _a = 3;
    [Test]
    public void Test1() => Assert.That(_a++, Is.EqualTo(3));

    [Test]
    public void Test2() => Assert.That(_a++, Is.EqualTo(3)); // NUnit fails here     
    ```
  - xunit
    ```csharp
    int _a = 3;
    [Fact]
    public void Test1() => Equal(3, _a++);

    [Fact]
    public void Test2() => Equal(3, _a++);
    ```

- expression tree
  - ReadableExpressions.Visualizers
