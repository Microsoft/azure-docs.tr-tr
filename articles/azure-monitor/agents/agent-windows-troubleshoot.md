---
title: Windows için Log Analytics Aracısı sorunlarını giderme
description: Azure Izleyici 'de Windows için Log Analytics Aracısı ile ilgili en yaygın sorunların belirtilerini, nedenlerini ve çözümlemesini açıklama.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 7ab67602ebba2ae5446ecc0052ef4b03bba1e1bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952992"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Windows için Log Analytics aracısıyla ilgili sorunları giderme 

Bu makalede, Azure Izleyici 'de Windows için Log Analytics aracısında karşılaşabileceğiniz sorunları gidermeye yönelik yardım ve bunları çözmek için olası çözümler sunulmaktadır.

Bu adımlardan hiçbiri sizin için çalışmazsa, aşağıdaki destek kanalları da kullanılabilir:

* Premier destek avantajlarına sahip müşteriler, [Premier](https://premier.microsoft.com/)ile bir destek isteği açabilir.
* Azure destek sözleşmeleri olan müşteriler [Azure Portal](https://azure.microsoft.com/support/options/)bir destek talebi açabilir.
* Gönderilen fikirleri ve hataları gözden geçirmek [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) veya yeni bir dosya göndermek için Log Analytics geri bildirim sayfasını ziyaret edin. 

## <a name="log-analytics-troubleshooting-tool"></a>Log Analytics sorun giderme aracı

Log Analytics Agent Windows sorun giderme aracı, Log Analytics aracısındaki sorunları bulmaya ve tanılamaya yardımcı olmak üzere tasarlanan bir PowerShell betikleri koleksiyonudur. Yükleme sonrasında aracıya otomatik olarak eklenir. Aracın çalıştırılması, bir sorunu tanılamada ilk adım olmalıdır.

### <a name="how-to-use"></a>Nasıl kullanılır?
1. Log Analytics aracısının yüklendiği makinede PowerShell komut istemi ' ni yönetici olarak açın.
1. Aracın bulunduğu dizine gidin.
   * `cd "C:\Program Files\Microsoft Monitoring Agent\Agent\Troubleshooter"`
1. Şu komutu kullanarak ana betiği yürütün:
   * `.\GetAgentInfo.ps1`
1. Bir sorun giderme senaryosu seçin.
1. Konsolundaki yönergeleri izleyin. (Note: izleme günlükleri adımları, günlük toplamayı durdurmak için el ile müdahale gerektirir. Sorunun reproducibility temel alınarak, süre için bekleyin ve ' günlük toplamayı durdurmak ve sonraki adıma geçmek için ' a basın.

   Sonuç dosyasının konumları tamamlandığında günlüğe kaydedilir ve yeni bir Gezgin penceresi açılır.

### <a name="installation"></a>Yükleme
Sorun giderme aracı, Log Analytics Agent derleme 10.20.18053.0 ve sonraki sürümleri yüklendikten sonra otomatik olarak eklenir.

### <a name="scenarios-covered"></a>Kapsanan senaryolar
Sorun giderme aracı tarafından denetlenen senaryoların listesi aşağıda verilmiştir:

- Aracı veri veya sinyal verisi bildirmiyor
- Aracı uzantısı dağıtım başarısız
- Aracı çökme
- Yüksek CPU/bellek kullanan aracı
- Yükleme/kaldırma sorunları
- Özel Günlükler sorunu
- OMS ağ geçidi sorunu
- Performans sayaçları sorunu
- Tüm günlükleri topla

>[!NOTE]
>Sorun yaşıyorsanız lütfen sorun giderme aracını çalıştırın. Bir bilet açarken, başlangıçta, destek ekibimizin sorununuzu daha hızlı gidermelerine büyük ölçüde yardımcı olur.

## <a name="important-troubleshooting-sources"></a>Önemli sorun giderme kaynakları

 Aracı, Windows için Log Analytics aracısıyla ilgili sorunları gidermeye yardımcı olmak üzere olayları Windows olay günlüğü 'ne özel olarak *uygulama ve Services\Operations Manager* altında günlüğe kaydeder.  

## <a name="connectivity-issues"></a>Bağlantı sorunları

Aracı bir ara sunucu veya güvenlik duvarı üzerinden iletişim kurduğundan, kaynak bilgisayardan ve Azure Izleyici hizmetinden iletişimi engelleyen kısıtlamalar olabilir. İletişim engellendiğinde, yanlış yapılandırma nedeniyle bir çalışma alanı ile kayıt, aracıyı yüklemeye çalışırken başarısız olabilir veya aracı Kurulum sonrası ek bir çalışma alanına rapor verebilir. Kayıt başarıyla kaydedildikten sonra aracı iletişimi başarısız olabilir. Bu bölümde, Windows aracısında bu tür bir sorunu gidermeye yönelik yöntemler açıklanmaktadır.

Güvenlik duvarının veya proxy 'nin aşağıdaki tabloda açıklanan bağlantı noktalarına ve URL 'Lere izin verecek şekilde yapılandırıldığından emin olun. Ayrıca, aracı ve Azure Izleyici arasında güvenli bir TLS kanalını engelleyebilen için HTTP incelemesini web trafiği için etkin değil olarak onaylayın.  

|Aracı Kaynağı|Bağlantı noktaları |Yön |HTTPS denetlemesini atlama|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Bağlantı noktası 443 |Giden|Yes |  
|*.oms.opinsights.azure.com |Bağlantı noktası 443 |Giden|Yes |  
|*.blob.core.windows.net |Bağlantı noktası 443 |Giden|Yes |  
|*. agentsvc.azure-automation.net |Bağlantı noktası 443 |Giden|Yes |  

Azure Kamu için gereken güvenlik duvarı bilgileri için bkz. [Azure Kamu Yönetimi](../../azure-government/compare-azure-government-global-azure.md#azure-monitor). Ortamınızdaki runbook 'ları veya yönetim çözümlerini kullanmak üzere otomasyon hizmetine bağlanmak ve kaydolmak için Azure Otomasyonu karma Runbook Worker kullanmayı planlıyorsanız, bağlantı noktası numarasına ve [ağınızı karma Runbook Worker Için yapılandırma](../../automation/automation-hybrid-runbook-worker.md#network-planning)bölümünde açıklanan URL 'lere erişimi olmalıdır. 

Aracının Azure Izleyici ile başarılı bir şekilde iletişim kurduğunu doğrulayabilmeniz için birkaç yol vardır.

- Çalışma alanında [Azure Log Analytics Aracı durumu değerlendirmesini](../insights/solution-agenthealth.md) etkinleştirin. Aracının listelenip listelenmediğini görmek için Aracı Durumu panosundan, **yanıt vermeyen aracılar sütununun sayısını** görüntüleyin.  

- Aracının raporlamak üzere yapılandırıldığı çalışma alanına bir sinyal gönderdiğini doğrulamak için aşağıdaki sorguyu çalıştırın. `<ComputerName>`Makinenin gerçek adıyla değiştirin.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Bilgisayar hizmetle başarılı bir şekilde iletişim kurduğundan sorgu bir sonuç döndürmelidir. Sorgu bir sonuç döndürmezse, önce aracının doğru çalışma alanına rapor verecek şekilde yapılandırıldığını doğrulayın. Doğru yapılandırılmışsa, adım 3 ' e ilerleyin ve Windows olay günlüğü 'nde, aracının Azure Izleyici ile iletişim kurmasını önlemek için hangi sorunun günlüğe kaydedilmesine neden olduğunu belirlemek için arama yapın.

- Bir bağlantı sorununu belirlemek için başka bir yöntem **Testcloudconnectivity aracını çalıştırmıştır** . Araç varsayılan olarak *%systemroot%\Program Files\Microsoft Monitoring sagent* klasöründe aracı ile yüklenir. Yükseltilmiş komut isteminden klasöre gidin ve aracı çalıştırın. Araç sonuçları ve testin başarısız olduğunu vurgular (örneğin, engellenen belirli bir bağlantı noktası/URL ile ilgiliyse). 

    ![TestCloudConnection Aracı yürütme sonuçları](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Modül, sistem durumu hizmeti ve hizmet Bağlayıcısı sistem sağlığı hizmeti olay kaynaklarına göre *Operations Manager* olay günlüğünü filtreleyin  -  ve **Olay düzeyi** *uyarısı* ve *hata* ile filtreleyerek olayları aşağıdaki tablodan yazıp yazamadığına emin olun.  Bunlar, olası her olay için dahil edilen çözüm adımlarını gözden geçirin.

    |Olay Kimliği |Kaynak |Açıklama |Çözüm |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Sistem Sağlığı Hizmeti |Aracıdan hizmetle bağlantı kurulamadı |Bu hata, aracı doğrudan veya bir güvenlik duvarı/ara sunucu aracılığıyla Azure Izleyici hizmetine iletişim kuramadığınızda ortaya çıkabilir. Aracı ara sunucu ayarlarını doğrulayın veya ağ güvenlik duvarının/proxy 'sinin bilgisayardan hizmete TCP trafiğine izin verdiğini doğrulayın.|
    |2138 |Sistem Sağlığı Hizmeti modüller |Proxy kimlik doğrulaması gerektiriyor |Aracı proxy ayarlarını yapılandırın ve proxy sunucu ile kimlik doğrulamak için gereken kullanıcı adını/parolayı belirtin. |
    |2129 |Sistem Sağlığı Hizmeti modüller |Başarısız bağlantı/TLS anlaşması |Ağ bağdaştırıcınızın TCP/IP ayarları ve aracı ara sunucu ayarlarını kontrol edin.|
    |2127 |Sistem Sağlığı Hizmeti modüller |Veri göndermede hata kodu alındı |Yalnızca gün boyunca düzenli olarak gerçekleşdiğinde, yok sayılacak bir yalnızca rastgele bir anomali olabilir. Ne sıklıkta gerçekleştiğini anlamak için izleyin. Gün boyunca sıklıkla gerçekleşmezse, önce ağ yapılandırmanızı ve ara sunucu ayarlarını kontrol edin. Açıklama HTTP hata kodu 404 ' ü içeriyorsa ve Aracı, hizmete veri göndermeyi ilk kez denediğinde, iç 404 hata koduna sahip bir 500 hatası olur. 404, yeni çalışma alanı için depolama alanının sağlanmakta olduğunu belirten, bulunamadı anlamına gelir. Bir sonraki yeniden denendiğinde, veriler çalışma alanına beklendiği gibi başarıyla yazar. HTTP Hatası 403, izin veya kimlik bilgileri sorununu gösterebilir. Sorunu gidermeye yardımcı olmak için 403 hatasına daha fazla bilgi dahildir.|
    |4000 |Hizmet Bağlayıcısı |DNS ad çözümlemesi başarısız oldu |Makine, hizmete veri gönderilirken kullanılan Internet adresini çözümleyemedi. Bu, makinenizde DNS çözümleyici ayarları, yanlış ara sunucu ayarları veya sağlayıcınızda geçici bir DNS sorunu olabilir. Düzenli aralıklarla gerçekleşirse, ağla ilgili geçici bir sorundan kaynaklanabilir.|
    |4001 |Hizmet Bağlayıcısı |Hizmetle bağlantı kurulamadı. |Bu hata, aracı doğrudan veya bir güvenlik duvarı/ara sunucu aracılığıyla Azure Izleyici hizmetine iletişim kuramadığınızda ortaya çıkabilir. Aracı ara sunucu ayarlarını doğrulayın veya ağ güvenlik duvarının/proxy 'sinin bilgisayardan hizmete TCP trafiğine izin verdiğini doğrulayın.|
    |4002 |Hizmet Bağlayıcısı |Hizmet, bir sorguya yanıt olarak 403 HTTP durum kodunu döndürdü. Hizmetin sistem durumu için Hizmet Yöneticisi ile görüşün. Sorgu daha sonra yeniden denenecek. |Bu hata aracının ilk kayıt aşamasında yazılır ve aşağıdakine benzer bir URL görürsünüz: *https:// \<workspaceID> . OMS.opinsights.Azure.com/AgentService.svc/AgentTopologyRequest*. 403 hata kodu yasak anlamına gelir ve yanlış yazılmış bir çalışma alanı KIMLIĞI veya anahtarından kaynaklanabilir ya da veri ve saat bilgisayarda yanlış olur. Süre geçerli zamandan +/-15 dakika ise, ekleme başarısız olur. Bunu düzeltmek için, Windows bilgisayarınızın tarihini ve/veya saat dilimini güncelleştirin.|

## <a name="data-collection-issues"></a>Veri toplama sorunları

Aracı yüklendikten ve yapılandırılan çalışma alanına ya da çalışma alanlarına rapor ettikten sonra, etkin olan ve bilgisayar hedefleme ayarlarına bağlı olarak, yapılandırmayı ve performansı, günlükleri veya diğer verileri hizmete almayı durdurabilir. Şunları öğrenmek gereklidir:

- Bu, belirli bir veri türü veya çalışma alanında kullanılamayan tüm veriler mi?
- Veri türü bir çözüm tarafından belirtilmiş veya çalışma alanı veri koleksiyonu yapılandırmasının parçası olarak belirtilmiş mi?
- Kaç bilgisayar etkileniyor? Bu, çalışma alanına rapor veren tek veya birden çok bilgisayar mı?
- Çalışıyor mu, günün belirli bir saatinde mi duruyor, yoksa hiç toplanmadı mi? 
- Günlük arama sorgusu, sözdizimsel olarak doğru mı kullanıyorsunuz? 
- Aracı, Azure Izleyici 'nin yapılandırmasını hiç aldı mı?

Sorun gidermenin ilk adımı, bilgisayarın bir sinyal olayı gönderdiğini belirlemektir.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Sorgu sonuçları döndürürse, belirli bir veri türünün toplanmadığını ve hizmete iletilip iletilmeyeceğini belirlemeniz gerekir. Bunun nedeni, aracının hizmetten güncelleştirilmiş yapılandırmayı almamasından veya aracının normal şekilde çalıştırılmasını engellediği başka bir belirtiyle kaynaklanıyor olabilir. Daha fazla sorun gidermek için aşağıdaki adımları gerçekleştirin.

1. Bilgisayarda yükseltilmiş bir komut istemi açın ve yazarak aracı hizmetini yeniden başlatın `net stop healthservice && net start healthservice` .
2. *Operations Manager* olay günlüğünü açın ve olay **kaynağı** *healthservice*'ten *7023, 7024, 7025, 7028* ve *1210* **olay kimliklerini** arayın.  Bu olaylar, aracının Azure Izleyici 'den başarıyla yapılandırmayı aldığını ve bilgisayarı etkin bir şekilde izliyor olduğunu gösterir. Olay KIMLIĞI 1210 için olay açıklaması, aracıdaki izleme kapsamına dahil edilen tüm çözümlerin ve öngörülerin son satırına da dahil edilir.  

    ![Olay KIMLIĞI 1210 açıklaması](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Birkaç dakika sonra, verileri bir çözüm veya öngörüden görüntülüyor olmanıza bağlı olarak sorgu sonuçlarında veya görselleştirmede beklenen verileri görmüyorsanız, *Operations Manager* olay günlüğünden olay **kaynakları** *HealthService* ve *sistem sağlığı hizmeti modülleri* ' ni arayın ve **Olay düzeyi** *uyarısı* ve *hata* ile filtreleme yapın.

    |Olay Kimliği |Kaynak |Açıklama |Çözüm |
    |---------|-------|------------|
    |8000 |HealthService |Bu olay, performans, olay veya toplanan diğer veri türüyle ilgili bir iş akışının, çalışma alanına alma için hizmete iletilememesine yönelik olduğunu belirtir. | Kaynak HealthService 'ten olay KIMLIĞI 2136, bu olayla birlikte yazılır ve aracının hizmetle iletişim kuramadığını belirtebilir, bunun nedeni proxy 'nin ve kimlik doğrulama ayarlarının yanlış yapılandırılmasından, ağ kesintisinden veya ağ güvenlik duvarının/proxy 'sinin bilgisayardan hizmete TCP trafiğine izin vermez.| 
    |10102 ve 10103 |Sistem Sağlığı Hizmeti modüller |İş akışı veri kaynağını çözümleyemedi. |Bu durum, belirtilen performans sayacı veya örneği bilgisayarda yoksa veya çalışma alanı veri ayarları 'nda yanlış tanımlanmışsa oluşabilir. Bu, Kullanıcı tarafından belirtilen bir [performans sayacıdır](data-sources-performance-counters.md#configuring-performance-counters), belirtilen bilgilerin doğru biçimi takip ettiğini ve hedef bilgisayarlarda mevcut olduğunu doğrulayın. |
    |26002 |Sistem Sağlığı Hizmeti modüller |İş akışı veri kaynağını çözümleyemedi. |Belirtilen Windows olay günlüğü bilgisayarda yoksa bu durum oluşabilir. Bu hata, bilgisayarın bu olay günlüğünün kayıtlı olması beklenmiyorsa güvenli bir şekilde yoksayılabilir, aksi takdirde Kullanıcı tarafından belirtilen bir [olay günlüğü](data-sources-windows-events.md#configuring-windows-event-logs)ise, belirtilen bilgilerin doğru olduğundan emin olun. |
