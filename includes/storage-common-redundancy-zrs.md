---
title: include dosyası
description: include dosyası
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b7fddce8f682bc341b361a47f8e083cc281e90aa
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309583"
---
Bölgesel olarak yedekli depolama (ZRS), verilerinizi tek bir bölgedeki üç depolama kümesi arasında eşzamanlı olarak çoğaltır. Her depolama kümesi diğerlerinden fiziksel olarak ayrılır ve kendi kullanılabilirlik bölgesinde (AZ) bulunur. Her bir kullanılabilirlik&mdash;alanı ve&mdash;içindeki ZRS kümesi özerk olur ve ayrı yardımcı programlar ve ağ özellikleri içerir. ZRS depolama hesabına yönelik yazma isteği, veriler yalnızca üç küme genelinde tüm yinelemelere yazıldıktan sonra başarıyla döndürülür.

Verilerinizi ZRS çoğaltmasını kullanarak bir depolama hesabında depoladığınızda, bir kullanılabilirlik bölgesi kullanılamaz hale gelirse verilerinize erişmeye ve bunları yönetmeye devam edebilirsiniz. ZRS, mükemmel performans ve düşük gecikme süresi sağlar. ZRS [yerel olarak yedekli depolama (LRS)](../articles/storage/common/storage-redundancy-lrs.md)ile aynı [ölçeklenebilirlik hedeflerini](../articles/storage/common/storage-scalability-targets.md) sunmaktadır.

Tutarlılık, dayanıklılık ve yüksek kullanılabilirlik gerektiren senaryolar için ZRS 'yi değerlendirin. Bir kesinti veya doğal olağanüstü durum bir kullanılabilirlik bölgesini kullanılamaz hale gelirse, ZRS belirli bir yıl boyunca en az% 99,9999999999 (12 9 ' a) depolama nesneleri için dayanıklılık sağlar.

Coğrafi bölge yedekli depolama (GZRS) (Önizleme), birincil bölgedeki üç Azure kullanılabilirlik bölgesinde verilerinizi eşzamanlı olarak çoğaltır ve sonra verileri zaman uyumsuz olarak ikincil bölgeye çoğaltır. GZRS, en yüksek dayanıklılık ile birlikte yüksek kullanılabilirlik sağlar. GZRS belirli bir yıl boyunca nesnelerin en az% 99.99999999999999 (16 9) oranında dayanıklılığını sağlamak üzere tasarlanmıştır. İkincil bölgedeki verilere yönelik okuma erişimi için, Okuma Erişimli Coğrafi bölge yedekli depolamayı (RA-GZRS) etkinleştirin. GZRS hakkında daha fazla bilgi için bkz. [coğrafi bölge yedekli depolama, yüksek oranda kullanılabilirlik ve en yüksek dayanıklılık (Önizleme) için](../articles/storage/common/storage-redundancy-gzrs.md).

Kullanılabilirlik alanları hakkında daha fazla bilgi için bkz. [kullanılabilirlik alanları genel bakış](https://docs.microsoft.com/azure/availability-zones/az-overview).
