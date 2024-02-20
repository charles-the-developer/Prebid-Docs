---
layout: page_v2
title: Prebid SDK Utilities - iOS & Android
description: Utilities used in conjunction with the Prebid SDK
top_nav_section: prebid-mobile
nav_section: prebid-mobile
sidebarType: 2
---

# Prebid SDK Utility functions
{:.no_toc}

This section documents the utilities that can used in conjunction with the Prebid SDK.

* TOC
{:toc}

## Find Prebid Creative Size

You can use the `findPrebidCreativeSize` function can be used to address a bug in the Google Ad Manager ad server (described [here](https://groups.google.com/forum/?utm_medium=email&utm_source=footer#!category-topic/google-admob-ads-sdk/ios/648jzAP2EQY)) where sometimes ads fail to render.

We recommend that when you integrate with Google Ad Manager, you resize all ads served based on the winning Prebid creative size `findPrebidCreativeSize`. The Prebid SDK uses [`onAdLoaded``](https://developers.google.com/android/reference/com/google/android/gms/ads/AdListener.html#onAdLoaded()) (when an ad is received in Android) or the [`adViewDidReceiveAd` event](https://developers.google.com/admob/ios/banner) (when an ad is received in iOS) to determine how to resize the ad slot.

{% include alerts/alert_note.html content="`findPrebidCreativeSize` is supported on Android API versions 19+. Using on earlier versions is safe to use, however the resizing would not function." %}

The following code snippet demonstrates how to use the `findPrebidCreativeSize` function:

{% capture android %}
  ```java
  adView.adListener = object : AdListener() {
    override fun onAdLoaded() {
        super.onAdLoaded()

        // Determine the kind of winning line item
        AdViewUtils.findPrebidCreativeSize(adView, object : AdViewUtils.PbFindSizeListener {
            override fun success(width: Int, height: Int) {

                // In the case of Prebid's line item - resize te ad view
                adView.setAdSizes(AdSize(width, height))
            }

            override fun failure(error: PbFindSizeError) {}
        })
    }
}
  ```
{% endcapture %}
{% capture ios %}
  ```swift
  func bannerViewDidReceiveAd(_ bannerView: GADBannerView) {

    // Determine the kind of winning line item
    AdViewUtils.findPrebidCreativeSize(bannerView, success: { size in
        guard let bannerView = bannerView as? GAMBannerView else { return }
        
        // In the case of Prebid's line item - resize te ad view
        bannerView.resize(GADAdSizeFromCGSize(size))
    }, failure: { (error) in
        PrebidDemoLogger.shared.error("Error occured during searching for Prebid creative size: \(error)")
    })
}
  ```
{% endcapture %}

{% include code/mobile-sdk.html id="find-prebid-creative-size" kotlin=android swift=ios %}