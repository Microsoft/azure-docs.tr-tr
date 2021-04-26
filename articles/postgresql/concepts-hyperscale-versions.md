---
title: Desteklenen sürümler – Hyperscale (Citus)-PostgreSQL için Azure veritabanı
description: PostgreSQL için Azure veritabanı 'nda bulunan PostgreSQL sürümleri-hiper ölçek (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: d8a584b6ba752e8f9220defa575f519828ba07e6
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023994"
---
# <a name="supported-database-versions-in-azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL için Azure veritabanı 'nda desteklenen veritabanı sürümleri – hiper ölçek (Citus)

## <a name="postgresql-versions"></a>PostgreSQL sürümleri

> [!IMPORTANT]
> Hiper ölçekte özelleştirilebilir PostgreSQL sürümleri (Citus) Şu anda önizleme aşamasındadır.  Bu önizleme, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
>
> Diğer yeni özelliklerin bir listesini, [hiper ölçek için Önizleme özellikleri (Citus)](hyperscale-preview-features.md)sayfasına bakabilirsiniz.

Hiper ölçek (Citus) sunucu grubunda çalışan PostgreSQL sürümü oluşturma sırasında özelleştirilebilir. Sürüm 11 ' den başka bir şey seçilmesi Şu anda bir önizleme özelliğidir.

Hiper ölçek (Citus) Şu anda aşağıdaki ana sürümleri desteklemektedir:

### <a name="postgresql-version-13-preview"></a>PostgreSQL sürüm 13 (Önizleme)

Geçerli küçük yayın 13,2 ' dir. Bu küçük sürümdeki geliştirmeler ve düzeltmeler hakkında daha fazla bilgi edinmek için [PostgreSQL belgelerine](https://www.postgresql.org/docs/13/static/release-13-2.html) bakın.

### <a name="postgresql-version-12-preview"></a>PostgreSQL sürüm 12 (Önizleme)

Geçerli küçük yayın 12,6 ' dir. Bu küçük sürümdeki geliştirmeler ve düzeltmeler hakkında daha fazla bilgi edinmek için [PostgreSQL belgelerine](https://www.postgresql.org/docs/12/static/release-12-6.html) bakın.

### <a name="postgresql-version-11"></a>PostgreSQL sürüm 11

Geçerli küçük yayın 11,11 ' dir. Bu küçük sürümdeki geliştirmeler ve düzeltmeler hakkında daha fazla bilgi edinmek için [PostgreSQL belgelerine](https://www.postgresql.org/docs/11/static/release-11-11.html) bakın.

### <a name="postgresql-version-10-and-older"></a>PostgreSQL sürüm 10 ve üzeri

PostgreSQL için Azure veritabanı-hiper ölçek (Citus) için PostgreSQL sürüm 10 ve daha eskisini desteklemiyoruz.

## <a name="citus-and-other-extension-versions"></a>Citus ve diğer uzantı sürümleri

Bir sunucu grubunda PostgreSQL 'ın hangi sürümünün çalıştığı üzerine bağlı olarak, [Postgres uzantılarının farklı sürümleri](concepts-hyperscale-extensions.md) de yüklenecektir.  Özellikle Postgres 13, Citus 10 ile gelir ve önceki Postgres sürümleri Citus 9,5 ile gelir.

## <a name="next-steps"></a>Sonraki adımlar

* Hangi sürümlerde yüklü olan [uzantıları](concepts-hyperscale-extensions.md) görün.
* [Bir Hyperscale (Citus) sunucu grubu oluşturmayı](quickstart-create-hyperscale-portal.md)öğrenin.
