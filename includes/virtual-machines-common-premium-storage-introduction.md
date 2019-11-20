---
title: include dosyası
description: include dosyası
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 86d03e980a233a60e4f3a6541462264dae0862af
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74005573"
---
Bu makalede, Azure Premium depolama kullanan yüksek performanslı uygulamalar oluşturmaya yönelik yönergeler sağlanmaktadır. Bu belgede sunulan yönergeleri, uygulamanız tarafından kullanılan teknolojiler için geçerli olan performans en iyi uygulamalarıyla birlikte kullanabilirsiniz. Yönergeleri göstermek için, bu belgenin tamamında bir örnek olarak Premium depolamada çalışan SQL Server kullandık.

Bu makaledeki depolama katmanının performans senaryolarını ele alırken uygulama katmanını iyileştirmeniz gerekecektir. Örneğin, Azure Premium depolamada bir SharePoint grubu barındırıyorsanız, veritabanı sunucusunu iyileştirmek için bu makaledeki SQL Server örnekleri kullanabilirsiniz. Ayrıca, en iyi performansı almak için SharePoint grubunun Web sunucusunu ve uygulama sunucusunu iyileştirin.

Bu makale, Azure Premium depolamada uygulama performansını iyileştirmek için aşağıdaki yaygın soruların yanıtlanmasına yardımcı olur.

* Uygulamanızın performansını ölçme  
* Beklenen yüksek performansa neden görmüyorsunuz?  
* Premium depolamada uygulama performansınızı hangi etkenlere etkiler?  
* Bu faktörler, Premium depolamada uygulamanızın performansını nasıl etkiler?  
* IOPS, bant genişliği ve gecikme süresi için nasıl iyileştirebileceğinizi?  

Premium depolamada çalışan iş yükleri yüksek performansa duyarlı olduğundan, bu yönergeleri özellikle Premium Depolama için sağladık. Uygun yerlerde örnek sağladık. Ayrıca, bu yönergelerin bazılarını standart depolama diskleriyle IaaS VM 'lerinde çalışan uygulamalar için de uygulayabilirsiniz.