Foundation Styles
=================

HTML and CSS are the rebar of the World Wide Web. In [Chapter 1][] we laid out our HTML, in this chapter we will build a solid CSS foundation, our foundation styles. To do so we will review some of the tools out there to help us get the job done. For our base styles we will then implement this chapters [starter CSS][], and conclude with an overvie of best practices in stylesheet set up and maintenance.

Lets get started.

Preprocessors
-------------

Probably the two most well-known dynamic stylesheet preprocessors in use today are [Sass][] and [Less][]. Sass is the sister of [Haml][], my preprocessor of choice, and the default preprocessor in Rails 3.X. As a Rails front end developer you are going to find Sass in many projects you work on as a consultant or as a new member of an existing team and project so it would be worth your while to get your head around it if you haven't already.

NOTE: If your new to preprocessors the following articles will give you a great overview on why Sass is great, and more importantly, Sass in Rails:

- [David Walsh on Redesigning with Sass][Redesigning with Sass]
- [An Introduction to Sass in Rails 1][Sass in Rails 1]
- [An Introduction to Sass in Rails 2][Sass in Rails 2]

When using Sass you have a choice in syntax: .sass or .scss. I prefer .sass aand throughout this chapter will use the syntax. Most front end developers will probably prefer the .scss syntax because it looks familiar (it is similar to CSS), and because that is what ships out-of-the-box in Rails, however, the .sass syntax is cleaner/terser (IMHO). To help you decide on the best syntax for you, check out the article: "[Sass vs. SCSS: Which Syntax is Better?][Sass vs. SCSS]."

So what about [Less][]? Well it's the runner-up.

NOTE: When using Sass or Haml the following resources are absolutely indispensable:

- [css2sass][]
- [Html2Haml][]
- [html2haml Gem][]

Compass
-------

So right off the bat I have to say that I love [Compass][]. It's based on Sass, powerful, well-documented, widely used, there are a ton of extensions for it, and it generally makes life easier. What's not to love?

> Compass is an open-source CSS Authoring Framework.

\- [Compass][]

All the [starter CSS][] files we will use in our foundation styles incorporate Compass.

In the next section will take a brief look at CSS Frameworks and Grid Systems, but only a brief look because we are not going to use them. Through Compass you can add all kinds of framework components like:

- [Susy][] or [Zen Grids][]
- [Sassy Buttons][] or [Fancy Buttons][]

...and essentially create your own framework with a unique look and feel. Traditional frameworks on the other hand just give you styles. If you want to break free it takes some tinkering.

When working with Compass the following resource may be useful to you:

- [35 Great Resources for Compass and Sass][35 Great Resources]

CSS Frameworks and Grid Systems
-------------------------------

Although my preference is to use Compass, I don't want to discourage you from adopting a framework in your project. Frameworks can be extremely helpful, especially when starting a new project. They give you a whole boatload of base styles that are instantly accessible through your HTML tags and/or specified class names. They include base font sizes and rhythm, default formats for almost every kind of HTML tag, and by simply dropping in the frameworks class names you can add some great looking styles to your project that are built to work, with very few hitches, across all browsers. On top of that you get prebuilt scripts for commonly used functions like pop-ups, modals and menu systems. The list goes on.

You'll find a roundup of the most well-known [frameworks][Appendix 1] in the Appendices.

Pretty much every framework has a grid systems incorporated into it, but there are also a number of standalone grid systems that might be useful to you. If you're not 100% positive what a grid system is, take a look at:

- [Grids Are Good][]

Why use a grid system? The following quote most succinctly answers this question:

> After a few minutes of griddy thinking, the benefits become clear: designers gain a rational, structured framework for organizing content and users gain well-organized, legible sites.

\- [Fluid Grids by Ethan Marcotte][Fluid Grids]

You'll also find a comprehensive roundup of [grid systems][Appendix 2] in the Appendices for you to review and choose from.

Our Foundation
--------------

It's time to build our foundation styles. Our [stylesheets][starter CSS] are written using Sass, but more important than the actual styles themselves � there are really not that many included � is the way that they are organized.

In my experience, as you move along a Rails project and the project grows in complexity, as different authors contribute, stylesheets can become behemoths, unmanageable, and downright confusing. To avoid this I highly recommend that you start a project with some kind of organizational structure in place from the get-go. The styles we will use in this chapter do exactly this.

Before we begin will need to quickly set up Compass.

### Compass Set Up

1.  If you have not done so already, per [Chapter 1][], add "compass-rails" to your gemfile. For more explicit directions take a look at the [compass-rails][] gem source.

2.  Run...

        $ bundle exec compass init

    Compass will generate a configuration file and stylesheets. Delete the stylesheets. We're going to use our own.

3.  Delete your *application.css* file and create and replace it with a blank *application.scss*.

    IMPORTANT: Use @import to organize styles rather than Sprockets. You can use Sprockets' require syntax, however per the explanation found at of the [compass-rails][] gem source this is not a good idea.

4.  Add the following commented out code to your *config/compass.rb* file generated in step 1:

        # To allow compass to import partials from subdirectories per:
        # http://blog.55minutes.com/2012/01/getting-compass-to-work-with-rails-31-and-32/
        # additional_import_paths = ["app/assets/stylesheets/<name>", "app/assets/stylesheets/<name>"]

        # To compile in the necessary debugging information for FireSass.
        # sass_options = {:debug_info => true}

    If you have problems with subfolders within your assets/stylesheets folder, un-comment the first three lines.

    If you use Firefox I highly recommend using [FireSass][]. It allows you to see exactly where sass partial styles are coming from which is extremely helpful when debugging. Un-comment the last two lines if you plan to use FireSass.

5.  Edit *config/application.rb*:

    Un-comment the following:

        if defined?(Bundler)
          # If you precompile assets before deploying to production, use this line
          Bundler.require(*Rails.groups(:assets => %w(development test)))
          # If you want your assets lazily compiled in production, use this line
          Bundler.require(:default, :assets, Rails.env)

    If you find your system has a problem with sass partial underscores when precompiling, add the following:

        # Precompile *all* assets, except those that start with underscore per:
        # http://blog.55minutes.com/2012/01/getting-compass-to-work-with-rails-31-and-32/
        config.assets.precompile << /(^[^_\/]|\/[^_])[^\/]*$/

That's it! You now have Compass and all that it brings available to you.

Some additional resources for working with Compass include:

- [Getting Compass to Work With Rails 3.1 (and 3.2)][Get Compass to Work]
- [How I Use Compass With Rails 3.1][How I Use Compass]
- [Sass, Compass, and the Rails 3.1 Asset Pipeline][Asset Pipeline]

### Stylesheet Set Up

Now that Compass is set up, let's set up our stylesheets. Clone this books [starter CSS][]:

        git clone git@github.com:maxxiimo/base-css.git

Setting up our CSS is pretty straightforward. Basically, all you have to do is copy into your project the cloned files and subfolders exactly as they are laid out, in their entirety. You will only need to replace the blank *application.scss* file, otherwise you should have no other files in your stylesheets directory, unless you forgot to delete *application.css* when setting up Compass.

Your stylesheet file structure should now look like this:

![][stylesheets]

<br>

Simple as that, and here is what your page will now look with the new stylesheets in place:

![][Basic HTML No Styles]

It's pretty basic, but much better than [before][].

NOTE: You may have noticed that only our base application file uses the .scss syntax, while all other other partials use the .sass syntax. This is perfectly fine and done so because of my own personal preference to use .sass where I can.

NOTE: Why the "desktop" subfolder? To better organize desktop/tablet specific files. In the [Chapter 5][] we will create another subfolder called "mobile". Files outside of these two folders are common to both device types.

[Manifesto]:            https://github.com/maxxiimo/the-front-end-manifesto/blob/master/the-manifesto.md
[Chapter 1]:            https://github.com/maxxiimo/the-front-end-manifesto/blob/master/chp1-foundation-markup.md
[Chapter 5]:            https://github.com/maxxiimo/the-front-end-manifesto/blob/master/chp5-mobile-on-rails.md
[Appendix 1]:           https://github.com/maxxiimo/the-front-end-manifesto/blob/master/appendices.md#appendix-1
[Appendix 2]:           https://github.com/maxxiimo/the-front-end-manifesto/blob/master/appendices.md#appendix-2
[starter CSS]:          https://github.com/maxxiimo/base-css

[Sass]:                 http://sass-lang.com/
[Less]:                 http://lesscss.org/
[Haml]:                 http://haml-lang.com/
[Redesigning with Sass]: http://css-tricks.com/redesigning-with-sass/
[Sass in Rails 1]:      http://rubysource.com/an-introduction-to-sass-in-rails/
[Sass in Rails 2]:      http://rubysource.com/an-introduction-to-sass-in-rails-2/
[Sass vs. SCSS]:        http://thesassway.com/articles/sass-vs-scss-which-syntax-is-better
[css2sass]:             http://css2sass.heroku.com/
[Html2Haml]:            http://html2haml.heroku.com/
[html2haml Gem]:        https://github.com/haml/html2haml

[Compass]:              http://compass-style.org/
[Susy]:                 http://susy.oddbird.net/
[Zen Grids]:            http://zengrids.com/
[Sassy Buttons]:        http://jaredhardy.com/sassy-buttons/
[Fancy Buttons]:        http://brandonmathis.com/projects/fancy-buttons/
[35 Great Resources]:   http://fuelyourcoding.com/35-great-resources-for-compass-and-sass/

[Grids Are Good]:       http://www.subtraction.com/pics/0703/grids_are_good.pdf
[Fluid Grids]:          http://www.alistapart.com/articles/fluidgrids/

[compass-rails]:        https://github.com/Compass/compass-rails
[FireSass]:             https://addons.mozilla.org/en-US/firefox/addon/firesass-for-firebug/
[Get Compass to Work]:  http://blog.55minutes.com/2012/01/getting-compass-to-work-with-rails-31-and-32/
[How I Use Compass]:    http://patrickward.com/code/2011/08/19/how-i-use-compass-with-rails-3-dot-1/
[Asset Pipeline]:       http://www.engineyard.com/blog/2011/sass-compass-and-the-rails-3-1-asset-pipeline/

[application.scss]:     https://github.com/maxxiimo/base-css/blob/master/application.scss
[before]:               https://github.com/maxxiimo/the-front-end-manifesto/blob/master/chp1-foundation-markup.md#end-result

[stylesheets]:          http://chrismaxwell.com/manifesto/chp-2/stylesheets.gif
[Basic HTML No Styles]: http://chrismaxwell.com/manifesto/chp-2/base-html-files-with-styles.jpg