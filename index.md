---
layout: home
title: Bill McRoberts
home: true
---

## Legacy Google Play Binding

Binding a Java Library &bull; Java Bindings Metadata 

Xamarin binding to a legacy Google Play release.

<img src="images/google_play.png" width="20%">


When I originally needed to add AdMob support to my MonoGame Linerunner 3D ([http://linerunner3d.com](http://linerunner3d.com "Linerunner 3D")), I was unable to get AdMob support from the existing Xamarin Google Play component. If I remember correctly, I believe it wasn't included at first - or I may have had some other issue. In any case, it gave me a great opportunity to add Binding to a Java Library to my skill set. This one proved challenging and gave me a chance to dig into the metadata.xml file to get an Admob component in place for myself.

Since then, I have done several Binding projects that built cleanly without any issues. 

| MobVista | Advertising SDK for China ([http://www.mobvista.com/en/](http://www.mobvista.com/en/ "MobVista"))|
| Fortumo | Carrier Billing for China ([https://fortumo.com/](https://fortumo.com/ "Fortumo"))|

But I still keep this project in place to reference for stabilizing a Java Library Binding project.

### Setup

1. I created a new Bindings Library (Android) project.
1. I placed the ```google-play-services.jar``` in the resulting Jars folder.
2. I added the ```google-play-services.jar``` file to the project and set the Build Action to ```EmbeddedJar```.

I then attempted to build the project and got a large number of errors - ouch. 


### Configuration

OK, the first thing is that I only needed the AdMob support, so an easy way to trim down the errors was to remove the packages I didn't need. Opening the ```Metadata.xml``` file in the Transforms folder I proceeded to add a large number of entries that looked like this:

    <remove-node path="/api/package[@name='com.google.android.gms.drive']" />

I could see the packages I needed and the ones I didn't need in the error messages from the build. Walking the ones that were not for AdMob, I added entries as above for each one.

The rest of the errors had the format:

    error CS0102: The type `Com.Google.Ads.Mediation.OnAdClosedEventArgs' already contains a definition for `p0'

This was due to the fact that several events had been created with the same name. To understand this better, take a look at how one of these, ```OnAdClosed``` was corrected in the ```Metadata.xml```:

```

    <attr path="/api/package[@name='com.google.android.gms.ads.mediation']/interface[@name='MediationBannerListener']/method[@name='onAdClosed']" name="argsType">BannerAdClosedEventArgs</attr>
    .
    .
    .
    <attr path="/api/package[@name='com.google.gms.android.ads.mediation']/interface[@name='MediationInterstitialListener']/method[@name='onAdClosed']" name="argsType">InterstitialAdClosedEventArgs</attr>

```

Note here how we are giving to separate names for each of the interfaces that original had events OnAdClosed. Below is the code snippet above with everything removed except for the unique event name definition.

```

    BannerAdClosedEventArgs
    .
    .
    .
    InterstitialAdClosedEventArgs

```

Reference: [http://developer.xamarin.com/guides/android/advanced_topics/java_integration_overview/binding-a-java-library/troubleshooting-bindings/](http://developer.xamarin.com/guides/android/advanced_topics/java_integration_overview/binding-a-java-library/troubleshooting-bindings/) 

### Usage
Once the project was building successfully, I added a reference to it from my game project. I was then able to reference AdMob API's as needed to add advertsing into my game.

#### IMPORTANT: This is no longer necessary as I now am able to embed advertising into my game using the current Google Play component from Xamarin. This write-up is here in case I need a reference to trouble-shoot Java Library Bindings.



