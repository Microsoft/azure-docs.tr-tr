---
title: 'Hızlı başlangıç: temel katman sunucu grubu oluşturma-hiper ölçek (Citus)-PostgreSQL için Azure veritabanı'
description: PostgreSQL için Azure veritabanı hiper ölçek (Citus) temel katmanını kullanmaya başlayın.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 04/07/2021
ms.openlocfilehash: 07ace217e5299662a1c3145a225abc25f4f1f337
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024095"
---
# <a name="create-a-hyperscale-citus-basic-tier-server-group-in-the-azure-portal"></a>Azure portal bir hiper ölçek (Citus) temel katman sunucu grubu oluşturun

PostgreSQL için Azure veritabanı-Hyperscale (Citus), bulutta yüksek oranda kullanılabilir PostgreSQL veritabanlarını çalıştırmak, yönetmek ve ölçeklendirmek için kullandığınız yönetilen bir hizmettir. [Temel katmanı](concepts-hyperscale-tiers.md) , ilk geliştirme ve test için uygun bir dağıtım seçeneğidir.

Bu hızlı başlangıçta, Azure portal kullanarak bir Hyperscale (Citus) temel katman sunucu grubu oluşturma gösterilmektedir. Sunucu grubunu hazırlayacak ve sorguları çalıştırmak için bu sunucuya bağlanabildiğinizi doğrulayacaksınız.

> [!IMPORTANT]
> Hiperscale (Citus) temel katmanı Şu anda önizlemededir.  Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
>
> Diğer yeni özelliklerin bir listesini, [hiper ölçek için Önizleme özellikleri (Citus)](hyperscale-preview-features.md)sayfasına bakabilirsiniz.

[!INCLUDE [azure-postgresql-hyperscale-create-basic-tier](../../includes/azure-postgresql-hyperscale-create-basic-tier.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir hiper ölçek (Citus) sunucu grubu sağlamayı öğrendiniz. Bu ağa psql ile bağlanırsınız, bir şema oluşturdunuz ve dağıtılmış veriler.

- [Ölçeklenebilir çok kiracılı uygulamalar oluşturmak](./tutorial-design-database-hyperscale-multi-tenant.md) için öğreticiyi izleyin
- Sunucu grubunuz için en iyi [ilk boyutu](howto-hyperscale-scale-initial.md) belirleme
