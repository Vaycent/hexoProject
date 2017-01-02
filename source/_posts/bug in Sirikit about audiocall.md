---
title: Bug In Sirikit About AudioCall ?
date: 2016-08-31
tags: 
- iOS
- Siri
- VoIP
---


I have tested startAudioCall sirikit in myApp recentlly, but there's some problem in my app about sirikit audiocall, actually, I think it's the bugs arround Xcode8 beta6/iPhone iOS beta6
<!--more-->


## Links about my app problem
Vaycent_ST, [Apple Developer Forums](https://forums.developer.apple.com/message/173259#173259)

Vaycent, [StackOverFlow](http://stackoverflow.com/questions/39182936/sirikit-startaudiocall-fail)


## INStartAudioCallIntent code
```
- (void)resolveContactsForStartAudioCall:(INStartAudioCallIntent *)intent
                          withCompletion:(void (^)(NSArray<INPersonResolutionResult *> *resolutionResults))completion{
    NSArray<INPerson *> *recipients = intent.contacts;
    NSMutableArray<INPersonResolutionResult *> *resolutionResults = [NSMutableArray array];
    NSLog(@"Test recipients count:%s",recipients.count+"");
    if (recipients.count == 0) {
        completion(@[[INPersonResolutionResult needsValue]]);
        return;
    }else if(recipients.count==1){
        [resolutionResults addObject:[INPersonResolutionResult successWithResolvedPerson:recipients.firstObject]];
    }else if(recipients.count>1){
         [resolutionResults addObject:[INPersonResolutionResult disambiguationWithPeopleToDisambiguate:recipients]];
    }else{
        [resolutionResults addObject:[INPersonResolutionResult unsupported]];

    }
    completion(resolutionResults);
}
- (void)confirmStartAudioCall:(INStartAudioCallIntent *)intent
                   completion:(void (^)(INStartAudioCallIntentResponse *response))completion{
    
    NSUserActivity *userActivity = [[NSUserActivity alloc] initWithActivityType:NSStringFromClass([INStartAudioCallIntent class])];
    INStartAudioCallIntentResponse *response = [[INStartAudioCallIntentResponse alloc] initWithCode:INStartAudioCallIntentResponseCodeReady userActivity:userActivity];
    completion(response);
}
- (void)handleStartAudioCall:(INStartAudioCallIntent *)intent
                  completion:(void (^)(INStartAudioCallIntentResponse *response))completion{
    NSUserActivity *userActivity = [[NSUserActivity alloc] initWithActivityType:NSStringFromClass([INStartAudioCallIntent class])];
    
    INStartAudioCallIntentResponse *response = [[INStartAudioCallIntentResponse alloc] initWithCode:INStartAudioCallIntentResponseCodeContinueInApp userActivity:userActivity];
    completion(response);
}

```


## Problems in my app
1. Both first name and last name  
2. Only two INPerson maximum in intent.contacts
3. INPerson is a null object except the displayName


### Both first name and last name
If a person has both two name(first/last name) in my contacts, when I talk to Siri, no matter "Call firstName with myApp" or "Call firstname lastname with myApp", Siri will use the local phone call to call this guy.

### Only two INPerson maximum in intent.contacts
If I have more than two person with the same first name, when I talk to Siri with this first name, intent.recipients in resolveRecipientsForSendMessage function will return a NSArray<INPerson *> which only has two object in it.
  
For example:  
John, John Smith, John Doll in my phone contacts, when I talk to Siri:"Call John with myApp"

```
NSArray<INPerson *> *recipients = intent.contacts;  
NSLog(@"Test recipients count:%s",recipients.count+"");
```
I can get a array(NSArray\<INPerson *\>) with two object as above code  

### INPerson is a null object except the displayName
When I get the INPerson object, the phone number is null except the person contacts name, code as below

```
NSArray<INPerson *> *recipients = intent.contacts;
for(int i=0; i<contacts.count; i++){
	INPerson *person = [recipients objectAtIndex:i];
	NSString *name = person.displayName;
	NSString *num = person.personHandle.value;
}
```

 

