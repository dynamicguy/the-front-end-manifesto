Mobile on Rails
===============

In Chapter 1 we learned about [foundation markup][Chapter 1], and in Chapter 2, [foundation styles][Chapter 2], but we never addressed mobile. What about smartphones and tablets? What about mobile browsers!! In the old days it never really was an issue for most developers. All you really had to worry about "cross anything" were browser issues. Desktop screen sizes were debated then, but it could be overlooked since the differences were not so great or prevalent, and the "fix" came relatively quickly: the industry settled on standard design widths and/or employed liquid layouts.

Fast-forward to today and as a front end engineer you cannot help but think about screen size, and the fix is a bit more difficult than it was back then to say the least!

> Mobile browsing is rapidly consuming the Internet. The smartphone and tablet lifestyle is replacing laptops and desktop computers as the primary way we go online.

\- [Mobile Browsing: It Will Get Better and Worse][Better and Worse] by Chris Kelly of New Relic

The fact of the matter is, your end-users are going to consume your work on smartphones or some kind of tablet. I think this is pretty much [common knowledge][] now, so let's deal with it proactively by laying down a mobile foundation. In this chapter we are going to explore the different ways in which we can serve content tailored to the different devices our users are using, and in this process develop our own mobile foundation's best practices.

Mobile First
------------

Once upon a time ago when I worked for Fidelity Investments' FEB Design unit in Boston, we took an existing desktop application and turned it into a mobile app (pre-smartphone era). The result was a precise definition of the applications basic information architecture, no more no less. Several of my colleagues pointed this out to me, and used the mobile application to better architect the greater desktop application.

Luke Wroblewski in his 2009 article "[Mobile First][LukeW]" echoes this exact same sentiment:

> Mobile devices require software development teams to focus on only the most important data and actions in an application. There simply isn't room in a 320 by 480 pixel screen for extraneous, unnecessary elements. You have to prioritize.

As an Information Architect with this experience, the [Mobile First][] design paradigm makes sense to me for information architecting alone, but given the large and increasing number of mobile users out there, it is an approach that you as a front end developer should consider adopting when building any application. In the very least, DO NOT leave mobile design as an afterthought.

Throughout the rest of this book I will take the Mobile First approach.

> Provide for the mobile experience as a forethought.

\- [Manifesto][]

Plan of Attack
--------------

When I asked [LaunchWare][] founder Dan Pickett what he thought was the best approach for mobile, his answer was so succinct (and backed with experience) that it deserves quoting in its entirety:

> We've generally found, UI/UX wise, though that the paradigm is so different that it generally requires a different set of controllers and views.
>
> So I see three or four different levels:
>
> - A responsive design for content oriented / non-workflow based sites (pure HTML/CSS/JS solution)
> - A set of different mobile vs. traditional web views sharing the same controllers (use only when the workflow experience is the same on both paradigms, but still have request specs exercising both mediums)
> - A set of different controllers and views optimized for each experience, with appropriate request specs for each paradigm. (we've suggested this approach in most apps - shared business logic, different views and controllers)
> - A separate, mobile web application that talks to the main application client side via a service oriented architecture (this is usually the last progression before going native, and we only go this route if the mobile app is vastly different from the web application)
>
> The later three allow for a phonegap or a similar solution so that the app can be listed in the app stores and can take advantage of some of the native functions of the device.

I think I would enjoy writing a book called "Mobile on Rails" after exploring the avenues he presents, but for this chapter (of this book) let's narrow down our plan of attack to the following:

1.  Detect user agents to serve mobile versions of our website.

2.  Detect screen size via media queries to serve up responsive web design.

3.  A hybrid of 1 and 2.

Personally I like to separate concerns and tend to use option three, but include responsive design within this separation since there is no one size fits all in mobile. I'm not alone in this thinking:

> Now, oftentimes, people think about device detection as a "one or the other" sort of thing. Either you’re doing responsive design or you’re using device detection to route people to separate templates, and that you would choose one of those two options; you wouldn’t build something that uses both. But we’ve actually combined responsive design with server-side detection quite a bit.

\- [Jason Grigsby – Mobile-First Responsive Design][Jason Grigsby] Podcast by Sean Carmichael

Like Jason Grigsby, I don't think an all or nothing approach really makes sense as a rule of thumb. Let the situation dictate the solution. Here are some thoughts on option 1 versus option 2:

- Keep them separate and you can serve lighter, device specific stylesheets, JavaScript and images. This translates to less complexity and better performance.
- As applications grow and user needs change, it might become necessary to separate mobile out completely. If you start out separate this transition might be easier.
- Many argue that if you keep styles together, you won't forget to work on one while working on the other.
- Designing for a mobile, tablet and desktop versionProcess of your website can be time-consuming, responsive design can accommodate all three.
- You may mis-categorize or entirely miss mobile devices using user agent strings and device databases.

The article [Reasons for Responsive Design][Responsive Reasons] give you some good reasons why you should consider using it. The article "[CSS MediaQuery for Mobile is Fool’s Gold][Fools Gold]" gives an opposite perspective and does a good job of illustrating why media queries might not be the silver bullet for serving up mobile styles and content. There is one quote in particular that the author uses that resonates with me:

> Create a product, don’t re-imagine one for small screens. Great mobile products are created, never ported.

\- [Mobile Design and Development ][Brian Fling] by Brian Fling

On the other hand, Jiří Stránský -- the author of the Mobvious gem we implement later in this chapter -- commented to me:

> I think responsive web design is going to be preferred over server-based solutions for the vast majority of use cases in the future, for two main reasons:
>
> 1. Boundaries within device types will blend. It used to be PCs and phones, now it's tablets, TVs, e-book readers and more. It's hard to detect them server-side and for some of them, even if you know what device it is, you're still not sure what category they should belong to :) Hence the term "phablets" :)
>
> 2. Bandwidth is less of an issue, year by year. It will be best to send the whole thing to the client, then detect client's precise capabilities and render what you want, how you want.

Whatever approach you decide, keep in mind that there are a spectrum of user and business needs. Responsive may in fact be the silver bullet for some sites on that spectrum, and for others not. For example my personal website is pretty simple so maybe detecting user agents to serve a mobile version of the site is not the best use of my time. In this case why not use Responsive Web Design and keep things together? But more complex applications may need to serve up specific markup and styles, take for example Basecamp mobile:

> Only using responsive design for Basecamp mobile would have been like fitting a Prius body to a Hummer... under-the-hood it would have been all wrong.

\- [Behind the speed: Basecamp mobile][Basecamp Mobile]

In this chapter we will explore all three plans of attack.

I. User Agent Sniffing
----------------------

When web-enabled mobile phones first appeared, front end engineers we were faced with many challenges:

- Mobile browsers were relatively unsophisticated and operating system/device bound, the browser market was highly fragmented, and browsers rendered and/or supported languages inconsistently.
- Parsing issues due to different protocol (WAP versus HTTP) markup languages: Wireless Markup Language (WML), XHTML Mobile Profile / XHTML Basic, HTML
- Bandwidth, latency, and CPU issues were significantly worse than what we experience today.
- Screen sizes were much more varied or unstandardized, and smaller than today.
- Carrier "walled gardens" oftentimes did not allow CSS, or JavaScript, or HTML tables, or any combination of the three.
- Proxy servers might strip your site of images, flash (hugely popular back then), multimedia, or enforce throughput limitations.
- Only a basic set of media types were available and they were implemented inconsistently across browsers.
- A highly fragmented device market made compatibility testing extremely difficult.

At Fidelity, under these challenges, our goal was to cover 99.999% of all web-enabled mobile phones that our customers used. To accomplish this we developed a super dumbed down HTML 1.0 template that would render correctly on 90% of customer devices, and for the remaining edge cases serve an alternative template. We were able to do this by analyzing device HTTP_ACCEPT and HTTP_USER_AGENT headers, i.e. user agent sniffing.

> The User-Agent request-header field contains information about the user agent originating the request. This is for statistical purposes, the tracing of protocol violations, and automated recognition of user agents for the sake of tailoring responses to avoid particular user agent limitations.

\- [Hypertext Transfer Protocol -- HTTP/1.1, Section 14.43 User-Agent][User-Agent]

Over time Fidelity developed an extensive database of devices their global customer base used which included information about the devices screen size, operating system, browser, and other pertinent information. Armed with this Fidelity could then serve markup depending on the request and the information it contained.

Fast-forward to today, and you can still effectively use device user agents and third-party databases to identify device characteristics and based on this information serve appropriate markup and styles. Doing this in Rails is not too difficult. Take a look at the [Mobile Solutions Roundup][Mobile Roundup] in the Appendix to get an idea of what's available.

### Mobylette

To deliver our mobile views lets experiment with one of the quickest and simplest solutions: Tiago Scolari's [mobylette][] gem with [jQuery Mobile][] for our user interface. Here are the steps you will follow to implement this solution:

*Step 1:* Copy all the [base-mobile][] files from the mobylette folder and place them into their corresponding directories, i.e. stylesheets/mobile files go in stylesheets/mobile in your application.

*Step 2:* Add the following to your application.rb file:

    # Precompile *all* assets, except those that start with underscore per:
    # http://blog.55minutes.com/2012/01/getting-compass-to-work-with-rails-31-and-32/
    config.assets.precompile << /(^[^_\/]|\/[^_])[^\/]*$/

\- [Getting Compass to Work With Rails 3.1 (and 3.2)][Get Compass to Work]

*Step 3:* Add the following gem to your Gemfile and then bundle install:

    gem 'mobylette'

*Step 4:* Add the following to application_controller.rb:

    include Mobylette::RespondToMobileRequests
    mobylette_config do |config|
      config[:skip_xhr_requests] = false
    end

*Step 5:* Add the following to config/environments/production.rb:

    # Precompile additional assets (application.js, application.css, and all non-JS/CSS are already added)
    config.assets.precompile += %w( modernizr-2.6.2.min.js, jquery.mobile-1.2.0.css )

And that's it!

One thing that you may have noticed is that there certainly does seem to be a lot of repetition in our code, two files for almost everything. One of the key arguments for Responsive Web Design is the elimination of duplication. We will investigate this option thoroughly, but before then let's try one more user agent sniffing solution.

NOTE: I like to use [User Agent Switcher][] to test on my desktop. Give it a try.

### Mobvious

For an alternative to Mobylette we will try a gem called [Mobvious][]. It is a rack-based solution, easy to set up, and highly configurable. It is also versatile in on how you detect mobile requests:

1.  User-Agent sniffing
2.  URL pattern matching
3.  Remembering a user's manual choice

In other words it gives us more options, although we will only be experimenting with the first.

#### Set Up

Here are the steps we will follow to configure Mobvious for our needs:

*Step 1:* Copy all the [base-mobile][] files from the mobvious folder and place them into their corresponding directories, i.e. stylesheets/mobile files go in stylesheets/mobile in your application.

*Step 2:* Add the following to your application.rb file:

    # Precompile *all* assets, except those that start with underscore per:
    # http://blog.55minutes.com/2012/01/getting-compass-to-work-with-rails-31-and-32/
    config.assets.precompile << /(^[^_\/]|\/[^_])[^\/]*$/

\- [Getting Compass to Work With Rails 3.1 (and 3.2)][Get Compass to Work]

*Step 3:* Add the following gems to your Gemfile and then bundle install:

    gem 'mobvious'
    gem 'mobvious-rails'

*Step 4:* Add the following to application.rb:

    # Tell your app to use Mobvious::Manager as Rack middleware.
    config.middleware.use Mobvious::Manager

*Step 5:* Add the following includes to application_controller.rb and application_helper.rb:

    include Mobvious::Rails::Controller

    include Mobvious::Rails::Helper

*Step 6:* Create a initializer file (config/initializers/mobvious.rb) and configure it as follows:

    Mobvious.configure do |config|
      config.strategies = [ Mobvious::Strategies::MobileESP.new(:mobile_desktop) ]
    end

Don't forget to restart your application, and wallah! You have a detection strategy that will detect user agents and return a variable of :mobile or :desktop depending on the device type of the request. Tablets as configured here will return :desktop as well, although you can change this.

To test that Mobvious is working you can add the following helper method in application_helper.rb:

    def device_test
        # Drop in controller: @device = request.env['mobvious.device_type']
        no_device = "What!? That's nil bro."
        if @device.nil?
          no_device
        else
          "#{@device}"
        end
    end

#### Usage

Now that we have Mobvious set up, here is how we will use it.

To begin we will add a bit of [Ryan Bates mobile solution][Ryan Beats] to our Mobvious implementation. Part of his solution creates a new mime type (:mobile), and uses a before_filter to test if an incoming request is mobile. If true, the before_filter sets the mime type to :mobile and only files named with the ".mobile.haml" extension are served to mobile requests.

To do this in our application in conjunction with Mobvious we add the following to application_controller.rb:

    class ApplicationController < ActionController::Base
      protect_from_forgery
      include Mobvious::Rails::Controller
      before_filter :prepare_for_mobile


      private

      def prepare_for_mobile
        if request.env['mobvious.device_type'] == :mobile
          request.format = :mobile
        end
      end
    end

...and the following to /config/initializers/mime_types.rb:

    Mime::Type.register_alias "text/html", :mobile

Just like in the Mobylette solution, requests from mobile devices are detected by Mobvious and served the correct markup and styles. Of course we will need to create all the new .html.mobile views, and again like in the Mobylette solution, I'm feeling like there are way too many files in our app/views folder – some ending with ".html.haml" others with ".mobile.haml".

#### Reorganize

To better organize, and reduce clutter and even repetition, let's keep our mobile views separate from our regular views.

Create a new app/views/mobile folder and move all mobile views there. We tell Rails that our mobile views are located here by adding the following to the prepare_for_mobile method in application_controller.rb:

    def prepare_for_mobile
      if request.env['mobvious.device_type'] == :mobile
        request.format = :mobile
        prepend_view_path "app/views/mobile"
      end
    end

Now in addition to our mime type, we have designated a specific view path as the location to organize our mobile views.

With this new folder structure in place there really no longer is a need for a mobile mime type, plus with a common mime type you can use the same partials for both desktop and mobile devices. Through Rails [template inheritance][] your application will default to regular views when mobile views in the new mobile folder are not available – which is especially useful when making an existing app mobile friendly a little bit at a time.

Remove:

- the mime type we defined in mime_types.rb
- request.format = :mobile in application_controller.rb in the preparer_for_mobile method

If you prefer to organize your mobile views outside of the regular app/views path, for example in app/views_mobile, swap the prepend_view_path with:

         prepend_view_path Rails.root + 'app' + 'views_mobile'

II. Responsive Web Design
-------------------------

User agent sniffing is awesome, but what about in projects where it's just not the right approach? Is there something else we can do? The answer is yes, and it's called Responsive Web Design (RWD). Responsive Web Design allows for pages to adapt to different screen sizes in a manner that makes sense for the device the page renders on, whether that is on a mobile device, the desktop, or devices yet to be introduced to the market.

Ethan Marcotte is widely credited for coining the term "Responsive Web Design" in his 2010 A List Apart article, "[Responsive Web Design][RWD]", and in his [book][RWD Book] he goes on to explain:

> So what does it take to create a responsive design? Speaking purely in terms of front-end layout, it takes three core ingredients:
>
> 1. A flexible, grid-based layout,
> 2. Flexible images and media, and
> 3. Media queries, a module from the CSS3 specification.

\- Ethan Marcotte, [Responsive Web Design][RWD Book], p.9

Let's take a look at these three components one by one starting with flexible grids. Before we do I want to first recommend the following resources to keep in your back pocket:

- [This Is Responsive][]
- [Responsive Design][]

### 1. Flexible Grids

So the first component of Responsive Web Design is the "flexible, grid-based layout." What this means is a layout whose [grid][] dimensions change proportionately as a containing element, such as the screen size or viewport, changes. For example, if a pages major containing element width were 960 pixels relative to a typical desktop screen, maybe as the screen real estate decreased on a tablet it would make sense to decrease the width to 768 pixels. In other words, proportionally respond to the context in which the page is drawn.

This can be accomplished through the use of relative measurements in declaring grid element dimensions, margins, and/or padding. Ethan Marcotte recommends using percentages as the relative measurement, and in his book gives the following formula to determine these percentages:

Target ÷ Context = Result

...where target is the element in a layout you wish to apply the relevant measurement to, and context is the containing element in which the target responds to. For example, if the main container in your webpage, call it .container, has a width of 960 pixels, and within .container there are two equally sized containers called .left-side and .right-side:

    %body
      .container
        .left-side
        .right-side

...what would there relative measurement widths be?

The context is .container and since it contains two equally sized grid elements we can easily calculate in our heads that since they must be equal, each will have a width of 480 pixels (half of 960 pixels), but rather than use "width: 480px" in our CSS for both elements, we use "width: 50%". Mathematically, using the formula, this is calculated as follows: 480 ÷ 960 = .5 or 50%.

In our CSS we then write:

    .container
      width: 960px

    .left-side, .right-side
      width: 50%

Now if you were to change the size of .container, .left-side and .right-side would proportionally resize themselves to the new container size. Expand this example out to all grid elements including converting the 960px in .container to a percentage, and you have a responsive grid that will resize itself depending upon the context it renders on.

#### Using Susy

As you can imagine there's quite a lot of math involved, so rather than calculate all the different percentage for your flexible grid, you can use a grid system that does this for you and save time. There is a pretty comprehensive list of [grid systems][] in the appendix, but my preference is to use [Susy][] since it is a plug-in of Compass, which we are already using, and more importantly it is authored by [Eric Meyer][] whom I have a great deal of confidence in.

##### Installation

Before we begin I'm going to assume that you have removed or disabled our previous work with user agent sniffing. If so proceed. Installation is pretty straightforward:

*Step 1:* Add the following to */config/compass.rb*:

    require "susy"

*Step 2:* Add the Susy gem to your *.gemfile* and bundle install:

    # Compass specific gems.
    gem 'compass-rails'
    gem 'oily_png'
    gem 'susy'

*Step 3:* Import Susy into your project (uncomment):

app/assets/stylesheets/application.scss

    /* BASIC STRUCTURE
      ============================================================================ */
    @import "susy";
    @import "desktop/layout";

Don't forget to restart your server, and wallah! You have a powerful responsive grid system ready for implementation in your project.

##### Implementation

Within our application layout we will implement our Susy responsive grid as follows:

*Step 1*: Define the [basic settings][] of our grid in *application.scss*:

    /* DEFINITIONS
      ============================================================================ */
    @import "define";

    /*  Susy Grid
      -----------------------

    $total-columns:     12
    $column-width:      4em
    $gutter-width:      1em
    $grid-padding:      $gutter-width

This tells Susy what the basic characteristics of your flexible grid are. In this case the grid spans 12 columns across, each column has a width of 4em with a gutter and grid padding of 1em.

To calculate the total width of your grid including its padding use the following formula:

($total-columns x $column-width) + (($total-columns - 1) x $gutter-width) + ($grid-padding x 2)

(12 x 4em) + ((12 - 1) x 1em) + (1em x 2) = 61em

*Step 2*: Create an [outer grid-containing element][.container] in *application.html.haml* called .container:

    %body
      .container
        = chromeframe
        %header{:role => "banner"}
          = render :partial => 'shared/logo'
          = render :partial => 'shared/navigation'
        #main{:role => "main"}
          = yield
        = render :partial => 'shared/footer'
      = scripts

*Step 3*: Add the corresponding CSS to *_layout.sass*:

    .container
      +container

+container is a Susy mixin that applies the definitions you defined in Step 1 to the outer grid containing element you created in Step 2.

NOTE: We remove the following styles from the body tag since we are now replacing these properties with Susy:

      margin: 0 auto
      width: 960px

##### Using Susy

Using Susy is also pretty straightforward. Essentially it is a mixin scheme that applies a specified amount of columns to an element, based on the total columns available. If you recall in the previous Flexible Grids section, in our example we used percentages to define our widths:

    .left-side, .right-side
      width: 50%

With Susy instead of using percentages directly, we allow Susy to do the calculations by defining our widths through the Susy +span-columns() mixin:

    .left-side
      +span-columns(6, 12)

    .right-side
      +span-columns(6 omega, 12)

What did we do? Since we set the total number of columns in our Susy grid to 12:

    $total-columns:     12

...for the two elements we are targeting we need only set the total number of columns each element will span of the available 12 columns, i.e. +span-columns(6, 12); in our case half of the available columns for each element. The "omega" in .right-side denotes that it will be the last column in the grid and therefore will not have a gutter (right margin). With this information Susy calculates your percentages and produces the following CSS:

    .container:after {
      clear: both;
      content: "";
      display: table;
    }

    .container {
      margin-left: auto;
      margin-right: auto;
      max-width: 59em;
      padding-left: 1em;
      padding-right: 1em;
    }

    .left-side {
      display: inline;
      float: left;
      margin-right: 1.69492%;
      width: 49.1525%;
    }

    .right-side {
      display: inline;
      float: right;
      margin-right: 0;
      width: 49.1525%;
    }

And there it is in a nutshell! To really learn how to use Susy – it really packs a lot more punch than what I've shown – the best reference can be found at the [source][]. The following tutorials also demonstrate Susy in action:

- [Responsive Grids With Susy][Susy Grids]
- [Off-canvas layout with Susy][Off-canvas]

NOTE: You may have noticed the "max-with: 59em" property in .container. The reason this is here is to constrain the maximum width in the largest of screens, while maintaining flexibility within this limit. Doing so ensures that pages do not completely span the widths of very large screens and thereby reduce readability. This setting can be overridden in Susy.

### 2. Flexible Media

Now that we have a flexible grid, it's time to look at Ethan's second ingredient in RWD; flexible media. Why flexible media? Imagine loading a 600 pixel wide image into a 320px wide screen. It just doesn't make any sense.









### 3. Media Queries

Now that we have a Susy flexible grid, it's time to look at Ethan's second ingredient in RWD; media queries. Our goal is to respond to different screen sizes with content that makes sense for the device being used by our users. For example, maybe in smaller devices it makes sense to use a smaller font size, or a single column layout rather than a three column layout. Media queries will allow us to do this. To understand them and how they will help us, let's take a quick look at their history.

##### Media Types

Media dependent stylesheets were first specified in the HTML4 and CSS2 W3C recommendations, and were designed to allow developers to target different devices through [media types][]: all, braille, embossed, handheld, print, projection, screen, speech, tty, tv. The idea was that through these media types, devices the belonged to a specific type could be targeted using @media or @import rules, or the HTML \<link> element, and served styles that applied to them and not other types. For example:

    %link{:href => "screen.css", :media => "screen", :rel => "stylesheet", :type => "text/css"}
    %link{:href => "print.css", :media => "print", :rel => "stylesheet", :type => "text/css"}

In this example screen.css would be served only to devices that were of the "screen" type, and print.css was served to devices of the "print" type.

The problem with this specification in practice for mobile was inconsistent implementation across mobile browsers, plus the list of media types did not accommodate the wide range of device types, especially in regards to different screen sizes. And that's essentially where media queries come in and save the day.

##### Media Queries to the Rescue

With inconsistent browser implementation and so many screen sizes and new devices coming to market, developers found that using media types alone to serve up styles for specific devices was impractical. On June 19, 2012, although in practice well before this, an official W3C recommendation was released that greatly expanded the capabilities of media types through media queries:

> A media query consists of a media type and zero or more expressions that check for the conditions of particular media features. Among the media features that can be used in media queries are ‘width’, ‘height’, and ‘color’. By using media queries, presentations can be tailored to a specific range of output devices without changing the content itself.

\- [Media Queries - W3C Recommendation 19 June 2012][Media Queries]

Unlike media types, media queries, do not solely rely on just a handful of predefined types. Media queries are much more flexible in that they allow developers to test a media type with a logical expression that evaluates to true or false. For example:

    @media (max-width: 480px) { color: red }

In this case, the media type "All" (implied by shorthand syntax) is matched against a devices user agent. If it evaluates to trueBeen the device is then tested for a maximum width of 480px. If it passes the second test the specified styles, the color red, is applied. If false it is not, and the device continues to use the default font color. A smart phone in portrait and landscape mode would pass this test, a desktop computer would not.

In addition to max-width, media queries allow you to test for a broader range of conditions including minimum widths, heights, screen orientation, [and more][]. Perfect for serving up device sensitive styles and content to a wide range of devices.

#### Target Devices

Now that we have a basic understanding of media queries, the first step you will need to take before writing your own is determine what screen sizes you plan to target, i.e. what devices are your users using? The best source of this information comes from your weblogs. The following references will also help you understand what's out there and what's being used:

- [StatCounter Global Stats][Stats]
- [Tired of Hunting][]
- [2012 Device Map][]
- [A Simple Device Diagram for Responsive Design Planning][Device Diagram]

In general, here are the most common devices and dimensions:

- 320px (iPhone portrait, default)
- 480px (iPhone landscape)
- 768px (iPad portrait)
- 1024px (iPad landscape)
- 1140+ (Desktops)

So what should you use? In the ResponsiveDesign.is website under [Defining Breakpoints][] the section begins...

> Breakpoints are the point a which your sites content will respond to provide the user with the best possible layout to consume the information.
>
> When you first begin to work with Responsive Design you will define your breakpoints at the exact device widths that you are looking to target. Most often these are the smart phone (usually the iPhone at 320px and 480px), the tablet (usually the iPad at 768px and 1024px) and finally anything above 1024px.
>
> WRONG!
>
> I hope I didn't hurt your feelings but seriously, you're approaching this in the wrong way.

Ha! Something to keep in mind. I like this thinking. We should start with content and see how it looks on different screen sizes, then define our breakpoints.

On the other hand I think it's good to start with something and redefine as content dictates. I like the examples the author gives:

- [media queries for common device breakpoints][breakpoints]

Why don't we grab a few and begin.

#### Breakpoints

Based on [StatCounter Global Stats][Stats] for North America over a three month period, it looks like there are five sizes we will need to address. Rather than use device names let's use generic names like: xs (extra small), s (small), m (medium), l (large), and xl (extra large). FYI - The breakpoints we define here will serve as a starting point, we can always change numbers or add/remove sizes (xxs, xxl, etc.) when user needs dictate that we should.

Here is the mapping I'm thinking of:

![][@media Definitions]

Having defined these sizes, and to drill a point home, I want to quote Happy Cog founder Jeffrey Zeldman:

> The most popular size is every size. If you're not thinking in a mobile-first, content-first way, if you're not planning an adaptive or responsive redesign, if you still think we have a standard canvas that ‘everybody’ uses, and if you can't feel the hot breath of mobile singeing the hairs on the back of your neck, you don't deserve to be a designer, or a consultant, or whatever these people think they are.

\- [Browser screen resolution stats rile devs][Happy Cog]

So what do our brand-new breakpoints look like?

    @mixin breakpoint($point)
      @if $point == xxs
        @media (max-width: 130px)
          @content

      @else if $point == xs
        @media (min-width: 240px)
          @content

      @else if $point == s
        @media (min-width: 320px)
          @content

      @else if $point == m
        @media (min-width: 768px)
          @content

      @else if $point == l
        @media (min-width: 1200px)
          @content

      @else if $point == xl
        @media (min-width: 1400px)
          @content

      @else if $point == retina
        @media (-webkit-min-device-pixel-ratio: 2), (min-resolution: 192dpi)
          @content

How do you use them? First we'll place them in a sass partial called [_media_queries.sass][], and then import them into our application.scss file:

    /* MIXINS
      ============================================================================ */
    @import "compass";
    @import "media_queries";
    @import "mixins"

Then if you want to specify a particular style for an element, lets say a different font color for super small screens for .some-class, you might write something like this:

    .some-class
      color: blue
      @include breakpoint(xxs)
        color: red

Now for any device with a minimum device screen size less than 130px (as defined by the xxs breakpoint), the font color will be red, and blue for everything else. Pretty straightforward.

There's a whole heck of a lot more you can do with media queries. Here are some good references for you if you're interested in learning more:

- [Media queries][]
- [Responsive Web Design in Sass: Using Media Queries in Sass 3.2][Sass Media Queries]
- [Retina Display Media Query][Retina Media Queries]

#### Em's and Media Queries

You might have noticed that our responsive grid uses em's in it's definitions, and our media queries are using pixels. In fact all over the web in various media query articles you'll see the use of pixels, but this in fact is not the best practice. Em's-based media queries are actually a better idea and here's why.

Let's say a user – when viewing your project on their desktop – increases the base font size of their browser by hitting CTRL + several times (maybe they have trouble seeing at the default font size). Imagine that the content they are reading is split into three columns, which makes sense on a desktop, and for smaller screen sizes changes to a single column based on breakpoints. When the user zooms in,, the pixel-based breakpoints will have no affect on the layout since the pixel widths remain the same, and that three column layout that used to fit harmoniously into a desktop screen no longer does.

The bottom line, it might not look so good. If those same breakpoints had been defined with em's, the layout would have responded to the base font size increase. In other words, a three column layout would have changed to a single column layout even though the screen size remained the same, which might make better sense than serving the desktop version when zoomed in.

That's a lot to digest, and as usual I'm going to point you to some references that will help you understand the concept better:

- [The EMs have it: Proportional Media Queries FTW!][EMs have it]
- [How we learned to leave default font-size alone and embrace the em][Embrace the em]

NOTE: You can convert our pixel-based breakpoints to em's by dividing each breakpoint by 16: assumption being that the default screen size is 16px and therefore 1em = 16px.

#### Susy Breakpoints

One of the great features of Susy is that breakpoints are baked right in. We could use the breakpoints we created above, but why not take advantage of the mathematical capabilities of Susy and also be consistent with our grid system at the same time? Sounds like a good idea, and here's how we will use Susy breakpoints in our project.

First, let's redefine our Susy flexible grid as follows:

    $total-columns:     4;
    $column-width:      4em;
    $gutter-width:      1em;
    $grid-padding:      $gutter-width;

    $break5:            5;
    $break6:            6;
    $break7:            7;
    $break8:            8;
    $break9:            9;
    $break10:           10;
    $break11:           11;
    $break12:           12;

We're starting from a total column size of 4 instead of 12 since we are approaching development from a mobile first perspective. Then I add breakpoints from 5 to 12 column layouts. I do this because I want to test which breakpoints and corresponding grid layouts are best for the devices I'm targeting. To help me identify the breakpoint being utilized I set up and colorcode my breakpoints as follows:

    .container
      +container($total-columns, $break5, $break6, $break7, $break8, $break9, $break10, $break11, $break12)
      +susy-grid-background

      +at-breakpoint($break5)
        +susy-grid-background
        background-color: red

      +at-breakpoint($break6)
        +susy-grid-background
        background-color: blue

      +at-breakpoint($break7)
        +susy-grid-background
        background-color: green

      +at-breakpoint($break8)
        +susy-grid-background
        background-color: yellow

      +at-breakpoint($break9)
        +susy-grid-background
        background-color: aqua

      +at-breakpoint($break10)
        +susy-grid-background
        background-color: brown

      +at-breakpoint($break11)
        +susy-grid-background
        background-color: violet

      +at-breakpoint($break12)
        +susy-grid-background
        background-color: white

This produces the following @media rules:

    @media (min-width: 24em) {
      .container {
        *omitted*
      }
    }
    @media (min-width: 29em) {
      .container {
        *omitted*
      }
    }
    @media (min-width: 34em) {
      .container {
        *omitted*
      }
    }
    @media (min-width: 39em) {
      .container {
        *omitted*
      }
    }
    @media (min-width: 44em) {
      .container {
        *omitted*
      }
    }
    @media (min-width: 49em) {
      .container {
        *omitted*
      }
    }
    @media (min-width: 54em) {
      .container {
        *omitted*
      }
    }
    @media (min-width: 59em) {
      .container {
        *omitted*
      }

As you can see it is very similar to what we created manually except Susy calculates all the breakpoints based on the grid we defined and the following formula:

($total-columns x $column-width) + (($total-columns - 1) x $gutter-width)

(12 x 4em) + ((12 - 1) x 1em) = 59em

...and remember that since we're using a base font size of 16px, if you multiply the em's value by that number you will get the equivalent breakpoints in pixels:

-  2 columns:  9em x 16px = 144px
-  3 columns: 14em x 16px = 224px
-  4 columns: 19em x 16px = 304px
-  5 columns: 24em x 16px = 384px
-  6 columns: 29em x 16px = 464px
-  7 columns: 34em x 16px = 544px
-  8 columns: 39em x 16px = 624px
-  9 columns: 44em x 16px = 704px
- 10 columns: 49em x 16px = 784px
- 11 columns: 54em x 16px = 864px
- 12 columns: 59em x 16px = 944px

After reviewing the grid on several different devices I settle on the following breakpoints:

- 4 columns, 19em x 16px = 304px (Samsung, iPhone)
- 6 columns, 29em x 16px = 464px (Small Tablet)
- 9 columns, 44em x 16px = 704px (iPad)
- 12 columns, 59em x 16px = 944px (Desktop)

I then remove the unneeded breakpoints which leaves the following Susy definition:

    $total-columns:     4;
    $column-width:      4em;
    $gutter-width:      1em;
    $grid-padding:      $gutter-width;

    $break6:            6;
    $break9:            9;
    $break12:           12;

...and in our styles:

    .container
      +container($total-columns, $break6, $break9, $break12)
      +susy-grid-background

      +at-breakpoint($break6)
        +susy-grid-background

      +at-breakpoint($break9)
        +susy-grid-background

      +at-breakpoint($break12)
        +susy-grid-background

Now when you need to apply a particular style to a specific screen size use these breakpoints.

Here is a practical example using the Flexible Grids section example we've been working with. Since our four column base setting is designed for smaller screen sizes, we might want to stack the .left-side and .right-side div's (rather than have them appear cramped side-by-side), but for the remaining three breakpoints we will apply grid widths that will result in a 50-50 side-by-side placement. For illustration purposes let's color .left-side and .right-side red and blue, but only for our two largest screen sizes: ipad and desktops, i.e. $break9 and $break12.

    .left-side
      +span-columns(4 omega, 4)

      +at-breakpoint($break6)
        +span-columns(3, $break6)

      +at-breakpoint($break9)
        +span-columns(4, $break9)
        background-color: red

      +at-breakpoint($break12)
        +span-columns(6, $break12)

    .right-side
      +span-columns(4 omega, 4)

      +at-breakpoint($break6)
        +span-columns(3 omega, $break6)

      +at-breakpoint($break9)
        +span-columns(5 omega, $break9)
        background-color: blue

      +at-breakpoint($break12)
        +span-columns(6 omega, $break12)

What did we do? For the 4 column case we told Susy to span .left-side and .right-side across 4 of the available 4 columns.

    +span-columns(4 omega, 4)

Since they are floated elements, doing so stacks .left-side and .right-side one on top of the other. For the remaining cases we told Susy to float the elements side-by-side equally, except in the case of the 9 column layout. In that case one side is slightly bigger than the other. We can override this if we want to. Finally starting at the 9 column breakpoint, we apply our background color which also trickles down to the 12 column layout.

If you're not too sure how this is all working, in addition to the Susy's own [reference][source] (and the articles I recommend above), the following discussion hits the nail right on the head when it comes to understanding how to use Susy breakpoints:

- [Susy and Media Queries (guide for Muppets)][Muppets]

III. A Hybrid Approach
----------------------

> That’s not to say that mobile websites are inherently flawed, or that there aren’t valid business cases for creating them. But I do think fragmenting our content across different "device-optimized" experiences is a losing proposition, or at least an unsustainable one. As the past few years have shown us, we simply can’t compete with the pace of technology.

\- Ethan Marcotte, [Responsive Web Design][RWD Book], p.96

But what about a hybrid approach? What Ethan says here is true, things are happening so fast it might be difficult to keep up with the speed of change using User Agent Sniffing to deliver different optimized mobile version of a website, plus some devices might not register as mobile and slip through the cracks and receive the desktop version of the website.

On the other hand, a purely responsive web design may deliver too much bloat for smaller devices with lower connection speeds, higher latency, and smaller CPUs. The idea of context sensitivity also comes into play. Perhaps your user needs are different on mobile than they are on a desktop because of the context in which they are viewing and using your application. A hybrid approach allows you to address these needs. On top of that, smaller devices may require a lot more tweaks between device capabilities (from dumb phones to smart phones), and require developers to contend with a greater amount of screen size increments found with smaller devices. All of these factors might be good reason to keep code organized separately, not just in a separate stylesheet or in hidden and conditionally loaded elements, but as a separate system entirely.

If we took this hybrid approach, we might do so by providing responsive web design for larger screens and tablets, and a responsive web design for small screen devices that register as mobile via our user agent detection scheme. The smaller devices then would not receive any of the bloat that may be associated to desktop and tablet versions of our website. We could also use the information available in user agent strings for even greater levels adaptation and tweaking.

For small screen devices that slip through the cracks, our desktop version of the site could provide a small screen responsive design backup for those devices the slip through the cracks.

Will this be too much work? That remains to be discovered, I personally don't think so, but if we should choose to use this type of approach here is how we would do it:

1. Leave the User Agent Sniffing solution in place.

2. Leave the Susy-based Responsive Web Design in place as is.

3. Create a second Susy-based Responsive Web Design specifically for small devices.

### File Structure

To create a second Susy-based responsive web design specifically for small devices we are literally going to take all of the techniques we learned in the previous section, and repeat them for mobile. First let's understand where everything will belong by revisiting the lay of the land, i.e. our file organization:

![][file-structure]

What you see is our project as it is organized up until now. To understand how this relates with a hybrid approach allow me to draw a couple arrows and explain:

![][file-structure-w-lines]

**(1)** There are two main stylesheets:

- **mobile.scss** which organizes and pulls together all the partials under the mobile folder.
- **application.scss** which organizes and pulls together all the partials under the desktop folder.

**mobile.scss** is called through the mobile version of **application.html.haml** (mobile/layouts/application.html.haml) – served only when Mobvious detects that the device is a mobile device.

**application.scss** is served in all other cases.

**(2)** Partials common to both **mobile.scss** and **application.scss**.

**(3)** All mobile related views are organized under the "mobile" folder. Desktop views are organized under the default Rails file structure.

**(4)** When Mobvious detects that a request is coming from a mobile device, Rails will serve mobile views from the mobile folder. When those views are not available in the mobile folder, through template inheritance, Rails will look for the view with the same name in its regular default location. For example, the _logo.html.haml partial is common to both website versions, so there is no reason repeat it.

### Susy-based Mobile RWD

Now that we have reviewed where everything belongs, let's set up the mobile version of our website.

First, import Susy into app/assets/stylesheets/mobile.scss:

    /* BASIC STRUCTURE
      ============================================================================ */
    @import "susy";
    @import "mobile/layout";

In our previous implementation of Susy breakpoints we defined the following in application.scss:

    // Susy Grid

    $total-columns:     4;
    $column-width:      4em;
    $gutter-width:      1em;
    $grid-padding:      $gutter-width;

    $break6:            6;
    $break9:            9;
    $break12:           12;

To start our mobile implementation we will do the same in mobile.scss, but focus only on the first two breakpoints as follows:

    // Susy Grid

    $total-columns:     4;
    $column-width:      4em;
    $gutter-width:      1em;
    $grid-padding:      $gutter-width;

    $break6:            6;

Finally, create the outer grid-containing element in app/views/mobile/layouts/application.html.haml:

    %body
      .container
        = chromeframe
        %header{:role => "banner"}
          = render :partial => 'shared/logo'
          = render :partial => 'shared/navigation'
        #main{:role => "main"}
          = yield
        = render :partial => 'shared/footer'
      = scripts

...and add the corresponding CSS to app/assets/stylesheets/mobile/_layout.sass:

    .container
      +container

That's it! How will this work?

It all starts with the question, "is the request coming from a mobile device?"

If true, mobile.scss along with of the mobile version of application.html.haml will be served.

NOTE: mobile.scss is coded to optimally handle devices with column widths of < 6 columns (464px), and application.html.haml is coded with mobile in mind only. Compare the _head.html.haml files for both and you will see that they are significantly different from one another. One draws from regular [HTML5 Boilerplate][] and the other from [Mobile Boilerplate][].

If false, application.scss along with of the mobile version of application.html.haml will be served.

NOTE: application.scss is designed to take advantage of the greater capabilities of devices with > 9 columns (704px) screen widths.

If a false positive squeaks by, application.scss is coded to handle the < 6 columns cases as well (although > 9 columns assets and elements might be received by the smaller device and therefore the experience is not optimized).

I personally really like this approach. To see how I have organized this in a live application, feel free to clone or poke around https://github.com/maxxiimo/viewthought.

Using JavaScript
----------------

### Media Query Polyfills

Media queries are a cornerstone of responsive web design, however, as a front end developer you need to be aware that many older versions of browsers do not support media queries. To overcome these deficiencies in older browsers there are a couple JavaScript-based solutions that will bridge this gap:

- [css3-mediaqueries-js][]

  > ...a JavaScript library to make IE 5+, Firefox 1+ and Safari 2 transparently parse, test and apply CSS3 Media Queries. Firefox 3.5+, Opera 7+, Safari 3+ and Chrome already offer native support.

- [Respond.js][]

  > A fast & lightweight polyfill for min/max-width CSS3 Media Queries (for IE 6-8, and more)

- [Mediatizr][]

  > This library adds media queries support to browsers that don't support it (like Internet Explorer 5.5-8).

The key for these JavaScript-based fixes is that JavaScript must be available or enabled, but unfortunately this is not always the case. Fortunately for our end-users, we approached development from a mobile first perspective. If media queries or JavaScript are not available, breakpoints will not take affect, and our users will be served our four column layout, which is not a bad fallback since the site will still look great and be readable: Another perk of the mobile first approach!

> Employ progressive enhancements and graceful degradation.

\- [Manifesto][]

### Conditional Loading

The idea behind conditional loading is to use JavaScript to test if there is enough screen real estate to display certain pieces of content, say for example a sidebar or a large image. If there is enough room then load the content, if not, don't.

A great idea, but beyond the scope of this chapter and the section it belongs to: Setting up a Foundation. To help you explore this idea though, here is a list of articles iin publication order that will more than set you on the right path:

- *12/18/10* [Speed Up Your Site with Delayed Content][24 Ways Speed]
- *02/12/11* [Conditional Loading for Responsive Designs][24 Ways Loading]
- *12/02/11* [Clean conditional loading][Clean Loading]
- *12/02/11* [Conditional Loading for Responsive Designs][Conditional Loading]
- *03/28/12* [An Ajax-Include Pattern for Modular Content][Ajax-Include Pattern]
- *04/24/12* [Conditionally loading content][Conditional Content]
- *09/04/12* [Enquire.js – Media Query Callbacks in JavaScript][Callbacks]

JavaScript Libraries:

- [enquire.js][]

  > enquire.js is a lightweight, pure javascript library (with no dependencies) for programmatically responding to media queries.

- [Adapt.js - Adaptive CSS ][Adapt.js]

  > Adapt.js is a lightweight (848 bytes minified) JavaScript file that determines which CSS file to load before the browser renders a page. If the browser tilts or resizes, Adapt.js simply checks its width, and serves only the CSS that is needed, when it is needed.

### Zepto

One of the major issues with mobile browsing is CPU and bandwidth limitations. Zepto is a JavaScript library developed for speed, or it could be said to reduce the type of bloat found in libraries such as jQuery:

> Zepto is a minimalist JavaScript library for modern browsers with a largely jQuery-compatible API. If you use jQuery, you already know how to use Zepto.

\- [Github Zepto][]

To learn more visit:

- [Zepto.js][]
- [The Essentials of Zepto.js][Zepto]

What We've Done
---------------

We began this chapter by briefly describing the state of mobile today and yesterday, how important it is, and in light of this, adopted the [Mobile First][] design and development strategy. We then laid out our plan of attack, the different means by which we might develop a mobile foundation for our application.

Our plan of attack divided this chapter into three major sections for each prospective method:

I. User Agent Sniffing<br>
II. Responsive Web Design<br>
III. A Hybrid Approach

We covered each method in detail and incorporated our work into our foundation code base, but with so many different approaches begs the question, which one do I use? Well, that depends. It depends on the project and it's needs. What is important is that you now have options for a mobile foundation that can handle a variety of needs, depending on which one you choose to employ.

We ended the chapter with a quick overview of JavaScript and mobile: media query polyfills, conditional loading, and ending with a very brief reference to Zepto, a minimalist JavaScript library.

"Mobile on Rails" is the last chapter of "Section I - Setting up a Foundation". The three pillars of this foundation are our [foundation markup][Chapter 1], [foundation styles][Chapter 2], and with this chapter our mobile foundation.

We now have a solid base to build on, and are ready to move onto Section II of this book beginning with Chapter 4, "[Information Architecting][Chapter 4]".


[Chapter 1]:            https://github.com/maxxiimo/the-front-end-manifesto/blob/master/chp1-foundation-markup.md
[Chapter 2]:            https://github.com/maxxiimo/the-front-end-manifesto/blob/master/chp2-foundation-styles.md
[Better and Worse]:     http://insights.wired.com/profiles/blogs/mobile-browsing-will-get-both-better-and-worse#axzz2IFWc81o0
[common knowledge]:     http://www.themobileplaybook.com/en-us/#/introduction
[LukeW]:                http://www.lukew.com/ff/entry.asp?933
[Mobile First]:         http://www.abookapart.com/products/mobile-first
[Manifesto]:            https://github.com/maxxiimo/the-front-end-manifesto/blob/master/the-manifesto.md
[Chapter 4]:            https://github.com/maxxiimo/the-front-end-manifesto/blob/master/chp4-information-architecting.md
[LaunchWare]:           http://launchware.com/
[Jason Grigsby]:        http://www.uie.com/brainsparks/2012/10/12/jason-grigsby-mobile-first-responsive-design/
[Responsive Reasons]:   http://www.mixd.co.uk/blog/technical/reasons-for-responsive-design/
[Fools Gold]:           http://blog.cloudfour.com/css-media-query-for-mobile-is-fools-gold/
[Brian Fling]:          http://shop.oreilly.com/product/9780596155452.do
[Basecamp Mobile]:      http://37signals.com/svn/posts/3269-behind-the-speed-basecamp-mobile
[User-Agent]:           http://tools.ietf.org/html/rfc2616#section-14.43
[Mobile Roundup]:       https://github.com/maxxiimo/the-front-end-manifesto/blob/master/appendix-4.md#mobile-solutions-roundup
[mobylette]:            https://github.com/tscolari/mobylette
[jQuery Mobile]:        http://jquerymobile.com/
[base-mobile]:          https://github.com/maxxiimo/base-mobile
[Get Compass to Work]:  http://blog.55minutes.com/2012/01/getting-compass-to-work-with-rails-31-and-32/
[User Agent Switcher]:  http://chrispederick.com/work/user-agent-switcher/
[Mobvious]:             https://github.com/jistr/mobvious
[Ryan Bates]:           http://railscasts.com/episodes/199-mobile-devices
[template inheritance]: http://railscasts.com/episodes/269-template-inheritance
[RWD]:                  http://www.alistapart.com/articles/responsive-web-design/
[RWD Book]:             http://www.abookapart.com/products/responsive-web-design
[Responsive Design]:    http://alpha.responsivedesign.is/
[This Is Responsive]:   http://bradfrost.github.com/this-is-responsive/index.html
[grid]:                 http://www.subtraction.com/pics/0703/grids_are_good.pdf
[grid systems]:         https://github.com/maxxiimo/the-front-end-manifesto/blob/master/appendix-3.md#grid-systems
[Susy]:                 http://susy.oddbird.net/
[Eric Meyer]:           http://meyerweb.com/
[source]:               http://susy.oddbird.net/guides/getting-started/
[basic settings]:       http://susy.oddbird.net/guides/reference/#ref-basic-settings
[.container]:           http://susy.oddbird.net/guides/reference/#ref-basic-mixins
[Susy Grids]:           http://net.tutsplus.com/tutorials/html-css-techniques/responsive-grids-with-susy/
[Off-canvas]:           http://oddbird.net/2012/11/27/susy-off-canvas/
[media types]:          http://www.w3.org/TR/CSS21/media.html#media-types
[Media Queries]:        http://www.w3.org/TR/2012/REC-css3-mediaqueries-20120619/
[and more]:             http://www.w3.org/TR/css3-mediaqueries/#media0
[Stats]:                http://gs.statcounter.com/
[Tired of Hunting]:     http://www.websitedimensions.com/
[2012 Device Map]:      http://viljamis.com/blog/2012/responsive-workflow/device-map-2012.pdf
[Device Diagram]:       http://www.metaltoad.com/blog/simple-device-diagram-responsive-design-planning
[Defining Breakpoints]: http://alpha.responsivedesign.is/strategy/page-layout/defining-breakpoints
[breakpoints]:          http://alpha.responsivedesign.is/develop/media-queries/media-queries-for-common-device-breakpoints
[Happy Cog]:            http://www.netmagazine.com/news/browser-screen-resolution-stats-rile-devs-121897
[_media_queries.sass]:  https://github.com/maxxiimo/base-css/blob/master/_media_queries.sass
[Media queries]:        http://alpha.responsivedesign.is/develop/media-queries
[Sass Media Queries]:   http://thesassway.com/intermediate/responsive-web-design-in-sass-using-media-queries-in-sass-32
[Retina Media Queries]: http://css-tricks.com/snippets/css/retina-display-media-query/
[EMs have it]:          http://blog.cloudfour.com/the-ems-have-it-proportional-media-queries-ftw/
[Embrace the em]:       http://filamentgroup.com/lab/how_we_learned_to_leave_body_font_size_alone/
[Muppets]:              https://groups.google.com/d/topic/compass-users/oXHAtZE4euI/discussion
[HTML5 Boilerplate]:    http://html5boilerplate.com/
[Mobile Boilerplate]:   http://html5boilerplate.com/html5boilerplate.com/dist/mobile/
[css3-mediaqueries-js]: http://code.google.com/p/css3-mediaqueries-js/
[Respond.js]:           https://github.com/scottjehl/Respond
[Mediatizr]:            https://github.com/pyrsmk/mediatizr
[24 Ways Speed]:        http://24ways.org/2010/speed-up-your-site-with-delayed-content/
[24 Ways Loading]:      http://24ways.org/2011/conditional-loading-for-responsive-designs/
[Clean Loading]:        http://adactio.com/journal/5042/
[Conditional Loading]:  http://adactio.com/articles/5043/
[Ajax-Include Pattern]: http://filamentgroup.com/lab/ajax_includes_modular_content/
[Conditional Content]:  http://adactio.com/journal/5414/
[Callbacks]:            http://css-tricks.com/enquire-js-media-query-callbacks-in-javascript/
[enquire.js]:           http://wicky.nillia.ms/enquire.js/
[Adapt.js]:             http://adapt.960.gs/
[Github Zepto]:         https://github.com/madrobby/zepto
[Zepto.js]:             http://zeptojs.com/
[Zepto]:                http://net.tutsplus.com/tutorials/javascript-ajax/the-essentials-of-zepto-js/

[@media Definitions]:   http://chrismaxwell.com/manifesto/media-queries.gif
[file-structure]:       http://chrismaxwell.com/manifesto/file-structure.gif
[file-structure-w-lines]: http://chrismaxwell.com/manifesto/file-structure-w-lines.gif

