# Inline Styles: Yes or No?

Recently, a team I work with has been porting some existing tools written with JavaScript to a new codebase using ReactJS. We were discussing how we should handle styling, and one member suggested using inline styles.

Another teammate said: "Inline styles? Are you crazy?"

The first one came back, "yeah man, it's the new hotness, haven't you heard?"

This led to a heated discussion about the merits of CSS/LESS/SASS vs. JavaScript-based inline styling. We went back and forth trying to decide, which would be more maintainable? Readable? Performant?

Since most of us didn't really know how inline styles even became a thing, I decided to do a little digging and find out what it's all about.

## Background

You're probably feeling the way I did when this idea came onto my radar. WAT? We all know inline styles are the devil. What is there to even talk about? Here's a little background on how we got to here.

### Setting the Stage: Thinking in Components

My first step was to find out where the idea of using inline-styles in this new way originated. After doing some research, I think I've got a sense.

In the past couple of years, the front-end community has been moving more and more toward a component-based web. With projects like [Polymer](https://www.polymer-project.org/1.0/) and [Component](https://componentjs.com/), as well as built-in component support in [Ember](https://github.com/emberjs) and [React](https://github.com/reactjs), we've been increasingly seeing a webpage as a collection of self-contained atoms that include UI, state, behavior, and styling.

Along with this change in mindset, we've seen the rise of practices to support it. With the rise of React and JSX, we began to put our code right alongside the markup.  The community has come up with plenty of ways to enforce a modular architecture, such as [pods](http://cball.me/organize-your-ember-app-with-pods/), [BEM](http://getbem.com/) and related namespaced CSS conventions, and more recently, CSS modules (more on these in a minute).

### The Pricipitating Event: vjeux's Talk

In November of 2014, Christopher Chedaeu of Facebook, also known as vjeux, gave a talk entitled [React: CSS in JS](https://speakerdeck.com/vjeux/react-css-in-js).

In it, he examined seven problems with writing CSS at scale. Using the task of building a simple button as an example, he walked through some fairly complex approaches to solving the first five of them and leaving that last two unsolved. He then dropped a pretty big bomb, declaring that all seven problems could be solved by using inline styles defined in the local react component.

Although vjeux stated in his slides that his goal was _not_ to convince developers to drop CSS and use JS instead, that's exactly what began to happen. The talk spurred a flurry of activity.

### All The Libraries!

In the wake of the talk, a whole crop of libraries appeared overnight, including [reactcss](https://github.com/casesandberg/reactcss), [react-jss](https://github.com/cssinjs/react-jss), [jsxstyle](https://github.com/smyte/jsxstyle), [react-css-modules](https://github.com/gajus/react-css-modules), and most notably [css-modules](https://github.com/css-modules/css-modules) and [radium](https://github.com/FormidableLabs/radium).

These libraries sought to solve some of the limitations that come with using inline styles, most notably lack of support for pseudo-selectors, media queries, and autoprefixer. They fall into two camps: those that seek to solve the problem by making JS-based styling work better and those that seek to make CSS less horrible by addressing vjeux's seven problems more directly.

In [this post](http://devnacho.com/2016/02/24/what-is-the-best-way-to-style-react-components/), Ignacio Gutierrez suggests that if you look at a graph of GitHub stars, you can see how [redux](http://redux.js.org/) began to beat out its competitors, and that radium and css-modules are now poised to do battle for the same honor.

In case you haven't looked at these two, let me give you a quick overview.

radium is a library that makes it easier to style React components with inline styles. It provides support for pseudo-selectors like `:hover` and `:focus`, media queries, vendor prefixing, and keyframe animations

Css-modules represent a different approach to solving the problems posed by vjeux. In this case, styles are scoped locally (rather than globally) by default. During the styling build step, the css-modules compiler looks in imported stylesheets for selectors and makes them available via a styles object. The keys in the styles object are namespaced with a unique, locally-scoped key (such as `_styles__button_482571023`). The resulting JS-generated HTML and stylesheets are locally scoped by default.

## A Quick Example

At this point, you may still be scratching your head wondering: "Ok, but what does it look like? I don't really want to learn _another_ new library."

Of course, you don't have to use radium any other library to experiment with inline styles in React. It's as easy as declaring a styles object, then leveraging it in your `render()` method:

```javascript

const styles = {
  circle: {
    border-radius: '50%',
    height: '100px',
    width: '100px'
  }
}

class Circle extends Component {

  // some React code

  render() {
    <div style={styles.circle}></div>
  }
}

```


## What Are the Benefits?

I know, I know, I promised to help you decide whether you should use inline styles or not when I titled this article. So let's examine some of the pros and cons of inline styling. I read a lot of blog posts and discussion threads, and asked some friends who have experience with inline styles in production apps. Here are some of the most common arguments I came across.

### Featureset

One of the first things you'll notice about inline styles is that they don't work _quite_ the same as CSS. If you're just using vanilla inline styles as we did in the example above, you won't have access to certain features of CSS. These include pseudoselectors, media queries, the ability to autoprefix vendor names to selectors, and keyframe animations. You can get around these restrictions to some extent, for example by using logic branching in your `render()` method for hover state. Solving these problems is the primary reason radium was written, and based on its popularity, it does a good job of it. So, as long as you don't mind adding a dependency to your project, these aren't insurmountable obstacles.

Outside of these features, there are a couple of other things you lose when you adopt inline styles. You lose the "cascading" nature of CSS, because inline styles have the greatest amount of specificity. Of course, this is kind of the point - we want to make styles local to each component. Additionally, it's difficult to style `html` and `body` tags using inline styles. And existing developer tools weren't built for this use case, so they're a little harder to get around in than they are when you're using CSS.

### Modularity

An important part of the promise made by inline styles is that they make it possible to develop in a component-centered paradigm. In the words of my friend [Hargobind](https://twitter.com/khalsah), they encourage "considering styles as part of your components rather than a separate entity. Each component should be a self-contained atom. So, proponents of inline styles argue they shouldn't overwrite each other's CSS selectors. They also point out that when styles change based on a state or user interaction governed by a component, it's more straightforward to implement that interaction when the state, listener, and style all live in the same file.

On the other hand, detractors say that inline styles aren't necessary to write components with styles. Pedantically, you could argue that the idea violates the [single responsibility principle](https://8thlight.com/blog/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html), and that styles, behavior, and markup should remain separated. Webpack already makes it easy to maintain your CSS/LESS/SASS right alongside your JavaScript files if you're using a pod structure. Also, you can rely on namespacing your classes as you do in BEM, and use the [&- selector](http://jamesknelson.com/why-you-shouldnt-style-with-javascript/) to keep your styles from colliding. Finally, if you do have some shared global styles, it will be tricky to make sweeping changes to all of your components without opening up every single one and making the change manually (although some of the inline styling libraries have ways to mitigate this problem).

### Reuse

When considering adopting any package or paradigm, it's good to consider how reusable your code will be with the inclusion. When it comes to reusability, there a number of arguments on each side. Since React Native always uses a style object, it might make sense to use this approach if you're hoping to share any of your code between web and native apps. Also, the fact that the styles are local to the component makes the component easily shareable across projects.

But there are also a couple of ways in which inline styles actually make things more difficult. For one thing, it will be impossible to use existing CSS from external packages. That is, unless you want to go down the road of mixing CSS and inline styles, which could lead to some headaches when it comes to selector specificity. Secondly, what if you end up moving away from React in the future? In that case, you would have to go through and extract your style rules back out into stylesheets.

### Maintenance

Another important consideration is maintenance. While inline styles make it more difficult to leverage designers with CSS skills, they are also a great way to prevent developers from having to wade through thousands of lines of CSS to find the selector they're looking for. Of course, you can go down the road of mixing CSS and inline styles. But as mentioned above, this can make selector specificity tricky. It's also difficult to track down the source of an applied rule in this case.

### Performance

What about performance? One of the main concerns on my team was whether we would take a big hit on load time if we went down this road. We were picturing it going like this: the DOM loads, the browser requests the JS files from the server, executes the JS, then the styles are applied, one node at a time. To display the page, the browser then has to parse every style tag, rather than applying a single CSS rule. It seems like that could be a pretty long process. [Stéphane Derosiaux](https://twitter.com/ChtefiD) did a simple performance test comparing the two, and it does indeed seem that [inline styles are slower](http://ctheu.com/2015/08/17/react-inline-styles-vs-css-stupid-benchmark/).

However, these ideas and the test are based on a simple comparison of CSS and vanilla React inline styles. As [Dan Abramov](https://twitter.com/dan_abramov) points out, [many “inline styles” libraries work by injecting CSS rule sheets](https://twitter.com/dan_abramov/status/780469858538512389). That means that all the arguments about the browser having to parse every style tag don't apply. That is, if you're using one of those libraries. We should also note that there are a couple of potential performance advantages to the approach.. Unused styles can be removed using [dead code elimination](https://blog.engineyard.com/2016/tree-shaking). Also, since React only applies changes it sees in the diff, there is less repainting to be done when dealing with styles that change in reaction to events.

### Dev Happiness

Finally, I always think it's important to think about developer happiness. We spend a good part of our lives writing code, so it might as well be a pleasurable process, right? Some positive feedback I got from developers I asked about their experience is that it's good for design-related work, since everything's right there, and that it's easy to stay in the flow since you don't have to context switch between JS and CSS files.

On the other hand, some find the syntax of the style values confusing, since some values expect a Number and others expect a string. When working with large components, the style object can become rather large and take up a large part of the file, making it harder to find the code you're trying to find.

## Ok, So Really, Should I Use It Or Not?

Ok, so we've dug through the history of the inline style approach, and looked at some of the pros and cons of using them. But the question remains: should you use them?

Well, it depends.

You should give them a try if your project fits this description: you're pretty sure that you're going to stick with React for a pretty long time, you have a strong need for reusable components, you don't rely on external CSS libraries, you don't care about pseudoselectors, vendor prefixes, or animations (OR you are willing to try an inline style library like radium), and, of course, you like to try new things.

You probably shouldn't use inline styles if you feel that you may move away from React or have components you need to share in non-React projects, you aren't willing to take on a new dependency in the form of an inline-styling library, you have a lot of global styling to maintain, or you're just skeptical by nature.

Whichever way you're leaning, I would recommend taking a look at [css-modules](https://github.com/css-modules/css-modules) as an alternative to inline styles. Both approaches have their merits, and it's worth taking a look at each before you decide to rework your whole styling setup. [Here's a good place to start](https://medium.com/seek-ui-engineering/the-end-of-global-css-90d2a4a06284).

## Conclusion

In this post, we've taken a look at the debate surrounding whether to use inline styles in your JavaScript project. We looked at the history of this idea, showed a quick example of what it looks like, and weighed the pros and cons of using them. I hope that this overview helps you and your team make the best decision for your project, and that it leads to productivity and maintainability, whatever you decide.

In case you want to do some more reading on this subject, I would suggest checking out [the radium launch post](https://formidable.com/blog/2015/03/01/launching-radium/), [this overview of JS styling](http://survivejs.com/react/advanced-techniques/styling-react/), and [these](https://byjoeybaker.com/react-inline-styles), [posts](http://jamesknelson.com/why-you-shouldnt-style-with-javascript/).

Until next time!

P. S. We'd love to hear about your experiences with inline styles, CSS modules, or the decision not to use them at all. Leave us a comment!
