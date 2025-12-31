---
author: ["John Nguyen"]
title: "Disabling Ad tracking in Meta and Google Apps"
description: "A  tutorial on disabling ad tracking services through Meta and Google privacy center"
date: "2025-09-04"
type: post
translationKey: pagefind
draft: false
tags:  ["privacy"]
categories: ["tutorials"]
cover: '/images/metaprivacy.png'
---

![alt text](/images/metaprivacy.png)


# Advertising Tracking
You would be suprised on how much information is stored about you just from your activity on Meta apps like FaceBook and Instagram. This information includes advertisers you've interacted with, keywords on your interests, posts you've seen, and more. Fortunately, you can see which advertisers are using your data and disable tracking. The dangers of ad tracking is that companies can build profiles on you like your age, where you live, gender, race, etc. From this data, companies can create targeted advertisements where you are most vulnerable. For example, a company could offer discounts for vacations or events for someone going through a breakup. This can leave that individual vulnerable and emotionally manipulated. Maching learning algorithms could also be trained on data provided on your behavior to predict any classification on you, that could potentially include negative bias. Let's see what data Meta and Google already have.

# Exporting your Ad Data
Meta's account center lets you export to JSON and HTML the data that advertisers are using. Navigate to https://accountscenter.facebook.com/info_and_permissions and click 'Your information and permissions' in the left panel. Then click Export your informatioin and create a request. To my surprise, the advertisers_using_your_information file had 25000 lines. I also exported the ads_interest file which contained over 100 keywords relating to my interests such as Devops, Software, Japan, and even cities in my area. You might see this as a benefit for more personalized ads, however, I prefer not letting companies target me with this information. On google, you can go to https://takeout.google.com/ and select all the data Google has on you.

# Disabling Ad Tracking and Personalized Ads on Meta
From the same Meta account center page, select Ad preferences in the left panel. Click on the right Manage info tab. Here you will find a number of settings to disable. First you can check the Categories used to reach you and Audience-based advertising to see what advertisers are currently using your information and what categories relate to you. You can remove the categories listed.

## Block activity information to advertisers
1. Select Activity information from ad partners
2. Review the setting and select No, don't make my ads more relevant by using this information.

![alt text](/images/metauseinfo.png)

## Blocking ads from ad partners and Meta
1. Under 'Ads Shown outside of Meta', select 'Ads from ad partners'
2. Select 'Don't show me ads from ad partners' 

![alt text](/images/dontshowads.png)

3. Go back to the menu and select 'Ads about Meta'
4. Select 'Don't use my activity to show me ads about Meta'


# Disabling Personalized Ads on Google
Google makes this very easy by disabling personalized ads for your Google account with a single button. Simply go to ad settings on your Google account or click here: https://myadcenter.google.com/personalizationoff?ref=my-account&ref-media=WEB&hl=en. Then select the Off dropdown for Personalized Ads.

![alt text](/images/googleads.png)

You can also go to Data & privacy under myaccount.google.com. From there scroll down and turn off Personalized Ads as well as Search personalization.


# Further Steps
These steps should limit the information advertisers will be able to get on your profile and interactions. However, this doesn't necessarily remove the data they already have. Removing the data may require putting in a request with each advertiser. For now, if you want to feel even more safe, you can disable location services and set your mobile devices settings to ask your apps not to track your information. Unfortunately, the real solution to privacy may require stronger legislation protecting constumers and consumers alike from advertisers.