---
title: Özel bir model - Form tanıyıcı için bir eğitim veri kümesi oluşturma
titleSuffix: Azure Cognitive Services
description: Bir Form tanıyıcı modeli eğitmek için eğitim veri kümeniz iyileştirilmesini sağlamak öğrenin.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 643f0d6dd3ee073bd19f8697346689523032ad9f
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592638"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Özel bir model için bir eğitim veri kümesi oluşturma

Form tanıyıcı özel modeli kullandığınız zaman için sektöre özel formlarınızı modeli eğitmek kendi eğitim verilerini sağlar. İki tür doldurulmuş bir model ile doldurulmuş beş forms veya (dosya adı "boş" sözcüğünü içermelidir) boş bir form eğitebilirsiniz. Eğitim veri kümeniz için boş bir form ekleme ile eğitmek için yeterli doldurulmuş forms olsa bile, modelin doğruluğunu geliştirebilir.

## <a name="training-data-tips"></a>Eğitim veri ipuçları

Eğitim için optimize edilmiş bir veri kümesini kullanmak önemlidir. Kullanım emin olmak için aşağıdaki ipuçlarını en iyi sonuçları almak [modeli eğitme](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) işlemi:

* Mümkünse, metin tabanlı PDF belgeleri görüntü tabanlı belge yerine kullanın. PDF taranmış görüntü olarak işlenir.
* Kullanılabilir bunları varsa boş bir form ve iki doldurulmuş biçimini kullanın.
* Formlarda doldurulmuş doldurulmuş alanlarının tüm örnekleri kullanın.
* Her alandaki farklı değerleri olan form kullanın.
* Form görüntülerinizi daha düşük kaliteli, daha büyük bir veri kümesi (örneğin, 10-15 görüntüleri) kullanın.

## <a name="general-input-requirements"></a>Genel bir giriş gereksinimleri

Eğitim veri kümesi, ayrıca tüm Form tanıyıcı içerik için giriş gereksinimleri uyduğundan emin olun. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>Eğitim verilerinizi karşıya yükleme

Eğitim için kullanacağınız form belge kümesini araya getirdik, bir Azure blob depolama kapsayıcısına karşıya gerekir. Bir kapsayıcı ile bir Azure depolama hesabı oluşturma işlemini bilmiyorsanız, aşağıdaki [Azure Storage Hızlı Başlangıç için Azure portalında](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

### <a name="organize-your-data-in-subfolders-optional"></a>(İsteğe bağlı) alt olarak verilerinizi düzenlemek

Varsayılan olarak, [modeli eğitme](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) API yalnızca depolama kapsayıcınızda kökünde olan form belgeleri kullanın. Ancak, API çağrısında belirtirseniz klasörlerdeki verilerle eğitebilirsiniz. Normalde, gövdesi [modeli eğitme](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) çağrı aşağıdaki biçime sahip burada `<SAS URL>` kapsayıcı paylaşılan erişim imzası URL'si olan:

```json
{
  "source":"<SAS URL>"
}
```

Aşağıdaki içeriği için istek gövdesi ekleyin, API alt klasörlerinde bulunan belgeleri eğitmek. `"prefix"` Alanı isteğe bağlıdır ve eğitim veri kümesi, yolları verilen dize ile başlayan dosyaları için sınırlar. Bu nedenle değerini `"Test"`, örneğin, yalnızca dosya veya "Test" kelimesiyle başlayan klasörler bakmak API neden olur.

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

Bir eğitim veri kümesi oluşturmak öğrendiniz, özel bir formu tanıyıcı modeli eğitmek ve formlarınızı kullanmaya başlamak için bir Hızlı Başlangıç'ı izleyin.

* [Hızlı Başlangıç: Bir modeli eğitmek ve cURL kullanarak form verileri ayıklayın](./quickstarts/curl-train-extract.md)
* [Hızlı Başlangıç: Bir modeli eğitmek ve Python ile REST API kullanarak form verilerini ayıklama](./quickstarts/python-train-extract.md)

