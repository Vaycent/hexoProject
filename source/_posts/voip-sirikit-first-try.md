---
title: iOS10 Sirikit VoIP First Try
tags: 
- iOS
- Siri
- VoIP
---

This is my first time to test sirikit since updated to iOS10. Actually, this is just a simple test since lacking of information about sirikit on iOS10.
<!--more-->



### Background
* IDE: Xcode 8 beta6
* Device: iPhone6 iOS10 beta6
* Language: Object-C

----

### Add IntentExtension
1. File -> New -> Target
2. Intents Extension
3. Selected "Include UI Extension"


-----


### Add VoIP Handler Protocol
1. intentExtension -> Info.plist -> NSExtension -> NSExtensionAttributes -> IntentsSupported -> intem -> INStartAudioCallIntent
2. IntentHandler.m -> extend INStartAudioCallIntentHandling

-----

### AudioCall Code
```object-c
- (void)resolveContactsForStartAudioCall:(INStartAudioCallIntent *)intent
                          withCompletion:(void (^)(NSArray<INPersonResolutionResult *> *resolutionResults))completion{
    NSLog(@"maytest resolveContactsForStartAudioCall");
    NSUserActivity *userActivity = [[NSUserActivity alloc] initWithActivityType:NSStringFromClass([INStartAudioCallIntent class])];
    INStartAudioCallIntentResponse *response = [[INStartAudioCallIntentResponse alloc] initWithCode:INStartAudioCallIntentResponseCodeReady userActivity:userActivity];
    completion(response);
    
}
- (void)confirmStartAudioCall:(INStartAudioCallIntent *)intent
                   completion:(void (^)(INStartAudioCallIntentResponse *response))completion{
    NSLog(@"maytest confirmStartAudioCall");
    NSLog(@"maytest response:%@",completion);
    
    NSUserActivity *userActivity = [[NSUserActivity alloc] initWithActivityType:NSStringFromClass([INStartAudioCallIntent class])];
    INStartAudioCallIntentResponse *response = [[INStartAudioCallIntentResponse alloc] initWithCode:INStartAudioCallIntentResponseCodeReady userActivity:userActivity];
    completion(response);
}
- (void)handleStartAudioCall:(INStartAudioCallIntent *)intent
                  completion:(void (^)(INStartAudioCallIntentResponse *response))completion{
    NSLog(@"maytest handleStartAudioCall");
    NSUserActivity *userActivity = [[NSUserActivity alloc] initWithActivityType:NSStringFromClass([INStartAudioCallIntent class])];
    
    INStartAudioCallIntentResponse *response = [[INStartAudioCallIntentResponse alloc] initWithCode:INStartAudioCallIntentResponseCodeContinueInApp userActivity:userActivity];
    completion(response);
}

```


------



### Siri Permission
Device -> Settings -> Siri -> App Support -> Turn on myApp permission


-----


### Test Sirikit On Device
Test siri on device as follow sample

* "Call XXX on myApp"
* "Call XXX using myAPP app"


More info: [SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html#//apple_ref/doc/uid/TP40016875-CH11-SW1)
