# Test Checklist for Tracking 2.0 RC 1.0
*Plugins that might be needed:*
- Tracking 2.0
- Slider Ads
- Advanced Ads Pro

A list of tests we use to check if everything is all right. Needs to be automated at one point.

Work in progress

## 1. Basic information

- **1.1** _AJAX handler_ refers to the file created under `wp-content/ajax-handler.php`
- **1.2** _AJAX tracking_ or _Frontend tracking_ refers to the first tracking method under _Advanced Ads > Settings > Tracking_. It is possible that it could be used also in some cases when another method is selected
- **1.3** _new ad_ - could be any ad type with any content. The most simple is just using the _Dummy_ ad type with a title of your choice. If you need different ads then use the _plain text and code_ ad type with code from [this page](https://wpadvancedads.com/codex/ad-placeholder/)
- **1.4** “local” tracking method refers to any method storing impressions and clicks in our database, e.g., “AJAX” or ”on load”, but not “Analytics”
- **1.5** _new placement - find placements under _Advanced Ads > Placements_ or right after an ad was published for the first time. The best default placement for quick testing is _Before Content_.
- **1.6** "Content" placement - if that is part of the test, in most cases, any of the following placements are ok: : _Before Content_, _Content_, _After Content_
- **1.7** _debug option_ refers to _Advanced Ads > Stats > Open database management > Debug mode_. It only shows up after some ads were tracked already.
- **1.8** The call to some tracking methods is visible under the Network-tab in the browser’s developer tools. Look for `ajax-handler.php` for the AJAX method and `batch` for Google Analytics. A click on that call should show the `Headers` tab. Scroll down to `Form Data` to find the ad information. For the AJAX handler, you should see a line like `ads[]: 234` for each tracked ad.
- **1.9** constants are always set in `wp-config.php` above the `That’s all...` comment with a line like `define( 'ABC', 'value' );` Our instructions might say “Set ABC to 'value'”
- **1.10** ignore the sections saying `[needs more details]`. We only need to test them for larger changes or when these are likely to be relevant

## 2. After (first) install

- [ ] **2.1** `wp-content/ajax-handler.php` was created after plugin activation.
- [ ] **2.2** the file contains DB credentials and other information about the environment
- [ ] **2.3** the file returns `no ads` when accessed directly in the browser under `wp-content/ajax-handler.php`
- [ ] **2.4** the file updates when credentials change, Test: change the debug option. `ajax-handler.php` should be updated afterward
- [ ] **2.5** _AJAX_ is the default tracking method under _Advanced Ads > Settings > Tracking_
- [ ] **2.6** we remove the AJAX handler file if it does not create the expected output on installation. To reproduce this: 1. add die( 'test' ); at the top of `nowp-ajax-handler.php`; 2. delete the file `wp-content/ajax-handler.php` (if exists); 3. reload a page in wp-admin

## 3. Tracking

Basic setup:

- create a new "Dummy" ad in the "Before Content" placement
- visit a single post page in the frontend where the ad should then show up (and be tracked)

### 3.1 general (check for all methods)

- [ ] **3.1.1** stats are visible for the ads under _Advanced Ads > Stats_
- [ ] **3.1.2** ignore logged-in users after the line `define( 'ADVANCED_ADS_TRACKING_IGNORE_LOGGED_IN_USERS', true )` was added to `wp-config.php`

### 3.2 AJAX tracking

Set the tracking method to "AJAX”/"Frontend” under _Advanced Ads > Settings > Tracking_

- [ ] **3.2.1** a newly published ad is visible in the call to `ajax-handler.php`
- [ ] **3.2.2** works with multiple ads as well, though only a single AJAX call is sent for all of them together
- [ ] **3.2.3** fall back to using `admin-ajax.php` when `ajax-handler.php` was removed; Test: remove the file via FTP and then track in frontend (loading the backend should create the file again)
- [ ] **3.2.4** [needs more details] show warning about using `admin-ajax.php` if our handler cannot be created; Test: empty the file and set permissions to `000`, then update the debug option to force the file to be rewritten
- [ ] **3.2.5** set `ADVANCED_ADS_TRACKING_LEGACY_AJAX` constant to `true`, reload a page in WP Admin => In the Network tab, you should now no longer find a call to `ajax-handler.php` but to `admin-ajax.php` with the ad IDs in the call.

Setup:

- enable _Advanced Ads > Settings > Ad blocker fix_
- save the settings page
- scroll down to click on _Rebuild asset folder_
- install and enable an ad blocker in your browser, e.g., “Adblock Plus”

- [ ] **3.2.6** AJAX tracking works also with ad blockers

### 3.3 On load tracking

(to be defined)

### 3.4 Analytics tracking

Analytics tracking can be verified by looking for the `batch` call in the Network tab of the browser’s developer tools

- [ ] **3.4.1** after switching to Analytics tracking, no impressions and clicks are tracked locally, there is no call to any AJAX tracking either, especially to `ajax-handler.php`
- [ ] **3.4.2** the file `wp-content/admin-ajax.php` is removed when Analytics tracking is the main method
- [ ] **3.4.3** in the frontend, one should find the call to `batch` in the Network tab with names and IDs of the ads to track in the parameters
- [ ] **3.4.4** works with multiple ads, but only sends one tracking call to Analytics for all of them
- [ ] **3.4.5** switch to _Google Analytics_ tracking under _Advanced Ads > Settings > Tracking_ and save the options. You should now find the _Google Analytics_ option to enter the Analytics Tracking ID
- [ ] **3.4.6** [needs more details] one should find the impression tracking in the Live events report in the Analytics account
- [ ] **3.4.7** set `define( 'ADVANCED_ADS_TRACKING_FORCE_ANALYTICS', true );` in `wp-config.php`. The “Your Tracking ID” option should show up _Advanced Ads > Settings > Tracking_ now even if Analytics is not the selected tracking method. Verify that it tracks now with Analytics and the other method

## 4. Impression Tracking on AMP pages

Test the whole list with each of the following plugins (not enabled at the same time)

- [official AMP plugin](https://wordpress.org/plugins/amp/)
- [AMP for WP](https://wordpress.org/plugins/accelerated-mobile-pages/)

Enabling any of them should allow you to visit any single post in AMP format through a link in the Admin Toolbar in the frontend.

Our Responsive Add-on is not needed if the ads in question are simple HTML or image ads.

- [ ] **4.1** any tracking method above should track impressions for ads displayed on the AMP pages
- [ ] **4.2** _frontend_ method triggers a call to `ajax-handler.php` using `amp-pixel` for all ads visible on the page; test on different page types, e.g., archive, page, post
- [ ] **4.3** when `ADVANCED_ADS_TRACKING_LEGACY_AJAX` constant is set with _frontend_ method, the impressions are tracked on load (no extra visible call)
- [ ] **4.4** _on load_ method works without any additional calls in the frontend
- [ ] **4.5** (not implemented yet) tracking with Google Analytics; when enabled, no local tracking is happening
- [ ] **4.6** test with different tracking options for ads (e.g., tracking disabled, impressions only, clicks only)
- [ ] **4.7** debugging works as expected. Handlers have the "AMP" string in them and URLs include the AMP URLs
- [ ] **4.8** ads using Cache Busting in Advanced Ads Pro are implemented like non-CB ads and therefore tracked in the same way

## 5. Other features

## 6. Deferred Tracking

Ads that show up with a delay, e.g., PopUps that appear after scrolling 200px down, are tracked individually using our Frontend tracking method, even if it is not selected, or Google Analytics.

Prior to Tracking 2.0, the "delayed ads" option had to be selected for this behavior. With Tracking 2.0 deferred tracking is used by default and there is no option to change that. The list of tests assumes that the option is selected or Tracking 2.0 is used.

Test the following with:

- a PopUp ad (with the appropriate add-on) that shows up after scrolling 200px.
- a Sticky ad that shows up with a delay

- [ ] **6.1** with the AJAX tracking method, the call to AJAX tracking only happens when the ad shows up
- [ ] **6.2** with the "on load" tracking method, the impression is not tracked right away, but when it shows up, using the AJAX call
- [ ] **6.3** the Google Analytics tracking method behaves as the AJAX method

Enable Google Analytics tracking as a fallback method using the constant in `wp-config`.

- [ ] **6.4** test in combination with the methods above
- [ ] **6.5** delayed tracking should not happen at all when impression tracking is disabled in general or for these particular ads

...

Use multiple ads

...

Use the same ad in multiple placements, one deferred, the other not.

...

Test with CB

...

### 7. limit ad impressions

Setup:

- when a local tracking method is enabled, there is a _Limit_ option in the _Stats_ meta box on ad edit pages
- when the value in _limit_ is higher than the one in _current_ the ad should show up
- set _Advanced Ads > Settings > Tracking > Recalculate sums_ to `0` (which allows the check to work immediatelly

(Option on the ad edit pages, test with all tracking methods)

- [ ] **7.1** set the limit 2 impressions higher than the current value, reload a page in the frontend with the ad placed on it at least twice => the ad should not show up anymore
- [ ] **7.2** increase the limit again => the ad should show up again

## 8. Compatibility with other add-ons

(maybe separate by tracking method)

### 9. Advanced Ads Pro

General setup:

- Advanced Ads Pro installed and enabled

Setup:

- enable _Advanced Ads > Settings > Pro > Cache Busting_
- set up two ads with different Content placements
- change the _Cache Busting_ option of one of the placements to _AJAX_

- [ ] **9.1** both ad impressions are tracked in that stats
- [ ] **9.2** with AJAX tracking enabled, there is no call to `ajax-handler.php`. In the AJAX call, only the ad ID of the one not using "AJAX" Cache Busting is showing up.

Setup:

- go to the edit page of an ad, scroll to "Display Conditions", select "Device" with the "Desktop" option
- set up a Content placement with that ad
- set Cache Busting option of the placement to "auto"

- [ ] **9.3** when using a local tracking method, the ad in the placement should always be visible in a call of `ajax-handler.php`

Setup:

- create two ads
- create a new group under _Advanced Ads > Groups_ and assign both ads
- enable the _Refresh Interval_ option
- make sure that _Advanced Ads > Settings > Pro > Cache Busting_ is enabled
- add the group to a post using a Content placement

- [ ] **9.4** Go to the post with the rotating ads. The impression of an ad is tracked the first time it appears, so there should be multiple calls to `ajax-handler.php` depending on the number of ads in the group

Setup:

- "Content" placement
- set to “after 1 paragraph” (or higher if the post in the frontend is longer)
- post in frontend with more than 3 paragraphs
- “repeat position” option enabled

- [ ] **9.5** Load the single page in the frontend. The ad should be tracked as often as it shows up.

### 10. Ad Slider

Setup:

- create two or more ads
- install and activate the Ad Slider add-on
- create a new group of the "slider" type
- place the group in the frontend using a shortcode or placement

- [ ] **10.1** Each ad in a slider is tracked on page load and only once, not when it appears in the slider

## 11. Tracking debug

Only shows up in the options when ad impressions were tracked, locally.

- [ ] **11.1** find debug mode under _Advanced Ads > Stats > Open database management_ when impressions were tracked
- [ ] **11.2** debug all ads or just a single one
- [ ] **11.3** when enabled, `wp-content/advanced-ads-tracking-{URL}-{RANDOMHASH}.csv` is created with a line per tracking
- [ ] **11.4** setting `ADVANCED_ADS_TRACKING_DEBUG` in `wp-config.php` enables the debugging option manually. `0` => disabled, `1` => enabled for all ads, `2` and higher => debug a single ad only; the option in the settings overrides this though, so it is still possible to enable debugging for 24h even if the constant has the value `0`
- [ ] **11.5** the _delete the file_ link removes the log file

## 12. Tracking Bots

- use a browser that allows changing the user agent, e.g., Safari’s "Responsive Design" test option. Note: I just stumbled upon an issue where the showed me my changed user agent but used a different one. Saving it again helped
- use the `Mediapartners-Google` as string in the user agent setting
- add some ads on the page

(test for all tracking methods)

### 12.1 Impressions

- [ ] **12.1.1** ads should show but not be tracked in the frontend
- [ ] **12.1.2** ads should neither show nor be tracked when the _Advanced Ads > Settings > General > Hide ads from bots_ is enabled and the user agent includes the string “bot”.
- [ ] **12.1.3** ads should show AND be tracked when _Advanced Ads > Settings > Tracking > Track bots_ is enabled

### 12.2 Clicks

- [ ] **12.2.1** click on an ad => it should not be tracked
- [ ] **12.2.3** clicks are tracked for the mentioned bots when _Advanced Ads > Settings > Tracking > Track bots_ is enabled

## 12.3 On uninstall

- [ ] **12.3.1** `wp-content/ajax-handler.php` was removed when the plugin is disabled (not deleted)
- [ ] **12.3.2** tracking tables `wp_advads_impressions` and `wp_advads_clicks` are removed when the `remove on uninstall` option was is enabled
