# browsertrix-yaml-examples
YAML files for including and excluding things


## Default and handy starter file
This includes all sub domains like abc.collection.litme.com.ua, handles http:// to https:// conversions & subdomains.

    collection: "collection-litme-com-ua"
    workers: 16
    saveState: always
    seeds:
        - url: http://collection.litme.com.ua/
          include: 
            - .*collection\.litme\.com.ua.*
          scopeType: "host"
    
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
