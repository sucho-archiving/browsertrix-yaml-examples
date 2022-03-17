# browsertrix-yaml-examples
YAML files for including and excluding things

See the [browsertrix project](https://github.com/webrecorder/browsertrix-crawler) for more general documentation.

## Default and handy starter file
This includes all sub domains like abc.collection.litme.com.ua, handles http:// to https:// conversions & subdomains.

    collection: "collection-litme-com-ua"
    workers: 16
    saveState: always

    # A few examples that exclude parts of a page from being fetched and recorded.
    blockRules:

      # Unnecessary trackers
      - url: google-analytics.com
      - url: googletagmanager.com
      - url: googlesyndication.com
      - url: yandex.ru
      - url: liveinternet.ru
      - url: hotlog.ru
      - url: openstat.net
      - url: mycounter.ua
      - url: facebook.(com|net)

      # Malware
      - url: www.acint.net       # spam-seo.sape malware
      - url: news.2xclick.ru     # mwblacklisted35 malware
      - url: culturaltracking.ru # culturaltracking malware

      # Non-threatened resources that are bandwidth-intensive and perhaps not
      # a current priority. Uncomment these rules to bypass recording.
      #- url: youtube.com/embed/ # Embedded YouTube videos
      #- url: w.soundcloud.com   # Embedded SoundCloud tracks

    seeds:
      - url: http://collection.litme.com.ua/
        scopeType: "domain"

## Excluding trouble

If you notice that a crawl is collecting duplicate links due to parameters, like 

            - '{"url":"https://nmiu.org/index.php/novyny-museum?iccaldate=2022-4-1","seedId":0,"depth":2}'
            - '{"url":"https://nmiu.org/index.php/novyny-museum?iccaldate=2023-03-1","seedId":0,"depth":2}'
            - '{"url":"https://nmiu.org/index.php/novyny-museum?iccaldate=2022-2-1","seedId":0,"depth":2}'
            
where each is the same base page when loaded in the browser, then first check that excluding it results in the same webpage.
in this case, as https://nmiu.org/index.php/novyny-museum goes to the same webpage, we can exclude all cases where iccaldate= is used as a query parameter / link to follow by adding the following to our yaml file:

    exclude:
      - .*iccaldate=.*

This will prevent the same page taking up multiple workers / space in the final file / time.
So the whole file will read

    collection: "collection-nmiu-org"
    workers: 16
    saveState: always
    seeds:
        - url: https://nmiu.org
          include: .*nmiu\.org.*
          scopeType: "host"
          exclude:
            - .*iccaldate=.*
            
            
## Making Sure You Don't Go Out Of Depth

Some websites have recursive links.  They can look like this

    '{"url":"https://nmiu.org/lektoriy/181-ekskursiji-kontent/vysta/3/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/index.php/exponat-tyzhnya","seedId":0,"depth":48}'

If you notice this kind of pattern in the URLs of a site that never seems to end, avoid getting stuck in such a trap by adding the "depth" flag to the yaml file:

    collection: "collection-litme-com-ua"
    workers: 16
    saveState: always
    seeds:
        - url: http://collection.litme.com.ua/
          include: 
            - .*collection\.litme\.com.ua.*
          scopeType: "host"
          depth: 25

This will tell the tool to only follow a given path to a depth of 25 different URL clicks, so use it with care!

In the example given at the start of this section, exclude can also be used with 
    
    exclude: 
      - .*index.php/index.php.*
      
as this was what was causing the recursion
