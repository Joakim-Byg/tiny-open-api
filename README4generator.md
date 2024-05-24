# How to OpenApi Spec + openapi-generator 
This is a low code initiation to make a dotnet api with OpenApi Spec.
The steps heavily rely the [cli part](https://openapi-generator.tech/docs/installation) of the `openapi-generator`-tool.
## Generate an ASPnet Server from the spec
```shell
openapi-generator generate -i openapi.yaml -o . -g aspnetcore --additional-properties=aspnetCoreVersion=6.0
```
Even though it generates for dotnet v6.0, it is possible to modify the `Org.OpenAPITools.csproj`, field: 
`Project.PropertyGroup.TargetFramework` from `net6.0` to `net8.0`

## Adjust to make it work "out-of-the-box"
### Startup.cs
Near the bottom, between 
```csharp
...
app.UseRouting();
app.UseEndpoints(endpoints =>
...
```
insert `app.UseAuthorization();`, like so:
```csharp
...
app.UseRouting();
app.UseAuthorization();
app.UseEndpoints(endpoints =>
...
```
### Org.OpenAPITools.Authentication.ApiKeyRequirementHandler
Inside `Org.OpenAPITools.Authentication.ApiKeyRequirementHandler`, change the method: 
`SucceedRequirementIfApiKeyPresentAndValid`, so that it always hits: `context.Succeed(requirement);`  

We have effectively disabled auth, you have to provide your own means of doing so.

### Controllers
Inside: `Org.OpenAPITools.Controllers.ComputerApiController` we just want the examples to return json, so we remove the 
xml-part.

### Try it
Build with the generated build command `build.sh` or `build.bat` and run it with:
```shell
dotnet run -p src/Org.OpenAPITools/Org.OpenAPITools.csproj
```
goto: http://localhost:8080/openapi/index.html to see the OpenApi spec ui
and call `curl localhost:8080/api/computer/10` to receive some json.