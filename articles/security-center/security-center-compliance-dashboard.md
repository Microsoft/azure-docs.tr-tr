---
title: 'Öğretici: mevzuat uyumluluk denetimleri-Azure Güvenlik Merkezi'
description: "Öğretici: Azure Güvenlik Merkezi 'ni kullanarak mevzuata uyumluluğunuzu geliştirmeyi öğrenin."
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 04/21/2021
ms.author: memildin
ms.openlocfilehash: c8ac9079321e47a1e6d9b8689be46bf55bdd4243
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834629"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Öğretici: Mevzuat uyumluluğunuzu artırma

Azure Güvenlik Merkezi, mevzuat **Uyumluluk panosunu** kullanarak, mevzuat uyumluluk gereksinimlerini karşılamaya yönelik işlemin kolaylaştırılmasına yardımcı olur. 

Güvenlik Merkezi, aboneliklerinize uygulanan standartların denetimlerine ve en iyi uygulamalarına göre risk faktörlerini analiz etmek için karma bulut ortamınızı sürekli değerlendirir. Pano, bu standartlarla uyumluluğun durumunu yansıtır. 

Azure aboneliğinde Güvenlik Merkezi 'ni etkinleştirdiğinizde, [Azure Güvenlik kıyaslaması](https://docs.microsoft.com/security/benchmark/azure/introduction) otomatik olarak bu aboneliğe atanır. Bu, yaygın olarak kullanılan kıyaslama, [Internet güvenliği (CIS) Için merkezden](https://www.cisecurity.org/benchmark/azure/) ve [ulusal standartlar ve teknolojı Enstitüsü (NIST)](https://www.nist.gov/) ile bulut merkezli güvenliğe odaklanarak yapılar oluşturur.

Mevzuat uyumluluk panosu, ortamınızdaki tüm değerlendirmelerin durumunu seçtiğiniz standartlar ve yönetmeliklerinize göre gösterir. Önerilerle ilgili işlem yaparken ve ortamınızdaki risk etmenlerini azaltmanız durumunda uyumluluk durmanız artar.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Mevzuat uyumluluk panosunu kullanarak mevzuata uyumluluğunuzu değerlendirin
> * Önerilerle ilgili eylem gerçekleştirerek uyumluluk durunuzu geliştirme
> * Uyumluluk sonrasında yapılan değişiklikler üzerinde uyarı kurulumu
> * Uyumluluk verilerinizi sürekli bir akış olarak ve haftalık anlık görüntüler olarak dışa aktarın

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide ele alınan özellikler arasında ilerlemek için:

- [Azure Defender](azure-defender.md) etkin olmalıdır. Azure Defender 'ı 30 gün boyunca ücretsiz olarak deneyebilirsiniz.
- İlke uyumluluk verilerine okuyucu erişimi olan bir hesapla oturum açmış olmanız gerekir (**güvenlik okuyucusu** yeterli değildir). Abonelik için **Genel okuyucu** rolü çalışacaktır. En azından, **kaynak Ilkesi katılımcısı** ve **Güvenlik yönetici** rollerinin atanması gerekir.

##  <a name="assess-your-regulatory-compliance"></a>Mevzuata uyumluluğunuzu değerlendirin

Yasal uyumluluk panosu, seçilen uyumluluk standartlarınızı, desteklenen gereksinimlerin ilgili güvenlik değerlendirmelerine eşlendiği tüm gereksinimleriyle birlikte gösterir. Bu değerlendirmelerin durumu, standart ile uyumluluğunu yansıtır.

Seçtiğiniz standartlar ve yönetmeliklerle uyumlu olan boşlukların dikkatine odaklanmaya yardımcı olması için mevzuat uyumluluk panosunu kullanın. Bu odaklanmış görünüm, dinamik bulut ve karma ortamlarda zaman içinde uyumluluğunuzu sürekli olarak izlemenize de olanak sağlar.

1. Güvenlik Merkezi menüsünde, **mevzuat uyumluluğu**' nı seçin.

    Ekranın üst kısmında, desteklenen uyumluluk düzenlemeleri kümesiyle uyumluluk durumunuz hakkında genel bakış içeren bir panodur. Genel uyumluluk puanınızı ve her bir standart ile ilişkili geçen ve başarısız değerlendirmelerin sayısını görürsünüz.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Mevzuat uyumluluk panosu" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. Uyumluluk standardı için sizin için uygun bir sekme seçin (1). Standart (2) üzerinde hangi aboneliklerin uygulandığını ve bu standart (3) için tüm denetimlerin listesini görürsünüz. İlgili denetimler için, bu denetimle ilişkili geçen ve başarısız değerlendirmelerin ayrıntılarını (4) ve etkilenen kaynakların sayısını (5) görebilirsiniz. Bazı denetimler gri renkte. Bu denetimlere kendileriyle ilişkili güvenlik merkezi değerlendirmeleri yok. Gereksinimlerini denetleyin ve ortamınızda değerlendirin. Bunlardan bazıları teknik değil işlem ile ilgili olabilir.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Belirli bir standartta uyumluluk ayrıntılarını keşfetme":::

1. Belirli bir standart için geçerli uyumluluk durumunuzu Özetle bir PDF raporu oluşturmak için, **raporu indir**' i seçin.

    Rapor, güvenlik merkezi değerlendirme verilerine bağlı olarak seçili standart için uyumluluk durumunuzu üst düzey bir Özet sağlar. Rapor, söz konusu standart denetimlere göre düzenlenmiştir. Rapor ilgili hissedarlarla paylaşılabilir ve iç ve dış denetçiler için kanıt sağlayabilir.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Uyumluluk raporunu indir":::

## <a name="improve-your-compliance-posture"></a>Uyumluluk durunuzu geliştirme

Yasal uyumluluk panosundaki bilgileri kullanarak, önerileri doğrudan pano içinde çözerek uyumluluk durunuzu geliştirebilirsiniz.

1.  Bu öneriye ilişkin ayrıntıları görüntülemek için Panoda görünen başarısız olan değerlendirmelerin birini seçin. Her öneri, sorunu çözmek için bir düzeltme adımları kümesi içerir.

1.  Daha fazla ayrıntı görüntülemek ve söz konusu kaynak için öneriyi çözümlemek üzere belirli bir kaynağı seçin. <br>Örneğin, **Azure CIS 1.1.0** Standard 'da, **sanal makinelere uygulanacak öneri disk şifrelemesi**' ni seçin.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="Standart Müşteri adaylarından öneri seçme, doğrudan öneri ayrıntıları sayfasına":::

1. Bu örnekte, öneri ayrıntıları sayfasında **eylemi al** ' ı seçtiğinizde, Azure Portal Azure sanal makine sayfalarına geldiğinizde **güvenlik** sekmesinden şifrelemeyi etkinleştirebilirsiniz:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="Öneri ayrıntıları sayfasında eylem Al düğmesi düzeltme seçeneklerine yol açar":::

    Önerilerin nasıl uygulanacağı hakkında daha fazla bilgi için bkz. [Azure Güvenlik Merkezi 'nde güvenlik önerilerini uygulama](security-center-recommendations.md).

1.  Önerileri çözümlemek için işlem yaptıktan sonra, uyumluluk puanınız artdığından uyumluluk panosu raporunda sonucu görürsünüz.

    > [!NOTE]
    > Değerlendirmeler yaklaşık 12 saatte bir çalışır, bu nedenle yalnızca ilgili değerlendirmenin bir sonraki çalıştırıldıktan sonra uyumluluk verilerinize etkisini görürsünüz.


## <a name="export-your-compliance-status-data"></a>Uyumluluk durumu verilerinizi dışarı aktarma

Ortamınızdaki diğer izleme araçlarıyla uyumluluk durumunuzu izlemek isterseniz, güvenlik merkezi bunu basit hale getirmek için bir dışarı aktarma mekanizması içerir. Azure Olay Hub 'ına veya bir Log Analytics çalışma alanına seçim verileri göndermek için **sürekli dışarı aktarmayı** yapılandırın.

Azure Olay Hub 'ına veya Log Analytics çalışma alanına sürekli veri aktarma kullanın:

- **Sürekli bir akışta** tüm yasal uyumluluk verilerini dışarı aktar:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-stream.png" alt-text="Yasal bir uyumluluk verileri akışını sürekli dışa aktarma" lightbox="media/security-center-compliance-dashboard/export-compliance-data-stream.png":::

- Yasal uyumluluk verilerinizin **haftalık anlık görüntülerini** dışarı aktarın:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png" alt-text="Mevzuat uyumluluk verilerinin haftalık anlık görüntüsünü sürekli dışa aktarma" lightbox="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png":::

Ayrıca, uyum verilerinizin **PDF/CSV raporunu** doğrudan mevzuata uyumluluk panosundan dışarı aktarabilirsiniz:

:::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-report.png" alt-text="Yasal uyumluluk verilerinizi PDF veya CSV raporu olarak dışa aktarma" lightbox="media/security-center-compliance-dashboard/export-compliance-data-report.png":::

[Güvenlik Merkezi verilerini sürekli dışa aktarma](continuous-export.md)hakkında daha fazla bilgi edinin.


## <a name="run-workflow-automations-when-there-are-changes-to-your-compliance"></a>Uyumluluğa değişiklik yapıldığında iş akışı otomatiklemeleri çalıştırın

Güvenlik Merkezi 'nin iş akışı Otomasyonu özelliği, mevzuat uyumluluk değerlendirmelerinden biri değiştiğinde Logic Apps tetikleyebilir.

Örneğin, bir uyumluluk değerlendirmesi başarısız olduğunda güvenlik merkezi 'nin belirli bir kullanıcıyı e-posta ile postasını isteyebilirsiniz. Önce mantıksal uygulamayı oluşturmanız gerekir ( [Azure Logic Apps](../logic-apps/logic-apps-overview.md)kullanarak) ve ardından tetikleyiciyi [Güvenlik Merkezi tetikleyicilerine otomatik hale](workflow-automation.md)getirme bölümünde açıklandığı gibi yeni bir iş akışı otomasyonunda ayarlamanız gerekir.

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Bir iş akışı Otomasyonu tetiklemek için mevzuat uyumluluk değerlendirmelerinde yapılan değişiklikleri kullanma" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::




## <a name="faq---regulatory-compliance-dashboard"></a>SSS - Mevzuat uyumluluğu panosu

- [Uyumluluk panosunda hangi standartlar destekleniyor?](#what-standards-are-supported-in-the-compliance-dashboard)
- [Bazı denetimler neden gri renkte görünüyor?](#why-do-some-controls-appear-grayed-out)
- [Panodan PCI-DSS, ISO 27001 veya SOC2 TSP gibi yerleşik bir standart nasıl kaldırabilirim?](#how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard)
- [Öneri temelinde önerilen değişikliği yaptı, ancak panoda yansıtılmıyor](#i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard)
- [Uyumluluk panosuna erişmek için hangi izinlere ihtiyacım var?](#what-permissions-do-i-need-to-access-the-compliance-dashboard)
- [Yasal uyumluluk panosu benim için yüklenmiyor](#the-regulatory-compliance-dashboard-isnt-loading-for-me)
- [Panom 'ta standart olarak geçen ve başarısız denetimler raporunu nasıl görüntüleyebilirim?](#how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard)
- [Uyumluluk verileriyle bir raporu PDF dışında bir biçimde nasıl indirebilirim?](#how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf)
- [Yasal uyumluluk panosundaki bazı ilkeler için nasıl özel durumlar oluşturabilirim?](#how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard)
- [Yasal uyumluluk panosunu kullanmak için hangi Azure Defender planlarına veya lisanslarına ihtiyacım var?](#what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard)
- [Nasıl yaparım? hangi kıyaslama veya standart kullanacağınızı tanıyor musunuz?](#how-do-i-know-which-benchmark-or-standard-to-use)

### <a name="what-standards-are-supported-in-the-compliance-dashboard"></a>Uyumluluk panosunda hangi standartlar destekleniyor?
Varsayılan olarak, mevzuat uyumluluk panosu Azure Güvenlik kıyaslaması ' nı gösterir. Azure Güvenlik kıyaslaması, genel uyumluluk çerçevelerine dayalı olarak güvenlik ve uyumluluk en iyi uygulamaları için Microsoft tarafından yazılan, Azure 'a özgü kılavuzlardır. [Azure Güvenlik kıyaslaması giriş](../security/benchmarks/introduction.md)bölümünde daha fazla bilgi edinin.

Diğer standartlarla uyumluluğunuzu izlemek için, bunları panonuza açıkça eklemeniz gerekir.
 
Azure CIS 1.3.0, NıST SP 800-53, NıST SP 800-171, SWIFT CSP CSCF-V2020, UK resmi ve UK NHS, HIPAA, Kanada Federal PBMM, ISO 27001, SOC2-TSP ve PCI-DSS 3.2.1 gibi diğer standartları ekleyebilirsiniz.  

Panoya daha fazla standartlar eklenecektir ve [mevzuat uyumluluk panonuzdaki standartlar kümesini özelleştirme](update-regulatory-compliance-packages.md)bilgilerine dahil edilecek.

### <a name="why-do-some-controls-appear-grayed-out"></a>Bazı denetimler neden gri renkte görünüyor?
Panodaki her uyumluluk standardı için, Standart denetimlerin bir listesi vardır. İlgili denetimler için, iletme ve başarısız değerlendirmelerin ayrıntılarını görüntüleyebilirsiniz.

Bazı denetimler gri renkte. Bu denetimlere kendileriyle ilişkili güvenlik merkezi değerlendirmeleri yok. Bazıları yordam veya işlemle ilgili olabilir ve bu nedenle Güvenlik Merkezi tarafından doğrulanamaz. Henüz uygulanan otomatik ilkeler veya değerlendirmeler yoktur, ancak gelecekte olacaktır. Ve bazı denetimler, [bulutta paylaşılan sorumluluk](../security/fundamentals/shared-responsibility.md)bölümünde açıklandığı gibi platform sorumluluğu olabilir. 

### <a name="how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard"></a>Panodan PCI-DSS, ISO 27001 veya SOC2 TSP gibi yerleşik bir standart nasıl kaldırabilirim? 
Yasal uyumluluk panosunu özelleştirmek ve yalnızca sizin için geçerli olan standartlara odaklanmak için, kuruluşunuzla ilgili olmayan, görüntülenen düzenleme standartlarından herhangi birini kaldırabilirsiniz. Bir standart kaldırmak için [panonuzdan bir standart kaldırma](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard)bölümündeki yönergeleri izleyin.

### <a name="i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard"></a>Öneri temelinde önerilen değişikliği yaptı, ancak panoda yansıtılmıyor
Önerileri çözümlemek için işlem yaptıktan sonra, uyumluluk verilerinize yönelik değişiklikleri görmek için 12 saat bekleyin. Değerlendirmeler yaklaşık 12 saatte bir çalıştırılır. bu nedenle, uyumluluk verilerinizde yalnızca değerlendirmelerin çalıştırıldıktan sonra etkisini görürsünüz.
 
### <a name="what-permissions-do-i-need-to-access-the-compliance-dashboard"></a>Uyumluluk panosuna erişmek için hangi izinlere ihtiyacım var?
Uyumluluk verilerini görüntülemek için, ilke uyumluluk verilerine en az **okuyucu** erişimine de sahip olmanız gerekir; Güvenlik okuyucusu tek başına yeterli olmaz. Abonelik üzerinde küresel bir okuyucunuz varsa, bu da yeterli olacaktır.

Panoya erişmek ve standartları yönetmek için en düşük rol kümesi, **kaynak Ilkesi katılımcısı** ve **Güvenlik Yöneticisi**'dir.


### <a name="the-regulatory-compliance-dashboard-isnt-loading-for-me"></a>Yasal uyumluluk panosu benim için yüklenmiyor
Yasal uyumluluk panosunu kullanmak için Azure Güvenlik Merkezi 'nin abonelik düzeyinde Azure Defender 'ın etkinleştirilmiş olması gerekir. Pano doğru şekilde yüklenmemesi durumunda aşağıdaki adımları deneyin:

1. Tarayıcınızın önbelleğini temizleyin.
1. Farklı bir tarayıcı deneyin.
1. Panoyu farklı ağ konumundan açmayı deneyin.


### <a name="how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard"></a>Panom 'ta standart olarak geçen ve başarısız denetimler raporunu nasıl görüntüleyebilirim?
Ana panoda, (1) panoda ' ilk 4 ' en düşük uyumluluk standartlarından oluşan geçirme ve başarısız denetimler raporunu görebilirsiniz. Tüm iletme/başarısız denetimlerin durumunu görmek için, (2) **Tümünü göster** (x, izlemekte olduğunuz standartların sayısıdır) seçeneğini belirleyin. Bağlam düzlemi, izlenen standartlarınızın her biri için uyumluluk durumunu görüntüler.

:::image type="content" source="media/security-center-compliance-dashboard/summaries-of-compliance-standards.png" alt-text="Mevzuat uyumluluk panosunun Özet bölümü":::


### <a name="how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf"></a>Uyumluluk verileriyle bir raporu PDF dışında bir biçimde nasıl indirebilirim?
**Raporu indir**' i seçtiğinizde, standart ve BIÇIMI (PDF veya CSV) seçin. Sonuçta elde edilen rapor, portalın filtresinde seçtiğiniz geçerli abonelik kümesini yansıtır.

- PDF raporu seçtiğiniz standart için bir Özet durumu gösterir
- CSV raporu, her denetimle ilişkili ilkelerle ilişkili olduğundan kaynak başına ayrıntılı sonuçlar sağlar

Şu anda özel bir ilke için rapor indirme desteği yoktur; yalnızca sağlanan düzenleme standartları için.


### <a name="how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard"></a>Yasal uyumluluk panosundaki bazı ilkeler için nasıl özel durumlar oluşturabilirim?
Güvenlik Merkezi 'nde yerleşik olan ve güvenli puana dahil olan ilkeler için, [güvenli puanınızdan kaynakları ve önerileri muaf tutma](exempt-resource.md)bölümünde açıklandığı gibi doğrudan portalda bir veya daha fazla kaynak için muafiyet oluşturabilirsiniz.

Diğer ilkeler için, [Azure ilke muafiyet yapısındaki](../governance/policy/concepts/exemption-structure.md)yönergeleri izleyerek doğrudan ilkenin kendisinde bir istisna oluşturabilirsiniz.


### <a name="what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard"></a>Yasal uyumluluk panosunu kullanmak için hangi Azure Defender planlarına veya lisanslarına ihtiyacım var?
Azure Kaynak türlerinizi üzerinde etkinleştirilmiş Azure Defender Paketleriniz varsa, güvenlik merkezi 'nde tüm verileriyle birlikte mevzuat uyumluluk panosuna erişebilirsiniz.


### <a name="how-do-i-know-which-benchmark-or-standard-to-use"></a>Nasıl yaparım? hangi kıyaslama veya standart kullanacağınızı tanıyor musunuz?
[Azure Güvenlik kıyaslaması](https://docs.microsoft.com/security/benchmark/azure/introduction) (ASB), Microsoft tarafından tanımlanan, CIS ve [nıst SP 800-53](https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final) [Microsoft Azure](https://www.cisecurity.org/benchmark/azure/) temel uyumluluk denetim çerçeveleri ile hizalı, Microsoft tarafından tanımlanan, yaygın olarak kullanılan güvenlik önerileri ve en iyi uygulamalar kümesidir. ASB, çok kapsamlı bir kıyaslama sağlar ve çok çeşitli Azure hizmetlerinin en güncel güvenlik yeteneklerini önermek üzere tasarlanmıştır. ASB 'nin güvenlik duruşunu en üst düzeye çıkarmak isteyen müşterilerine ve uyumluluk durumlarını sektör standartlarıyla hizalamasına olanak öneririz.

[CIS kıyaslaması](https://www.cisecurity.org/benchmark/azure/) , Internet GÜVENLIĞI (CIS) için bir bağımsız varlık tarafından yazılır ve temel Azure hizmetlerinin bir alt kümesiyle ilgili öneriler içerir. Azure 'daki en son geliştirmeler sayesinde önerilerin güncel olduğundan emin olmak için CIS ile birlikte çalışacağız, ancak bazen arka planda yer aldığından ve süresi geçmiş hale gelmiştir. Nonetheless, bazı müşteriler bu amacı, CIS 'den ilk ve birincil güvenlik temeli olarak üçüncü taraf değerlendirmesi kullanmak gibi bazı müşteriler.

Azure Güvenlik kıyaslaması yayımladığımız için birçok müşteri, CIS kıyaslamalarıyla ilgili olarak bu uygulamayı geçirmeye seçti.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, güvenlik merkezi 'nin mevzuat uyumluluk panosunu şu şekilde kullanma hakkında bilgi edindiniz:

> [!div class="checklist"]
> * Sizin için önemli olan standartlar ve yönetmeliklerle ilgili uyumluluk durunuzu görüntüleyin ve izleyin.
> * İlgili önerileri çözümleyerek ve Uyumluluk puanı iyileştirerek uyumluluğu izleyerek uyumluluk durumunuzu geliştirebilirsiniz.

Mevzuat uyumluluk panosu uyumluluk sürecini büyük ölçüde basitleştirebilir ve Azure, hibrit ve çok bulut ortamınız için uyumluluk kanıtını toplamak için gereken süreyi önemli ölçüde kesebilir.

Daha fazla bilgi edinmek için bu ilgili sayfalara bakın:

- [Yasal uyumluluk panonuzda standartlar kümesini özelleştirme](update-regulatory-compliance-packages.md) -yasal uyumluluk panonuzda hangi standartların görüneceğini seçme hakkında bilgi edinin. 
- [Azure Güvenlik Merkezi 'nde güvenlik önerilerini yönetme](security-center-recommendations.md) -Azure kaynaklarınızı korumaya yardımcı olması Için Güvenlik Merkezi 'ndeki önerilerin nasıl kullanılacağını öğrenin.