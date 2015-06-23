# Akana API Hook
![Image of Akana] 
(https://www.akana.com/img/formerlyLOGO8.png) 
<br/>
[Akana.com](http://akana.com)

## Twitter-API-Hook
API Hook using Akana's Products to interact with the Twitter API.  Also, integrates with Twitter's OAuth authorization steps.

## Twitter API

### About the API
- The Twitter API.
- API Documentation: [Twitter API Documents](https://dev.twitter.com/overview/documentation)

### Pre-Reqs
- Create a Twitter Developer account at [Twitter Developers](https://developer.twitter.com) and define your application.
    + Click on the 'Create New App' button.
    + Enter the name of your application.
    + Enter a description for your application.
    + Enter the Website of your application.
    + If necessary, enter the Callback URL that if required for OAuth 1.0a
    + Accept the agreement and click on 'Create your Twitter appliction' button.
- You must install the pso extension custom policies
    + unzip the com.akana.pso.apihooks.extensions_7.2.0.zip (available in this repository) into the /sm70 directory.
    + unzip the com.akana.pso.apihooks.technology.preview_7.2.90.zip (available in this repository) into the /sm70 directory.
    + unzip the com.akana.pso.persistence_7.2.0.zip (available in this repository) into the /sm70 directory.
    + stop all PM and ND nodes
    + Run the configurator for all PM and ND instances:
        * for each instance delete the /{akanainstallroot}/{sm7.2}/instances/{instanceName}/cache directory. This is important to do before you run the below command.
        * Run this command, depending on your environment:
            - Windows: `<Gateway base dir>\sm70\bin\startup.bat configurator -Dsilent=true -DdeploymentName=Standalone -Dproperties=C:\\myprops.properties`
            - UNIX/Linux: `<Gateway base dir>/sm70/bin/startup.sh configurator -Dsilent=true -Dproperties=/export/home/username/myprops.properties`
            - The myprops.propertis path must be the fully qualified path, and the file contents will look like:
            ```
            container.instance.name=<instance name, e.g. PM>
            wizard.mode=update
            ```
    + Using the Akana Admin Console, install the following Plug-ins in each PM container:
        * Akana PSO Persistence
    + Using the Akana Admin Console, install the following Plug-ins in each ND container:
        * Akana APIHooks Enhancements 
    + Restart all PM and ND containers.

### Getting Started Instructions

#### Download and Import
- Download org_Twitter_API_Hook_export.zip
- Download the migration.properties file, and edit it to replace the text with the 'Container Key' of the ND or ND cluster in your target PM.
    + The container key is found by going to the 'Details Tab' of the ND, or ND Cluster, defined in the Policy Manager Console.  Looking at the 'Container Overview' tab on that page, copy the 'Container Key:' value.
    ![container key screenshot](https://github.com/pogo61/Google-Sheets-API-Integration/blob/master/Screen%20Shot%202015-03-18%20at%2011.24.45%20am.png "ND Container Key")
    + Login to Policy Manger, example: https://<hostname>:<port>
    + Select the root 'Registry' organization and click on the 'Import Package' from the Action Portlet window on the right side of the screen
        * Click on the button to browse for the org_Twitter_API_Hook_export.zip archive file
        * Make sure to select the migrations.properties file.
        * Click OK button to start the importation of the hook.
    + This will create a 'Twitter API Hook Organization' with the requisite artifacts to run the API.

#### Verify Import
- Expand the service folder in the 'Twitter API Hook' you imported and find 'Twitter_API_vs0'

#### Activate Anonymous Contract
- Expand the contracts folder in the 'Twitter API Hook' you imported and find the 'Twitter Anonymous' contract under the 'Provided Contracts' folder.
- Click on the 'Activate Contract' workflow activity in the right hand Activities portlet.
- Ensure that the status changes to 'Workflow is Completed'

#### Configure Security
- Go to Twitter API Hook --> Policies --> Operational Policies --> ProcessVariables policy
    + Click 'modify' in the XML Policy Tab.  An XML Policy content editor dialog will be displayed.
    + Change the value of the 'redirectURI' element to be the 'Redirect URL' value you added in the Twitter Developers Application Console, from above.
    + Save the changes
    + Click on the 'Activate Policy' workflow activity in right hand Activities portlet.
    + Validate that the status changes to 'State: Active'

####Verify Connectivity
Twitter API offers using 2-legged OAuth2 to consume specific API's.  Akana Software's Twitter API Hook are successfully authenticated using Twitter's OAuth2 API's.

The figure below shows the entire OAuth2 2-Legged Application-Only flow:
![2-Legged OAuth](https://github.com/AkanaInc/Twitter-API-Hook/blob/master/src/2-leggedOauth.png "2-Legged OAuth")
Steps:
<br/>
1-4) The consuming application requests an access token to use Twitter API's via Akana Software's API Gateway.  Twitter expects the api key and api secret to be passed in a Basic Authentication header on the message.  The grant type is a required parameter with the value of 'client_credentials':
<br/>
* Request:

    ```
        POST /oauth2/token HTTP/1.1
        Accept-Encoding: gzip,deflate
        Content-Type: application/x-www-form-urlencoded
        Authorization: Basic eHlXeTUxZmFiYnpaMlBRRktvTVlJUDJVdzp2d2dxbjFRcU91d3E2UWdvRE54WUlQREhjSXh6bjRxTHBpOGJZT0RNaGlkblpWakRXWA==
        Content-Length: 29
        Host: enord-macbook-pro.local:9920
        Connection: Keep-Alive
        User-Agent: Apache-HttpClient/4.1.1 (java 1.5)
    ```
* Response:

    ```
        HTTP/1.1 200 OK
        Content-Type: application/json;charset=utf-8
        Cache-Control: no-cache, no-store, must-revalidate, pre-check=0, post-check=0
        content-disposition: attachment; filename=json.json
        Date: Mon, 18 May 2015 13:25:32 GMT
        Expires: Tue, 31 Mar 1981 05:00:00 GMT
        Last-Modified: Mon, 18 May 2015 13:25:32 GMT
        ml: A
        Set-Cookie: guest_id=v1%3A143195553271495424;Domain=.twitter.com;Path=/;Expires=Wed, 17-May-2017 13:25:32 UTC
        status: 200 OK
        strict-transport-security: max-age=631138519
        x-connection-hash: 367252cdcf4c0f282032293078848c3c
        x-content-type-options: nosniff
        x-frame-options: SAMEORIGIN
        x-response-time: 17
        x-transaction: ef6f24c4ddbfc530
        x-tsa-request-body-time: 0
        x-twitter-response-tags: BouncerCompliant
        x-ua-compatible: IE=edge,chrome=1
        x-xss-protection: 1; mode=block
        Transfer-Encoding: chunked
        Server: Jetty(7.6.10.v20130312)

        {"token_type":"bearer","access_token":"AAAAAAAAAAAAAAAAAAAAAArPfgAAAAAAEbfGdNpuDl%2FYCIC7Dx0%2FGgkbMEs%3DrX8NQm2O0a7eHUBGkhQ6ADfHgFGn2ZJEUvBcY5IoIc7rgpUkgK"}
    ```

5-8) The consuming application requests an actual API using the access token that was returned from Twitter's OAuth2 call:
* Request:

    ```
        GET /twitterapihook/statuses/user_timeline.json?count=100&screen_name=twitterapi HTTP/1.1
        Authorization: Bearer AAAAAAAAAAAAAAAAAAAAAArPfgAAAAAAEbfGdNpuDl%2FYCIC7Dx0%2FGgkbMEs%3DrX8NQm2O0a7eHUBGkhQ6ADfHgFGn2ZJEUvBcY5IoIc7rgpUkgK
        Accept-Encoding: gzip
        Host: enord-macbook-pro.local:9920
        Connection: Keep-Alive
        User-Agent: Apache-HttpClient/4.1.1 (java 1.5)
    ```
* Response:

    ```
        [
          {
          "created_at": "Tue Apr 21 08:57:15 +0000 2015",
          "id": 590439141420224512,
          "id_str": "590439141420224512",
          "text": "API updates for Direct Message options https://t.co/RXjGMkCbbi",
          "source": "<a href=\"http://itunes.apple.com/us/app/twitter/id409789998?mt=12\" rel=\"nofollow\">Twitter for Mac<\/a>",
          "truncated": false,
          "in_reply_to_status_id": null,
          "in_reply_to_status_id_str": null,
          "in_reply_to_user_id": null,
          "in_reply_to_user_id_str": null,
          "in_reply_to_screen_name": null,
          "user":       {
             "id": 6253282,
             "id_str": "6253282",
             "name": "Twitter API",
             "screen_name": "twitterapi",
             "location": "San Francisco, CA",
             "description": "The Real Twitter API. I tweet about API changes, service issues and happily answer questions about Twitter and our API. Don't get an answer? It's on my website.",
             "url": "http://t.co/78pYTvWfJd",
             "entities":          {
                "url": {"urls": [            {
                   "url": "http://t.co/78pYTvWfJd",
                   "expanded_url": "http://dev.twitter.com",
                   "display_url": "dev.twitter.com",
                   "indices":                [
                      0,
                      22
                   ]
                }]},
                "description": {"urls": []}
             },
             "protected": false,
             "followers_count": 3283862,
             "friends_count": 48,
             "listed_count": 12958,
             "created_at": "Wed May 23 06:01:13 +0000 2007",
             "favourites_count": 27,
             "utc_offset": -25200,
             "time_zone": "Pacific Time (US & Canada)",
             "geo_enabled": true,
             "verified": true,
             "statuses_count": 3534,
             "lang": "en",
             "contributors_enabled": false,
             "is_translator": false,
             "is_translation_enabled": false,
             "profile_background_color": "C0DEED",
             "profile_background_image_url": "http://pbs.twimg.com/profile_background_images/656927849/miyt9dpjz77sc0w3d4vj.png",
             "profile_background_image_url_https": "https://pbs.twimg.com/profile_background_images/656927849/miyt9dpjz77sc0w3d4vj.png",
             "profile_background_tile": true,
             "profile_image_url": "http://pbs.twimg.com/profile_images/2284174872/7df3h38zabcvjylnyfe3_normal.png",
             "profile_image_url_https": "https://pbs.twimg.com/profile_images/2284174872/7df3h38zabcvjylnyfe3_normal.png",
             "profile_banner_url": "https://pbs.twimg.com/profile_banners/6253282/1431474710",
             "profile_link_color": "0084B4",
             "profile_sidebar_border_color": "C0DEED",
             "profile_sidebar_fill_color": "DDEEF6",
             "profile_text_color": "333333",
             "profile_use_background_image": true,
             "default_profile": false,
             "default_profile_image": false,
             "following": null,
             "follow_request_sent": null,
             "notifications": null
          },
          "geo": null,
          "coordinates": null,
          "place": null,
          "contributors": null,
          "retweet_count": 95,
          "favorite_count": 99,
          "entities":       {
             "hashtags": [],
             "symbols": [],
             "user_mentions": [],
             "urls": [         {
                "url": "https://t.co/RXjGMkCbbi",
                "expanded_url": "https://twittercommunity.com/t/api-updates-for-direct-messages-rules/36061",
                "display_url": "twittercommunity.com/t/api-updates-…",
                "indices":             [
                   39,
                   62
                ]
             }]
          },
          "favorited": false,
          "retweeted": false,
          "possibly_sensitive": false,
          "lang": "en"
       }
    ]
    ```

#### Reference
[Twitter Application-Only](https://dev.twitter.com/oauth/application-only)

#### License
Copyright 2015 Akana, Inc.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
