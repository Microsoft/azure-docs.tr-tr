---
title: Azure purview 'ta arama özelliklerini anlama (Önizleme)
description: Bu makalede, Azure purview 'ın arama özellikleri aracılığıyla veri bulmayı nasıl etkinleştirdiği açıklanır.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: af8ec9e0aac38240c7da92edd614892ff65712e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96553970"
---
# <a name="understand-search-features-in-azure-purview"></a>Azure purview 'da arama özelliklerini anlama

Bu makalede, Azure purview 'daki arama deneyimine genel bir bakış sunulmaktadır. Arama, bir kuruluştaki veri bulma ve verilerin idare deneyimlerini destekleyen bir çekirdek platform özelliğidir.

## <a name="search"></a>Arayın

Takip görünümü arama deneyimi, yönetilen bir arama diziniyle desteklenir. Bir veri kaynağı purview 'a kaydedildikten sonra, verileri kolay bulma sağlamak için arama hizmeti tarafından dizine alınır. Dizin, milyonlarca meta veri varlığını sorgulayarak arama ilgi özellikleri sağlar ve arama isteklerini tamamlar. Arama, verilerin en iyi şekilde yararlanmasına yardımcı olmak için verileri keşfetmenize, anlamanıza ve kullanmanıza yardımcı olur.

Purview 'daki arama deneyimi üç aşamalı bir işlemdir:

1. Arama kutusu, son kullanılan anahtar kelimeleri ve varlıkları içeren geçmişi gösterir.
1. Tuş vuruşlarını yazmaya başladığınızda arama, eşleşen anahtar kelimeleri ve varlıkları önerir. 
1. Arama sonucu sayfası, girilen anahtar sözcükle eşleşen varlıklarla birlikte gösterilir.

## <a name="reduce-the-time-to-discover-data"></a>Veri bulma süresini azaltma

Veri bulma bir veri analizinin veya veri tüketicileri için veri yönetimi iş yükünün ilk adımıdır. Veri bulma zaman alabilir, çünkü istediğiniz verileri nerede bulabileceğinizi bilmiyor olabilirsiniz. Verileri bulduktan sonra bile, verilere güvenip güvenmeyeceğinizi ve bunun üzerinde bir bağımlılık alıp vermeyeceğinizi da görebilirsiniz. 

Azure purview 'da aramanın hedefi, önemli verileri hızlı bir şekilde bulmak için hareketleri sağlayarak veri bulma sürecini hızlandırmaktır.

## <a name="recent-search-and-suggestions"></a>Son arama ve öneriler

Birçok kez aynı anda birden fazla proje üzerinde çalışıyor olabilirsiniz. Önceki projelerin sürdürülmesi daha kolay hale getirmek için, takip etme arama, son arama anahtar sözcüklerini ve önerilerini görüntüleme olanağı sağlar. Ayrıca, arama kutusu açılır listesinden **Tümünü görüntüle** ' yi seçerek son arama geçmişini yönetebilirsiniz.

## <a name="filters"></a>Filtreler

Filtreler ( *model* olarak da bilinir), aramayı tamamlamak için tasarlanmıştır. Filtreler arama sonucu sayfasında gösterilir. Arama sonucu sayfasındaki Filtreler, sınıflandırma, duyarlılık etiketi, veri kaynağı ve sahipler ' i içerir. Kullanıcılar yalnızca eşleşen veri varlıklarını görmek için bir filtredeki belirli değerleri seçebilir ve arama sonucunu eşleşen varlıklarla kısıtlayabilir.

## <a name="hit-highlighting"></a>İsabet vurgulama

Arama sonucu sayfasında eşleşen anahtar sözcükler, bir veri varlığının neden arama tarafından döndürüldüğünü daha kolay bir şekilde belirlenmesini kolaylaştırmak için vurgulanır. Anahtar sözcük eşleşmesi, veri varlık adı, açıklaması ve sahibi gibi birden çok alanda gerçekleşebilir.

İsabet vurgulaması etkinleştirilmiş olsa bile, bir veri varlığının arama için neden dahil edildiğini açık bir şekilde olmayabilir. Tüm özellikler varsayılan olarak aranır. Bu nedenle, bir veri varlığı bir sütun düzeyi özelliğindeki eşleşme nedeniyle döndürülebilir. Birden çok Kullanıcı kendi sınıflandırmalarını ve açıklamalarını kullanarak veri varlıklarına açıklama ekleyebilir, ancak arama sonuçları listesinde tüm meta veriler gösterilmez.

## <a name="sort"></a>Sırala

Kullanıcıların, arama sonuçlarını sıralamak için iki seçeneği vardır. Varlık adına göre veya varsayılan arama ilgisi olarak sıralama yapabilir.

## <a name="search-relevance"></a>İlgiyi ara

İlgi, arama sonucu sayfasında varsayılan sıralama sıraladır. Arama ilgisi arama anahtar sözcüğünü (bazıları veya tümü) içeren belgeleri bulur. Arama anahtar sözcüğünün çok sayıda örneğini içeren varlıklar daha yüksek sıralanır. Ayrıca, arama ilgi düzeyini artırmak için özel Puanlama mekanizmaları sürekli ayarlanır.

## <a name="next-steps"></a>Sonraki adımlar

* [Hızlı başlangıç: Azure portal bir Azure purview hesabı oluşturun](create-catalog-portal.md)
* [Hızlı başlangıç: Azure PowerShell/Azure CLı kullanarak Azure purview hesabı oluşturma](create-catalog-powershell.md)
* [Hızlı başlangıç: purview Studio 'Yu kullanma](use-purview-studio.md)
