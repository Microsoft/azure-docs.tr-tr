---
title: Ortak yük dengeleyici için işlemleri, olayları ve sayaçları izleme
titleSuffix: Azure Load Balancer
description: Azure Load Balancer için günlük kaydını etkinleştirmeyi öğrenin.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: 36937ace82d2bd8d4317f90a375042de10fe719f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709827"
---
# <a name="azure-monitor-logs-for-azure-standard-load-balancer"></a>Azure Standart Load Balancer Azure Izleyici günlükleri

Azure Standart Load Balancer yönetmek ve sorunlarını gidermek için farklı türlerde Azure Izleyici günlüklerini kullanabilirsiniz. Günlükler bir olay hub 'ına veya Log Analytics çalışma alanına akışla eklenebilir. Azure Blob depolama alanındaki tüm günlükleri ayıklayabilir ve bunları Excel ve Power BI gibi araçlarla görüntüleyebilirsiniz. 

Günlüklerin türleri şunlardır:

* **Etkinlik günlükleri:** Azure aboneliklerinize gönderilen tüm etkinlikleri, bunların durumuyla birlikte görüntüleyebilirsiniz. Daha fazla bilgi için bkz. [kaynaklardaki eylemleri izlemek için etkinlik günlüklerini görüntüleme](../azure-resource-manager/management/view-activity-logs.md). Etkinlik günlükleri varsayılan olarak etkindir ve Azure portal görüntülenebilir. Bu Günlükler hem Azure temel Load Balancer hem de Standart Load Balancer için kullanılabilir.
* **Standart Load Balancer ölçümleri:** Bu günlüğü, Standart Load Balancer Günlükler olarak dışarıya aktarılmış ölçümleri sorgulamak için kullanabilirsiniz. Bu günlükler yalnızca Standart Load Balancer için kullanılabilir.

> [!IMPORTANT]
> Durum araştırması ve Load Balancer uyarı olay günlükleri Şu anda işlevsel değildir ve [Azure Load Balancer ilgili bilinen sorunlarda](whats-new.md#known-issues)listelenmiştir. 

> [!IMPORTANT]
> Günlükler yalnızca Azure Resource Manager dağıtım modelinde dağıtılan kaynaklar için kullanılabilir. Klasik dağıtım modelinde kaynaklar için günlük kullanamazsınız. Dağıtım modelleri hakkında daha fazla bilgi için bkz. [Kaynak Yöneticisi dağıtımı ve klasik dağıtımı anlama](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Günlü kaydını etkinleştir

Etkinlik günlüğü tüm Kaynak Yöneticisi kaynakları için otomatik olarak etkinleştirilir. Bu Günlükler aracılığıyla kullanılabilir verileri toplamaya başlamak için olay ve durum araştırma günlüğünü etkinleştirin. Aşağıdaki adımları kullanın:

1. [Azure portalında](https://portal.azure.com) oturum açın. Zaten bir yük dengeleyiciniz yoksa devam etmeden önce [yük dengeleyici oluşturun](./quickstart-load-balancer-standard-public-portal.md) .
1. Portalda **kaynak grupları**' nı seçin.
2. **\<resource-group-name>** Yük dengeleyicinizin nerede olduğunu seçin.
3. Yük dengeleyicinizi seçin.
4. **Etkinlik günlüğü**  >  **Tanılama ayarları**' nı seçin.
5. **Tanılama ayarları** bölmesinde, **Tanılama ayarları** altında **+ Tanılama ayarı Ekle**' yi seçin.
6. **Tanılama ayarları** oluşturma bölmesinde, **ad** kutusuna **mylbdiagnostics** ' i girin.
7. **Tanılama ayarları** için üç seçeneğiniz vardır. Bunlardan birini, ikisini veya üçünü seçebilirsiniz ve her birini gereksinimleriniz için yapılandırabilirsiniz:

   * **Bir depolama hesabına arşivleme**. Bu işlem için zaten oluşturulmuş bir depolama hesabınız olması gerekir. Depolama hesabı oluşturmak için bkz. [depolama hesabı oluşturma](../storage/common/storage-account-create.md?tabs=azure-portal).
     1. **Depolama hesabına Arşivle** onay kutusunu seçin.
     2. **Depolama hesabı seç** bölmesini açmak için **Yapılandır** ' ı seçin.
     3. **Abonelik** açılan listesinde, depolama hesabınızın oluşturulduğu aboneliği seçin.
     4. **Depolama hesabı** açılır listesinde, depolama hesabınızın adını seçin.
     5. **Tamam**’ı seçin.

   * **Bir olay hub 'ına akış**. Bu işlem için önceden oluşturulmuş bir olay hub 'ı gerekir. Bir olay hub 'ı oluşturmak için bkz. [hızlı başlangıç: Azure Portal kullanarak bir olay hub 'ı oluşturma](../event-hubs/event-hubs-create.md).
     1. **Bir olay hub 'ının akışını** seçin onay kutusu.
     2. **Olay Hub 'ını Seç** bölmesini açmak için **Yapılandır** ' ı seçin.
     3. **Abonelik** açılan listesinde, Olay Hub 'ınızın oluşturulduğu aboneliği seçin.
     4. **Olay Hub 'ı ad alanı seç** aşağı açılan listesinde, ad alanını seçin.
     5. **Olay Hub 'ı ilke adı Seç** aşağı açılan listesinde, adı seçin.
     6. **Tamam**’ı seçin.

   * **Log Analytics gönder**. Bu işlem için oluşturulmuş ve yapılandırılmış bir Log Analytics çalışma alanınız olması gerekir. Log Analytics çalışma alanı oluşturmak için, bkz. [Azure portal Log Analytics çalışma alanı oluşturma](../azure-monitor/logs/quick-create-workspace.md).
     1. **Log Analytics’e gönder** onay kutusunu seçin.
     2. **Abonelik** açılan listesinde Log Analytics çalışma alanınızın olduğu aboneliği seçin.
     3. **Log Analytics çalışma alanı** açılır listesinde, çalışma alanını seçin.

8. **Tanılama ayarları** bölmesinin **ölçüm** bölümünde **allölçümler** onay kutusunu seçin.

9. Her şeyin doğru göründüğünü doğrulayın ve ardından **Tanılama ayarları** oluşturma bölmesinin en üstünde bulunan **Kaydet** ' i seçin.

## <a name="view-and-analyze-the-activity-log"></a>Etkinlik günlüğünü görüntüleme ve analiz etme

Etkinlik günlüğü varsayılan olarak oluşturulur. [Bu makaledeki yönergeleri izleyerek](../azure-monitor/essentials/activity-log.md), aboneliği bir abonelik düzeyinde aktarılacak şekilde yapılandırabilirsiniz. [Kaynaklardaki eylemleri izlemek için etkinlik günlüklerini görüntüle](../azure-resource-manager/management/view-activity-logs.md) makalesindeki bu Günlükler hakkında daha fazla bilgi edinin makalesini okuyun.

Aşağıdaki yöntemlerden birini kullanarak, etkinlik günlüğü verilerini görüntüleyebilir ve çözümleyebilirsiniz:

* **Azure Araçları:** Azure PowerShell, Azure CLı, Azure REST API veya Azure portal aracılığıyla etkinlik günlüğünden bilgi alın. [Kaynak Yöneticisi makalesinde denetim işlemleri](../azure-resource-manager/management/view-activity-logs.md) , her yöntem için adım adım yönergeler sağlar.
* **Power BI:** Zaten bir [Power BI](https://powerbi.microsoft.com/pricing) hesabınız yoksa ücretsiz olarak deneyebilirsiniz. [Power BI Için Azure denetim günlükleri tümleştirmesini](https://powerbi.microsoft.com/integrations/azure-audit-logs/)kullanarak verilerinizi önceden yapılandırılmış panolar ile çözümleyebilirsiniz. Ya da, görünümleri gereksinimlerinize uyacak şekilde özelleştirebilirsiniz.

## <a name="view-and-analyze-metrics-as-logs"></a>Ölçümleri günlük olarak görüntüleyin ve çözümleyin
Azure Izleyici 'de dışarı aktarma işlevini kullanarak Load Balancer ölçümlerini dışarı aktarabilirsiniz. Bu ölçümler, her bir dakikalık örnekleme aralığı için bir günlük girişi oluşturur.

Her kaynak düzeyinde, ölçümleri günlüklere dışarı aktarma etkinleştirilir. Bu günlükleri etkinleştirmek için:

1. **Tanılama ayarları** bölmesine gidin.
1. Kaynak grubuna göre filtreleyin ve ardından ölçüm dışarı aktarılmasını etkinleştirmek istediğiniz Load Balancer örneğini seçin. 
1. Load Balancer için Tanılama ayarları sayfası yukarı ayarlandığında, uygun ölçümleri günlük olarak dışarı aktarmak için **Allölçümleri** ' ni seçin.

Ölçüm dışarı aktarma sınırlamaları için, bu makalenin [sınırlamalar](#limitations) bölümüne bakın.

Standart Load Balancer tanılama ayarlarında **Allölçümlerini** etkinleştirdikten sonra, bir olay hub 'ı veya Log Analytics çalışma alanı kullanıyorsanız, bu Günlükler **AzureMonitor** tablosunda doldurulur. 

Depolama alanına veriyorsanız depolama hesabınıza bağlanın ve olay ve durum araştırma günlükleri için JSON günlüğü girdilerini alın. JSON dosyalarını indirdikten sonra bunları CSV 'ye dönüştürebilir ve bunları Excel, Power BI veya başka bir veri görselleştirme aracında görüntüleyebilirsiniz. 

> [!TIP]
> Visual Studio ve C# ' deki sabitler ve değişkenler için değer değiştirme temel kavramları hakkında bilgi sahibiyseniz, GitHub 'da bulunan [günlük dönüştürücü araçlarını](https://github.com/Azure-Samples/networking-dotnet-log-converter) kullanabilirsiniz.

## <a name="stream-to-an-event-hub"></a>Bir olay hub'ına akış yap
Tanılama bilgileri bir olay hub 'ına akış yapıldığında, Azure Izleyici tümleştirmesiyle bir iş ortağı SıEM aracında Merkezi günlük analizi için bunu kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure izleme verilerini bir olay hub 'ına akış](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

## <a name="send-to-log-analytics"></a>Log Analytics’e gönderme
Azure 'daki kaynaklar için tanılama bilgilerini doğrudan bir Log Analytics çalışma alanına gönderebilirsiniz. Bu çalışma alanında, sorun giderme ve analiz amacıyla bilgilere karşı karmaşık sorgular çalıştırabilirsiniz. Daha fazla bilgi için bkz. Azure [izleyici 'de Log Analytics çalışma alanında Azure Kaynak günlüklerini toplama](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

## <a name="limitations"></a>Sınırlamalar
Azure Load Balancer için ölçülerle günlüklere dışarı aktarma özelliği aşağıdaki sınırlamalara sahiptir:
* Ölçümler, şu anda Günlükler olarak verildiğinde iç adlar aracılığıyla görüntülenir. Eşlemeyi aşağıdaki tabloda bulabilirsiniz.
* Ölçümlerin boyutlılık korunmaz. Örneğin, **Dipavailability** (durum araştırma durumu) gibi ölçümler sayesinde arka uç IP adresine göre bölünemez veya görüntüleyemezsiniz.
* Kullanılan SNAT bağlantı noktaları ve ayrılmış SNAT bağlantı noktaları için ölçümler Şu anda Günlükler olarak dışa aktarma için kullanılamaz.

## <a name="next-steps"></a>Sonraki adımlar
* [Yük dengeleyiciniz için kullanılabilir ölçümleri gözden geçirin](./load-balancer-standard-diagnostics.md)
* [Azure Izleyici yönergelerini izleyerek sorgular oluşturma ve test etme](../azure-monitor/logs/log-query-overview.md)