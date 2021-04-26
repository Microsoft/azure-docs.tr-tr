---
title: Temel katman önizlemesi-hiper ölçek (Citus)-PostgreSQL için Azure veritabanı
description: PostgreSQL için Azure veritabanı için tek düğümlü temel katman-hiper ölçek (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c8cad5eb0983715a7cc7c18249243e93a2c498d7
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024049"
---
# <a name="basic-tier-preview"></a>Temel katman (Önizleme)

> [!IMPORTANT]
> Hiperscale (Citus) temel katmanı Şu anda önizlemededir.  Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
>
> Diğer yeni özelliklerin bir listesini, [hiper ölçek için Önizleme özellikleri (Citus)](hyperscale-preview-features.md)sayfasına bakabilirsiniz.

PostgreSQL için Azure veritabanı 'nın (Citus) temel katmanı, daha sonra ölçeklendirilebilen küçük bir sunucu grubu oluşturmanın basit bir yoludur. Standart katmandaki sunucu gruplarında bir düzenleyici düğümü ve en az iki çalışan düğümü olduğunda, temel katman her şeyi tek bir veritabanı düğümünde çalıştırır.

Daha az düğüm kullanmaktan başka, temel katmanda standart katmanın tüm özellikleri vardır. Standart katman gibi, diğer özelliklerin yanı sıra yüksek kullanılabilirlik, okuma çoğaltmaları ve sütunlu tablo depolamayı destekler.

## <a name="choosing-basic-vs-standard-tier"></a>Temel vs standart katmanını seçme

Temel katman, ilk geliştirme, test ve sürekli tümleştirme için ekonomik ve kullanışlı bir dağıtım seçeneği olabilir. Tek bir veritabanı düğümü kullanır ve standart katmanla aynı SQL API 'sini gösterir. Uygulamaları temel katmanla test edebilir ve daha sonra arabirimin aynı kaldığı güvenle [Standart katmana](howto-hyperscale-scale-grow.md#add-worker-nodes) daha sonra mezun olursunuz.

Temel katman, üretimde daha küçük iş yükleri için de uygundur (Önizleme işleminden sonra genel kullanıma sunulduktan sonra). Sunucu sanal çekirdekleri sayısını artırarak temel *katmanda dikey olarak* ölçeklenebileceğiniz yer vardır.

Daha fazla ölçek gerektiğinde standart katmanı kullanın. En düşük izin verilen sunucu grubunda bir düzenleyici düğümü ve iki çalışan vardır. [İlk boyutlandırma](howto-hyperscale-scale-initial.md) nasıl yapılır bölümünde açıklandığı gibi, kullanım örneklerinizi temel alan daha fazla düğüm kullanmayı tercih edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Temel katmanı sağlamayı](quickstart-create-hyperscale-basic-tier.md) öğrenin
* Hazırsanız, bkz. Temel katmandan Standart katmana [nasıl mezun](howto-hyperscale-scale-grow.md#add-worker-nodes) olun
* [Sütunlu depolama](concepts-hyperscale-columnar.md) seçeneği hem temel hem de Standart katmanda kullanılabilir
