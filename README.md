[![Build Status](https://travis-ci.org/vufind-org/vufind.svg?branch=master)](https://travis-ci.org/vufind-org/vufind)
# VuFind Bento Box 
## Hacking VuFind Combined Search And Making Bento Searching easy - based on VuFind 3.1.3
======
## Set up ILS & Index the catalog
VuFind Installation: https://vufind.org/wiki/installation</br>
MARC Export Notes for different ILS: https://vufind.org/wiki/indexing:marc:export_notes</br>
MARC Records Import: https://vufind.org/wiki/indexing:marc</br>
Automation: https://vufind.org/wiki/administration:automation</br>

## Contact Vendor and request to enable API access. 
Summon: https://api.summon.serialssolutions.com/</br>
EDS: https://www.ebscohost.com/discovery/api</br>
PrimoCentral: https://developers.exlibrisgroup.com/primo/apis</br>

## Active Combined Search
Once you received the API information from your vendor, you can:

### Summon
/local/config/vufind/config.ini
``` sh
[Summon]
apiId        = myAccessId
apiKey       = mySecretKey
```
### EDS
/local/config/vufind/EDS.ini
``` sh
[EBSCO_Account]
ip_auth = false
user_name = [USERNAME]
password  = [PASSWORD]
profile   = [PROFILE]
organization_id = "VuFind 2.x from MyUniversity"
```
### PrimoCentral
/local/config/vufind/primo.ini

### LibGuides
/local/config/vufind/libguides.ini
``` sh
; Your institution id (called site id in api version 2; may have changed w/ upgrade)
iid = my-id

; API version to use (1 or 2)
version = 1
```
/local/config/vufind/combined.ini
``` sh
[Layout]
columns = 3	- or 2 columns
```
Install php:cURL for EDS API
``` sh
php5:
sudo apt-get install php5-curl
php7:
sudo apt-get install php-curl
restart Apache2
```
## Remove Thumbnail/book cover in combined search result page
/theme/bootstrap3/css/combined-search.css</br>
change
``` sh
.combined-list .result .left   {width:25%}
.combined-list .result .middle {width:75%}
.combined-list .result .right  {display:none}
```
to 
``` sh
.combined-list .result .media-left   {display:none}
.combined-list .result .middle {width:100%}
.combined-list .result .right  {display:none}
```

## Add missing EDS style sheet in ajax
/themes/bootstrap3/templates/combined/results-ajax.phtml</br>
Add this line in the end:
``` sh
<?  $this->headLink()->appendStylesheet('EDS.css'); ?>
```
## Redirect more URL back to vendor search interface
/themes/bootstrap3/templates/combined/results-list.phtml</br>
$lookfor is the variable of search query</br>
$moreUrl represents the URL that redirect to by different type of resources.</br>
Change:
``` sh
  $moreUrl = $this->url($params->getOptions()->getSearchAction()) . $results->getUrlQuery()->setPage(1);
  $params->setLimit($limit);
```
To:
``` sh
  if ($currentSearch['label']=='Articles & more'): 
 $moreUrl = "http://search.ebscohost.com/login.aspx?direct=true&site=eds-live&scope=site&type=0&custid=s8953837&groupid=main&profid=eds&mode=bool&lang=en&authtype=cookie,ip,guest&bquery=".urlencode($lookfor)."+AND+PZ+Article";
 elseif($currentSearch['label'] =='Research Guides'):
$moreUrl = "http://guides.wpunj.edu/srch.php?q=".urlencode($lookfor);
 elseif($currentSearch['label']!=='Articles' or $currentSearch['label'] !=='Research Guides'):
   $moreUrl = $this->url($params->getOptions()->getSearchAction()) . $results->getUrlQuery()->setPage(1);
 endif; 
   $params->setLimit($limit);
```

##Add Database Recommendation
Add databases section after “Research Guides”</br>
/themes/bootstrap3/templates/combined/results-list.phtml</br>
insert code before the last <? endif; ?>
``` sh
<? if ($currentSearch['label']=='Research Guides'): ?>
  
 <div class="pull-right"><a href="http://guides.wpunj.edu/az.php?q=<?=urlencode($lookfor)?>" target='_blank' ><i class="fa fa-gears" aria-hidden="true"></i> <?=$this->transEsc('More Results')?></a></div>
<div><h2><a id="databases" name="databases"></a><a href="http://guides.wpunj.edu/az.php?q=<?=urlencode($lookfor)?>" target="_blank">Databases</a></h2></div>

<div id="databases" class="row result clearfix">
<script src="//lgapi.libapps.com/sa.php?site_id=915"></script><script>
springshare_widget_config_1479753727825 = { path: 'assets' };
</script>
<div id="s-lg-widget-1479753727825"></div>
<script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+"://lgapi.libapps.com/widgets.php?site_id=915&widget_type=2&search_terms=<?=urlencode($lookfor)?>&search_match=2&subject_ids=&sort_by=name&list_format=1&drop_text=Select+a+Database...&output_format=1&load_type=2&enable_description=0&widget_title=A-Z+Database+List&widget_height=250&widget_width=100%25&widget_link_color=2954d1&widget_embed_type=1&num_results=6&enable_more_results=0&window_target=2&config_id=1479753727825";fjs.parentNode.insertBefore(js,fjs);}}(document,"script","s-lg-widget-script-1479753727825");</script>
<style>
                #s-lg-widget-1479753727825 ul {padding-left: 25px;}
				#s-lg-widget-1479753727825 li {font-weight: 600;}
                #s-lg-widget-1479753727825 ul li {list-style-type: decimal;}
</style>
 <div id = "altdatabases" style="display: none;">  <i>Please try Selected Databases RECOMMENDED by librarians:</i> <ul>
    <li><a href="http://ezproxy.wpunj.edu/login?url=http://search.ebscohost.com/login.aspx?direct=true&site=ehost-live&scope=site&type=1&db=&db=a9h&authtype=ip&bquery=<?=urlencode($lookfor)?>" target='_blank'><?=$this->transEsc('Academic Search Complete')?></a></li>
    <li><a href="http://ezproxy.wpunj.edu/login?url=http://www.jstor.org/action/doBasicSearch?Query=<?=urlencode($lookfor)?>&acc=on&wc=on&fc=off&group=none" target='_blank'><?=$this->transEsc('Jstor')?></a></li>
    <li><a href="http://ezproxy.wpunj.edu/login?url=https://scholar.google.com/scholar?hl=en&q=<?=urlencode($lookfor)?>" target='_blank'><?=$this->transEsc('Google Scholar')?></a></li>
    <li><a href="http://ezproxy.wpunj.edu/login?url=http://search.proquest.com/central" target='_blank'><?=$this->transEsc('ProQuest Central')?></a></li>
<ul></div>
<script>
// Set id for RECOMMENDED databases to something - #altdatabases
// Set style for #altdatabases to display: none;
// Only show #altdatabases if #databases.s-lg-widget-list-info contains "No results match the request."
$(window).on('load', function() {
$("div[class*='s-lg-widget-list-info']:contains('No results match the request')").append($('#altdatabases').clone().css('display','block') );
    });
</script>
```
