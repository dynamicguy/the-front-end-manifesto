The Front End Manifesto
=======================

by Chris Maxwell

*High-performing, efficient,*  
*Un-bloated,*  
*Modularized and Organized*

**Front End Code**


Personal views and direction on front end coding, from a Ruby on Rails perspective.

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">
  <img alt="Creative Commons License" style="border-width:0" src="http://i.creativecommons.org/l/by-nc-sa/3.0/88x31.png" /></a>
<br />This work is licensed under a 
<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License</a>.


Table of Contents
-----------------

- [Cover][]
- [The Manifesto][]
- [Foundation Markup][]
  -  Groundwork
    - Gemfile
    - .gitignore
    - Deployment
  -  The Application Layout
    - A Framework within a Framework
    - Where Does It Live?
  - The Code
    - Where Do Things Go?
    - Prep the App
  - Our Foundation
  - Organization
    - Partials
    - JavaScript
    - What to Put in \<head>
    - The Title
  - Moving Forward
    - Naming Conventions
    - Using Partials
    - IE 6
  - What We've Done
- [Foundation Styles][]
  - Compass Set Up
  - Stylesheet Set Up
  - Our Foundation
  - Organization
    - Style TOC
    - Resets
  - Moving Forward
    - Modularize Styles
    - Use a Labeling System
    - Naming Conventions
  - Keep it DRY!
    - Consolidate
    - Variables
    - Mixins
  - Preprocessors and Frameworks
    - Sass and Less
    - CSS Frameworks
  - What We've Done
- [Mobile Foundation][]
  - Mobile First
  - Plan of Attack
  - User Agent Sniffing
  - Mobile Solutions Roundup
  - Responsive Web Design
  - Where Do Styles Go?
  - User Agents or Media Queries
- [Information Architecting][]
  - Blocks of Information
  - The Storyline
  - Gathering and Organizing Information
  - Prototyping
  - Look and Feel
    - Inspiration
  - Feedback
  - What We've Done
- [Slicing and Dicing Mockups][]
  - Step 1 - Backgrounds
  - Step 2 - Set Widths
  - Step 3 - Fonts
  - Step 4 - Images
  - Step 5 - Sectioning Content (layout/application.html.haml)
  - Step 6 - Start with %header and %footer
  - Step 8 - Navigation
  - Step 9 - HTML for the Main Content
  - Step 10 - Sprites and CSS3 for Images
- [Accessibility][]
  - Standardize Your Links
  - Use ARIA Roles
- [Optimization][]
  - Gzipping
  - Caching
    - Client-Side
    - Server-Side
  - Image Optimization
  - Compressing and the Asset Pipeline
  - Spriting
  - What We've Done
- [Getting the Fonts Right][]
  - font-family
- [Images][]
  - Organization
  - Choosing an Image Format
  - Spriting
  - Responsive Resizing
  - Retina Displays
- [Forms][]
  - Basic Form Structure
  - Ordered List or Paragraphs?
  - Fieldsets and Legends
  - Extras
  - FormHelpers
  - Fancy/Sassy Buttons
- [Navigation][]
  - Types
  - CSS
  - Context Awareness Patterns
- [Gradients][]
- [Email Coding][]
- [Refactoring][]
- [Tools][]
  -.gemfile
  -.gitignore
- [Search Engine Optimization][]
- [Style Guides][]
- [Good Advice][]
- [JavaScript Libraries][]

[Cover]:                             https://github.com/maxxiimo/the-front-end-manifesto/blob/master/cover.md
[The Manifesto]:                     https://github.com/maxxiimo/the-front-end-manifesto/blob/master/the-manifesto.md
[Foundation Markup]:                 https://github.com/maxxiimo/the-front-end-manifesto/blob/master/foundation-markup.md
[Foundation Styles]:                 https://github.com/maxxiimo/the-front-end-manifesto/blob/master/foundation-styles.md
[Mobile Foundation]:                 https://github.com/maxxiimo/the-front-end-manifesto/blob/master/mobile-foundation.md
[Accessibility]:                     https://github.com/maxxiimo/the-front-end-manifesto/blob/master/accessibility.md
[Optimization]:                      https://github.com/maxxiimo/the-front-end-manifesto/blob/master/optimization.md
[Slicing and Dicing Mockups]:        https://github.com/maxxiimo/the-front-end-manifesto/blob/master/slicing-and-dicing-mockups.md
[Information Architecting]:          https://github.com/maxxiimo/the-front-end-manifesto/blob/master/information-architecting.md
[Getting the Fonts Right]:           https://github.com/maxxiimo/the-front-end-manifesto/blob/master/getting-the-fonts-right.md
[Images]:                            https://github.com/maxxiimo/the-front-end-manifesto/blob/master/images.md
[Forms]:                             https://github.com/maxxiimo/the-front-end-manifesto/blob/master/forms.md
[Navigation]:                        https://github.com/maxxiimo/the-front-end-manifesto/blob/master/navigation.md
[Gradients]:                         https://github.com/maxxiimo/the-front-end-manifesto/blob/master/gradients.md
[Email Coding]:                      https://github.com/maxxiimo/the-front-end-manifesto/blob/master/email-coding.md
[Refactoring]:                       https://github.com/maxxiimo/the-front-end-manifesto/blob/master/refactoring.md
[Tools]:                             https://github.com/maxxiimo/the-front-end-manifesto/blob/master/tools.md
[Search Engine Optimization]:        https://github.com/maxxiimo/the-front-end-manifesto/blob/master/search-engine-optimization.md
[Style Guides]:                      https://github.com/maxxiimo/the-front-end-manifesto/blob/master/style-guides.md
[Good Advice]:                       https://github.com/maxxiimo/the-front-end-manifesto/blob/master/good-advice.md
[JavaScript Libraries]:              https://github.com/maxxiimo/the-front-end-manifesto/blob/master/javascript-libraries.md
