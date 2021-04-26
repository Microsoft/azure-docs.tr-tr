---
title: Eşleme veri akışı dönüşümüne genel bakış
description: Eşleme veri akışı 'nda bulunan farklı dönüşümlere genel bakış
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: bb5021c0125c3140ef44a1ec3304b9d0ac40c30f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060236"
---
# <a name="mapping-data-flow-transformation-overview"></a>Eşleme veri akışı dönüşümüne genel bakış

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Aşağıda şu anda eşleme veri akışında desteklenen dönüşümlerin bir listesi verilmiştir. Yapılandırma ayrıntılarını öğrenmek için her dönüşüme tıklayın.

| Name | Kategori | Açıklama |
| ---- | -------- | ----------- |
| [Toplama](data-flow-aggregate.md) | Şema değiştiricisi | SUM, MIN, MAX ve COUNT gibi farklı toplamalar türlerini, var olan veya hesaplanan sütunlara göre gruplanmış olarak tanımlayın. | 
| [Satırı değiştirme](data-flow-alter-row.md) | Satır değiştiricisi | Satırlarda INSERT, DELETE, Update ve upsert ilkeleri ayarlayın. |
| [Koşullu bölme](data-flow-conditional-split.md) | Birden çok giriş/çıkış | Verilerin satırlarını, eşleşen koşullara göre farklı akışlara yönlendirin. |
| [Türetilmiş sütun](data-flow-derived-column.md) | Şema değiştiricisi | Yeni sütunlar oluşturun veya veri akışı ifade dilini kullanarak mevcut alanları değiştirin. | 
| [Var](data-flow-exists.md) | Birden çok giriş/çıkış | Verilerinizin başka bir kaynakta veya akışta bulunup bulunmadığını denetleyin. | 
| [Filtrele](data-flow-filter.md) | Satır değiştiricisi | Bir koşula göre bir satırı filtreleyin. |
| [Düzleştirme](data-flow-flatten.md) | Şema değiştiricisi |  Dizi değerlerini JSON gibi hiyerarşik yapılar içinde alın ve bunları tek tek satırlara geri alın. |
| [Join](data-flow-join.md) | Birden çok giriş/çıkış |  İki kaynaktan veya akışlardan verileri birleştirin. |
| [Arama](data-flow-lookup.md) | Birden çok giriş/çıkış | Başka bir kaynaktan başvuru verileri. |
| [Yeni dal](data-flow-new-branch.md) | Birden çok giriş/çıkış | Aynı veri akışına karşı birden çok işlem ve dönüşüm kümesi uygulayın. |
| [Parse](data-flow-new-branch.md) | Biçimlendirici | JSON, sınırlandırılmış metin veya XML biçimli metin dizeleri olan veri akışdaki metin sütunlarını ayrıştırın. |
| [Pivot](data-flow-pivot.md) | Şema değiştiricisi | Bir veya daha fazla gruplama sütununun ayrı sütunlara dönüştürülmüş ayrı satır değerleri olduğu bir toplama. |
| [Derece](data-flow-rank.md) | Şema değiştiricisi | Sıralama koşullarına göre sıralı bir derecelendirme oluşturma |
| [Seç](data-flow-select.md) | Şema değiştiricisi | Diğer ad sütunları ve akış adları ve sütunları bırakma veya yeniden sıralama |
| [Havuz](data-flow-sink.md) | - | Verileriniz için son hedef |
| [Sırala](data-flow-sort.md) | Satır değiştiricisi | Geçerli veri akışındaki gelen satırları Sırala |
| [Kaynak](data-flow-source.md) | - | Veri akışı için bir veri kaynağı |
| [Vekil anahtar](data-flow-surrogate-key.md) | Şema değiştiricisi | Artan iş dışı bir anahtar değeri ekleyin |
| [Birleşim](data-flow-union.md) | Birden çok giriş/çıkış | Birden çok veri akışını dikey olarak birleştirme |
| [Özetlemeyi açma](data-flow-unpivot.md) | Şema değiştiricisi | Sütunları satır değerlerine ekleyin |
| [Pencere](data-flow-window.md) | Şema değiştiricisi |  Veri akışlarınızın pencere tabanlı toplamalar tanımlayın. |
| [Parse](data-flow-parse.md) | Şema değiştiricisi |  Sütun verilerini JSON veya sınırlandırılmış metin olarak Ayrıştır |
