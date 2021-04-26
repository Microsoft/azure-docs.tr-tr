---
title: Azure DDoS koruması standart raporları ve akış günlükleri
description: Raporların ve akış günlüklerinin nasıl yapılandırılacağını öğrenin.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 7f8e3df927b74cff7e4dc8bf1456600740c07088
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567687"
---
# <a name="view-and-configure-ddos-diagnostic-logging"></a>DDoS tanılama günlüğünü görüntüleme ve yapılandırma

Azure DDoS koruması standardı, DDoS saldırı analiziyle ayrıntılı saldırı öngörüleri ve görselleştirmeleri sağlar. Sanal ağlarını DDoS saldırılarına karşı koruyan müşteriler, saldırı saldırılarına karşı saldırı ve risk azaltma & raporları aracılığıyla saldırının etkilerini azaltmak için gerçekleştirilen saldırı ve eylemler hakkında ayrıntılı görünürlük sağlar. Zengin telemetri, DDoS saldırısının süresi boyunca ayrıntılı ölçümler dahil olmak üzere Azure Izleyici aracılığıyla sunulur. DDos Koruması tarafından sunulan tüm Azure İzleyici ölçümleri için uyarı yapılandırılabilir. Günlüğe kaydetme, Azure Izleme tanılama arabirimi aracılığıyla [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), splunk (Azure Event Hubs), OMS Log Analytics ve gelişmiş analiz Için Azure depolama ile daha da tümleştirilebilir.

Aşağıdaki tanılama günlükleri Azure DDoS koruma standardı için kullanılabilir: 

- **Ddosprotectionnotifications**: bildirimler, BIR genel IP kaynağı saldırı altında olduğunda ve saldırı azalmasına açık olduğunda sizi bilgilendirir.
- **DDoSMitigationFlowLogs**: saldırı risk azaltma akış günlükleri, etkin bir DDoS saldırısı sırasında bırakılan trafiği, iletilen trafiği ve diğer ilgi çekici veri noktalarını neredeyse gerçek zamanlı olarak incelemenizi sağlar. Bu verilerin sabit akışını Azure Sentinel 'e veya üçüncü taraf SıEM sistemlerine, neredeyse gerçek zamanlı izleme için Olay Hub 'ı aracılığıyla alabilir, olası eylemleri gerçekleştirebilir ve savunma operasyonlarınızın gereksinimini ortadan kaldırabilirsiniz.
- **DDoSMitigationReports**: saldırı risk azaltma raporları, kaynak üzerinde saldırı hakkında ayrıntılı bilgi sağlamak Için toplanan Netflow protokol verilerini kullanır. Genel bir IP kaynağı saldırı altında olduğunda, risk azaltma başladıktan sonra rapor oluşturma başlatılır. Her 5 dakikada bir ve risk azaltma süresi boyunca bir azaltma sonrası raporuyla oluşturulan bir artımlı rapor olacaktır. Bu, DDoS saldırısının daha uzun bir süre boyunca devam ettiğinden emin olmak için, risk azaltma süresi bittikten sonra, her 5 dakikada bir azaltma raporunun en güncel anlık görüntüsünü ve tüm Özeti görüntüleyebileceksiniz. 
- **Allölçümlerini**: DDoS saldırısının süresi boyunca kullanılabilir tüm olası ölçümleri sağlar. 

Bu öğreticide aşağıdakilerin nasıl yapılacağını öğreneceksiniz:

> [!div class="checklist"]
> * Bildirimler, risk azaltma raporları ve azaltma akışı günlükleri dahil olmak üzere DDoS tanılama günlüklerini yapılandırın. 
> * Tanımlı bir kapsamdaki tüm genel IP 'lerde tanılama günlüğünü etkinleştirin.
> * Çalışma kitaplarında günlük verilerini görüntüleyin.

## <a name="prerequisites"></a>Ön koşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
- Bu öğreticideki adımları tamamlayabilmeniz için önce bir [Azure DDoS standart koruma planı](manage-ddos-protection.md) oluşturmanız ve DDoS koruma standardının bir sanal ağ üzerinde etkinleştirilmesi gerekir.
- DDoS, bir sanal ağ içindeki kaynaklara atanan genel IP adreslerini izler. Sanal ağda genel IP adresi olan hiç kaynağınız yoksa önce genel IP adresiyle bir kaynak oluşturmanız gerekir. Azure App Service ortamları dışında, [Azure hizmetleri Için sanal ağda](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (arka uç sanal makinelerin sanal ağda bulunduğu Azure yük dengeleyiciler dahil) (klasik değil), Kaynak Yöneticisi aracılığıyla dağıtılan tüm KAYNAKLARıN genel IP adresini izleyebilirsiniz. Bu öğreticiye devam etmek için hızlı bir şekilde [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sanal makinesi oluşturabilirsiniz.    

## <a name="configure-ddos-diagnostic-logs"></a>DDoS tanılama günlüklerini yapılandırma

Bir ortamdaki tüm genel IP 'lerde tanılama günlüğünü otomatik olarak etkinleştirmek istiyorsanız, [tüm genel IP 'lerde tanılama günlüğünü etkinleştir](#enable-diagnostic-logging-on-all-public-ips)' i atlayın.

1. Portalın sol üst kısmında **Tüm hizmetler**'i seçin.
2. *Filtre* kutusuna **İzleyici**'yi girin. Sonuçlarda **İzleyici** görüntülendiğinde bunu seçin.
3. **Ayarlar**'ın altında **Tanılama Ayarları**'nı seçin.
4. Günlüğe kaydetmek istediğiniz genel IP'yi içeren **Abonelik** ve **Kaynak grubunu** seçin.
5. **Kaynak türü** IÇIN **genel IP adresi** ' ni seçin ve ardından günlükleri ETKINLEŞTIRMEK istediğiniz belirli genel IP adresini seçin.
6. **Tanılama ayarı ekle**’yi seçin. **Kategori Ayrıntıları**'nın altında size gereken tüm seçenekleri belirtin ve ardından **Kaydet**'i seçin.

    ![DDoS Tanılama ayarları](./media/ddos-attack-telemetry/ddos-diagnostic-settings.png)

7. **Hedef ayrıntıları**' nın altında, gerek duyduğunuz sayıda aşağıdaki seçenekten birini seçin:

    - **Bir depolama hesabına arşivleme**: veriler bir Azure depolama hesabına yazılır. Bu seçenek hakkında daha fazla bilgi edinmek için bkz. [Arşiv kaynak günlükleri](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage).
    - **Bir olay hub 'ına akış**: günlük alıcının bir Azure Olay Hub 'ı kullanarak günlükleri seçmesine izin verir. Olay Hub 'ları, splunk veya diğer SıEM sistemleriyle tümleştirmeyi etkinleştirir. Bu seçenek hakkında daha fazla bilgi edinmek için bkz. [kaynak günlüklerini bir olay hub 'ına akış](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).
    - **Log Analytics gönder**: günlükleri Azure izleyici hizmetine yazar. Bu seçenek hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici günlüklerinde kullanılacak günlükleri toplama](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace).

### <a name="log-schemas"></a>Günlük şemaları

Aşağıdaki tabloda alan adları ve açıklamaları listelenmektedir:

# <a name="ddosprotectionnotifications"></a>[DDoSProtectionNotifications](#tab/DDoSProtectionNotifications)

| Alan adı | Description |
| --- | --- |
| **TimeGenerated** | Bildirimin oluşturulduğu UTC Tarih ve saati. |
| **ResourceId** | Genel IP adresinizin kaynak KIMLIĞI. |
| **Kategori** | Bildirimler için bu olacaktır `DDoSProtectionNotifications` .|
| **Kaynak** | Genel IP adresinizi ve Sanal ağınızı içeren kaynak grubu. |
| **SubscriptionID** | DDoS koruma planı abonelik KIMLIĞINIZ. |
| **Kaynak** | Genel IP adresinizin adı. |
| **ResourceType** | Bu her zaman olur `PUBLICIPADDRESS` . |
| **OperationName** | Bildirimler için bu olacaktır `DDoSProtectionNotifications` .  |
| **İleti** | Saldırının ayrıntıları. |
| **Tür** | Bildirim türü. Olası değerler şunlardır `MitigationStarted` . `MitigationStopped`. |
| **PublicIpAddress** | Genel IP adresiniz. |

# <a name="ddosmitigationflowlogs"></a>[DDoSMitigationFlowLogs](#tab/DDoSMitigationFlowLogs)

| Alan adı | Description |
| --- | --- |
| **TimeGenerated** | Akış günlüğünün oluşturulduğu saat UTC Tarih ve saati. |
| **ResourceId** | Genel IP adresinizin kaynak KIMLIĞI. |
| **Kategori** | Akış günlükleri için bu olacaktır `DDoSMitigationFlowLogs` .|
| **Kaynak** | Genel IP adresinizi ve Sanal ağınızı içeren kaynak grubu. |
| **SubscriptionID** | DDoS koruma planı abonelik KIMLIĞINIZ. |
| **Kaynak** | Genel IP adresinizin adı. |
| **ResourceType** | Bu her zaman olur `PUBLICIPADDRESS` . |
| **OperationName** | Akış günlükleri için bu olacaktır `DDoSMitigationFlowLogs` . |
| **İleti** | Saldırının ayrıntıları. |
| **Sourcepublicıpaddress** | Genel IP adresinize trafik oluşturan istemcinin genel IP adresi. |
| **SourcePort** | 0 ile 65535 arasında bir bağlantı noktası numarası. |
| **Destpublicıpaddress** | Genel IP adresiniz. |
| **DestPort** | 0 ile 65535 arasında bir bağlantı noktası numarası. |
| **Protokol** | Protokol türü. Olası değerler şunlardır `tcp` , `udp` , `other` .|

# <a name="ddosmitigationreports"></a>[DDoSMitigationReports](#tab/DDoSMitigationReports)

| Alan adı | Description |
| --- | --- |
| **TimeGenerated** | Raporun oluşturulduğu UTC Tarih ve saati. |
| **ResourceId** | Genel IP adresinizin kaynak KIMLIĞI. |
| **Kategori** | Bildirimler için bu olacaktır `DDoSProtectionNotifications` .|
| **Kaynak** | Genel IP adresinizi ve Sanal ağınızı içeren kaynak grubu. |
| **SubscriptionID** | DDoS koruma planı abonelik KIMLIĞINIZ. |
| **Kaynak** | Genel IP adresinizin adı. |
| **ResourceType** | Bu her zaman olur `PUBLICIPADDRESS` . |
| **OperationName** | Risk azaltma raporları için bu olacaktır `DDoSMitigationReports` . |
| **ReportType** | Olası değerler şunlardır `Incremental` `PostMitigation` .|
| **MitigationPeriodStart** | Risk azaltma başladığında UTC olarak tarih ve saat.  |
| **MitigationPeriodEnd** | Risk azaltma sona erdiği zaman UTC olarak tarih ve saat. |
| **IPAddress** | Genel IP adresiniz. |
| **Kvektöri saldırın** |  Saldırı türlerinin dökümünü yapın. Anahtarlar şunlardır,,,, `TCP SYN flood` `TCP flood` `UDP flood` `UDP reflection` `Other packet flood` .|
| **TrafficOverview** |  Saldırı trafiğinin dökümünü yapın. Anahtarlar şunlardır,,,,,, `Total packets` `Total packets dropped` `Total TCP packets` `Total TCP packets dropped` `Total UDP packets` `Total UDP packets dropped` `Total Other packets` , `Total Other packets dropped` . |
| **Protokoller** | Dahil edilen protokollerin dökümü. Anahtarlar şunlardır `TCP` , `UDP` , `Other` . |
| **Açılan nedenler** | Bırakılan paketlerin nedenlerinin dökümü. Anahtarlar şunlardır,,,,,,, `Protocol violation invalid TCP syn` `Protocol violation invalid TCP` `Protocol violation invalid UDP` `UDP reflection` `TCP rate limit exceeded` `UDP rate limit exceeded` `Destination limit exceeded` `Other packet flood` , `Rate limit exceeded` , `Packet was forwarded to service` . |
| **Topsourceülkeleriyle** | Gelen trafiğin ilk 10 kaynak ülkelerinden oluşan döküm. |
| **TopSourceCountriesForDroppedPackets** | En çok karşılaşılan saldırı trafiği için ilk 10 kaynak ülkelerin dökümü. |
| **TopSourceASNs** | Gelen trafiğin ilk 10 kaynak otonom sistem numarası (ASN) dökümü.  |
| **Kaynak kıtalar** | Gelen trafiğin kaynak kıtalarını çözümleme. |
***

## <a name="enable-diagnostic-logging-on-all-public-ips"></a>Tüm genel IP 'lerde tanılama günlüğünü etkinleştirme

Bu [şablon](https://aka.ms/ddosdiaglogs) , tanımlı bir kapsamdaki tüm genel IP günlüklerinde tanılama günlüğünü otomatik olarak etkinleştirmek Için bir Azure ilke tanımı oluşturur.

[![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%20DDoS%20Protection%2FPolicy%20-%20DDOS%20Enable%20Diagnostic%20Logging%2FAzure%20Policy%2FDDoSLogs.json)

## <a name="view-log-data-in-workbooks"></a>Çalışma kitaplarında günlük verilerini görüntüleme

### <a name="azure-sentinel-data-connector"></a>Azure Sentinel veri Bağlayıcısı

Günlükleri Azure Sentinel 'e bağlayabilirsiniz, çalışma kitaplarında verilerinizi görüntüleyip analiz edebilir, özel uyarılar oluşturabilir ve bunları araştırma süreçlerine ekleyebilirsiniz. Azure Sentinel 'e bağlanmak için bkz. [Azure Sentinel 'e bağlanma](../sentinel/connect-azure-ddos-protection.md). 

![Azure Sentinel DDoS Bağlayıcısı](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Azure DDoS koruması çalışma kitabı

[Bu Azure Resource Manager (ARM) şablonunu](https://aka.ms/ddosworkbook) , bir saldırı Analizi çalışma kitabını dağıtmak için kullanabilirsiniz. Bu çalışma kitabı, farklı filtrelenebilir panolar genelinde saldırı verilerini görselleştirmenize olanak tanır. 

[![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%20DDoS%20Protection%2FWorkbook%20-%20Azure%20DDOS%20monitor%20workbook%2FAzureDDoSWorkbook_ARM.json)

![DDoS koruması çalışma kitabı](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

## <a name="validate-and-test"></a>Doğrula ve test et

Günlüklerinizi doğrulamak için DDoS saldırısının benzetimini yapmak için bkz. [DDoS algılamayı doğrulama](test-through-simulations.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

- Bildirimler, risk azaltma raporları ve azaltma akışı günlükleri dahil olmak üzere DDoS tanılama günlüklerini yapılandırın. 
- Tanımlı bir kapsamdaki tüm genel IP 'lerde tanılama günlüğünü etkinleştirin.
- Çalışma kitaplarında günlük verilerini görüntüleyin.

Saldırı uyarılarını yapılandırma hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [DDoS konuma uyarılarını görüntüleme ve yapılandırma](alerts.md)
