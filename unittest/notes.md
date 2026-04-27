# NUnit

## 1. The common naming format: 
```csharp
MethodName_ShouldExpectedBehavior_WhenCondition
```
exp:
```c#
deleteDataSet_ ShouldReturnSuccess_WhenUserIsAdmin 
```
# 2. Clean template
[Test]
public async Task TestName(){

// Arrange
await AuthenticateInRole(UserRole.User);
var client = webApiContext.Client;

// Act
var response = await client.GetAsync("/api/...");

// Assert
Assert.AreEqual(HttpStatusCode.OK, response.StatusCode);

}

# 3. Commonly used tests
