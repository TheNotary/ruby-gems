# Gem Building
## Professionalizing Your Ruby Code

#### Presentation Style:

- It's a workshop, so do some typing in your project folder
- Build a presentation with reveal.js
  - Write presentation speaking points to be read off of smart phone
  - Present changes to code via resulting files
  - Have the whole tutorial open on a separate tab

#### Speaking Notes/ ToC

###### Section 1 - Gems by Example
  - What's a Gem?
  - Example of a Gem (hub)
  - The value of packaging your code

###### Section 2 - Build Pseudo Complex large_app
  - The Formation of Our Software
  - Rails App, Square 1
  - Add Pseudo complexity, Messy controller
  - Improve via Basic Method Extraction Refactoring
  - OO Refactor

###### Section 3 - Gem Extraction
  - Bundler Usage and Configs
  - The Anatomy of a Gem
    - APP_NAME.gemspec
    - lib/APP_NAME.rb
    - lib/APP_NAME/
    - lib/APP_NAME/version.rb
    - bin/ vs exe/
    - spec/
  - Gem Extraction, Square 1
    - ¿ Where do I paste my code?
    - ¿ What code do we cut from larger_app?
    - checkout g1: Basic Extraction
  - Improve Library Interface
    - How Cumbersome
    - Refactor
    - checkout g2: easier to use
    - ¿ Is it beautiful?
    - Checkout g3: class gets own file

###### Section 4 - Hook it into the Rails App
  - Source Gem Off File System
  - Cut Old Logic from Rails App
  - A Manual Test Indicates it Worked!
  - Who haz thoughts?


# Section 1 - Gems by Example

## What's a Gem?

So if you're new to ruby gems, let's start out with a very basic description.  

  "A gem is a piece of ruby code that has been formatted into an easy to manage package."

So if you've ever used the `apt-get` package manager on debian based linux distros, that's pretty much what gems are, but for code within the ruby ecosystem.  


## Example of a Gem (hub)

Let's go over an example of a very popular gem, Hub.  Hub is a ruby gem that allows you to interact with github from the command line.  

Hub can easily be installed on any machine that has ruby installed by the below command:

```
  $  gem install hub

  Fetching: hub-1.12.4.gem (100%)
```

Go ahead and try it now.  In the command's output, you'll see that it fetched `hub-1.12.4.gem`.  That's telling you the name of the gem and the version installed... Gem defaults to the latest version of any given gem.  If that version is no good, you can tell gem to install a specific version manually.  

```
  $  gem install hub -v 1.12.3
```


Hub is a CLI tool.  Most ruby gems are just libraries, code that you use from within other ruby code, but we're installing a ruby gem that has a "binary file" component.  
Once you install a CLI gem, the binary becomes exposed on the system path.  Typing `which hub` will show you where this "binary" is.  
I'm scare quoting the term binary because if you open that file up, you'll actually see that it's just a ruby script with a shebang.  

Now you have two versions of a CLI gem on your system.  How do you choose between them?  
By default the latest version is used, which is probably ideal, but you can explicitly declare which gem to use by making the first argument an underscoric version identifier.

```
  $ hub _1.12.3_ --version
```

I don't know how this works... it just does.  


## The value of packaging your code

When I first learned to package my code, I became filled me with feelings of empowerment.  
Not only was I able to look at all those gems that other professionals had built, and understand how that was made useable to me...
But it also gave me the power to:  
  - Easily Deploy my code on a foreign server
  - Easily include my code in OTHER ruby projects
  - Easily update my code (after the fact) without breaking dependent codebases
  - Easily share my code with others

  Everything about making ruby gems leads to:
    - Increased developer performance
    - Reduced overhead on compatibility issues
    - And overall lower cost per employee

Everything about making gems leads to really complicated, stressful things suddenly becoming _easy_.  

So let's get started making our lives better by packaging up some code.  



# Section 2 - Build Pseudo Complex large_app

## The Formation of Our Software

Before we get started building a gem, let's suppose that we have some code in an actual large application that needs to be extracted out into a gem.  Better than supposing, let's build a mock large application right now.  And in doing so, we'll add some ruby logic that will later be extracted into a gem.  

> What should that logic be?

Well... **what if** we had to encrypt and decrypt a string from within an important web app?  And we expect to be using this same crypto algo in several other rails apps.  This is the perfect place to use a gem.  

This is a fairly typical workflow for a gem's creation; we do this by first stumbling upon code that exists in a large app and then we decide to 'extract' the functionality into its own gem because late in the game we realized how re-usable this code was, or who atomically complex it has become.  

> Does anyone know how a star is formed?

*Wait to see if it's remotely applicable*

I was hoping that someone could tie in the creation of stars to the creation of gems.  But I realized last night that it's not gems that form like stars, rather rails apps do!  All this stuff, these space particles, mostly hydrogen, just kinda drifted in -- gravitated towards this central, monolithic body.  And after a certain point, things started to get hot.  Things got out of hand!  There was too much friction, too much stuff, and things reached the verge of igniting in a blaze of nuclear fusion -- this is the kind of fire that won't extinguish in our god, damn lifetimes.  Once a star reaches that point, it's completely out of anyone's control.  At this point, this point of no return it becomes a fixture of the universe.  A symbol of life amidst haphazardness, amidst chaos --completely absent of any notions of coercion or management outside of it's own brilliant, blazing body.  

Did you bank 50k this year?  100k?  200k?  Ok... ok... but are you a fixture of the universe...  Are you the reason for the spectrum that we know as the periodic table of elements?  No madams and sirs, we are not of these things.  And try as some might with ascriptions of measurements and commendations, those on that path are nothing more than kidders and self-delusionists with but a loose sense of context.  A more noble question than salary or rank I recommend everyone ask themselves is this: am I creating beauty of some kind?  

Anyway that's what a feature-rich rails application is: A mass of particles on the verge of nuclear detonation.  And maybe every good rails app --MAYBE EVERY GOOD THING should lead up to an explosion.  A really nice one that produces more for the universe than what existed before hand.  Maybe that's what I'm getting at.

With that said, lets begin by first creating a fake rails app that we'll add pseudo complex functionality to.  


## Rails App, Square 1

First, everyone should just clone my repo with the following command:

```
git clone https://github.com/thenotary/large_app
```

Also, checkout the first commit.  

```
  git checkout 84ea25c
```


For the benefit of the class, I'll begin building what you just cloned with the below simple commands....

```
  rails _4.2.6_ new large_app
  cd large_app
  rails g controller pages home
  awk 'NR==1{print; print "  root to: \"pages#home\""} NR!=1' config/routes.rb > tmp.swp && \
    mv tmp.swp config/routes.rb
```

Ok, there we go, all of us now have a perfect rails app with a home page.
Let's add functionality to it.  


## Add Pseudo complexity, Messy controller

* In the controller for pages#home, we'll send some strings of text to the home page view.  
  * We'll present a string in 3 forms on the views:  untouched, encrypted and decrypted.  


1.  So go into the controller to conduct some arbitrary crypto...

(app/controllers/pages_controller.rb)
```
class PagesController < ApplicationController
  def home
    @untouched = "Hello Gems"
    @encrypted = Base64.encode64(@untouched)
    @decrypted = Base64.decode64(@decrypted)
  end
end
```


2.  Then I go into the view and render the work done in the controller...

(app/views/pages/home.html.erb)
```
<h1>Pages#home</h1>
<ul>
  <li><%= @untouched %>
  <li><%= @encrypted %>
  <li><%= @decrypted %>
</ul>
```

3. Now test it... and it works


## Improve via Basic Method Extraction Refactoring

Now's a good time to think about whether our code is really ready to show off to our peers.
_What if_ we wanted to add more commands to take place during encryption to make things more secure?  
Let's pretend for the moment that this crypto code is going to get really messy, really fast.  

From what I can see, there a lot of random logic just floating around in the controller.

> What can I do to clean up my logic?

*Wait for audience to recommend "methods" or "models"*

[Methods|models] great!  Let's do it.  


(app/controllers/pages_controller.rb)
```
class PagesController < ApplicationController
  def home
    @untouched = "Hello Gems"
    @encrypted = encrypt(@untouched)
    @decrypted = decrypt(@encrypted)
  end

  def encrypt(string)
    Base64.encode64(string)
  end

  def decrypt(string)
    Base64.decode64(string)
  end
end
```


So that's better, we have methods.  And we can now call them.  
At this point we should be caught up to the next commit, let me check my work and
fast forward HEAD.  
```
git diff HEAD 1e7254e
git stash
# check out s1: methods used
git checkout 1e7254e
```


I'm so eager to put these new methods into their own file,
but before we do that
let's refactor our code so it's more object oriented.  


## OO Refactor

So it's fastest for everyone to just have a look at this diff on github.  This is the refactor that I'm going to do: https://github.com/TheNotary/large_app/commit/4d8c4dba8223984373dc5d2f2ea4b116f6955e1c

So I added a new class, directly in the PagesController, this is a horrible anti-pattern btw!

(app/controllers/pages_controller.rb)
```
class PagesController < ApplicationController
  def home
    @message = KewlCrypto.new("Hello Gems")
  end

  # Warning: placement here is an anti-pattern
  class KewlCrypto
    attr_reader :untouched

    def initialize(string)
      @untouched = string
    end

    def encrypted
      Base64.encode64(@untouched)
    end

    def decrypted
      Base64.decode64(@untouched)
    end
  end
end
```

Above, I instantiated 1 object in the home action with a single line, rather than using three instance variables to pass in my data (that's great!)

Now check out the view:

(app/views/pages/home.html.erb)
```
<h1>Pages#home</h1>
<ul>
  <li><%= @message.untouched %>
  <li><%= @message.encrypted %>
  <li><%= PagesController::KewlCrypto.new(@message.encrypted).decrypted %>
</ul>
```

There's more lines in the view, but that's because our original display was entirely contrived and our new model likens more closely to something you'd see and work with in the wild.  

At this point, we can fast forward  

###### FAST FORWARD:  s2: starting OO, but putting things in wrong spot
```
git diff HEAD 4d8c4db
git stash; git stash drop
# check out
git checkout 4d8c4db
```

Ok, so now that we have a class right here in the controller...  and it's giving us weird name spacing issues as you can see in the view...  Let's really quickly move that into the models directory where it somewhat belongs and tweak our code.  


(app/models/kewl_crypto.rb)
```
class KewlCrypto
  attr_reader :untouched

  def initialize(string)
    @untouched = string
  end

  def encrypted
    Base64.encode64(@untouched)
  end

  def decrypted
    Base64.decode64(@untouched)
  end
end
```

(app/controllers/pages_controller.rb)
```
class PagesController < ApplicationController
  def home
    @untouched = "Hello Gems"
    @encrypted = encrypt(@untouched)
    @decrypted = decrypt(@encrypted)
  end

  def encrypt(string)
    Base64.encode64(string)
  end

  def decrypt(string)
    Base64.decode64(string)
  end
end
```


(app/views/pages/home.html.erb)
```
<h1>Pages#home</h1>
<ul>
  <li><%= @message.untouched %>
  <li><%= @message.encrypted %>
  <li><%= KewlCrypto.new(@message.encrypted).decrypted %>
</ul>
```



Any questions?  Comments?  What's cool about this new state?

*Wait for view discussion*

We can jump ahead to the next commit now that we're done with our OO refactor...

###### checkout 0ef99a7 s3: put kewl_crypt into its own file
```  
  git checkout 0ef99a7
```

We're now at a point where we have a perfectly fine rails application.  But **what if?** ...what if we want to use our crypto library in other ruby projects?  We'll have to extract it into a gem.  

# Section 3 - Gem Extraction

Note: Tests...

So now it's time to start a new gem.

## Bundler Usage and Configs

It's a particularly under-advertized feature, but bundler, that nearly ubiquitous ruby gem that tends to be installed at the same time as ruby, has the capability of creating a gem skeleton.  This is really handy!  

You can configure some helpful defaults for bundler's gem function in the below file as I've done:

(~/.bundle/config)
```
---
BUNDLE_GEM__COC: 'false'
BUNDLE_GEM__TEST: rspec
BUNDLE_GEM__MIT: false
```

To build a gem, follow these commands:

```
  cd ~/dev/misc/edu/gem_building

  bundle gem kewl_crypto
```


## The Anatomy of a Gem
Let's briefly go over a gems anatomy.

```
.
├── bin
│   ├── console
│   └── setup
├── Gemfile
├── kewl_crypto.gemspec
├── lib
│   ├── kewl_crypto
│   │   └── version.rb
│   └── kewl_crypto.rb
├── Rakefile
├── README.md
└── spec
    ├── kewl_crypto_spec.rb
    └── spec_helper.rb
```

###### APP_NAME.gemspec

This is the main configuration file for your package.  It's basically how you talk to bundler, gemcutter and rubygems.org.  You indicate your dependencies here, you add a description and name for your gem, and define package details like the version of the gem.  


###### lib/APP_NAME.rb

This is the 'entry point' for your ruby app.  It's the first file loaded when someone does a `require 'your_gem'`.  If your gem is simple enough, the entirety of your ruby code will go here.  


###### lib/APP_NAME/

This folder is for when things get out of hand and you need to break your ruby code up into multiple files.  An easy rule for breaking up your ruby library is that each class should have it's own file.  If a class is massive and has many subclasses that are only used by that one class... then this class can have it's own folder if you want.  But with rubygems, you actually get to employ a little bit of creativity as to how things are organized...  well, not really creativity, I think of it as impromptu logic deduction.  


###### lib/APP_NAME/version.rb

This file is where you define the version of your gem.  There's code in the gemspec file that sources this file such that it can be placed into the configuration object fed to the tool chain.  


###### bin/ vs exe/

The bin folder used to be where people put their 'binary' files, now it's got this setup and console rubish in it.  I'm not fond of either of those files, but the new convention is to put your CLI wrappers in the `exe/` folder rather than in `bin/`.  Feel free to delete this folder in favor of using `pry` and unit tests.  


###### spec/

Tests!  I talked about this after everything else... hmmm...  So we put all our tests in the spec folder.  This workshop isn't about testing.  We'll do that at the end if there's time, quite ironically...



## Gem Extraction, Square 1

So now that you know the anatomy of a gem, we can start working.  Gem extraction at it's best is a matter of copy and pasting.  

> Where do I paste in my code?

*Right!  How about `lib/kewl_crypto.rb`*

Let's take a peak at that file now.  

```
require "kewl_crypto/version"

module KewlCrypto
  # Your code goes here...
end
```

Oh man, it's even telling us to do that exact thing right there in the comments.  

> My next question is, what code are we going to cut out of the larger_app to be place here?

*Wait for models/kewl_crypto.rb*

Right!  Let's start that.  

Maybe the new file will look something like this?
(lib/kewl_crypto.rb)
```
require "base64"

require "kewl_crypto/version"

module KewlCrypto

  class KewlCrypto
    attr_reader :untouched

    def initialize(string)
      @untouched = string
    end

    def encrypted
      Base64.encode64(@untouched)
    end

    def decrypted
      Base64.decode64(@untouched)
    end
  end

end
```

###### Checkout g1: Basic extraction

```
  git checkout 850e0af   # g1: Basic extraction
```

## Improve Library Interface

###### How Cumbersome
That's kinda cool, but using that console script, we can see that it works... but it's a little bit cumbersome to use.  eg...


```
$  ruby bin/console
>  kc = KewlCrypto::KewlCrypto.new("clear")
```

###### Refactor

See how we have to type KewlCrypto twice in a row?  That's pretty much as lame as it gets.  There are a couple ways to deal with this problem, but for me, what I like to do is expose a class variable directly on our KewlCrypto module such that the module method itself is instantiating the KewlCrypto class object and then returning it.  Pretty clever huh?  Let me show you.  


(lib/kewl_crypto.rb)
```
module KewlCrypto

  def self.new(string)
    KewlCrypto.new(string)
  end

  class KewlCrypto
    attr_reader :untouched

    def initialize(string)
      @untouched = string
    end

    def encrypted
      Base64.encode64(@untouched)
    end

    def decrypted
      Base64.decode64(@untouched)
    end
  end

end
```

###### checkout g2: easier to use
```
  git checkout a06c602     # g2: easier to use
```


Ok that's cool and easy to use... but...

> Is it beautiful?  What do you think?  Would you publish this for your peers to see?  How could we clean it up a bit?

*Wait for create extra file*

Right!  We can move the KewlCrypto class to its own file now.  

Have a look at this diff

  https://github.com/TheNotary/kewl_crypto/commit/01e43f96867cb75b6186380de8e8c559d225cd2b

###### checkout g3: class gets own file

I have to admit it's a bit weird having two files named the same thing in a ruby application.  We can look at this of an instance where we named our gem wrong, or where we named our class wrong.  Either way lets not think about it too much and get on with the workshop.  

Ok, before we can use this code from a Gemfile, we need to zip the package up into a `.gem` file.  For this we use rake's built-in `build` command.  

```
  rake build
    kewl_crypto 0.1.0 built to pkg/kewl_crypto-0.1.0.gem.
```

Rake has a handy install command that automates the process of uninstalling the gem at that version if it was previously  the `.gem` file, rebuilding the `.gem` file, and then installing it onto your system.  

Rake also has a `release` subcommand which will send your gem up to rubygems.org for public consumption.  You need to create an account for that to happen.  We won't release this code, but that's the command you run when you want to publish your gem for consumption just like all the other gems you put into your Gemfiles.  


# Section 4 - Hook it into the Rails App

###### Source Gem Off File System
So now is a good time to hook our new gem into our rails app.  Upon the early stages of gem extraction, it's best to source the gem directly off the file system.  

(Gemfile)
```
gem 'kewl_crypto', path: "../kewl_crypto"
```

###### Cut Old Logic from Rails App

Now lets cut out that model... and everything should just work since rails automatically requires all the Gems in our Gemfile upon server boot.  

###### A Manual Test Indicates it Worked!

That's great!  Notice that if we make changes to the gem, we need to reboot the server before they'll impact the rails app.  

After everything seems solid, you have the choice to publish the gem to rubygems.org, or perhaps publish them to a private gem server such as geminabox.  




Who
haz
thoughts?













Clever things:

"We'll come back to this in a moment, but I want to talk about..."

"WHAT IF x3"  <- repeat to draw attention for part 2 of explanations (this guy is brilliant!)

"Can anyone tell me"  <- so attention grabbing!  

"Or another way of putting that"

"[technical definition] by that I mean [concrete description]"

"think of it this way" <-

"For now we're just going to have to chalk that up to one of the mysteries of X"   <- for when something weird is going on and you can't stop to debug
