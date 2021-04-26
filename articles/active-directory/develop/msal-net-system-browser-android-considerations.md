---
title: Xamarin Android Sistem tarayıcısı konuları (MSAL.NET) | Mavisi
titleSuffix: Microsoft identity platform
description: .NET için Microsoft kimlik doğrulama kitaplığı (MSAL.NET) ile Xamarin Android üzerinde sistem tarayıcıları kullanmayla ilgili önemli noktalar hakkında bilgi edinin.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 4230a194fb18587a209c100a39b0924e6170502d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98063476"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>MSAL.NET kullanmaya yönelik Xamarin Android Sistem tarayıcısı konuları

Bu makalede, .NET için Microsoft kimlik doğrulama kitaplığı (MSAL.NET) ile Xamarin Android üzerinde sistem tarayıcısını kullandığınızda göz önünde bulundurmanız gerekenler açıklanmaktadır.

MSAL.NET 2.4.0 Preview sürümünden itibaren, MSAL.NET Chrome dışındaki tarayıcıları destekler. Artık, kimlik doğrulaması için Android cihazında Chrome 'un yüklü olmasını gerektirmez.

Özel sekmeleri destekleyen tarayıcılar kullanmanızı öneririz. Bu tarayıcıların bazı örnekleri aşağıda verilmiştir:

| Özel sekmeler desteği olan tarayıcılar | Paket adı |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com. Microsoft. emmx|
|Firefox | org. Mozilla. Firefox|
|Eko | com. ekosı. Android|
|Kivi | com. kiwibrowser. Browser|
|Brave | com. brave. Browser|

Sınamamız, özel sekme desteği sunan tarayıcıları tanımlamaya ek olarak, özel sekmeleri desteklemeyen birkaç tarayıcının kimlik doğrulaması için de çalıştığını gösterir. Bu tarayıcılarda Opera, Opera Mini, InBrowser ve Maxthon bulunur. 

## <a name="tested-devices-and-browsers"></a>Test edilen cihazlar ve tarayıcılar
Aşağıdaki tabloda, kimlik doğrulama uyumluluğu için test edilmiş cihazlar ve tarayıcılar listelenmektedir.

| Cihaz | Tarayıcı     |  Sonuç  | 
| ------------- |:-------------:|:-----:|
| Huawei/One + | Chrome\* | Başarılı|
| Huawei/One + | Edge\* | Başarılı|
| Huawei/One + | Firefox\* | Başarılı|
| Huawei/One + | Brave\* | Başarılı|
| Tek + | Eko\* | Başarılı|
| Tek + | Kivi\* | Başarılı|
| Huawei/One + | Opera | Başarılı|
| Huawei | İşletimsel mini | Başarılı|
| Huawei/One + | InBrowser | Başarılı|
| Tek + | Maxthon | Başarılı|
| Huawei/One + | DuckDuckGo | Kullanıcı kimlik doğrulamasını iptal etti|
| Huawei/One + | UC tarayıcısı | Kullanıcı kimlik doğrulamasını iptal etti|
| Tek + | Dolphin | Kullanıcı kimlik doğrulamasını iptal etti|
| Tek + | CM tarayıcısı | Kullanıcı kimlik doğrulamasını iptal etti|
| Huawei/One + | Hiçbiri yüklü değil | AndroidActivityNotFound özel durumu|

\* Özel sekmeleri destekler

## <a name="known-issues"></a>Bilinen sorunlar

Kullanıcı cihazda hiç tarayıcı etkinleştirilmemişse, MSAL.NET bir `AndroidActivityNotFound` özel durum oluşturur.  
  - **Risk azaltma**: kullanıcıdan cihazındaki bir tarayıcıyı etkinleştirmesini isteyin. Özel sekmeleri destekleyen bir tarayıcı önerin.

Kimlik doğrulaması başarısız olursa (örneğin, kimlik doğrulaması DuckDuckGo ile başlatırsa), MSAL.NET döndürülür `AuthenticationCanceled MsalClientException` . 
  - **Kök sorun**: cihazda özel sekmeleri destekleyen bir tarayıcı etkinleştirilmemiş. Kimlik doğrulaması, kimlik doğrulamasını tamamlayamadık bir tarayıcıyla başlatıldı. 
  - **Risk azaltma**: kullanıcıdan cihazındaki bir tarayıcıyı etkinleştirmesini isteyin. Özel sekmeleri destekleyen bir tarayıcı önerin.

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla bilgi ve kod örneği için bkz. Xamarin Android ve [Embedded sistem Web Kullanıcı arabiriminde](msal-net-web-browsers.md#embedded-vs-system-web-ui)bir [Katıştırılmış Web tarayıcısı ve bir sistem tarayıcısı arasında seçim yapma](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) .  