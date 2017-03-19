---
layout: post
title: UIViewController Lifecycle
---

When discussing iOS development, there is no better place to start than UIViewController. VCs (as we call them for short) are the workhorses of an iOS app, every view is backed by one. As a developer, it is essential to understand their lifecycle, so you know where to place appropriate code. Let's review the related methods.

## Loading Views

Before we can discuss lifecycle, we should mention that their are 3 ways to create view objects for your UIViewController : via Storyboard, .xib (pronounced "Nib") or via code. The root view is stored in the view property and it acts primarily as a container for the rest of the view hierarchy in the VC. Note that the View Controller is the sole owner of its views and decides when to create and relinquish them. 

- ### From a Storyboard
To load a view controller from a storyboard, call the ```instantiateViewController(withIdentifier:)``` 

- ### From a Nib File
Create your view controller class programmatically and initialize it using the ```init(nibName:bundle:)``` method

- ### From Code
Specify the views for a view controller using the ```loadView()``` method. In that method, create your view hierarchy programmatically and assign the root view of that hierarchy to the view controller’s view property.

## Lazy Loading

It is important to mention that view controllers load their views lazily. Accessing the view property for the first time loads or creates the view controller’s views. 

## Notification Methods

The following methods are called on the View Controller upon view lifecycle state changes. 
This allows the signals to propogate to any UIViewController subclasses. 

### – loadView:
The view controller calls this method when its view property is requested but is currently nil. In this method the view, which the view controller manages, is either created or loaded. As mentioned above, you can override this method when you want to create your views programmatically, instead of using SToryboard or Nibs.

### - viewWillLoad:
This is fired when the VC begins the process of fetching a UIView for the view property, the lazy load process has begun.
I can't say that I have ever seen this method used in an app, but it is available.

### – viewDidLoad:
This method is called once after the view hierarchy is loaded into memory, before the bounds of the view are defined. It is common to use this method to populate the user interface of the view controller with data before the user sees it. It is also a good place where to start some background activity where you need to have the user interface in place at the end. A common case are network calls that you need to do only once when the screen is loaded. 

### – viewWillAppear:(BOOL)animated
This method is called each time before the view is made visible to the screen, before any animations are configured. The animated BOOL lets you know if the view will appear immediately or after a transition. When this event is fired, the view has bounds, but orientation is not set. Add code here that should be run each time the screen is shown.

### – viewWillLayoutSubviews:
This notifies the VC that we are about to layout its subviews. One example is when orientation is changed. At this point, view bounds are finalized. This method is typically overriden if we want to adjust subviews manually.

### – viewDidLayoutSubviews:
This method is called after the VC's view has adjusted its subviews folowing a change in bounds. Add code here if you want to make change to subviews after they have been set. 

### - viewDidAppear:
This method is called after the view is presented to the screen. This is a good point to perform animations and request data from Core Data or a server.

### – viewWillDisappear:
This method is called before the view is removed from the view hierarchy. The view is still in the hierarchy and any unload animations haven't been configured yet. Add code here to handle timers, hide the keyboard, cancel network requests, revert any changes to the parent UI. Also, this is an ideal place to save state.

### - viewDidDisappear:
This method is called after the VC's view has been removed from the view hierarchy. Use this method to stop listening for notifications or device sensors.

## - deinit:
Before a view controller is removed from memory, it gets deinitialized. You usually override deinit() to clean resources that the view controller has allocated that are not freed by ARC. Keep in mind that just because a VC is no longer visible, doesn't mean that it has been deallocated. Container view controllers such as NavigationController can keep their VCs available in memory. Keep in mind that even though a VC is off screen, if it is still in memory, it still works normally and can receive notifications.

## Resource Notification

### - didReceiveMemoryWarning()
When memory starts to fill up, iOS does not automatically move data from memory to its limited hard disk space. It does, however, issue this warning and YOU (I mean YOU) are responsible for clearing some objects out of memory. Be aware that if the memory of your app goes over a certain threshold, iOS will shutdown your app. Unfortunately, this will look like a crash to the end user. 

## Handling Rotations

### - viewWillTransition(to:with:)
When the interface orientation changes, UIKit calls this method on the window’s root view controller before the size changes are about to be made. The root view controller then notifies its child view controllers, propagating the message throughout the view controller hierarchy. to contains the new CGSize size of the container's view and with contains a UIViewControllerTransitionCoordinator coordinator, an enum that describes the new orientation.

### - willRotate(to:duration:)
Called before rotation.

### - willAnimateRotation(to:duration:)
Called before rotation animation


## Diagram
![lifecycle]({{ site.url }}/Tim-iOS-blog/assets/images/lifecycle.jpg)

## Official Apple Docs
[UIViewController Apple Docs](https://developer.apple.com/reference/uikit/uiviewcontroller)


