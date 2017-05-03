[![Build Status](https://travis-ci.org/vufind-org/vufind.svg?branch=master)](https://travis-ci.org/vufind-org/vufind)
VuFind Bento Box - based on VuFind 3.1.3
======

VuFind Installation: https://vufind.org/wiki/installation
MARC Export Notes for different ILS: https://vufind.org/wiki/indexing:marc:export_notes
MARC Records Import: https://vufind.org/wiki/indexing:marc
Automation: https://vufind.org/wiki/administration:automation

1. Contact Vendor and request to enable API access.  

Summon: https://api.summon.serialssolutions.com/
EDS: https://www.ebscohost.com/discovery/api
PrimoCentral: https://developers.exlibrisgroup.com/primo/apis

2. Active Combined Search

Once you received the API information from your vendor, you can:
Summon:
/local/config/vufind/config.ini
[Summon]
apiId        = myAccessId
apiKey       = mySecretKey

EDS:
/local/config/vufind/EDS.ini
[EBSCO_Account]
ip_auth = false
user_name = [USERNAME]
password  = [PASSWORD]
profile   = [PROFILE]
organization_id = "VuFind 2.x from MyUniversity"

PrimoCentral:
/local/config/vufind/primo.ini

LibGuides:
/local/config/vufind/libguides.ini
; Your institution id (called site id in api version 2; may have changed w/ upgrade)
iid = my-id

; API version to use (1 or 2)
version = 1

/local/config/vufind/combined.ini
[Layout]
columns = 3	- or 2 columns

For EDS
Php5:
sudo apt-get install php5-curl

php7:
sudo apt-get install php-curl

restart Apache2

3. Remove Thumbnails/pics in combined search result page

\theme\bootstrap3\css\combined-search.css
change
.combined-list .result .left   {width:25%}
.combined-list .result .middle {width:75%}
.combined-list .result .right  {display:none}
to 
.combined-list .result .media-left   {display:none}
.combined-list .result .middle {width:100%}
.combined-list .result .right  {display:none}

4. Add missing EDS style sheet in ajax
\themes\bootstrap3\templates\combined\results-ajax.phtml
Add line in the end:
<?  $this->headLink()->appendStylesheet('EDS.css'); ?>

