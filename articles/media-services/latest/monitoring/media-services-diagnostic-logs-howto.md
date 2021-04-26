---
title: Azure Izleyici aracılığıyla tanılama günlüklerini izleme
description: Bu makalede, Azure Izleyici aracılığıyla tanılama günlüklerinin nasıl yönlendirileceği ve görüntüleneceği gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/17/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 290b68b90deb669f70fa708e6c3bda83cb7187c0
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227798"
---
# <a name="monitor-media-services-diagnostic-logs"></a>Tanılama günlüklerini izleme Media Services

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

[Azure izleyici](/azure/azure-monitor/overview) , uygulamalarınızın nasıl çalıştığını anlamanıza yardımcı olan ölçümleri ve tanılama günlüklerini izlemenize olanak sağlar. Bu özelliğin ayrıntılı açıklaması ve Azure Media Services ölçümleri ve tanılama günlüklerini nasıl kullanmak istediğinizi görmek için bkz. [izleme Media Services ölçümleri ve tanılama günlükleri](monitor-media-services.md).

Bu makalede, verileri depolama hesabına yönlendirme ve sonra verileri görüntüleme işlemlerinin nasıl yapılacağı gösterilir.

## <a name="prerequisites"></a>Önkoşullar

- [Media Services hesabı oluşturun](../account-create-how-to.md).
- [İzleyici Media Services](monitor-media-services.md)gözden geçirin.

## <a name="route-data-to-the-storage-account-using-the-portal"></a>Portalı kullanarak verileri depolama hesabına yönlendirme

1. https://portal.azure.com adresinden Azure portalında oturum açın.
1. İçinde Media Services hesabınıza gidin ve **izleyici** altındaki **Tanılama ayarları** ' na tıklayın. Burada, aboneliğinizde Azure İzleyici ile izleme verileri oluşturan tüm kaynakların bir listesini görürsünüz.

    ![Izleme bölümü altındaki tanılama ayarlarını vurgulayan ekran görüntüsü.](../media/media-services-diagnostic-logs/logs01.png)

1. **Tanılama ayarı Ekle**' ye tıklayın.

   Kaynak tanılama ayarı, belirli bir kaynaktan *hangi* izleme verilerinin yönlendirilmesi gerektiğine ve bu izleme verilerinin *nereye* gideceğine ilişkin bir tanımdır.

1. Görüntülenen bölümde, ayarınıza bir **ad** verin ve **Bir depolama hesabında arşivle** kutusunu işaretleyin.

    Günlükleri göndermek istediğiniz depolama hesabını seçin ve **Tamam**' a basın.
1. **Günlük** ve **Ölçüm** altındaki tüm kutuları işaretleyin. Kaynak türüne bağlı olarak, bu seçeneklerden yalnızca birini kullanabilirsiniz. Bu onay kutuları, seçtiğiniz hedefe (bu örnekte bir depolama hesabına) ilgili kaynak türü için kullanılabilen günlük ve ölçüm verileri kategorilerinden hangilerinin gönderildiğini denetler.

   ![Tanılama ayarları bölümü](../media/media-services-diagnostic-logs/logs02.png)
1. **Bekletme (gün)** kaydırıcısını 30’a ayarlayın. Bu kaydırıcı, depolama hesabında izleme verilerinin tutulacağı gün sayısını ayarlar. Azure İzleyici, belirtilen gün sayısından daha eski verileri otomatik olarak siler. Bekletme günü sayısının sıfır olması verileri süresiz olarak depolar.
1. **Kaydet**’e tıklayın.

Kaynağınızdaki izleme verileri artık depolama hesabına akar.

## <a name="route-data-to-the-storage-account-using-the-azure-cli"></a>Azure CLı kullanarak verileri depolama hesabına yönlendirme

Bir depolama hesabında tanılama günlüklerinin depolanmasını etkinleştirmek için aşağıdaki `az monitor diagnostic-settings` Azure CLI komutunu çalıştırın:

```azurecli-interactive
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Örnek:

```azurecli-interactive
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforams  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount" \
    --resource-group "amsResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="view-data-in-the-storage-account-using-the-portal"></a>Portalı kullanarak depolama hesabındaki verileri görüntüleme

Yukarıdaki adımları izlediyseniz, veriler depolama hesabınıza akmaya başlamıştır.

Olayın depolama hesabında görünmesi için beş dakikaya kadar beklemeniz gerekebilir.

1. Portalda, sol gezinti çubuğundaki **Depolama Hesapları** bölümüne gidin.
1. Önceki bölümde oluşturduğunuz depolama hesabını belirleyin ve tıklayın.
1. **Bloblar**' a ve ardından **Öngörüler-logs-keydeliveryrequests** etiketli kapsayıcıda öğesine tıklayın. Bu, günlüklerinizin bulunduğu kapsayıcıdır. İzleme verileri, kaynak KIMLIĞI ve sonra tarih ve saate göre kapsayıcılara ayrılır.
1. Kaynak kimliği, tarih ve saat için kapsayıcılara tıklayarak PT1H.json dosyasına gidin. PT1H.json dosyasına ve **İndir**’e tıklayın.

 Artık depolama hesabında depolanmış JSON olayını görüntüleyebilirsiniz.

### <a name="examples-of-pt1hjson"></a>PT1H.jsörnekleri

#### <a name="clear-key-delivery-log"></a>Anahtar teslim günlüğünü temizle

```json
{
  "time": "2019-05-21T00:07:33.2820450Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 253,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "e4276e1d-c012-40b1-80d0-ac15808b9277",
      "nbf": "1558396914",
      "exp": "1558400814",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "fb5c2b3a-bffa-4434-9c6f-73d689649add",
    "keyType": "Clear",
    "keyId": "e4276e1d-c012-40b1-80d0-ac15808b9277",
    "policyName": "SharedContentKeyPolicyUsedByAllAssets",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

#### <a name="widevine-encrypted-key-delivery-log"></a>Wdevine şifreli anahtar teslim günlüğü

```json
{
  "time": "2019-05-20T23:15:22.7088747Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 69,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
      "nbf": "1558392430",
      "exp": "1558396330",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "49613dd2-16aa-4595-a6e0-4e68beae6d37",
    "keyType": "Widevine",
    "keyId": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
    "policyName": "DRMContentKeyPolicy",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

## <a name="additional-notes"></a>Ek notlar

* Widevine, Google Inc. tarafından sunulan bir hizmettir ve Google, Inc 'nin hizmet koşullarına ve gizlilik Ilkesine tabidir.

## <a name="see-also"></a>Ayrıca bkz.

* [Azure İzleyici Ölçümleri](/azure/azure-monitor/data-platform)
* [Azure Izleyici tanılama günlükleri](/azure/azure-monitor/essentials/platform-logs-overview)
* [Azure kaynaklarınızdan günlük verilerini toplama ve kullanma](/azure/azure-monitor/essentials/platform-logs-overview)

## <a name="next-steps"></a>Sonraki adımlar

[İzleyici ölçümleri](media-services-metrics-howto.md)
