---
layout: post
title: "Waiting for completed Ajax in Capybara and other tricks."
date: 2013-06-16 16:17
comments: true
categories: 
---

[Capybara 2.1](https://github.com/jnicklas/capybara) does a pretty good job waiting for elements to appear or disapear from the page you're testing. Every Capybara finder method (`find_field`, `find_button`), or action method that depends on finding something (`fill_in`, `choose`, `check`), or even querying methods (`has_content?`, `has_css?`) have a built-in waiting mechanism explained in [the README](https://github.com/jnicklas/capybara#asynchronous-javascript-ajax-and-friends).
Essentially, Capybara will wait for the specified element to appear or disapear from the page, before asserting or performing the required action. The amount of time to wait is configurable.
For example, if you have this (artificial) test:

``` ruby
visit 'http://www.wikipedia.org/'
fill_in 'search', with: 'Lisbon'
click_button '→'
puts find('#firstHeading').text
# outputs 'Lisbon'
```

Capybara will wait for an element with name or id `search` to appear on the page before filling it with `Lisbon`. After that it will wait for the button labeled `→` to apear on the page before clicking it. Finally, it will wait for the element with a CSS id of `firstHeading` to appear on the page for the find action.

This is all very good **if you know what to wait for** in the first place. If clicking a button triggers some Ajax call that fills some select element with random data, you will have some trouble knowing what to wait for.

For these situations you can add the following code to your testing harness to wait for completed Ajax requests.

```javascript
// Add this to every javascript in testing mode
if (window.jQuery) {
  _ajax_sent      = 0; // number of sent ajax requests
  _ajax_completed = 0; // number of completed ajax requests
  $(document).ajaxSend(function(e, x, s) { _ajax_sent++; });
  $(document).ajaxComplete(function(e, x, s) { _ajax_completed++; });
}
```

This first snippet adds two custom Ajax counters that will increment for each sent and completed request.

```ruby
# Add this to a new file
# spec/support/capybara_helpers.rb
module CapybaraHelpers
  def waiting_for_completed_ajax(seconds = Capybara.default_wait_time)
    ajax_sent = page.evaluate_script("_ajax_sent").to_i
    yield
    raise "No Ajax request sent after action. Can't wait for completed Ajax." if ajax_sent == page.evaluate_script("_ajax_sent").to_i
    Timeout::timeout(seconds) do
      until( page.evaluate_script "_ajax_sent == _ajax_completed" ) do
        sleep 0.1
      end
    end
  rescue Timeout::Error
    raise "Ajax request didn't complete in #{seconds} seconds (sent requests: #{page.evaluate_script('_ajax_sent')}, completed requests: #{page.evaluate_script('_ajax_completed')})"
  end
end
```

This snippet defines a helper method that takes a block. The method checks that the action present on the block triggers an Ajax call and waits for that call to finish. You then include it in your testing framework of choice. For example in RSpec, configure it this way:

```ruby
# spec/spec_helper.rb
require 'support/capybara_helpers'

RSpec.configure do |config|
  config.include(CapybaraHelpers)
end
```

And then use it like this:

```ruby
visit '/'
waiting_for_completed_ajax do
  click_button 'generate_random_options'
end
first_option = first('#roulette option').text
select first_option, from: 'roulette'
```

Bear in mind that if you have lots of background activity firing Ajax calls this method may not work as expected. This was designed for waiting for Ajax requests triggered by some action, and may not work reliably in other situations.

The way that you include the needed javascript in your testing environment is up to you, as this depends heavily on your testing setup. In my case I have a special javascript file that gets included in all javascript bundles if the project is in "testing mode".

Also in testing mode, a lot of external services are turned off, or mocked out, like facebook, google analytics and adsense, and other metrics tools. This will increase the reliability of your tests, because you are removing an external source of entropy that you do not control and that can interfere with the outcome of your tests. As a bonus, they will run faster.

As a final note, in testing mode I also include this snippet in all javascript bundles:

```javascript
if (window.jQuery) { jQuery.fx.off = true; }
```

This will turn off all jQuery animations and speed up your integration tests. More importantly, it will increase their reliability because stuff is not moving around while you're trying to click it. This is especially important if you're using [Poltergeist](https://github.com/jonleighton/poltergeist) to drive [PhantomJS](http://phantomjs.org) as it has an advanced click behaviour [explained in the README](https://github.com/jonleighton/poltergeist#mouseeventfailed-errors). Poltergeist asks the browser (PhantomJS) the coordinates of an element to click, and then tries to click on those coordinates. If you're using jQuery animations, the element may be moving around and the click will fail. If you turn off animations with [jQuery.fx.off](http://api.jquery.com/jQuery.fx.off/) these problems will go away and you'll have snappier tests because you're not waiting for the animations to reach their final state.

These tricks have increased the reliability and speed of my integration tests. I hope it helps yours too.
