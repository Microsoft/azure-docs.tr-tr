---
title: Azure Izleyici 'de çözüm Aracı Durumu | Microsoft Docs
description: Bu makale, doğrudan Log Analytics veya System Center Operations Manager ' a rapor veren aracılarınızın sistem durumunu izlemek için bu çözümü nasıl kullanacağınızı anlamanıza yardımcı olmaya yöneliktir.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 03/19/2017
ms.openlocfilehash: 5a48bbff89f0d6a0be9adf2ad242dbca41eec6db
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555326"
---
#  <a name="agent-health-solution-in-azure-monitor"></a>Azure Izleyici 'de Aracı Durumu çözümü
Azure 'daki Aracı Durumu çözümü, doğrudan Azure Izleyici 'de Log Analytics çalışma alanına veya Azure Izleyici 'ye bağlı bir System Center Operations Manager yönetim grubuna veya yanıt vermeyen ve işletimsel veriler gönderiliyor.  Ayrıca, kaç aracının dağıtıldığını, bunların coğrafi olarak nerelere dağıtıldığını da izleyebilir ve Azure’da, diğer bulut ortamlarında ya da şirket içinde dağıtılmış aracıların dağılımından her zaman haberdar olmaya yönelik diğer sorguları gerçekleştirebilirsiniz.    

## <a name="prerequisites"></a>Önkoşullar
Bu çözümü dağıtmadan önce, Log Analytics çalışma alanına rapor veren veya çalışma alanınız ile tümleştirilmiş bir [Operations Manager yönetim grubuna](../../azure-monitor/platform/om-agents.md) raporlama için şu anda desteklenen [Windows aracılarını](../../log-analytics/log-analytics-windows-agent.md) doğrulayın.

## <a name="solution-components"></a>Çözüm bileşenleri
Bu çözüm, çalışma alanınıza eklenen aşağıdaki kaynaklardan ve doğrudan bağlanılan aracılardan veya Operations Manager bağlantılı yönetim grubundan oluşur.

### <a name="management-packs"></a>Yönetim paketleri
System Center Operations Manager yönetim grubunuz bir Log Analytics çalışma alanına bağlıysa, aşağıdaki yönetim paketleri Operations Manager yüklenir.  Bu çözüm eklendikten sonra bu yönetim paketleri doğrudan bağlı Windows bilgisayarlarına da yüklenir. Bu yönetim paketlerinde yapılandırılacak veya yönetilecek hiçbir şey yoktur.

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack  (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Çözüm yönetim paketlerini güncelleştirme hakkında daha fazla bilgi için bkz. [Operations Manager'ı Log Analytics’e Bağlama](../../azure-monitor/platform/om-agents.md).

## <a name="configuration"></a>Yapılandırma
Çözüm [ekleme](solutions.md)bölümünde açıklanan işlemi kullanarak Log Analytics çalışma alanınıza Aracı durumu çözümünü ekleyin. Başka bir yapılandırma işlemi gerekmez.


## <a name="data-collection"></a>Veri toplama
### <a name="supported-agents"></a>Desteklenen aracılar
Aşağıdaki tabloda bu çözüm tarafından desteklenen bağlı kaynaklar açıklanmaktadır.

| Bağlı Kaynak | Desteklenen | Açıklama |
| --- | --- | --- |
| Windows aracıları | Yes | Sinyal olayları doğrudan Windows aracılarından toplanır.|
| System Center Operations Manager yönetim grubu | Yes | Sinyal olayları, her 60 saniyede bir yönetim grubuna rapor veren aracılardan toplanır ve sonra Azure Izleyici 'ye iletilir. Operations Manager aracılarından Azure Izleyici 'ye doğrudan bağlantı gerekli değildir. Sinyal olayı verileri yönetim grubundan Log Analytics çalışma alanına iletilir.|

## <a name="using-the-solution"></a>Çözümü kullanma
Çözümü Log Analytics çalışma alanınıza eklediğinizde **Aracı durumu** kutucuk panonuza eklenir. Bu kutucuk, son 24 saat içindeki toplam aracı sayısını ve yanıt vermeyen aracı sayısını gösterir.<br><br> ![Panodaki Aracı Durumu Çözüm kutucuğu](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

**Aracı Durumu** kutucuğuna tıklayarak **Aracı Durumu** panosunu açın.  Pano aşağıdaki tabloda gösterilen sütunları içerir. Her sütunda, ilgili sütunun belirtilen zaman aralığına ilişkin ölçütlerle eşleşen ilk on olay sayılarına göre listelenir. Her sütunun sağ alt tarafındaki **Tümünü görüntüle**’yi seçerek ya da sütun başlığına tıklayarak listenin tamamını sağlayan bir günlük araması çalıştırabilirsiniz.

| Sütun | Açıklama |
|--------|-------------|
| Zaman içinde aracı sayısı | Hem Linux hem de Windows aracıları için yedi günlük bir dönem boyunca aracı sayınızın eğilimi.|
| Yanıt vermeyen aracı sayısı | Son 24 saat içinde bir sinyal göndermemiş tüm aracıların listesi.|
| İşletim Sistemi Türüne Göre Dağılım | Ortamınızda kaçar tane Windows ve Linux aracısı olduğuna ilişkin bir bölüm.|
| Aracı Sürümüne Göre Dağılım | Ortamınızda yüklü olan farklı aracı sürümlerine ve her birinin sayısına ilişkin bir bölüm.|
| Aracı Kategorisine Göre Dağılım | Sinyal olayları gönderen farklı kategorilerdeki araçlara ilişkin bir bölüm: Doğrudan aracılar, OpsMgr aracıları veya OpsMgr Management Server.|
| Yönetim Grubuna Göre Dağılım | Ortamınızdaki farklı Operations Manager yönetim gruplarının bir bölümü.|
| Aracıların coğrafi konumu | Aracılarınızın bulunduğu farklı ülkelerin/bölgelerin bir bölümü ve her bir ülkede/bölgede yüklenmiş olan aracıların toplam sayısı.|
| Yüklü Ağ Geçidi Sayısı | Log Analytics ağ geçidinin yüklü olduğu sunucu sayısı ve bu sunucuların listesi.|

![Aracı Durumu Çözüm panosu örneği](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Azure Izleyici günlük kayıtları
Çözüm Log Analytics çalışma alanında bir kayıt türü oluşturur.  

### <a name="heartbeat-records"></a>Sinyal kayıtları
**Sinyal** türünde bir kayıt oluşturulur.  Bu kayıtlar aşağıdaki tabloda gösterilen özelliklere sahiptir.  

| Özellik | Açıklama |
| --- | --- |
| `Type` | *Sinyal*|
| `Category` | Değer *Doğrudan Aracı*, *SCOM Aracısı* veya *SCOM Yönetim Sunucusu*’dur.|
| `Computer` | Bilgisayar adı.|
| `OSType` | Windows veya Linux işletim sistemi.|
| `OSMajorVersion` | İşletim sistemi ana sürümü.|
| `OSMinorVersion` | İşletim sistemi alt sürümü.|
| `Version` | Log Analytics Aracısı veya Operations Manager Aracısı sürümü.|
| `SCAgentChannel` | Değer *Doğrudan* ve/veya *SCManagementServer*’dır.|
| `IsGatewayInstalled` | Log Analytics ağ geçidi yüklüyse, değer *true*'dur, aksi takdirde değer *false 'tur*.|
| `ComputerIP` | Bilgisayarın IP adresi.|
| `RemoteIPCountry` | Bilgisayarın dağıtıldığı coğrafi konum.|
| `ManagementGroupName` | Operations Manager yönetim grubunun adı.|
| `SourceComputerId` | Bilgisayarın benzersiz kimliği.|
| `RemoteIPLongitude` | Bilgisayarın coğrafi konumunun boylamı.|
| `RemoteIPLatitude` | Bilgisayarın coğrafi konumunun enlemi.|

Bir Operations Manager yönetim sunucusuna raporlama yapılan her aracı iki sinyal gönderir ve sahip olduğunuz veri kaynaklarına ve izleme çözümlerine bağlı olarak, SCAgentChannel özelliğinin değeri hem **doğrudan** hem de **scmanagementserver** 'ı içerir aboneliğinizde etkinleştirildi. Geri çağırma yaparsanız, çözümlerdeki veriler doğrudan bir Operations Manager yönetim sunucusundan Azure Izleyicisine gönderilir ya da aracıda toplanan verilerin hacmi nedeniyle doğrudan aracıdan Azure Izleyici 'ye gönderilir. **SCManagementServer** değerine sahip sinyal olayları için ComputerIP değeri, verileri aslında karşıya yükleyen yönetim sunucusunun IP adresidir.  SCAgentChannel’ın **Doğrudan** olarak ayarlandığı sinyaller için bu adres, aracının genel IP adresidir.  

## <a name="sample-log-searches"></a>Örnek günlük aramaları
Aşağıdaki tabloda, bu çözüm tarafından toplanan kayıtlara ilişkin örnek günlük aramaları sunulmaktadır.

| Sorgu | Açıklama |
|:---|:---|
| Heartbeat &#124; distinct Computer |Toplam aracı sayısı |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Son 24 saat içinde yanıt vermeyen aracı sayısı |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Son 15 dakika içinde yanıt vermeyen aracı sayısı |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Çevrimiçi olan bilgisayarlar (son 24 saat) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Son 30 dakika içinde Toplam Çevrimdışı Aracı Sayısı (son 24 saat için) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |İşletim sistemi türüne göre zaman içinde aracı sayısı eğilimini görün|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |İşletim Sistemi Türüne Göre Dağılım |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Aracı Sürümüne Göre Dağılım |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Aracı Kategorisine Göre Dağılım |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Yönetim Grubuna Göre Dağılım |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Aracıların coğrafi konumu |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Yüklü Log Analytics ağ geçidi sayısı |




## <a name="next-steps"></a>Sonraki adımlar

* Günlük sorgularından uyarı oluşturma hakkında ayrıntılı bilgi için [Azure izleyici 'Deki uyarılar](../platform/alerts-overview.md) hakkında bilgi edinin. 
