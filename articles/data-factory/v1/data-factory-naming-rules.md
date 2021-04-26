---
title: Azure Data Factory varlıkları adlandırma kuralları-sürüm 1
description: Data Factory v1 varlıklarının adlandırma kurallarını açıklar.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 3c68159f20873aeff5938ab21f348be4a922041c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779486"
---
# <a name="rules-for-naming-azure-data-factory-entities"></a>Azure Data Factory varlıkları adlandırma kuralları
> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [Data Factory kuralları adlandırma](../naming-rules.md).

Aşağıdaki tabloda Data Factory yapıtlar için adlandırma kuralları verilmiştir.

| Name | Ad benzersizliği | Doğrulama denetimleri |
|:--- |:--- |:--- |
| Data Factory |Microsoft Azure genelinde benzersiz. Adlar, büyük/küçük harfe duyarlıdır `MyDF` ve `mydf` aynı veri fabrikasına başvurur. |<ul><li>Her veri fabrikası, tam olarak bir Azure aboneliğine bağlanır.</li><li>Nesne adları bir harf veya sayı ile başlamalıdır ve yalnızca harf, rakam ve tire (-) karakterini içerebilir.</li><li>Her tire (-) karakteri hemen önce ve ardından bir harf veya sayı gelmelidir. Kapsayıcı adlarında ardışık kesik çizgilerden izin verilmez.</li><li>Ad 3-63 karakter uzunluğunda olabilir.</li></ul> |
| Bağlı hizmetler/tablolar/işlem hatları |Bir veri fabrikasında ile benzersizdir. Adlar büyük/küçük harfe duyarlıdır. |<ul><li>Bir tablo adındaki en fazla karakter sayısı: 260.</li><li>Nesne adları bir harf, sayı veya alt çizgi (_) ile başlamalıdır.</li><li>Şu karakterlere izin verilmez: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", " \\ "</li></ul> |
| Kaynak Grubu |Microsoft Azure genelinde benzersiz. Adlar büyük/küçük harfe duyarlıdır. |<ul><li>En fazla karakter sayısı: 1000.</li><li>Ad harf, rakam ve şu karakterleri içerebilir: "-", "_", "," ve "."</li></ul> |

