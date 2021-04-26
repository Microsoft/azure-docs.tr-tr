---
title: Özel otomatik öneri önerileri tanımlayın-Bing Özel Arama
titleSuffix: Azure Cognitive Services
description: Özel otomatik öneri, arama deneyiminiz ile ilgili önerilen arama sorgu dizelerinin bir listesini döndürür.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 986a7c8f1282b76c2ae94eebcc2c31dc3b3e7cad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96352023"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Özel otomatik öneri deneyiminizi yapılandırın

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Özel otomatik öneri, arama deneyiminiz ile ilgili önerilen arama sorgu dizelerinin bir listesini döndürür. Önerilen sorgu dizeleri, kullanıcının arama kutusunda sağladığı kısmi bir sorgu dizesini temel alır. Listede en fazla 10 öneri yer alacak. 

Yalnızca özel önerilerin döndürülüp döndürülmeyeceğini veya Bing önerilerini de dahil etmek istediğinizi belirtirsiniz. Bing önerilerini eklerseniz, Bing önerilerinde önce özel öneriler görüntülenir. Yeterli sayıda ilgili öneri sağlarsanız, döndürülen öneri listesi Bing önerilerini içermez. Bing önerileri her zaman özel arama örneğinizin bağlamındadır. 

Örneğiniz için arama sorgusu önerilerini yapılandırmak için **otomatik öneri** sekmesine tıklayın.  

> [!NOTE]
> Bu özelliği kullanmak için, özel aramaya uygun düzeyde abone olmanız gerekir (bkz. [fiyatlandırma](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)).

Önerilerin, hizmet uç noktasında (API veya barındırılan Kullanıcı arabirimi) yansıtılması 24 saate kadar sürebilir.

## <a name="enable-bing-suggestions"></a>Bing önerilerini etkinleştir

Bing önerilerini etkinleştirmek için **Otomatik Bing öneriler** kaydırıcısını açık konuma değiştirin. Kaydırıcı mavi hale gelir.

## <a name="add-your-own-suggestions"></a>Kendi önerilerinizi ekleyin

Kendi sorgu dizesi önerilerinizi eklemek için onları **Kullanıcı tanımlı öneriler** altında listeye ekleyin. Listeye bir öneri ekledikten sonra ENTER tuşuna basın veya **+** simgeye tıklayın. Öneriyi dilediğiniz dilde belirtebilirsiniz. En fazla 5.000 sorgu dizesi önerisi ekleyebilirsiniz.

## <a name="upload-suggestions"></a>Yükleme önerileri

Bir seçenek olarak, bir dosyadaki öneri listesini karşıya yükleyebilirsiniz. Dosya, satır başına bir arama sorgu dizesi içermelidir. Dosyayı karşıya yüklemek için karşıya yükle simgesine tıklayın ve karşıya yüklenecek dosyayı seçin. Hizmet, dosyadaki önerileri ayıklar ve listeye ekler.

## <a name="remove-suggestions"></a>Önerileri kaldır

Bir sorgu dizesi önerisini kaldırmak için kaldırmak istediğiniz önerinin yanındaki Kaldır simgesine tıklayın.

## <a name="block-suggestions"></a>Engelleme önerileri

Bing önerilerini eklerseniz, Bing 'in döndürmesini istemediğiniz bir arama sorgu dizeleri listesi ekleyebilirsiniz. Engellenen sorgu dizeleri eklemek için **Engellenen önerileri göster**' e tıklayın. Sorgu dizesini listeye ekleyin ve ENTER tuşuna basın veya **+** simgeye tıklayın. En fazla 50 engellenen sorgu dizesi ekleyebilirsiniz.



[!INCLUDE [publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Özel otomatik öneri yapılandırma değişikliklerinin etkili olması 24 saate kadar sürebilir.


## <a name="enabling-autosuggest-in-hosted-ui"></a>Barındırılan Kullanıcı arabiriminde otomatik öneri etkinleştiriliyor

Barındırılan Kullanıcı arabiriminiz için sorgu dizesi önerilerini etkinleştirmek üzere **barındırılan Kullanıcı arabirimi**' ne tıklayın. **Ek yapılandırma** bölümüne gidin. **Web araması** altında **otomatik öneri**' i etkinleştirmek için **Açık** ' ı seçin. Otomatik öneri özelliğini etkinleştirmek için, arama kutusu içeren bir düzen seçmeniz gerekir.


## <a name="calling-the-autosuggest-api"></a>Otomatik öneri API 'sini çağırma

Bing Özel Arama API'si kullanarak önerilen Sorgu dizelerini almak için `GET` aşağıdaki uç noktaya bir istek gönderin.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

Yanıt, `SearchAction` Önerilen Sorgu dizelerini içeren nesnelerin bir listesini içerir.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Her öneri bir `displayText` ve `query` alanı içerir. `displayText`Alan, arama kutusunun açılan listesini doldurmak için kullandığınız önerilen sorgu dizesini içerir.

Kullanıcı, açılan listeden önerilen bir sorgu dizesi seçerse, `query` [Bing özel arama API'si](overview.md)çağırırken alanın sorgu dizesini kullanın.


## <a name="next-steps"></a>Sonraki adımlar

- [Özel öneriler alma]()
- [Özel örneğinize arama](./search-your-custom-view.md)
- [Özel barındırılan Kullanıcı arabirimini yapılandırma ve kullanma](./hosted-ui.md)