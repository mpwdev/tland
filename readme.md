
# Test Checklist for Tracking 2.0 RC 1.0

Plugins that might be needed:
Tracking 2.0
Slider Ads
Advanced Ads Pro

A list of tests we use to check if everything is all right. Needs to be automated at one point.

Work in progress

## Basic information

- _AJAX handler_ refers to the file created under `wp-content/ajax-handler.php`
- _AJAX tracking_ or _Frontend tracking_ refers to the first tracking method under _Advanced Ads > Settings > Tracking_. It is possible that it could be used also in some cases when another method is selected
- _new ad_ - could be any ad type with any content. The most simple is just using the _Dummy_ ad type with a title of your choice. If you need different ads then use the _plain text and code_ ad type with code from [this page](https://wpadvancedads.com/codex/ad-placeholder/)
- “local” tracking method refers to any method storing impressions and clicks in our database, e.g., “AJAX” or ”on load”, but not “Analytics”
- _new placement - find placements under _Advanced Ads > Placements_ or right after an ad was published for the first time. The best default placement for quick testing is _Before Content_.
- "Content" placement - if that is part of the test, in most cases, any of the following placements are ok: : _Before Content_, _Content_, _After Content_
- _debug option_ refers to _Advanced Ads > Stats > Open database management > Debug mode_. It only shows up after some ads were tracked already.
- The call to some tracking methods is visible under the Network-tab in the browser’s developer tools. Look for `ajax-handler.php` for the AJAX method and `batch` for Google Analytics. A click on that call should show the `Headers` tab. Scroll down to `Form Data` to find the ad information. For the AJAX handler, you should see a line like `ads[]: 234` for each tracked ad.
- constants are always set in `wp-config.php` above the `That’s all...` comment with a line like `define( 'ABC', 'value' );` Our instructions might say “Set ABC to 'value'”
- ignore the sections saying `[needs more details]`. We only need to test them for larger changes or when these are likely to be relevant