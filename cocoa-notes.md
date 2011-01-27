---
title: Cocoa and iOS Notes
layout: default

---

# Cocoa and iOS Notes

## Gesture recognizers:

After load, add the recognizer:

	UITapGestureRecognizer *recognizer;
	recognizer = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(tapRecognized:)];
	[self.scrollView addGestureRecognizer:recognizer];
	[recognizer release];

For the handler:

	-(void)tapRecognized:(UITapGestureRecognizer *)sender{
		// ...
	}
	
Note that swipe gestures need to be registered separately:

	// right
	recognizer = [[UISwipeGestureRecognizer alloc] initWithTarget:self action:@selector(swipeRecognized:)];
	[recognizer setDirection:(UISwipeGestureRecognizerDirectionRight)];
	
	// left
	recognizer = [[UISwipeGestureRecognizer alloc] initWithTarget:self action:@selector(swipeRecognized:)];
	[recognizer setDirection:(UISwipeGestureRecognizerDirectionLeft)];
	
To detect the direction (or the view touched, etc), the handler can inspect sender:	

	-(void)swipeRecognized:(UISwipeGestureRecognizer *)sender{
		NSLog(@"got swipe:%d", sender.direction);
	}
	
## Using Categories to Extend Stuff	

In `UIView+mycat.h`:

	@interface UIView(mycat)
	-(void)myMethod;
	@end

In `UIView+mycat.m`:

	#import "UIView+mycat.h"

	@implementation UIView(mycat)

	- (void)myMethod {
    .. do some stuff
	}
	
	@end

## AppDelegate shortcut

	#define AppDelegate (YourAppDelegate *)[[UIApplication sharedApplication] delegate]