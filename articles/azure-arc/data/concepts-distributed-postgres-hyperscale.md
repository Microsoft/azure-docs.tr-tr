---
title: Yay etkin PostgreSQL hiper ölçek sunucu grubu ile verileri dağıtmaya ve ölçeklendirmeye yönelik kavramlar
titleSuffix: Azure Arc enabled data services
description: Yay etkin PostgreSQL hiper ölçek sunucu grubu ile veri dağıtmaya yönelik kavramlar
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: c01da4aed9e27296ea7b570420bb190b16749848
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90941810"
---
# <a name="concepts-for-distributing-data-with-arc-enabled-postgresql-hyperscale-server-group"></a>Yay etkin PostgreSQL hiper ölçek sunucu grubu ile veri dağıtmaya yönelik kavramlar

Bu makalede, Azure Arc etkin PostgreSQL hiper ölçeğinden en iyi şekilde yararlanmak için önemli olan temel kavramlar açıklanmaktadır.
Aşağıda bağlanılan makaleler, PostgreSQL için Azure veritabanı hiper ölçek (Citus) için açıklanan kavramlara işaret noktasıdır. Aynı kavramların ve perspektiflerin uygulanması için, Azure Arc 'ın PostgreSQL hiper Ölçeklendirmesiyle aynı teknolojiden de aynıdır.

**Bunlar arasındaki fark nedir?**
- _PostgreSQL için Azure Veritabanı Hiper Ölçek (Citus)_

Bu, Azure 'da hizmet olarak veritabanı olarak kullanılabilen Postgres veritabanı altyapısının hiper ölçek formu etkendir (PaaS). Hiper ölçek deneyimini sağlayan Citus uzantısı tarafından desteklenir. Bu form faktöründe hizmet Microsoft veri merkezlerinde çalışır ve Microsoft tarafından işletilebilir.

- _Azure Arc etkin PostgreSQL hiper ölçeği_

Bu, Azure Arc etkin veri hizmeti ile sunulan Postgres veritabanı altyapısının hiper ölçek form faktördür. Bu form faktöründe, müşterilerimiz sistemleri barındıran altyapıyı sağlar ve bunları çalışır.

Azure Arc etkin PostgreSQL Hyperscale etrafındaki temel kavramlar aşağıda özetlenmiştir:

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="nodes-and-tables"></a>Düğümler ve tablolar
Azure Arc etkin Postgres hiper ölçeğinden en iyi şekilde yararlanmak için aşağıdaki kavramlar hakkında bilgi sahibi olmanız önemlidir:
- Azure yay etkin PostgreSQL hiper ölçek: düzenleyici ve çalışanlar içindeki özelleştirilmiş Postgres düğümleri
- Tablo türleri: dağıtılmış tablolar, başvuru tabloları ve yerel tablolar
- Parçalar

[PostgreSQL Için Azure veritabanı 'nda bulunan düğümler ve tablolar hakkında daha fazla bilgi için bkz. Hyperscale (Citus)](../../postgresql/concepts-hyperscale-nodes.md). 

## <a name="determine-the-application-type"></a>Uygulama türünü belirleme
Oluşturmakta olduğunuz uygulamanın türünü açıkça tanımlamak önemlidir. Neden? Azure Arc etkin bir PostgreSQL hiper ölçek sunucu grubu üzerinde etkili sorgular çalıştırmak için tabloların sunucular arasında düzgün bir şekilde dağıtılması gerekir. Önerilen dağıtım, uygulama türüne ve sorgu desenlerine göre değişir. Azure Arc etkin Postgres Hyperscale üzerinde iyi çalışan çok sayıda uygulama vardır:
- Çok kiracılı uygulamalar
- Gerçek Zamanlı Uygulamalar

Veri modellemesinin ilk adımı, uygulamanızın uygulamanıza ne kadar benzediğini belirlemektir.

Bkz. [uygulama türünü belirlemede](../../postgresql/concepts-hyperscale-app-type.md)ayrıntılara bakın.


## <a name="choose-a-distribution-column"></a>Dağıtım sütunu seçme
Neden dağıtılmış bir sütun seçmelisiniz?

Bu, yaptığınız en önemli Modellendirme kararlarından biridir. Azure Arc etkin PostgreSQL hiper ölçek, satırların dağıtım sütununun değerine göre parçaları parçalar halinde depolar. Doğru seçim, ilgili verileri aynı fiziksel düğümlerde gruplandırır, bu da sorguları hızlı bir şekilde yapar ve tüm SQL özellikleri için destek ekler. Yanlış bir seçim sistemin yavaş çalışmasını sağlar ve tüm SQL özelliklerini düğümler genelinde desteklemez. Bu makale, en yaygın iki hiper ölçek senaryosu için dağıtım sütunu ipuçları sağlar.

[Dağıtım sütunlarını seçin](../../postgresql/concepts-hyperscale-choose-distribution-column.md)' de ayrıntılara bakın.


## <a name="table-colocation"></a>Tablo birlikte bulundurma

Birlikte bulundurma, ilgili bilgilerin aynı düğümlerde depolanması ile ilgilidir. Tüm gerekli veriler herhangi bir ağ trafiği olmadan kullanılabilir olduğunda sorgular hızlı bir şekilde geçebilir. Farklı düğümlerde ilgili verileri birlikte bulundurma, sorguların her düğüm üzerinde verimli bir şekilde çalışmasını sağlar.

[Tablodaki](../../postgresql/concepts-hyperscale-colocation.md)ayrıntılara bakın.


## <a name="next-steps"></a>Sonraki adımlar
- [Azure Arc etkin PostgreSQL hiper ölçeğini oluşturma hakkında bilgi edinin](create-postgresql-hyperscale-server-group.md)
- [Arc veri denetleyicinizde oluşturulan Azure Arc etkin PostgreSQL hiper ölçek sunucu gruplarını ölçeklendirme hakkında bilgi edinin](scale-out-postgresql-hyperscale-server-group.md)
- [Azure Arc etkin veri hizmetleri hakkında bilgi edinin](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
- [Azure Arc hakkında bilgi edinin](https://aka.ms/azurearc)

