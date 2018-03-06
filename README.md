# Android PrometheanTVSDK

Promethean player SDK is used to play live and vod video content as well as render overlay content on top of the video.
The SDK supports following players:
- Exo player
- Youtube player
- Any other players, that implement IPTVEngine interface and provide calls to proper IPTVPlayerController methods.

Inside this SDK the controller uses IPTVEngine interface object instead of concrete engine type object. The concrete engine objects for default engines (exo and youtube) are created in PTVPlayerViewCreator factory, that creates engines due to their type.

Promethean sdk documentation:
https://prometheantv.github.io/sdk-android/

## Modules

This project contains two modules:
1. 'player' - sdk module, which includes all sdk logic and ui components
1. 'app' - a sample application module that uses PrometheanTVSDK from 'player' module

## Sample-app

To run the sample-app you should: 
1. Clone this repo
2. Open project in Android Studio
3. Run 'app' module

## SDK configuration

To change the default sdk configuration values in your application, you must override some configuration keys in the application's string resources. To achieve this, you need to create and fill out configuration files such as **PrometheanSDKFirebase.xml** and **PrometheanSDKSocial.xml**. These files should then be placed in the *res\values* folder.
Configuration files should have the same structure as string resources files:
```
<resources>
    <string name="config_key_1">config_value_1</string>
    <string name="config_key_2">config_value_2</string>
    ....
</resources>
```

### Details of filling configuration files

**PrometheanSDKFirebase.xml** - a file with firebase settings.
In this file, you can override the following values:
- **ptv_firebase_api_key** - is the api key for firebase, which can be found in the google-services.json file for your application as *client.api_key.current_key*
- **ptv_firebase_app_id** - is the firebase application identifier that can be found in the google-services.json file for your application as *client.client_info.mobilesdk_app_id*
- **ptv_firebase_db_url** - is a link to the firebase database, which can be found in the google-services.json file for your application as *project_info.firebase_url*
- **ptv_firebase_proj_id** - is the identifier of the firebase project, which can be found in the google-services.json file for your application as *project_info.project_id*

**PrometheanSDKSocial.xml** - a file with the configuration settings for various social networks and services (e.g twitter, twitch).
In this file, you can override the following values:
- **ptv_twitter_consumer_key** - id the consumer key for twitter application, which you will get after creating your application on twitter (read more: https://apps.twitter.com/)
- **ptv_twitter_consumer_secret** - is the consumer secret key for twitter application that you will get after creating your application in twitter (read more: https://apps.twitter.com/)
- **ptv_twitch_client_id** - is the identifier of the twitch application that you will get after creating your application in twitch (read more: https://dev.twitch.tv/)

## SDK integration

If you want to integrate PTV sdk into one of your own projects, you need to perform a few basic tasks to prepare your Android Studio project.

### Gradle configuration

1. Add latest version of ptv sdk with .aar extension in your `app/libs` folder.

2. Then, in your module Gradle file (usually the `app/build.gradle`) add sdk dependency:  
```
dependencies {
	// app dependencies
	compile(name: 'player', ext: 'aar')
}
```

3. To add ability to read from libs folder you should also add this lines of code after your dependencies task:
```
repositories {
    flatDir {
        dirs 'libs'
    }
}
```

4. Then you should add to your dependencies all sdk dependencies. 
If you are already using some of them then please check the versions and update depencencies if necessary.
	1. Support library
	```
	compile "com.android.support:appcompat-v7:${supportLibVersion}"
	```
	2. Glide
	```
	compile "com.github.bumptech.glide:glide:${glideVersion}"
	```
	3. Exo player
	```
	compile "com.google.android.exoplayer:exoplayer:${exoPlayerVersion}"
	```
	4. OkHttp
	```
	compile "com.squareup.okhttp3:okhttp:${okHttpVersion}"
	compile "com.squareup.okhttp3:okhttp-urlconnection:${okHttpVersion}"
	compile "com.squareup.okhttp3:logging-interceptor:${okHttpVersion}"
	```
	5. Retrofit 
	```
	compile "com.squareup.retrofit2:retrofit:${retrofitVersion}"
	compile "com.squareup.retrofit2:converter-gson:${retrofitVersion}"
	compile "com.jakewharton.retrofit:retrofit2-rxjava2-adapter:${retrofitRxAdapterVersion}"
	```
	6. RxJava
	```
	compile "io.reactivex.rxjava2:rxjava:${rxJavaVersion}"
	compile "io.reactivex.rxjava2:rxandroid:${rxJavaVersion}"
	```
	To prevent possible bugs related to rxjava please add such lines to your `build.gradle` file:
	```
	android {
		// your android settings
		packagingOptions {
			// your packaging options
			exclude 'META-INF/rxjava.properties'
		}
	}
	```
	7. Firebase
	```
	compile "com.google.firebase:firebase-core:${playServiceVersion}"
	compile "com.google.firebase:firebase-database:${playServiceVersion}"
	compile "com.google.firebase:firebase-messaging:${playServiceVersion}"
	```
	Note that firebase is a part of google services, so to use it you should add google services plugin after dependencies:
	```
	apply plugin: 'com.google.gms.google-services'
	```
	8. Keen
	```
	compile "io.keen:keen-client-api-android:${keenVersion}@aar"
	```
	9. Twitter
	```
	compile "com.twitter.sdk.android:twitter:${twitterSdkVersion}"
	compile "com.twitter.sdk.android:twitter-core:${twitterSdkVersion}"
	```
	10. IMA
	```
	compile "com.google.ads.interactivemedia.v3:interactivemedia:${imaAdsVersion}"
	compile "com.google.android.gms:play-services-ads:${playServiceVersion}"
	```
	11. Multidex
	```
	compile "com.android.support:multidex:${multidexSupportVersion}"
	```

5. Please make sure that you use these versions of the libraries:
supportLibVersion - 25.3.1
playServiceVersion - 11.0.4
twitterSdkVersion - 3.1.1
okHttpVersion - 3.4.1
retrofitVersion - 2.3.0
rxJavaVersion - 2.0.0
keenVersion - 3.0.0
imaAdsVersion - 3.6.0
glideVersion - 3.7.0
exoPlayerVersion - 2.7.0
multidexSupportVersion - 1.0.1
retrofitRxAdapterVersion - 1.0.0

6. Press `Sync` button in the top toolbar and wait for sdk to sync.

### SDK usage

7. Open your Application class and in onCreate() method initialize sdk via PrometheanManager class.
```
public class MyApplication extends Application {
	@Override
	public void onCreate() {
		PrometheanManager.get().init(this);
	}
}
```

The example initializes sdk with default configuration, but if you want you can set server configuration by setting config key as the second parameter of the init() method. 
Config key types: `PrometheanManager.QA, PrometheanManager.PARTNER_QA, PrometheanManager.PRODUCTION`
Example:
```
PrometheanManager.get().init(this, PrometheanManager.PARTNER_QA);
```

8. To use ptv player or ptv overlays you must create container view for it, so first of all in your xml layout file create RelativeLayout, that will be the same size as PlayerView and placed on the same position.

```
<RelativeLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <YourPlayerView
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />

    <RelativeLayout
        android:id="@+id/containerView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignTop="@id/brightcove_video_view"
        android:layout_alignBottom="@id/brightcove_video_view" />
</RelativeLayout>
```

9. Then you should create instance of `IPTVPlayerController` using one of four methods:

	1. Use this method if you don't have your own player and want to use default player builded in promethean sdk
	```
	IPTVPlayerController createController(RelativeLayout containerView)
	```

	2. Use this method if you have your own player and just want to show overlays on top of it.
	```
	IPTVPlayerController createController(RelativeLayout containerView, String appType)
	```
	appType - is your unique application type, provided by Promethean.

	3. Use this method if you don't have your own player, but you want to use your own controls view.
	```
	IPTVPlayerController createController(RelativeLayout containerView, BaseControlsView controlsView)
	```
	controlsView - your custom controls view that should be extendes from BaseControlsView

10. To prepare controller you should call prepare controller method
```
prepare(String uniqueExternalVideoId)
```
uniqueExternalVideoId - unique video or channel id in your app, that can be used by Promethean to identify your content.

11. If you have your own player you need to notify ptv player controller about your current player status you should call all these methods:
`void onPlayerStarting()` - call this method when your player is starting or buffering
`void onPlayerStarted()` - call this method when your player is started playing for the first time
`void onPlayerResumed()` - call this method when your player is resumed from pausing
`void onPlayerPaused()` - call this method when your player is paused
`void onPlayingEnded()` - call this method when the video is over and your player finished playing
`void onPlayerError()` - call this method when some error occurred during the playback
`void setVideoStreamSize(width, height)` - call this method to set proper video stream size (size of PlayerView without black lines)
