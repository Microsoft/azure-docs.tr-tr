---
title: Internet Explorer sorunları (MSAL.js) | Mavisi
titleSuffix: Microsoft identity platform
description: Internet Explorer tarayıcısı ile JavaScript için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL.js) kullanın.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 73dd7877b399d34a009c9fe479550eb1bf6dc0f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99583985"
---
# <a name="known-issues-on-internet-explorer-browsers-msaljs"></a>Internet Explorer tarayıcılarındaki bilinen sorunlar (MSAL.js)

JavaScript için Microsoft kimlik doğrulama kitaplığı (MSAL.js), Internet Explorer 'da çalıştırılabilmesi için [JAVASCRIPT ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) için oluşturulur. Ancak bilmemiz gereken birkaç nokta vardır.

## <a name="run-an-app-in-internet-explorer"></a>Internet Explorer 'da uygulama çalıştırma
Internet Explorer 'da çalışabilecek uygulamalarda MSAL.js kullanmayı düşünüyorsanız, MSAL.js betiğine başvurmadan önce Promise polyfill öğesine bir başvuru eklemeniz gerekir.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Bunun nedeni, Internet Explorer 'ın yerel olarak bulunan JavaScript 'ı desteklemesidir.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Internet Explorer 'da çalışan bir uygulamada hata ayıklama

### <a name="running-in-production"></a>Üretimde çalıştırma
Uygulamanızı üretime dağıtmak (Azure Web Apps 'te olduğu gibi) normalde ince çalışır ve bu da Son Kullanıcı açılan pencereleri kabul etmiş olarak sunulur. Internet Explorer 11 ile test ettik.

### <a name="running-locally"></a>Yerel olarak çalışıyor
Uygulamanızı Internet Explorer 'da çalışan yerel olarak çalıştırmak ve hata ayıklamak istiyorsanız, aşağıdaki noktalara dikkat edin (uygulamanızı farklı çalıştırmak istediğinizi varsayın *http://localhost:1234* ):

- Internet Explorer 'ın, MSAL.js düzgün çalışmasını engelleyen "korumalı mod" adlı bir güvenlik mekanizması vardır. Belirtiler arasında, oturum açtıktan sonra, sayfa öğesine yeniden yönlendirilebilir http://localhost:1234/null .

- Uygulamanızı yerel olarak çalıştırmak ve hatalarını ayıklamak için, bu "korumalı mod" seçeneğini devre dışı bırakmanız gerekir. Bunun için:

    1. Internet Explorer **araçları** (dişli simgesi) seçeneğine tıklayın.
    1. **Internet seçenekleri** ' ni ve ardından **güvenlik** sekmesini seçin.
    1. **Internet** bölgesine tıklayın ve **korumalı modu etkinleştir (Internet Explorer 'ın yeniden başlatılmasını gerektirir)** seçeneğinin işaretini kaldırın. Internet Explorer, bilgisayarınızın artık korunmuyor olduğunu uyarır. **Tamam**'a tıklayın.
    1. Internet Explorer 'ı yeniden başlatın.
    1. Uygulamanızı çalıştırın ve hata ayıklayın.

İşiniz bittiğinde Internet Explorer güvenlik ayarlarını geri yükleyin.  **Ayarlar**  ->  **Internet seçenekleri**  ->  **güvenlik**  ->  **tüm bölgeleri varsayılan düzeye sıfırla**' yı seçin.

## <a name="next-steps"></a>Sonraki adımlar
[Internet Explorer 'da MSAL.js kullanırken bilinen sorunlar](msal-js-use-ie-browser.md)hakkında daha fazla bilgi edinin.