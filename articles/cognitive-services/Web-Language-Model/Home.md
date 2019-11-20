---
title: Web Dil Modeli API’sine genel bakış
titleSuffix: Azure Cognitive Services
description: Microsoft Bilişsel Hizmetler'in Web Dil Modeli API'si doğal dil işleme için en son teknoloji araçlar sağlar.
services: cognitive-services
author: piyushbehre
manager: nitinme
ms.service: cognitive-services
ms.subservice: web-language-model
ms.topic: overview
ms.date: 08/12/2016
ms.author: piyush
ROBOTS: NOINDEX
ms.openlocfilehash: 066c7f8dbd6f64ba40ec539b636922069ee8b925
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849903"
---
# <a name="what-is-the-web-language-model-api-preview"></a>Web Dili Modeli API’si nedir? (Önizleme)

> [!IMPORTANT]
> Web Dil Modeli önizleme sürümü 9 Ağustos 2018 tarihinde kullanımdan kaldırılmıştır. Metin işleme ve analiz için [Azure Machine Learning metin analizi modüllerini](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) kullanmanızı öneririz.

Microsoft Web Dil Modeli API'si, doğal dil işleme için en son teknoloji araçlar sunan REST tabanlı bir bulut hizmetidir. Uygulamanız bu API'yi kullanarak Bing tarafından tr-TR pazarında toplanan web ölçeğindeki konuşma yapılarında eğitilen dil modelleri aracılığıyla büyük verinin gücünden yararlanabilir.

Beşinci düzeye kadar Markov zincirlerini destekleyen bu düzleştirilmiş geri almalı N gramer öğeli dil modelleri aşağıdaki konuşma yapılarında eğitilir:

- Web sayfası gövde metni
- Web sayfası başlık metni
- Web sayfası yer işareti metni
- Web araması sorgu metni

Web Dil Modeli API'si dört arama işlemini destekler:

1. Kelime dizisinin toplu (log10) olasılığı.
2. Önceki kelime dizisi verildiğinde, bir kelimenin koşullu (log10) olasılığı.
3. Belirli bir kelime dizisini izlemesi en olası olan kelimelerin (tamlamaların) listesi.
4. Boşluk içermeyen dizelerin kelimelere ayrılması.

## <a name="getting-started"></a>Başlarken

1. Hizmete abone olun.
2. [SDK](https://www.github.com/microsoft/cognitive-weblm-windows)'yı indirin.
3. SDK örnek kodunu çalıştırın.
4. Uç noktalarının çeşitli dillerde kod parçacıkları ile tüm ayrıntıları için [API Başvurusu](https://web.archive.org/web/20170503191852/westus.dev.cognitive.microsoft.com/docs/services/55de9ca4e597ed1fd4e2f104/operations/55de9ca4e597ed19b0de8a51)'na bakın.

## <a name="underlying-technology"></a>Temel Alınan Teknoloji

Aşağıdaki teknik yazı, bu dil modellerinin geliştirilmesine ilişkin ayrıntılar sağlamaktadır ve bu hizmeti kullanan araştırma yayınlarında kaynak gösterilmelidir:

- [An Overview of Microsoft Web N-gram Corpus and Applications](https://research.microsoft.com/apps/pubs/default.aspx?id=130762), NAACL-HLT 2010

Bu çalışmayı kaynak gösteren teknik yazıların bir listesi için [buraya](https://academic.microsoft.com/#/search?iq=And%28Ty%3D'0'%2CRId%3D2145833060%29&q=papers%20citing%20an%20overview%20of%20microsoft%20web%20n%20gram%20corpus%20and%20applications&filters=&from=0&sort=0) tıklayın.
