---
title: Azure Izleyici 'de Log Analytics Agent ile Windows olay günlüğü veri kaynaklarını toplama
description: Windows olay günlükleri koleksiyonunun Azure Izleyici tarafından ve oluşturdukları kayıtların ayrıntıları tarafından nasıl yapılandırılacağı açıklanmaktadır.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: 0eaa73fa7e0a9896a875af7e3a3aab22db2a37d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104657068"
---
# <a name="collect-windows-event-log-data-sources-with-log-analytics-agent"></a>Log Analytics aracısıyla Windows olay günlüğü veri kaynaklarını toplama
Windows olay günlükleri, birçok uygulama Windows olay günlüğü 'ne yazdığından, Windows sanal makinelerinde Log Analytics aracıları için en yaygın [veri kaynaklarından](../agents/agent-data-sources.md) biridir.  İzlemeniz gereken uygulamalar tarafından oluşturulan özel günlüklerin belirtilmesine ek olarak, sistem ve uygulama gibi standart günlüklerden olayları toplayabilirsiniz.

> [!IMPORTANT]
> Bu makalede, Azure Izleyici tarafından kullanılan aracılardan biri olan [Log Analytics aracısıyla](./log-analytics-agent.md) Windows olaylarının toplanması ele alınmaktadır. Diğer aracılar farklı veriler toplar ve farklı şekilde yapılandırılır. Kullanılabilir aracıların ve toplayabilecekleri verilerin bir listesi için bkz. [Azure izleyici aracılarına genel bakış](../agents/agents-overview.md) .

![Windows Olayları](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Windows olay günlüklerini yapılandırma
Log Analytics çalışma alanı için [aracılar yapılandırma menüsünden](../agents/agent-data-sources.md#configuring-data-sources) Windows olay günlüklerini yapılandırın.

Azure Izleyici yalnızca ayarlarda belirtilen Windows olay günlüklerinden olayları toplar.  Günlük adını yazarak ve ' a tıklayarak bir olay günlüğü ekleyebilirsiniz **+** .  Her günlük için yalnızca seçilen önem derecelerine sahip olaylar toplanır.  Toplamak istediğiniz belirli bir günlüğün önem derecesi ' ni denetleyin.  Olayları filtrelemek için herhangi bir ek ölçüt sağlamazsanız.

Bir olay günlüğünün adını yazdığınızda Azure Izleyici, ortak olay günlüğü adları için öneriler sağlar. Eklemek istediğiniz günlük listede görünmezse, yine de günlüğün tam adını yazarak eklemeye devam edebilirsiniz. Olay Görüntüleyicisi 'ni kullanarak günlüğün tam adını bulabilirsiniz. Olay Görüntüleyicisi 'nde, günlük için *Özellikler* sayfasını açın ve dizeyi *tam ad* alanından kopyalayın.

[![Windows olaylarını yapılandırma](media/data-sources-windows-events/configure.png)](media/data-sources-windows-events/configure.png#lightbox)

> [!IMPORTANT]
> Çalışma alanından güvenlik olayları koleksiyonu yapılandıramazsınız. Güvenlik olaylarını toplamak için [Azure Güvenlik Merkezi](../../security-center/security-center-enable-data-collection.md) 'Ni veya [Azure Sentinel](../../sentinel/connect-windows-security-events.md) 'i kullanmış olmanız gerekir.


> [!NOTE]
> Windows olay günlüğü 'ndeki kritik olaylar, Azure Izleyici günlüklerinde "hata" önem derecesine sahip olur.

## <a name="data-collection"></a>Veri toplama
Azure Izleyici, olay oluşturulduğu için izlenen bir olay günlüğünden seçilen önem derecesine uyan her olayı toplar.  Aracı, topladığı her olay günlüğündeki yerini kaydeder.  Aracı bir süre çevrimdışı kalırsa, bu olaylar, aracı çevrimdışıyken oluşturulsa bile, son kaldığınız yerden olayları toplar.  Bu olayların, aracı çevrimdışıyken, toplanan olayların üzerine yazılmakta olan olay günlüğü sarılmışsa toplanmaması olası bir olasılık vardır.

>[!NOTE]
>Azure Izleyici SQL Server tarafından oluşturulan denetim olaylarını toplamıyor- *Klasik* veya *Denetim başarısı* *ile anahtar* sözcük, *0xa0000000000000* anahtar sözcüğünü içeren olay kimliği 18453.
>

## <a name="windows-event-records-properties"></a>Windows olay kayıtları özellikleri
Windows olay kayıtları bir tür **olaya** sahiptir ve aşağıdaki tabloda bulunan özelliklere sahiptir:

| Özellik | Açıklama |
|:--- |:--- |
| Bilgisayar |Olayın toplandığı bilgisayarın adı. |
| EventCategory |Etkinliğin kategorisi. |
| EventData |Ham biçimdeki tüm olay verileri. |
| EventID |Olay sayısı. |
| EventLevel |Olayın önem derecesi, sayısal biçimde. |
| EventLevelName |Metin biçimindeki olayın önem derecesi. |
| EventLog |Olayın toplandığı olay günlüğünün adı. |
| ParameterXml |XML biçimindeki olay parametresi değerleri. |
| ManagementGroupName |System Center Operations Manager aracıları için yönetim grubunun adı.  Diğer aracılar için bu değer `AOI-<workspace ID>` |
| RenderedDescription |Parametre değerleriyle olay açıklaması |
| Kaynak |Etkinliğin kaynağı. |
| SourceSystem |Olayın toplandığı aracının türü. <br> OpsManager – Windows Aracısı, doğrudan bağlanma veya Operations Manager yönetilen <br> Linux – tüm Linux aracıları  <br> AzureStorage – Azure Tanılama |
| TimeGenerated |Olayın Windows 'da oluşturulduğu tarih ve saat. |
| UserName |Olayı günlüğe tutan hesabın Kullanıcı adı. |

## <a name="log-queries-with-windows-events"></a>Windows olaylarıyla sorguları günlüğe kaydet
Aşağıdaki tabloda, Windows olay kayıtlarını alan günlük sorgularının farklı örnekleri verilmiştir.

| Sorgu | Description |
|:---|:---|
| Olay |Tüm Windows olayları. |
| Olay &#124; EventLevelName = = "Error" |Hata önem derecesine sahip tüm Windows olayları. |
| Event &#124; Count () kaynağına göre özetleme |Kaynağa göre Windows olay sayısı. |
| EventLevelName = = "Error" &#124; Count () kaynağını kaynağa göre özetleyen olay &#124; |Kaynağa göre Windows hata olaylarının sayısı. |


## <a name="next-steps"></a>Sonraki adımlar
* Analiz için diğer [veri kaynaklarını](../agents/agent-data-sources.md) toplamak üzere Log Analytics yapılandırın.
* Veri kaynaklarından ve çözümlerinden toplanan verileri analiz etmek için [günlük sorguları](../logs/log-query-overview.md) hakkında bilgi edinin.  
* Windows aracılarınızın [performans sayaçlarını toplamayı](data-sources-performance-counters.md) yapılandırın.
