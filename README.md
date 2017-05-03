[![Build Status](https://travis-ci.org/vufind-org/vufind.svg?branch=master)](https://travis-ci.org/vufind-org/vufind)
VuFind Bento Box - based on VuFind 3.1.3
======

VuFind Installation: https://vufind.org/wiki/installation</br>
MARC Export Notes for different ILS: https://vufind.org/wiki/indexing:marc:export_notes</br>
MARC Records Import: https://vufind.org/wiki/indexing:marc</br>
Automation: https://vufind.org/wiki/administration:automation</br>

1. Contact Vendor and request to enable API access. 

Summon: https://api.summon.serialssolutions.com/</br>
EDS: https://www.ebscohost.com/discovery/api</br>
PrimoCentral: https://developers.exlibrisgroup.com/primo/apis</br>

2. Active Combined Search

Once you received the API information from your vendor, you can:

Summon:</br>
/local/config/vufind/config.ini
``` sh
[Summon]
apiId        = myAccessId
apiKey       = mySecretKey
```
EDS:</br>
/local/config/vufind/EDS.ini
``` sh
[EBSCO_Account]
ip_auth = false
user_name = [USERNAME]
password  = [PASSWORD]
profile   = [PROFILE]
organization_id = "VuFind 2.x from MyUniversity"
```
PrimoCentral:</br>
/local/config/vufind/primo.ini</br>

LibGuides:</br>
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
Install php:cURL for EDS API</br>
Php5:</br>
sudo apt-get install php5-curl

php7:</br>
sudo apt-get install php-curl

restart Apache2

3. Remove Thumbnails/pics in combined search result page

\theme\bootstrap3\css\combined-search.css</br>
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

4. Add missing EDS style sheet in ajax

\themes\bootstrap3\templates\combined\results-ajax.phtml</br>
Add line in the end:
``` sh
<?  $this->headLink()->appendStylesheet('EDS.css'); ?>
```

