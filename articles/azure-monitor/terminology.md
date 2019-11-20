---
title: Azure Izleyici terminolojisi güncelleştirmeleri | Microsoft Docs
description: Azure izleme hizmetlerinde yapılan son terminoloji değişikliklerini açıklar.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 5324c17cdf2593d8e1022e6ddbf52b4e24ee64e8
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597820"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Azure Izleyici adlandırma ve terminoloji değişiklikleri
Azure 'da, Azure müşterilerine yönelik izlemeyi basitleştirmek için, Azure Izleyici 'de son zamanlarda, farklı hizmetlerle birleştirilmiş değişiklikler yapılmıştır. Bu makalede, Azure Izleyici belgelerindeki son ad ve terminoloji değişiklikleri açıklanmaktadır.

## <a name="october-2019---diagnostic-log-to-resource-log"></a>2019 Ekim-kaynak günlüğüne tanılama günlüğü
Tanılama günlükleri, gerçekten toplanmaya daha iyi eşleşecek şekilde "kaynak günlükleri" olarak değiştirildi. "Tanılama ayarları" terimi aynı kalır.  

## <a name="february-2019---log-analytics-terminology"></a>Şubat 2019-Log Analytics terimleri
Azure Izleyici kapsamında farklı hizmetlerin Birleştirmeden sonra, Azure Izleyici hizmetini ve farklı bileşenlerini daha iyi anlatmak için belgelerimizin terminolojisini değiştirerek bir sonraki adımı sunuyoruz. 

### <a name="log-analytics"></a>Log Analytics
Azure Izleyici günlük verileri bir Log Analytics çalışma alanında depolanır ve yine de aynı Log Analytics hizmeti tarafından toplanıp çözümlenmektedir, ancak birçok yerde _Log Analytics_ terimi _Azure izleyici günlüklerine_değiştiririz. Bu terim, Azure Izleyici 'de rolünü daha iyi yansıtır ve [Azure izleyici 'de ölçümlerle](platform/data-platform-metrics.md)daha iyi tutarlılık sağlar.

_Log Analytics_ terimi artık öncelikle sorguları yazmak ve çalıştırmak ve günlük verilerini çözümlemek için kullanılan Azure Portal sayfa için geçerlidir. Ölçüm verilerini çözümlemek için kullanılan Azure portal sayfa olan [Ölçüm Gezgini](platform/metrics-charts.md)'nin işlevsel eşdeğeridir.

### <a name="log-analytics-workspaces"></a>Log Analytics çalışma alanları
Azure Izleyici 'de günlük verilerini tutan [çalışma alanlarına](platform/manage-access.md) hala Log Analytics çalışma alanları adı verilir. Azure portal **Log Analytics** menüsü **Log Analytics çalışma alanları** olarak yeniden adlandırıldı ve [Yeni çalışma alanları oluşturduğunuz](learn/quick-create-workspace.md) ve veri kaynaklarını yapılandırdığınız yerdir. Günlüklerinizi ve diğer izleme verilerinizi **Azure izleyici** 'de çözümleyin ve çalışma alanınızı **Log Analytics çalışma alanlarında**yapılandırın.

### <a name="management-solutions"></a>Yönetim çözümleri
[Yönetim çözümleri](insights/solutions.md) , işlevlerini daha iyi açıklayan _izleme çözümleri_olarak yeniden adlandırıldı.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>Ağustos 2018-izleme hizmetlerini Azure Izleyici 'ye birleştirme
Log Analytics ve Application Insights Azure Izleyici 'de birleştirildi ve Azure kaynaklarını ve karma ortamlarını izlemeye yönelik tek bir tümleşik deneyim sağlar. Bu hizmetlerden hiçbir işlev kaldırılmadı ve kullanıcılar herhangi bir kayıp veya tehlikeye girmeden her zaman tamamlandıkları senaryoları gerçekleştirebilir.

Bu hizmetlerin her biri için belgeler, Azure Izleyici için tek bir içerik kümesiyle birleştirildi. Bu, birden çok içerik kümesine başvurmak zorunda kalmadan tek bir konumdaki belirli bir izleme senaryosuna ilişkin içeriğin tümünü bulmaya yardımcı olur. Birleştirilmiş hizmet geliştikçe içerik daha tümleşik hale gelir.

Aracılar ve görünümler gibi Log Analytics bir parçası olarak kabul edilen diğer özellikler de Azure Izleyici 'nin özellikleri olarak yeniden konumlandırılamıştı. İşlevselliği, Azure portal deneyimlerine ait olası geliştirmelerden farklı şekilde değişmemiştir.


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>Nisan 2018-Operations Management Suite markasını devre dışı bırakma
Operations Management Suite (OMS), lisans amaçlarıyla aşağıdaki Azure Yönetim hizmetlerinden bir paketiydi:

- Application Insights
- Azure Otomasyonu
- Azure Yedekleme
- Log Analytics
- Site Kurtarma

[Bu hizmetler Için yeni fiyatlandırma sunuldu](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)ve OMS paketlendirmesi yeni müşteriler için artık kullanılamıyor. OMS 'nin parçası olan hizmetlerden hiçbiri, yukarıda açıklanan Azure Izleyici ile birleştirme dışında değişmemiştir. 




## <a name="next-steps"></a>Sonraki adımlar

- Farklı bileşenlerini ve özelliklerini açıklayan [Azure izleyici 'ye genel bakış](overview.md) konusunu okuyun.
- [OMS portalının geçişi](../log-analytics/log-analytics-oms-portal-transition.md)hakkında bilgi edinin.
