---
layout    : post
title     : "Including Snappy as a plugin for Unreal Engine 4"
date      : 2020-01-25 11:15:05 +0200
categories: 
  - Unreal Engine 4
---

[Snappy](https://github.com/google/snappy), a fast compressor/decompressor from Google. 

It does not aim for maximum compression, or compatibility with any other compression library; instead, it aims for very high speeds and reasonable compression. For instance, compared to the fastest mode of zlib, Snappy is an order of magnitude faster for most inputs, but the resulting compressed files are anywhere from 20% to 100% bigger. (For more information, see "Performance", below.)

Let's port it to UE4.

Create a plugin with name **SnappyUE4**

Create a new module with name **Snappy**

> Module build rules in `Snappy.Build.cs`
{% highlight csharp %}
using System.IO;
using UnrealBuildTool;

public class Snappy : ModuleRules
{
	public Snappy(ReadOnlyTargetRules Target) : base(Target)
	{
        // PCH
        PrivatePCHHeaderFile = "Public/Snappy.h";
        // for Snappy correct compiling and linking
        PrivateDefinitions.Add("SNAPPY_STATIC=1");
        // Private include path
        PrivateIncludePaths.Add(Path.Combine(ModuleDirectory, "Private"));
        // Public include path
        PublicIncludePaths.Add(Path.Combine(ModuleDirectory, "Public"));
        // Epics modules
        PublicDependencyModuleNames.AddRange(
            new string[]
            {
                "Core",
                "CoreUObject",
                "Engine",
                "InputCore",
            }
        );
    }
}
{% endhighlight %}

> Module interface in `Snappy.h`
{% highlight cpp %}
#pragma once

#include "Modules/ModuleInterface.h"
#include "Modules/ModuleManager.h"

class ISnappyModule : public IModuleInterface
{
public:
    static ISnappyModule& Get()
    {
        static const FName ModuleName(TEXT("Snappy"));
        return FModuleManager::GetModuleChecked<ISnappyModule>(ModuleName);
    }

    static ISnappyModule& Load()
    {
        static const FName ModuleName(TEXT("Snappy"));
        return FModuleManager::LoadModuleChecked<ISnappyModule>(ModuleName);
    }

    virtual bool Compress(const TArray<uint8>& InBuffer, TArray<uint8>& OutBuffer) = 0;

    virtual bool Uncompress(const TArray<uint8>& InBuffer, TArray<uint8>& OutBuffer) = 0;
};
{% endhighlight %}

> And module implementation in `Snappy.cpp`
{% highlight cpp %}
#include "Snappy.h"
#include "src/snappy.h"

#define LOCTEXT_NAMESPACE "FSnappyModule"

class FSnappyModule
    : public ISnappyModule
{
    virtual bool Compress(const TArray<uint8>& InBuffer, TArray<uint8>& OutBuffer) override
    {
        size_t compressedLength(0);
        snappy::RawCompress((const ANSICHAR*)InBuffer.GetData(), InBuffer.Num(), (ANSICHAR*)OutBuffer.GetData(), &compressedLength);

        return compressedLength > 0;
    }

    virtual bool Uncompress(const TArray<uint8>& InBuffer, TArray<uint8>& OutBuffer) override
    {
        return snappy::RawUncompress((const ANSICHAR*)InBuffer.GetData(), InBuffer.Num(), (ANSICHAR*)OutBuffer.GetData());
    }
};

#undef LOCTEXT_NAMESPACE

IMPLEMENT_MODULE(FSnappyModule, Snappy)
{% endhighlight %}