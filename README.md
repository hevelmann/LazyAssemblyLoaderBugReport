# LazyAssemblyLoader bug report repo
Demo repository for a bug report of LazyAssemblyLoader (aspnet core 5 RC 1)

### Description
The app should lazy load <b>Newtonsoft.Json.dll</b> on navigation to page <b>"Newtonsoft lazy"</b> and it should load <b>LazyAssemblyLoaderBugReport.MyLazy.dll</b> on navigation to page <b>"My lazy"</b>.

The OnNavigateAsync method in App.razor:

<code>
    
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("one"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(new List<string>() { "Newtonsoft.Json.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
                var message = string.Format("expected: Newtonsoft.Json | received: {0}", string.Join(";", assemblies.Select(a => a.GetName().Name)));
                await jsRuntime.InvokeVoidAsync("alert", message);
            }
            else if (args.Path.EndsWith("two"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(new List<string>() { "LazyAssemblyLoaderBugReport.MyLazy.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
                var message = string.Format("expected: LazyAssemblyLoaderBugReport.MyLazy | received: {0}", string.Join(";", assemblies.Select(a => a.GetName().Name)));
                await jsRuntime.InvokeVoidAsync("alert", message);
            }
        }
        catch (Exception ex)
        {
            await jsRuntime.InvokeVoidAsync("alert", ex.Message);
        }
    }
    
</code>

If you run the app and hit first the nav entry <b>"Newtonsoft lazy"</b>, everything runs as expected and returns the <b>Newtonsoft.Json</b> assembly.

![Image of Newton](https://github.com/hevelmann/LazyAssemblyLoaderBugReport/blob/main/img/screen1.png)

But if you hit nav entry <b>"My lazy"</b> after hitting <b>"Newtonsoft lazy"</b> it does not run as expected. 

It should return the <b>LazyAssemblyLoaderBugReport.MyLazy</b> assembly but it returns the <b>Newtonsoft.Json</b> assembly.

The <b>LazyAssemblyLoaderBugReport.MyLazy.dll</b> was loaded from the server (see network tab in the browser) but it was not returned by the LazyAssemblyLoader.

![Image of Newton](https://github.com/hevelmann/LazyAssemblyLoaderBugReport/blob/main/img/screen2.png)


I hope this helps.


