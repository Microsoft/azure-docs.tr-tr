---
title: Evrensel Windows Platformu konuları (.NET için Microsoft kimlik doğrulama kitaplığı)
titleSuffix: Microsoft identity platform
description: .NET için Microsoft kimlik doğrulama kitaplığı (MSAL.NET) ile Evrensel Windows Platformu kullanırken belirli hususlar hakkında bilgi edinin.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27a41ad084d21c7623011b5678fb84ed27d68325
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802669"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>MSAL.NET ile ilgili Evrensel Windows Platformu özel noktalar
UWP 'de, MSAL.NET kullanırken dikkate almanız gereken bazı noktalar vardır.

## <a name="the-usecorporatenetwork-property"></a>UseCorporateNetwork özelliği
WinRT platformunda, `PublicClientApplication` aşağıdaki Boolean özelliğine sahiptir ``UseCorporateNetwork``. Bu özellik, kullanıcı federe bir Azure AD kiracısında oturum açmışsa, Win 8.1 ve UWP uygulamalarının tümleşik Windows kimlik doğrulamasından (ve bu nedenle işletim sistemiyle oturum açmış kullanıcı ile oturum açanlar) faydalarına olanak sağlar. Bu özelliği ayarladığınızda, MSAL.NET WAB (Web kimlik doğrulama Aracısı) kullanır.

> [!IMPORTANT]
> Bu özelliğin true olarak ayarlanması, uygulama geliştiricisinin uygulamada tümleşik Windows kimlik doğrulamasını (ıWA) etkinleştirdiğinizi varsayar. Bunun için:
> - UWP uygulamanızın ``Package.appxmanifest``, **yetenekler** sekmesinde, aşağıdaki özellikleri etkinleştirin:
>   - Kurumsal kimlik doğrulama
>   - Özel ağlar (Istemci & sunucusu)
>   - Paylaşılan Kullanıcı sertifikası

Kurumsal kimlik doğrulaması veya paylaşılan Kullanıcı sertifikaları yeteneklerini talep eden uygulamalar, Windows Mağazası 'na kabul edilmesi için daha yüksek bir doğrulama düzeyi gerektirdiğinden ve tüm geliştiriciler daha fazla gerçekleştirmek istemeyeceğinden, varsayılan olarak ıWA etkinleştirilmemiştir doğrulama düzeyi.

UWP platformunda (WAB) temel alınan uygulama, Koşullu erişimin etkinleştirildiği kurumsal senaryolarda doğru şekilde çalışmaz. Bu belirti, kullanıcının Windows Hello ile oturum açmayı deneme ve bir sertifika seçmek için önerileceğinden, ancak:

- PIN sertifikası bulunamadı,
- ya da Kullanıcı onu seçer, ancak PIN için hiçbir istem istenmez.

Geçici çözüm, alternatif bir Yöntem (Kullanıcı adı/parola + telefon kimlik doğrulaması) kullanmaktır, ancak deneyim iyi değildir.

## <a name="troubleshooting"></a>Sorun giderme

Bazı müşteriler bazı belirli kurumsal ortamlarda aşağıdaki oturum açma hatası olduğunu raporladı:

```Text
We can't connect to the service you need right now. Check your network connection or try this again later
```

bir internet bağlantısı olduğunu ve bu kişiler ortak bir ağla birlikte çalışıp çalışmadığını öğrenirler.

Geçici bir çözüm olarak, WAB 'nin (temeldeki Windows bileşeni) özel ağa izin verdiğinden emin olun. Bir kayıt defteri anahtarı ayarlayarak bunu yapabilirsiniz:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Ayrıntılar için bkz. [Web kimlik doğrulama Aracısı-Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki örneklerde daha fazla ayrıntı verilmiştir:

Örnek | Platform | Açıklama 
|------ | -------- | -----------|
|[Active-Directory-DotNet-Native-UWP-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Azure AD v 2.0 uç noktası ile kimlik doğrulaması yapan bir kullanıcı için Microsoft Graph erişen, msal.net kullanan Evrensel Windows Platformu istemci uygulaması. <br>![Topoloji](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Özel bir Xamarin Forms uygulaması, MSAL ve Azure AD 'nin AAD v 2.0 uç noktası aracılığıyla kimliğini doğrulamak ve elde edilen belirteçle Microsoft Graph erişmek için nasıl kullanılacağını gösterir. <br>![Topoloji](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
