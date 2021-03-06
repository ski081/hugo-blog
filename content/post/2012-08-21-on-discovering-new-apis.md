+++

author = "Mark Struzinski"
title = "On Discovering New APIs"
date =  "2011-06-11"
tags = [ "cocoa", "coding" ]
showSocial = false

+++

In working with any programming language, I've always found that if you seem to be fighting things to get work done, you're probably doing it wrong. This is especially true in Cocoa Touch. When I was first learning the frameworks, I read a lot of blogs, books, and any other materials I could get my hands on. Almost everyone said something to the effect of _"You don't get it? Read the docs again."_

It took me a while to realize that Apple has excellent documentation. It's gotten a lot better in the Microsoft world, but in the C# 2.0 and 3.5 days, you would frequently go in search of a particular framework method or property, and come across a blank page that was obviously generated by some kind of tool. Or, my favorite was when you had a property that was named something like ToolsArray, and the description would contain verbiage like "The array that holds the tools".

<!-- more -->

Apple has excellent documentation that gives common sense user cases, detailed definitions, and, in most cases, real world usage examples. I was reading the excellent new book [iOS Recipes][pragprog] [Matt Drance][drance] and [Paul Warren][warren], and discovered a new gem. This one is on the UserDefaults class.

I use the UserDefaults class in a few places in my apps, mostly to hold and maintain state, such as if the app has been launched, if the user has been registered, etc. I always found myself writing guard code like this:

{{< highlight obj-c >}}
BOOL appWasLoaded = NO;
NSUserDefaults *standardDefaults = [NSUserDefaults standardDefaults];
if([defaults objectForKey:@"appLoadedKey"]{
    appWasLoaded = [defaults boolForKey:@"appLoadedKey"];
}
{{< /highlight >}}


There is a way to ensure user defaults have some pre-configured settings, which will reduce some of the redundancy in testing that user settings key/value pairs exist. This is accomplished using the following method call on the user defaults:

```objective-c Register Defaults
[[NSUserDefaults standardUserDefaults] registerDefaults:defaults];
```

When you make this method call, the defaults variable is an NSDictionary that contains they keys and values you want to set as initial defaults. The best part about this particular method, though, is that it will only set the values in user defaults if they don't already exist. What I have been doing to set these initial defaults is to create a .plist file that includes my default values. I pass this into the code as a dictionary, then call the registerDefaults method on startup to provide the default values at runtime. Here is a quick example:

My PList looks like this:

{{< highlight xml >}}
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
    "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
        <dict>
            <key>Test</key>
            <string>TestValue</string>
        </dict>
    </plist>
{{< /highlight >}}

{{< highlight obj-c >}}
NSDictionary *defaultValues =
[NSDictionary dictionaryWithContentsOfFile:[[NSBundle mainBundle]
pathForResource:@"defaultValues"
ofType:@"plist"]];

[[NSUserDefaults standardUserDefaults] registerDefaults:defaultValues];
{{< /highlight >}}

After this point, calling

{{< highlight obj-c >}}
[[NSUserDefaults standardUserDefaults] stringForKey:@"Test"]
{{< /highlight >}}

will get you the string "TestValue", even if you haven't initialized it somewhere else in your code.

For a full rundown, make sure you check out the [official documentation][userdefaultsdocs].

[pragprog]:http://pragprog.com/book/cdirec/ios-recipes
[drance]:http://www.appleoutsider.com
[warren]:http://twitter.com/#!/pawpoise
[userdefaultsdocs]:http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/UserDefaults/Tasks/UsingDefaults.html
