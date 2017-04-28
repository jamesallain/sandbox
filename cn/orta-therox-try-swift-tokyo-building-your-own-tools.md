You want to build your apps using the least amount of code, quickly, with the largest impact. You do this by finding the right abstractions, which takes years of practice. The Artsy mobile team have multiple apps with Swift, but that is not the future of our applications. This talk covers the build up to using Swift, and how that triggered the discussion that lead to our usage of React Native.

****

### Intro (0:00)

My name is [Orta Therox](https://twitter.com/orta), and I work at a company called Artsy. We're a company that aims to make art as accessible as music. We do this by bringing the art world online, through apps and websites. I joined about 5 and a half years ago and founded the Artsy Mobile Team. I've helped dismantle it. I also helped set up Artsy's policies for open source by default. Open source by default gave me a way in which I could contribute and improve the open source I rely on in my spare time. 

I have been working on CocoaPods for the last 5 years. Which is a pretty big project that takes up a large amount of my time. I've also been building something called Danger for the last year. This is a project to automate the tedious parts of code review, you should check it out if you're on a dev team. 

I'm here to talk about how we moved away from Swift, and more specifically, Native Code. We have four apps, two of which are built in Swift, one of which is built in Objective-C, and the main Artsy application, called Eigen. I know it is an odd idea to talk about not writing Swift at a Swift conference, but we do not work in a vacuum. There are many alternative ways to build an application. There's Ruby by Rubymotion. There's C-Sharp via Mono. Web technology via Cordova, or Java via J to Objective C. Each one of these technologies has a trade-off versus writing Objective-C or Swift. 

Before I dig into this topic, let me add a very relevant disclaimer. I think Swift is a great language, just like I think Objective-C is a great language, and I think Rust is a great language, I think Go is a great language, I think Ruby is a great language, Elixir, and JavaScript. They all have a space in which they are great in, and I've touched them all. I'm going to be using the term Swift here as a catchall. It will represent the general tools that you would use when doing native development. That could be x-code, LLVM, LLDB, or UI-kit foundation. 

### Why not Swift? (2:50)

**Why did we even have these discussions in the first place?**

We were finding that our current ways of building applications were not scaling as the team and apps grew. Building anything inside Eigen, the main Artsy app, rarely reused existing native code and progressively took longer to build features. Our apps' build and test times were increasing until eventually we hit the point where it would take two iOS engineers the same amount of time to build a feature as it would take one web engineer. 

We started Eigen in November 2012. And we've been working on it ever since with a team of usually three people, working full time on the project. Eigen is an app where we show almost every resource inside Artsy, and this means that there is a very large amount of custom view controllers that can be browsed. 

We internally describe it as building both the browser and building the webpages. By March 2015, we gave up trying to keep pace with the web. Once we'd hit this disappointing conclusion, we started to think to ourselves, well what can we do to fix this? 

There is nothing intrinsic to iOS application development that should make them slower to create. Over the course of the 2015 winter break, we explored ideas on how we could start writing more reusable code and making it faster to develop new features. We focused a lot on our idea of code reuse. Each new view controller would require considerable new code and effort on one specific aspect of the design. 

We focused on reuse because the changes that we hoped we could make, would make compile cycles much less of an issue for us. In retrospect we truly hadn't come to the conclusion that the symptom was really that native development is just slower than web development, at it's heart. 

### Native Design Patterns (5:20)

So we talked about three native design patterns that could help our problems, one tooling fix that may work, and one really out there idea. 

Our first design pattern that we came up with was building our own component base architecture. We would be doing this using a lot of Swift paradigms that would fit very nicely in a Swifty type app. We explored the idea of building JSON-Powered components. For example, Spotify's Hub, and Hyperslow's spot. We liked this pattern a lot, it had a very distinctive way to describe both the way in which data should be organized, and what the behavior of that should look like. It could also handle live reloading; you change the JSON and it would reflect in the UI. It felt like a very elegant answer. 

We considered building a component structure heavily inspired by React. An example of this is a project called Katana by Bending Spoons. The mobile team at the time were hearing great reports from our web team about the react programming paradigms, and that was making very serious improvements on the developer productivity. 

If building an app was slow because you needed to compile some or all of the application, plus doing the signing process, then perhaps the answer was to have less to compile. We could move to an architecture of a browser and separate projects for different subsections of the app, by making an x-code project for each browse review controller via scripting. This sounded good on paper, but in the long run would be brittle and complex to maintain. 

We had an engineer who wanted to give React Native a shot. It would be an experiment to see what the integration would look like, and what a single view controller would be once rewritten in JavaScript. 

### Exploring Solutions (7:45)

We only had four engineers, and we all had products to ship; we couldn't spend a lot of time making all of our ideas reality. 

Two of us, Ash Furrow who spoke here last time on our testing strategies, explored trying to build better abstractions with Swift, with an entirely new substantial part of our code base. Our Swift code ended up being very specific to the domain, negating any possibility for reuse. Compile cycles were pretty terrible in this. A simultaneous deadline with the web made it harder for them and both the server team to ship, as well as multiple reference implementations at the same time as creating something from scratch. 

Our experiments with React Native were giving really great results. Rewrites of existing view controllers had considerably less code. They were faster to build, and developers were really enjoying the experience. It came down to two options for us. 

- Native code. 
- Swift had a lot going for it. It was consistent with our existing code bases. People were excited about Swift. There's a lot of effort going into improving it every day. It is an evolving language, it's uplifting to see changes come in, and you can influence them. It's the path of least resistance, like it's obvious that Apple would want you to be using Swift right now, and an alternative is an alternative, and you will have the downsides it. 

It is a known-unknown territory for us. In the idea being we know a lot about the Swift ecosystem because we're involved in CocoaPods and we're involved in building our own libraries. It had a few downsides, too. The developer experience with Native Code is comparatively terrible. Compile cycles for our apps were hitting tens of seconds for incremental changes. And when I played with a project called Injection for x-code a year ago, it really put into perspective for me just how slow native development was. 

With Injection we could get sub-second changes, but by moving more of our code to Swift, it would negate a lot of the time gains that we would actually get from using Injection, due to it's lack of runtime support. Swift felt ill-suited for our API driven app. A view control is a representation of an API endpoint, and the amount of code that we needed just to represent the data inside our app, was nontrivial and boilerplate. UI Kit wants to divide the least amount of networking and API abstractions as possible so that you would end up building on top of it. You would end up building a totally custom networking stack per app, as well as custom object modeling or, interface binding. 

### React Native (11:20)

So how did that lay against React Native? 

Adding React Native gave us an additional 593 dependencies. Yes, 593 dependencies. That is effectively making it impossible for you to really understand the dependency tree. It is still a few years old, and only a few large companies are using it and it is extremely fast moving. Until very recently, it had a new release every two weeks. It's extremely pragmatically made, which means the React Native developers really only fix Facebook's problems first. It's at the beginning of a hype cycle. This means, a lot of the things you will hear from people are that it will fix every single problem. It can't, it won't, and it shouldn't. On the upside, though, React is to me, a considerably better abstraction layer for an API driven app. When we mix React with Relay, our client code base became extremely focused on just displaying API results. 

We're still writing Native a year later, one year after using React Native, and you can still fall back to Objective C and Swift whenever you want. More importantly, whenever the thing you're building needs it, we found it very difficult to write blocking code. 

All JavaScript runs on a separate thread; layout and UI changes are eventually propagated back to the main thread, outside of your components the thing you're building control. It had a simpler layout system. No more auto-layout. React Native uses a much simpler and more battle-tested Flex Box model. It's been around since 2008, has multiple implementations, and was built specifically for app-like interfaces by many companies. JavaScript testing is so far ahead of Native testing that it is like comparing a 1950s car to a self-driving car. Same concept, very different levels of execution. We can talk to the creators of these projects; we can help shape their direction. 

This is a stocked difference to the Native development. For example, Radar, the system used internally by Apple, in which external developers have write access only to, for external contributors, Radar is opaque. To me, it feels like a waste of time. I get an issue on the other side, doesn't have to wait for project maintainers, others can respond, and it can get value out of being publicly indexed. 

The mobile team is five developers in a 25 developer team. We now share the same para dimes as the web teams. One developer can and does ship a feature across Native and web. The move to mobile React really did de-silo our mobile team. 

Finally, everything is forkable. We have forked and improved pretty much every major subsystem that we rely on: React Native, Relay, our text editor. This is a key point, we can rely on our tools. We can build and fix every problem we hit. The only blockers are our time and expertise. 

### JavaScript (15:41)

One of the things the Native developers are pretty arrogant about is that JavaScript is a terrible language. They point out some pretty serious flaws in the language, which it definitely has, and dismiss the entire thing as though you are only going to spend your time writing the worst code possible. For a language that was created in 10 days, it has had an additional 22 years to mature and improve. Contemporary JavaScript is a joy to write, especially when you have great tooling to improve your writing. 

We use TypeScript which is a Microsoft variant of JavaScript that adds types around it. It also has an idea called optional typing where you can ignore types when you don't need them and you can use them when you need them. It's very pragmatic. 

We eventually came to the conclusion that for the types of projects that we create, React Native made sense for us. We felt that we had enough internal knowledge to handle deep diving into the key projects we depended on. Meaning, if they became unsupportive, we could keep running them if and until we decided to move on. It doesn't look like we will, but we're thinking of the next five years, not the next five months. We'll always be shipping apps at Artsy. 

### React Native (17:09)

Let's abstract. What is React? 

React is a Facebook project which offers a single directional component model. Data can only float down. That can replace the whole MVC stack in a front-end application. React was built out of a desire to abstract the way web pages view hierarchy, called the Dom, so that they could make changes to all of their views, and then React would handle making the differences between these view states. 

React Native is an implementation of React where instead of having to abstract the web page's Dom, it creates a Native view hierarchy. Note that this does not include view controllers. The MVC model from Apple's cocoa framework doesn't directly map into React Native. You write JavaScript which React Native will interact with to create a view hierarchy. It's not transforming your JavaScript into Native code, but interacting with it. That view hierarchy could then be on anything. React Native is cross-platform, so it could be on a Samsung TV, a Windows phone, an Android phone, or a webpage. 

### Example (18:30)

So what does it look like in practice? We're going to recreate a single component from the Artsy app, then we're going to look at some production components. 

To create this, we're going to need an artist's name and a year. Let's start with a new component, we use objects that are subclasses of `React.component`. You can make them as functions as well. We want it to be a default export, so when somebody else references this file, they get a class definition. We're going to need to import React in order to have a reference to React. In JavaScript, every single symbol inside a file needs a reference. 

Next, we define a `render` function for describing the Dom output of this component. This function says that it will return just a view component. In this case, it would just output a UI view to the UI view hierarchy. 

We can better represent our data by using some labels. Now our component represents well, two labels. Since we're using Flex Box, these two would actually just sit neatly, vertically on top of each other. 

We want to reuse components though, so let's use the Artsy header component, and it would now have a consistence dialed throughout the app. Each component is the equivalent of both a UI view and a UI view controller, where you would write your view controller code in JavaScript, and your view code in JSX, which is a JavaScript version of HTML. The separation of concerns here feels nice; like when you're using story boards or interface builder for a UI, and then using code to make it all work together. 

Finally, we're going to want to handle some data. We're going to use something called **Props**. Props are variables that get set by the parent. This is what I mean by a single directional data flow. Props are immutable pieces of data for a component that are set by it's parent, and when they change, the component is re-rendered. The whole component tree below is then re-rendered too. This may or may not recreate your UI view structure, depending on the re-rendering. You just handle, "with this data, make this component hierarchy."

```javascript

	<ArtistHeader artist={{ name: "Josef Albers", year: "b.1888" }} />

	import React from "react"
	import { View, Label } from "react-native"

	import Header from ".../components/header"

	export default class ArtistHeader extends React.Component {
		render() {
			const artist = this.props.artist
			return (
				<View>
					<Header>{artist.name}</Header>
					<Label>{artist.year}</Label>
				</View>
			);
		}
	}

```

### Relay (21:10)

So where does this artist data actually come from? Somewhere you need to request and handle putting the data into your app. We use **Relay**. I cannot stress how much I love Relay. Relay changes our entire client side programming paradigms. 

So what is Relay? 

Relay is a library that allows a component to describe small chunks of a networking request that it would need in order to fully render. Relay would then look through your component hierarchy and take all of the networking fragments and make a singe API request. 

Back to our original code. We're going to need to remove the export, because that's not what's going to be removed when we're using Relay. We're going to also need to include Relay. This is now effectively a file private class is Swift. This is all of our networking handled for us. 

```javascript

	import React from "react"
	import { View, Label } from "react-native"
	import Relay from "react-relay"
	import Header from ".../components/header"

	class ArtistHeader extends React.Component {
		render() {
			const artist = this.props.artist
			return (
				<View>
					<Header>{artist.name}</Header>
					<Label>{artist.year}</Label>
				</View>
			);
		}
	}

	export default Relay.createContainer(ArtistHeader, { fragments: {
		artist: () => Relay.QL`
			fragment on Artist {
				name
				year
			}
		`,
		}
	})

```

I should explain how this actually works. First, we export a Relay.create container instead of our class definition. Then inside this container, we declare that when you are asking for API information on an artist, please include the name and the year. Relay will then take all these fragments together and create a potential component tree and Relay will combine all of these fragments to get you exactly the data that you asked for. Relay will check the local cache for the data. If it does not exist, then it will make the API call for the uncached results. 

Here's some production code, it shows handling an artist's bio, and let's go through the components. 

```javascript

	class Biography extends React.Component {

		render() {
			const artist = this.props.artist
			if (!artist.blurb && !artist.bio) { return null }

			const bio = this.props.artist.bio.replace('born', 'b.')

			return (
				<View style={{ marginLeft: sideMargin, marginRight: sideMargin }}>
					<Headline style={{ marginBottom: 20 }}>Biography</Headline>
					{ this.blurb(artist) }
					<SerifText style={styles.bio} numberOfLines={0}>{bio}</SerifText>
				</View>
			)
		}
	blurb(artist) {
		return artist.blurb ? <SerifText style={styles.blurb} numberOfLines=
				: null
		}
	}
  
```

First, the entire function returns null, if there's no bio or blip. This means there's nothing to render so that's totally fine. This is the root view of our component tree. It sets the right margins. Then, there's a headline, and this shows the word biography. Under that is our blurb, which comes from a function that is also nullable. Then offscreen we have our bio too. Below that we define our style sheets and our Relay fragments.

The entire view controller is just these, all the way down. Some are a little bit more complex, but not many. You define the pieces of data you want from an API and then you use them in the related component. It's significant difference on the amount of messing around you would have writing it natively with Apple's tooling. 

### Tools (24:18)

Tools are where it all really comes back to. After exploring outside of the native sandbox, we discovered that there were tools better suited for the apps that we were building at Artsy. 

It should come as no surprise that Apple's tooling has a bias towards the kind of apps that they want people to make. Look at the 2016 design awards. The tools that won are Complete Anatomy, Auxie, an audio/video creation app, DJ Pro, Ulysses, a great text editor, Streaks, a habit-based to-do list app, Frame.io, a video reviewing app, and Zova, personal trainer. Only two of these really use the internet. The vast majority are completely atomic apps. The sole that Apple actually have to build on a day-to-day basis. When I say atomic apps, I mean the ones where the majority of the inputs and the outputs exist within the domain of the application. 

For example like, Garageband, Keynote, Notes, Photos, calendar, reminders all do handle syncing data to a server, but that's not the core aspect of the application. These are the kind of apps that Swift and x-code are built for. It's built for building operating systems and apps in which you run them on; as that's what Apple build, and that's what Apple sell. 

The developer team obviously care about the work, and outside developers, but they're building for Apple and then for us. It's how they ended up being one of the most valuable companies in the world. And there's little to no incentive to change that. Those of us who are building apps driven entirely by APIs, end up fighting an uphill battle against these tools. You can obviously do it, but it feels off. We have a new obsessively typed language, like-- High boilerplate abstractions that provide only barebone networking APIs. 

Every year we get new libraries but the core problem of building it easier and quicker to write an app, has never been addressed outside of storybooks. Swift only makes it safer to build an app, and I never needed that. I can totally understand that Apple does. They ship rarely and the value stability. We eventually decided that this was not the path we wanted to continue down, and a year later, our tooling is entirely open, abstractions are better, and we can just focus on our app logic. This is only true for a subset of applications. 

I've stressed multiple times that this is the decision that was right for our team and products. If you're making an API driven app, and you're feeling like the process of going from an API to a screen is wrong, like we did, then perhaps looking outside of x-code is an answer for you. 

### Questions (27:24)

**How difficult is it to build complex animations and transitions and really complex behavior that you can make natively, but just using React Native?**

To be honest I'm not entirely sure that React Native is great for those kind of apps. There's a lot of places where you could move back to Native Tooling to do that right. We have animations in our application but none that you would consider complex. It has never been a problem for us to do this. There are many different libraries that try to abstract the animation tooling across both android and iOS, but in making those abstractions, it's definitely lossy. If I had a beautifully animated application, I probably wouldn't be considering using React Native. You can do it, but it wouldn't feel right.

**Let's say you have one specific functionality area, where it's API driven, and then you have another demanding Native animations. Does React Native lend itself to a kind of hybrid application structure like that?**

The way that we built React Native into our application was, we built a pod that all of our React Native sits inside; we work on it in a completely separate app, and then we integrate it through CocoaPods and effectively, the main application doesn't know the difference between a React Native view controller, a view controller that was created in Objective C or in Swift, so it does very well if you think about it beforehand. It's not the way they necessarily recommend it, but if you're building into a big complex application like we were, then it's very doable, and it feels nice. We treat it as a library. We work inside a different app, so that different app is faster to work in, because it's only focused on building components versus our actual main application which can continue to be focused on being an application.