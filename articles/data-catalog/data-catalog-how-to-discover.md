---
title: Azure Veri Kataloğu 'nda veri kaynaklarını bulma
description: Bu makalede, Azure Veri Kataloğu portalının arama ve filtreleme ve isabet vurgulama özelliklerini kullanma dahil olmak üzere Azure Veri Kataloğu ile kayıtlı veri varlıklarını bulma işlemi anlatılmaktadır.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: b12cb94832a1ea977fb13f5f2271984dc8780cee
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736369"
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Azure Veri Kataloğu 'nda veri kaynaklarını bulma

## <a name="introduction"></a>Giriş

Azure Veri Kataloğu, kurumsal veri kaynakları için kayıt ve bulma sistemi olarak hizmet veren tam olarak yönetilen bir bulut hizmetidir. Diğer bir deyişle, Veri Kataloğu, kullanıcıların veri kaynaklarını bulmasına, anlamasına ve kullanmasına yardımcı olur. Kuruluşların mevcut verilerinden daha fazla değer almasını sağlar. Veri kaynağı veri kataloğuna kaydedildikten sonra, ihtiyacınız olan verileri bulmayı kolayca arayabilmeniz için meta verileri hizmet tarafından dizine alınır.

## <a name="searching-and-filtering"></a>Arama ve filtreleme

Veri kataloğunda bulma iki birincil mekanizma kullanır: arama ve filtreleme.

Arama hem sezgisel hem de güçlü olacak şekilde tasarlanmıştır. Varsayılan olarak, arama terimleri kullanıcı tarafından ek açıklamalar dahil olmak üzere katalogdaki herhangi bir özellikle eşleştirilir.

Filtreleme, aramayı tamamlamak üzere tasarlanmıştır. Uzmanlar, veri kaynağı türü, nesne türü ve Etiketler gibi belirli özellikleri seçebilirsiniz. Yalnızca eşleşen veri varlıklarını görüntüleyebilir ve arama sonuçlarını eşleşen varlıklarla kısıtlayabilirsiniz.

Arama ve filtreleme birleşimini kullanarak, ihtiyacınız olan veri kaynaklarını saptamak için veri kataloğu 'Na kaydedilmiş veri kaynaklarında hızlıca gezinebilirsiniz.

## <a name="search-syntax"></a>Söz dizimi arama

Varsayılan boş metin araması basit ve sezgisel olsa da, arama sonuçları üzerinde daha fazla denetim için veri kataloğu arama sözdizimini de kullanabilirsiniz. Veri Kataloğu arama aşağıdaki teknikleri destekler:

| Teknik | Bir yönetim grubuna bağlanmak veya bağlı bir yönetim grubunun özelliklerini düzenlemek için Yönetim çalışma alanında | Örnek |
| --- | --- | --- |
| Temel arama |Bir veya daha fazla arama terimi kullanan temel arama. Sonuçlar, belirtilen koşullara bir veya daha fazlasına sahip herhangi bir özellikle eşleşen varlıklardır. |`sales data` |
| Özellik kapsamı |Yalnızca arama teriminin belirtilen özellikle eşleştiği veri kaynaklarını döndürün. |`name:finance` |
| Boole işleçleri |Boole işlemlerini kullanarak bir aramayı genişletin veya daraltın. |`finance NOT corporate` |
| Parantez ile gruplandırma |Özellikle Boolean işleçleriyle birlikte mantıksal yalıtım elde etmek üzere sorgunun parçalarını gruplamak için ayraçları kullanın. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Karşılaştırma işleçleri |Sayısal ve Tarih veri türlerine sahip özellikler için eşitlik dışındaki karşılaştırmaları kullanın. |`modifiedTime > "11/05/2014"` |

Veri Kataloğu araması hakkında daha fazla bilgi için bkz. [Azure Veri Kataloğu](/rest/api/datacatalog/#search-syntax-reference) makalesi.

## <a name="hit-highlighting"></a>İsabet vurgulama

Arama sonuçlarını görüntülediğinizde, belirtilen arama terimleriyle eşleşen (veri varlık adı, açıklama ve Etiketler gibi) görüntülenen tüm özellikler, belirli bir veri varlığının belirli bir arama tarafından neden döndürüldüğünü belirlemeyi kolaylaştırmak için vurgulanır.

> [!NOTE]
> İsabet Vurgulamayı devre dışı bırakmak için, veri kataloğu portalındaki **vurgu** anahtarını kullanın.

Arama sonuçlarını görüntülediğinizde, isabet vurgulaması etkinleştirilmiş olsa bile, her zaman bir veri varlığının neden dahil edilmediğini her zaman açık olmayabilir. Tüm özellikler varsayılan olarak arandığından, bir sütun düzeyi özelliğindeki eşleşme nedeniyle bir veri varlığı döndürülebilir. Birden çok Kullanıcı, kendi etiketleri ve açıklamalarıyla kayıtlı veri varlıklarına açıklama ekleyebilir, ancak arama sonuçları listesinde tüm meta veriler gösterilmez.

Varsayılan kutucuk görünümünde, arama sonuçlarında görüntülenen her kutucuk, eşleşme sayısını ve bunların konumunu hızlıca görüntüleyebilmeniz ve isterseniz bunlara atlayabilmeniz için arama **terimi eşleşmelerini görüntüle** simgesini içerir.

 ![Azure Veri Kataloğu portalında vurgulama ve arama eşleşmeleri](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Özet

Veri Kataloğu ile veri kaynağı kaydetme, veri kaynağından Katalog hizmetine yapısal ve açıklayıcı meta verileri kopyaladığından, veri kaynağının keşfve anlaşılması daha kolay hale gelir. Bir veri kaynağını kaydettikten sonra, veri kataloğu portalı içinden filtreleme ve arama işlemlerini kullanarak bulabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* Veri kaynaklarını bulma hakkında adım adım ayrıntılar için bkz. [Azure Veri Kataloğu 'nu kullanmaya başlama](data-catalog-get-started.md).
