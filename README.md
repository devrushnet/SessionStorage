# Blazored SessionStorage
A library to provide access to local storage in Blazor applications

[![Build Status](https://dev.azure.com/blazored/SessionStorage/_apis/build/status/Blazored.SessionStorage?branchName=master)](https://dev.azure.com/blazored/SessionStorage/_build/latest?definitionId=1&branchName=master)

[![Nuget](https://img.shields.io/nuget/v/blazored.localstorage.svg)](https://www.nuget.org/packages/Blazored.SessionStorage/)

### Installing

You can install from NuGet using the following command:

`Install-Package Blazored.SessionStorage`

Or via the Visual Studio package manager.

### Setup

You will need to register the local storage services with the service collection in your _Startup.cs_ file in Blazor Server.

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.AddBlazoredSessionStorage();
}
``` 

Or in your _Program.cs_ file in Blazor WebAssembly.

```c#
public static async Task Main(string[] args)
{
    var builder = WebAssemblyHostBuilder.CreateDefault(args);
    builder.RootComponents.Add<App>("app");

    builder.Services.AddBlazoredSessionStorage();

    await builder.Build().RunAsync();
}
```

### Configuration

The local storage provides options that can be modified by you at registration in your _Startup.cs_ file in Blazor Server.


```c#
public void ConfigureServices(IServiceCollection services)
{
    services.AddBlazoredSessionStorage(config =>
        config.JsonSerializerOptions.WriteIndented = true);
}
```
Or in your _Program.cs_ file in Blazor WebAssembly.

```c#
public static async Task Main(string[] args)
{
    var builder = WebAssemblyHostBuilder.CreateDefault(args);
    builder.RootComponents.Add<App>("app");

    builder.Services.AddBlazoredSessionStorage(config =>
        config.JsonSerializerOptions.WriteIndented = true);

    await builder.Build().RunAsync();
}
```

### Usage (Blazor WebAssembly)
To use Blazored.SessionStorage in Blazor WebAssembly, inject the `ISessionStorageService` per the example below.

```c#
@inject Blazored.SessionStorage.ISessionStorageService localStorage

@code {

    protected override async Task OnInitializedAsync()
    {
        await localStorage.SetItemAsync("name", "John Smith");
        var name = await localStorage.GetItemAsync<string>("name");
    }

}
```

With Blazor WebAssembly you also have the option of a synchronous API, if your use case requires it. You can swap the `ISessionStorageService` for `ISyncStorageService` which allows you to avoid use of `async`/`await`. For either interface, the method names are the same.

```c#
@inject Blazored.SessionStorage.ISyncStorageService localStorage

@code {

    protected override void OnInitialized()
    {
        localStorage.SetItem("name", "John Smith");
        var name = localStorage.GetItem<string>("name");
    }

}
```

### Usage (Blazor Server)

**NOTE:** Due to pre-rendering in Blazor Server you can't perform any JS interop until the `OnAfterRender` lifecycle method.

```c#
@inject Blazored.SessionStorage.ISessionStorageService localStorage

@code {

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        await localStorage.SetItemAsync("name", "John Smith");
        var name = await localStorage.GetItemAsync<string>("name");
    }

}
```

The APIs available are:

- asynchronous via `ISessionStorageService`:
  - SetItemAsync()
  - GetItemAsync()
  - RemoveItemAsync()
  - ClearAsync()
  - LengthAsync()
  - KeyAsync()
  - ContainsKeyAsync()
  
- synchronous via `ISyncStorageService` (Synchronous methods are **only** available in Blazor WebAssembly):
  - SetItem()
  - GetItem()
  - RemoveItem()
  - Clear()
  - Length()
  - Key()
  - ContainsKey()

**Note:** Blazored.SessionStorage methods will handle the serialisation and de-serialisation of the data for you.
