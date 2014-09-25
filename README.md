# Harpy
### Notify users when a new version of your app is available, and prompt them with the App Store link.

---
### About
**Harpy** is a utility that checks a user's currently installed version of your iOS application against the version that is currently available in the App Store. If a new version is available, an instance of UIAlertView is presented to the user informing them of the newer version, and giving them the option to update the application. 

This library is built to work with the [Semantic Versioning](http://semver.org/) system.

### Changelog (v2.7.0)
- Device compatiblity check is now off by default, due to  the iPhone 6 and 6+ not being listed in the `supportedDevices` key.
- Added HarpyAlertTypeNone
	- Thanks to [Patrick Debois](https://github.com/jedi4ever) for the idea!
- Added Debug option that prints out logs into the console if enabled
	- Thanks to [Patrick Debois](https://github.com/jedi4ever) for the idea!

### Features
- Cocoapods Support
- Three types of alerts to present to the end-user (see **Screenshots** section)
- Optional delegate and delegate methods (see **Optional Delegate** section)
- Check for Supported Devices (see **Supported Devices Compatibility** section)
- Localized for 15 languages: Basque, Chinese (Simplified), Chinese (Traditional), Danish, Dutch, English, French, German, Italian, Japanese, Korean, Portuguese, Russian, Slovenian, Spanish
	- Optional ability to override an iOS device's default language to force the localization of your choice (see **Force Localization** section)

### Screenshots

- The **left picture** forces the user to update the app.
- The **center picture** gives the user the option to update the app.
- The **right picture** gives the user the option to skip the current update.
- These options are controlled by the `HarpyAlertType` typede that is found in `Harpy.h`.
 
![Forced Update](https://github.com/ArtSabintsev/Harpy/blob/master/samplePictures/picForcedUpdate.png?raw=true "Forced Update") 
![Optional Update](https://github.com/ArtSabintsev/Harpy/blob/master/samplePictures/picOptionalUpdate.png?raw=true "Optional Update")
![Skipped Update](https://github.com/ArtSabintsev/Harpy/blob/master/samplePictures/picSkippedUpdate.png?raw=true "Optional Update")

### Installation Instructions

#### CocoaPods Installation
```
pod 'Harpy'
```

#### Manual Installation

Copy the 'Harpy' folder into your Xcode project. It contains the Harpy.h and Harpy.m files. Also, there's a dependency on [UIDevice+SupportedDevices](https://github.com/ArtSabintsev/UIDevice-SupportedDevices), so you'll need to download that library as well, if you don't use Cocoapods.

### Setup Instructions	
1. Import **Harpy.h** into your AppDelegate or Pre-Compiler Header (.pch)
1. In your `AppDelegate`, set the **appID**, and optionally, you can set the **alertType**.
1. In your `AppDelegate`, call **only one** of the `checkVersion` methods, as all three perform a check on your application's first launch. Use either:
    - `checkVersion` in `application:didFinishLaunchingWithOptions:`
    - `checkVersionDaily` in `applicationDidBecomeActive:`.
    - `checkVersionWeekly` in `applicationDidBecomeActive:`.


``` obj-c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{

	// Present Window before calling Harpy
	[self.window makeKeyAndVisible];
	
	// Set the App ID for your app
	[[Harpy sharedInstance] setAppID:@"<#app_id#>"];
	
	// (Optional) Set the App Name for your app
	[[Harpy sharedInstance] setAppName:@"<#app_name#>"];
	
	/* (Optional) Set the Alert Type for your app 
	 By default, Harpy is configured to use HarpyAlertTypeOption */
	[[Harpy sharedInstance] setAlertType:<#alert_type#>];
	
	/* (Optional) If your application is not availabe in the U.S. App Store, you must specify the two-letter
	 country code for the region in which your applicaiton is available. */
	[[Harpy sharedInstance] setCountryCode:@"<#country_code#>"]; 
	
	/* (Optional) Overides system language to predefined language. 
	 Please use the HarpyLanguage constants defined inHarpy.h. */
	[[Harpy sharedInstance] setForceLanguageLocalization<#HarpyLanguageConstant#>];
	
	// Perform check for new version of your app 
	[[Harpy sharedInstance] checkVersion]; 
}

- (void)applicationDidBecomeActive:(UIApplication *)application
{
	 
	/*
	 Perform daily check for new version of your app
	 Useful if user returns to you app from background after extended period of time
 	 Place in applicationDidBecomeActive:
 	 
 	 Also, performs version check on first launch.
 	*/
	[[Harpy sharedInstance] checkVersionDaily];

	/*
	 Perform weekly check for new version of your app
	 Useful if you user returns to your app from background after extended period of time
	 Place in applicationDidBecomeActive:
	 
	 Also, performs version check on first launch.
	 */
	[[Harpy sharedInstance] checkVersionWeekly];
    
}
```

And you're all set!

### Differentiated Alerts for Patch, Minor, and Major Updates
If you would like to set a different type of alert for patch, minor, and/or major updates, simply add one or all of the following *optional* lines to your setup *before* calling any of the `checkVersion` methods:

``` obj-c
	/* By default, Harpy is configured to use HarpyAlertTypeOption for all version updates */
	[[Harpy sharedInstance] setPatchUpdateAlertType:<#alert_type#>]; 
	[[Harpy sharedInstance] setMinorUpdateAlertType:<#alert_type#>];
	[[Harpy sharedInstance] setMajorUpdateAlertType:<#alert_type#>];
```

### Optional Delegate and Delegate Methods
If you'd like to handle or track the end-user's behavior, four delegate methods have been made available to you:

```	obj-c
	// User presented with update dialog
	- (void)harpyDidShowUpdateDialog;
	
	// User did click on button that launched App Store.app
	- (void)harpyUserDidLaunchAppStore;
	
	// User did click on button that skips version update
	- (void)harpyUserDidSkipVersion;
	
	// User did click on button that cancels update dialog
	- (void)harpyUserDidCancel;
```

### Force Localization
There are some situations where a developer may want to the update dialog to *always* appear in a certain language, irrespective of the devices/system default language (e.g. apps released in a specific country). As of v2.5.0, this feature has been added to Harpy (see [Issue #41](https://github.com/ArtSabintsev/Harpy/issues/41)). Please set the `forceLanguageLocalization` property using the `HarpyLanugage` string constants defined in `Harpy.h` if you would like override the system's default lanuage for the Harpy alert dialogs.

``` obj-c 
[[Harpy sharedInstance] setForceLanguageLocalization<#HarpyLanguageConstant#>];
```

### Supported Devices Compatibility
Every new release of iOS deprecates support for one or more older device models. Harpy checks to make sure that a user's current device supports the new version of your app. If it it does, the `UIAlertView` pops up as usual. If it does not, no alert is shown. This extra check was added into Harpy after a [lengthy discussion](https://github.com/ArtSabintsev/Harpy/issues/35). A new helper utility, [UIDevice+SupportedDevices](https://github.com/ArtSabintsev/UIDevice-SupportedDevices), came out of this discussion and is included with Harpy.

As of **v2.7.0**, this check is turned off by default, as Apple has yet to provide values for the iPhone 6 and iPhone 6+ in the supportedDevices key in the JSON response from the iTunes store. To turn it on, please add the following line of code before calling any of the `checkVersion` methods:

```obj-c

```

### Important Note on App Store Submissions
The App Store reviewer will **not** see the alert. 

### Created and maintained by
[Arthur Ariel Sabintsev](http://www.sabintsev.com/) 
