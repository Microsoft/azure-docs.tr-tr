---
title: Azure Güvenlik Merkezi için sürüm notları
description: Azure Güvenlik Merkezi 'nde nelerin yeni ve değiştirilmiş olduğuna ilişkin bir açıklama
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 04/21/2021
ms.author: memildin
ms.openlocfilehash: c47543fa1401a8da533a9af3ebe4554e405622d3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835551"
---
# <a name="whats-new-in-azure-security-center"></a>Azure Güvenlik Merkezi 'ndeki yenilikler nelerdir?

Güvenlik Merkezi, etkin geliştirme aşamasındadır ve gelişmede geliştirmeler sunar. En son gelişmelerden haberdar olmak için bu sayfada yeni özellikler, hata düzeltmeleri ve kullanım dışı işlevler hakkında bilgi verilmektedir.

Bu sayfa sıklıkla güncelleştirildi, bu nedenle sık olarak yeniden ziyaret edin. 

Güvenlik Merkezi 'ne yakında çıkacak *planlı* değişiklikler hakkında bilgi edinmek için bkz. [Azure Güvenlik Merkezi 'nde yapılan önemli değişiklikler](upcoming-changes.md). 

> [!TIP]
> Altı aydan eski olan öğeleri arıyorsanız, [Azure Güvenlik Merkezi 'ndeki yenilikler Için arşivde](release-notes-archive.md)bulabilirsiniz.

## <a name="april-2021"></a>2021 Nisan

Nisan 'daki güncelleştirmeler şunları içerir:
- [Son çekilen kapsayıcı kayıt defteri görüntüleri artık haftalık olarak yeniden taranıp (genel kullanılabilirlik)](#recently-pulled-container-registry-images-are-now-rescanned-weekly-general-availability)
- [Karma ve çoklu bulut Kubernetes dağıtımlarını korumak için Kubernetes için Azure Defender 'ı kullanma (Önizleme)](#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-preview)
- [DNS ve Kaynak Yöneticisi için Azure Defender 'ı etkinleştirme önerileri (Önizleme)](#recommendations-to-enable-azure-defender-for-dns-and-resource-manager-preview)
- [Eklenen üç mevzuat uyumluluk standardı: Azure CIS 1.3.0, CMMC düzey 3 ve Yeni Zelanda ıSM kısıtlanmış](#three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted)
- [Konuk yapılandırmasıyla ilgili dört yeni öneri (Önizleme)](#four-new-recommendations-related-to-guest-configuration-preview)
- [CMK önerileri en iyi yöntemler güvenlik denetimine taşındı](#cmk-recommendations-moved-to-best-practices-security-control)
- [11 Azure Defender uyarıları kullanım dışı](#11-azure-defender-alerts-deprecated)
- ["Sistem güncelleştirmelerini Uygula" güvenlik denetiminin iki önerisi kullanım dışı bırakıldı](#two-recommendations-from-apply-system-updates-security-control-were-deprecated)
- [Makinedeki SQL için Azure Defender kutucuk Azure Defender panosundan kaldırıldı](#azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard)

### <a name="recently-pulled-container-registry-images-are-now-rescanned-weekly-general-availability"></a>Son çekilen kapsayıcı kayıt defteri görüntüleri artık haftalık olarak yeniden taranıp (genel kullanılabilirlik)

Kapsayıcı kayıt defterleri için Azure Defender, yerleşik bir güvenlik açığı tarayıcısı içerir. Bu tarayıcı, Kayıt defterinize gönderdiğiniz tüm görüntüyü ve son 30 gün içinde çekilen tüm görüntüyü hemen tarar.

Her gün yeni güvenlik açıkları bulunur. Bu güncelleştirmeyle, son 30 gün boyunca kayıt defterlerinden çekilen kapsayıcı görüntüleri her hafta yeniden **taranıp** . Bu, yeni keşfedilen güvenlik açıklarının görüntülerinizin tanımlanmasını sağlar.

Tarama, görüntü başına ücretlendirilir. bu nedenle, bu yeniden taramalar için ek ücret alınmaz.

Bu tarayıcı hakkında daha fazla bilgi için bkz. [Azure Defender 'ı kapsayıcı kayıt defterleri Için kullanarak görüntülerinizi güvenlik açıklarına karşı tarayın](defender-for-container-registries-usage.md).


### <a name="use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-preview"></a>Karma ve çoklu bulut Kubernetes dağıtımlarını korumak için Kubernetes için Azure Defender 'ı kullanma (Önizleme)

Kubernetes için Azure Defender, dağıttıkları her yerde kümelerinizi savunmak için tehdit koruması yeteneklerini genişletiyor. Bu, [Azure Arc etkin Kubernetes](../azure-arc/kubernetes/overview.md) ve yeni [uzantı özellikleri](../azure-arc/kubernetes/extensions.md)ile tümleştirilirken etkinleştirilmiştir. 

Azure 'da olmayan Kubernetes kümelerinizde Azure yayı 'yi etkinleştirdiğinizde, Azure Güvenlik Merkezi 'nin yeni bir önerisi, Azure Defender uzantısını yalnızca birkaç tıklamayla dağıtmak üzere sunmaktadır.

Öneriyi kullanın (**Azure Arc etkin Kubernetes kümeleri, Azure Defender 'ın uzantısının yüklü olması gerekir**) ve yönetilen Kubernetes hizmetlerinde olmasa da diğer bulut sağlayıcılarıyla dağıtılan Kubernetes kümelerini koruma uzantısı.

Azure Güvenlik Merkezi, Azure Defender ve Azure Arc etkin Kubernetes arasındaki bu tümleştirme şunları getirir:

- Azure Defender uzantısının korumasız Azure Arc etkin Kubernetes kümelerine kolay sağlanması (el ile ve ölçekli)
- Azure Arc portalından Azure Defender uzantısının ve sağlama durumunun izlenmesi
- Güvenlik Merkezi 'ndeki güvenlik önerileri, Azure Arc portalının yeni güvenlik sayfasında raporlanır
- Azure 'daki güvenlik tehditleri, Azure Arc portalının yeni güvenlik sayfasında raporlanır
- Azure Arc etkin Kubernetes kümeleri, Azure Güvenlik Merkezi platformu ve deneyimiyle tümleşiktir

Şirket [içi ve çoklu bulut Kubernetes kümelerinizle Kubernetes Için Azure Defender 'ı kullanma](defender-for-kubernetes-azure-arc.md)hakkında daha fazla bilgi edinin.

:::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Güvenlik Merkezi 'nin Azure Arc etkin Kubernetes kümeleri için Azure Defender uzantısını dağıtmaya yönelik önerisi." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::


### <a name="recommendations-to-enable-azure-defender-for-dns-and-resource-manager-preview"></a>DNS ve Kaynak Yöneticisi için Azure Defender 'ı etkinleştirme önerileri (Önizleme)

[Kaynak Yöneticisi Için Azure Defender](defender-for-resource-manager-introduction.md) 'ı ve [DNS Için Azure Defender 'ı](defender-for-dns-introduction.md)etkinleştirme sürecini basitleştirmek üzere iki yeni öneri eklenmiştir:

- **Kaynak Yöneticisi Için Azure Defender etkinleştirilmiş** olmalıdır-Defender, Kaynak Yöneticisi için otomatik olarak kuruluşunuzdaki kaynak yönetimi işlemlerini izler. Azure Defender tehditleri algılar ve şüpheli etkinlikler hakkında sizi uyarır.
- **DNS Için Azure Defender etkin** OLMALıDıR-DNS için Defender, Azure kaynaklarınızdaki tüm DNS sorgularını sürekli olarak izleyerek bulut kaynaklarınız için ek bir koruma katmanı sağlar. Azure Defender, DNS katmanında şüpheli etkinlik hakkında sizi uyarır.

Azure Defender planlarını etkinleştirme, ücretlere neden olur. Güvenlik Merkezi 'nin fiyatlandırma sayfasındaki Bölge başına fiyatlandırma ayrıntıları hakkında bilgi edinin: https://aka.ms/pricing-security-center .

> [!TIP]
> Önizleme önerileri bir kaynağı sağlıksız bir şekilde işlemez ve güvenli puanınızın hesaplamalarına dahil değildir. Önizleme dönemi sona erdiğinde puanınızın altına katkıda bulunmak için bunları mümkün olduğunda düzeltin. [Azure Güvenlik Merkezi 'ndeki önerileri düzeltin](security-center-remediate-recommendations.md)bölümünde bu önerilere yanıt verme hakkında daha fazla bilgi edinin.


### <a name="three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted"></a>Eklenen üç mevzuat uyumluluk standardı: Azure CIS 1.3.0, CMMC düzey 3 ve Yeni Zelanda ıSM kısıtlanmış

Azure Güvenlik Merkezi ile kullanılmak üzere üç standart ekledik. Yasal uyumluluk panosunu kullanarak bundan sonra uyumluluğunuzu izleyebilirsiniz:

- [CIS Microsoft Azure temelleri kıyaslaması 1.3.0](../governance/policy/samples/cis-azure-1-3-0.md)
- [CMMC düzey 3](../governance/policy/samples/cmmc-l3.md)
- [Yeni Zelanda ıSM kısıtlanmış](../governance/policy/samples/new-zealand-ism.md)

Bunları [, mevzuat uyumluluk panonuzdaki standartlar kümesini özelleştirme](update-regulatory-compliance-packages.md)bölümünde açıklandığı gibi aboneliklerinize atayabilirsiniz.

:::image type="content" source="media/release-notes/additional-regulatory-compliance-standards.png" alt-text="Azure Güvenlik Merkezi 'nin mevzuat uyumluluk panosu ile kullanılmak üzere üç standart eklenmiştir." lightbox="media/release-notes/additional-regulatory-compliance-standards.png":::

Daha fazlasını öğrenin:
- [Yasal uyumluluk panonuzdaki standartlar kümesini özelleştirme](update-regulatory-compliance-packages.md)
- [Öğretici: Mevzuat uyumluluğunuzu artırma](security-center-compliance-dashboard.md)
- [SSS - Mevzuat uyumluluğu panosu](security-center-compliance-dashboard.md#faq---regulatory-compliance-dashboard)

### <a name="four-new-recommendations-related-to-guest-configuration-preview"></a>Konuk yapılandırmasıyla ilgili dört yeni öneri (Önizleme)

Azure 'un [Konuk yapılandırma uzantısı](../governance/policy/concepts/guest-configuration.md) , sanal makinelerinizin Konuk içi ayarlarının sağlamlaştırılmış olduğundan emin olmak Için Güvenlik Merkezi 'ne rapor eder. Arc bağlı makine aracısına dahil edildiğinden, genişleme özellikli sunucular için uzantı gerekli değildir. Uzantı, makinede sistem tarafından yönetilen bir kimlik gerektirir.

Bu uzantının en iyi şekilde kullanılabilmesini sağlamak için Güvenlik Merkezi 'ne dört yeni öneri ekledik.

- Uzantıyı ve sistem tarafından yönetilen gerekli kimliğini yüklemek için iki öneri istenir:
    - **Makinelerinizde Konuk yapılandırma uzantısının yüklü olması gerekir**
    - **Sanal makinelerin Konuk yapılandırma uzantısı, sistem tarafından atanan yönetilen kimlikle dağıtılmalıdır**

- Uzantı yüklenip çalıştırıldığında, makinelerinizi denetlemeye başlayacaktır ve işletim sistemi ve ortam ayarlarının yapılandırılması gibi ayarları almanız istenecektir. Bu iki öneri, Windows ve Linux makinelerinizi açıklandığı şekilde en fazla bilgi ister:
    - **Makinelerinizde Windows Defender Exploit Guard etkinleştirilmelidir**
    - **Linux makinelerinde kimlik doğrulaması için SSH anahtarları gerekir**

[Azure Ilkesinin Konuk yapılandırmasını anlama](../governance/policy/concepts/guest-configuration.md)bölümünde daha fazla bilgi edinin.

### <a name="cmk-recommendations-moved-to-best-practices-security-control"></a>CMK önerileri en iyi yöntemler güvenlik denetimine taşındı

Her kuruluşun güvenlik programı, veri şifreleme gereksinimlerini içerir. Varsayılan olarak, Azure müşterilerinin verileri, hizmet tarafından yönetilen anahtarlarla geri kalanında şifrelenir. Ancak, yasal uyumluluk standartlarını karşılamak için genellikle müşteri tarafından yönetilen anahtarlar (CMK) gereklidir. CMKs, verilerinizi oluşturduğunuz ve size ait olan bir [Azure Key Vault](../key-vault/general/overview.md) anahtarıyla şifrelemenize olanak tanır. Bu, döndürme ve yönetim dahil olmak üzere anahtar yaşam döngüsü için tam denetim ve sorumluluk sağlar.

Azure Güvenlik Merkezi 'nin güvenlik denetimleri, ilgili güvenlik önerilerinin mantıksal gruplarıdır ve savunmasız saldırı yüzeylerinizi yansıtır. Tüm kaynaklarınız için denetimde listelenen tüm önerileri düzeltmeniz durumunda her denetim, güvenli puanınızı ekleyebileceğiniz en fazla sayıda noktaya sahiptir. **En iyi güvenlik uygulamaları** uygulama güvenlik denetimi sıfır noktalara uygundur. Bu nedenle bu denetimdeki öneriler, güvenli puanınızı etkilemez.

Aşağıda listelenen öneriler, isteğe bağlı yapısını daha iyi yansıtacak şekilde **güvenlik en iyi yöntemlerini Uygula** güvenlik denetimine taşınıyor. Bu taşıma, Bu önerilerin amacını karşılamak için en uygun denetimde olmasını sağlar.

- Azure Cosmos DB hesaplar, bekleyen verileri şifrelemek için müşteri tarafından yönetilen anahtarları kullanmalıdır
- Azure Machine Learning çalışma alanları, müşteri tarafından yönetilen bir anahtarla şifrelenmelidir (CMK)
- Bilişsel hizmetler hesapları, müşteri tarafından yönetilen bir anahtarla (CMK) veri şifrelemeyi etkinleştirmelidir
- Kapsayıcı kayıt defterleri, müşteri tarafından yönetilen bir anahtarla şifrelenmelidir (CMK)
- SQL yönetilen örnekler, bekleyen verileri şifrelemek için müşteri tarafından yönetilen anahtarları kullanmalıdır
- SQL Server 'lar, bekleyen verileri şifrelemek için müşteri tarafından yönetilen anahtarları kullanmalıdır
- Depolama hesapları, şifreleme için müşteri tarafından yönetilen anahtar (CMK) kullanmalıdır

[Güvenlik denetimlerinde ve bunların önerilerinde](secure-score-security-controls.md#security-controls-and-their-recommendations)her güvenlik denetiminde hangi önerilerin olduğunu öğrenin.


### <a name="11-azure-defender-alerts-deprecated"></a>11 Azure Defender uyarıları kullanım dışı

Aşağıda listelenen on bir Azure Defender uyarısı kullanım dışıdır.

- Yeni uyarılar, bu iki uyarıyı değiştirecek ve daha iyi kapsam sağlayacak:

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | PREVIEW-mikro patlama araç seti "Get-AzureDomainInfo" işlev çalıştırması algılandı |
    | ARM_MicroBurstRunbook    | PREVIEW-mikro patlama araç seti "Get-AzurePasswords" işlev çalıştırması algılandı  |
    |                          |                                                                          |

- Bu dokuz uyarı, zaten kullanım dışı bırakılmış bir Azure Active Directory Kimlik Koruması Bağlayıcısı (IPC) ile ilgilidir:

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | Bilinmeyen oturum açma özellikleri |
    | AnonymousLogin      | Anonim IP adresi          |
    | Bulaşıp Teddevicelogin | Kötü amaçlı yazılım bağlı IP adresi     |
    | Impossıbleseyahat    | Olağandışı yolculuk               |
    | MaliciousIP         | Kötü amaçlı IP adresi          |
    | LeakedCredentials   | Sızdırılan kimlik bilgileri            |
    | Passwordpüskürtme       | Parola spreyi                |
    | LeakedCredentials   | Azure AD tehdit bilgileri  |
    | AADAı               | Azure AD AI                   |
    |                     |                               |
 
    > [!TIP]
    > Bu dokuz IPC uyarısı hiç güvenlik merkezi uyarıymıştı. Bunlar, güvenlik merkezi 'ne gönderen Azure Active Directory (AAD) kimlik koruma bağlayıcısının (IPC) bir parçasıdır. Son iki yılda, bu uyarıları görmekte olan müşteriler yalnızca 2019 veya önceki sürümlerde dışarı aktarmayı (bağlayıcıdan ASC 'ye) yapılandıran kuruluşlardır. AAD IPC bunları kendi uyarı sistemlerinde göstermeye devam etti ve Azure Sentinel 'de sunulmaya devam ediyor. Tek değişiklik artık Güvenlik Merkezi 'nde görünistemdir.

### <a name="two-recommendations-from-apply-system-updates-security-control-were-deprecated"></a>"Sistem güncelleştirmelerini Uygula" güvenlik denetiminin iki önerisi kullanım dışı bırakıldı 

Aşağıdaki iki öneri kullanımdan kaldırılmıştır ve değişiklikler güvenli puanınızın üzerinde küçük bir etkiye neden olur:

- **Sistem güncelleştirmelerinin uygulanabilmesi için makineleriniz yeniden başlatılmalıdır**
- **İzleme Aracısı makinelerinizde yüklü olmalıdır**. Bu öneri yalnızca şirket içi makinelerle ilgilidir ve mantığının bir kısmı başka bir öneriye aktarılacaksa **Log Analytics aracı sistem durumu sorunları makinelerinizde çözümlenmelidir**

Bu önerilerin bunlara dahil edilip edilmeyeceğini görmek için sürekli dışarı aktarma ve iş akışı Otomasyonu yapılandırmalarının denetlenmesini öneririz. Ayrıca, bunları kullanıyor olabilecek panolar veya diğer izleme araçları da buna uygun şekilde güncelleştirilmeleri gerekir.

[Güvenlik önerileri başvuru sayfasında](recommendations-reference.md)bu öneriler hakkında daha fazla bilgi edinin.

### <a name="azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard"></a>Makinedeki SQL için Azure Defender kutucuk Azure Defender panosundan kaldırıldı

Azure Defender panosunun kapsam alanı, ortamınız için ilgili Azure Defender planlarına yönelik kutucukları içerir. Korunan ve korumasız kaynakların sayısı ile ilgili bir sorun nedeniyle, sorun çözülene kadar **MAKINELERDE SQL Için Azure Defender** kaynak kapsamı durumunu geçici olarak kaldırmaya karar verdik.


## <a name="march-2021"></a>Mart 2021

Mart 'taki güncelleştirmeler şunları içerir:

- [Güvenlik Merkezi ile tümleştirilmiş Azure Güvenlik Duvarı yönetimi](#azure-firewall-management-integrated-into-security-center)
- [SQL güvenlik açığı değerlendirmesi artık "kuralı devre dışı bırak" deneyimini (Önizleme) içerir](#sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview)
- [Güvenlik Merkezi ile tümleştirilmiş Azure Izleyici çalışma kitapları ve üç şablon sağlanmaktadır](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)
- [Mevzuat uyumluluk panosu artık Azure Denetim raporlarını içerir (Önizleme)](#regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview)
- [Öneri verileri, Azure Kaynak grafiğinde "ARG 'da keşfet" ile görüntülenebilir](#recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg)
- [İş akışı Otomasyonu dağıtmaya yönelik ilkelere yönelik güncelleştirmeler](#updates-to-the-policies-for-deploying-workflow-automation)
- [Artık doğrudan Azure etkinlik günlüğüne veri yazmayan iki eski öneri](#two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log)
- [Öneriler sayfası iyileştirmeleri](#recommendations-page-enhancements)


### <a name="azure-firewall-management-integrated-into-security-center"></a>Güvenlik Merkezi ile tümleştirilmiş Azure Güvenlik Duvarı yönetimi

Azure Güvenlik Merkezi 'ni açtığınızda, görüntülenecek ilk sayfa genel bakış sayfasıdır. 

Bu etkileşimli Pano, hibrit bulut iş yüklerinizin güvenlik duruşuna birleştirilmiş bir görünüm sağlar. Ayrıca, güvenlik uyarılarını, kapsam bilgilerini ve daha fazlasını gösterir.

Güvenlik durumunuzu merkezi bir deneyimden görüntülemenize yardımcı olma kapsamında, Azure Güvenlik Duvarı yöneticisini bu panoya tümleştirdik. Artık tüm ağlarda güvenlik duvarı kapsamı durumunu denetleyebilir ve Güvenlik Merkezi 'nden başlayarak Azure Güvenlik Duvarı ilkelerini merkezi olarak yönetebilirsiniz.

[Azure Güvenlik Merkezi 'nin Genel Bakış sayfasında](overview-page.md)bu pano hakkında daha fazla bilgi edinin.

:::image type="content" source="media/release-notes/overview-dashboard-firewall-manager.png" alt-text="Güvenlik Merkezi 'nin Azure Güvenlik Duvarı kutucuğuna ilişkin genel bakış panosu":::


### <a name="sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview"></a>SQL güvenlik açığı değerlendirmesi artık "kuralı devre dışı bırak" deneyimini (Önizleme) içerir

Güvenlik Merkezi, olası veritabanı güvenlik açıklarını keşfetmenize, izlemenize ve düzeltmenize yardımcı olan yerleşik bir güvenlik açığı tarayıcısı içerir. Değerlendirme taramalarınızın sonuçları, SQL makinelerinizin güvenlik durumuna genel bir bakış ve güvenlik bulgularının ayrıntıları sağlar.

Bir bulmayı yok saymanız gereken bir kuruluş varsa, bunu düzeltmek yerine isteğe bağlı olarak devre dışı bırakabilirsiniz. Devre dışı bulgular, güvenli puanınızı etkilemez veya istenmeyen gürültü oluşturmaz.

[Belirli bulguları devre dışı bırakma](defender-for-sql-on-machines-vulnerability-assessment.md#disable-specific-findings-preview)konusunda daha fazla bilgi edinin.



### <a name="azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided"></a>Güvenlik Merkezi ile tümleştirilmiş Azure Izleyici çalışma kitapları ve üç şablon sağlanmaktadır

Ignite Spring 2021 kapsamında, güvenlik merkezi 'nde tümleşik bir Azure Izleyici çalışma kitapları deneyimi duyurduk.

Yeni tümleştirmeden yararlanarak Güvenlik Merkezi 'nin galerisinden kullanıma hazır şablonları kullanmaya başlayabilirsiniz. Çalışma kitabı şablonlarını kullanarak, kuruluşunuzun güvenlik duruşunu izlemek için dinamik ve görsel raporlara erişebilir ve bunları oluşturabilirsiniz. Ayrıca, güvenlik merkezi verilerine veya desteklenen diğer veri türlerine göre yeni çalışma kitapları oluşturabilir ve topluluk çalışma kitaplarını Güvenlik Merkezi 'nin GitHub topluluğundan hızlıca dağıtabilirsiniz.

Üç şablon raporu sağlanır:

- **Zaman Içinde güvenli puan** -aboneliklerinizin puanlarını ve kaynaklarınızın önerilerini izleyin
- **Sistem güncelleştirmeleri** -kaynaklar, işletim sistemi, önem derecesi ve daha fazlası için eksik sistem güncelleştirmelerini görüntüleyin
- **Güvenlik açığı değerlendirmesi bulguları** -Azure kaynaklarınızın güvenlik açığı taramalarının bulgularını görüntüleyin

Bu raporları kullanmayı veya kendi kendinize ait [zengin, etkileşimli güvenlik merkezi veri raporlarınızı oluşturmayı](custom-dashboards-azure-workbooks.md)öğrenin.

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Zaman içindeki güvenli puan raporu":::


### <a name="regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview"></a>Mevzuat uyumluluk panosu artık Azure Denetim raporlarını içerir (Önizleme)

Yasal uyumluluk panosunun araç çubuğundan artık Azure ve Dynamics sertifika raporlarını indirebilirsiniz. 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="Mevzuat uyumluluk panosunun araç çubuğu":::

İlgili rapor türleri için sekmeyi seçebilirsiniz (PCI, SOC, ISO ve diğerleri) ve ihtiyacınız olan belirli raporları bulmak için filtreleri kullanabilirsiniz.

[Yasal uyumluluk panonuzda standartları yönetme](update-regulatory-compliance-packages.md)hakkında daha fazla bilgi edinin.

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard.png" alt-text="Kullanılabilir Azure denetim raporları listesini filtreleme":::



### <a name="recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg"></a>Öneri verileri, Azure Kaynak grafiğinde "ARG 'da keşfet" ile görüntülenebilir

Öneri ayrıntıları sayfaları artık "ARG 'da keşfet" araç çubuğu düğmesini içerir. Bu düğmeyi kullanarak bir Azure Kaynak Grafiği sorgusu açın ve önerinin verilerini bulun, dışarı aktarın ve paylaşabilirsiniz.

Azure Kaynak Grafiği (ARG), güçlü filtreleme, gruplama ve sıralama özelliklerine sahip bulut ortamlarınızdaki kaynak bilgilerine anında erişim sağlar. Azure aboneliklerindeki bilgileri programlı olarak veya Azure portal içinden sorgulamak için hızlı ve verimli bir yoldur.

[Azure Kaynak Grafiği (arg)](../governance/resource-graph/index.yml)hakkında daha fazla bilgi edinin.

:::image type="content" source="media/release-notes/explore-in-resource-graph.png" alt-text="Azure Kaynak Grafiği 'nde öneri verilerini araştırma.":::


### <a name="updates-to-the-policies-for-deploying-workflow-automation"></a>İş akışı Otomasyonu dağıtmaya yönelik ilkelere yönelik güncelleştirmeler

Kuruluşunuzun izleme ve olay yanıtı süreçlerini otomatik hale getirmek, güvenlik olaylarını araştırmak ve azaltmak için gereken süreyi büyük ölçüde iyileştirebilir.

Kuruluşunuzda otomatikleştirilmesini dağıtabilmeniz için iş akışı Otomasyonu yordamlarını oluşturan ve yapılandıran üç Azure Ilkesi ' DeployIfNotExist ' ilkesi sunuyoruz:

|Hedef  |İlke  |İlke KIMLIĞI  |
|---------|---------|---------|
|Güvenlik uyarıları için iş akışı Otomasyonu|[Azure Güvenlik Merkezi uyarıları için İş Akışı Otomasyonu dağıtımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Güvenlik önerileri için iş akışı Otomasyonu|[Azure Güvenlik Merkezi önerileri için İş Akışı Otomasyonu dağıtımı](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
|Yasal uyumluluk değişiklikleri için iş akışı Otomasyonu|[Azure Güvenlik Merkezi mevzuatı uyumluluğu için Iş akışı Otomasyonu dağıtma](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-A5F1-d0dac20be63c|
||||

Bu ilkelerin özelliklerine iki güncelleştirme vardır:

- Atandığında, bu uygulama zorlama tarafından etkin kalır.
- Artık bu ilkeleri özelleştirebilir ve parametreleri daha önce dağıtıldıktan sonra bile güncelleştirebilirsiniz. Örneğin, bir Kullanıcı başka bir değerlendirme anahtarı eklemek isterse veya mevcut bir değerlendirme anahtarını düzenlemek isterse, bunu yapabilir.

[İş akışı Otomasyonu şablonları](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)ile çalışmaya başlayın.

[Güvenlik Merkezi tetikleyicilerine yapılan yanıtları otomatikleştirme](workflow-automation.md)hakkında daha fazla bilgi edinin.


### <a name="two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log"></a>Artık doğrudan Azure etkinlik günlüğüne veri yazmayan iki eski öneri 

Güvenlik Merkezi, Azure Advisor 'a neredeyse tüm güvenlik önerileri için verileri geçirir, bu da [Azure etkinlik günlüğüne](../azure-monitor/essentials/activity-log.md)yazar.

İki öneri için, veriler eşzamanlı olarak doğrudan Azure etkinlik günlüğüne yazılır. Bu değişiklik ile, güvenlik merkezi bu eski güvenlik önerileri için verileri doğrudan etkinlik günlüğüne yazmayı durduruyor. Bunun yerine, diğer tüm önerilerin yaptığımız gibi verileri Azure Advisor 'a dışarı aktaracağız.

Bu iki eski öneri şunlardır:
- Endpoint Protection sistem durumu sorunları makinelerinizde çözümlenmelidir
- Makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir

Bu iki öneriye ilişkin bilgilere, Etkinlik günlüğünün "TaskDiscovery türü önerisi" kategorisinin bilgilerine erişiyorsanız, artık kullanılamaz.


### <a name="recommendations-page-enhancements"></a>Öneriler sayfası iyileştirmeleri 

Bir bakışta daha fazla bilgi sunmak için öneriler listesinin gelişmiş bir sürümünü yayımladık.

Şimdi şu sayfada göreceksiniz:

1. Her güvenlik denetiminin en fazla puanı ve geçerli puanı.
1. **Hızlı düzelme** ve **Önizleme** gibi etiketleri değiştirme simgeleri.
1. "Denetimleri Gruplandır" devre dışı bırakıldığında, her öneriye ilişkin [ilke girişiminin](security-policy-concept.md) gösterildiği yeni bir sütun.

:::image type="content" source="media/release-notes/recommendations-grid-enhancements.png" alt-text="Azure Güvenlik Merkezi 'nin öneriler sayfasında geliştirmeler-Mart 2021" lightbox="media/release-notes/recommendations-grid-enhancements.png":::

:::image type="content" source="media/release-notes/recommendations-grid-enhancements-initiatives.png" alt-text="Azure Güvenlik Merkezi 'nin öneriler ' düz ' listesi geliştirmeleri-Mart 2021" lightbox="media/release-notes/recommendations-grid-enhancements-initiatives.png":::

[Azure Güvenlik Merkezi 'Nde güvenlik önerileri](security-center-recommendations.md)hakkında daha fazla bilgi edinin.


## <a name="february-2021"></a>Şubat 2021

Şubat 'taki güncelleştirmeler şunları içerir:

- [Genel kullanıma sunulduktan Azure portal yeni güvenlik uyarıları sayfası (GA)](#new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga)
- [Kubernetes iş yükü koruma önerileri genel kullanıma sunuldu (GA)](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [Azure Defender ile Endpoint Integration için Microsoft Defender artık Windows Server 2019 ve Windows 10 sanal masaüstü (WVD) desteklemektedir (önizlemede)](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview)
- [Öneri ayrıntıları sayfasından ilkeye doğrudan bağlantı](#direct-link-to-policy-from-recommendation-details-page)
- [SQL veri sınıflandırması önerisi artık güvenli puanınızı etkilemesiz](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [İş akışı tahminleri, mevzuat uyumluluk değerlendirmelerinde yapılan değişikliklere göre tetiklenebilir (önizlemede)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)
- [Varlık envanteri sayfa iyileştirmeleri](#asset-inventory-page-enhancements)


### <a name="new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga"></a>Genel kullanıma sunulduktan Azure portal yeni güvenlik uyarıları sayfası (GA)

Azure Güvenlik Merkezi 'nin güvenlik uyarıları sayfası, şunları sağlayacak şekilde yeniden tasarlandı:

- **Uyarılar Için geliştirilmiş önceliklendirme deneyimi** -uyarıları daha kolay hale getirmeye ve en ilgili tehditlere odaklanmaya yardımcı olma, listede özelleştirilebilir filtreler ve gruplama seçenekleri de bulunur.
- **Uyarılar listesinde,** MITRE ATT&ACK Tactika gibi daha fazla bilgi.
- **Örnek uyarılar oluşturma düğmesi** -Azure Defender özelliklerini değerlendirmek ve uyarılarınızı test etmek. Yapılandırma (SıEM tümleştirmesi, e-posta bildirimleri ve iş akışı otomasyonları için), tüm Azure Defender planlarından örnek uyarılar oluşturabilirsiniz.
- **Azure Sentinel 'in olay deneyimiyle hizalaması** -her iki ürünü de kullanan müşteriler için, aralarında geçiş yapmak artık daha basit bir deneyimdir ve bunlardan birini öğrenmenin kolay bir deneyimi vardır.
- Büyük uyarı listeleri için **daha iyi performans** .
- Uyarı listesi aracılığıyla **klavye gezintisi** .
- **Azure Kaynak grafının uyarıları** -tüm kaynaklarınızın kusto benzeri API 'Si olan Azure Kaynak Grafında uyarıları sorgulayabilirsiniz. Bu, kendi uyarı panolarınızı oluşturuyorsanız de kullanışlıdır. [Azure Kaynak Grafiği hakkında daha fazla bilgi edinin](../governance/resource-graph/index.yml).
- **Örnek uyarılar oluşturma** -yeni uyarılar deneyiminden örnek uyarılar oluşturmak için bkz. [örnek Azure Defender uyarıları](security-center-alert-validation.md#generate-sample-azure-defender-alerts)oluşturma.

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure Güvenlik Merkezi 'nin güvenlik uyarıları listesi":::


### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Kubernetes iş yükü koruma önerileri genel kullanıma sunuldu (GA)

Kubernetes iş yükü korumaları için öneri kümesinin genel kullanılabilirliğini (GA) duyurmaktan mutluluk duyuyoruz.

Kubernetes iş yüklerinin varsayılan olarak güvenli olduğundan emin olmak için, güvenlik merkezi Kubernetes giriş denetimi ile zorlama seçenekleri dahil olmak üzere Kubernetes düzeyi sağlamlaştırma önerilerini ekledi.

Kubernetes için Azure Ilke eklentisi, Azure Kubernetes Service (AKS) kümenize yüklendiğinde, Kubernetes API sunucusuna gönderilen her istek, kümede kalıcı hale gelmeden önce 13 güvenlik önerisi olarak görüntülenen, önceden tanımlanmış en iyi uygulamalar kümesine göre izlenir. Daha sonra, en iyi uygulamaları zorlamak ve gelecekteki iş yükleri için bunları zorunlu kılmak üzere yapılandırabilirsiniz.

Örneğin, ayrıcalıklı kapsayıcıların oluşturulmaması ve ileride yapılacak istekleri engellenemeyeceksiniz.

[Kubernetes giriş denetimini kullanan Iş yükü koruma en iyi uygulamaları](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)hakkında daha fazla bilgi edinin.

> [!NOTE]
> Öneriler önizlemede olduğundan, AKS küme kaynağını sağlıksız bir şekilde işlemedik ve güvenli puanınızın hesaplamalarına dahil etmiyordu. Bu GA duyurusu ile bunlar puan hesaplamasına dahil edilir. Zaten düzeltilmediyse, bu durum güvenli puanınızda küçük bir etkiye neden olur. [Azure Güvenlik Merkezi 'ndeki önerileri](security-center-remediate-recommendations.md)düzeltme bölümünde açıklandığı gibi, mümkün olan yerlerde bunları düzeltin.


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview"></a>Azure Defender ile Endpoint Integration için Microsoft Defender artık Windows Server 2019 ve Windows 10 sanal masaüstü (WVD) desteklemektedir (önizlemede)

Uç nokta için Microsoft Defender, bütünsel, bulut tarafından sunulan bir uç nokta güvenlik çözümüdür. Bu, risk tabanlı güvenlik açığı yönetimi ve değerlendirmesi ve uç nokta algılama ve yanıt (EDR) sağlar. Defender for Endpoint for the Azure Security Center ile birlikte kullanmanın avantajlarından tam bir listesi için bkz. [Güvenlik Merkezi 'nin tümleşik EDR çözümü ile uç noktalarınızı koruma: uç nokta Için Microsoft Defender](security-center-wdatp.md).

Windows Server 'da sunucular için Azure Defender 'ı etkinleştirdiğinizde, plana uç nokta için bir Defender lisansı dahildir. Sunucular için Azure Defender 'ı zaten etkinleştirdiyseniz ve aboneliğinizde Windows 2019 sunucularınız varsa, bu güncelleştirme ile otomatik olarak Defender for Endpoint alırlar. El ile eylem gerekli değildir. 

Windows Server 2019 ve [Windows sanal masaüstü (WVD)](../virtual-desktop/overview.md)dahil etmek için destek artık genişletildi.

> [!NOTE]
> Windows Server 2019 makinesinde Endpoint için Defender 'ı etkinleştirdiğinizden, [Endpoint Integration Için Microsoft Defender 'ı etkinleştirme](security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration)bölümünde açıklanan önkoşulları karşıladığından emin olun.

### <a name="direct-link-to-policy-from-recommendation-details-page"></a>Öneri ayrıntıları sayfasından ilkeye doğrudan bağlantı

Bir önerinin ayrıntılarını gözden geçirirken genellikle temel alınan ilkeyi görmeniz yararlı olur. Bir ilke tarafından desteklenen her öneri için, öneri ayrıntıları sayfasından yeni bir bağlantı vardır:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Öneriyi destekleyen belirli bir ilke için Azure Ilke sayfasına bağlantı":::

İlke tanımını görüntülemek ve değerlendirme mantığını gözden geçirmek için bu bağlantıyı kullanın. 

[Güvenlik önerileri başvuru kılavuzumuzdaki](recommendations-reference.md)önerilerin listesini gözden geçiriyorsunuz, ayrıca ilke tanımı sayfalarına bağlantılar da görürsünüz:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Belirli bir ilke için Azure Ilke sayfasına doğrudan Azure Güvenlik Merkezi öneriler başvuru sayfasından erişme" lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>SQL veri sınıflandırması önerisi artık güvenli puanınızı etkilemesiz
**SQL veritabanlarınızdaki önerinin hassas verileri** , artık güvenli puanınızı etkilememelidir. Bu, denetim artık 0 ' ın güvenli bir puan değerine sahip olması için **veri sınıflandırması** güvenlik denetimindeki tek öneriden oluşur.

Güvenlik Merkezi 'ndeki tüm güvenlik denetimlerinin puanlarını ve her bir içindeki önerilerin listesini içeren tam bir liste için bkz. [güvenlik denetimleri ve önerileri](secure-score-security-controls.md#security-controls-and-their-recommendations).

### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview"></a>İş akışı tahminleri, mevzuat uyumluluk değerlendirmelerinde yapılan değişikliklere göre tetiklenebilir (önizlemede)
İş akışı otomasyonuna yönelik tetikleyici seçeneklerine üçüncü bir veri türü ekledik: mevzuat uyumluluk değerlendirmelerinde yapılan değişiklikler.

[Güvenlik Merkezi tetikleyicilerine yönelik yanıtları otomatik hale getirmek için](workflow-automation.md)iş akışı Otomasyon araçlarını nasıl kullanacağınızı öğrenin.

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Bir iş akışı Otomasyonu tetiklemek için mevzuat uyumluluk değerlendirmelerinde yapılan değişiklikleri kullanma" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


### <a name="asset-inventory-page-enhancements"></a>Varlık envanteri sayfa iyileştirmeleri
Güvenlik Merkezi 'nin varlık Envanteri sayfası aşağıdaki yollarla geliştirilmiştir:

- Artık sayfanın en üstündeki özetler, güvenlik merkezi 'nin etkinleştirilmeksizin aboneliklerin sayısını gösteren **kayıtsız abonelikler** içerir.

    :::image type="content" source="media/release-notes/unregistered-subscriptions.png" alt-text="Varlık envanteri sayfasının en üstündeki özetlerdeki kayıtlı olmayan aboneliklerin sayısı":::

- Filtreler genişletilir ve şunları içerecek şekilde geliştirilmiştir:
    - **Sayımlar** -her bir filtre, her bir kategorinin ölçütlerine uyan kaynak sayısını gösterir

        :::image type="content" source="media/release-notes/counts-in-inventory-filters.png" alt-text="Azure Güvenlik Merkezi 'nin varlık envanteri sayfasındaki filtrelerdeki sayımlar":::

    - **Muafiyet Filtresi içerir** (isteğe bağlı)-dışarıda olan/olmayan kaynaklara Sonuçları daraltın. Bu filtre varsayılan olarak gösterilmez, ancak **Filtre Ekle** düğmesinden erişilebilir.

        :::image type="content" source="media/release-notes/adding-contains-exemption-filter.gif" alt-text="Azure Güvenlik Merkezi 'nin varlık envanteri sayfasına ' istisna içeriyor ' filtresini ekleme":::

[Varlık envanteriyle kaynaklarınızı araştırıp yönetme](asset-inventory.md)hakkında daha fazla bilgi edinin.

## <a name="january-2021"></a>Ocak 2021

Ocak ayında güncelleştirmeler şunları içerir:

- [Azure Güvenlik kıyaslaması artık Azure Güvenlik Merkezi için varsayılan ilke girişimidir](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [Şirket içi ve çoklu bulut makinelerinde güvenlik açığı değerlendirmesi, genel kullanıma sunuldu (GA)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [Yönetim grupları için güvenli puan şimdi önizlemede kullanılabilir](#secure-score-for-management-groups-is-now-available-in-preview)
- [Güvenli puan API 'SI genel kullanıma sunuldu (GA)](#secure-score-api-is-released-for-general-availability-ga)
- [App Service için Azure Defender 'a Dangling DNS korumaları eklendi](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [Çoklu bulut bağlayıcıları genel kullanıma sunuldu (GA)](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [Abonelikler ve yönetim grupları için tüm önerileri güvenli puanınızdan muaf tutma](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [Kullanıcılar artık genel yöneticlerinden kiracı genelinde görünürlük isteğinde bulunabilir](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [Azure Güvenlik kıyaslamasının kapsamını artırmak için 35 önizleme önerisi eklendi](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [Filtrelenmiş öneriler listesinin CSV dışa aktarılması](#csv-export-of-filtered-list-of-recommendations)
- ["Uygulanamaz" kaynakları artık Azure Ilke değerlendirmelerinde "uyumlu" olarak bildirildi](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [Sürekli dışa aktarma ile güvenli Puanlama ve mevzuat uyumluluk verilerinin haftalık anlık görüntülerini dışarı aktarma (Önizleme)](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>Azure Güvenlik kıyaslaması artık Azure Güvenlik Merkezi için varsayılan ilke girişimidir

Azure Güvenlik kıyaslaması, yaygın uyumluluk çerçevelerine göre güvenlik ve uyum en iyi uygulamaları için Microsoft tarafından yazılan, Azure 'a özgü bir dizi kılavuzlardır. Bu, yaygın olarak kullanılan kıyaslama, [Internet güvenliği (CIS) Için merkezden](https://www.cisecurity.org/benchmark/azure/) ve [ulusal standartlar ve teknolojı Enstitüsü (NIST)](https://www.nist.gov/) ile bulut merkezli güvenliğe odaklanarak yapılar oluşturur.

Son aylarda Güvenlik Merkezi 'nin yerleşik güvenlik önerileri listesi, Bu kıyaslama kapsamımızın kapsamını genişletmenizi önemli ölçüde artmıştır.

Bu sürümden, kıyaslama, güvenlik merkezi 'nin önerilerinden ve varsayılan ilke girişimi olarak tam tümleştirilen bir temelidir. 

Tüm Azure hizmetlerinin belgelerinde güvenlik temeli sayfası vardır. Örneğin, [Bu güvenlik merkezi 'nin temelini](security-baseline.md). Bu temeller Azure Güvenlik kıyaslaması üzerine kurulmuştur.

Güvenlik Merkezi 'nin mevzuat uyumluluk panosunu kullanıyorsanız, bir geçiş dönemi boyunca kıyaslamaya ait iki örnek görürsünüz:

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="Azure Güvenlik Merkezi 'nin Azure Güvenlik kıyaslaması gösteren yasal uyumluluk panosu":::

Mevcut öneriler etkilenmemiştir ve kıyaslama arttıkça, değişiklikler otomatik olarak güvenlik merkezi 'nde yansıtılır. 

Daha fazla bilgi edinmek için aşağıdaki sayfalara bakın:

- [Azure Güvenlik kıyaslaması hakkında daha fazla bilgi](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [Yasal uyumluluk panonuzdaki standartlar kümesini özelleştirme](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>Şirket içi ve çoklu bulut makinelerinde güvenlik açığı değerlendirmesi, genel kullanıma sunuldu (GA)

Ekim 'de, Azure Arc etkin sunucularını, sunucuların tümleşik güvenlik açığı değerlendirmesi tarayıcısı (Qualys tarafından desteklenir) [Için Azure Defender](defender-for-servers-introduction.md)ile taramanın bir önizlemesini duyurduk.

Artık genel kullanılabilirlik (GA) için yayımlanmıştır.

Azure dışı makinelerinizde Azure yayı 'yi etkinleştirdiyseniz, Güvenlik Merkezi, tümleşik güvenlik açığı tarayıcısını bunlara el ile ve ölçekli olarak dağıtmayı sağlar.

Bu güncelleştirmeyle, Azure Defender 'ın tüm Azure ve Azure dışı varlıklarınızda güvenlik açığı yönetimi programınızı birleştirmek için **Azure Defender** 'ın gücünü açığa çıkarın.

Ana yetenekler:

- Azure Arc makinelerinde VA (güvenlik açığı değerlendirmesi) tarayıcı sağlama durumunu izleme
- Tümleşik VA aracısını korumasız Windows ve Linux Azure yay makinelerine sağlama (el ile ve ölçekli)
- Dağıtılan aracılardan algılanan güvenlik açıklarını alma ve çözümleme (el ile ve ölçekli)
- Azure sanal makineleri ve Azure yay makineleri için Birleşik deneyim

[Tümleşik güvenlik açığı tarayıcısını karma makinelerinize dağıtma hakkında daha fazla bilgi edinin](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Azure Arc etkin sunucuları hakkında daha fazla bilgi edinin](../azure-arc/servers/index.yml).


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>Yönetim grupları için güvenli puan şimdi önizlemede kullanılabilir

Güvenli puan sayfası artık abonelik düzeyine ek olarak yönetim gruplarınız için toplanmış güvenli puanları gösterir. Böylece artık kuruluşunuzdaki yönetim gruplarının listesini ve her bir yönetim grubunun Puanını görebilirsiniz.

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Yönetim gruplarınız için güvenli puanları görüntüleme.":::

[Azure Güvenlik Merkezi 'nde güvenli puan ve güvenlik denetimleri](secure-score-security-controls.md)hakkında daha fazla bilgi edinin.

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>Güvenli puan API 'SI genel kullanıma sunuldu (GA)

Artık puanınızı [güvenli Puanlama API 'si](/rest/api/securitycenter/securescores/)aracılığıyla erişebilirsiniz. API yöntemleri, verileri sorgulama ve zaman içinde güvenli Puanlarınızın kendi raporlama mekanizmanızı oluşturma esnekliğini sağlar. Örnek:

- belirli bir aboneliğin Puanını almak için **güvenli puanlar** API 'sini kullanma
- güvenlik denetimlerini ve aboneliklerinizin geçerli Puanını listelemek için **güvenli puan denetimleri** API 'sini kullanın

[GitHub Community 'nin güvenli Puanlama alanındaki](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)güvenli Puanlama API 'si ile mümkün kılınan dış araçlar hakkında bilgi edinin.

[Azure Güvenlik Merkezi 'nde güvenli puan ve güvenlik denetimleri](secure-score-security-controls.md)hakkında daha fazla bilgi edinin.


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>App Service için Azure Defender 'a Dangling DNS korumaları eklendi

Alt etki alanı kuruluşları, kuruluşlar için ortak ve yüksek öneme sahip bir tehdittir. Önceden sağlanmış bir Web sitesine işaret eden bir DNS kaydınız varsa, alt etki alanı ele alma işlemi gerçekleşebilir. Bu tür DNS kayıtları, "dangze DNS" girişleri olarak da bilinir. CNAME kayıtları özellikle bu tehdide karşı savunmasız. 

Alt etki alanı cılar, tehdit aktörlerini bir kuruluşun etki alanı için tasarlanan trafiği kötü amaçlı etkinlik gerçekleştiren bir siteye yeniden yönlendirmek üzere etkinleştirir.

App Service için Azure Defender, artık bir App Service Web sitesi kullanımdan kalkdığı zaman DNS girdilerini algılar. Bu, DNS girişinin var olmayan bir kaynağa işaret ettiği veya Web sitenizin ele alındığı bir alt etki alanıyla güvenlik açığının olduğu bir çalışmadır. Bu korumalar, etki alanlarınızın Azure DNS veya dış etki alanı kaydedicisi ile yönetilip yönetilmediği ve hem Windows hem de Linux üzerinde App Service App Service için geçerlidir.

Daha fazla bilgi edinin:

- [Uyarı başvuru tablosu App Service](alerts-reference.md#alerts-azureappserv) -BIR tehlike DNS girişi algılandığında tetiklenecek Iki yeni Azure Defender uyarısı içerir
- [DNS girişlerinin tehlikelere geçmeyi önleyin ve alt etki alanı devrini kullanmaktan kaçının](../security/fundamentals/subdomain-takeover.md)
- [App Service için Azure Defender 'a giriş](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>Çoklu bulut bağlayıcıları genel kullanıma sunuldu (GA)

Bulut güvenlik hizmetleri genellikle birden çok bulut platformunu kapsayan bulut iş yükleri ile aynı olmalıdır.

Azure Güvenlik Merkezi, Azure, Amazon Web Services (AWS) ve Google Cloud Platform (GCP) iş yüklerini korur.

AWS veya GCP hesaplarınızı bağlamak, AWS güvenlik merkezi ve GCP güvenlik komut merkezi gibi yerel güvenlik araçlarını Azure Güvenlik Merkezi 'ne tümleştirir.

Bu özellik, güvenlik merkezi 'nin tüm büyük bulut ortamlarında görünürlük ve koruma sağladığı anlamına gelir. Bu tümleştirmenin avantajlarından bazıları şunlardır:

- Otomatik aracı sağlama-Güvenlik Merkezi, Log Analytics aracısını AWS örneklerinizi dağıtmak için Azure yay kullanır
- İlke yönetimi
- Güvenlik açığı yönetimi
- Gömülü uç nokta algılama ve yanıtı (EDR)
- Güvenlik yapılandırması hataları algılanamadı
- Tüm bulut sağlayıcılarından gelen güvenlik önerilerini gösteren tek bir görünüm
- Tüm kaynaklarınızı Güvenlik Merkezi 'nin güvenli puan hesaplamalarına dahil edin
- AWS ve GCP kaynaklarınızın yasal uyumluluk değerlendirmeleri

Güvenlik Merkezi 'nin menüsünden **çoklu bulut bağlayıcıları** ' nı seçin ve yeni bağlayıcılar oluşturma seçeneklerini görürsünüz:

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Güvenlik Merkezi 'nin çoklu bulut bağlayıcıları sayfasına AWS hesabı Ekle düğmesi":::

Daha fazlasını öğrenin:
- [AWS hesaplarınızı Azure Güvenlik Merkezi 'ne bağlama](quickstart-onboard-aws.md)
- [GCP hesaplarınızı Azure Güvenlik Merkezi 'ne bağlama](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>Abonelikler ve yönetim grupları için tüm önerileri güvenli puanınızdan muaf tutma

Tüm önerilerin dahil olduğu istisna özelliğini genişletiyoruz. Güvenlik Merkezi 'nin abonelikleriniz, yönetim grubunuz veya kaynaklarınız için yaptığı güvenlik önerilerini hassas bir şekilde ayarlamaya yönelik daha fazla seçenek sağlar.

Bazen, sorunu Güvenlik Merkezi 'nin algıladığı üçüncü taraf bir araçla çözeceğinizde, bir kaynak sağlıksız olarak listelenecektir. Ya da bir öneri, ait olmadığı yerde bir kapsamda gösterilecektir. Öneri, belirli bir abonelik için uygun olmayabilir. Ya da kuruluşunuz, belirli kaynakla veya öneriyle ilgili riskleri kabul etmeye karar verdi.

Bu önizleme özelliğiyle artık öneri için bir istisna oluşturabilirsiniz:

- İleride sağlıksız kaynaklarla listelenmediğinden emin olmak için **bir kaynağı muaf tut** ve güvenli puanınızı etkilemez. Kaynak geçerli değil olarak listelenecektir ve bu nedenle seçtiğiniz belirli bir gerekçe ile "muaf tutulan" olarak gösterilir.

- Önerinin güvenli puanınızı etkilememesini sağlamak için **bir aboneliği veya yönetim grubunu muaf tut** ve gelecekte abonelik veya yönetim grubu için gösterilmeyecektir. Bu, mevcut kaynaklarla ve gelecekte oluşturduğunuz herhangi bir kaynak ile ilgilidir. Öneri, seçtiğiniz kapsam için seçtiğiniz belirli bir gerekçe ile işaretlenir.

[Güvenli puanınızdan kaynakları ve önerileri muaf tutmak](exempt-resource.md)için daha fazla bilgi edinin.



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>Kullanıcılar artık genel yöneticlerinden kiracı genelinde görünürlük isteğinde bulunabilir

Bir kullanıcının güvenlik merkezi verilerini görme izni yoksa, kuruluşunuzun genel Yöneticisi 'nden izin istemek için bir bağlantı görür. İstek, beğendikleri rolü ve neden gerekli olduğu konusunda gerekçe içerir.

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Kullanıcıya kiracı genelinde izinler isteyebilecekleri bir başlık bildiren başlık.":::

[Sizinki yetersiz olduğunda kiracı genelinde Izin iste](tenant-wide-permissions-management.md#request-tenant-wide-permissions-when-yours-are-insufficient)hakkında daha fazla bilgi edinin.


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Azure Güvenlik kıyaslamasının kapsamını artırmak için 35 önizleme önerisi eklendi

[Azure Güvenlik kıyaslaması](https://docs.microsoft.com/security/benchmark/azure/introduction) , Azure Güvenlik Merkezi 'ndeki varsayılan ilke girişimidir. 

Bu kıyaslama kapsamını artırmak için, güvenlik merkezi 'ne aşağıdaki 35 önizleme önerileri eklenmiştir.

> [!TIP]
> Önizleme önerileri bir kaynağı sağlıksız bir şekilde işlemez ve güvenli puanınızın hesaplamalarına dahil değildir. Önizleme dönemi sona erdiğinde puanınızın altına katkıda bulunmak için bunları mümkün olduğunda düzeltin. [Azure Güvenlik Merkezi 'ndeki önerileri düzeltin](security-center-remediate-recommendations.md)bölümünde bu önerilere yanıt verme hakkında daha fazla bilgi edinin.

| Güvenlik denetimi                     | Yeni öneriler                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Bekleyen şifrelemeyi etkinleştir            | -Azure Cosmos DB hesapları, bekleyen verileri şifrelemek için müşteri tarafından yönetilen anahtarları kullanmalıdır<br>-Azure Machine Learning çalışma alanları, müşteri tarafından yönetilen bir anahtarla şifrelenmelidir (CMK)<br>-MySQL sunucuları için kendi anahtar veri korumanın etkinleştirilmesi gerekir<br>-PostgreSQL sunucuları için kendi anahtar veri korumanızı getir özelliği etkinleştirilmelidir<br>Bilişsel hizmetler hesapları, müşteri tarafından yönetilen bir anahtarla (CMK) veri şifrelemeyi etkinleştirmelidir<br>-Kapsayıcı kayıt defterleri, müşteri tarafından yönetilen bir anahtarla şifrelenmelidir (CMK)<br>-SQL yönetilen örnekler, bekleyen verileri şifrelemek için müşteri tarafından yönetilen anahtarları kullanmalıdır<br>-SQL sunucuları, bekleyen verileri şifrelemek için müşteri tarafından yönetilen anahtarları kullanmalıdır<br>-Depolama hesapları, şifreleme için müşteri tarafından yönetilen anahtar (CMK) kullanmalıdır                                                                                                                                                              |
| En iyi güvenlik uygulamalarını uygulayın    | -Abonelikler güvenlik sorunları için bir iletişim e-posta adresine sahip olmalıdır<br> -Log Analytics aracısının otomatik olarak sağlanması aboneliğinizde etkinleştirilmelidir<br> -Yüksek önem derecesine sahip uyarılar için e-posta bildirimi etkinleştirilmelidir<br> -Yüksek önem derecesi uyarıları için abonelik sahibine e-posta bildirimi etkinleştirilmelidir<br> -Anahtar kasaları Temizleme koruması etkinleştirilmelidir<br> -Anahtar kasaları geçici silme etkin olmalıdır |
| Erişimi ve izinleri yönetme        | -Function uygulamalarında ' Istemci sertifikaları (gelen istemci sertifikaları) ' etkin olmalıdır |
| DDoS saldırılarına karşı uygulamaları koruma | -Web uygulaması güvenlik duvarı (WAF) Application Gateway için etkinleştirilmelidir<br> -Azure ön kapı hizmeti hizmeti için Web uygulaması güvenlik duvarı (WAF) etkinleştirilmelidir |
| Yetkisiz ağ erişimini kısıtla | -Güvenlik duvarının Key Vault üzerinde etkinleştirilmesi gerekir<br> -Özel uç nokta Key Vault için yapılandırılmalıdır<br> -Uygulama yapılandırması özel bağlantı kullanmalıdır<br> -Redsıs için Azure önbelleği bir sanal ağ içinde yer almalıdır<br> -Azure Event Grid etki alanları özel bağlantı kullanmalıdır<br> -Azure Event Grid konular özel bağlantı kullanmalıdır<br> -Azure Machine Learning çalışma alanları özel bağlantı kullanmalıdır<br> -Azure SignalR hizmeti özel bağlantı kullanmalıdır<br> -Azure yay bulutu, ağ ekleme kullanmalıdır<br> -Kapsayıcı kayıt defterleri Kısıtlanmamış ağ erişimine izin vermiyor<br> -Kapsayıcı kayıt defterleri özel bağlantı kullanmalıdır<br> -MariaDB sunucuları için genel ağ erişimi devre dışı bırakılmalıdır<br> -MySQL sunucuları için genel ağ erişimi devre dışı bırakılmalıdır<br> -PostgreSQL sunucuları için genel ağ erişimi devre dışı bırakılmalıdır<br> -Depolama hesabı bir özel bağlantı bağlantısı kullanmalıdır<br> -Depolama hesapları, sanal ağ kurallarını kullanarak ağ erişimini kısıtlamalı<br> -VM görüntü Oluşturucu şablonlarının özel bağlantı kullanması gerekir|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

İlgili bağlantılar:

- [Azure Güvenlik kıyaslaması hakkında daha fazla bilgi](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [MariaDB için Azure veritabanı hakkında daha fazla bilgi edinin](../mariadb/overview.md)
- [MySQL için Azure veritabanı hakkında daha fazla bilgi edinin](../mysql/overview.md)
- [PostgreSQL için Azure veritabanı hakkında daha fazla bilgi edinin](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>Filtrelenmiş öneriler listesinin CSV dışa aktarılması 

2020 Kasım 'da öneriler sayfasına filtreler ekledik ([öneriler listesi şimdi filtreler içeriyor](#recommendations-list-now-includes-filters)). Aralık ayında, bu filtreleri genişlettik ([Öneriler sayfasında ortam, önem derecesi ve kullanılabilir yanıtlar için yeni filtreler bulunur](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)). 

Bu duyuruda CSV dışarı aktarmanın yalnızca filtrelenmiş listede görüntülenen önerileri içermesi **IÇIN CSV 'ye indir** düğmesinin davranışını değiştiriyorsunuz. 

Örneğin, aşağıdaki görüntüde, listenin iki önerilere göre filtrelendiğine bakabilirsiniz. Oluşturulan CSV dosyası, bu iki önerinin etkilediği her kaynak için durum ayrıntılarını içerir.   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="Filtrelenmiş önerileri bir CSV dosyasına dışarı aktarma":::

[Azure Güvenlik Merkezi 'Nde güvenlik önerileri](security-center-recommendations.md)hakkında daha fazla bilgi edinin.


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>"Uygulanamaz" kaynakları artık Azure Ilke değerlendirmelerinde "uyumlu" olarak bildirildi

Daha önce, bir öneri için değerlendirilen ve **geçerli olmayan** kaynaklar Azure ilkesinde "uyumlu değil" olarak göründü. Kullanıcı eylemi, durumunu "uyumlu" olarak değiştiremez. Bu değişiklik ile, gelişmiş açıklık için "uyumlu" olarak bildirilir.

Azure Ilkesinde, uyumlu kaynakların sayısının arttıracağı tek etkisi görünür. Azure Güvenlik Merkezi 'nde güvenli puanınızın hiçbir etkisi olmayacaktır.


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>Sürekli dışa aktarma ile güvenli Puanlama ve mevzuat uyumluluk verilerinin haftalık anlık görüntülerini dışarı aktarma (Önizleme)

Güvenli Puanlama ve mevzuat uyumluluk verilerinin haftalık anlık görüntülerini dışarı aktarmak için [sürekli dışa aktarma](continuous-export.md) araçlarına yeni bir önizleme özelliği ekledik.

Sürekli dışarı aktarma tanımladığınızda dışa aktarma sıklığını ayarlayın:

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="Sürekli dışarı aktarmanın sıklığını seçme":::

- **Akış** – bir kaynağın sistem durumu güncelleştirildiğinde değerlendirmeler gerçek zamanlı olarak gönderilir (hiçbir güncelleştirme gerçekleşmezse, hiçbir veri gönderilmez).
- **Anlık görüntüler** : tüm yasal uyumluluk değerlendirmelerinin geçerli durumunun bir anlık görüntüsü her hafta gönderilir (Bu, güvenli puanlar ve mevzuat uyumluluk verilerinin haftalık anlık görüntüleri için bir önizleme özelliğidir).

[Güvenlik Merkezi verilerini sürekli dışa aktarma](continuous-export.md)bölümünde bu özelliğin tam özellikleri hakkında daha fazla bilgi edinin.

## <a name="december-2020"></a>Aralık 2020

Aralık ayında güncelleştirmeler şunlardır:

- [Makinelerde SQL Server 'lar için Azure Defender genel kullanıma sunuldu](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Azure SYNAPSE Analytics adanmış SQL havuzu için SQL için Azure Defender desteği genel kullanıma sunuldu](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [Genel Yöneticiler, artık kendi kiracı düzeyinde izinler verebilir](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [İki yeni Azure Defender planı: DNS için Azure Defender ve Kaynak Yöneticisi için Azure Defender (önizlemede)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Azure portal yeni güvenlik uyarıları sayfası (Önizleme)](#new-security-alerts-page-in-the-azure-portal-preview)
- [Azure SQL veritabanı & SQL yönetilen örneği 'nde yeniden kullanıma alınmış güvenlik merkezi deneyimi](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [Varlık envanter araçları ve filtreleri güncelleştirildi](#asset-inventory-tools-and-filters-updated)
- [SSL sertifikaları isteyen web uygulamaları hakkında öneri artık güvenli puanın bir parçası değil](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [Öneriler sayfasında ortam, önem derecesi ve kullanılabilir yanıtlar için yeni filtreler bulunur](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [Sürekli dışa aktarma yeni veri türlerini ve geliştirilmiş deployıfnotexist ilkelerini alır](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>Makinelerde SQL Server 'lar için Azure Defender genel kullanıma sunuldu

Azure Güvenlik Merkezi, SQL sunucuları için iki Azure Defender planı sunar:

- Azure **SQL veritabanı sunucuları Için Azure Defender** -Azure Native SQL Server 'larınızı savunma 
- **MAKINELERDE SQL Server 'lar Için Azure Defender** -aynı korumaların karma, çoklu bulut ve şirket içi ortamlarınızdaki SQL Server 'larınızla aynı korumaları genişlettiğini genişletiyor

Bu duyuru sayesinde, **SQL Için Azure Defender** artık veritabanlarınızı ve bu verilerin bulundukları yerden korunmasını sağlar.

SQL için Azure Defender, güvenlik açığı değerlendirme özelliklerini içerir. Güvenlik açığı değerlendirme aracı aşağıdaki gelişmiş özellikleri içerir:

- Güvenlik açığı sonuçlarını, gerçek güvenlik sorunlarını temsil eden bir şekilde iyileştirmek için **temel yapılandırma** (yeni!). Temel güvenlik durumlarınızı kurduktan sonra, güvenlik açığı değerlendirmesi aracı yalnızca bu temel durumdan sapmaları raporlar. Taban çizgisiyle eşleşen sonuçlar, sonraki taramaların geçirilmesi olarak değerlendirilir. Bu, sizin ve analistlerinizin önemli olduğunda ilgilenmeniz için odaklanmasına olanak tanır.
- Keşfedilen bulguları ve kaynaklarınızla ilişkilerini *anlamanıza* yardımcı olacak **ayrıntılı kıyaslama bilgileri** .
- Belirlenen riskleri azaltmanıza yardımcı olacak **Düzeltme betikleri** .

[SQL Için Azure Defender](defender-for-sql-introduction.md)hakkında daha fazla bilgi edinin.


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>Azure SYNAPSE Analytics adanmış SQL havuzu için SQL için Azure Defender desteği genel kullanıma sunuldu

Azure SYNAPSE Analytics (eski adıyla SQL DW), kurumsal veri depolama ve büyük veri analizlerini birleştiren bir analiz hizmetidir. Adanmış SQL havuzları, Azure SYNAPSE 'ın kurumsal veri ambarı özellikleridir. [Azure SYNAPSE Analytics (eski ADıYLA SQL DW)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)hakkında daha fazla bilgi edinin.

SQL için Azure Defender, adanmış SQL havuzlarınızı ile korur:

- Tehditleri ve saldırıları algılamak için **Gelişmiş tehdit koruması** 
- Güvenlik yapılandırmalarını belirlemek ve düzeltmek için Güvenlik **açığı değerlendirme özellikleri**

SQL için Azure Defender 'ın Azure SYNAPSE Analytics SQL havuzları desteği, Azure Güvenlik Merkezi 'nde Azure SQL veritabanlarına otomatik olarak eklenir. Azure portal SYNAPSE çalışma alanı sayfanızda yeni bir "SQL için Azure Defender" sekmesi bulacaksınız.

[SQL Için Azure Defender](defender-for-sql-introduction.md)hakkında daha fazla bilgi edinin.


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>Genel Yöneticiler, artık kendi kiracı düzeyinde izinler verebilir

**Genel yönetici** Azure Active Directory rolüne sahip bir kullanıcının kiracı genelinde sorumlulukları olabilir, ancak Azure Güvenlik Merkezi 'nde kuruluş genelinde bilgileri görüntülemek için Azure izinlerinin olmaması gerekir. 

Kendi kiracı düzeyi izinlerini atamak için, [kiracı genelindeki izinleri kendinize verme](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself)bölümündeki yönergeleri izleyin.


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>İki yeni Azure Defender planı: DNS için Azure Defender ve Kaynak Yöneticisi için Azure Defender (önizlemede)

Azure ortamınız için iki yeni bulut Yerel enine koruma özelliği ekledik.

Bu yeni korumalar tehdit aktörlerini saldırılara karşı dayanıklılığını büyük ölçüde geliştirir ve Azure Defender tarafından korunan Azure kaynakları sayısını önemli ölçüde artırır.

- **Kaynak Yöneticisi Için Azure Defender** -kuruluşunuzda gerçekleştirilen tüm kaynak yönetimi işlemlerini otomatik olarak izler. Daha fazla bilgi için bkz.
    - [Kaynak Yöneticisi için Azure Defender 'a giriş](defender-for-resource-manager-introduction.md)
    - [Resource Manager için Azure Defender uyarılarına yanıt verme](defender-for-resource-manager-usage.md)
    - [Kaynak Yöneticisi için Azure Defender tarafından sunulan uyarıların listesi](alerts-reference.md#alerts-resourcemanager)

- **DNS Için Azure Defender** -Azure kaynaklarınızdaki tüm DNS sorgularını sürekli izler. Daha fazla bilgi için bkz.
    - [DNS için Azure Defender 'a giriş](defender-for-dns-introduction.md)
    - [DNS için Azure Defender uyarılarına yanıt verme](defender-for-dns-usage.md)
    - [DNS için Azure Defender tarafından sunulan uyarıların listesi](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Azure portal yeni güvenlik uyarıları sayfası (Önizleme)

Azure Güvenlik Merkezi 'nin güvenlik uyarıları sayfası, şunları sağlayacak şekilde yeniden tasarlandı:

- **Uyarılar Için geliştirilmiş önceliklendirme deneyimi** -uyarıları daha kolay hale getirmeye ve en ilgili tehditlere odaklanmaya yardımcı olma, listede özelleştirilebilir filtreler ve gruplandırma seçenekleri de bulunur
- **Uyarılar listesinde,** MITRE ATT&ACK Tactika gibi daha fazla bilgi
- **Örnek uyarılar oluşturma düğmesi** -Azure Defender özelliklerini değerlendirmek ve uyarı yapılandırmanızı sınamak IÇIN (SIEM tümleştirmesi, e-posta bildirimleri ve iş akışı tahminleri için), tüm Azure Defender planlarından örnek uyarılar oluşturabilirsiniz
- **Azure Sentinel 'in olay deneyimiyle hizalaması** -her iki ürünü de kullanan müşteriler için, aralarında geçiş yapmak artık daha basit bir deneyimdir ve bunlardan birini öğrenmenin kolay bir deneyimi vardır.
- Büyük uyarı listeleri için **daha iyi performans**
- Uyarı listesi aracılığıyla **klavye gezintisi**
- **Azure Kaynak grafının uyarıları** -tüm kaynaklarınızın kusto benzeri API 'Si olan Azure Kaynak Grafında uyarıları sorgulayabilirsiniz. Bu, kendi uyarı panolarınızı oluşturuyorsanız de kullanışlıdır. [Azure Kaynak Grafiği hakkında daha fazla bilgi edinin](../governance/resource-graph/index.yml).

Yeni deneyimle erişmek için, güvenlik uyarıları sayfasının en üstündeki başlığından ' Şimdi deneyin ' bağlantısını kullanın.

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="Yeni önizleme uyarıları deneyimine bağlantı içeren başlık":::

Yeni uyarılar deneyiminden örnek uyarılar oluşturmak için bkz. [örnek Azure Defender uyarıları](security-center-alert-validation.md#generate-sample-azure-defender-alerts)oluşturma.


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>Azure SQL veritabanı & SQL yönetilen örneği 'nde yeniden kullanıma alınmış güvenlik merkezi deneyimi 

SQL 'deki güvenlik merkezi deneyimi, aşağıdaki güvenlik merkezi ve SQL için Azure Defender özelliklerine erişim sağlar:

- **Güvenlik önerileri** : Güvenlik Merkezi, olası güvenlik yapılandırmalarını belirlemek üzere tüm bağlı Azure kaynaklarının güvenlik durumunu düzenli aralıklarla analiz eder. Daha sonra bu güvenlik açıklarını düzeltme ve kuruluşların güvenlik duruşunu geliştirme hakkında öneriler sağlar.
- **Güvenlik uyarıları** : SQL ekleme, deneme yanılma saldırıları ve ayrıcalık kötüye kullanımı gibi tehditler IÇIN Azure SQL etkinliklerini sürekli olarak izleyen bir algılama hizmeti. Bu hizmet, güvenlik merkezi 'nde ayrıntılı ve eyleme dayalı güvenlik uyarılarını tetikler ve Microsoft 'un Azure-Native SıEM çözümü olan Azure Sentinel ile araştırmalar devam ettirme seçeneklerini sunar.
- **Bulmalar** : Azure SQL yapılandırmasını sürekli olarak izleyen ve güvenlik açıklarını düzeltmeye yardımcı olan bir güvenlik açığı değerlendirme hizmeti. Değerlendirme taramaları, ayrıntılı güvenlik bulguları ile birlikte Azure SQL güvenlik durumlarına genel bir bakış sağlar.     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="Azure Güvenlik Merkezi 'nin SQL için güvenlik özellikleri Azure SQL içinden kullanılabilir":::


### <a name="asset-inventory-tools-and-filters-updated"></a>Varlık envanter araçları ve filtreleri güncelleştirildi

Azure Güvenlik Merkezi 'ndeki envanter sayfası aşağıdaki değişikliklerle yenilendi:

- Araç çubuğuna **Kılavuzlar ve geri bildirimler** eklendi. Bu, ilgili bilgi ve araçların bağlantılarını içeren bir bölme açar. 
- Kaynaklarınız için kullanılabilen varsayılan filtrelere **Abonelik filtresi** eklendi.
- Geçerli filtre seçeneklerini bir Azure Kaynak Grafiği sorgusu olarak **açmak için sorgu bağlantısını açın** (eski adıyla "kaynak grafik Gezgininde görünüm" olarak adlandırılır).
- Her filtrenin **işleç seçenekleri** . Artık ' = ' dışında daha fazla mantıksal işleç arasından seçim yapabilirsiniz. Örneğin, başlıkları ' Encrypt ' dizesini içeren etkin önerilere sahip tüm kaynakları bulmak isteyebilirsiniz. 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="Varlık envanterinin filtrelerinde operatör seçeneğinin denetimleri":::

Envanter hakkında daha fazla bilgi edinmek için [varlık envanteriyle kaynaklarınızı bulun ve yönetin](asset-inventory.md).


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>SSL sertifikaları isteyen web uygulamaları hakkında öneri artık güvenli puanın bir parçası değil

"Web Apps tüm gelen istekler için bir SSL sertifikası istemelidir" önerisi güvenlik denetiminden, en **iyi güvenlik uygulamalarını** (yani, hiçbir noktaya değer olmaksızın) uygulama **erişimi ve izinleri yönetme** (en fazla 4 Pts değeri). 

Bir Web uygulamasının bir sertifikayı talep etmelerini sağlamak, bunu kesinlikle daha güvenli hale getirir. Ancak, herkese açık Web uygulamaları için önemli değildir. Sitenize HTTPS değil, HTTP üzerinden eriştiğinizde hiçbir istemci sertifikası almazsınız. Bu nedenle, uygulamanız için istemci sertifikaları gerekiyorsa, HTTP üzerinden uygulamanıza yönelik isteklere izin vermeniz gerekir. [Azure App Service IÇIN TLS karşılıklı kimlik doğrulamasını yapılandırma](../app-service/app-service-web-configure-tls-mutual-auth.md)bölümünde daha fazla bilgi edinin.

Bu değişiklik ile, öneri artık puanınızı etkilemeyecek önerilen en iyi uygulamadır. 

[Güvenlik denetimlerinde ve bunların önerilerinde](secure-score-security-controls.md#security-controls-and-their-recommendations)her güvenlik denetiminde hangi önerilerin olduğunu öğrenin.


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>Öneriler sayfasında ortam, önem derecesi ve kullanılabilir yanıtlar için yeni filtreler bulunur

Azure Güvenlik Merkezi, tüm bağlı kaynakları izler ve güvenlik önerileri oluşturur. Karma bulut durunuzu güçlendirin ve kuruluşunuz, sektör ve ülkeniz ile ilgili ilkeler ve standartlarla uyumluluğu izleyin.

Güvenlik Merkezi, kapsamını ve özelliklerini genişletmeye devam ettiğinden, güvenlik önerilerinin listesi her ay büyüyordur. Örneğin, [Azure Güvenlik kıyaslamasının kapsamını artırmak için bkz. 29 Preview önerileri eklendi](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark).

Büyüyen liste ile, en fazla ilgilendiğiniz olanları bulmak için önerileri filtrelemeniz gerekir. Kasım ayında öneriler sayfasına filtreler ekledik (bkz. [öneriler listesi şimdi filtreler içeriyor](#recommendations-list-now-includes-filters)).

Bu ay eklenen filtreler şunlara göre öneriler listesini iyileştirmek için seçenekler sağlar:

- **Ortam** -AWS, GCP veya Azure kaynaklarınız (veya herhangi bir bileşim) için öneriler görüntüleyin
- **Önem derecesi** -Güvenlik Merkezi tarafından ayarlanan önem derecesine göre önerileri görüntüleme
- **Yanıt eylemleri** -Güvenlik Merkezi yanıt seçeneklerinin kullanılabilirliğine göre önerileri görüntüleyin: hızlı düzeltme, reddetme ve zorlama

    > [!TIP]
    > Yanıt eylemleri filtresi, **kullanılabilir hızlı düzeltilmesi (Evet/Hayır)** filtresinin yerini alır. 
    > 
    > Bu yanıt seçeneklerinin her biri hakkında daha fazla bilgi edinin:
    > - [Hızlı düzeltme düzeltme](security-center-remediate-recommendations.md#quick-fix-remediation)
    > - [Zorlama/Reddetme önerileriyle yanlış yapılandırmaları önleme](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="Güvenlik denetimine göre gruplanmış öneriler" lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>Sürekli dışa aktarma yeni veri türlerini ve geliştirilmiş deployıfnotexist ilkelerini alır

Azure Güvenlik Merkezi 'nin sürekli dışa aktarma araçları, ortamınızdaki diğer izleme araçlarıyla kullanılmak üzere güvenlik merkezi 'nin önerilerini ve uyarılarını dışa aktarmanız sağlar.

Sürekli dışarı aktarma, nelerin dışa aktarılacağını ve nerede gidebileceklerini tamamen özelleştirmenizi sağlar. Tüm ayrıntılar için bkz.  [Güvenlik Merkezi verilerini sürekli dışa aktarma](continuous-export.md).

Bu araçlar geliştirilmiştir ve aşağıdaki yollarla genişletilmiştir:

- **Sürekli dışa aktarma 'nın deployıfnotexist ilkeleri geliştirildi**. İlkeler şimdi:

    - **Yapılandırmanın etkinleştirilip etkinleştirilmediğini denetleyin.** Değilse, ilke uyumlu değil olarak görünür ve uyumlu bir kaynak oluşturur. Azure Ilke şablonları hakkında daha fazla bilgi edinmek için [sürekli dışarı aktarma ayarlama](continuous-export.md#set-up-a-continuous-export)bölümünde "Azure ilke sekmesi ile ölçeklendirin ölçeğinde dağıtım".

    - **Güvenlik bulgularını dışarı aktarma desteği.** Azure Ilke şablonlarını kullanırken, sürekli dışarı aktarmayı bulguları içerecek şekilde yapılandırabilirsiniz. Bu, güvenlik açığı değerlendirme tarayıcılarından bulguları veya ' Ana ' öneri "sistem güncelleştirmeleri" makinelerinizde yüklü olmalıdır "gibi ' Sub ' önerilerine sahip olan önerileri dışarı aktarırken geçerlidir.
    
    - **Güvenli puanı verileri dışarı aktarma desteği.**

- **Mevzuat uyumluluk değerlendirmesi verileri eklendi (önizlemede).** Artık bir Log Analytics çalışma alanına veya Olay Hub 'ına tüm özel girişimler dahil olmak üzere yasal uyumluluk değerlendirmelerine yönelik güncelleştirmeleri sürekli dışa aktarabilirsiniz. Bu özellik Ulusal/bağımsız bulutlarda kullanılamaz.

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="Sürekli dışa aktarma verilerinize mevzuat uyumlu değerlendirme bilgilerini ekleme seçenekleri.":::


## <a name="november-2020"></a>Kasım 2020

Kasım 'daki güncelleştirmeler şunlardır:

- [Azure Güvenlik kıyaslamasının kapsamını artırmak için 29 önizleme önerileri eklenmiştir](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [NıST SP 800 171 R2, güvenlik merkezi 'nin mevzuat uyumluluk panosuna eklendi](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [Öneriler listesi şimdi filtreler içeriyor](#recommendations-list-now-includes-filters)
- [Otomatik sağlama deneyimi geliştirildi ve genişletildi](#auto-provisioning-experience-improved-and-expanded)
- [Güvenli puan, sürekli dışarı aktarma (Önizleme) ile kullanılabilir](#secure-score-is-now-available-in-continuous-export-preview)
- ["Makinelerinizde sistem güncelleştirmeleri yüklenmelidir" önerisi artık alt öneriler içeriyor](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations)
- [Azure portal ilke yönetimi sayfasında artık varsayılan ilke atamalarının durumu gösterilmektedir](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Azure Güvenlik kıyaslamasının kapsamını artırmak için 29 önizleme önerileri eklenmiştir

Azure Güvenlik kıyaslaması, yaygın uyumluluk çerçevelerine dayalı olarak güvenlik ve uyumluluk en iyi uygulamaları için Microsoft tarafından yazılan, Azure 'a özgü bir dizi kılavuzlardır. [Azure Güvenlik Karşılaştırması hakkında daha fazla bilgi edinin](https://docs.microsoft.com/security/benchmark/azure/introduction).

Bu kıyaslama kapsamını artırmak için Güvenlik Merkezi 'ne aşağıdaki 29 önizleme önerileri eklenmiştir.

Önizleme önerileri bir kaynağı sağlıksız bir şekilde işlemez ve güvenli puanınızın hesaplamalarına dahil değildir. Önizleme dönemi sona erdiğinde puanınızın altına katkıda bulunmak için bunları mümkün olduğunda düzeltin. [Azure Güvenlik Merkezi 'ndeki önerileri düzeltin](security-center-remediate-recommendations.md)bölümünde bu önerilere yanıt verme hakkında daha fazla bilgi edinin.

| Güvenlik denetimi                     | Yeni öneriler                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Yoldaki verileri şifreleme              | -PostgreSQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir<br>-MySQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir<br>-TLS API uygulamanız için en son sürüme güncelleştirilmeleri gerekir<br>-TLS, işlev uygulamanız için en son sürüme güncelleştirilmeleri gerekir<br>-TLS, Web uygulamanız için en son sürüme güncelleştirilmeleri gerekir<br>-FTPS API uygulamanızda gerekli olmalıdır<br>-FTPS, işlev uygulamanızda gerekli olmalıdır<br>-FTPS Web uygulamanızda gerekli olmalıdır                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Erişimi ve izinleri yönetme        | -Web uygulamaları tüm gelen istekler için bir SSL sertifikası istemelidir<br>-Yönetilen kimlik API uygulamanızda kullanılmalıdır<br>-Yönetilen kimlik, işlev uygulamanızda kullanılmalıdır<br>-Yönetilen kimliğin Web uygulamanızda kullanılması gerekir                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Yetkisiz ağ erişimini kısıtla | -PostgreSQL sunucuları için özel uç noktanın etkinleştirilmesi gerekir<br>-Özel uç noktanın MariaDB sunucuları için etkinleştirilmesi gerekir<br>-MySQL sunucuları için özel uç noktanın etkinleştirilmesi gerekir                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Denetim ve günlüğe kaydetmeyi etkinleştirme          | -Uygulama hizmetlerindeki tanılama günlükleri etkinleştirilmelidir                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| En iyi güvenlik uygulamalarını uygulayın    | -Azure Backup sanal makineler için etkinleştirilmelidir<br>-Coğrafi olarak yedekli yedekleme, MariaDB için Azure veritabanı 'nda etkinleştirilmelidir<br>-MySQL için Azure veritabanı için coğrafi olarak yedekli yedeklemenin etkinleştirilmesi gerekir<br>-PostgreSQL için Azure veritabanı için coğrafi olarak yedekli yedeklemenin etkinleştirilmesi gerekir<br>-PHP, API uygulamanız için en son sürüme güncelleştirilmeleri gerekir<br>-PHP, Web uygulamanız için en son sürüme güncelleştirilmeleri gerekir<br>-Java, API uygulamanız için en son sürüme güncelleştirilmeleri gerekir<br>-Java, işlev uygulamanız için en son sürüme güncelleştirilmeleri gerekir<br>-Java, Web uygulamanız için en son sürüme güncelleştirilmeleri gerekir<br>-Python, API uygulamanız için en son sürüme güncelleştirilmeleri gerekir<br>-Python, işlev uygulamanız için en son sürüme güncelleştirilmeleri gerekir<br>-Python, Web uygulamanız için en son sürüme güncelleştirilmeleri gerekir<br>-SQL Server 'lar için denetim bekletme en az 90 gün olarak ayarlanmalıdır |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

İlgili bağlantılar:

- [Azure Güvenlik kıyaslaması hakkında daha fazla bilgi](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [Azure API Apps hakkında daha fazla bilgi](../app-service/app-service-web-tutorial-rest-api.md)
- [Azure işlev uygulamaları hakkında daha fazla bilgi edinin](../azure-functions/functions-overview.md)
- [Azure Web Apps hakkında daha fazla bilgi](../app-service/overview.md)
- [MariaDB için Azure veritabanı hakkında daha fazla bilgi edinin](../mariadb/overview.md)
- [MySQL için Azure veritabanı hakkında daha fazla bilgi edinin](../mysql/overview.md)
- [PostgreSQL için Azure veritabanı hakkında daha fazla bilgi edinin](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>NıST SP 800 171 R2, güvenlik merkezi 'nin mevzuat uyumluluk panosuna eklendi

NıST SP 800-171 R2 Standard artık, Azure Güvenlik Merkezi 'nin mevzuat uyumluluk panosu ile birlikte kullanılmak üzere yerleşik bir girişim olarak sunulmaktadır. Denetimlerle ilgili eşlemeler, [NıST SP 800-171 R2 mevzuatı uyumluluğu yerleşik girişiminin ayrıntıları](../governance/policy/samples/nist-sp-800-171-r2.md)bölümünde açıklanmaktadır. 

Standart aboneliğinizi aboneliklerinize uygulamak ve uyumluluk durumunuzu sürekli olarak izlemek için, [mevzuat uyumluluk panonuzdaki standartlar kümesini özelleştirme](update-regulatory-compliance-packages.md)bölümündeki yönergeleri kullanın.

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="Güvenlik Merkezi 'nin mevzuat uyumluluk panosundaki NıST SP 800 171 R2 Standard":::

Bu uyumluluk standardı hakkında daha fazla bilgi için bkz. [NıST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).


### <a name="recommendations-list-now-includes-filters"></a>Öneriler listesi şimdi filtreler içeriyor

Artık güvenlik önerileri listesini bir dizi ölçüte göre filtreleyebilirsiniz. Aşağıdaki örnekte, öneriler listesi şu önerileri gösterecek şekilde filtrelenmiştir:

- **genel kullanıma sunulmuştur** (yani Önizleme)
- **depolama hesapları** içindir
- **hızlı düzeltme** düzeltmesini destekleme

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="Öneriler listesi için filtreler":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>Otomatik sağlama deneyimi geliştirildi ve genişletildi

Otomatik sağlama özelliği, güvenlik merkezi 'nin korumalarının avantajlarından faydalanabilmesi için gerekli uzantıları yeni ve mevcut Azure VM 'lerine yükleyerek yönetim yükünü azaltmaya yardımcı olur. 

Azure Güvenlik Merkezi arttıkça, daha fazla uzantı geliştirilmiştir ve Güvenlik Merkezi, kaynak türlerinin daha büyük bir listesini izleyebilir. Otomatik sağlama araçları artık Azure Ilkesinin yeteneklerini kullanarak diğer uzantıları ve kaynak türlerini destekleyecek şekilde genişletilmiştir.

Artık otomatik sağlamayı yapılandırabilirsiniz:

- Log Analytics aracısı
- Yeni Kubernetes için Azure Ilke eklentisi
- Yeni Microsoft bağımlılık Aracısı

[Azure Güvenlik Merkezi 'Nden otomatik sağlama aracıları ve uzantıları](security-center-enable-data-collection.md)hakkında daha fazla bilgi edinin.


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>Güvenli puan, sürekli dışarı aktarma (Önizleme) ile kullanılabilir

Güvenli puanı sürekli dışa aktarma ile Azure Event Hubs veya bir Log Analytics çalışma alanına gerçek zamanlı olarak yaptığınız değişiklikleri gerçek zamanlı olarak akışla aktarabilirsiniz. Bu özelliği şu şekilde kullanın:

- Dinamik raporlarla zaman içindeki güvenli puanınızı izleyin
- güvenli Puanlama verilerini Azure Sentinel 'e (veya başka bir SıEM) aktarma
- Bu verileri, kuruluşunuzda güvenli puanı izlemek için zaten kullanmakta olabileceğiniz işlemlerle tümleştirin

[Güvenlik Merkezi verilerinin sürekli olarak nasıl dışarı aktarılacağı](continuous-export.md)hakkında daha fazla bilgi edinin.


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations"></a>"Makinelerinizde sistem güncelleştirmeleri yüklenmelidir" önerisi artık alt öneriler içeriyor

**Sistem güncelleştirmelerinin, makinelerinizde yüklü olması** önerilir. Yeni sürüm, eksik olan her güncelleştirme için alt öneriler içerir ve aşağıdaki geliştirmeleri getirir:

- Azure portal Azure Güvenlik Merkezi sayfalarında yeniden tasarlanan bir deneyim. **Makinelerinizde sistem güncelleştirmeleri** için öneri ayrıntıları sayfası, aşağıda gösterildiği gibi bulguları listesini içerir. Tek bir bulma seçtiğinizde, Ayrıntılar bölmesi düzeltme bilgilerinin bağlantısını ve etkilenen kaynakların listesini açar.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Güncelleştirilmiş öneri için Portal deneyimindeki alt önerilerden birini açma":::

- Azure Kaynak grafiğinden (ARG) öneri için verileri zenginleştirir. Bağımsız değişken, verimli kaynak araştırması sağlamak için tasarlanan bir Azure hizmetidir. Ortamınızı etkin bir şekilde yönetebilmeniz için, belirli bir abonelik kümesi genelinde ölçeği sorgulamak için bağımsız değişkeni kullanabilirsiniz. 

    Azure Güvenlik Merkezi için bağımsız değişken ve [kusto sorgu dili (KQL)](/azure/data-explorer/kusto/query/) kullanarak çok çeşitli güvenlik sonrası verileri sorgulayabilirsiniz.

    Daha önce, bu öneriyi bağımsız değişken ' de sorguladıysanız, yalnızca bir makinenin bir makinede düzeltilme ihtiyacı vardır. Aşağıdaki gelişmiş sürümü sorgusu, tüm eksik sistem güncelleştirmelerini makineye göre gruplanmış olarak döndürür.

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>Azure portal ilke yönetimi sayfasında artık varsayılan ilke atamalarının durumu gösterilmektedir

Artık abonelikleriniz için, Azure portal Güvenlik Merkezi 'nin **güvenlik ilkesi** sayfasında, aboneliklerinizde varsayılan güvenlik merkezi ilkesi atanmış olup olmadığını görebilirsiniz.

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="Azure Güvenlik Merkezi 'nin varsayılan ilke atamalarını gösteren ilke yönetimi sayfası":::