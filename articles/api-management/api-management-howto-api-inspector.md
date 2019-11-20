---
title: Azure API Yönetimi’nde istek izlemeyi kullanarak API’lerinizdeki hataları ayıklama | Microsoft Docs
description: Azure API Yönetimi’nde istek işleme adımlarını inceleme hakkında bilgi edinmek için bu öğreticinin adımlarını izleyin.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: b646c64b0ec45a11f99350ff5bd81a89418b2ecd
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072522"
---
# <a name="debug-your-apis-using-request-tracing"></a>İstek izleme özelliğini kullanarak API’lerinizdeki hataları ayıklama

Bu öğreticide, API’nizdeki hataları ayıklamanıza ve sorunları gidermenize yardımcı olması için istek işlemenin nasıl inceleneceği açıklanmaktadır. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Çağrı izleme

![API denetçisi](media/api-management-howto-api-inspector/api-inspector001.PNG)

## <a name="prerequisites"></a>Önkoşullar

+ [Azure API Management terminolojisini](api-management-terminology.md) öğrenin.
+ Aşağıdaki hızlı başlangıcı doldurun: [Azure API Management örneği oluşturun](get-started-create-service-instance.md).
+ Ayrıca, aşağıdaki öğreticiyi de tamamlayabilirsiniz: [Ilk API 'Nizi Içeri aktarın ve yayımlayın](import-and-publish.md).

## <a name="trace-a-call"></a>Çağrı izleme

![API izleme](media/api-management-howto-api-inspector/06-DebugYourAPIs-01-TraceCall.png)

1. **API’ler** seçeneğini belirleyin.
2. API listenizden **Tanıtım Konferansı API’sine** tıklayın.
3. **Test** sekmesine geçin.
4. **GetSpeakers** işlemini seçin.
5. Değer **true** olarak ayarlanmış şekilde, **Ocp-Apim-Trace** adlı bir HTTP üst bilgisini eklediğinizden emin olun.

    > [!NOTE]
    > Ocp-Apim-Subscription-Key otomatik olarak doldurulmazsa, Geliştirici Portalına gidip profil sayfasında anahtarları görüntüleyerek bu değeri alabilirsiniz.

6. API çağrısı yapmak için **"Gönder"** seçeneğine tıklayın. 
7. Çağrının tamamlanmasını bekleyin. 
8. **API konsolu**’nda **İzleme** sekmesine gidin. Ayrıntılı izleme bilgilerine atlamak için aşağıdaki bağlantılardan herhangi birine tıklayın: **gelen**, **arka uç**, **giden**.

    **Gelen** bölümünde, çağırandan alınan özgün istek API Yönetimini ve 2. adımda eklediğimiz hız sınırı ve üst bilgi ayarlama ilkeleri de dahil isteğe uygulanan tüm ilkeleri görürsünüz.

    **Arka uç** bölümünde, API arka ucuna gönderilen isteklerin API Yönetimini ve aldığı yanıtı görürsünüz.

    **Giden** bölümünde, çağırana geri gönderilmeden önce yanıta uygulanan tüm ilkeleri görürsünüz.

    > [!TIP]
    > Her bir adım, isteğin API Yönetimi tarafından alınmasından bu yana geçen süreyi de gösterir.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Çağrı izleme

Sonraki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
> [Düzeltmeleri kullanma](api-management-get-started-revise-api.md)
