---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 59cf0b81363c724d709d85105525bece13743f26
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474849"
---
#### <a name="applicationstabapplications"></a>[Uygulamalar](#tab/applications/)

1. **Uygulamalar**' ı seçin.
1. **Özellikler** sayfasını açmak için *webapi1* uygulamasını seçin.
1. **Yayımlanan kapsamları**seçin. Yayımlanan kapsamlar, bir istemci uygulamasına, Web API 'sine belirli izinler vermek için kullanılabilir.
1. **Kapsam**için `demo.read`girin ve **Açıklama**için `Read access to the web API`girin.
1. **Kapsam**için `demo.write`girin ve **Açıklama**için `Write access to the web API`girin.
1. **Kaydet**’i seçin.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Uygulama kayıtları (Önizleme)](#tab/app-reg-preview/)

1. **Uygulama kayıtları (Önizleme)** öğesini seçin.
1. **Genel bakış** sayfasını açmak için *webapi1* uygulamasını seçin.
1. **Yönet**altında **bir API 'yi kullanıma**sunma ' yı seçin.
1. **Uygulama kimliği URI 'sinin**yanındaki **Ayarla** bağlantısını seçin.
1. Varsayılan değeri (bir GUID) `api`değiştirin ve ardından **Kaydet**' i seçin. Tam URI gösterilir ve `https://your-tenant-name.onmicrosoft.com/api`biçiminde olmalıdır. Web uygulamanız API için bir erişim belirteci istediğinde, bu URI 'yi API için tanımladığınız her bir kapsamın ön eki olarak eklemesi gerekir.
1. **Bu API tarafından tanımlanan kapsamlar**altında **Kapsam Ekle**' yi seçin.
1. API 'ye okuma erişimini tanımlayan bir kapsam oluşturmak için aşağıdaki değerleri girin, ardından **Kapsam Ekle**' yi seçin:
    1. **Kapsam adı**: `demo.read`
    1. **Yönetici onayı görünen adı**: `Read access to demo API`
    1. **Yönetici onay açıklaması**: `Allows read access to the demo API`
1. **Kapsam Ekle**' yi SEÇIN, API 'ye yazma erişimini tanımlayan bir kapsam eklemek için aşağıdaki değerleri girin ve ardından **Kapsam Ekle**' yi seçin:
    1. **Kapsam adı**: `demo.write`
    1. **Yönetici onayı görünen adı**: `Write access to demo API`
    1. **Yönetici onay açıklaması**: `Allows write access to the demo API`