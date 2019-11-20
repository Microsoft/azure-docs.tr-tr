---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 8106b1a77f11874b1b13f41bfc9e62ea800afc98
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361537"
---
### <a name="database-tier"></a>Veritabanı katmanı

Veritabanı katmanı, uygulamanın veritabanı örneklerini içerir. Veritabanı bir Oracle DB, Oracle RAC veya Oracle sınavı veri veritabanı sistemi olabilir. 

Oracle DB kullanmak tercih ediyorsanız, veritabanı örneği Azure Market 'te bulunan Oracle DB görüntüleri aracılığıyla Azure 'da dağıtılabilir. Alternatif olarak, Oracle DB OCı üzerinde bir PaaS modelinde dağıtmak için Azure ile OCı arasındaki Interconnect 'i de kullanabilirsiniz.

Oracle RAC için, IaaS modelinde veya PaaS modelinde OCı olarak Azure CloudSimple üzerinde Oracle RAC dağıtımı yapabilirsiniz. İki düğümlü bir RAC sistemi kullanmanız önerilir. 

Son olarak, sınava veri sistemleri için OCı bağlantısını kullanın ve bu verileri OCı 'da dağıtın. Yukarıdaki önceki mimari diyagramda, iki alt ağ arasında OCı halinde dağıtılan bir sınava veri sistemi gösterilmektedir.

Üretim senaryolarında, iki kullanılabilirlik bölgesinde (Azure 'da dağıtım yapıyorsanız) veya iki kullanılabilirlik etki alanı (OCı) üzerinde veritabanının birden çok örneğini dağıtın. Birincil ve hazır bekleyen veritabanlarını eşleştirmek için Oracle Active Data Guard 'ı kullanın.

Veritabanı katmanı yalnızca orta katmandan istekleri alır. Bir ağ güvenlik grubu ayarlamanız önerilir (veritabanını OCı 'da dağıtırsanız), yönetim nedenlerinden dolayı yalnızca orta katmandan ve bağlantı noktası 22 numaralı bağlantı noktası için bağlantı noktası 1521 ' deki isteklere izin vermek üzere gereklidir.

OCı 'da dağıtılan veritabanları için, FastConnect Devrenize bağlı bir dinamik yönlendirme ağ geçidi (DRG) ile ayrı bir sanal bulut ağı ayarlanmalıdır.