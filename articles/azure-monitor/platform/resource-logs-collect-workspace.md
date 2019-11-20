---
title: Log Analytics çalışma alanında Azure Kaynak günlüklerini toplayın
description: Azure Kaynak günlüklerinin Azure Izleyici 'de bir Log Analytics çalışma alanına akışını nasıl sağlayacağınızı öğrenin.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 92de47041791c8b6c540844adb62391268b81c34
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200511"
---
# <a name="collect-azure-resource-logs-in-log-analytics-workspace-in-azure-monitor"></a>Azure Izleyici 'de Log Analytics çalışma alanında Azure Kaynak günlüklerini toplayın
Azure 'da [kaynak günlükleri](resource-logs-overview.md) , bir Azure kaynağının iç işlemi hakkında zengin, sık veriler sağlar. Bu makalede, güçlü günlük sorgularını kullanarak ve ayrıca uyarılar gibi diğer Azure Izleyici özelliklerinden yararlanmak için, bu dosyayı Azure Izleyici günlüklerinde toplanan diğer izleme verileriyle analiz etmenizi sağlayan bir Log Analytics çalışma alanında kaynak günlüklerinin toplanması açıklanır. görüntüler. 


## <a name="what-you-can-do-with-resource-logs-in-a-workspace"></a>Çalışma alanındaki kaynak günlükleriyle yapabilecekleriniz
Kaynak günlüklerinin bir Log Analytics çalışma alanında toplanması, tüm Azure kaynaklarınızın günlüklerini birlikte analiz etmenize ve aşağıdakiler de dahil olmak üzere [Azure Izleyici günlüklerine](data-platform-logs.md) sunulan tüm özelliklerden faydalanabilmenizi sağlar:

* **Günlük sorguları** -tanılama verilerinizi hızlıca çözümlemek ve bunlarla ilgili Öngörüler elde etmek ve Azure izleyici 'deki diğer kaynaklardan toplanan verilerle analiz etmek için güçlü bir sorgu dili kullanarak [günlük sorguları](../log-query/log-query-overview.md) oluşturun.
* **Uyarı** - [Azure izleyici 'de günlük uyarılarını](alerts-log.md)kullanarak kaynak günlükleriniz için tanımlanan kritik koşullar ve desenlerin öngörülü bildirimini alın.
* **Görselleştirmeler** -bir günlük sorgusunun sonuçlarını bir Azure panosuna sabitleyin veya etkileşimli bir raporun parçası olarak çalışma kitabına dahil edin.

## <a name="prerequisites"></a>Önkoşullar
Henüz bir tane yoksa [Yeni bir çalışma alanı oluşturmanız](../learn/quick-create-workspace.md) gerekir. Ayarı yapılandıran kullanıcının her iki aboneliğe de uygun RBAC erişimi olduğundan, çalışma alanının kaynakla aynı abonelikte olması gerekmez.

## <a name="create-a-diagnostic-setting"></a>Tanılama ayarı oluştur
Kaynak günlükleri varsayılan olarak toplanmaz. Azure kaynağı için bir tanılama ayarı oluşturarak bunları bir Log Analytics çalışma alanında ve diğer hedeflere toplayın. Ayrıntılar için bkz. [Azure 'da günlükleri ve ölçümleri toplamak için tanılama ayarı oluşturma](diagnostic-settings.md) .

## <a name="collection-mode"></a>Toplama modu
Log Analytics çalışma alanında toplanan veriler, [Azure Izleyici günlüklerinin yapısı](../log-query/logs-structure.md)bölümünde açıklandığı gibi tablolarda depolanır. Kaynak günlükleri tarafından kullanılan tablolar, kaynağın kullandığı koleksiyon türüne bağlıdır:

- Azure tanılama-yazılan tüm veriler _AzureDiagnostics_ tablosuna gönderilir.
- Kaynağa özgü veriler, kaynağın her kategorisi için ayrı ayrı tabloya yazılır.

### <a name="azure-diagnostics-mode"></a>Azure Tanılama modu 
Bu modda, herhangi bir [Tanılama ayarının](diagnostic-settings.md) tüm verileri _AzureDiagnostics_ tablosunda toplanır. Bu, bugün en çok Azure hizmeti tarafından kullanılan eski yöntemdir.

Birden çok kaynak türü aynı tabloya veri gönderdiğinden, bu şema, toplanmakta olan tüm farklı veri türlerinin şemaların üst kümesidir.

Aşağıdaki veri türleri için tanılama ayarlarının aynı çalışma alanında toplandığı aşağıdaki örneği göz önünde bulundurun:

- Hizmet 1 ' in denetim günlükleri (A, B ve C sütunlarından oluşan bir şemaya sahip)  
- Hizmet 1 ' in hata günlükleri (D, E ve F sütunlarından oluşan bir şemaya sahip)  
- Hizmet 2 ' nin denetim günlükleri (G, H ve I sütunlarından oluşan bir şemaya sahip)  

AzureDiagnostics tablosu şöyle görünür:  

| ResourceProvider    | Kategori     | A  | B  | C  | D  | A  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft. Service1 | AuditLogs    | x1 | Y1 | z1 |    |    |    |    |    |    |
| Microsoft. Service1 | Günlüklerini    |    |    |    | Q1 | W1 | E1 |    |    |    |
| Microsoft. Service2 | AuditLogs    |    |    |    |    |    |    | j1 | K1 | L1 |
| Microsoft. Service1 | Günlüklerini    |    |    |    | üç | W2 | E2 |    |    |    |
| Microsoft. Service2 | AuditLogs    |    |    |    |    |    |    | j3 | k3 | L3 |
| Microsoft. Service1 | AuditLogs    | x5 | Y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Kaynağa özgü
Bu modda, seçilen çalışma alanındaki tek tablolar, tanılama ayarında seçilen her kategori için oluşturulur. Bu yöntem, günlük sorgularındaki verilerle çalışmayı çok daha kolay hale getiren, şemalar ve yapıları için daha iyi keşfedilebilirlik sağlayan, hem alma gecikmesi hem de sorgu saatlerinde performansı artıran ve bir üzerinde RBAC hakları verme olanağı sağladığından önerilir. Belirli tablo. Tüm Azure Hizmetleri, sonunda kaynağa özgü moda geçirilir. 

Yukarıdaki örnek, üç tablo oluşturulmasını neden olur:
 
- Tablo *Service1AuditLogs* aşağıdaki gibi:

    | Kaynak sağlayıcısı | Kategori | A | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | x1 | Y1 | z1 |
    | Service1 | AuditLogs | x5 | Y5 | z5 |
    | ... |

- Tablo *Service1ErrorLogs* aşağıdaki gibi:  

    | Kaynak sağlayıcısı | Kategori | D | A | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | Günlüklerini |  Q1 | W1 | E1 |
    | Service1 | Günlüklerini |  üç | W2 | E2 |
    | ... |

- Tablo *Service2AuditLogs* aşağıdaki gibi:  

    | Kaynak sağlayıcısı | Kategori | G | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | AuditLogs | j1 | K1 | L1|
    | Service2 | AuditLogs | j3 | k3 | L3|
    | ... |



### <a name="select-the-collection-mode"></a>Koleksiyon modunu seçin
Azure kaynaklarının çoğu, verileri bir seçim yapmadan **Azure tanılama** veya **kaynağa özgü modda** çalışma alanına yazar. Hangi modun kullandığı hakkında ayrıntılı bilgi edinmek için [her hizmet için belgelere](diagnostic-logs-schema.md) bakın. Tüm Azure Hizmetleri, sonunda kaynağa özgü modu kullanacaktır. Bu geçişin bir parçası olarak, bazı kaynaklar tanılama ayarında bir mod seçmenizi sağlayacak. Tüm yeni tanılama ayarları için kaynağa özgü modu belirtmeniz gerekir, çünkü bu, verilerin daha kolay yönetilmesini sağlar ve daha sonraki bir tarihte karmaşık geçişlere karşı kaçınmanıza yardımcı olabilir.
  
   ![Tanılama ayarları mod seçicisi](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> Şu anda, **Azure tanılama** ve **kaynağa özgü** mod yalnızca Azure Portal tanılama ayarı yapılandırılırken seçilebilir. Ayarı CLı, PowerShell veya REST API kullanarak yapılandırırsanız, varsayılan olarak **Azure tanılama**olur.

Mevcut bir tanılama ayarını kaynağa özgü mod olarak değiştirebilirsiniz. Bu durumda, zaten toplanmış olan veriler, çalışma alanının saklama ayarına göre kaldırılana kadar _AzureDiagnostics_ tablosunda kalır. Yeni veriler, ' de adanmış tabloya toplanacaktır. Her iki tablo genelinde verileri sorgulamak için [UNION](https://docs.microsoft.com/azure/kusto/query/unionoperator) işlecini kullanın.

Kaynağa özgü modu destekleyen Azure hizmetleri hakkında duyurular için [Azure Updates](https://azure.microsoft.com/updates/) blogunu izlemeye devam edin.

### <a name="column-limit-in-azurediagnostics"></a>AzureDiagnostics içinde sütun sınırı
Azure Izleyici günlüklerinde herhangi bir tablo için 500 Özellik sınırı vardır. Bu sınıra ulaşıldığında, ilk 500 dışında herhangi bir özelliği olan verileri içeren tüm satırlar alma zamanında bırakılır. *AzureDiagnostics* tablosu, bu sınıra yönelik olan tüm Azure hizmetlerinin özelliklerini içerdiğinden bu sınıra açıktır.

Tanılama günlüklerini birden çok hizmetten topluyorsanız, _AzureDiagnostics_ bu sınırı aşabilir ve veriler kaçırılacaktır. Tüm Azure hizmetleri kaynağa özgü modu destekleene kadar, 500 sütun sınırına ulaşma olasılığını azaltmak üzere kaynakları birden fazla çalışma alanına yazacak şekilde yapılandırmanız gerekir.

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory, çok ayrıntılı bir günlük kümesi nedeniyle, çok sayıda sütun yazmak bilinen ve olası _AzureDiagnostics_ nedeni olan bir hizmettir. Kaynağa özgü mod etkinleştirilmeden önce yapılandırılan tüm Tanılama ayarları için, her bir etkinliğe karşı benzersiz olarak adlandırılan her Kullanıcı parametresi için yeni bir sütun oluşturulur. Etkinlik girişlerinin ve çıktıların ayrıntılı doğası nedeniyle daha fazla sütun oluşturulacak.
 
Günlüklerinizi kaynağa özgü modu en kısa sürede kullanmak üzere geçirmeniz gerekir. Hemen bunu yapamadıysanız, geçici bir alternatif, bu günlüklerin çalışma alanınızda toplanmakta olan diğer günlük türlerini etkileme olasılığını en aza indirmek için Azure Data Factory günlüklerini kendi çalışma alanlarında yalıtmaktır.


## <a name="next-steps"></a>Sonraki adımlar

* Azure Kaynak günlükleri hakkında bilgi edinmek için bkz. [Azure Kaynak günlüklerine genel bakış](resource-logs-overview.md).
* Log Analytics çalışma alanına kaynak günlüklerini toplamak üzere bir tanılama ayarı oluşturmak için bkz. [Azure 'da günlükleri ve ölçümleri toplamak için tanılama ayarı oluşturma](diagnostic-settings.md).
