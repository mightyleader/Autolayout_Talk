#Autolayout:
#Making sure you go to space today[^1]
![fit, 370%](https://dl.dropboxusercontent.com/u/5034400/Autolayout/rocket.jpg)

[^1]:Image credit: [NASA](http://history.nasa.gov/SP-4310/illustrations.htm)

^My name is Rob
I'm going to tell you a story about Auto Layout
& some lessons I learnt from it
But first, why the title?

---

#https://xkcd.com/1133/
##The Saturn V rocket
## "_explained using only the ten hundred words people use the most often_"[^2]

[^2]: Reference of [1,000 most commonly used word](http://splasho.com/upgoer5/phpspellcheck/dictionaries/1000.dicin)

^Who reads XKCD?
1133 is a blueprint drawing of Saturn V
described in the most commonly used 1000 words

---

![fit, 290%](https://dl.dropboxusercontent.com/u/5034400/Autolayout/upgoer.png)

^This is a detail of it... [read]

^So in this story, to go to space,
I had to work with autolayout

^So I started playing with it...

---

##Wow, that looks difficult[^3]
![original, fit, 260%](http://farm4.staticflickr.com/3077/2735548888_9bb109f319.jpg)


[^3]: Image credit: [Flickr](http://www.flickr.com/photos/27386635@N03/2735548888/)

^I’ve skirted round Auto Layout; intrigued by the potential, mildly burned by Interface Builder, a little anxious about the removal of choice in iOS 8.

^Recently I’ve been working on a project, a modular addition to an existing static framework, that required some different layout handling between both orientations and devices. It’s also built for iOS 7 and upwards and felt like an ideal opportunity to revisit Auto Layout

---

#Interface Builder[^6]
![original, fit, 175%](http://upload.wikimedia.org/wikipedia/en/7/7e/InterfaceBuilder.png)

^My starting point was two XIB files, one per device. I think this has become the natural assumption for the starting point for AL. If we can make it work in IB then we can understand enough to then move on to the programmatic version, right?

[^6]: Interface Builder © [Apple Inc.](http://www.apple.com), Image credit: [Wikipedia](http://upload.wikimedia.org/wikipedia/en/7/7e/InterfaceBuilder.png)

---

##"It’s how we started when we were all chimps-around-the-monolith of Project Builder for Mac or Xcode for iOS."[^4]


![original, left, 350%](http://4.bp.blogspot.com/-ZXWpbKhwDlk/UKTx15HuaoI/AAAAAAAALdc/1WW-D1YzoIQ/s1600/2001_apes_monolith.png)

[^4]:Image credit: [Blogspot](http://4.bp.blogspot.com/-ZXWpbKhwDlk/UKTx15HuaoI/AAAAAAAALdc/1WW-D1YzoIQ/s1600/2001_apes_monolith.png)

^It’s how we started when we were all chimps-around-the-monolith of Project Builder for Mac or Xcode for iOS.
I enjoyed trying to work out how little I can actually specify in IB, for the constraints to still work. Generally you can throw in the kitchen sink then pare down to the minimal set.

---

#Space?
![original](https://dl.dropboxusercontent.com/u/5034400/Autolayout/Galaxy.jpg)

^So, Cmd-R :). Things are behaving, I’m fortunate enough to have real test data not lorem ipsum text and it’s looking good.
However...

---

#1. The designs change...[^5]
![original, right](https://dl.dropboxusercontent.com/u/5034400/Autolayout/pony.jpg)

^The existing design is a top-to-bottom stack of items. 
New landscape design puts the image to the left and the buttons and labels to the right.
I don’t have the flexibility. It’s not iOS 8 time yet and there’s no Size Classes to save the day.
I make a second pair of XIBs for the landscape layouts and switch them on rotation 

[^5]:Image credit: [Blogspot](http://4.bp.blogspot.com/-dd2Ug1qpMJ0/UhNV8N349TI/AAAAAAAABSs/dgEkJdYPYJE/s1600/Make+it+a+Pony.jpg)

---

#2. It's a static framework[^9]
![original, fit, right](https://dl.dropboxusercontent.com/u/5034400/Autolayout/framework.png)

[^9]: Interface Builder © [Apple Inc.](http://www.apple.com)

^Then the second thing happens.
I’m building this in a basic project to abstract construction from the integration into the framework. 
I can’t include binary XIB files in the static framework.
I’m going to need to do all this programatically, aren’t I? I’m suddenly quite sleepy… (insert nap here)

---

![original, fit, 240%](https://dl.dropboxusercontent.com/u/5034400/Autolayout/documentation.png)
#Documentation[^8]

[^8]: Image credit: [Tom Gauld](http://myjetpack.tumblr.com/image/100152274895)

^My first port of call was the documentation. 
I was expecting a Tolstoy-esque volume to wade through.
PDF version runs to only 46 pages. 
I recommend you go read it too.

---

#Space!
![original](https://dl.dropboxusercontent.com/u/5034400/Autolayout/Galaxy.jpg)

^Using this I was able to rebuild the entire module to use programmatic auto layout.


---

#[fit]Lessons 

^Sharing the learning.

---

#[fit]Order is important[^*]

1. Initialise every view-based object with a frame of ```CGRectZero```.
1. Add every view to it’s superview, all the way down the proposed hierarchy.
1. use ```NSDictionaryOfVariableBindings()```


[^*]: [(or you may not go to space today)](http://xkcd.com/1133)

^The order you do things matters. A lot. Auto Layout is dependent on a fully realised view hierarchy.

---

#[fit]Out with the old[^**]

```obj-c
UIView *myView = [UIView alloc] initWithFrame:CGRectZero];
[myView setTranslatesAutoresizingMaskIntoConstraints:NO];
```


[^**]: [(or you will not go to space today)](http://xkcd.com/1133)

^You can implement everything correctly for Auto Layout and still not go to space, unless you do one simple thing first.
By default, every object that is a UIView derivative has this set to YES.
IB turns it off for everything.
In code you need to do it per-view.

--- 

#[fit]It's a two-way street[^***]

```obj-c

UIView *infoView = [[UIView alloc] initWithFrame:CGRectZero]; 
[infoView setTranslatesAutoresizingMaskIntoConstraints:NO];
[self.view addSubview:infoView];
NSArray *someConstraints = 
[NSLayoutConstraint constraintsWithVisualFormat:@“|-10-[_infoView]-10-|" options:0 
                                        metrics:nil 
                                          views:@{@“H:_infoView”:infoView}];
                                       
[self.view addConstraints:someConstraints];

```

^you create one or more constraints for a view, you add those constraints to it’s superview.
BUT..
What if the content of the subview is variable and could cause the subview to need more room than the superview provides?


[^***]: [(remember, space should be upwards-only)](http://xkcd.com/1133)

---

#[fit]It's a two-way street[^***]

```obj-c

UIView *infoView = [[UIView alloc] initWithFrame:CGRectZero]; 
[infoView setTranslatesAutoresizingMaskIntoConstraints:NO];
[self.view addSubview:infoView];
NSMutableArray *someConstraints = [NSLayoutConstraint constraintsWithVisualFormat:@“|-10-[_infoView]-10-|" options:0 
                                        metrics:nil 
                                          views:@{@“H:_infoView”:infoView}];

[someconstraints addObjectFromArray:[NSLayoutConstraint constraintsWithVisualFormat:@“V:|-10-[_infoView]" options:0 
                                        metrics:nil 
                                          views:@{@“H:_infoView”:infoView}]];

[someConstraints addObject: [NSLayoutConstraint constraintWithItem:self.view attribute:NSLayoutAttributeBottom
																		relatedBy:NSLayoutRelationEqual
						  													toItem:infoView
																		attribute:NSLayoutAttributeBottom
																		multiplier:1.0
																			constant:0.0]];
                                       
[self.view addConstraints:someConstraints];

```

^Well the good news is that the applicability of constraints between sub- and superview is a two way street.
You’d tie the subviews top, left and right edges to the superview and the *superviews* bottom edge to the subview
This is how you do dynamically sized tableview cells WWDC 216


[^***]: [(remember, space should be upwards-only)](http://xkcd.com/1133)

---
#[fit]Choose your weapon[^†]

##_A constraint references the relationship of one attribute of one view to one attribute of zero or one other views._

1. Visual Format Language
2. Single relation-based constraints

^there are two ways to create constraints
1. array of constraints based on VFL string. 
2 a single constraint, based on relation of one view to another.
VFL can't express relational layout only explicit spacing
Single constraints can fine tune with relational expressions.



[^†]: This presentation does not endorse [space-based weapons](http://www.starwars.com)

---

#[fit]Forests and Trees[^††]
![fit](http://people.cs.uchicago.edu/~amr/122/labs/images/Binary_search_tree.png)

1. Start at the top of the view hierarchy
2. Constrain each subview to the view.
3. Repeat for each subview.

^more readable code to start with the subviews of self.view, define constraints,add them en masse to self.view.
Then for each subview repeat for it’s subviews.

[^††]: Image credit: [University of Chicago](http://people.cs.uchicago.edu/~amr/122/labs/images/Binary_search_tree.png)

---

#[fit]Helter-Skelter

##Handling rotation

```objc
- (void)viewWillTransitionToSize:(CGSize)size 
				withTransitionCoordinator:(id<UIViewControllerTransitionCoordinator>)coordinator {
	if (size.width > size.height) {
		[self.view removeConstraints:self.portraitConstraints];
    	[self.view addConstraints:self.landscapeContraints];
	} else {
		[self.view removeConstraints:self.landscapeContraints];
    	[self.view addConstraints:self.portraitConstraints];
	}
}

```

^to handle different layouts when the size changes,
create two arrays of constraints, one for each ratio
and switch them out when viewWillTransition.. is called
tip: animate the change with blocks-based calls

---

#[fit]Conclusion

##"What's the best launchpad for Auto Layout? IB or Code?"


---

#[fit]Conclusion

###Take the time to learn to how to do it entirely programmatically. Once you do you’ll feel more confident with what’s happening with what IB is doing in the background. [And you will go to space today](http://xkcd.com/1133).

^My answer was that IB is an amazing, timesaving tool once you know what it’s doing for you. 
^To appreciate it, practice doing it in code.
And you will go to space today.

---

#[fit]End
[^11]

![original](http://nssdc.gsfc.nasa.gov/planetary/lunar/images/as11_44_6642.jpg)

[^11]: Image Credit: [NASA](http://nssdc.gsfc.nasa.gov/planetary/lunar/images/as11_44_6642.jpg)

---
#[fit]Rob Stearn
#[fit]@cocoadelica
#[fit]www.cocoadelica.co.uk

