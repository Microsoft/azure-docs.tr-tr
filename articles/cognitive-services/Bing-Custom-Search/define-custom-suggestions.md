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
ms.openlocfilehash: 5b2b8871d868e827532f23c7ef4f14fb00afb5bc
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072796"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Özel otomatik öneri deneyiminizi yapılandırın

Özel otomatik öneri, arama deneyiminiz ile ilgili önerilen arama sorgu dizelerinin bir listesini döndürür. Önerilen sorgu dizeleri, kullanıcının arama kutusunda sağladığı kısmi bir sorgu dizesini temel alır. Listede en fazla 10 öneri yer alacak. 

Yalnızca özel önerilerin döndürülüp döndürülmeyeceğini veya Bing önerilerini de dahil etmek istediğinizi belirtirsiniz. Bing önerilerini eklerseniz, Bing önerilerinde önce özel öneriler görüntülenir. Yeterli sayıda ilgili öneri sağlarsanız, döndürülen öneri listesi Bing önerilerini içermez. Bing önerileri her zaman özel arama örneğinizin bağlamındadır. 

Örneğiniz için arama sorgusu önerilerini yapılandırmak için **otomatik öneri** sekmesine tıklayın.  

> [!NOTE]
> Bu özelliği kullanmak için, özel aramaya uygun düzeyde abone olmanız gerekir (bkz. [fiyatlandırma](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)).

Önerilerin, hizmet uç noktasında (API veya barındırılan Kullanıcı arabirimi) yansıtılması 24 saate kadar sürebilir.

## <a name="enable-bing-suggestions"></a>Bing önerilerini etkinleştir

Bing önerilerini etkinleştirmek için **Otomatik Bing öneriler** kaydırıcısını açık konuma değiştirin. Kaydırıcı mavi hale gelir.

## <a name="add-your-own-suggestions"></a>Kendi önerilerinizi ekleyin

Kendi sorgu dizesi önerilerinizi eklemek için onları **Kullanıcı tanımlı öneriler**altında listeye ekleyin. Listeye bir öneri ekledikten sonra ENTER tuşuna basın veya **+** simgesine tıklayın. Öneriyi dilediğiniz dilde belirtebilirsiniz. En fazla 5.000 sorgu dizesi önerisi ekleyebilirsiniz.

## <a name="upload-suggestions"></a>Yükleme önerileri

Bir seçenek olarak, bir dosyadaki öneri listesini karşıya yükleyebilirsiniz. Dosya, satır başına bir arama sorgu dizesi içermelidir. Dosyayı karşıya yüklemek için karşıya yükle simgesine tıklayın ve karşıya yüklenecek dosyayı seçin. Hizmet, dosyadaki önerileri ayıklar ve listeye ekler.

## <a name="remove-suggestions"></a>Önerileri kaldır

Bir sorgu dizesi önerisini kaldırmak için kaldırmak istediğiniz önerinin yanındaki Kaldır simgesine tıklayın.

## <a name="block-suggestions"></a>Engelleme önerileri

Bing önerilerini eklerseniz, Bing 'in döndürmesini istemediğiniz bir arama sorgu dizeleri listesi ekleyebilirsiniz. Engellenen sorgu dizeleri eklemek için **Engellenen önerileri göster**' e tıklayın. Sorgu dizesini listeye ekleyin ve ENTER tuşuna basın veya **+** simgesine tıklayın. En fazla 50 engellenen sorgu dizesi ekleyebilirsiniz.



[!INCLUDE [publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Özel otomatik öneri yapılandırma değişikliklerinin etkili olması 24 saate kadar sürebilir.


## <a name="enabling-autosuggest-in-hosted-ui"></a>Barındırılan Kullanıcı arabiriminde otomatik öneri etkinleştiriliyor

Barındırılan Kullanıcı arabiriminiz için sorgu dizesi önerilerini etkinleştirmek üzere **barındırılan Kullanıcı arabirimi**' ne tıklayın. **Ek yapılandırma** bölümüne gidin. **Web araması**altında **otomatik öneri**' i etkinleştirmek için **Açık** ' ı seçin. Otomatik öneri özelliğini etkinleştirmek için, arama kutusu içeren bir düzen seçmeniz gerekir.


## <a name="calling-the-autosuggest-api"></a>Otomatik öneri API 'sini çağırma

Bing Özel Arama API'si kullanarak önerilen Sorgu dizelerini almak için aşağıdaki uç noktaya `GET` bir istek gönderin.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

Yanıt, önerilen Sorgu dizelerini içeren `SearchAction` nesnelerinin bir listesini içerir.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Her öneri bir `displayText` ve `query` alanı içerir. `displayText` alanı, arama kutusunun açılan listesini doldurmak için kullandığınız önerilen sorgu dizesini içerir.

Kullanıcı açılan listeden önerilen bir sorgu dizesi seçerse, [Bing özel arama API'si](overview.md)çağrılırken `query` alanındaki sorgu dizesini kullanın.


## <a name="next-steps"></a>Sonraki adımlar

- [Özel öneriler alın](./get-custom-suggestions.md)
- [Özel örneğinize arama](./search-your-custom-view.md)
- [Özel barındırılan Kullanıcı arabirimini yapılandırma ve kullanma](./hosted-ui.md)
