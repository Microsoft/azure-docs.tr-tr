---
title: Özel Görüntü İşleme projelerini Kopyala ve taşı
titleSuffix: Azure Cognitive Services
description: Özel Görüntü İşleme projelerinizi kopyalamak ve taşımak için ExportProject ve ımportproject API 'Lerini nasıl kullanacağınızı öğrenin.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/08/2020
ms.author: pafarley
ms.openlocfilehash: 1def60843df55a80e8b7d28d7eb61e262a560111
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044167"
---
# <a name="copy-and-move-your-custom-vision-projects"></a>Özel Görüntü İşleme projelerinizi kopyalayın ve taşıyın

Bir Özel Görüntü İşleme projesi oluşturup eğitimli olduktan sonra, projenizi başka bir kaynağa kopyalamak isteyebilirsiniz. Örneğin, bir projeyi geliştirmeden üretim ortamına taşımak veya daha fazla veri güvenliği için bir projeyi farklı bir Azure bölgesindeki bir hesaba yedeklemek isteyebilirsiniz.

**[Exportproject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** ve **[ımportproject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** API 'leri, projeleri bir özel görüntü işleme hesabından başkalarına kopyalamanızı sağlayarak bu senaryoyu etkinleştirir. Bu kılavuz, bu REST API 'Lerini kıvrımlı ile nasıl kullanacağınızı gösterir. İstekleri vermek için Postman gibi bir HTTP isteği hizmeti de kullanabilirsiniz.

## <a name="business-scenarios"></a>İş senaryoları

Uygulamanız veya işletmeniz Özel Görüntü İşleme bir proje kullanımına bağımlıysa, modelinizi başka bir bölgedeki başka bir Özel Görüntü İşleme hesabına kopyalamanızı öneririz. Daha sonra bölgesel bir kesinti oluşursa, projenize kopyalandığı bölgede erişebilirsiniz.

##  <a name="prerequisites"></a>Önkoşullar

- İki Azure Özel Görüntü İşleme kaynağı. Bunlar yoksa, Azure portal gidin ve [Yeni bir özel görüntü işleme kaynağı oluşturun](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true).
- Özel Görüntü İşleme kaynaklarınızın eğitim tuşları ve uç nokta URL 'Leri. Bu değerleri, Azure portal kaynağın **genel bakış** sekmesinde bulabilirsiniz.
- Oluşturulmuş bir Özel Görüntü İşleme projesi. Bunu nasıl yapacağınız hakkında yönergeler için bkz. [sınıflandırıcı oluşturma](./getting-started-build-a-classifier.md) .
* [PowerShell sürüm 6.0 +](/powershell/scripting/install/installing-powershell-core-on-windows)veya benzer bir komut satırı yardımcı programı.

## <a name="process-overview"></a>İşleme genel bakış

Bir projeyi kopyalama işlemi aşağıdaki adımlardan oluşur:

1. İlk olarak, kopyalamak istediğiniz kaynak hesabınızda projenin KIMLIĞINI alırsınız.
1. Ardından, kaynak hesabınızın proje KIMLIĞI ve eğitim anahtarını kullanarak **exportproject** API 'sini çağırabilirsiniz. Geçici bir belirteç dizesi alacaksınız.
1. Ardından, hedef hesabınızın belirteç dizesini ve eğitim anahtarını kullanarak **ımportproject** API 'sini çağırabilirsiniz. Proje daha sonra hedef hesabınız altında listelenecektir.

## <a name="get-the-project-id"></a>Proje KIMLIĞINI al

İlk olarak, var olan Özel Görüntü İşleme projelerinizi ve bunların kimliklerini görmek için **[Getprojects](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddead)** 'i çağırın. Kaynak hesabınızın eğitim anahtarını ve uç noktasını kullanın.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects"
-H "Training-key: {training key}"
```

Bir `200\OK` Proje listesini ve gövdesinde bulunan meta verilerini içeren bir yanıt alırsınız. `"id"`Değer, sonraki adımlar için kopyalanacak dizedir.

```json
[
  {
    "id": "00000000-0000-0000-0000-000000000000",
    "name": "string",
    "description": "string",
    "settings": {
      "domainId": "00000000-0000-0000-0000-000000000000",
      "classificationType": "Multiclass",
      "targetExportPlatforms": [
        "CoreML"
      ],
      "useNegativeSet": true,
      "detectionParameters": "string",
      "imageProcessingSettings": {
        "augmentationMethods": {}
      }
    },
    "created": "string",
    "lastModified": "string",
    "thumbnailUri": "string",
    "drModeEnabled": true,
    "status": "Succeeded"
  }
]
```

## <a name="export-the-project"></a>Projeyi dışarı aktarma

Proje KIMLIĞI ve kaynak eğitim anahtarınızı ve uç noktasını kullanarak **[Exportproject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** 'i çağırın.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects/{projectId}/export"
-H "Training-key: {training key}"
```

`200/OK`, Dışarıya aktarılmış proje ve bir başvuru dizesi hakkında meta veriler içeren bir yanıt alırsınız `"token"` . Belirtecin değerini kopyalayın.

```json
{
  "iterationCount": 0,
  "imageCount": 0,
  "tagCount": 0,
  "regionCount": 0,
  "estimatedImportTimeInMS": 0,
  "token": "string"
}
```

## <a name="import-the-project"></a>Projeyi içeri aktar

Hedef eğitim anahtarınızı ve uç noktasını kullanarak, başvuru belirteciyle birlikte **[ımportproject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** 'i çağırın. Ayrıca, projenize yeni hesapta bir ad verebilirsiniz.

```curl
curl -v -G -X POST "{endpoint}/customvision/v3.3/Training/projects/import"
--data-urlencode "token={token}" --data-urlencode "name={name}"
-H "Training-key: {training key}" -H "Content-Length: 0"
```

`200/OK`Yeni içeri aktarılan projeniz hakkında meta veriler içeren bir yanıt alırsınız.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda, bir projeyi Özel Görüntü İşleme kaynakları arasında kopyalamayı ve taşımayı öğrendiniz. Daha sonra, Özel Görüntü İşleme ile yapabileceklerinizi görmek için API başvuru belgelerini inceleyin.
* [REST API başvuru belgeleri](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)