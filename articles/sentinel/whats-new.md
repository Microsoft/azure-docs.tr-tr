---
title: Azure Sentinel 'deki yenilikler
description: Bu makalede, son birkaç aydan Azure Sentinel 'deki yeni özellikler açıklanmaktadır.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 7f9a8cb54458999d8f20a258bc36241dfdbd0de8
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376044"
---
# <a name="whats-new-in-azure-sentinel"></a>Azure Sentinel 'deki yenilikler

Bu makalede, Azure Sentinel için eklenen son özellikler ve Azure Sentinel 'de gelişmiş bir kullanıcı deneyimi sağlayan ilgili hizmetlerde yeni özellikler listelenir.

Sunulan önceki özellikler hakkında daha fazla bilgi için bkz. [Tech Community bloglarımız](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

Belirtilen özellikler Şu anda ÖNIZLEME aşamasındadır. [Azure önizleme ek koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) , Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek yasal koşulları içerir.


> [!TIP]
> Microsoft 'un, takımlarınızın uyarlayabilmesi ve kullanabileceği belirli bir arama [sorguları](https://github.com/Azure/Azure-Sentinel) dahil olmak üzere [Azure Sentinel Community](https://github.com/Azure/Azure-Sentinel)'ye yönelik Microsoft 'a yönelik sorguları, PlayBook 'ları, çalışma kitaplarını ve not defterlerini ele alan tehdit.
>
> Ayrıca katkıda bulunabilirsiniz! [Azure Sentinel tehdit arayıcılar GitHub Community](https://github.com/Azure/Azure-Sentinel/wiki)' de bize katılarak.
>

## <a name="april-2021"></a>2021 Nisan

- [Olay zaman çizelgesi (Genel Önizleme)](#incident-timeline-public-preview)

### <a name="incident-timeline-public-preview"></a>Olay zaman çizelgesi (Genel Önizleme)

Olay Ayrıntıları sayfasındaki ilk sekme artık, olaydaki uyarı ve yer işaretlerinin zaman çizelgesini gösteren **zaman çizelgesi**' dir. Bir olayın zaman çizelgesi olayı daha iyi anlamanıza ve ilgili uyarılar ve yer işaretleri genelinde saldırgan etkinliğinin zaman çizelgesini yeniden oluşturmanıza yardımcı olabilir.

- Olay bağlamından çıkmadan ayrıntılarını görmek için zaman çizelgesinde bir öğe seçin
- Zaman çizelgesi içeriğini yalnızca uyarıları veya yer imlerini veya belirli bir önem derecesi ya da MITRE 'nın öğelerini göstermek için filtreleyin.
- **Günlükler** alanında ilgili olayları görmek için kaydın tamamını veya **Olaylar** bağlantısını görüntülemek için **Sistem Uyarısı kimliği** bağlantısını seçebilirsiniz.

Örnek:

:::image type="content" source="media/tutorial-investigate-cases/incident-timeline.png" alt-text="Olay zaman çizelgesi sekmesi":::

Daha fazla bilgi için bkz. [öğretici: Azure Sentinel ile olayları araştırma](tutorial-investigate-cases.md).

## <a name="march-2021"></a>Mart 2021

- [Çalışma kitaplarını görünüm modundayken otomatik olarak yenilenecek şekilde ayarla](#set-workbooks-to-automatically-refresh-while-in-view-mode)
- [Azure Güvenlik Duvarı için yeni algılamalar](#new-detections-for-azure-firewall)
- [Otomasyon kuralları ve olay tarafından tetiklenen PlayBook 'ları](#automation-rules-and-incident-triggered-playbooks) (tüm yeni PlayBook belgeleri dahil)
- [Yeni uyarı zenginleştirme: geliştirilmiş varlık eşlemesi ve özel ayrıntılar](#new-alert-enrichments-enhanced-entity-mapping-and-custom-details)
- [Azure Sentinel çalışma kitaplarınızı yazdırma veya PDF olarak kaydetme](#print-your-azure-sentinel-workbooks-or-save-as-pdf)
- [Olay filtreleri ve sıralama tercihleri artık oturumunuza kaydedilir (Genel Önizleme)](#incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview)
- [Microsoft 365 Defender olay tümleştirmesi (Genel Önizleme)](#microsoft-365-defender-incident-integration-public-preview)
- [Azure Ilkesi kullanarak yeni Microsoft hizmet bağlayıcıları](#new-microsoft-service-connectors-using-azure-policy)

### <a name="set-workbooks-to-automatically-refresh-while-in-view-mode"></a>Çalışma kitaplarını görünüm modundayken otomatik olarak yenilenecek şekilde ayarla

Azure Sentinel kullanıcıları artık, bir görüntüleme oturumu sırasında çalışma kitabı verilerini otomatik olarak yenilemek için yeni [Azure izleyici özelliğini](https://techcommunity.microsoft.com/t5/azure-monitor/azure-workbooks-set-it-to-auto-refresh/ba-p/2228555) kullanabilir.

Her çalışma kitabında veya çalışma kitabı şablonunda, :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false"::: Aralık seçeneklerinizi göstermek için **Otomatik Yenile** ' yi seçin. Geçerli görünüm oturumu için kullanmak istediğiniz seçeneği seçin ve **Uygula**' yı seçin.

- Desteklenen yenileme aralıkları **5 dakikadan** **1 güne** kadar değişir.
- Varsayılan olarak, otomatik yenileme kapalıdır. Performansı iyileştirmek için, bir çalışma kitabını her kapattığınızda otomatik yenileme de kapatılır ve arka planda çalıştırılmaz. Çalışma kitabını bir sonraki açışınızda gerektiğinde otomatik yenilemeyi yeniden açın.
- Bir çalışma kitabını düzenlerken otomatik yenileme duraklatılır ve düzenleme modundan görüntüleme moduna geçtiğinizde otomatik yenileme aralıkları yeniden başlatılır.

    Yenile düğmesini seçerek çalışma kitabını el ile yenilediğinizde aralıklar da yeniden başlatılır :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false":::  .

Daha fazla bilgi için bkz. Öğretici: verilerinizi ve [Azure izleyici belgelerini](../azure-monitor/visualize/workbooks-overview.md) [görselleştirin ve izleyin](tutorial-monitor-your-data.md) .

### <a name="new-detections-for-azure-firewall"></a>Azure Güvenlik Duvarı için yeni algılamalar

Azure Güvenlik Duvarı 'nın çeşitli kullanıma hazır algılamaları Azure Sentinel 'teki [analiz](import-threat-intelligence.md#analytics-puts-your-threat-indicators-to-work-detecting-potential-threats) alanına eklenmiştir. Bu yeni algılamalar, iç ağdaki makineler, algılama kuralı sorgusunda tanımlandığı şekilde, bilinen ıocs ile ilişkili İnternet etki alanı adlarını veya IP adreslerini sorgulamaya veya bunlara bağlanmayı denediklerinde, güvenlik ekiplerinin uyarı almasını sağlar.

Yeni algılamalar şunları içerir:

- [Solorigate ağ Işareti](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
- [Bilinen GALLıUM etki alanları ve karmaları](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
- [Bilinen ıRIDUM IP 'si](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
- [Bilinen Phosphorus grup etki alanları/IP](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
- [DCU ÖZETI 'ne dahil edilen THALUM etki alanları](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
- [Bilinen ZıNC ile ilgili maldoc karması](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)
- [Bilinen STRONTIUM Group etki alanları](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
- [NOINANıLMAZ um-etki alanı ve IP ıocs-Mart 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)


Azure Güvenlik duvarları için algılama, yerleşik şablon galerisine sürekli olarak eklenir. Azure Güvenlik Duvarı 'nın en son algılamalarını almak için, **kural şablonları**' nın altında, **veri kaynaklarını** **Azure Güvenlik Duvarı** ile filtreleyin:

:::image type="content" source="media/whats-new/new-detections-analytics-efficiency-workbook.jpg" alt-text="Analiz verimlilik çalışma kitabındaki yeni algılamalar":::

Daha fazla bilgi için bkz. [Azure 'Da Azure Güvenlik Duvarı Için yeni algılamalar](https://techcommunity.microsoft.com/t5/azure-network-security/new-detections-for-azure-firewall-in-azure-sentinel/ba-p/2244958).

### <a name="automation-rules-and-incident-triggered-playbooks"></a>Otomasyon kuralları ve olay tarafından tetiklenen PlayBook 'ları 'lar

Otomasyon kuralları Azure Sentinel 'de yeni bir kavramdır ve olay işleme otomasyonunu merkezi olarak yönetmenize olanak tanır. Otomasyon kuralları, bir seferde birden fazla analiz kuralına yönelik yanıtları otomatik hale getirmenize, el ile etiketleme, atayabilir veya kapatabilir ve bu da yürütülen eylemlerin sırasını kontrol etmenizi sağlar ve bunları çalıştırabilirsiniz. Otomasyon kuralları, Azure Sentinel 'de Otomasyon kullanımını kolaylaştırır ve olay düzenleme işlemleriniz için karmaşık iş akışlarını basitleştirmenize imkan sağlar.

[Otomasyon kurallarının bu tüm açıklamasından](automate-incident-handling-with-automation-rules.md)daha fazla bilgi edinin.

Yukarıda belirtildiği gibi, PlayBook 'lar artık uyarı tetikleyicisine ek olarak olay tetikleyicisi ile etkinleştirilebilir. Olay tetikleyicisi, PlayBook 'ları ile birlikte çalışmak için daha büyük bir giriş kümesi sağlar (olay, tüm uyarı ve varlık verilerini de içerdiğinden), yanıt iş akışlarınızda daha da fazla güç ve esneklik sağlar. Olay ile tetiklenen PlayBook 'lar Otomasyon kurallarından çağrılarak etkinleştirilir.

[PlayBook 'ları 'ın gelişmiş özellikleri](automate-responses-with-playbooks.md)hakkında daha fazla bilgi edinin ve [bir yanıt iş akışını, bir](tutorial-respond-threats-playbook.md) Otomasyon kurallarıyla birlikte PlayBook 'ları kullanarak nasıl oluşturabileceğinizi öğrenin.

### <a name="new-alert-enrichments-enhanced-entity-mapping-and-custom-details"></a>Yeni uyarı zenginleştirme: geliştirilmiş varlık eşlemesi ve özel ayrıntılar

Uyarıları daha kullanışlı ve daha bilgilendirici hale getirmek için iki yeni yolla uyarılarınızı zenginleştirin.

Varlık eşlemenizi bir sonraki düzeye alarak başlayın. Artık, kullanıcılardan, konaklardan ve IP adreslerinden, dosya ve işlemlere, posta kutularına, Azure kaynaklarına ve IoT cihazlarına kadar neredeyse 20 tür varlığı eşleyebilirsiniz. Her varlık için birden fazla tanımlayıcı da kullanabilirsiniz, bu da benzersiz tanımlamayı güçlendirin. Bu, daha geniş bağıntı ve daha güçlü araştırma sağlayan olaylarınızın daha zengin bir veri kümesini sağlar. Uyarılarınızda [varlıkları eşlemenin yeni yolunu öğrenin](map-data-fields-to-entities.md) .

[Varlıklar hakkında daha fazla bilgi edinin](entities-in-azure-sentinel.md) ve [kullanılabilir varlıkların tam listesini ve tanımlayıcılarını](entities-reference.md)görüntüleyin.

Uyarılarınızı ham olaylarınızın yüzey ayrıntılarına özelleştirerek, araştırma ve yanıt olanaklarınıza daha da fazla bir yükseltme sağlayın. Olaylarınızın olay içeriğini görünürlüğünü, güvenlik tehditlerine yanıt verme ve araştırma konusunda size daha fazla güç ve esneklik kazandırın. Uyarılarınız [için özel ayrıntıların nasıl yüzeyini öğrenin](surface-custom-details-in-alerts.md) .



### <a name="print-your-azure-sentinel-workbooks-or-save-as-pdf"></a>Azure Sentinel çalışma kitaplarınızı yazdırma veya PDF olarak kaydetme

Artık Azure Sentinel çalışma kitaplarını yazdırabilir ve bu sayede bunları PDF 'lere aktarıp yerel olarak veya paylaşıma kaydetmenizi da sağlayabilirsiniz.

Çalışma kitabınızda, :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **içerik Yazdır**> seçenekler menüsünü seçin. Ardından yazıcınızı seçin veya gerektiğinde **PDF olarak kaydet** ' i seçin.

:::image type="content" source="media/whats-new/print-workbook.png" alt-text="Çalışma kitabınızı yazdırın veya PDF olarak kaydedin.":::

Daha fazla bilgi için bkz. [öğretici: verilerinizi görselleştirme ve izleme](tutorial-monitor-your-data.md).

### <a name="incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview"></a>Olay filtreleri ve sıralama tercihleri artık oturumunuza kaydedilir (Genel Önizleme)

Artık olay filtrelerinizin ve sırınız, ürünün diğer alanlarında gezinirken bile Azure Sentinel oturumunuz boyunca kaydedilir.
Hala aynı oturumda yaptığınız sürece, Azure Sentinel 'deki [Olaylar](tutorial-investigate-cases.md) alanına geri dönmek, yaptığınız gibi filtrelerinizi ve sıralamayı gösterir.

> [!NOTE]
> Olay filtreleri ve sıralama, Azure Sentinel 'den ayrıldıktan veya tarayıcınızı yeninden sonra kaydedilmez.

### <a name="microsoft-365-defender-incident-integration-public-preview"></a>Microsoft 365 Defender olay tümleştirmesi (Genel Önizleme)

Azure Sentinel [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) olay tümleştirmesi, tüm M365D olaylarını Azure Sentinel 'e akışını ve her iki Portal arasında eşitlenmesini sağlar. M365D (eski adıyla Microsoft tehdit koruması veya MTP) olayları, ilişkili tüm uyarıları, varlıkları ve ilgili bilgileri içerir ve Azure Sentinel 'de önceliklendirme ve ön araştırma gerçekleştirmek için yeterli bağlam sağlar. Sentinel 'de, olaylar M365D ile eşitlenmiş olarak kalır ve olay araştırmanızda her iki portalın avantajlarından yararlanmanızı sağlar.

Hem Azure Sentinel hem de Microsoft 365 Defender 'ın birlikte kullanılması, her iki dünyanın da en iyi şekilde yararlanmanızı sağlar. Bir SıEM 'nin kuruluşunuzun tüm bilgi kaynakları kapsamında size sahip olduğunu ve ayrıca bir XDR 'nin Microsoft 365 kaynaklarınızı korumak için sunduğu özelleştirilmiş ve özel araştırma gücünün derinliğine sahip olduğunu ve bunların her ikisi de bu koordine ve sorunsuz SOC işlemleri için eşitlenmiş olduğunu elde edersiniz.

Daha fazla bilgi için bkz. [Azure Sentinel ile Microsoft 365 Defender tümleştirmesi](microsoft-365-defender-sentinel-integration.md).

### <a name="new-microsoft-service-connectors-using-azure-policy"></a>Azure Ilkesi kullanarak yeni Microsoft hizmet bağlayıcıları

[Azure ilkesi](../governance/policy/overview.md) , bir kaynağın özelliklerini zorlamak ve denetlemek için ilkeleri kullanmanıza olanak tanıyan bir Azure hizmetidir. İlkelerin kullanımı, kaynakların BT idare standartlarınızla uyumlu kalmasını sağlar.

İlkeler tarafından denetlenebilecek kaynakların özellikleri arasında, tanılama ve denetim günlüklerinin oluşturulması ve işlenmesi vardır. Azure Sentinel, günlükleri Azure Sentinel 'e almak istediğiniz belirli bir türün tüm (geçerli ve gelecekteki) kaynaklarına ortak bir tanılama günlüğü ayarı uygulamanızı sağlamak için Azure Ilkesini kullanıyor. Azure Ilkesi sayesinde, artık tanılama günlükleri ayarları kaynağını kaynağa göre ayarlamanız gerekmez.

Azure Ilke tabanlı bağlayıcılar artık aşağıdaki Azure hizmetleri için kullanılabilir:
- [Azure Key Vault](connect-azure-key-vault.md) (Genel Önizleme)
- [Azure Kubernetes hizmeti](connect-azure-kubernetes-service.md) (Genel Önizleme)
- Azure SQL veritabanları/sunucuları (GA)

Müşteriler yine de belirli örnekler için günlükleri el ile gönderebilecektir ve ilke altyapısını kullanmak zorunda kalmaz.

## <a name="february-2021"></a>Şubat 2021

- [Siber güvenlik vade Model sertifikası (CMMC) çalışma kitabı](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [Üçüncü taraf veri bağlayıcıları](#third-party-data-connectors)
- [Varlık sayfasında UEBA öngörüleri (Genel Önizleme)](#ueba-insights-in-the-entity-page-public-preview)
- [Geliştirilmiş olay araması (Genel Önizleme)](#improved-incident-search-public-preview)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>Siber güvenlik vade Model sertifikası (CMMC) çalışma kitabı

Azure Sentinel CMMC çalışma kitabı, Microsoft Güvenlik teklifleri, Office 365, takımlar, Intune, Windows sanal masaüstü ve çok daha fazlası dahil olmak üzere Microsoft portföy genelinde CMMC denetimlerine hizalanmış günlük sorgularını görüntülemek için bir mekanizma sağlar.

CMMC çalışma kitabı, güvenlik mimarları, mühendisler, güvenlik işlemleri analistleri, Yöneticiler ve BT uzmanlarının bulut iş yüklerinin güvenlik duruşunu durumsal tanıma görünürlüğü kazanmasına olanak sağlar. Ayrıca, Microsoft tekliflerini ilgili CMMC gereksinimleri ve uygulamalarıyla hizalama için seçme, tasarlama, dağıtma ve yapılandırma önerileri de vardır.

CMMC 'ye uymanız gerekli olmasa bile, CMMC çalışma kitabı güvenlik Işlemleri merkezleri oluşturma, uyarılar geliştirme, tehditleri görselleştirme ve iş yüklerinin durumsal farkında sağlama konusunda yardımcı olur.

Azure Sentinel **çalışma kitapları** alanında cmmc çalışma kitabına erişin. **Şablon**' u seçin ve ardından **cmmc**' yi arayın.

:::image type="content" source="media/whats-new/cmmc-guide-toggle.gif" alt-text="CMMC çalışma kitabı kılavuzunu aç ve Kapat" lightbox="media/whats-new/cmmc-guide-toggle.gif":::


Daha fazla bilgi için bkz.

- [Azure Sentinel Cybersecurity vade modeli sertifikası (CMMC) çalışma kitabı](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524)
- [Öğretici: Verilerinizi görselleştirme ve izleme](tutorial-monitor-your-data.md)


### <a name="third-party-data-connectors"></a>Üçüncü taraf veri bağlayıcıları

Üçüncü taraf tümleştirmelerimiz, son iki aya kadar otuz bağlayıcı eklendikçe büyümeye devam etmektedir. Bir liste aşağıda verilmiştir:

- [Agari savunma ve marka koruması](connect-agari-phishing-defense.md)
- [Akamai güvenlik olayları](connect-akamai-security-events.md)
- [Active Directory için alsıd](connect-alsid-active-directory.md)
- [Apache HTTP sunucusu](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [BlackBerry CylancePROTECT](connect-data-sources.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Cisco FirePOWER eStreamer](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [Cisco Birleşik Bilgi Işlem sistemi (UCS)](connect-cisco-ucs.md)
- [ESET kurumsal Denetçi](connect-data-sources.md)
- [ESET güvenlik Yönetim Merkezi](connect-data-sources.md)
- [Google çalışma alanı (eski adıyla G Suite)](connect-google-workspace.md)
- [Kusurda WAF ağ geçidi](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Netüse](connect-data-sources.md)
- [NXLog DNS günlükleri](connect-nxlog-dns.md)
- [NXLog Linux denetimi](connect-nxlog-linuxaudit.md)
- [Onapsıs platformu](connect-data-sources.md)
- [Düzeltme noktası Isteğe bağlı e-posta güvenliği (POD)](connect-proofpoint-pod.md)
- [Qualys güvenlik açığı yönetimi Bilgi Bankası](connect-data-sources.md)
- [Salesforce Service Cloud](connect-salesforce-service-cloud.md)
- [SonicWall güvenlik duvarı](connect-data-sources.md)
- [Sophos Cloud OPX](connect-sophos-cloud-optix.md)
- [Squid Proxy](connect-squid-proxy.md)
- [Symantec Endpoint Protection](connect-data-sources.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Eğilim mikro XDR](connect-data-sources.md)
- [VMware ESXi](connect-vmware-esxi.md)

### <a name="ueba-insights-in-the-entity-page-public-preview"></a>Varlık sayfasında UEBA öngörüleri (Genel Önizleme)

Azure Sentinel varlık ayrıntıları sayfaları, varlığa ilişkin davranış öngörülerini görüntüleyen ve anomali ve güvenlik tehditlerini hızlı bir şekilde belirlemek için yardım veren bir [Öngörüler bölmesi](identify-threats-with-entity-behavior-analytics.md#entity-insights)sağlar.

[Ueba etkinse](ueba-enrichments.md)ve en az dört gün bir zaman dilimi seçtiyseniz bu Öngörüler bölmesi artık, ueba öngörüleri için aşağıdaki yeni bölümleri de içerir:

|Section  |Açıklama  |
|---------|---------|
|**UEBA öngörüleri**     | Anormal Kullanıcı etkinliklerini özetler: <br>-Coğrafi konumlar, cihazlar ve ortamlar arasında<br>-Kullanıcının kendi geçmişiyle karşılaştırıldığında zaman ve sıklık Horizons <br>-Eşler davranışına kıyasla <br>-Kuruluşun davranışına kıyasla     |
|**Güvenlik grubu üyeliğine göre Kullanıcı eşleri**     |   Azure AD güvenlik grupları üyeliğine göre kullanıcının eşlerini listeler ve güvenlik işlemleri ekiplerine benzer izinleri paylaşan diğer kullanıcıların listesini sağlar.  |
|**Azure aboneliğine yönelik kullanıcı erişim Izinleri**     |     Kullanıcının Azure aboneliklerine doğrudan erişilebilen veya Azure AD grupları/hizmet sorumluları aracılığıyla erişim izinlerini gösterir.   |
|**Kullanıcıyla Ilgili tehdit göstergeleri**     |  Kullanıcının etkinliklerinde temsil edilen IP adresleriyle ilgili bilinen tehditler koleksiyonunu listeler. Tehditler tehdit türü ve ailesine göre listelenmiştir ve Microsoft 'un tehdit bilgileri hizmeti tarafından zenginleştirilebilir.       |
|     |         |

### <a name="improved-incident-search-public-preview"></a>Geliştirilmiş olay araması (Genel Önizleme)

Azure Sentinel olay arama deneyimini geliştirdik ve belirli bir tehdidi araştırırken olaylar aracılığıyla daha hızlı gezinmelerini sağlar.

Azure Sentinel 'de olayları ararken, artık aşağıdaki olay ayrıntılarına göre arama yapabileceksiniz:

- ID
- Başlık
- Ürün
- Sahip
- Etiket

## <a name="january-2021"></a>Ocak 2021

- [Analiz Kuralı Sihirbazı: Gelişmiş sorgu düzenlemesi deneyimi (Genel Önizleme)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [Az. Securityınsights PowerShell modülü (Genel Önizleme)](#azsecurityinsights-powershell-module-public-preview)
- [SQL Veritabanı Bağlayıcısı](#sql-database-connector)
- [Dynamics 365 Bağlayıcısı (Genel Önizleme)](#dynamics-365-connector-public-preview)
- [Geliştirilmiş olay açıklamaları](#improved-incident-comments)
- [Adanmış Log Analytics kümeleri](#dedicated-log-analytics-clusters)
- [Logic Apps tarafından yönetilen kimlikler](#logic-apps-managed-identities)
- [Analiz kuralı önizleme grafikleriyle geliştirilmiş kural ayarlama](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>Analiz Kuralı Sihirbazı: Gelişmiş sorgu düzenlemesi deneyimi (Genel Önizleme)

Azure Sentinel zamanlanmış analiz Kuralı Sihirbazı artık sorguları yazma ve düzenlemeyle ilgili aşağıdaki geliştirmeleri sağlar:

-   Sorgunuzu görüntülemek için daha fazla ekran alanı sağlayan genişletilebilir bir düzen penceresi.
-   Sorgu kodunuzda anahtar sözcük vurgulama.
-   Genişletilmiş otomatik tamamlama desteği.
-   Gerçek zamanlı sorgu doğrulamaları. Sorguınızdaki hatalar artık kaydırma çubuğunda kırmızı bir blok olarak ve **kural mantığını ayarla** sekmesi adında kırmızı bir nokta olarak gösterilir. Ayrıca, hatalı bir sorgu kaydedilemez.

Daha fazla bilgi için bkz. [öğretici: tehditleri algılamak için özel analiz kuralları oluşturma](tutorial-detect-threats-custom.md).
### <a name="azsecurityinsights-powershell-module-public-preview"></a>Az. Securityınsights PowerShell modülü (Genel Önizleme)

Azure Sentinel artık yeni [az. Securityınsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) PowerShell modülünü desteklemektedir.

**Az. Securityınsights** modülü, vs es, önem derecesi, sahip vb. değişiklik yapmak, olaylara yorum ve Etiketler eklemek ve yer işaretleri oluşturmak için olaylarla etkileşme gibi yaygın Azure Sentinel kullanım durumlarını destekler.

CI/CD işlem hattınız için [Azure Resource Manager (ARM)](../azure-resource-manager/templates/index.yml) şablonları kullanmanızı öneririz ancak, **az. securityınsights** modülü dağıtım sonrası görevler için yararlıdır ve SOC otomasyonu için hedeflenmelidir.  Örneğin, SOC otomasyonunda veri bağlayıcıları yapılandırma, analiz kuralları oluşturma veya analiz kurallarına Otomasyon eylemleri ekleme adımları bulunabilir.

Kullanılabilir cmdlet 'ler, parametre açıklamaları ve örneklerin tam listesi ve açıklaması dahil olmak üzere daha fazla bilgi için, [az. Securityınsights PowerShell belgelerine](/powershell/module/az.securityinsights/)bakın.

### <a name="sql-database-connector"></a>SQL Veritabanı Bağlayıcısı

Azure Sentinel artık, veritabanlarının denetim ve tanılama günlüklerini Azure Sentinel 'e akışını ve tüm örneklerinizin etkinliklerini sürekli olarak izlemek için kullanabileceğiniz bir Azure SQL Veritabanı Bağlayıcısı sunmaktadır.

Azure SQL, Kullanıcı katılımı olmadan yükseltme, düzeltme eki uygulama ve izleme gibi birçok veritabanı yönetim işlevini işleyen, tam olarak yönetilen bir hizmet olarak platform (PaaS) veritabanı altyapısıdır.

Daha fazla bilgi için bkz. [Azure SQL veritabanı tanılamayı ve denetim günlüklerini bağlama](connect-azure-sql-logs.md).

### <a name="dynamics-365-connector-public-preview"></a>Dynamics 365 Bağlayıcısı (Genel Önizleme)

Azure Sentinel artık, Microsoft Dynamics 365 için bir bağlayıcı sağlar ve bu, Dynamics 365 uygulamalarınızın Kullanıcı, yönetici ve destek etkinlik günlüklerini Azure Sentinel 'e toplamanıza olanak tanır. Bu verileri, gerçekleşen veri işleme eylemlerinin tamamını denetlemeye ve olası güvenlik ihlallerine yönelik analiz etmenize yardımcı olması için kullanabilirsiniz.

Daha fazla bilgi için bkz. [Dynamics 365 etkinlik günlüklerini Azure Sentinel 'e bağlama](connect-dynamics-365.md).

### <a name="improved-incident-comments"></a>Geliştirilmiş olay açıklamaları

Analistlerin olaylar üzerinde işbirliği yapmak, işlemleri ve adımları el ile veya bir PlayBook 'ın parçası olarak belgelemek için olay açıklamalarını kullanır. 

Geliştirilmiş olay yorum deneyimimiz, yorumlarınızı biçimlendirmeniz ve var olan açıklamaları düzenlemenizi veya silmenizi sağlar.

Daha fazla bilgi için bkz. [Microsoft Güvenlik uyarılarından olayları otomatik olarak oluşturma](create-incidents-from-alerts.md).
### <a name="dedicated-log-analytics-clusters"></a>Adanmış Log Analytics kümeleri

Azure Sentinel artık dağıtım seçeneği olarak adanmış Log Analytics kümelerini desteklemektedir. Şunu yaparsanız adanmış bir küme dikkate almanız önerilir:

- Azure Sentinel çalışma alanınıza **günde 1 TB 'tan fazla** alma
- Azure kaydınıza **birden çok Azure Sentinel çalışma alanı var**

Adanmış kümeler, aynı kümede birden fazla çalışma alanınız olduğunda, müşteri tarafından yönetilen anahtarlar, kasa, Çift şifreleme ve daha hızlı çalışma alanı sorguları gibi özellikleri kullanmanıza olanak sağlar.

Daha fazla bilgi için bkz. [Azure izleyici günlükleri adanmış kümeler](../azure-monitor/logs/logs-dedicated-clusters.md).

### <a name="logic-apps-managed-identities"></a>Logic Apps tarafından yönetilen kimlikler

Azure Sentinel, artık Azure Sentinel Logic Apps Bağlayıcısı için yönetilen kimlikleri destekler, böylece ek kimlikler oluşturmak yerine Azure Sentinel 'de çalışmak üzere doğrudan belirli bir PlayBook 'a izin vermenizi sağlar.

- **Yönetilen bir kimlik olmadığında** Logic Apps Bağlayıcısı, Azure Sentinel 'de çalıştırmak Için Azure Sentinel RBAC rolüne sahip ayrı bir kimlik gerektirir. Ayrı kimlik bir Azure AD kullanıcısı veya Azure AD kayıtlı uygulaması gibi bir hizmet sorumlusu olabilir.

- **Mantıksal uygulamanızda yönetilen kimlik desteğini açmak** mantıksal uygulamayı Azure AD 'ye kaydeder ve bır nesne kimliği sağlar. Azure Sentinel çalışma alanınızda bir Azure RBAC rolü ile mantıksal uygulamayı atamak için Azure Sentinel içindeki nesne KIMLIĞINI kullanın. 

Daha fazla bilgi için bkz.

- [Azure Logic Apps yönetilen kimlikle kimlik doğrulaması](../logic-apps/create-managed-service-identity.md)
- [Azure Sentinel Logic Apps bağlayıcı belgeleri](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>Analiz kuralı önizleme grafikleriyle geliştirilmiş kural ayarlama (Genel Önizleme)

Azure Sentinel artık analiz kurallarınızı daha iyi ayarlamanıza yardımcı olur ve bu da doğruluğu artırabilir ve paraziti azaltır.

**Kural mantığını ayarla** sekmesinde bir analiz kuralını düzenledikten sonra, sağdaki **sonuçlar benzetimi** alanını bulun. 

Azure Sentinel 'in analiz kuralınızın son 50 çalıştırmalarının benzetimini çalıştırmasını sağlamak için **geçerli verilerle test** ' i seçin. Değerlendirilen Ham olay verileri temelinde kuralın üretilebilecek Ortalama uyarı sayısını göstermek için bir grafik oluşturulur. 

Daha fazla bilgi için bkz. [kural sorgu mantığını tanımlama ve ayarları yapılandırma](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings).

## <a name="december-2020"></a>Aralık 2020

- [80 yeni yerleşik arama sorguları](#80-new-built-in-hunting-queries)
- [Log Analytics Aracısı geliştirmeleri](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 yeni yerleşik arama sorguları
 
Azure Sentinel 'in yerleşik arama sorguları, geçerli algılama kapsamındaki boşlukları azaltmak için SOC analistlerini ve yeni bir yeni arama müşteri adaylarını azaltır.

Azure Sentinel için bu güncelleştirme, MITRE ATT&CK çerçevesi matrisinde kapsam sağlayan yeni bir arama sorguları içerir:

- **Koleksiyon**
- **Komut ve denetim**
- **Kimlik bilgisi erişimi**
- **Bulma**
- **Yürütme**
- **Sızdırma**
- **Etki**
- **İlk erişim**
- **Kalıcılığı**
- **Ayrıcalık Yükseltme**

Eklenen arama sorguları, ortamınızda şüpheli etkinlik bulmanıza yardımcı olmak için tasarlanmıştır. Meşru etkinlikler ve potansiyel olarak kötü amaçlı etkinlikler döndürebilirler, ancak bu kişiler, avınız tahmin etmek için yararlı olabilir. 

Bu sorguları çalıştırdıktan sonra sonuçlarla emin olursunuz, bunları analiz kurallarına dönüştürmek veya var olan veya yeni olaylara arama sonuçları eklemek isteyebilirsiniz.

Tüm eklenen sorgular, Azure Sentinel arama sayfası aracılığıyla kullanılabilir. Daha fazla bilgi için bkz. [Azure Sentinel ile tehditler Için Hunt](hunting.md).

### <a name="log-analytics-agent-improvements"></a>Log Analytics Aracısı geliştirmeleri

Azure Sentinel kullanıcıları aşağıdaki Log Analytics Aracısı geliştirmelerinden faydalanır:

- CentOS 8, RedHat 8 ve SUSE Linux 15 dahil **daha fazla işletim sistemi Için destek**.
- Python 2 ' ye ek olarak **Python 3 desteği**

Azure Sentinel, Windows güvenlik olayları, syslog olayları, CEF günlükleri ve daha fazlası dahil olmak üzere çalışma alanınıza olay göndermek için Log Analytics aracısını kullanır.

> [!NOTE]
> Log Analytics Aracısı bazen OMS Aracısı veya Microsoft Monitoring Agent (MMA) olarak adlandırılır. 
> 

Daha fazla bilgi için [Log Analytics belgelerine](../azure-monitor/agents/log-analytics-agent.md) ve [Log Analytics Aracısı sürüm notlarına](https://github.com/microsoft/OMS-Agent-for-Linux/releases)bakın.
## <a name="november-2020"></a>Kasım 2020

- [Azure Sentinel 'de PlayBook sistem durumunu izleme](#monitor-your-playbooks-health-in-azure-sentinel)
- [Microsoft 365 Defender Bağlayıcısı (Genel Önizleme)](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>Azure Sentinel 'de PlayBook sistem durumunu izleme

Azure Sentinel PlayBook 'ları, görevleri, iş süreçlerini ve iş akışlarını zamanlamanıza, otomatikleştirmenize ve ayarlamanıza yardımcı olan bir bulut hizmeti olan [Azure günlük uygulamalarında](../logic-apps/index.yml)oluşturulan iş akışlarını temel alır. PlayBook 'lar bir olay oluşturulduğunda ya da önceliklendirme ve olaylar ile çalışırken otomatik olarak çağrılabilir. 

PlayBook 'larınızın sistem durumu, performansı ve kullanımı hakkında Öngörüler sağlamak için, **playbooks sistem durumu izleme** adında bir [çalışma kitabı](../azure-monitor/visualize/workbooks-overview.md) ekledik. 

Playbooks **sistem durumu izleme** çalışma kitabını kullanarak PlayBook 'larınızın sistem durumunu izleyin veya başarılı veya başarısız çalıştırmalar için bozukluklar olup olmadığına bakın. 

**Playbooks sistem durumu izleme** çalışma kitabı artık Azure Sentinel şablonlar galerisinde kullanılabilir:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Örnek playbooks sistem durumu izleme çalışma kitabı":::

Daha fazla bilgi için bkz.

- [Logic Apps belgeleri](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Azure İzleyici belgeleri](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Microsoft 365 Defender Bağlayıcısı (Genel Önizleme)
 
Azure Sentinel için Microsoft 365 Defender Bağlayıcısı, Microsoft 365 Defender 'dan Azure Sentinel 'e kadar gelişmiş arama günlüklerini (Ham olay verileri türü) akışla kullanmanıza olanak sağlar. 

[Uç nokta Için Microsoft Defender 'ın (MDADTP)](/windows/security/threat-protection/) [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) güvenliği şemsiye tümleştirmesiyle, artık microsoft Defender 'ı, Microsoft 365 Defender bağlayıcısını kullanarak uç nokta gelişmiş arama olayları Için toplayabilir ve bunları Azure Sentinel çalışma alanınızdaki yeni amaç tarafından oluşturulan tablolarda doğrudan akışla aktarabilirsiniz. 

Azure Sentinel tabloları, Microsoft 365 Defender portalında kullanılan şemaya kurulmuştur ve gelişmiş arama günlüklerinin tamamına tam erişim sağlar. 

Daha fazla bilgi için bkz. [Microsoft 365 Defender 'Dan Azure Sentinel 'e veri bağlama](connect-microsoft-365-defender.md).

> [!NOTE]
> Microsoft 365 Defender daha önce Microsoft tehdit koruması veya MTP olarak biliniyordu. Uç nokta için Microsoft Defender, daha önce Microsoft Defender Gelişmiş tehdit koruması veya MDADTP olarak biliniyordu.
> 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
>[Yerleşik Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Uyarılara yönelik görünürlük elde etme](quickstart-get-visibility.md)
