---
title: Internet Explorer ve Microsoft Edge 'de bilinen sorunlar (JavaScript için MSAL)
titleSuffix: Microsoft identity platform
description: Internet Explorer ve Microsoft Edge tarayıcılarıyla JavaScript (MSAL. js) için Microsoft kimlik doğrulama kitaplığı 'nı kullanırken oluşan sorunları öğrenin.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7b4e24a02bcce372f020833a82b685748432c39
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934833"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>MSAL. js ile Internet Explorer ve Microsoft Edge tarayıcılarındaki bilinen sorunlar

## <a name="issues-due-to-security-zones"></a>Güvenlik bölgeleri nedeniyle oluşan sorunlar
IE ve Microsoft Edge 'de kimlik doğrulamasıyla ilgili birçok sorun raporu sunuyoruz ( *Microsoft Edge tarayıcı sürümü 40.15063.0.0 ' e*güncelliyoruz). Bunları izliyor ve Microsoft Edge ekibinin bilgilendirilmesi yaptık. Microsoft Edge bir çözünürlükte çalışırken, sık karşılaşılan sorunların ve uygulanabilecek olası geçici çözümlerin açıklaması aşağıda verilmiştir.

### <a name="cause"></a>Nedeni
Bu sorunların büyük bir kısmının nedeni aşağıdaki gibidir. Oturum depolama ve yerel depolama, Microsoft Edge tarayıcısında güvenlik bölgelerine göre bölümlendirilir. Microsoft Edge 'in bu sürümünde, uygulama bölgeler arasında yeniden yönlendirildiğinde, oturum depolama ve yerel depolama temizlenir. Özellikle, oturum depolama, normal tarayıcı gezinmede temizlenir ve hem oturum hem de yerel depolama, tarayıcının InPrivate modunda temizlenir. MSAL. js, oturum depolama alanındaki belirli durumları kaydeder ve kimlik doğrulama akışları sırasında bu durumu denetlemeye dayanır. Oturum depolama alanı kaldırıldığında, bu durum kaybedilir ve bu nedenle bozuk deneyimler oluşur.

### <a name="issues"></a>Sorunlar

- **Kimlik doğrulama sırasında sonsuz yeniden yönlendirme döngüleri ve sayfa yeniden yükler**. Kullanıcılar Microsoft Edge 'de uygulamada oturum açtıklarında, AAD oturum açma sayfasından geri yönlendirilir ve yinelenen sayfa yeniden yüklemeye neden olan sonsuz bir yeniden yönlendirme döngüsünde takılırlar. Bu, genellikle oturum depolama alanındaki bir `invalid_state` hatası ile birlikte sunulur.

- **Sonsuz belirteci alma döngüleri ve AADSTS50058 hatası**. Microsoft Edge üzerinde çalışan bir uygulama bir kaynak için belirteç almaya çalıştığında, uygulama, ağ izinizdeki AAD 'den aşağıdaki hata ile birlikte, belirteç alma çağrısının sonsuz bir döngüsüne takılmış olabilir:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- Açılan **pencere, kimlik doğrulaması Için açılan pencere aracılığıyla oturum açma kullanılırken kapanmaz veya takılmaz**. Microsoft Edge veya IE (InPrivate) üzerinde açılan pencere aracılığıyla kimlik doğrulaması yaparken, kimlik bilgilerini girdikten ve oturum açtıktan sonra, güvenlik bölgelerinde birden çok etki alanı gezinmede yer alıyorsa, MSAL. js tanıtıcıyı açılan pencere.  

    Microsoft Edge sorun İzleyicisi 'nde bu sorunların bağlantıları aşağıda verilmiştir:  
    - [Hata 13861050](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861050/)
    - [Hata 13861663](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861663/)

### <a name="update-fix-available-in-msaljs-023"></a>Güncelleştirme: MSAL. js 0.2.3 'de çözüm kullanılabilir
Kimlik doğrulama yönlendirme döngüsü sorunları için düzeltmeler [msal. js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)içinde yayımlanmıştır. Bu düzeltmeden faydalanmak için MSAL. js yapılandırmasında bayrak `storeAuthStateInCookie` etkinleştirin. Bu bayrak varsayılan olarak false değerine ayarlanır.

`storeAuthStateInCookie` bayrağı etkinleştirildiğinde, MSAL. js, kimlik doğrulama akışlarının doğrulanması için gereken istek durumunu depolamak üzere tarayıcı tanımlama bilgilerini kullanır.

> [!NOTE]
> Bu düzelme henüz msal-angular ve msal-AngularJS sarmalayıcıları için kullanılamaz. Bu düzeltilme, açılan pencereler ile ilgili sorunu gidermez.

Aşağıdaki geçici çözümleri kullanın.

#### <a name="other-workarounds"></a>Diğer geçici çözümler
Sorununuzun yalnızca Microsoft Edge tarayıcısının belirli bir sürümünde gerçekleştiğini ve bu geçici çözümleri benimseerek diğer tarayıcılarda çalışıp çalışmadığını test ettiğinizden emin olun.  
1. Bu sorunları çözmek için ilk adım olarak, uygulama etki alanının ve kimlik doğrulama akışının yeniden yönlendirmelerine dahil olan tüm diğer sitelerin, tarayıcının güvenlik ayarlarında güvenilen siteler olarak eklendiğinden emin olun, böylece aynı güvenlik bölgesine ait olmaları gerekir.
Bunu yapmak için şu adımları izleyin:
    - **Internet Explorer** 'ı açın ve sağ üst köşedeki **Ayarlar** ' a (dişli simgesi) tıklayın
    - **Internet seçeneklerini** belirleyin
    - **Güvenlik** sekmesini seçin
    - **Güvenilen siteler** seçeneğinin altında, **siteler** düğmesine tıklayın ve açılan iletişim kutusuna URL 'leri ekleyin.

2. Daha önce belirtildiği gibi, normal gezinme sırasında yalnızca oturum depolama alanı temizlendiğinden, bunun yerine MSAL. js ' yi yerel depolamayı kullanacak şekilde yapılandırabilirsiniz. Bu, MSAL başlatılırken `cacheLocation` config parametresi olarak ayarlanabilir.

Not, bu, hem oturum hem de yerel depolama olmadığından, InPrivate Gözatma sorununu çözmeyecektir.

## <a name="issues-due-to-popup-blockers"></a>Açılır engelleyiciler nedeniyle oluşan sorunlar

IE veya Microsoft Edge 'de açılan pencereler engellendiğinde, örneğin çok faktörlü kimlik doğrulaması sırasında ikinci bir açılan pencere oluştuğunda oluşan durumlar vardır. Tarayıcıda, açılan pencerede bir kez veya her zaman izin vermek için bir uyarı alırsınız. İzin vermeyi seçerseniz tarayıcı açılır pencereyi otomatik olarak açar ve bunun için bir `null` tutamacı döndürür. Sonuç olarak, kitaplığın pencere için bir tutamacı yoktur ve açılır pencereyi kapatmak için bir yol yoktur. Aynı sorun, otomatik olarak bir açılan pencere açmadığından açılanları izin vermenizi isterse Chrome 'da gerçekleşmez.

Geçici bir **çözüm**olarak, geliştiricilerin bu sorundan kaçınmak üzere uygulamalarını kullanmaya başlamadan önce IE ve Microsoft Edge 'de açılan pencerelere izin vermesi gerekir.

## <a name="next-steps"></a>Sonraki adımlar
[Internet Explorer 'DA msal. js kullanma](msal-js-use-ie-browser.md)hakkında daha fazla bilgi edinin.
