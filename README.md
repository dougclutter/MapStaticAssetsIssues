# MapStaticAssets Issues
Demonstrates issues with MapStaticAssets in .NET 9.0.7.

We have an existing .NET 8 application which has the following projects types:
- **Microsoft.NET.Sdk.BlazorWebAssembly**: *Form #1 UI*
- **Microsoft.NET.Sdk.Web**: *API* which also hosts the *Form #1 UI* project
- **Microsoft.NET.Sdk.Razor**: Razor Component Library (RCL) used by the *Form #1 UI*
- **Microsoft.NET.Sdk**: *Services* used by *API* project
- **Microsoft.NET.Sdk**: *Class Library* shared by *Services*, *API*, and the *Form #1 UI* projects

It has the following URLs:
- **/forms/api/myService** - provides APIs used by the UI
- **/forms/form1/** - serves the *Form #1 UI*

We will soon be adding the following URLs:
- **/forms/form2/** - serves *Form #2 UI*
- **/forms/form3/** - serves *Form #3 UI*

These new projects will use the existing *RCL* and *Class Library* projects.

We have been unable to follow the guidance provide in the
[Migration Docs](https://learn.microsoft.com/en-us/aspnet/core/migration/80-90?view=aspnetcore-9.0&tabs=visual-studio#replace-usestaticfiles-with-mapstaticassets)
to update `UseStaticFiles` to `MapStaticAssets`.

In this solution, we have provided a minimal reproduction of the problems we are having. 
This solution contains the following projects which were created using the current .NET 9 templates:
- **MyApp**: equivalent to our *API* project
- **MyApp.Client**: equivalent to our *Form #1 UI* project

We have updated these projects as follows:
- MyApp -> Program.cs: added `app.UsePathBase("/forms")`
- MyApp -> Program.cs: added `options.PathPrefix = "/form1"` to `AddInteractiveWebAssemblyRenderMode` per [Prefix Docs](https://learn.microsoft.com/en-us/aspnet/core/blazor/fundamentals/static-files?view=aspnetcore-9.0#prefix-for-blazor-webassembly-assets)
- MyApp -> App.razor: added `<base href="/form1/" />`
- MyApp.Client.csproj: tried adding `<StaticWebAssetBasePath>form1</StaticWebAssetBasePath>` but it didn't help

URLs are not returning expected values:

| URL | Expected | Actual |
|---- |--------- |------- |
| /forms/form1 | Form #1 UI | HTTP 404 |
| /forms       | HTTP 404   | static HTML of `App.razor` |
| /       | HTTP 404   | static HTML of `App.razor` |
