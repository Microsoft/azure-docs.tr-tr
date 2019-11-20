---
title: Azure Güvenlik Merkezi 'nde güvenlik uyarıları | Microsoft Docs
description: Bu konu başlığı altında, güvenlik uyarıları ve Azure Güvenlik Merkezi 'nde bulunan farklı türler açıklanmaktadır.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: bd538d436d1c900a370f522bccf724eaf87210b4
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582614"
---
# <a name="security-alerts-in-azure-security-center"></a>Azure Güvenlik Merkezi'nde güvenlik uyarıları

Azure Güvenlik Merkezi 'nde birçok farklı kaynak türü için çeşitli uyarılar vardır. Güvenlik Merkezi, Azure 'da dağıtılan ve ayrıca şirket içi ve hibrit bulut ortamlarında dağıtılan kaynaklar için uyarı oluşturur.

Gelişmiş algılamalar Azure Güvenlik Merkezi'nin Standart Katmanında mevcuttur. Ücretsiz deneme sürümü mevcuttur. [Güvenlik İlkesi](security-center-pricing.md) bölümündeki Fiyatlandırma Katmanı’ndan yükseltme yapabilirsiniz. Fiyatlandırma hakkında daha fazla bilgi almak için [Güvenlik Merkezi sayfasını](https://azure.microsoft.com/pricing/details/security-center/) ziyaret edin.

## Bugünün tehditlerine <a name="respond-threats"></a> yanıt verme

Son 20 yılda tehdit kapsamında önemli değişiklikler olmuştur. Geçmişte şirketlerin yalnızca çoğunlukla “yapabileceklerini” görmeyle ilgilenen bireysel saldırganların web sitesi tahrifatından endişe duyması gerekiyordu. Bugün ise saldırganlar çok daha karmaşık ve organize hareket etmektedir. Saldırganlar genellikle belirli finansal ve stratejik hedeflere sahiptir. Ayrıca, ulus devletler veya organize suç örgütleri tarafından finanse edilebildiklerinden ellerinde daha fazla kaynak bulunmaktadır.

Bu değişen bu değişiklik, saldırgan derecelendirmelerinin dışı bir professionalism düzeyine kadar LED 'e sahiptir. Saldırganlar artık web tahrifatı ile ilgilenmemektedir. Bunlar artık bilgileri, finansal hesapları ve özel verileri çalmaya ve bunların tümünün açık pazar üzerinde nakit oluşturmak veya belirli bir iş, siyasi veya askeri bir konumdan yararlanmak için kullanabileceği bir şekilde ilgileniyor. Mali hedefleri olan saldırganlardan çok daha fazla endişe verici olan ise altyapıya ve insanlara zarar vermek için ağları ihlal eden saldırganlardır.

Buna yanıt olarak, kuruluşlar çoğunlukla bilinen saldırı imzalarını arayarak kurumsal çevrelerini veya uç noktalarını savunmaya odaklanan çeşitli nokta çözümleri dağıtmaktadır. Bu çözümler, güvenlik analizi uzmanının belirlemesini ve araştırmasını gerektiren yüksek miktarda düşük güvenilirlik uyarıları oluşturma eğilimindedir. Çoğu kurum bu uyarılara yanıt verecek zaman ve uzmanlığa sahip olmadığından birçoğu çözüme kavuşmamaktadır.  

Ayrıca saldırganlar, çok sayıda imza tabanlı savunma ve [bulut ortamlarına uyum](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/)sağlamak için kendi yöntemlerini geliştirmiştir. Ortaya çıkan tehditleri daha hızlı belirlemek ve algılama ile yanıtı hızlandırmak için yeni yaklaşımlar gereklidir.

## <a name="what-are-security-alerts"></a>Güvenlik uyarıları nedir?

Uyarılar, güvenlik merkezi 'nin kaynaklarınızın tehditleri algıladığında oluşturduğu bildirimlerdir. Güvenlik Merkezi, uyarıları hızlı bir şekilde araştırmanız için gereken bilgilerle birlikte, uyarıları önceliklendirir ve listeler. Güvenlik Merkezi ayrıca bir saldırıyı nasıl düzeltebileceğiniz konusunda öneriler de sağlar.

## Güvenlik Merkezi tehditleri nasıl algılar? <a name="detect-threats"> </a>

Microsoft güvenlik araştırmacıları sürekli olarak tehditleri araştırmaktadır. Microsoft 'un bulutta ve şirket içinde küresel olup olmadığı için, bir dizi telemetri kümesine erişebilirler. Geniş ve çok sayıda veri kümesi, yeni saldırı desenlerinin ve eğilimler ile şirket içi tüketici ve kurumsal ürünlerin yanı sıra çevrimiçi hizmetler. Sonuç olarak, saldırganlar yeni ve giderek karmaşık hale gelen sömürülerini ortaya çıkardıkça Güvenlik Merkezi algılama algoritmalarını hızlı bir şekilde güncelleştirebilmektedir. Bu yaklaşık hızlı hareket eden bir ortama ayak uydurmanıza yardımcı olmaktadır.

Güvenlik Merkezi, gerçek tehditleri algılamak ve hatalı pozitif sonuçları azaltmak için Azure kaynaklarınızdan ve ağınızdan gelen günlük verilerini toplar, çözümler ve tümleştirir. Ayrıca, güvenlik duvarı ve uç nokta koruma çözümleri gibi bağlı iş ortağı çözümleri ile de birlikte kullanılabilir. Güvenlik Merkezi, tehditleri belirlemek için genellikle birden çok kaynaktan gelen bilgileri ilişkilendirmek üzere bu bilgileri analiz eder.

![Güvenlik Merkezi Veri toplama ve sunumu](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

Güvenlik Merkezi, imza tabanlı yaklaşımların ötesine geçen gelişmiş güvenlik analizleri kullanır. Büyük veri ve [machine learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) teknolojilerindeki sıçramalar, tüm bulut yapısındaki olayları değerlendirmek için kullanılır: elle yaklaşımlar kullanılarak ve saldırıların gelişimi tahmin edilerek belirlenmesi imkansız olan tehditler algılanır. Bu güvenlik analizleri şunlardır:

* **Tümleşik tehdit bilgileri**: Microsoft ürün ve Hizmetleri, Microsoft Digital Crimes birimi (DCU), Microsoft Güvenlik Yanıt MERKEZI (MSRC) ve dış akışların küresel tehdit bilgilerini kullanarak bilinen kötü aktörler için arama yapar.
* **Davranış analizi**: kötü amaçlı davranışı keşfetmeye yönelik bilinen desenleri uygular.
* **Anomali algılama**: bir geçmiş taban çizgisi oluşturmak için istatistiksel profil oluşturma kullanır. Olası bir saldırı vektörüne uygun olan yerleşik taban çizgilerinden sapmalar konusunda uyarır.

Aşağıdaki bölümler, bu analizlerin her birini daha ayrıntılı olarak tartışır.

### <a name="integrated-threat-intelligence"></a>Tümleşik tehdit bilgileri

Microsoft yoğun miktarda genel tehdit bilgisine sahiptir. Azure, Office 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes birimi (DCU) ve Microsoft Güvenlik Yanıt Merkezi (MSRC) gibi birden fazla kaynaktan telemetri akışı. Araştırmacılar, büyük bulut hizmeti sağlayıcıları ve diğer üçüncü taraflardan akışlar arasında paylaşılan tehdit bilgileri bilgilerini de alır. Azure Güvenlik Merkezi bilinen kötü aktörlerden gelen tehditler konusunda sizi uyarmak için bu bilgileri kullanabilir.

### <a name="behavioral-analytics"></a>Davranış analizi

Davranış analizi, verileri analiz eden ve bilinen modeller koleksiyonuyla karşılaştıran bir tekniktir. Ancak, bu modeller basit imzalar değildir. Bunlar büyük veri kümelerine uygulanan karmaşık machine learning algoritmaları aracılığıyla belirlenir. Bunlar, kötü amaçlı davranışların uzman analistler tarafından dikkatlice çözümlenmesiyle de belirlenir. Azure Güvenlik Merkezi, sanal makine günlüklerinin, sanal ağ cihaz günlüklerinin, yapı günlüklerinin, kilitlenme dökümlerinin ve diğer kaynakların analizine dayalı olarak güvenliği aşılmış kaynakları belirlemek için davranış analizini kullanabilir.

Ayrıca, geniş bir kampanyanın destek kanıtını denetlemek için başka sinyallerle bağıntı vardır. Bu bağıntı yerleşik tehlike göstergeleriyle tutarlı olayları tanımlamaya yardımcı olur. 

### <a name="anomaly-detection"></a>Anormallik algılama

Azure Güvenlik Merkezi, tehditleri tanımlamak için anormallik algılamayı da kullanır. Davranış analizinden (büyük veri kümelerinden türetilmiş bilinen modellere bağlıdır) farklı olarak anormallik algılama daha fazla “kişiselleştirilmiştir” ve dağıtımlarınıza özel taban çizgilerine odaklanır. Dağıtımlarınızın normal etkinliğini belirlemek için machine learning uygulanır ve sonra bir güvenlik olayını gösterebilecek aykırı değer koşullarını tanımlamak üzere kurallar oluşturulur.

## <a name="how-are-alerts-classified"></a>Uyarılar nasıl sınıflandırıldı?

Güvenlik Merkezi, her uyarıya katılma sırasını önceliklendirmenize yardımcı olmak için uyarılara önem derecesi atar. böylece, bir kaynak tehlikeye girdiğinde, hemen bu işe başlayabilirsiniz. Önem derecesi, uyarı veren etkinliğin arkasında kötü amaçlı bir amaç olduğunu ve uyarıyı vermek için kullanılan analitik düzeyini, ne kadar güvenli bir güvenlik merkezinin olduğunu temel alır.

> [!NOTE]
> Uyarı önem derecesi portalda ve REST API farklı şekilde görüntülenirken, farklar aşağıdaki listede belirtilmiştir.

* **Yüksek:** Kaynağınızın güvenliğinin tehlikeye girdiği büyük bir olasılık vardır. Hemen bir yere bakmanız gerekir. Güvenlik Merkezi, hem kötü amaçlı amaç hem de uyarı vermek için kullanılan bulgularda yüksek güvenilirliğe sahiptir. Örneğin, Mimikatz gibi bilinen kötü amaçlı bir aracın yürütülmesini algılayan bir uyarı, kimlik bilgilerinin çalınması için kullanılan ortak bir araçtır.
* **Orta (REST API düşük olabilir)** : Bu, büyük olasılıkla bir kaynağın tehlikeye girdiği anlamına gelebilir.
Güvenlik Merkezi 'nin analitik veya bulma 'daki güvenilirliği orta ve kötü amaçlı amaç 'nin güvenilirliği orta ile yüksektir. Bunlar genellikle makine öğrenimi veya anomali tabanlı algılamalar olur. Örneğin, anormal bir konumdan oturum açma girişimi.
* **Düşük (REST API bilgiler)** : Bu, bir zararsız veya engellenmiş saldırı olabilir.
   * Güvenlik Merkezi, amacın kötü amaçlı olduğu ve etkinliğin masum olabileceğinden emin olmak için yeterli değildir. Örneğin, günlük Temizleme, bir saldırgan parçaları gizlemeyi denediğinde gerçekleşemeyen bir eylemdir, ancak çoğu durumda Yöneticiler tarafından gerçekleştirilen bir rutin işlemdir.
   * Güvenlik Merkezi, baktığımız ilginç bir durum olmadığı takdirde saldırı ne zaman engellendiğine ilişkin bilgi vermez. 
* **Bilgilendirici (REST API sessiz)** : yalnızca bir güvenlik olayının detayına göre veya REST API belirli BIR uyarı kimliğiyle kullandığınızda bilgilendirme uyarıları görürsünüz. Genellikle bir olay bir dizi uyarıdan oluşur, bazıları yalnızca bilgilendirici olarak görünebilir, ancak diğer uyarıların bağlamında daha yakından bir görünüme sahip olabilir. 
 

## <a name="continuous-monitoring-and-assessments"></a>Sürekli izleme ve değerlendirmeler

Azure Güvenlik Merkezi, Microsoft 'un tamamında güvenlik araştırması ve veri bilimi ekiplerine sahip olmanın yanı sıra tehdit yatadaki değişiklikleri sürekli olarak izlemeye yöneliktir. Buna aşağıdaki girişimler dahildir:

* **Tehdit bilgileri izleme**: tehdit bilgileri, mevcut veya ortaya çıkan tehditler hakkında mekanizmalar, göstergeler, etkiler ve eyleme dönüştürülebilir öneriler içerir. Bu bilgileri güvenlik topluluğu içinde paylaşılır ve Microsoft, iç ve dış kaynaklardan gelen tehdit bilgisi akışlarını sürekli olarak izler.
* **Sinyal paylaşımı**: Microsoft'un bulut ve şirket içi hizmetler, sunucular ve istemci uç noktası cihazları içeren geniş portföyünde güvenlik ekiplerinden alınan bilgiler paylaşılır ve analiz edilir.
* **Microsoft güvenlik uzmanları**: Microsoft’ta hukuk ve web saldırıcı algılama gibi uzman güvenlik alanlarında çalışan ekiplerle sürekli iletişim.
* **Algılama ayarı**: Gerçek müşteri veri kümelerine göre algoritmalar çalıştırılır ve güvenlik araştırmacıları, sonuçları doğrulamak üzere müşterilerle işbirliği yapar. Doğru ve yanlış pozitifler kullanılarak machine learning algoritmaları iyileştirilir.

Bu birleşik çabalar yeni ve geliştirilmiş algılamalarla sonuçlanır ve sizin hiçbir şey yapmanıza gerek kalmaz.

## Güvenlik uyarısı türleri <a name="security-alert-types"></a>

Aşağıdaki konular, kaynak türlerine göre farklı uyarılarda size rehberlik sağlar:

* [IaaS VM 'Leri ve sunucu uyarıları](security-center-alerts-iaas.md)
* [Yerel işlem uyarıları](security-center-alerts-compute.md)
* [Veri Hizmetleri uyarıları](security-center-alerts-data-services.md)

Aşağıdaki konularda, Azure üzerinde dağıtılan kaynaklar için ek koruma katmanları uygulamak amacıyla, güvenlik merkezi 'nin Azure altyapısıyla tümleştirerek topladığı farklı Telemetriyi nasıl kullandığı açıklanmaktadır:

* [Hizmet katmanı uyarıları](security-center-alerts-service-layer.md)
* [Azure Güvenlik ürünleriyle tümleştirme](security-center-alerts-integration.md)

## <a name="what-are-security-incidents"></a>Güvenlik olayları nelerdir?

Güvenlik olayı, her uyarıyı ayrı ayrı listelemek yerine ilgili uyarıların bir koleksiyonudur. Güvenlik Merkezi, farklı uyarıların ve düşük uygunlukta sinyallerin güvenlik olaylarına ilişkilendirilmesi için [bulut Akıllı uyarı bağıntısını](security-center-alerts-cloud-smart.md) kullanır.

Güvenlik Merkezi, olayları kullanarak bir saldırı kampanyasının ve tüm ilgili uyarıların tek bir görünümünü sağlar. Bu görünüm, saldırganın hangi eylemleri yaptığını ve hangi kaynakların etkilendiğini hızlıca anlamanıza olanak sağlar. Daha fazla bilgi için bkz. [bulut Akıllı uyarı bağıntısı](security-center-alerts-cloud-smart.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, güvenlik merkezi 'nde bulunan farklı Uyarı türleri hakkında bilgi edindiniz. Daha fazla bilgi için bkz.

* [Azure Güvenlik Merkezi planlama ve işlemler kılavuzu](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure Güvenlik Merkezi SSS](https://docs.microsoft.com/azure/security-center/security-center-faq)

