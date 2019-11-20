---
title: Azure Izleyici 'de aracı veri kaynaklarını yapılandırma | Microsoft Docs
description: Veri kaynakları, Azure Izleyici 'nin aracılardan ve diğer bağlı kaynaklardan topladığı günlük verilerini tanımlar.  Bu makalede, Azure Izleyici 'nin veri kaynaklarını nasıl kullandığı kavramı açıklanmakta, nasıl yapılandırılacağı hakkında ayrıntılı bilgi verilmektedir ve kullanılabilir farklı veri kaynaklarının bir özeti sağlanır.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: f7960adca1c3665dbf8737cb10a898c58b1542ec
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932827"
---
# <a name="agent-data-sources-in-azure-monitor"></a>Azure Izleyici 'de aracı veri kaynakları
Azure Izleyicisinin aracılardan topladığı veriler, yapılandırdığınız veri kaynakları tarafından tanımlanır.  Aracılardan alınan veriler bir kayıt kümesiyle [günlük verileri](data-platform-logs.md) olarak depolanır.  Her veri kaynağı, her tür kendi özellik kümesine sahip olan belirli bir türün kayıtlarını oluşturur.

![Günlük verileri toplama](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>Veri kaynaklarının Özeti
Aşağıdaki tabloda, Azure Izleyici 'de Şu anda kullanılabilir olan aracı veri kaynakları listelenmektedir.  Her birinin, bu veri kaynağı için ayrıntı sağlayan ayrı bir makaleye bağlantısı vardır.   Ayrıca, kendi yöntemi ve koleksiyon sıklığı hakkında bilgi de sağlar. 


| Veri kaynağı | Platform | Log Analytics Aracısı | Operations Manager Aracısı | Azure Storage | Operations Manager gerekli mi? | Yönetim grubu aracılığıyla gönderilen aracı verileri Operations Manager | Toplama sıklığı |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Özel günlükler](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | varış noktasında |
| [Özel günlükler](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | varış noktasında |
| [IIS günlükleri](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |Günlük dosyası geçiş ayarına bağlıdır |
| [Performans sayaçları](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |zamanlandığı gibi, en az 10 saniye |
| [Performans sayaçları](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |zamanlandığı gibi, en az 10 saniye |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |Azure depolama 'dan: 10 dakika; aracıda: gelişde |
| [Windows olay günlükleri](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | varış noktasında |


## <a name="configuring-data-sources"></a>Veri kaynaklarını yapılandırma
Veri kaynaklarını, çalışma alanı için **Gelişmiş ayarlar** ' da **veri** menüsünden yapılandırırsınız.  Tüm yapılandırmalar, çalışma alanınızdaki tüm bağlı kaynaklara dağıtılır.  Şu anda bu yapılandırmadan hiçbir aracıyı dışlayamazsınız.

![Windows olaylarını yapılandırma](media/agent-data-sources/configure-events.png)

1. Azure portal, **Gelişmiş ayarlar**> çalışma alanınız > **Log Analytics çalışma alanları** ' nı seçin.
2. **Veri**seçin.
3. Yapılandırmak istediğiniz veri kaynağına tıklayın.
4. Yapılandırma ayrıntıları için yukarıdaki tabloda yer alan her bir veri kaynağına ilişkin belgelere bağlantıyı izleyin.


## <a name="data-collection"></a>Veri toplama
Veri kaynağı konfigürasyonları, birkaç dakika içinde Azure Izleyici 'ye doğrudan bağlı aracılara dağıtılır.  Belirtilen veriler aracıdan toplanır ve her bir veri kaynağına özel aralıklarla doğrudan Azure Izleyici 'ye dağıtılır.  Bu bilgiler için her bir veri kaynağına yönelik belgelere bakın.

Bağlı bir yönetim grubundaki System Center Operations Manager aracıları için, veri kaynağı konfigürasyonları yönetim paketlerine çevrilir ve varsayılan olarak her 5 dakikada bir yönetim grubuna dağıtılır.  Aracı yönetim paketini diğer gibi indirir ve belirtilen verileri toplar. Veri kaynağına bağlı olarak, veriler, verileri Azure Izleyicisine ileten bir yönetim sunucusuna gönderilir veya aracı, yönetim sunucusundan geçmeden verileri Azure Izleyici 'ye gönderir. Ayrıntılar için bkz. [Azure 'da çözümleri izlemek Için veri toplama ayrıntıları](../insights/solutions-inventory.md) .  Operations Manager ve Azure Izleyicisini bağlama hakkında bilgi edinebilir ve yapılandırmanın yapılandırma [System Center Operations Manager Ile tümleştirilmesine](om-agents.md)göre değişiklik sıklığını değiştirebilirsiniz.

Aracı Azure Izleyici 'ye veya Operations Manager bağlanamazsa, bir bağlantı kurduğunda teslim edileceği verileri toplamaya devam edecektir.  Veri miktarı, istemcinin en büyük önbellek boyutuna ulaşırsa veya aracı 24 saat içinde bir bağlantı kuramazsa, veriler kaybolabilir.

## <a name="log-records"></a>Günlük kayıtları
Azure Izleyici tarafından toplanan tüm günlük verileri çalışma alanında kayıt olarak depolanır.  Farklı veri kaynakları tarafından toplanan kayıtlar kendi özellik kümesine sahip olur ve bunların **tür** özelliklerine göre tanımlanır.  Her bir kayıt türü hakkındaki ayrıntılar için her bir veri kaynağına ve çözüme yönelik belgelere bakın.

## <a name="next-steps"></a>Sonraki adımlar
* Azure Izleyici 'ye işlevsellik ekleyen ve ayrıca çalışma alanına veri toplayacağınız [çözümleri izleme](../insights/solutions.md) hakkında bilgi edinin.
* Veri kaynaklarından ve izleme çözümlerinden toplanan verileri analiz etmek için [günlük sorguları](../log-query/log-query-overview.md) hakkında bilgi edinin.  
* Veri kaynaklarından ve izleme çözümlerinden toplanan kritik verileri proaktif olarak bildirmek için [uyarıları](alerts-overview.md) yapılandırın.
