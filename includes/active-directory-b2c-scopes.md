---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: bb004afe6cdd6992f742877318426052d1bead6d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106073526"
---
#### <a name="app-registrations"></a>[Uygulama kayıtları](#tab/app-reg-ga/) 

1. **Uygulama kayıtları**’nı seçin.
1. **Genel bakış** sayfasını açmak için *webapi1* uygulamasını seçin.
1. **Yönet** altında **bir API 'yi kullanıma** sunma ' yı seçin.
1. **Uygulama kimliği URI 'sinin** yanındaki **Ayarla** bağlantısını seçin.
1. Varsayılan değeri (bir GUID) ile değiştirin `api` ve ardından **Kaydet**' i seçin. Tam URI gösterilir ve biçiminde olmalıdır `https://your-tenant-name.onmicrosoft.com/api` . Web uygulamanız API için bir erişim belirteci istediğinde, bu URI 'yi API için tanımladığınız her bir kapsamın ön eki olarak eklemesi gerekir.
1. **Bu API tarafından tanımlanan kapsamlar** altında **Kapsam Ekle**' yi seçin.
1. API 'ye okuma erişimini tanımlayan bir kapsam oluşturmak için aşağıdaki değerleri girin, ardından **Kapsam Ekle**' yi seçin:
    1. **Kapsam adı**: `demo.read`
    1. **Yönetici onayı görünen adı**: `Read access to demo API`
    1. **Yönetici onay açıklaması**: `Allows read access to the demo API`
1. **Kapsam Ekle**' yi SEÇIN, API 'ye yazma erişimini tanımlayan bir kapsam eklemek için aşağıdaki değerleri girin ve ardından **Kapsam Ekle**' yi seçin:
    1. **Kapsam adı**: `demo.write`
    1. **Yönetici onayı görünen adı**: `Write access to demo API`
    1. **Yönetici onay açıklaması**: `Allows write access to the demo API`

#### <a name="applications-legacy"></a>[Uygulamalar (eski)](#tab/applications-legacy/)

1. **Uygulamalar (eski)** seçeneğini belirleyin.
1. **Özellikler** sayfasını açmak için *webapi1* uygulamasını seçin.
1. **Yayımlanan kapsamları** seçin. Yayımlanan kapsamlar, bir istemci uygulamasına, Web API 'sine belirli izinler vermek için kullanılabilir.
1. **Kapsam** için girin `demo.read` ve **Açıklama** için girin `Read access to the web API` .
1. **Kapsam** için girin `demo.write` ve **Açıklama** için girin `Write access to the web API` .
1. **Kaydet**’i seçin.