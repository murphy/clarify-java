# Introduction

The clarify-java library allows you to use the Clarify API from within Java. It handles all of the HTTP requests and response codes for you.

The library itself is MIT-licensed so if you find anything which is incomplete, unclear, or even wrong, feel free to submit a pull request!

You can get started in minutes using our Quickstarts:

[http://clarify.io/docs/quickstarts/](http://clarify.io/docs/quickstarts/)

## More Information

While what follows is documentation on the Java library specifically, when in doubt, check out our API documentation at https://developer.clarify.io

## Prerequisites

* Java JDK-1.6 or higher
* Apache Maven 3 or higher

## Java

* Download Java from http://www.oracle.com.
* Make sure you set your JAVA_HOME environment variable correctly.

## Maven

* Please refer to this handy [Maven in 5 Minutes guide](http://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) for any help downloading, installing, configuring, or using Maven.

## Eclipse

* If you are using Eclipse, you can import the project by following these steps in the [M2Eclipse guide](http://books.sonatype.com/m2eclipse-book/reference/creating-sect-importing-projects.html) 

## Building the SDK locally

Note that this is not strictly necessary since Maven will automatically download the SDK from the central Maven repository if it is listed as a dependency.  If you'd like to build it locally, however, follow these instructions:
```
$ git clone https://github.com/Clarify/clarify-java.git
$ cd clarify-java
$ mvn install
```

## Hello World!

To create and test a "Hello World!" app:
```
$ mvn archetype:generate -DgroupId=io.clarify.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
$ cd my-app
$ mvn package
$ java -cp target/my-app-1.0-SNAPSHOT.jar io.clarify.app.App
```

## Adding Clarify as a dependency in your Maven file

Open your pom.xml file and add the following to the <dependencies> section:
```
<dependency>
 <groupId>io.clarify.api</groupId>
 <artifactId>clarify-api-sdk</artifactId>
 <version>1.0.0</version>
</dependency>
```

If you're trying to launch your own library into the Maven Central Repository, check out [these instructions](http://datumedge.blogspot.com/2012/05/publishing-from-github-to-maven-central.html).

## Testing your configuration

Copy-and-paste the following code over the contents of App.java.

*Don't forget to set the appKey variable to the application key you created!*

```
package io.clarify.app

import io.clarify.api.*;
import java.net.URI;

public class App {
  public static void main(String[] args) throws Exception {
    String appKey = "0123456789abcdef";

    // Construct the API client
    ClarifyClient client = new ClarifyClient(appKey);

    // Create your first bundle using an example audio file
    String name = "Harvard Sentences";
    URI mediaUrl =  URI.create("http://media.clarify.io/audio/samples/harvard-sentences-1.wav");
    Bundle bundle = client.createBundle(name, mediaUrl);

    System.out.println(bundle.id());
  }
}
```

## Listing your bundles

```
import io.clarify.api.*;
import java.net.URI;

public class App {
  public static void main(String[] args) throws Exception {
    String appKey = "0123456789abcdef";

    // Construct the API client
    ClarifyClient client = null;
    client = new ClarifyClient(appKey);

    // Start listing all of your media
    BundleList bundleList = client.listBundles();

    JSONArray linkItems = bundleList.getLinkItems();
    for(int i=0;i<linkItems.length();i++) {
        JSONObject item = (JSONObject)linkItems.get(i);
        System.out.println("href="+item.get("href"));
    }

    // ask for the next page, if we have one
    if (bundleList.hasNextPage()) {
        bundleList = bundleList.nextPage();
        // ...
    }
  }
}
```


## Searching your media

```
import io.clarify.api.*;
import java.net.URI;

public class App {
  public static void main(String[] args) throws Exception {
    String appKey = "0123456789abcdef";

    // Construct the API client
    ClarifyClient client = new ClarifyClient(appKey);

    // Search your media by query string
    String query = "monkeys";
    BundleSearchResults bundleSearchResults = client.searchBundles(query);
    JSONArray itemResults = results.getItemResults();
    for(int i=0;i<itemResults.length();i++) {
        JSONObject item = (JSONObject)itemResults.get(i);
        System.out.println("score="+item.get("score"));
    }


    // ask for the next page, if we have one
    if (bundleSearchResults.hasNextPage()) {
        bundleSearchResults = bundleSearchResults.nextPage();
        // ...
    }
  }
}
```

## Deleting your media

```
import io.clarify.api.*;
import java.net.URI;

public class App {
  public static void main(String[] args) throws Exception {
    String appKey = "0123456789abcdef";

    // Construct the API client
    ClarifyClient client = null;
    client = new ClarifyClient(appKey);

    // Search your media by query string
    String bundleId = "abc1234";
    Bundle bundle = client.findBundle(bundleId);
    if bundle != null { 
        boolean success = bundle.delete();
    }
  }
}
```

## Storing your Media

```
// Create your first bundle using an example audio file
String name = "Harvard Sentences";
URI mediaUrl =  URI.create("http://media.clarify.io/audio/samples/harvard-sentences-1.wav");

Bundle bundle = client.createBundle(name, mediaUrl);
System.out.println(bundle.id());
```

## Get the Metadata for a Bundle

```
// Obtain the metadata in one API call by using the embed parameter
String bundleId = "abc1234";
HashMap<String,String> args = new HashMap(); 
args.put("embed","metadata");
Bundle bundle = client.findBundle(bundleId, args);
if bundle != null { 
    System.out.println(bundle.getMetadata());
}

or

// Obtain the metadata by bundle ID in one API call
String bundleId = "abc1234";
Metadata metadata = client.findMetadata(bundleId);
```

## Update the Metadata for a Bundle

```
String bundleId = "abc1234";
String newDataInJson = "{...}";

// Update the metadata in two API calls: 1) Retrieve the bundle with the bundle’s metadata, 2) call the update Metadata API
HashMap<String,String> args = new HashMap(); 
args.put("embed","metadata");
Bundle bundle = client.findBundle(bundleId, args);

if bundle != null { 
    BundleMetadata metadata = bundle.getMetadata();
    metadata.update(newDataInJson);
}

or

BundleMetadata metadata = client.findMetadata(bundleId);
metadata.update(newDataInJson);
```

## Reset the Metadata (delete)

```
String bundleId = "abc1234";
Bundle bundle = client.findBundle(bundleId);
if bundle != null { 
    BundleMetadata metadata = bundle.metadata();
    if metadata != null {
      boolean success = metadata.reset();
    }
}
```

## Add a Track to a bundle

```
String bundleId = "abc1234";
Bundle bundle = client.findBundle(bundleId);
if(bundle != null) { 
    URI trackUrl =  URI.create("http://media.clarify.io/audio/samples/harvard-sentences-1.wav");
    Track track = bundle.addTrack(trackUrl);
}

List of Tracks for a bundle
String bundleId = "abc1234";

// Note: makes two API calls
Bundle bundle = client.findBundle(bundleId);
if bundle != null { 
    BundleTrackList tracks = bundle.listTracks();
}

or

// Note: makes only one API call
BundleTrackList tracks = client.listTracksForBundle(bundleId)

Get a track
String bundleId = "abc1234";
String trackId = "wxyz9876";
Bundle bundle = client.findBundle(bundleId);
if bundle != null { 
   Track track = bundle.findTrack(trackId);
}
```

## Delete a track

```
String bundleId = "abc1234";
String trackId = "wxyz9876";

// Note: makes two API calls
Bundle bundle = client.findBundle(bundleId);
if bundle != null { 
   boolean success = bundle.deleteTrack(trackId);
}

or

// Note: makes only one API call
client.deleteTrack(bundleId, trackId)
```

## Direct-Access Client API

Note: the direct-access client API uses the Resty API directly. See the [Resty documentation](http://beders.github.io/Resty/Resty/Overview.html) and [Javadoc](http://beders.github.io/Resty/Resty/API_Docs.html) for more details on how to use it. 

Example:

```
public class App {
  public static void main(String[] args) throws Exception {
    String appKey = "0123456789abcdef";

    // Construct the API client
    ClarifyClient client = new ClarifyClient(appKey);

    // All API calls to the Resty API will include your app key automatically in the header
    JSONResource jsonResource = 
                client.json(client.buildPathFromResource("/bundles"));

    // … use the standard Resty API to interact with the parsed JSON response directly
  }
}
```
