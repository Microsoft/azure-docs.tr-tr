---
title: Azure Izleyici 'de Windows olay günlüklerini toplayın ve çözümleyin | Microsoft Docs
description: Windows olay günlükleri koleksiyonunun Azure Izleyici tarafından ve oluşturdukları kayıtların ayrıntıları tarafından nasıl yapılandırılacağı açıklanmaktadır.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: dd8f1e0e79f85c5d91966bcba13052f297422e67
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932398"
---
# <a name="windows-event-log-data-sources-in-azure-monitor"></a>Azure Izleyici 'de Windows olay günlüğü veri kaynakları
Windows olay günlükleri, birçok uygulama Windows olay günlüğü 'ne yazdığından Windows aracılarını kullanarak veri toplamaya yönelik en yaygın [veri kaynaklarından](agent-data-sources.md) biridir.  İzlemeniz gereken uygulamalar tarafından oluşturulan özel günlüklerin belirtilmesine ek olarak, sistem ve uygulama gibi standart günlüklerden olayları toplayabilirsiniz.

![Windows Olayları](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Windows olay günlüklerini yapılandırma
[Gelişmiş ayarlar 'Daki veri menüsünden](agent-data-sources.md#configuring-data-sources)Windows olay günlüklerini yapılandırın.

Azure Izleyici yalnızca ayarlarda belirtilen Windows olay günlüklerinden olayları toplar.  Günlüğün adına yazıp **+** ' ye tıklayarak bir olay günlüğü ekleyebilirsiniz.  Her günlük için yalnızca seçilen önem derecelerine sahip olaylar toplanır.  Toplamak istediğiniz belirli bir günlüğün önem derecesi ' ni denetleyin.  Olayları filtrelemek için herhangi bir ek ölçüt sağlamazsanız.

Bir olay günlüğünün adını yazdığınızda Azure Izleyici, ortak olay günlüğü adları için öneriler sağlar. Eklemek istediğiniz günlük listede görünmezse, yine de günlüğün tam adını yazarak eklemeye devam edebilirsiniz. Olay Görüntüleyicisi 'ni kullanarak günlüğün tam adını bulabilirsiniz. Olay Görüntüleyicisi 'nde, günlük için *Özellikler* sayfasını açın ve dizeyi *tam ad* alanından kopyalayın.

![Windows olaylarını yapılandırma](media/data-sources-windows-events/configure.png)

> [!NOTE]
> Windows olay günlüğü 'ndeki kritik olaylar, Azure Izleyici günlüklerinde "hata" önem derecesine sahip olur.

## <a name="data-collection"></a>Veri toplama
Azure Izleyici, olay oluşturulduğu için izlenen bir olay günlüğünden seçilen önem derecesine uyan her olayı toplar.  Aracı, topladığı her olay günlüğündeki yerini kaydeder.  Aracı bir süre çevrimdışı kalırsa, bu olaylar, aracı çevrimdışıyken oluşturulsa bile, son kaldığınız yerden olayları toplar.  Bu olayların, aracı çevrimdışıyken, toplanan olayların üzerine yazılmakta olan olay günlüğü sarılmışsa toplanmaması olası bir olasılık vardır.

>[!NOTE]
>Azure Izleyici SQL Server tarafından oluşturulan denetim olaylarını toplamıyor- *Klasik* veya *Denetim başarısı* *ile anahtar* sözcük, *0xa0000000000000*anahtar sözcüğünü içeren olay kimliği 18453.
>

## <a name="windows-event-records-properties"></a>Windows olay kayıtları özellikleri
Windows olay kayıtları bir tür **olaya** sahiptir ve aşağıdaki tabloda bulunan özelliklere sahiptir:

| Özellik | Açıklama |
|:--- |:--- |
| Bilgisayar |Olayın toplandığı bilgisayarın adı. |
| EventCategory |Etkinliğin kategorisi. |
| EventData |Ham biçimdeki tüm olay verileri. |
| Even |Olay sayısı. |
| EventLevel |Olayın önem derecesi, sayısal biçimde. |
| EventLevelName |Metin biçimindeki olayın önem derecesi. |
| EventLog |Olayın toplandığı olay günlüğünün adı. |
| ParameterXml |XML biçimindeki olay parametresi değerleri. |
| ManagementGroupName |System Center Operations Manager aracıları için yönetim grubunun adı.  Diğer aracılar için bu değer `AOI-<workspace ID>` |
| RenderedDescription |Parametre değerleriyle olay açıklaması |
| Kaynak |Etkinliğin kaynağı. |
| SourceSystem |Olayın toplandığı aracının türü. <br> OpsManager – Windows Aracısı, doğrudan bağlanma veya Operations Manager yönetilen <br> Linux – tüm Linux aracıları  <br> AzureStorage – Azure Tanılama |
| TimeGenerated |Olayın Windows 'da oluşturulduğu tarih ve saat. |
| Nitelen |Olayı günlüğe tutan hesabın Kullanıcı adı. |

## <a name="log-queries-with-windows-events"></a>Windows olaylarıyla sorguları günlüğe kaydet
Aşağıdaki tabloda, Windows olay kayıtlarını alan günlük sorgularının farklı örnekleri verilmiştir.

| Sorgu | Açıklama |
|:---|:---|
| Olay |Tüm Windows olayları. |
| EventLevelName = = "Error" adlı olay &#124; |Hata önem derecesine sahip tüm Windows olayları. |
| Kaynağa &#124; göre olay özetleme sayısı () |Kaynağa göre Windows olay sayısı. |
| EventLevelName = = "Error" &#124; &#124; ın Count () kaynağını kaynağa göre özetleme olayı |Kaynağa göre Windows hata olaylarının sayısı. |


## <a name="next-steps"></a>Sonraki adımlar
* Analiz için diğer [veri kaynaklarını](agent-data-sources.md) toplamak üzere Log Analytics yapılandırın.
* Veri kaynaklarından ve çözümlerinden toplanan verileri analiz etmek için [günlük sorguları](../log-query/log-query-overview.md) hakkında bilgi edinin.  
* Windows aracılarınızın [performans sayaçlarını toplamayı](data-sources-performance-counters.md) yapılandırın.
