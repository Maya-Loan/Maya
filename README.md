Requirements

MBProgressHUD works on iOS 9.0+. It depends on the following Apple frameworks, which should already be included with most Xcode templates:
MayaLoanSTtest-phl-api.fyinformation.ccMayaLoanEN
Foundation.framework
UIKit.framework
CoreGraphics.framework
You will need the latest developer tools in order to build MBProgressHUD. Old Xcode versions might work, but compatibility will not be explicitly maintained.

Adding MBProgressHUD to your project

CocoaPods

CocoaPods is the recommended way to add MBProgressHUD to your project.

Add a pod entry for MBProgressHUD to your Podfile pod 'MBProgressHUD', '~> 1.2.0'
Install the pod(s) by running pod install.
Include MBProgressHUD wherever you need it with #import "MBProgressHUD.h".
Carthage

Add MBProgressHUD to your Cartfile. e.g., github "jdg/MBProgressHUD" ~> 1.2.0
Run carthage update
Follow the rest of the standard Carthage installation instructions to add MBProgressHUD to your project.
SwiftPM / Accio

Add the following to your Package.swift:
.package(url: "https://github.com/jdg/MBProgressHUD.git", .upToNextMajor(from: "1.2.0")),
Next, add MBProgressHUD to your App targets dependencies like so:
.target(name: "App", dependencies: ["MBProgressHUD"]),
Then open your project in Xcode 11+ (SwiftPM) or run accio update (Accio).
Source files

Alternatively you can directly add the MBProgressHUD.h and MBProgressHUD.m source files to your project.

Download the latest code version or add the repository as a git submodule to your git-tracked project.
Open your project in Xcode, then drag and drop MBProgressHUD.h and MBProgressHUD.m onto your project (use the "Product Navigator view"). Make sure to select Copy items when asked if you extracted the code archive outside of your project.
Include MBProgressHUD wherever you need it with #import "MBProgressHUD.h".
Static library

You can also add MBProgressHUD as a static library to your project or workspace.

Download the latest code version or add the repository as a git submodule to your git-tracked project.
Open your project in Xcode, then drag and drop MBProgressHUD.xcodeproj onto your project or workspace (use the "Product Navigator view").
Select your target and go to the Build phases tab. In the Link Binary With Libraries section select the add button. On the sheet find and add libMBProgressHUD.a. You might also need to add MBProgressHUD to the Target Dependencies list.
Include MBProgressHUD wherever you need it with #import <MBProgressHUD/MBProgressHUD.h>.
Usage

The main guideline you need to follow when dealing with MBProgressHUD while running long-running tasks is keeping the main thread work-free, so the UI can be updated promptly. The recommended way of using MBProgressHUD is therefore to set it up on the main thread and then spinning the task, that you want to perform, off onto a new thread.

[MBProgressHUD showHUDAddedTo:self.view animated:YES];
dispatch_async(dispatch_get_global_queue( DISPATCH_QUEUE_PRIORITY_LOW, 0), ^{
	// Do something...
	dispatch_async(dispatch_get_main_queue(), ^{
		[MBProgressHUD hideHUDForView:self.view animated:YES];
	});
});
You can add the HUD on any view or window. It is however a good idea to avoid adding the HUD to certain UIKit views with complex view hierarchies - like UITableView or UICollectionView. Those can mutate their subviews in unexpected ways and thereby break HUD display.

If you need to configure the HUD you can do this by using the MBProgressHUD reference that showHUDAddedTo:animated: returns.

MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.view animated:YES];
hud.mode = MBProgressHUDModeAnnularDeterminate;
hud.label.text = @"Loading";
[self doSomethingInBackgroundWithProgressCallback:^(float progress) {
	hud.progress = progress;
} completionCallback:^{
	[hud hideAnimated:YES];
}];
You can also use a NSProgress object and MBProgressHUD will update itself when there is progress reported through that object.

MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.view animated:YES];
hud.mode = MBProgressHUDModeAnnularDeterminate;
hud.label.text = @"Loading";
NSProgress *progress = [self doSomethingInBackgroundCompletion:^{
	[hud hideAnimated:YES];
}];
hud.progressObject = progress;
Keep in mind that UI updates, including calls to MBProgressHUD should always be done on the main thread.

If you need to run your long-running task in the main thread, you should perform it with a slight delay, so UIKit will have enough time to update the UI (i.e., draw the HUD) before you block the main thread with your task.

