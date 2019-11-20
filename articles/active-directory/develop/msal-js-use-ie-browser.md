---
title: Internet Explorer 'ı kullanma (JavaScript için Microsoft kimlik doğrulama kitaplığı)
titleSuffix: Microsoft identity platform
description: JavaScript için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL. js) Internet Explorer tarayıcısı ile kullanma hakkında bilgi edinin.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15502217edc6f3fd723076eda78d06fcf090aa49
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150498"
---
# <a name="use-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>MSAL. js ile Internet Explorer ve Microsoft Edge tarayıcılarını kullanma

JavaScript için Microsoft kimlik doğrulama kitaplığı (MSAL. js), Internet Explorer 'da çalışabilecek şekilde [JAVASCRIPT ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) için oluşturulur. Ancak bilmemiz gereken birkaç nokta vardır.

## <a name="run-an-app-in-internet-explorer"></a>Internet Explorer 'da uygulama çalıştırma
Internet Explorer 'da çalışabilecek uygulamalarda MSAL. js kullanmayı düşünüyorsanız, MSAL. js betiğine başvurmadan önce Promise polyfill öğesine bir başvuru eklemeniz gerekir.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Bunun nedeni, Internet Explorer 'ın yerel olarak bulunan JavaScript 'ı desteklemesidir.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Internet Explorer 'da çalışan bir uygulamada hata ayıklama

### <a name="running-in-production"></a>Üretimde çalıştırma
Uygulamanızı üretime dağıtmak (Azure Web Apps 'te olduğu gibi) normalde ince çalışır ve bu da Son Kullanıcı açılan pencereleri kabul etmiş olarak sunulur. Internet Explorer 11 ile test ettik.

### <a name="running-locally"></a>Yerel olarak çalışıyor
Uygulamanızı Internet Explorer 'da çalışan yerel olarak çalıştırmak ve hata ayıklamak istiyorsanız, aşağıdaki noktalara dikkat etmeniz gerekir (uygulamanızı *http://localhost:1234* olarak çalıştırmak istediğinizi varsayın):

- Internet Explorer 'Da, MSAL. js ' nin düzgün çalışmasını engelleyen "korumalı mod" adlı bir güvenlik mekanizması vardır. Belirtiler arasında, oturum açtıktan sonra, sayfa http://localhost:1234/null yeniden yönlendirilebilir.

- Uygulamanızı yerel olarak çalıştırmak ve hatalarını ayıklamak için, bu "korumalı mod" seçeneğini devre dışı bırakmanız gerekir. Bunun için:

    1. Internet Explorer **araçları** (dişli simgesi) seçeneğine tıklayın.
    1. **Internet seçenekleri** ' ni ve ardından **güvenlik** sekmesini seçin.
    1. **Internet** bölgesine tıklayın ve **korumalı modu etkinleştir (Internet Explorer 'ın yeniden başlatılmasını gerektirir)** seçeneğinin işaretini kaldırın. Internet Explorer, bilgisayarınızın artık korunmuyor olduğunu uyarır. **Tamam**’a tıklayın.
    1. Internet Explorer 'ı yeniden başlatın.
    1. Uygulamanızı çalıştırın ve hata ayıklayın.

İşiniz bittiğinde Internet Explorer güvenlik ayarlarını geri yükleyin.   -> **ayarları** seçin **Internet seçenekleri** -> **güvenlik** -> **tüm bölgeleri varsayılan düzeye sıfırlayın**.

## <a name="next-steps"></a>Sonraki adımlar
[Internet Explorer 'DA msal. js kullanırken bilinen sorunlar](msal-js-use-ie-browser.md)hakkında daha fazla bilgi edinin.