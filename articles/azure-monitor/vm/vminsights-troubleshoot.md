---
title: VM öngörüleri sorunlarını giderme
description: VM Insights yüklemesinde sorun giderin.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.custom: references_regions
ms.openlocfilehash: 59b4f38efde2416687702647031d2b37553ff8ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103555765"
---
# <a name="troubleshoot-vm-insights"></a>VM öngörüleri sorunlarını giderme
Bu makalede, VM öngörülerini etkinleştirirken veya kullanırken sorun yaşadığınızda sorun giderme bilgileri sağlanmaktadır.

## <a name="cannot-enable-vm-insights-on-a-machine"></a>Makine üzerinde VM öngörüleri etkinleştirilemiyor
Azure portal bir Azure sanal makinesini eklerken aşağıdaki adımlar oluşur:

- Bu seçenek belirlenmişse varsayılan bir Log Analytics çalışma alanı oluşturulur.
- Aracı zaten yüklüyse, Log Analytics Aracısı Azure sanal makinelerine bir VM uzantısı kullanılarak yüklenir.
- Bağımlılık Aracısı, gerekli olduğunu tespit ederseniz bir uzantı kullanılarak Azure sanal makinelerine yüklenir.
  
Ekleme işlemi sırasında, bu adımların her biri doğrulanır ve portalda bir bildirim durumu gösterilir. Çalışma alanının yapılandırması ve aracı yüklemesi genellikle 5 ila 10 dakika sürer. Verilerin portalda görüntülemesi için kullanılabilir olması 5 ila 10 dakika sürer.

Ekleme işlemini gerçekleştirdikten sonra sanal makinenin eklendi olması gerektiğini belirten bir ileti alırsanız, işlemin tamamlanması 30 dakikaya kadar bekleyin. Sorun devam ederse olası nedenler için aşağıdaki bölümlere bakın.

### <a name="is-the-virtual-machine-running"></a>Sanal makine çalışıyor mu?
 Sanal makine bir süredir kapatılmışsa, şu anda kapalı veya yalnızca yakın zamanda açıldıysa, veriler gelene kadar bir bit için görüntülenecek verilere sahip olmayacaktır.

### <a name="is-the-operating-system-supported"></a>İşletim sistemi destekleniyor mu?
İşletim sistemi [desteklenen işletim sistemleri](vminsights-enable-overview.md#supported-operating-systems) listesinde değilse, uzantı yüklenemeyecektir ve verilerin gelmesi için beklediğimiz bu iletiyi görürsünüz.

### <a name="did-the-extension-install-properly"></a>Uzantı düzgün şekilde yüklenmi?
Hala sanal makinenin eklendi olması gerektiğini belirten bir ileti görürseniz, uzantıların bir veya her ikisinin doğru yüklenemebileceği anlamına gelebilir. Aşağıdaki uzantıların listelendiğini doğrulamak için Azure portal sanal makinenizin **Uzantılar** sayfasını kontrol edin.

| İşletim sistemi | Aracılar | 
|:---|:---|
| Windows | MicrosoftMonitoringAgent<br>Microsoft. Azure. Monitoring. DependencyAgent |
| Linux | OMSAgentForLinux<br>DependencyAgentForLinux |

Yüklü uzantılar listesinde işletim sisteminiz için her iki uzantıyı görmüyorsanız, yüklenmesi gerekir. Uzantılar listeleniyorsa ancak durumları *sağlama başarılı* olarak görünmezse, uzantının kaldırılması ve yeniden yüklenmesi gerekir.

### <a name="do-you-have-connectivity-issues"></a>Bağlantı sorunlarınız var mı?
Windows makinelerinde, bağlantı sorununu belirlemek için  *Testcloudconnectivity* aracını kullanabilirsiniz. Bu araç varsayılan olarak *%systemroot%\Program Files\Microsoft Monitoring sagent* klasöründe aracıyla yüklenir. Aracı yükseltilmiş bir komut isteminden çalıştırın. Sonuçları döndürür ve testin başarısız olduğunu vurgulayacaktır. 

![TestCloudConnectivity aracı](media/vminsights-troubleshoot/test-cloud-connectivity.png)

### <a name="more-agent-troubleshooting"></a>Diğer aracı sorunlarını giderme

Log Analytics aracısıyla ilgili sorunları gidermek için aşağıdaki makalelere bakın:

- [Windows için Log Analytics aracısıyla ilgili sorunları giderme](../agents/agent-windows-troubleshoot.md)
- [Linux için Log Analytics aracısıyla ilgili sorunları giderme](../agents/agent-linux-troubleshoot.md)

## <a name="performance-view-has-no-data"></a>Performans görünümünde veri yok
Aracılar doğru şekilde yüklenmiş gibi görünüyorsa ancak performans görünümünde herhangi bir veri görmüyorsanız, olası nedenler için aşağıdaki bölümlere bakın.

### <a name="has-your-log-analytics-workspace-reached-its-data-limit"></a>Log Analytics çalışma alanınız veri sınırına ulaştı mi?
[Kapasite rezervasyonlarını ve veri alımı için fiyatlandırmayı](https://azure.microsoft.com/pricing/details/monitor/)kontrol edin.

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>Sanal makineniz Azure Izleyici günlüklerine günlük ve performans verileri gönderiyor mu?

Azure portal Azure Izleyici menüsünde **günlüklerden** Log Analytics açın. Bilgisayarınız için aşağıdaki sorguyu çalıştırın:

```kuso
Usage 
| where Computer == "my-computer" 
| summarize sum(Quantity), any(QuantityUnit) by DataType
```

Herhangi bir veri görmüyorsanız aracıda sorunlarla karşılaşabilirsiniz. Aracı sorun giderme bilgileri için yukarıdaki bölüme bakın.

## <a name="virtual-machine-doesnt-appear-in-map-view"></a>Sanal makine harita görünümünde görünmüyor

### <a name="is-the-dependency-agent-installed"></a>Bağımlılık Aracısı yüklendi mi?
 Bağımlılık aracısının yüklenip düzgün çalışıp çalışmadığını öğrenmek için yukarıdaki bölümlerde bulunan bilgileri kullanın.

### <a name="are-you-on-the-log-analytics-free-tier"></a>Log Analytics ücretsiz katmanda misiniz?
[Log Analytics ücretsiz katmanı](https://azure.microsoft.com/pricing/details/monitor/) bu, beş adede kadar benzersiz hizmet eşlemesi makineye izin veren eski bir fiyatlandırma plandır. Önceki beş makine artık veri göndermiyor olsa dahi sonradan ekleyeceğiniz makineler Hizmet Eşlemesi'nde görünmez.

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>Sanal makineniz Azure Izleyici günlüklerine günlük ve performans verileri gönderiyor mu?
[Performans görünümündeki](#performance-view-has-no-data) günlük sorgusunun, sanal makine için verilerin toplanmakta olup olmadığını anlamak için veri bölümü yoktur. Veriler toplanmıyorsa, bağlantı sorunlarınız olup olmadığını öğrenmek için yukarıda açıklanan TestCloudConnectivity aracını kullanın.


## <a name="virtual-machine-appears-in-map-view-but-has-missing-data"></a>Sanal makine harita görünümünde görünüyor, ancak veriler eksik
Sanal makine harita görünümteyde ise, bağımlılık Aracısı yüklenip çalışır, ancak çekirdek sürücü yüklenmez. Aşağıdaki konumlarda günlük dosyasını denetleyin:

| İşletim sistemi | Günlük | 
|:---|:---|
| Windows | C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log |
| Linux | /var/seçenek/Microsoft/Dependency-Agent/log/Service.log |

Dosyanın son satırları, çekirdeğin neden yüklenmediğini göstermelidir. Örneğin çekirdeğinizi güncelleştirdiyseniz yeni çekirdek için Linux desteği sunulmuyor olabilir.
## <a name="next-steps"></a>Sonraki adımlar

- VM Insights aracılarını ekleme hakkında ayrıntılı bilgi için bkz. [VM Insights 'a genel bakış](vminsights-enable-overview.md).
