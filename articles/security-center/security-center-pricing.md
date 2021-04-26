---
title: Azure Güvenlik Merkezi Ücretsiz vs Azure Defender etkin
description: Azure Güvenlik Merkezi 'nde bulut iş yükü koruması için Azure Defender 'ı etkinleştirmenin avantajları hakkında bilgi edinin
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/23/2021
ms.openlocfilehash: aa65989953f761ff915383fcb59da7f36ea98dab
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600491"
---
# <a name="azure-security-center-free-vs-azure-defender-enabled"></a>Azure Güvenlik Merkezi Ücretsiz vs Azure Defender etkin
Azure Defender, ilk 30 gün boyunca ücretsizdir. 30 günün sonunda hizmeti kullanmaya devam etmeyi seçmeniz gerekir, kullanım için otomatik olarak ücretlendirmeye başlayacağız.

[Hızlı başlangıç: Azure Defender 'ı etkinleştirme](enable-azure-defender.md)bölümünde açıklandığı gibi **fiyatlandırma & ayarları** sayfasından yükseltebilirsiniz. Seçtiğiniz para birimindeki ve bölgenize göre fiyatlandırma ayrıntıları için bkz. [Güvenlik Merkezi fiyatlandırması](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="what-are-the-benefits-of-enabling-azure-defender"></a>Azure Defender 'ı etkinleştirmenin avantajları nelerdir?

Güvenlik Merkezi iki modda sunulur:

- Azure **Defender kapalı** (ücretsiz)-Azure Defender olmadan Güvenlik Merkezi, Azure Güvenlik merkezi panosunu Azure Portal ilk kez ziyaret ettığınızde veya API aracılığıyla programlı olarak etkinleştirilmişse tüm Azure aboneliklerinizde ücretsiz olarak etkinleştirilir. Bu ücretsiz modu kullanmak, Azure kaynaklarınızı korumanıza yardımcı olmak için güvenlik ilkesi, sürekli güvenlik değerlendirmesi ve eyleme dönüştürülebilir güvenlik önerileri sağlar.

- Azure **Defender on** -Azure Defender, ücretsiz modunun yeteneklerini özel ve diğer genel bulutlarda çalışan iş yüklerine genişleterek karma bulut iş yükleriniz arasında Birleşik güvenlik yönetimi ve tehdit koruması sağlar. Azure Defender 'ın bazı önemli özellikleri:

    - **Uç nokta Için Microsoft Defender** -sunucular Için Azure Defender, kapsamlı uç nokta algılama ve yanıt (EDR) Için [uç nokta için Microsoft Defender](https://www.microsoft.com/microsoft-365/security/endpoint-defender) içerir. Azure Defender ile Endpoint için Microsoft Defender 'ı kullanma avantajları hakkında daha fazla bilgi edinmek için [Güvenlik Merkezi 'nin tümleşik EDR çözümünü kullanın](security-center-wdatp.md).
    - **Sanal makineler ve kapsayıcı kayıt defterleri Için güvenlik açığı taraması** -sektörün güvenlik açığı yönetimi için en gelişmiş çözümünü sağlayan tüm sanal makinelerinize kolayca bir tarayıcı dağıtın. Bulguları doğrudan güvenlik merkezi içinde görüntüleyin, araştırın ve düzeltin. 
    - **Karma güvenlik** : tüm şirket içi ve bulut iş yüklerinizde güvenliğin birleştirilmiş bir görünümünü alın. Güvenlik standartları ile uyumluluğu sağlamak için güvenlik ilkelerini uygulayın ve hibrit bulut iş yüklerinizin güvenliğini sürekli değerlendirin. Güvenlik duvarları ve diğer iş ortağı çözümleri dahil olmak üzere birden çok kaynaktan güvenlik verilerini toplayın, arayın ve çözümleyin.
    - **Tehdit koruması uyarıları** -gelişmiş davranış analizi ve Microsoft Intelligent Security Graph, gelişen Cyber saldırıları üzerinde bir uç sağlar. Yerleşik davranış analizi ve makine öğrenimi, saldırıları ve sıfır günlük güvenlik açıklarını tanımlayabilir. Gelen saldırılar ve ihlal sonrası etkinlik için ağları, makineleri ve bulut hizmetlerini izleyin. Etkileşimli araçlar ve bağlamsal tehdit zekasıyla araştırmayı kolaylaştırın.
    - **Erişim ve uygulama denetimleri** (AAC)-belirli iş yüklerinize, izin verme ve reddetme listeleri oluşturmak üzere makine öğrenimi destekli öneriler uygulayarak kötü amaçlı yazılımları ve diğer Istenmeyen uygulamaları engelleyin. Azure VM 'lerinde yönetim bağlantı noktalarına tam zamanında, denetimli erişimle ağ saldırısı yüzeyini küçültün. AAC, deneme yanılma ve diğer ağ saldırılarına maruz kalmayı büyük ölçüde azaltır.
    - **Kapsayıcı güvenlik özellikleri** -Kapsayıcılı ortamlarınızda güvenlik açığı yönetimi ve gerçek zamanlı tehdit korumasından yararlanın. **Azure Defender 'ı kapsayıcı kayıt defterleri için** etkinleştirirken, tüm özellikler etkinleştirilene kadar 12 saat kadar sürebilir. Ücretler, bağlı kayıt defterinize gönderilen benzersiz kapsayıcı görüntülerinin sayısını temel alır. Bir görüntü bir kez tarandıktan sonra, bir kez değiştirilmediği ve bir kez itilemediği takdirde, bu yeniden ücretlendirilmezsiniz.
    - **Azure ortamına bağlı kaynaklar için kapsamlı tehdit koruması** -Azure Defender, tüm kaynaklarınız için ortak olan Azure hizmetleri için Azure-Native ve tehdit koruması içerir: Azure Resource Manager, Azure DNS, Azure ağ katmanı ve Azure Key Vault. Azure Defender, Azure Yönetim katmanı ve Azure DNS katmanının benzersiz görünürlüğüne sahiptir ve bu nedenle, bu katmanlara bağlı bulut kaynaklarını koruyabilir.


## <a name="faq---pricing-and-billing"></a>SSS-fiyatlandırma ve faturalandırma 

- [Kuruluşumun, güvenlik merkezi 'ndeki Azure Defender değişikliklerini etkinleştirdiğim kişileri nasıl izleyebilirim?](#how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center)
- [Güvenlik Merkezi tarafından sunulan planlar nelerdir?](#what-are-the-plans-offered-by-security-center)
- [Aboneliğim için Azure Defender'ı nasıl etkinleştirebilirim?](#how-do-i-enable-azure-defender-for-my-subscription)
- [Azure Defender'ı aboneliğimdeki sunucuların bir alt kümesinde yer alan sunucular için etkinleştirebilir miyim?](#can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription)
- [Uç nokta için bir Microsoft Defender lisansınız zaten varsa Azure Defender için bir indirim alabilir miyim?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Aboneliğimin etkinleştirilmiş sunucular için Azure Defender 'ı var, çalışan olmayan sunucular için ücret ödersiniz mi?](#my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [Log Analytics Aracısı yüklenmeden makineler için ücretlendirilmem gerekir mi?](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [Bir Log Analytics Aracısı birden çok çalışma alanına rapor veriyor, iki kez ücretlendirilecektir.](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [Bir Log Analytics Aracısı birden fazla çalışma alanına bildirirse, tüm bunlar üzerinde 500 MB boş veri alma işlemi kullanılabilir mi?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [Tüm çalışma alanı veya tamamen makine başına 500 MB boş veri alımı mi hesaplansın?](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)
- [500 MB veri günlük tahsisatta hangi veri türleri dahil edilir?](#what-data-types-are-included-in-the-500-mb-data-daily-allowance)


### <a name="how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center"></a>Kuruluşumun, güvenlik merkezi 'ndeki Azure Defender değişikliklerini etkinleştirdiğim kişileri nasıl izleyebilirim?
Azure aboneliklerinde fiyatlandırma ayarlarını değiştirme izinlerine sahip birden çok yönetici olabilir. Hangi kullanıcının bir değişiklik yaptığını öğrenmek için Azure etkinlik günlüğünü kullanın.

:::image type="content" source="media/security-center-pricing/logged-change-to-pricing.png" alt-text="Fiyatlandırma değişikliği olayını gösteren Azure etkinlik günlüğü":::

Kullanıcının bilgileri sütununda **tarafından başlatılan olay** listesinde yoksa, ilgili ayrıntılar IÇIN olayın JSON 'sini bulun.

:::image type="content" source="media/security-center-pricing/tracking-pricing-changes-in-activity-log.png" alt-text="Azure etkinlik günlüğü JSON Gezgini":::


### <a name="what-are-the-plans-offered-by-security-center"></a>Güvenlik Merkezi tarafından sunulan planlar nelerdir? 
Güvenlik Merkezi 'nin iki teklifi vardır: 

- Azure Güvenlik Merkezi ücretsiz 
- Azure Defender  

### <a name="how-do-i-enable-azure-defender-for-my-subscription"></a>Aboneliğim için Azure Defender'ı nasıl etkinleştirebilirim? 
Aboneliğiniz için Azure Defender 'ı etkinleştirmek üzere aşağıdaki yolların herhangi birini kullanabilirsiniz: 

| Yöntem                                          | Yönergeler                                                                                                                                       |
|-------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure portal Azure Güvenlik Merkezi sayfaları | [Azure Defender’ı etkinleştirme](enable-azure-defender.md)                                                                                                  |
| REST API                                        | [Prmerler API 'SI](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI                                       | [az Security fiyatlandırması](/cli/azure/security/pricing)                                                                                                 |
| PowerShell                                      | [Set-Azsecurityprsosu](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure İlkesi                                    | [Paket ları](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|                                                 |                                                                                                                                                    |

### <a name="can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>Azure Defender'ı aboneliğimdeki sunucuların bir alt kümesinde yer alan sunucular için etkinleştirebilir miyim?
Hayır. Bir abonelikteki [sunucular Için Azure Defender](defender-for-servers-introduction.md) 'ı etkinleştirdiğinizde, abonelikteki tüm sunucular Azure Defender tarafından korunur. 

Diğer bir seçenek de Log Analytics çalışma alanı düzeyinde sunucular için Azure Defender 'ı etkinleştirmektir. Bunu yaparsanız yalnızca bu çalışma alanına raporlama yapan sunucular korunur ve faturalandırılır. Ancak, bazı yetenekler kullanılamaz. Bunlar, tam zamanında VM erişimi, ağ algılamaları, mevzuat uyumluluğu, uyarlamalı ağ sağlamlaştırma, uyarlamalı uygulama denetimi ve daha fazlasını içerir. 

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Uç nokta için bir Microsoft Defender lisansınız zaten varsa Azure Defender için bir indirim alabilir miyim?
Uç nokta için bir Microsoft Defender lisansınız zaten varsa, Azure Defender lisansınızın bu bölümü için ödeme yapmak zorunda kalmazsınız.

İndirimi onaylamak için, güvenlik merkezi 'nin destek ekibine başvurun ve ilgili her lisans için ilgili çalışma alanı KIMLIĞI, bölge ve lisans bilgilerini belirtin.

### <a name="my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>Aboneliğimin etkinleştirilmiş sunucular için Azure Defender 'ı var, çalışan olmayan sunucular için ücret ödersiniz mi? 
Hayır. Bir abonelikteki [sunucular Için Azure Defender](defender-for-servers-introduction.md) 'ı etkinleştirdiğinizde, serbest bırakılmış güç durumunda olan tüm makineler söz konusu durumdayken ücretlendirilmezsiniz. Makineler, aşağıdaki tabloda gösterildiği gibi güç durumlarına göre faturalandırılır:

| Durum        | Açıklama                                                                                                                                      | Örnek kullanım faturalandırılıyor |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Başlatılıyor     | VM başlatılıyor.                                                                                                                               | Faturalandırılmamış            |
| Çalışma      | Bir VM için normal çalışma durumu                                                                                                                    | IP                |
| Durduruluyor     | Bu, geçici bir durumdur. İşlem tamamlandığında, durduruldu olarak gösterilir.                                                                           | IP                |
| Durduruldu      | VM, Konuk işletim sistemi içinden veya PowerOff API 'Leri kullanılarak kapatıldı. Donanım hala VM 'ye ayrıldı ve konakta kalır. | IP                |
| Serbest bırakılıyor | Geçiş durumu. İşlem tamamlandığında, VM serbest bırakıldı olarak gösterilir.                                                                             | Faturalandırılmamış            |
| Serbest bırakıldı  | VM başarıyla durdurulmuş ve konaktan kaldırılmış.                                                                                  | Faturalandırılmamış            |

:::image type="content" source="media/security-center-pricing/deallocated-virtual-machines.png" alt-text="Serbest bırakılmış bir makineyi gösteren Azure sanal makineleri":::

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>Log Analytics Aracısı yüklenmeden makineler için ücretlendirilmem gerekir mi?
Evet. Azure Defender 'ı bir abonelik üzerinde [sunucular için](defender-for-servers-introduction.md) etkinleştirdiğinizde, Bu abonelikteki makineler Log Analytics aracısını yüklememiş olsanız bile bir dizi koruma alır.

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Bir Log Analytics Aracısı birden çok çalışma alanına rapor veriyor, iki kez ücretlendirilecektir. 
Evet. Log Analytics aracınızı, verileri iki veya daha fazla farklı Log Analytics çalışma alanına (çoklu giriş) gönderecek şekilde yapılandırdıysanız, ' Güvenlik ' veya ' kötü amaçlı yazılımdan koruma ' çözümlerinin yüklü olduğu her çalışma alanı için ücretlendirilirsiniz. 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Bir Log Analytics Aracısı birden fazla çalışma alanına bildirirse, tüm bunlar üzerinde 500 MB boş veri alma işlemi kullanılabilir mi?
Evet. Log Analytics aracınızı, verileri iki veya daha fazla farklı Log Analytics çalışma alanına (çoklu giriş) gönderecek şekilde yapılandırdıysanız, 500 MB boş veri alımı alırsınız. Her düğüm için, bildirilen çalışma alanı başına, günlük olarak hesaplanır ve ' Güvenlik ' veya ' kötü amaçlı yazılımdan koruma ' çözümlerinin yüklü olduğu her çalışma alanında kullanılabilir. 500 MB üzerinden alınan tüm veriler için ücretlendirilirsiniz.

### <a name="is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine"></a>Tüm çalışma alanı veya tamamen makine başına 500 MB boş veri alımı mi hesaplansın?
Çalışma alanına bağlı her makine için günde 500 MB boş veri alımı alacaksınız. Özellikle Azure Güvenlik Merkezi tarafından doğrudan toplanan güvenlik veri türleri için.

Bu veri miktarı tüm düğümler arasında ortalama günlük orandır. Bu nedenle, bazı makineler 100 MB gönderiyor ve diğerleri 800-MB gönderiyor olsa da, toplam **[makine sayısı] x 500-MB** boş sınırı aşarsa ek ücret ödemezsiniz.

### <a name="what-data-types-are-included-in-the-500-mb-data-daily-allowance"></a>500 MB veri günlük tahsisatta hangi veri türleri dahil edilir?

Güvenlik Merkezi 'nin faturalandırması, Log Analytics için faturalandırmaya yakın bir şekilde bağlıdır. Güvenlik Merkezi, aşağıdaki [güvenlik veri türleri](/azure/azure-monitor/reference/tables/tables-category#security)alt KÜMESIYLE 500 MB/node/Day tahsisi sağlar:
- WindowsEvent
- SecurityAlert
- SecurityBaseline
- SecurityBaselineSummary
- SecurityDetection
- SecurityEvent
- WindowsFirewall
- MaliciousIPCommunication
- LinuxAuditLog
- SysmonEvent
- ProtectionStatus
- Güncelleştirme Yönetimi çözümü çalışma alanında çalışmadığı veya çözüm hedefleme etkin olduğunda Update ve UpdateSummary veri türleri

Çalışma alanı eski düğüm başına fiyatlandırma katmanındaysa, güvenlik merkezi ve Log Analytics ayırmaları birleştirilir ve tüm faturalandırılabilir veriler için birleştirilir.

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede Güvenlik Merkezi 'nin fiyatlandırma seçenekleri açıklanmaktadır. İlgili malzemeler için bkz.:

- [Azure iş yükü maliyetlerinizi iyileştirme](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Seçtiğiniz para birimi ve bölgenize göre fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/security-center/)
- Maliyetlerinizi yönetmek ve bir çözüm için toplanan veri miktarını belirli bir aracı kümesiyle sınırlayarak sınırlamak isteyebilirsiniz. [Çözüm hedefleme](../azure-monitor/insights/solution-targeting.md) , çözüme bir kapsam uygulamanıza ve çalışma alanındaki bilgisayarların bir alt kümesini hedeflemenizi sağlar. Çözüm hedefleme kullanıyorsanız, güvenlik merkezi çalışma alanını çözüm yok olarak listeler.
