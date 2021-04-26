---
title: Azure Arc etkin PostgreSQL hiper ölçek ile desteklenen sürümler-gres
description: Azure Arc etkin PostgreSQL hiper ölçek ile desteklenen sürümler-gres
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0f3f9e7f4566800bdea6871cae1c5bafb3b37b95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90941587"
---
# <a name="supported-versions-of-postgres-with-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc özellikli PostgreSQL Hiper Ölçek ile desteklenen Postgres sürümleri

Bu makalede, Azure Arc etkin PostgreSQL hiper ölçeklendirmesiyle birlikte hangi Postgres sürümlerinin kullanılabildiği açıklanmaktadır.
Desteklenen sürümlerin, zaman içinde geliştikçe oluşan liste. Günümüzde desteklenen Başlıca sürümler şunlardır:
- Postgres 12 (varsayılan)
- Postgres 11

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="how-to-chose-between-versions"></a>Sürümler arasında nasıl tercih edilecek?
Uygulamalarınızın hangi sürümlere tasarlandığına ve sürümlerin her birinin özelliklerine bakmanız önerilir. Daha fazla bilgi edinmek için resmi Postgres sitesindeki her bir sürüm hakkında bilgi edinin:
- [Postgres 12 (varsayılan)](https://www.postgresql.org/docs/12/index.html)
- [Postgres 11](https://www.postgresql.org/docs/11/index.html)

## <a name="how-to-create-a-particular-version-in-azure-arc-enabled-postgresql-hyperscale"></a>Azure yay etkin PostgreSQL hiper ölçeğinde belirli bir sürüm nasıl oluşturulur?
Oluşturma sırasında, _--Engine-Version_ parametresini geçirerek hangi sürümün oluşturulacağını belirtebilirsiniz. Sürüm bilgilerini belirtmezseniz, varsayılan olarak, Postgres sürüm 12 ' nin sunucu grubu oluşturulur.

## <a name="how-do-be-notified-when-other-versions-are-available"></a>Diğer sürümler kullanılabilir olduğunda nasıl bildirilir?
Geri dönüp bu makaleyi okuyun. Bu, uygun şekilde güncelleştirilir. Ayrıca, Kubernetes kümenizdeki yay veri denetleyicisindeki özel kaynak tanımlarının (CRD) türlerini listeleyebilirsiniz.
Şu komutu çalıştırın:
```console
kubectl get crds
```

Şu şekilde bir çıktı döndürür:
```console
NAME                                            CREATED AT
datacontrollers.arcdata.microsoft.com           2020-08-31T20:15:16Z
postgresql-11s.arcdata.microsoft.com            2020-08-31T20:15:16Z
postgresql-12s.arcdata.microsoft.com            2020-08-31T20:15:16Z
sqlmanagedinstances.sql.arcdata.microsoft.com   2020-08-31T20:15:16Z
```

Bu örnekte, bu çıktı, Postgres ile ilgili iki tür CRD olduğunu gösterir:
- postgresql-12s.arcdata.microsoft.com Postgres 12 için CRD
- postgresql-11s.arcdata.microsoft.com Postgres 11 için CRD

Bunlar, sunucu grupları değil, CRDs gruplarıdır. Bir CRD 'nin varlığı, bu sürümün sunucu grubunu oluşturmuş veya oluşturulmadığını belirten bir gösterge değildir.
CRD, ne tür kaynakların oluşturulabileceğini belirten bir göstergesidir.

## <a name="next-steps"></a>Sonraki adımlar:
- [Azure Arc etkin PostgreSQL hiper ölçeğini oluşturma hakkında bilgi edinin](create-postgresql-hyperscale-server-group.md)
- [Arc veri denetleyicinizde oluşturulan Azure Arc etkin PostgreSQL hiper ölçek sunucu gruplarının listesini alma hakkında bilgi edinin](list-server-groups-postgres-hyperscale.md)
