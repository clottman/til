Oren says - 
> For some frameworks, like .NET 4.5, that’s all you need to do. However, targeting .NET Standard and .NET 4.x is far from “the world.” We can do better! 

He goes on to explain how one could add a LanguageTargets property to your project file that will enable you to use platform-specific toolsets, like Windows Xaml or Android Resources, in our projects. At first I thought this was necessary if I wanted my shared project to be consumable by an Android project. 

Since shared projects in our stack do not contain any platform-specific code, adding the LanguageTargets property was unnecessary. `<TargetFrameworks>netstandard1.4,net461></TargetFrameworks>` was all we needed in most shared project csprojs. 

We did have one shared project which uses some Nuget packages that do not yet explicitly list their support for netstandard1.4. In this project, we had to use 
`<TargetFrameworks>net461;netstandard1.4</TargetFrameworks>`
`<PackageTargetFallback>xamarinios10;</PackageTargetFallback>`
For some reason, we do not have to specify a PackageTargetFallback for Android here, even though this shared project is used by both Xamarin Android and Xamarin Forms iOS projects. 

In the solution containing some shared projects and our Xamarin Android/Xamarin Forms clients, we do have one misleading error relating to project targeting. 

> The project 'Mobile.Core' cannot be referenced. The referenced project is targeted to a different framework family (.NETFramework)	Source project: iOSClient			

Mobile.Core, which iOSClient claims it cannot reference, has this in its csproj:
>     <TargetFrameworks>net461;netstandard1.4</TargetFrameworks>
>     <PackageTargetFallback>xamarinios10;</PackageTargetFallback>

So, it should be able to be referenced. When we reverse the order so that `<TargetFrameworks>` lists `netstandard1.4` first, we got the opposite error: the Android project and other projects targeting `net461` claim they cannot reference Mobile.Core because it is targeting a different framework family. The errors are misleading as those projects continue to run functionality defined in Mobile.Core without a problem. 

While re-targeting our projects to support .NET Standard 1.4 in addition to .NET Framework 4.6.1, another topic that confused me was the exact syntax of the various TargetFrameworks and [PackageTargetFallbacks](https://github.com/NuGet/Home/wiki/PackageTargetFallback-(new-design-for-Imports)) I could use. Is it Xamarin.iOS10, like some blog posts used, or xamarinios10? Is `net461` valid, or should I just use `net46` like I've seen in blog posts? After some digging, I found [the schema for Nuget Target Frameworks](https://docs.microsoft.com/en-us/nuget/schema/target-frameworks) which answered these questions. 

