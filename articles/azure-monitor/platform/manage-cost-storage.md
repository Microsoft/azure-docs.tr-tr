---
title: Azure Izleme günlükleri için kullanımı ve maliyetleri yönetme | Microsoft Docs
description: Azure Izleyici 'de Log Analytics çalışma alanınızın fiyatlandırma planını değiştirme ve veri hacmini ve bekletme ilkesini yönetme hakkında bilgi edinin.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: ae49a84dba01047d85ea0dbb854d1a7a59318567
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647649"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Azure Izleyici günlükleriyle kullanımı ve maliyetleri yönetme

> [!NOTE]
> Bu makalede, Azure Izleyici günlüklerine yönelik maliyetlerinizi anlama ve denetleme işlemlerinin nasıl yapılacağı açıklanır. [Kullanımı ve tahmini maliyetleri izleyen](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) ilgili bir makale, farklı fiyatlandırma modelleri için birden çok Azure izleme özelliği genelinde kullanım ve tahmini maliyetlerin nasıl görüntüleneceğini açıklar.

> [!NOTE]
> Bu makalede gösterilen tüm fiyatlar ve maliyetler yalnızca örnek amaçlıdır. 

Azure Izleyici günlükleri, kuruluşunuzda bulunan veya Azure 'da dağıtılan herhangi bir kaynakta çok büyük miktarlarda veri toplamayı, dizinlemesini ve depolamayı, ölçeklendirmek ve desteklemek üzere tasarlanmıştır.  Bu, kuruluşunuz için bir birincil sürücü olabilir, ancak maliyet verimliliği sonunda temel alınan sürücü. Bu uçta, bir Log Analytics çalışma alanının maliyetinin yalnızca toplanan verilerin hacmine dayanmadığını anlamak önemlidir, Ayrıca, seçilen plana de bağlıdır ve bağlı kaynaklarınızdan oluşturulan verileri depolamayı ne kadar tercih edersiniz.  

Bu makalede, alınan veri hacmi ve depolama büyümesinin nasıl proaktif bir şekilde izleneceğini ve ilgili maliyetleri denetlemek için sınırları tanımlamanızı inceliyoruz. 

## <a name="pricing-model"></a>Fiyatlandırma modeli

Log Analytics için varsayılan fiyatlandırma, veri hacmine dayalı ve isteğe bağlı olarak daha uzun veri saklama için bir **Kullandıkça Öde** modelidir. Veri hacmi, depolanacak verilerin boyutu olarak ölçülür. Her Log Analytics çalışma alanı ayrı bir hizmet olarak ücretlendirilir ve Azure aboneliğiniz için faturaya katkıda bulunur. Veri alma miktarı aşağıdaki faktörlere göre önemli ölçüde olabilir: 

  - Etkin yönetim çözümü sayısı ve yapılandırmaları (ör. 
  - İzlenen VM sayısı
  - İzlenen her bir VM 'den toplanan verilerin türü 
  
Kullandıkça Öde modeline ek olarak Log Analytics, Kullandıkça Öde fiyatına kıyasla %25 ' e kadar tasarruf etmeniz için **Kapasite rezervasyon** katmanlarına sahiptir. Kapasite ayırma fiyatlandırması, 100 GB/gün üzerinden başlayan bir rezervasyon satın almanıza olanak sağlar. Rezervasyon düzeyinin üzerindeki tüm kullanımlar, Kullandıkça Öde fiyatı üzerinden faturalandırılır. Kapasite rezervasyon katmanlarında 31 günlük taahhüt dönemi vardır. Taahhüt dönemi boyunca, daha yüksek düzey kapasite rezervasyon katmanına geçebilirsiniz (Bu, 31 günlük taahhüt dönemini yeniden başlatacak), ancak taahhüt dönemi bitene kadar, Kullandıkça Öde veya daha düşük bir kapasite Ayırma katmanına geri gidemezsiniz bitirdikten. 
Log Analytics Kullandıkça öde ve kapasite rezervasyon fiyatlandırması hakkında [daha fazla bilgi edinin](https://azure.microsoft.com/pricing/details/monitor/) . 

Ayrıca, [Azure Güvenlik Merkezi](https://azure.microsoft.com/pricing/details/security-center/) ve [Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/)gibi bazı çözümlerin kendi fiyatlandırma modeline sahip olduğunu unutmayın. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>Ortamınızı yönetme maliyetlerini tahmin etme 

Henüz Azure Izleyici günlüklerini kullanmıyorsanız, Log Analytics kullanmanın maliyetini tahmin etmek için [Azure izleyici Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/?service=monitor) ' ı kullanabilirsiniz. Arama kutusuna "Azure Izleyici" girerek ve sonuçta elde edilen Azure Izleyici kutucuğuna tıklayarak başlayın. Sayfayı Azure Izleyici 'ye kaydırın ve tür açılan menüsünden Log Analytics ' yi seçin.  Buraya, her bir VM 'den toplamak istediğiniz sanal makine sayısını ve GB veri miktarını girebilirsiniz. Genellikle 1 ila 3 GB veri ayı tipik bir Azure VM 'sinden alınır. Zaten Azure Izleyici günlüklerini değerlendiriyorsanız, kendi ortamınızdan veri istatistiklerinizi kullanabilirsiniz. [Izlenen sanal makinelerin sayısını](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) ve [çalışma alanınızın veri hacmini](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)nasıl belirleyeceğini öğrenmek için aşağıya bakın. 

## <a name="understand-your-usage-and-estimate-costs"></a>Kullanımınız ve Tahmini maliyetlerinizi anlayın

Azure Izleyici günlüklerini şimdi kullanıyorsanız, maliyetlerin en son kullanım desenlerine göre büyük olasılıkla ne olduğunu anlamak kolaydır. Bunu yapmak için, veri kullanımını gözden geçirmek ve çözümlemek üzere **Log Analytics kullanımı ve tahmini maliyetleri** kullanın. Bu, her bir çözüm tarafından ne kadar veri toplandığını, ne kadar veri tutulup tutulmadığını ve dahil edilen miktarın ötesinde ek bir bekletme miktarına göre maliyetlerinizin bir tahminini gösterir.

![Kullanım ve tahmini maliyetler](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Verilerinizi daha ayrıntılı bir şekilde araştırmak için **kullanım ve tahmini maliyetler** sayfasındaki grafiklerin sağ üst köşesindeki simgeye tıklayın. Artık kullanımınız hakkında daha fazla bilgi edinmek için bu sorguyla birlikte çalışabilirsiniz.  

![Günlükler görünümü](media/manage-cost-storage/logs.png)

**Kullanım ve tahmini maliyetler** sayfasında, veri hacminin ayı için gözden geçirebilirsiniz. Bu, Log Analytics çalışma alanınızda alınan ve saklanan tüm verileri içerir.  Kaynak, bilgisayar ve teklife göre veri hacmi eğilimlerini hakkında bilgi içeren Kullanım panosunu görüntülemek için sayfanın üst kısmından **kullanım ayrıntıları** ' na tıklayın. Günlük ucunu görüntülemek ve ayarlamak ya da saklama süresini değiştirmek için, **veri hacmi yönetimi**' ne tıklayın.
 
Log Analytics ücretleri Azure faturanızda eklenir. Azure faturanızın ayrıntılarını Azure portal Faturalandırma bölümünde veya [Azure faturalama portalı](https://account.windowsazure.com/Subscriptions)görebilirsiniz.  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Azure faturanızda Log Analytics kullanımı görüntüleme 

Azure, [Azure maliyet yönetimi + faturalandırma](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) hub 'ında yararlı bir işlevsellik sağlar. Örneğin, "maliyet analizi" işlevi, Azure kaynakları için kullandığınız süreyi görüntülemenize olanak sağlar. Kaynak türüne göre bir filtre ekleme (Log Analytics için Microsoft. operationalınsights/çalışma alanı için), harcamalarınızı izlemenize imkan tanır.

Kullanımınız [Azure Portal kullanımınıza indirilerek](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)kullanımınız daha fazla anlama kazanılabilir. İndirilen elektronik tabloda, Azure kaynağı başına kullanım (ör. Log Analytics çalışma alanı) için günlük kullanımını görebilirsiniz. Bu Excel elektronik tablosunda, Log Analytics çalışma alanlarınızın kullanımı, "içgörüler ve çözümlemeler" (bazı eski fiyatlandırma katmanlarından bazıları tarafından kullanılır) ve "Log Analytics" göstermek ve "örneğe" bir filtre eklemek için "ölçüm kategorisi" sütununda ilk filtrelemeye göre bulunabilir. ID "," Workspace içerir "olan sütun. Kullanım "tüketilen miktar" sütununda gösterilir ve her girdinin birimi "ölçü birimi" sütununda gösterilir.  [Microsoft Azure faturanızı anlamanıza](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)yardımcı olacak daha fazla ayrıntı bulabilirsiniz. 

## <a name="changing-pricing-tier"></a>Fiyatlandırma katmanını değiştirme

Çalışma alanınızın Log Analytics fiyatlandırma katmanını değiştirmek için 

1. Azure portal, çalışma alanınızdaki **kullanım ve tahmini maliyetler** ' i açarak bu çalışma alanı için kullanılabilir olan fiyatlandırma katmanlarının her birinin listesini görürsünüz.

2. Fiyatlandırma katmanlarının her biri için tahmini maliyetleri gözden geçirin. Bu tahmin, son 31 güne kadar kullanım için belirlenir. bu nedenle, bu maliyet tahmini, tipik kullanımınızın temsilciyle ilgili son 31 güne dayanır. Aşağıdaki örnekte, son 31 günden veri desenlerine bağlı olarak, bu çalışma alanının 100 GB/gün kapasite rezervasyon katmanıyla (#2) karşılaştırıldığında Kullandıkça Öde katmanında (#1) daha az ücret aldığına bakabilirsiniz.  

    ![Fiyatlandırma katmanları](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. Son 31 güne göre Tahmini maliyetleri gözden geçirdikten sonra, fiyatlandırma katmanını değiştirmeye karar verirseniz **Seç**' e tıklayın.  

Fiyatlandırma katmanını, `sku` parametresini (`pricingTier` Azure Resource Manager şablonunda) kullanarak [Azure Resource Manager aracılığıyla da ayarlayabilirsiniz](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) . 

## <a name="legacy-pricing-tiers"></a>Eski fiyatlandırma katmanları

2 Nisan 2018 tarihinden önce Log Analytics bir çalışma alanına veya Application Insights kaynağına sahip olan abonelikler, 1 Şubat 2019 ' den önce başlatılan bir Kurumsal Anlaşma bağlı olmaya devam **edecektir.** **Tek başına (GB başına)** ve **düğüm başına (OMS)** .  Ücretsiz fiyatlandırma katmanındaki çalışma alanlarında, günlük veri alımı 500 MB ile sınırlıdır (Azure Güvenlik Merkezi tarafından toplanan güvenlik verileri türleri hariç) ve veri saklama süresi 7 gün ile sınırlıdır. Ücretsiz fiyatlandırma katmanı yalnızca değerlendirme amaçlarıyla tasarlanmıştır. Tek başına veya düğüm başına fiyatlandırma katmanlarında çalışma alanları, Kullanıcı tarafından yapılandırılabilen ve 2 yıla kadar saklama sağlar. 

2016 Nisan 'dan önce oluşturulan çalışma alanları, 30 ve 365 günün sabit veri bekletmesini içeren orijinal **Standart** ve **Premium** fiyatlandırma katmanlarına de erişebilir. Yeni çalışma alanları **Standart** veya **Premium** fiyatlandırma katmanlarında oluşturulamaz ve bir çalışma alanı bu katmanlardan taşınmışsa, geri taşınamaz. 

Fiyatlandırma Katmanı sınırlamalarıyla ilgili daha fazla ayrıntıya [buradan](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces)ulaşabilirsiniz.

> [!NOTE]
> System Center için OMS E1 Suite, OMS E2 Suite veya OMS eklentisi satın alma işleminden gelen yetkilendirmeleri kullanmak için *düğüm başına* fiyatlandırma katmanını Log Analytics seçin.

## <a name="change-the-data-retention-period"></a>Veri saklama süresini değiştirme

Aşağıdaki adımlarda, çalışma alanınızda günlük verilerinin ne kadar süreyle saklanacağını nasıl yapılandıracağınız açıklanır.

### <a name="default-retention"></a>Varsayılan saklama

Çalışma alanınız için varsayılan saklama alanını ayarlamak için 
 
1. Azure portal, çalışma alanınızdan sol bölmeden **kullanım ve tahmini maliyetler** ' i seçin.
2. **Kullanım ve tahmini maliyetler** sayfasının üst kısmındaki **Veri hacmi yönetimi**'ni seçin.
3. Bölmede, gün sayısını artırmak veya azaltmak için kaydırıcıyı kaydırın ve ardından **Tamam**' a tıklayın.  *Ücretsiz* katmanınız varsa, veri saklama süresini değiştiremeyeceksiniz ve bu ayarı denetlemek için ücretli katmana yükseltmeniz gerekir.

    ![Çalışma alanı verilerini bekletme ayarını değiştir](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
Saklama, `retentionInDays` parametresi kullanılarak [Azure Resource Manager ile de ayarlanabilir](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) . Ayrıca, veri bekletmesini 30 güne ayarlarsanız, uyumluluk ile ilgili senaryolar için faydalı olabilecek `immediatePurgeDataOn30Days` parametresini kullanarak eski verilerin hemen temizliğini tetikleyebilirsiniz. Bu işlevsellik yalnızca Azure Resource Manager aracılığıyla sunulur. 

İki veri türü--`Usage` ve `AzureActivity`, varsayılan olarak 90 gün boyunca korunur ve bu 90 günlük bekletme için ücret alınmaz. Bu veri türleri de veri alma ücretlerinden ücretsizdir. 

### <a name="retention-by-data-type"></a>Veri türüne göre bekletme

Ayrıca, bireysel veri türleri için farklı bekletme ayarları belirtmek mümkündür. Her veri türü, çalışma alanının alt kaynağıdır. Örneğin, SecurityEvent tablosu [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) şöyle çözülebilir:

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Veri türünün (tablo) büyük/küçük harfe duyarlı olduğunu unutmayın.  Belirli bir veri türünün (Bu örnekte SecurityEvent) veri türü bekletme ayarlarını geçerli olarak almak için şunu kullanın:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

Çalışma alanınızdaki tüm veri türleri için geçerli veri türü bekletme ayarlarını almak için, yalnızca belirli veri türünü atlayın, örneğin:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Belirli bir veri türünün (Bu örnekte SecurityEvent) bekletilmesini 730 gün olarak ayarlamak için

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

`Usage` ve `AzureActivity` veri türleri özel saklama ile ayarlanamaz. Bu, varsayılan çalışma alanı saklama veya 90 gün üst sınırını alır. 

Veri türüne göre saklama ayarlamak için Azure Resource Manager doğrudan bağlanmak için harika bir araç, OSS aracı [Armclient](https://github.com/projectkudu/ARMClient)' dir.  [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) ve [Daniel bowbevet](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/)makalelerini kullanarak armclient hakkında daha fazla bilgi edinin.  ARMClient kullanarak, SecurityEvent verilerinin 730 günlük bekletmeye göre ayarlanması aşağıda verilmiştir:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!NOTE]
> Veri saklama maliyetlerinizi azaltmak için bireysel veri türlerinde bekletme ayarlama kullanılabilir.  2019 Ekim 'den başlayarak toplanan veriler için (Bu özellik yayınlandığında), bazı veri türlerine yönelik saklama süresini azaltmak zaman içinde bekletme maliyetinizi azaltabilir.  Daha önce toplanan veriler için, tek bir tür için daha düşük bir bekletme ayarlamak, bekletme maliyetlerinizi etkilemez.  

## <a name="manage-your-maximum-daily-data-volume"></a>Günlük veri hacminin maksimum sayısını yönetin

Bir günlük Cap yapılandırabilir ve çalışma alanınız için günlük alımı sınırlayabilirsiniz, ancak amacınız günlük sınıra ulaşmamak zorunda kalacağından dikkatli olabilirsiniz.  Aksi takdirde, günün geri kalanı için veri kaybedersiniz. Bu, işlevleri çalışma alanında kullanılabilir olan güncel verilere bağlı olabilecek diğer Azure hizmetlerini ve çözümlerini etkileyebilir.  Sonuç olarak, BT hizmetlerini destekleyen kaynakların sistem durumu koşulları etkilendiğinde uyarıları gözlemleyebilme ve alma olanağınız vardır.  Günlük üst sınır, yönetilen kaynaklarınızdan alınan veri hacminde beklenmeyen artışı yönetmek için bir yol olarak veya çalışma alanınız için plansız ücretleri sınırlamak istediğinizde kullanılmak üzere tasarlanmıştır.  

Günlük sınıra ulaşıldığında, faturalanabilir veri türleri koleksiyonu günün geri kalanı için duraklar. Seçili Log Analytics çalışma alanı için sayfanın üst kısmında bir uyarı başlığı görünür ve bir işlem olayı **Logmanagement** kategorisi altındaki *işlem* tablosuna gönderilir. *Günlük sınır*altında tanımlanan sıfırlama süresi, veri toplama işlemine devam eder. Günlük veri sınırına ulaşıldığında bildirmek üzere yapılandırılan bu işlem olayına dayalı bir uyarı kuralı tanımlamayı öneririz. 

> [!NOTE]
> Günlük sınır, Azure Güvenlik Merkezi 'nin 19 Haziran 2017 ' den önce yüklendiği çalışma alanları dışında Azure Güvenlik Merkezi 'ndeki verilerin toplanmasını durdurmaz. 

### <a name="identify-what-daily-data-limit-to-define"></a>Hangi günlük veri sınırının tanımlanacağını belirleyin

Veri alma eğilimi ve tanımlanacak günlük hacim üst sınırı olduğunu anlamak için [Log Analytics kullanımı ve tahmini maliyetleri](usage-estimated-costs.md) gözden geçirin. Sınıra ulaşıldıktan sonra kaynaklarınızı izleyemeyeceksiniz, bu, dikkatli olarak düşünülmelidir. 

### <a name="set-the-daily-cap"></a>Günlük ucunu ayarla

Aşağıdaki adımlarda, Log Analytics çalışma alanının günlük olarak kullanacağı veri hacmini yönetmek için bir sınırın nasıl yapılandırılacağı açıklanır.  

1. Çalışma alanınızın sayfasında, soldaki bölmeden **Kullanım ve tahmini maliyetler**’i seçin.
2. Seçili çalışma alanı için **kullanım ve tahmini maliyetler** sayfasında, sayfanın üst kısmından **veri hacmi yönetimi** ' ne tıklayın. 
3. Günlük uç varsayılan olarak **kapalıdır** : etkinleştirmek için **Açık** ' a tıklayın ve ardından veri hacmi sınırını GB/gün cinsinden ayarlayın.

    ![Log Analytics veri sınırı yapılandırma](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Günlük sınıra ulaşıldığında uyar

Veri sınırı eşiğine ulaşıldığı zaman Azure portal görsel bir ipucu sunduğumuz sürece, bu davranış anında ilgilenilmesi gereken işlem sorunlarını yönetme biçiminize uygun değildir.  Bir uyarı bildirimi almak için Azure Izleyici 'de yeni bir uyarı kuralı oluşturabilirsiniz.  Daha fazla bilgi edinmek için bkz. [Uyarılar oluşturma, görüntüleme ve yönetme](alerts-metric.md).

Başlamanıza başlamak için, uyarının önerilen ayarları aşağıda verilmiştir:

- Hedef: Log Analytics kaynağını seçin
- Ölçütlere 
   - Sinyal adı: özel günlük araması
   - Arama sorgusu: Işlem | Burada ayrıntıların ' fazla kota ' olması
   - Temel alan: sonuç sayısı
   - Koşul: büyüktür
   - Eşik: 0
   - Süre: 5 (dakika)
   - Sıklık: 5 (dakika)
- Uyarı kuralı adı: günlük veri sınırına ulaşıldı
- Önem derecesi: uyarı (sev 1)

Uyarı tanımlandıktan ve sınıra ulaşıldığında, bir uyarı tetiklenir ve eylem grubunda tanımlanan yanıtı gerçekleştirir. E-posta ve SMS iletileri aracılığıyla ekibinize bildirimde bulunabilir veya Web kancaları, Otomasyon Runbook 'ları veya [bir dış ıSM çözümüyle tümleştirme](itsmc-overview.md#create-itsm-work-items-from-azure-alerts)kullanarak eylemleri otomatikleştirebilir. 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Kullanımın neden beklenenden daha yüksek olduğuyla ilgili sorunları giderme

Yüksek kullanımın nedeni aşağıdakilerden biri veya her ikisidir:
- Log Analytics çalışma alanına beklenenden daha fazla düğüm gönderilemedi
- Log Analytics çalışma alanına gönderilmekte olan beklenenden daha fazla veri

## <a name="understanding-nodes-sending-data"></a>Veri gönderen düğümleri anlama

Son ayın her gününde sinyal raporlayan bilgisayar sayısını anlamak için şunu kullanın

```kusto
Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```

Düğüm olarak faturalandırılacak bilgisayarların listesini almak için, çalışma alanı, eski düğüm başına fiyatlandırma katmanındaysa, **faturalandırılan veri türlerini** gönderen düğümleri arayın (bazı veri türleri ücretsizdir). Bunu yapmak için `_IsBillable` [özelliğini](log-standard-properties.md#_isbillable) kullanın ve tam etki alanı adının en solundaki alanı kullanın. Bu, faturalandırılan verileri içeren bilgisayarların listesini döndürür:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Görülen faturalandırılabilir düğümlerin sayısı şu şekilde tahmin edilebilir: 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| billableNodes=dcount(computerName)
```

> [!NOTE]
> Veri türlerindeki taramaların yürütülmesi pahalı olduğundan bu `union withsource = tt *` sorguları dikkatli bir şekilde kullanın. Bu sorgu, bilgisayar başına bilgilerin kullanım veri türüyle sorgulanması için eski yolu değiştirir.  

Gerçekte faturalandırılacaklarının daha doğru bir şekilde hesaplanması, faturalanan veri türlerini Gönderen saat başına bilgisayar sayısını almak olacaktır. (Eski düğüm başına fiyatlandırma katmanındaki çalışma alanları için, Log Analytics saatlik olarak faturalandırılması gereken düğüm sayısını hesaplar.) 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="understanding-ingested-data-volume"></a>Alınan veri birimini anlama

**Kullanım ve tahmini maliyetler** sayfasında, *çözüm başına veri* alma grafiği, gönderilen toplam veri hacmini ve her bir çözüm tarafından ne kadarının gönderileceğini gösterir. Bu, genel veri kullanımının (veya belirli bir çözümün kullanımı) büyümesi, sürekli olarak geri kalan veya azalan gibi eğilimleri belirlemenizi sağlar. Bunu oluşturmak için kullanılan sorgu

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1000. by bin(TimeGenerated, 1d), Solution| render barchart
```

"Isfaturalandırılabilir = true" yan tümcesinin, hiçbir alma ücreti bulunmayan belirli çözümlerden veri türlerini filtreleyeceğini unutmayın. 

Belirli veri türleri için veri eğilimlerini görmek için daha fazla ayrıntıya gidebilirsiniz, örneğin, IIS günlükleri nedeniyle verileri incelemek istiyorsanız:

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1000. by bin(TimeGenerated, 1d), Solution| render barchart
```

### <a name="data-volume-by-computer"></a>Bilgisayara göre veri hacmi

Bilgisayar başına alınan faturalandırılabilir olayların **boyutunu** görmek için, bayt cinsinden boyutu sağlayan `_BilledSize` [özelliğini](log-standard-properties.md#_billedsize)kullanın:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize Bytes=sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

`_IsBillable` [özelliği](log-standard-properties.md#_isbillable) , alınan verilerin ücretlendirip tabi olmayacağını belirtir.

Bilgisayar başına alınan **faturalandırılabilir** olay sayısını görmek için şunu kullanın: 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount=count() by computerName  | sort by eventCount nulls last
```

Faturalanabilir veri türlerinin belirli bir bilgisayara veri gönderdiğini öğrenmek istiyorsanız şunu kullanın:

```kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Azure kaynağına, kaynak grubuna veya aboneliğe göre veri hacmi

Azure 'da barındırılan düğümlerdeki veriler için, __bilgisayar başına__alınan faturalandırılabilir olayların **boyutunu** alabilir, kaynağın tam yolunu sağlayan _resourceıd [özelliğini](log-standard-properties.md#_resourceid)kullanın:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Azure 'da barındırılan düğümlerdeki veriler için, __Azure aboneliği başına__alınan faturalandırılabilir olayların **boyutunu** alabilir, `_ResourceId` özelliğini şu şekilde ayrıştırabilirsiniz:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

`subscriptionId` `resourceGroup` olarak değiştirmek, Azure Kaynak grubu 'na göre faturalandırılabilen veri hacmini gösterir. 


> [!NOTE]
> Kullanım verileri türünün bazı alanları şemada hala kullanım dışı bırakılmıştır ve değerleri artık doldurulmayacaktır. Bunlar, **bilgisayar** ve alma (**totaltoplu işler**, **batcheswithınsla**, **batchesoutsidesla**, **batchescaıda** **averageprocessingtimems**) ile ilgili alanlardır.

### <a name="querying-for-common-data-types"></a>Ortak veri türleri sorgulanıyor

Belirli bir veri türü için veri kaynağını daha ayrıntılı bir şekilde incelemek için bazı yararlı örnek sorgular aşağıda verilmiştir:

+ **Güvenlik** çözümü
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ **Günlük Yönetimi** çözümü
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ **Perf** veri türü
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ **Event** veri türü
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ **Syslog** veri türü
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ **AzureDiagnostics** veri türü
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>Veri hacmi azaltma ipuçları

Toplanan günlüklerin hacmini azaltmaya yönelik bazı öneriler şunlardır:

| Yüksek veri hacminin kaynağı | Veri hacmi nasıl azaltılır |
| -------------------------- | ------------------------- |
| Güvenlik olayları            | [Yaygın veya en az güvenlik olaylarını](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) seçin <br> Güvenlik denetimi ilkesini yalnızca gerekli olayları toplayacak şekilde değiştirin. Özellikle, şunlarla ilgili olayları toplamak gerekip gerekmediğini gözden geçirin: <br> - [filtre platformu denetimi](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [kayıt defteri denetimi](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [dosya sistemi denetimi](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [çekirdek nesnesi denetimi](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [tanıtıcı değiştirme denetimi](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - çıkarılabilir depolama birimi denetimi |
| Performans sayaçları       | [Performans sayacı yapılandırmasını](data-sources-performance-counters.md) şöyle değiştirin: <br> - Koleksiyonun sıklığını azaltın <br> - Performans sayaçlarının sayısını azaltın |
| Olay günlükleri                 | [Olay günlüğü yapılandırmasını](data-sources-windows-events.md) şöyle değiştirin: <br> - Toplanan olay günlüklerinin sayısını azaltın <br> - Yalnızca gerekli olay düzeylerini toplayın. Örneğin, *Bilgi* düzeyindeki olayları toplamayın |
| Syslog                     | [Syslog yapılandırmasını](data-sources-syslog.md) şu şekilde değiştirin: <br> - Toplanan tesislerin sayısını azaltın <br> - Yalnızca gerekli olay düzeylerini toplayın. Örneği *Bilgi* ve *Hata Ayıklama* düzeyindeki olayları toplamayın |
| AzureDiagnostics           | Aşağıdaki amaçlarla kaynak günlüğü koleksiyonunu değiştirin: <br> - Log Analytics’e günlük gönderen kaynak sayısını azaltma <br> - Yalnızca gerekli günlükleri toplama |
| Çözüm ihtiyacı olmayan bilgisayarlardan toplanan çözüm verileri | Yalnızca gerekli bilgisayar gruplarından veri toplamak için [çözüm hedefleme](../insights/solution-targeting.md) özelliğini kullanın. |

### <a name="getting-security-and-automation-node-counts"></a>Güvenlik ve otomasyon düğüm sayılarını alma

"Düğüm başına (OMS)" Fiyatlandırma Katmanı kullanıyorsanız, kullandığınız düğüm ve çözüm sayısına göre ücretlendirilirsiniz, faturalandırılmakta olduğunuz Öngörüler ve analiz düğümlerinin sayısı **kullanım ve tahmini maliyet** sayfasındaki tabloda gösterilir.  

Farklı güvenlik düğümlerinin sayısını görmek için sorguyu kullanabilirsiniz:

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

Farklı Otomasyon düğümlerinin sayısını görmek için sorguyu kullanın:

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="create-an-alert-when-data-collection-is-high"></a>Veri toplama işlemi yüksekse uyarı oluştur

Bu bölümde, aşağıdaki durumlarda nasıl uyarı oluşturulacağı açıklanır:
- Veri hacmi belirtilen bir miktarı aştığında.
- Veri hacminin belirtilen bir miktarı aşacağı tahmin edildiğinde.

Azure Uyarıları, arama sorguları kullanan [günlük uyarılarını](alerts-unified-log.md) destekler. 

Aşağıdaki sorgu, son 24 saatte 100 GB'den fazla veri toplandığında bir sonuç verir:

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type 
| where DataGB > 100
```

Aşağıdaki sorgu, ne zaman bir günde 100 GB'den fazla veri toplanacağını tahmin etmek için basit bir formül kullanır: 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type 
| where EstimatedGB > 100
```

Farklı bir veri hacminde uyarıda bulunmak için, sorgulardaki 100 değerini uyarılmak istediğiniz GB sayısıyla değiştirin.

Toplanan veri beklenen miktarı aştığında size bildirilmesini sağlamak için, [yeni günlük uyarısı oluşturma](alerts-metric.md) başlığı altında açıklanan adımları kullanın.

İlk sorgu için, yani 24 saat içinde 100 GB'den fazla veri toplandığında uyarı oluştururken şu ayarları yapın:  

- **Uyarı koşulunu tanımlama** adımında Log Analytics çalışma alanınızı kaynak hedefi olarak belirtin.
- **Uyarı ölçütleri** alanında aşağıdakileri belirtin:
   - **Sinyal Adı** bölümünde **Özel günlük araması**'nı seçin
   - **Arama sorgusu**: `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type | where DataGB > 100`
   - **Uyarı mantığı**, **Temeli** *bir dizi sonuçtur* ve **Koşul**, *Büyüktür* bir **Eşik değeri**, *0*
   - Kullanım verileri saatte bir güncelleştirildiğinden **Süre** *1440* dakika, **Uyarı sıklığı** ise *60* dakikada bir olarak belirlenmiştir.
- **Uyarı ayrıntılarını tanımlama** adımında aşağıdakileri belirtin:
   - **Ad**: *24 saat içinde 100 GB'den büyük veri hacmi*
   - **Önem derecesi**: *Uyarı*

Günlük uyarısı ölçütlerle eşleştiğinde bilgilendirme yapılması için var olan bir [Eylem Grubunu](action-groups.md) kullanın veya yeni bir tane oluşturun.

İkinci sorgu için, yani 24 saat içinde 100 GB'den fazla veri olacağı tahmin edildiğinde uyarı oluştururken şu ayarları yapın:

- **Uyarı koşulunu tanımlama** adımında Log Analytics çalışma alanınızı kaynak hedefi olarak belirtin.
- **Uyarı ölçütleri** alanında aşağıdakileri belirtin:
   - **Sinyal Adı** bölümünde **Özel günlük araması**'nı seçin
   - **Arama sorgusu**: `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type | where EstimatedGB > 100`
   - **Uyarı mantığı**, **Temeli** *bir dizi sonuçtur* ve **Koşul**, *Büyüktür* bir **Eşik değeri**, *0*
   - Kullanım verileri saatte bir güncelleştirildiğinden **Süre** *180* dakika, **Uyarı sıklığı** ise *60* dakikada bir olarak belirlenmiştir.
- **Uyarı ayrıntılarını tanımlama** adımında aşağıdakileri belirtin:
   - **Ad**: *24 saat içinde veri hacminin 100 GB'den büyük olacağı tahmin ediliyor*
   - **Önem derecesi**: *Uyarı*

Günlük uyarısı ölçütlerle eşleştiğinde bilgilendirme yapılması için var olan bir [Eylem Grubunu](action-groups.md) kullanın veya yeni bir tane oluşturun.

Uyarı aldığınızda, kullanımın neden beklenenden fazla olduğu konusundaki sorunları gidermek için aşağıdaki bölümde yer alan adımları kullanın.

## <a name="data-transfer-charges-using-log-analytics"></a>Log Analytics kullanarak veri aktarımı ücretleri

Verilerin Log Analytics gönderilmesi veri bant genişliği ücretlerine neden olabilirler. [Azure bant genişliği fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/bandwidth/)açıklandığı gibi, iki bölgede bulunan Azure hizmetleri arasındaki veri aktarımı, normal fiyata giden veri aktarımı olarak ücretlendirilir. Gelen veri aktarımı ücretsizdir. Ancak, bu ücret çok küçük (az%) Log Analytics veri alımı maliyetleriyle karşılaştırılır. Sonuç olarak, Log Analytics için maliyetleri denetlemek, verileri alınan veri hacminin üzerine odaklamalıdır ve [burada](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)bu konuda bilgi sağlanmasına yardımcı olacak rehberlik sunuyoruz.   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Log Analytics neden artık veri toplamadığına ilişkin sorun giderme

Eski ücretsiz fiyatlandırma katmanınız varsa ve günde 500 MB 'tan fazla veri gönderdikten sonra, veri toplama günün geri kalanı için duraklar. Günlük sınıra ulaşmak Log Analytics veri toplamayı durdurmasının ve verilerin eksik göründüğünden yaygın bir nedenidir.  Log Analytics, veri toplama başladığında ve durdurulduğunda Işlem türünde bir olay oluşturur. Günlük sınıra ve eksik verilere ulaşıp ulaşılmayacağını denetlemek için aramada aşağıdaki sorguyu çalıştırın: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Veri toplama durdurulduğunda, OperationStatus **uyarısı**olur. Veri toplama başladığında, OperationStatus **başarılı**olur. Aşağıdaki tabloda veri koleksiyonunun durduğu nedenler ve veri toplamayı sürdürmesi için önerilen bir eylem açıklanmaktadır:  

|Neden koleksiyonun durması| Çözüm| 
|-----------------------|---------|
|Eski ücretsiz fiyatlandırma katmanının günlük sınırına ulaşıldı |Koleksiyonun otomatik olarak yeniden başlatılmasını veya ücretli bir fiyatlandırma katmanına geçiş yapmak için aşağıdaki güne kadar bekleyin.|
|Çalışma alanınızın günlük tepesine ulaşıldı|Koleksiyonun otomatik olarak yeniden başlatılmasını bekleyin veya en fazla günlük veri birimini yönetme bölümünde açıklanan günlük veri birimi sınırını artırın. Günlük üst sınır sıfırlama zamanı, **veri hacmi yönetimi** sayfasında gösterilir. |
|Azure aboneliği şu nedenlerle askıya alınmış durumda:<br> Ücretsiz deneme süresi sona erdi<br> Azure Pass 'nin süre geçti<br> Aylık harcama sınırına ulaşıldı (örneğin, bir MSDN veya Visual Studio aboneliği)|Ücretli aboneliğe Dönüştür<br> Limiti Kaldır veya sınır sıfırlanana kadar bekle|

Veri toplama durdurulduğunda uyarılmak için, veri toplama durdurulduğunda bildirim almak üzere *günlük veri Cap uyarısı oluşturma* bölümünde açıklanan adımları kullanın. Uyarı kuralı için bir e-posta, Web kancası veya Runbook eylemi yapılandırmak üzere [eylem grubu oluşturma](action-groups.md) bölümünde açıklanan adımları kullanın. 

## <a name="limits-summary"></a>Limit Özeti

Bazıları Log Analytics fiyatlandırma katmanına bağlı olan bazı ek Log Analytics limitleri vardır. Bunlar [burada](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces)belgelenmiştir.


## <a name="next-steps"></a>Sonraki adımlar

- Arama dilinin nasıl kullanılacağını öğrenmek için bkz. [Azure Izleyici günlüklerinde günlük aramaları](../log-query/log-query-overview.md) . Kullanım verilerinde başka analizler yapmak için arama sorgularını kullanabilirsiniz.
- Bir arama ölçütü karşılandığında size bildirilmesini sağlamak için, [yeni günlük uyarısı oluşturma](alerts-metric.md) başlığı altında açıklanan adımları kullanın.
- Yalnızca gerekli bilgisayar gruplarından veri toplamak için [çözüm hedefleme](../insights/solution-targeting.md) özelliğini kullanın.
- Etkin bir olay toplama ilkesini yapılandırmak için [Azure Güvenlik Merkezi filtreleme ilkesini](../../security-center/security-center-enable-data-collection.md)gözden geçirin.
- [Performans sayacı yapılandırmasını](data-sources-performance-counters.md) değiştirin.
- Olay toplama ayarlarınızda değişiklik yapmak için, [olay günlüğü yapılandırması](data-sources-windows-events.md) konusunu gözden geçirin.
- Syslog koleksiyonu ayarlarınızda değişiklik yapmak için, [syslog yapılandırması](data-sources-syslog.md) konusunu gözden geçirin.