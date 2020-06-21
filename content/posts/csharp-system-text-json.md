---
title: "C# System.Text.Json"
date: 2020-06-18T21:27:09+02:00
categories: ["backend"]
tags: ["json","dotnet"]
---

There is many Json Serializer for .NET out there.

Microsoft used to depend on the `Newtonsoft.Json` (aka. Json.NET) library. However, since the .NET Core 3, Microsoft introduced their [own json serializer](https://devblogs.microsoft.com/dotnet/try-the-new-system-text-json-apis/) in the `System.Text.Json` package.

The [Michael Blog's post] published some benchmark on differences .NET Json Serializers.
His benchmark show that the new `System.Text.Json` of Microsoft is not the fastest one, but the speed favor appears to go to the [`jil` serializer](https://github.com/kevin-montrose/Jil).
However interesting comments show some missing factors in his benchmark.

* Memory footprint usage is left out in the benchmark. So the benchmark is questionable in the multi-threading / big json scenario.
* The [Wade Blog's post] show a Apple vs Orange in the benchmark of the [Michael Blog's post]: the case insensitive options are not the same default value.
  * The [Wade Blog's post] show also a massive improvement in memory footprint of `System.Text.Json` compare to the `Newtonsoft.Json`.
  * But in the speed benchmark, it left out the fact that the `System.Text.Json` is optimized for the Utf-8 encoding of HTTP message (not the Utf-16 encoding of the C# String) and conclude that the speed of `System.Text.Json` is only slightly faster than `Newtonsoft.Json` which is optimal for Utf-16.

## My take away

I believe that Microsoft knows where they are going and capable to make the best .NET Json Serializer. They will improve further the `System.Text.Json` package evens if it is (questionably) not the fastest one right now. In addition, the `System.Text.Json` package is open source and will probably attract talented contributors. It's time for me to [migrate from the `Newtonsoft.Json` to this new `System.Text.Json`](https://docs.microsoft.com/en-us/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).

[Michael Blog's post]: https://michaelscodingspot.com/the-battle-of-c-to-json-serializers-in-net-core-3/
[Wade Blog's post]: https://dotnetcoretutorials.com/2020/01/25/what-those-benchmarks-of-system-text-json-dont-mention/