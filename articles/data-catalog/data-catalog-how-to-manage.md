---
title: Azure Veri Kataloğu 'nda veri varlıklarını yönetme
description: Makale, Azure Veri Kataloğu 'nda kayıtlı veri varlıklarının görünürlüğünü ve sahipliğini denetlemeyi vurgular.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 9905ed72ec54304bbdb0f7ee607cbb013fc645bb
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736338"
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Azure Veri Kataloğu 'nda veri varlıklarını yönetme
## <a name="introduction"></a>Giriş
Azure Veri Kataloğu, veri kaynağı bulma için tasarlanmıştır, böylece analiz yapmak ve kararlar almak için ihtiyaç duyduğunuz veri kaynaklarını kolayca bulabilir ve anlayabilmenizi sağlayabilirsiniz. Bu bulma özellikleri, siz ve diğer kullanıcılar en geniş kullanılabilir veri kaynaklarını bulabilir ve anladıysanız en büyük etkiyi yapar. Bu öğeler göz önünde bulundurularak, veri kataloğu 'nun varsayılan davranışı tüm kayıtlı veri kaynaklarının tüm katalog kullanıcıları tarafından görünür ve bulunabilir olmasını sağlar.

Veri Kataloğu size verilerin kendisi için erişim sağlamaz. Veri erişimi, veri kaynağının sahibi tarafından denetlenir. Veri Kataloğu ile veri kaynaklarını bulabilir ve kataloğa kayıtlı kaynaklarla ilgili meta verileri görüntüleyebilirsiniz.

Ancak, veri kaynaklarının yalnızca belirli kullanıcılara veya belirli grupların üyelerine görünebileceği durumlar olabilir. Bu tür senaryolarda, kullanıcılar katalog içinde kayıtlı veri varlıklarının sahipliğini alabilir ve ardından sahip oldukları varlıkların görünürlüğünü denetleyebilir.

> [!NOTE]
> Bu makalede açıklanan işlevsellik yalnızca Azure Veri Kataloğu 'nun standart sürümünde kullanılabilir. Ücretsiz sürüm, sahiplik ve veri varlık görünürlüğünü kısıtlama olanağı sağlamaz.
>
>

## <a name="manage-ownership-of-data-assets"></a>Veri varlıklarının sahipliğini yönetme
Varsayılan olarak, veri kataloğunda kayıtlı olan veri varlıkları sahiplenmemiş. Kataloğa erişim izni olan tüm kullanıcılar bu varlıkları bulabilir ve bunlara ek açıklama ekleyebilir. Kullanıcılar, sahip olunan veri varlıklarının sahipliğini alabilir ve ardından sahip oldukları varlıkların görünürlüğünü sınırlayabilir.

Veri Kataloğu 'ndaki bir veri varlığı aitse, yalnızca sahipleri tarafından yetkilendirilmiş kullanıcılar varlığı bulabilir ve meta verilerini görüntüleyebilir ve yalnızca sahipler varlığı katalogdan silebilir.

> [!NOTE]
> Veri kataloğunda sahiplik yalnızca Katalogda depolanan meta verileri etkiler. Sahiplik, temel alınan veri kaynağında hiçbir izni karşılamıyor.
>
>

### <a name="take-ownership"></a>Sahipliği üstlen
Kullanıcılar veri kataloğu portalındaki **sahipliğini al** seçeneğini belirleyerek veri varlıklarının sahipliğini alabilir. Sahip olmayan bir veri varlığının sahipliğini almak için özel izin gerekmez. Herhangi bir Kullanıcı, sahipsiz olmayan bir veri varlığının sahipliğini alabilir.

### <a name="add-owners-and-co-owners"></a>Sahipler ve ikincil sahipler ekleme
Bir veri varlığı zaten aitse, diğer kullanıcılar sahiplik alamaz. Bunlar, mevcut bir sahibe ait ikincil sahipler olarak eklenmelidir. Herhangi bir sahip, ikincil sahipler olarak ek kullanıcılar veya güvenlik grupları ekleyebilir.

> [!NOTE]
> Sahip olunan herhangi bir veri varlığı için en az iki bireye sahip olmak en iyi uygulamadır.
>
>

### <a name="remove-owners"></a>Sahipleri kaldır
Herhangi bir varlık sahibi ortak sahipler ekleyebilen gibi, herhangi bir varlık sahibi ortak sahibi kaldırabilir.

Kendilerini bir sahip olarak kaldıran bir varlık sahibi varlığı artık yönetemez. Varlık sahibi kendisini bir sahip olarak kaldırırsa ve başka ortak sahipler yoksa, varlık sahipsiz duruma geri döner.

## <a name="control-visibility"></a>Denetim görünürlüğü
Veri varlık sahipleri, sahip oldukları veri varlıklarının görünürlüğünü denetleyebilir. Tüm veri kataloğu kullanıcılarının veri varlığını bulabileceği ve görüntüleyebildiği varsayılan olarak Görünürlüğü kısıtlamak için varlık sahibi, varlığın özelliklerindeki **Bu kullanıcılara &** **Herkes** ' deki görünürlük ayarını değiştirebilir. Artık sahipler belirli kullanıcıları ve güvenlik gruplarını ekleyebilirler.

> [!NOTE]
> Mümkün olduğunda, varlık sahipliğinin ve görünürlük izinlerinin ayrı kullanıcılara değil güvenlik gruplarına atanması gerekir.
>
>

## <a name="catalog-administrators"></a>Katalog yöneticileri
Veri Kataloğu yöneticileri, katalogdaki tüm varlıkların örtük olarak ortak sahipileridir. Varlık sahipleri, yöneticilerin görünürlüğünü kaldıramaz ve Yöneticiler, katalogdaki tüm veri varlıklarının sahipliğini ve görünürlüğünü yönetebilir.

## <a name="summary"></a>Özet
Veri Kataloğu, meta verilere ve veri varlığı bulmaya yönelik modeli, tüm katalog kullanıcılarının katkıda bulunmasını ve bulmasını sağlar. Veri Kataloğu 'nun standart sürümü, belirli veri varlıklarının görünürlüğünü ve kullanımını sınırlamak üzere sahiplik ve yönetim için tasarlanmıştır.
