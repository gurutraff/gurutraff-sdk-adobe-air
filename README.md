# GuruTraff SDK
## Builds
Builds can be found from the [releases page](https://github.com/gurutraff/gurutraff-sdk-flash/releases).

## Minimum Requirements:
   - Adobe AIR 32 or higher.
   - Android 4.1 API 16 or higher.
   - iOS 8 or higher.

 ## Integration steps
### Step 1: Import GuruTraffSDK.ane into the project.
  

    <extensionID>com.gurutraff.flash</extensionID>

  *:exclamation: Warning! For the library to work on Android, play-services-basement and play-services-ads-identifier libraries must be connected*
### Step 2: Initialize the GuruTraff SDK in the code.
To initialize the GuruTraff SDK, you must call in: 

    GuruTraff.initialize("YOUR_APP_ID");

We also recommend setting the player ID according to the version of your backend server. To do this, call: 

    GuruTraff.setAppUserId = "YOUR_USER_ID;

To reward the user for viewing the AppUserId will be given from the ad server to your pre-registered server.

:exclamation:If you have in-app purchases, they must be sent using one of the methods:
 Android:
    

    eventPurchaseAndroid(price:Number, currency: String, originalJson:String, signature:String):void
iOS:
    

    eventPurchaseIOS(price:Number, currency: String, transactionId:String):void    
where: 

- price - price of goods in local currency 
- currency - local currency
- originalJson -  JSON a string which is used to authenticate the purchase 
- signature - data signature was signed with a private key   
- transactionId - transaction id
 
### Step 3:  Showing ads
a) In advance we recommend that you call ad caching using the method:

    GuruTraff.cacheVideo("placementName");

b) Check if cached ads are available using the method:

    GuruTraff.isReadyVideo("placementName");

c) You can show ads using the method:

    GuruTraff.showVideo("placementName");

d) To track changes in advertising conditions, you can subscribe to events
  - GuruTraffEvent.VIDEO_DID_CACHE    //resources were cached to display video ads
  - GuruTraffEvent.VIDEO_DID_FAIL_TO_LOAD  //error loading video ads
  - GuruTraffEvent.VIDEO_WINDOW_WILL_DISPLAY  //called before opening the advertisement window
  - GuruTraffEvent.VIDEO_WINDOW_DID_DISPLAY  //is called after the advertising window was opened on the screen 
  - GuruTraffEvent.VIDEO_DID_DISMISS  //after hiding the video, the post image is shown
  - GuruTraffEvent.VIDEO_WINDOW_WILL_CLOSE   //called before closing the advertisement window
  - GuruTraffEvent.VIDEO_WINDOW_DID_CLOSE  //called after the ad window was closed
  - GuruTraffEvent.VIDEO_POST_VIEW_DID_CLICK  //player tapped on an ad
  - GuruTraffEvent.VIDEO_RECEIVE_REWARD  //player can be rewarded for watching video ads
        
  Possible loading ads errors :
  - GuruTraff.ERROR_NO_ADS - no ads available
  - GuruTraff.ERROR_NO_FREE_DISK_SPACE - no free disk space
            

>    Note: 
> - The ads don't have to be cached in advance, but in this case, it's not possible to predict when the ads will load and display.
> - Before showing ads, all the sounds must be muted (paused).

# Ready!
Now you can show ads in your application.


## * Reward users on install
To increase your traffic rating, we recommend that you reward players for installing the advertised game.
    Using our API, you can get the necessary information to reward the player with the virtual currency (e.g., crystals, coins ...)
    To request information about the reward, we recommend that when you start the game and after switching from the background mode to the active mode, call the method:
    

    GuruTraff.requestMotivatedRewards();
    
  To process the received information, you can subscribe to the events: 
   

    //In case of receiving successful information from the server            
    GuruTraff.instance.addEventListener(GuruTraffEvent.RECEIVE_MOTIVATED_REWARDS, receiveMotivatedRewards);
    //In case of error
    GuruTraff.instance.addEventListener(GuruTraffEvent.FAILED_REQUEST_MOTIVATED_REWARDS, failedRequestMotivatedRewards);

You must subscribe to events before requesting for rewards 

`GuruTraff.requestMotivatedRewards()`

A handler of the received information (receiveMotivatedRewards) gets an event `GuruTraffMotivatedRewardsEvent`, which contains a list of data "rewards" where each element contains:
 - rewardId - reward identifier 
 - appName - name of installed application
 - amountOfReward - amount of virtual currency that needs to be credited
   to a player    

After crediting the user, they need to be removed from the ad server. This can be done automatically if the `GuruTraff.autoRemoveMotivationRewards` field is set to true, then the SDK will send a request to the advertising server to remove the received rewards; if false, you need to manually call the method:

    GuruTraff.removeMotivatedRewards(rewardIdsToRemove),
where rewardIdsToRemove - list of reward identifiers, received by the listener receiveMotivatedRewards.
    
:exclamation: For the rewards to work, you must install AppUserId - user identifier on your server, before advertising request. If this is not done, user motivation will not work.
