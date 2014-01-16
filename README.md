# ProcessWire Module "Search Engine Referrer Tracker" #

This module keeps track of the keywords used by users of popular search engines that referred to your site. The keywords will be normalized (stripped off unnecessary white spaces and lower-cased) and counted for the whole site and for references to each of its pages.

The module api lets you retrieve the top keywords per page or for the wohle website as an ordered array structure for template or other usage.

## Installing the module ##

Just copy the module files into you `site/modules/` folder or downoload it via the ModuleManager.  
Your directory structure should look like this:  

site/  
|-> modules/  
|--|-> SearchEngineReferrerTracker  
|--|--|-> README.md  
|--|--|-> SearchEngineReferrerTracker.module  
 
After deploying the module files go to **Setup/Modules** in your ProcessWire backend. You should find the **Search Engine Referrer Tracker** module in the **Search** section. Hit install and be ready.

As soon as the module is installed a new table **module_sert_keywords** will be added to the processwire database.

## Configuring the module ##

There are three configuration options for **Search Engine Referrer Tracker**.

1. You may specify the length of the keywords list retrieved by the module api.
2. You may specify the amount of seconds to cache keyword lists retrieved by the module api. The module uses the MarkupCache for storing data. Set this settings to 0 to disable caching entirely. 
3. You may define a comma-separated list of stop words. Referrer keywords that match any of this stop words will not be tracked.

Additionally you may delete all gathered keyword data from the database and reset the tracking.

## Supported Search Engines ##

As for now the following search engine queries will be recognized when found in $_SERVER['HTTP_REFERER']:

- google
- yahoo
- bing
- ask

Additional search engines may be added in future releases.

## Module API ##

The module api offers a public method `getTopKeywords(int|null $pageId = null, int|null $amount = null)`.  
If `$pageId` is omitted, keyword usage will be computed for the wohle site instead of a single page. If `$amount` is omitted, the default value from the module configuration will be used instead.

	// retrieve list of top keywords for wohle site
	$topKeywordsSite = wire('modules')->get('SearchEngineReferrerTracker')->getTopKeywords();

	// retrieve list of top keywords for single page
	$topKeywordsPage = wire('modules')->get('SearchEngineReferrerTracker')->getTopKeywords($page->id);

The single page mode can be used via a page hook as well.

	// retrieve list of top keywords for single page via page hook
	$topKeywordsPage = $page->getTopKeywords();

The return value would be an array of associative arrays with the keys `keyword`and `ref_count`. Of course the the return value may be an empty array as well if no keywords have been tracked for the site or page.
Here is a typical return value structure encoded in JSON:

	[
		{"keyword":"processwire", "ref_count":42},
		{"keyword":"module", "ref_count":28},
		{"keyword":"neuwaerts", "ref_count":19},
		...
	]