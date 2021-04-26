---
title: Media Encoder Standard birlikte bir kaplama oluşturma
description: Media Encoder Standard bir kaplama oluşturmayı öğrenin.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 6d68f35cd7eff745a3d17009b65b1ed73a190173
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490259"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Media Encoder Standard birlikte bir kaplama oluşturma

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Encoder Standard resim, ses dosyası veya başka bir videonun başka bir video üzerinde yer kaplamasını sağlar. Girişin tam olarak bir dosya belirtmesi gerekir. JPG, PNG, GIF veya BMP biçiminde bir görüntü dosyası veya bir ses dosyası (örneğin, bir WAV, MP3, WMA veya M4A dosyası) veya bir video dosyası belirtebilirsiniz.


## <a name="prerequisites"></a>Önkoşullar

* Örnekteki dosya *appsettings.js* yapılandırmak için ihtiyacınız olan hesap bilgilerini toplayın. Bunu nasıl yapadığınıza emin değilseniz, bkz. [hızlı başlangıç: Microsoft Identity platformu ile uygulama kaydetme](../../active-directory/develop/quickstart-register-app.md). Dosyasında *appsettings.js* aşağıdaki değerler beklenmektedir.

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Location": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

Dönüşümleri henüz bilmiyorsanız, aşağıdaki etkinlikleri gerçekleştirmeniz önerilir:

* [Media Services Ile kodlama videosu ve ses](encode-concept.md) okuma
* [Özel bir Transform-.net Ile nasıl kodlanacağını](transform-custom-presets-how-to.md)okuyun. Dönüşümlerle çalışmak için gereken .NET ayarlamak üzere bu makaledeki adımları izleyin ve ardından bir yer paylaşımlı önceden tanımlanmış örneği denemek için buraya geri dönün.
* Bkz. [dönüşümler başvuru belgesi](/rest/api/media/transforms).

Dönüşümleri öğrendiğinizde, kaplamalar örneğini indirin.

## <a name="overlays-preset-sample"></a>Yer paylaşımları önceden ayarlanmış örnek

Yer paylaşımlarını kullanmaya başlamak için [Media-Services-kaplama örneğini](https://github.com/Azure-Samples/media-services-overlays) indirin.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
