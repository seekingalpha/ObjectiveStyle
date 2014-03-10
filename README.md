# ObjectiveStyle - The Seeking Alpha Objective-C Style Guide

## Introduction & Motivation

The objective of this document is to standardize the code across the iOS team. We grouped here coding conventions so everyone can have the reference to follow available at GitHub.

Feel free to suggest pull requests.

## Table of contents

* [Dot Notation](https://github.com/seekingalpha/ObjectiveStyle#dot-notation)
* [When using braces...](https://github.com/seekingalpha/ObjectiveStyle#when-using-braces)
* [Declaring and returning methods](https://github.com/seekingalpha/ObjectiveStyle#declaring-and-returning-methods)
* [The ternary operator](https://github.com/seekingalpha/ObjectiveStyle#the-ternary-operator)
* [Objective-C literals](https://github.com/seekingalpha/ObjectiveStyle#objective-c-literals)
* [Commenting](https://github.com/seekingalpha/ObjectiveStyle#commenting)
* [Golden path](https://github.com/seekingalpha/ObjectiveStyle#golden-path)
* [CGGeometry](https://github.com/seekingalpha/ObjectiveStyle#cggeometry)
* [Typedef Enum](https://github.com/seekingalpha/ObjectiveStyle#Typedef-Enum)
* [Image naming](https://github.com/seekingalpha/ObjectiveStyle#image-naming)
* [Singleton creation](https://github.com/seekingalpha/ObjectiveStyle#singleton-creation)
* [instancetype](https://github.com/seekingalpha/ObjectiveStyle#instancetype)
* [General considerations](https://github.com/seekingalpha/ObjectiveStyle#general-considerations)
* [Organization and care](https://github.com/seekingalpha/ObjectiveStyle#organization-and-care)
* [Le grans finale](https://github.com/seekingalpha/ObjectiveStyle#le-grand-finale)

## Dot notation

Dot-notation should **always** be used for accessing and mutating properties. Bracket notation **should not be used** for properties, only for methods.

###Do:

	view.backgroundColor = [UIColor orangeColor];
	[UIApplication sharedApplication].delegate;
	NSInteger arrayCount = [myArray count];
	
###Don't:

	[view setBackgroundColor:[UIColor orangeColor]];
	UIApplication.sharedApplication.delegate;
	NSInteger arrayCount = myArray.count;
	
## When using braces...

Conditional bodies always use braces, even when the conditional body could be written without braces - in one or two lines. This must be done (1) [**to prevent errors**](http://programmers.stackexchange.com/questions/16528/single-statement-if-block-braces-or-no/16530#16530) and (2) **to be more scannable, more readable**. It’s better to use braces, by security, than having the need to add braces after we discover a bug.

###Do:

	if (condition) {
	    [self justDoIt];
	}
	
###Don't:

	if (condition)
	    [self justDoIt];

    if (condition) [self justDoIt];

Moreover, conditional braces should **open in the same line**, and **close in a new line**. Also, before an `if` statement, there should be an **empty line**.
    
###Do:

	[self executeAMethod];
	
	if (enterTheIf) {
	    [self doSomething];
	}
	else {
	    [self doSomethingElse];
	}
	
###Don't:

	[self executeAMethod];
	
	if (enterTheIf)
	{
	    [self doSomething];
	} else {
	    [self doSomethingElse];
	}
	
There is no need to compare results to NO or YES in conditionals.
    
###Do:

	if (user.isPro)

	if (![user canDoSomething])	
	
###Don't:

	if (user.isPro == YES)

	if ([user canDoSomething] == NO)
	
However, **method implementations** should open braces in a **new line**.

###Do:

	- (void)doSomethingWithText:(NSString *)text image:(UIImage *)image 
	{
		//do stuff
	}
	
###Don't:

	- (void)doSomethingWithText:(NSString *)text image:(UIImage *)image {
		//do stuff
	}

When methods return an error parameter by reference, the condition should be the returned value of the function, not the error variable. Some of the Apple’s APIs write garbage values to the error parameter even in succesful cases, so switching on the error can cause a false negative, and possibly a crash.

###Do:

	NSError *error;
	
	if (![self trySomethingWithError:&error]) {
	    // Handle error
	}
	
###Don't:

	NSError *error;
	[self trySomethingWithError:&error];
	
	if (error) {
	    // Handle error
	}


## Declaring and returning methods

In methods there should be a space after the scope (-/+ symbol), and a space between the kind of parameter and the ‘*’ character. Method names and parameters should start with non-capital case. Methods should be as descriptive as possible: don’t save characters, they are free to use.

###Do:

	- (void)doSomethingWithText:(NSString *)text image:(UIImage *)image
	
###Don't:

	-(void)DoSomethingWithText:(NSString*)text Image:(UIImage*)image
	
In every method that returns a value, keep an empty line before returning the value.

Keep one line between methods to help clarity. Inside a method, one line should separate functionality

###Do:
	
	-(BOOL)canDoSomething
	{
	    BOOL shouldDoSomething;
	    //calculate what's necessary to get shouldDoSomething and leave an empty line before returning it
	    
	    return shouldDoSomething;
	}
	
## The ternary operator

The ternary operator, _**?**_, should be used only if it increases code clarity. **Never** use it in a nested way, and avoid using it in situations different than assigning a variable/instance (e.g when defining objects for a collection).

###Do:

	result = a > b ? x : y;
	
###Don't:

	int result = a > b ? x == c + d ? c : d : y;
	
	NSArray *myArray = @[object1, object2, a > b ? x : y];
	

## Objective-C literals

When creating instances of NSDictionary, NSArray and NSNumber, prefer [Objective-C Literals](http://www.google.com/url?q=http%3A%2F%2Fblog.bignerdranch.com%2F398-objective-c-literals-part-1%2F&sa=D&sntz=1&usg=AFQjCNFQ4M3SIjWjDHDed0q53aJ9hoMFxQ) instead of foundation methods (and also when accessing objects inside them). For acessing the first object, use the -[NSArray firstObject] method, available in iOS 7+. Please pay attention to the fact that sending nil values into `NSArray` and `NSDictionary` will cause crashes.

###Do:

	NSNumber *theNumber = @613;
	NSArray *theArray = @[@”Abc”, @”Def”, @”Ghi”];
	NSDictionary *theDictionary = @{ @”Name” : @”Natan”, @”Age” : @26 };
	
In case you need a mutable form, you can do it the following way:

###Do:
	NSMutableArray *theMutableArray = [@[@”Abc”, @”Def”, @”Ghi”] mutableCopy];
	
Also, when accessing your `NSArray`'s and `NSDictionary`'s objects, we recommend you to do it this way:

###Do:
	NSString *theString = [theArray firstObject];
	NSString *theString = theArray[2];
	NSString *theString = theDictionary[@”Name”];
	
## Commenting

Comments should be used to:

* Explain why a piece of code is important, 
* Warn of possible errors if something is not performed in a specific way
* Reference where a solution was found from, linking to a blog post or stackoverflow answer.
* Generate documentation from comments written in header classes.

All comments used must be kept up-to-date or deleted. Wrong comments lead to errors.

##Golden path
When coding with conditionals, the left hand margin of the code should be the "golden" or "happy" path. That is, don't nest `if` statements. Multiple return statements are OK.

###Do:
	- (void)myPreferredMethod
	{
		if (!shouldRunMethod) {
			return;
		}
		
		//do stuff
	}
	
###Don't:
	- (void)myPreferredMethod
	{
		if (shouldRunMethod) {
			//do stuff
		}
	}
	
## CGGeometry
When accessing x, y, width or height of a CGRect, always use CGGeometry functions, as recommended by Apple: *“your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics”*. Prefer this methods instead of manipulating CGRect properties - **they can do some work for you**. For more about CGGeometry, check [NSHipster’s post about it](http://www.google.com/url?q=http%3A%2F%2Fnshipster.com%2Fcggeometry%2F&sa=D&sntz=1&usg=AFQjCNGX-KEjXmBVrbxiw20jUyXv2L3UTA).

###Do:
	CGFloat originY = CGRectMinY(self.view.frame);
	CGFloat middleY = CGRectMidY(self.view.frame);
	
###Don't:
	CGFloat originY = self.view.frame.origin.y;
	CGFloat middleY = self.view.frame.origin.y +  self.view.frame.size.heigth/2;

## Typedef Enum

Enumerated types should follow the new type specification, as it has stronger type checking and code completion. 

###Do:
	typedef NS_ENUM(NSInteger, SALContentType) {
	    SALContentTypeArticle,
	    SALContentTypeMarketCurrent,
	};

## Image naming
Images should be named consistently to preserve organization and developer sanity. They should be named as one camel case (without underscores) string with a description of their purpose, followed by the un-prefixed name of the class or property they are customizing (if there is one), followed by a further description of color and/or placement, and finally their state.

###Do:
	ArticleNavigationButtonWhite@2x.png
	ArticleNavigationButtonWhiteSelected@2x.png
	ArticleNavigationButtonBlack@2x.png
	ArticleNavigationButtonBlackSelected@2x.png

## Singleton creation

Singletons should be created using the [**thread safe pattern**](http://www.google.com/url?q=http%3A%2F%2Fstackoverflow.com%2Fquestions%2F5720029%2Fcreate-singleton-using-gcds-dispatch-once-in-objective-c&sa=D&sntz=1&usg=AFQjCNHkLOZ52x1bBmy5SY9-QnXdB2JKww) (our [code snippets repository](https://github.com/seekingalpha/CodeSniper) contains [a code snippet of this](https://github.com/seekingalpha/CodeSniper/blob/master/singleton.m)):

###Do:

	+ (instancetype)sharedInstance
	{
	    static dispatch_once_t once;
	    static id sharedInstance;
	    dispatch_once(&once, ^{
	        sharedInstance = [[self alloc] init];
	    });
	    return sharedInstance;
	}

This will [prevent crashes](http://cocoasamurai.blogspot.co.il/2011/04/singletons-your-doing-them-wrong.html).

## instancetype
Init methods should return instancetype instead of id. This is better for check type in compile time, and also for code autocompletion. To read more about it, [check here](http://www.google.com/url?q=http%3A%2F%2Fnshipster.com%2Finstancetype%2F&sa=D&sntz=1&usg=AFQjCNGPg-LB4R7tQGcAxCHZt-72-cYzUg).

###Do:

	- (instancetype)initWithText:(NSString *)text;
	
###Don't:

	- (id)initWithText:(NSString *)text;
	
## General considerations

Variables should be named as descriptively as possible (and single letter variable names should be avoided except, in `for( )` loops). Asterisks indicating pointers belong with the variable, e.g., `NSString \*text` **not** `NSString\* text` or `NSString \* text`. Property definitions should be used in place of naked instance variables **whenever possible**. Direct instance variable access should be avoided except in initializer methods (init, initWithCoder:, etc…), dealloc methods and within custom setters and getters. For more information on using Accessor Methods in Initializer Methods and dealloc, see [here](https://www.google.com/url?q=https%3A%2F%2Fdeveloper.apple.com%2Flibrary%2Fmac%2Fdocumentation%2FCocoa%2FConceptual%2FMemoryMgmt%2FArticles%2FmmPractical.html%23%2F%2Fapple_ref%2Fdoc%2Fuid%2FTP40004447-SW6&sa=D&sntz=1&usg=AFQjCNHrj-h-GQ709HPjtcdvjx-5XZcU0Q "Use Accessor Methods to Make Memory Management Easier").

###Do:

	@interface SALObject : NSObject
	
	@property (nonatomic) NSString *someText;
	
	@end
	
###Don't:

	@interface SALObject : NSObject {
	    NSString *someText;
	}
	
Underscores should be used **only** when synthesizing a property and calling the backing instance (i.e. _variableName). **As LLVM can synthesize variables automatically, let it do this work for you.**

Names of objects should be as descriptive as possible, and should never abbreviate words. Again, don’t save characters, they are free.

###Do:

	UIButoon *settingsButton;
	
###Don't:

	UIButoon *setBut;
	
A three letter prefix should always be used for class names and constants (only not for Core Data entities). Constants should be camel-case with all words capitalized, and prefixed by the related class name for clarity. Also, constants should be declared as static and not #define unless it’s a macro.

###Do:

	static const NSTimeInterval SALPostViewControllerFadeAnimationDuration = 0.25;
	static NSString * const SALAbouViewControllerCompanyName = @”Seeking Alpha”
	
###Don't:

	static conts NSTimeInterval fadetime = 0.25;
	#define CompanyName @”Seeking Alpha”
	
`dealloc` methods should be placed at the top of the implementation, directly after `@synthesize` and `@dynamic`. `init` should be placed directly below `dealloc`.

If the name of a `BOOL` property is an adjective, the property should omit the “is” prefix, but should specify the conventional name for the get accessor.

	@property (assign, getter=isPro) BOOL pro;

Indentation should be done with cmd+[ and cmd-] so it is done via Xcode (setting on the preferences, and not with tabs.

##Organization and care

Don’t try to write too much code in one line only. In the same way, don’t break your code too much so that you will need 10 lines to do something you could use 5. 

Methods with up to 3, 4 arguments, write in only one line when calling them. If it has more than that, break one line for each argument. It means, if calling a method will take more than one line, you should split it into the number of arguments. Otherwise, keep it in one line.

Use `#pragma mark -` to organize methods in functional groupings. Insert a blank line before and after it for better readability. 

US English should be used and developers should care to write code with correctly spelled english.

#Le grand finale

As a rule of thumb, when you come across with some situation that is not described here, think about the most sensible and wise way to deal with. Or better than that - ask you iOS mates. They will deal with this code another day.

**Thou shall not ignore the rules of this style guide!**