---
title: Log Analytics SSS | Microsoft Docs
description: Azure Izleyici günlükleri Analizi hizmeti hakkında sık sorulan soruların yanıtları.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 11/01/2019
ms.openlocfilehash: 9eb921fc8ea19486db0fc3311764931f09e11464
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579304"
---
# <a name="log-analytics-faq"></a>Log Analytics SSS

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Bu Microsoft SSS, Azure Izleyici Log Analytics çalışma alanı hakkında sık sorulan sorulardan oluşan bir listesidir. Log Analytics hakkında başka sorularınız varsa, [tartışma forumuna](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) gidin ve sorularınızı gönderin. Bir soru sıkça sorulduğunda, hızlı ve kolay bir şekilde bulunabilmesi için bu makaleye ekleyeceğiz.


## <a name="new-logs-experience"></a>Yeni günlük deneyimi

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>S: yeni günlük deneyimi ve Log Analytics arasındaki fark nedir?

Y: aynı şeydir. [Log Analytics, Azure izleyici 'de](../../azure-monitor/azure-monitor-rebrand.md) daha birleştirilmiş bir izleme deneyimi sağlamak için bir özellik olarak tümleştirilir. Azure Izleyici 'deki yeni günlük deneyimi, birçok müşterinin zaten kullandığı Log Analytics sorgularıyla tam olarak aynıdır.

### <a name="q-can-i-still-use-log-search"></a>S: günlük aramasını kullanmaya devam edebilir miyim? 

A: günlük araması şu anda OMS portalında ve ad **günlükleri (klasik)** altındaki Azure Portal hala kullanılabilir. OMS portalı 15 Ocak 2019 tarihinde resmi olarak kullanımdan kaldırılacaktır. Azure portal içindeki klasik Günlükler deneyimi aşamalı olarak kullanımdan kalkar ve yeni günlük deneyiminin değiştirilmeyecektir. 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>S. Gelişmiş analiz portalını kullanmaya devam edebilir miyim? 
Azure portal yeni Günlükler deneyimi, gelişmiş analiz portalına dayalıdır, ancak yine de Azure portal dışında erişilebilir. Bu dış portalın kullanımdan kaldırılması için yol haritası yakında duyurulacaktır.

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>S. Sorgu Gezginini neden göremiyorum ve yeni Günlükler deneyiminde düğmeleri kaydedemiyorum?

**Sorgu Gezgini**, **Kaydet** ve **Ayarla uyarı** düğmeleri, belirli bir kaynak bağlamında Günlükler araştırılırken kullanılamaz. Uyarı oluşturmak, bir sorguyu kaydetmek veya yüklemek için günlüklerin bir çalışma alanı kapsamına alınmış olması gerekir. Günlükleri çalışma alanı bağlamında açmak için **tüm hizmetler** >  > günlüklerini **İzle** 'yi seçin. Son kullanılan çalışma alanı seçilir, ancak başka bir çalışma alanını seçebilirsiniz. Daha fazla bilgi için bkz. [Log Analytics verileri görüntüleme ve çözümleme](../log-query/portals.md) .

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>S. Nasıl yaparım? yeni Günlükler deneyiminde özel alanlar Ayıklansın mı? 

Y: özel alanlar ayıklama, şu anda klasik Günlükler deneyiminde destekleniyor. 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>S. Liste görünümünü yeni günlüklerde nerede bulabilirim? 

A: liste görünümü yeni günlüklerde kullanılamaz. Sonuçlar tablosundaki her kaydın solunda bir ok vardır. Belirli bir kaydın ayrıntılarını açmak için bu oka tıklayın. 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-are-available-how-can-i-see-filters"></a>S. Bir sorgu çalıştırdıktan sonra önerilen filtrelerin bir listesi kullanılabilir. Filtreleri nasıl görebilirim? 

Y: yeni filtreler uygulamasının önizlemesini görmek için sol bölmedeki ' Filtreler ' seçeneğine tıklayın. Bu artık, Kullanıcı arabiriminin 10.000 kayıt sınırı ile sınırlı olmak yerine, tam sonuç kümesini temel alır. Bu, şu anda en popüler filtrelerin ve her bir filtrenin en yaygın 10 değerinin bir listesidir. 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>S. Neden "Bu sorguyu, VM 'den detaya gitme sonrasında günlüklerde" Bu sorguyu etkinleştirmek için bu aboneliğin "Microsoft. Insights" kaynak sağlayıcısını Kaydet 

A: varsayılan olarak, birçok kaynak sağlayıcısı otomatik olarak kaydedilir, ancak bazı kaynak sağlayıcılarını el ile kaydetmeniz gerekebilir. Bu, aboneliğinizi kaynak sağlayıcısıyla çalışacak şekilde yapılandırır. Kayıt kapsamı her zaman abonelik olur. Daha fazla bilgi için bkz. [Kaynak sağlayıcıları ve türleri](../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>S. Bir VM sayfasından günlüklere erişirken neden erişim hatası mesajı alıyorum? 

Y: VM günlüklerini görüntülemek Için, VM günlüklerini depolayan çalışma alanları için okuma izniyle verilmesi gerekir. Bu durumlarda yöneticinizin Azure 'daki izinleri size vermesi gerekir.

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>S. Çalışma alanım 'ı OMS portalında neden erişebiliyorum, ancak Azure portal "erişiminiz yok" hatasını alıyorum?  

Y: Azure 'daki bir çalışma alanına erişmek Için Azure izinlerinin atanmış olması gerekir. Uygun erişim izinlerine sahip olmayabilirsiniz bazı durumlar vardır. Bu durumlarda yöneticinizin Azure 'daki izinleri vermesi gerekir. daha fazla bilgi için bkz. [OMS portalı Azure 'a geçme](oms-portal-transition.md) .

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>S. Günlüklerde neden görünüm tasarımcı girişi göremiyorum?

Y: Görünüm Tasarımcısı, yalnızca katkıda bulunan izinlerle veya üzeri olarak atanan kullanıcılara yönelik günlüklerde kullanılabilir.

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>S. Analiz portalını Azure dışında kullanmaya devam edebilir miyim?

A. Evet, Azure 'daki Günlükler sayfası ve gelişmiş analiz portalı aynı koda göre yapılır. Log Analytics, Azure Izleyici 'de daha birleştirilmiş bir izleme deneyimi sağlamak için bir özellik olarak tümleştirilir. Şu URL 'YI kullanarak analiz portalına erişmeye devam edebilirsiniz: https:\/\/Portal. loganalytics. IO/abonelikler/{SubscriptionID}/ResourceGroups/{resourceGroupName}/çalışma alanları/{Çalışmaadı}.



## <a name="general"></a>Genel

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>S. Görünümlerimi ve çözümlerinizi Azure portal nasıl görebilirim? 

Y: görünümler ve yüklü çözümlerin listesi Azure portal ' de mevcuttur. **Tüm hizmetler**’e tıklayın. Kaynak listesinde **izleyici**' yi seçin ve ardından... öğesine tıklayın **. Daha fazla**. Son kullanılan çalışma alanı seçilir, ancak başka bir çalışma alanını seçebilirsiniz. 

### <a name="q-why-i-cant-create-workspaces-in-west-central-us-region"></a>S. Orta Batı ABD bölgede çalışma alanlarını neden oluşturamıyoruz? 

Y: Bu bölge geçici kapasite sınırındedir. Bu sınır, Eylül 2019 ' nin sonuna kadar planlanarak yapılır.


### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>S. Log Analytics Azure Güvenlik Merkezi ile aynı aracıyı kullanıyor mu?

Y: erken Haziran 2017 ' de Azure Güvenlik Merkezi, veri toplamak ve depolamak için Microsoft Monitoring Agent kullanmaya başlamıştır. Daha fazla bilgi için bkz. [Azure Güvenlik Merkezi platformu GEÇIŞ SSS](../../security-center/security-center-enable-data-collection.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>S. AD ve SQL Değerlendirmesi çözümleri hangi denetimleri gerçekleştirir?

A: aşağıdaki sorgu, şu anda gerçekleştirilen tüm denetimlerin açıklamasını gösterir:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Daha sonra sonuçlar daha fazla gözden geçirilmek üzere Excel 'e aktarılabilir.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>S. Neden System Center Operations Manager konsolundaki OMS 'den farklı bir şey görüyorum?

Y: hangi Operations Manager güncelleştirme toplamasına bağlı olarak, *System Center Advisor*, *operasyonel*içgörüler veya *Log Analytics*için bir düğüm görebilirsiniz.

*OMS* 'deki metin dizesi güncelleştirmesi, el ile içeri aktarılması gereken bir yönetim paketine dahildir. Geçerli metin ve işlevselliği görmek için en son System Center Operations Manager güncelleştirme paketi BB makalesindeki yönergeleri izleyin ve konsolu yenileyin.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>S: Log Analytics 'ın şirket içi sürümü var mı?

Y: Hayır. Log Analytics, büyük miktarlarda veriyi işleyen ve depolayan ölçeklenebilir bir bulut hizmetidir. 

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>S. Veri toplama durdurulduğunda nasıl bildirim gönderebilirim?

A: veri toplama durdurulduğunda bildirim almak için [Yeni bir günlük uyarısı oluşturma](../../azure-monitor/platform/alerts-metric.md) bölümünde açıklanan adımları kullanın.

Veri toplama durdurulduğunda uyarı oluştururken şunu ayarlayın:

- **Uyarı koşulunu tanımlama** adımında Log Analytics çalışma alanınızı kaynak hedefi olarak belirtin.
- **Uyarı ölçütleri** alanında aşağıdakileri belirtin:
   - **Sinyal adı** **özel günlük araması**' nı seçin.
   - **Arama sorgusu**: `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Uyarı mantığı**, **Temeli** *bir dizi sonuçtur* ve **Koşul**, *Büyüktür* bir **Eşik değeri**, *0*
   - *30* dakikalık **zaman aralığı** ve *10* dakikada bir **Uyarı sıklığı**
- **Uyarı ayrıntılarını tanımlama** adımında aşağıdakileri belirtin:
   - *Veri toplamanın* **adı** durduruldu
   - **Önem derecesi**: *Uyarı*

Var olan veya yeni bir [eylem grubu](../../azure-monitor/platform/action-groups.md) oluşturun, böylece günlük uyarısı ölçütle eşleştiğinde, 15 dakikadan uzun bir sinyalle kayıp varsa size bildirilir.

## <a name="configuration"></a>Yapılandırma

### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>S. Azure Tanılama (WAD) öğesinden okumak için kullanılan tablo/blob kapsayıcısının adını değiştirebilir miyim?

A. Hayır, şu anda Azure Storage 'daki rastgele tablolardan veya kapsayıcılardan okumak mümkün değildir.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>S. Log Analytics hizmeti hangi IP adreslerini kullanır? Nasıl yaparım? Güvenlik duvarımın yalnızca Log Analytics hizmetine giden trafiğe izin verdiğinden emin misiniz?

A. Log Analytics hizmeti Azure üzerinde oluşturulmuştur. Log Analytics IP adresleri [Microsoft Azure veri MERKEZI IP aralıklarıyla](https://www.microsoft.com/download/details.aspx?id=41653)bulunur.

Hizmet dağıtımları yapıldığından, Log Analytics hizmetinin gerçek IP adresleri değişir. Güvenlik duvarınız aracılığıyla izin verilecek DNS adları [ağ gereksinimleri](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements)bölümünde belgelenmiştir.

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>S. Azure 'a bağlanmak için ExpressRoute kullanıyorum. Log Analytics Trafiğim ExpressRoute bağlantısını kullanıyor mu?

A. Farklı ExpressRoute trafiği türleri [ExpressRoute belgelerinde](../../expressroute/expressroute-faqs.md#supported-services)açıklanmıştır.

Log Analytics trafik genel eşleme ExpressRoute devresini kullanır.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>S. Mevcut bir Log Analytics çalışma alanını başka bir Log Analytics çalışma alanına/Azure aboneliğine taşımanın basit ve kolay bir yolu var mı?

A. `Move-AzResource` cmdlet 'i, bir Log Analytics çalışma alanını ve aynı zamanda bir Azure aboneliğindeki bir Otomasyon hesabını diğerine taşımanıza olanak tanır. Daha fazla bilgi için bkz. [Move-AzResource](https://msdn.microsoft.com/library/mt652516.aspx).

Bu değişiklik Azure portal de yapılabilir.

Verileri bir Log Analytics çalışma alanından diğerine taşıyamaz veya Log Analytics verilerin depolandığı bölgeyi değiştiremiyoruz.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>S: System Center Operations Manager Nasıl yaparım? Log Analytics eklensin mi?

Y: en son güncelleştirme toplamasına ve içeri aktarma yönetim paketlerini güncelleştirme, Log Analytics Operations Manager bağlanmanızı sağlar.

>[!NOTE]
>Log Analytics Operations Manager bağlantısı yalnızca System Center Operations Manager 2012 SP1 ve üzeri sürümlerde kullanılabilir.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>S: bir aracının Log Analytics ile iletişim kurabildiğini nasıl doğrulayabilirim?

Y: aracının Log Analytics çalışma alanıyla iletişim kurabildiğinden emin olmak Için şuraya gidin: Denetim Masası, güvenlik & ayarları **Microsoft Monitoring Agent**.

**Azure Log Analytics (OMS)** sekmesinde, yeşil bir onay işareti bulun. Yeşil onay işareti simgesi, aracının Azure hizmeti ile iletişim kurabildiğini onaylar.

Sarı bir uyarı simgesi, aracının Log Analytics iletişim sorunu olduğu anlamına gelir. Microsoft Monitoring Agent hizmetin durdurulduğu bir yaygın neden olur. Hizmeti yeniden başlatmak için hizmet denetimi Yöneticisi 'ni kullanın.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>S: Nasıl yaparım? bir aracının Log Analytics iletişim kurmasını mi durdurdunuz?

Y: System Center Operations Manager, bilgisayarı Log Analytics yönetilen bilgisayarlar listesinden kaldırın. Operations Manager, aracının yapılandırmasını artık Log Analytics raporlanmayacak şekilde güncelleştirir. Doğrudan Log Analytics bağlı aracılar için, iletişim kurmasını engellemek için: Denetim Masası, güvenlik & ayarları **Microsoft Monitoring Agent**.
**Azure Log Analytics (OMS)** altında, listelenen tüm çalışma alanlarını kaldırın.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>S: çalışma alanım 'ı bir Azure aboneliğinden diğerine taşımaya çalıştığımda neden bir hata alıyorum?

Y: bir çalışma alanını farklı bir aboneliğe veya kaynak grubuna taşımak Için, önce çalışma alanındaki Otomasyon hesabının bağlantısını kaldırmanız gerekir. Bir Otomasyon hesabının bağlantısını kesmek, çalışma alanına yüklendiklerinde bu çözümlerin kaldırılmasını gerektirir: Güncelleştirme Yönetimi, Değişiklik İzleme veya VM'leri çalışma saatleri dışında başlat/durdur kaldırılır. Bu çözümler kaldırıldıktan sonra, Otomasyon hesabı kaynağında sol bölmedeki **bağlı çalışma alanları** ' nı seçerek Otomasyon hesabının bağlantısını kaldırın ve Şeritteki **çalışma alanının bağlantısını kaldır** ' a tıklayın.
 > Kaldırılan çözümlerin çalışma alanına yeniden yüklenmesi gerekir ve çalışma alanının Otomasyon bağlantısının taşımadan sonra yeniden oluşturulması gerekir.

Azure aboneliklerinde izniniz olduğundan emin olun.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-update-a-savedsearch"></a>S: bir SavedSearch güncelleştirmeyi denediğimde neden hata alıyorum?

Y: API 'nin gövdesine ' ETag ' eklemeniz veya Azure Resource Manager şablonu özelliklerinin olması gerekir:
```
"properties": {
   "etag": "*",
   "query": "SecurityEvent | where TimeGenerated > ago(1h) | where EventID == 4625 | count",
   "displayName": "An account failed to log on",
   "category": "Security"
}
```

## <a name="agent-data"></a>Aracı verileri
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>S. Log Analytics için aracıdan ne kadar veri gönderebilirim? Müşteri başına maksimum miktarda veri var mı?
A. Karşıya yüklenen veri miktarı için bir sınır yoktur, bu, seçtiğiniz kapasite rezervasyonu veya kullandıkça öde fiyatlandırma seçeneğine dayanır. Bir Log Analytics çalışma alanı, bir müşteriden gelen hacmi işlemek üzere otomatik olarak ölçeklendirilecek şekilde tasarlanmıştır. Bu, gün başına terabayt olsa bile. Daha fazla bilgi için bkz. [fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/monitor/).

Log Analytics Aracısı, küçük bir parmak izine sahip olduğundan emin olmak için tasarlandı. Veri hacmi, etkinleştirdiğiniz çözümlere göre farklılık gösterir. Veri hacminin ayrıntılı bilgilerini bulabilir ve [kullanım](../../azure-monitor/platform/data-usage.md) sayfasında çözüme göre döküm bölümüne bakabilirsiniz.

Daha fazla bilgi için, Log Analytics aracısının kaynak kullanımını (parmak izi) değerlendirdikten sonra sonuçlarını gösteren bir [Müşteri blogunu](https://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) okuyabilirsiniz.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>S. Log Analytics veri gönderilirken Microsoft Yönetim Aracısı (MMA) tarafından ne kadar ağ bant genişliği kullanılıyor?

A. Bant genişliği, gönderilen veri miktarı üzerinde bir işlevdir. Veriler, ağ üzerinden gönderildiği için sıkıştırılır.

### <a name="q-how-much-data-is-sent-per-agent"></a>S. Aracı başına ne kadar veri gönderildi?

A. Aracı başına gönderilen veri miktarı şunlara bağlıdır:

* Etkinleştirdiğiniz çözümler
* Toplanmakta olan günlük ve performans sayacı sayısı
* Günlüklerdeki verilerin hacmi

Genel kullanım, [kullanım](../../azure-monitor/platform/data-usage.md) sayfasında gösterilir.

Kablolu veri aracısını çalıştırabilecek bilgisayarlar için, ne kadar veri gönderildiğini görmek üzere aşağıdaki sorguyu kullanın:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Sonraki adımlar

Log Analytics hakkında daha fazla bilgi edinmek için [Azure izleyici 'yi kullanmaya](../../azure-monitor/overview.md) başlayın ve dakikalar içinde çalışmaya başlayın.
