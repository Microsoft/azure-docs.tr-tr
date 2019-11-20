---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: ce49e9e26160017d64d745c9c7ad5402bc3ae2ca
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641544"
---
#### <a name="applicationstabapplications"></a>[Uygulamalar](#tab/applications/)

1. **Uygulamalar**' ı seçin ve ardından API erişimi olması gereken Web uygulamasını seçin. Örneğin, *WebApp1*.
1. **API erişimi**' ni seçin ve ardından **Ekle**' yi seçin.
1. **API Seç** açılan menüsünde, Web uygulamasına erişim IZNI verilecek API 'yi seçin. Örneğin, *webapi1*.
1. **Kapsamları Seç** açılan menüsünde, daha önce tanımladığınız kapsamları seçin. Örneğin, *demo. Read* ve *demo. Write*.
1. **Tamam**’ı seçin.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Uygulama kayıtları (Önizleme)](#tab/app-reg-preview/)

1. **Uygulama kayıtları (Önizleme)** öğesini seçin ve ardından API 'ye erişimi olması gereken Web uygulamasını seçin. Örneğin, *WebApp1*.
1. **Yönet**altında **API izinleri**' ni seçin.
1. **Yapılandırılan izinler**altında **izin Ekle**' yi seçin.
1. **API 'Lerim** sekmesini seçin.
1. Web uygulamasına erişim verilmesi gereken API 'yi seçin. Örneğin, *webapi1*.
1. **İzin**altında **demo**' i genişletin ve daha önce tanımladığınız kapsamları seçin. Örneğin, *demo. Read* ve *demo. Write*.
1. **Izin Ekle**' yi seçin. Yönlendirildiğinden, bir sonraki adıma geçmeden önce birkaç dakika bekleyin.
1. **Yönetici onayı ver ' i (kiracı adınız)** seçin.
1. Şu anda oturum açmış olan yönetici hesabınızı seçin veya Azure AD B2C kiracınızda, en azından *bulut uygulama Yöneticisi* rolüne atanan bir hesapla oturum açın.
1. **Kabul Et**’i seçin.
1. **Yenile**' yi seçin ve ardından "verilen..." öğesini doğrulayın Her iki kapsam için **durum** altında görünür. İzinlerin yayılması birkaç dakika sürebilir.