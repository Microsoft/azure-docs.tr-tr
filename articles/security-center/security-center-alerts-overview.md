---
title: Azure Güvenlik Merkezi 'nde güvenlik uyarıları ve olaylar
description: Azure Güvenlik Merkezi 'nin güvenlik uyarıları oluşturup bunları olaylar halinde nasıl sağladığını öğrenin.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: memildin
ms.openlocfilehash: cdf46a5f0185e33e1e877e3682ab418a00725de2
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012555"
---
# <a name="security-alerts-and-incidents-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde güvenlik uyarıları ve olaylar

Güvenlik Merkezi, Azure, şirket içi ve hibrit bulut ortamlarınızda dağıtılan kaynaklar için uyarı oluşturur.

Güvenlik uyarıları, gelişmiş algılamalar tarafından tetiklenir ve yalnızca Azure Defender ile kullanılabilir. [Hızlı başlangıç: Azure Defender 'ı etkinleştirme](enable-azure-defender.md)bölümünde açıklandığı gibi **fiyatlandırma & ayarları** sayfasından yükseltebilirsiniz. Ücretsiz 30 günlük deneme sürümü mevcuttur. Seçtiğiniz para birimindeki ve bölgenize göre fiyatlandırma ayrıntıları için bkz. [Güvenlik Merkezi fiyatlandırması](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="what-are-security-alerts-and-security-incidents"></a>Güvenlik uyarıları ve güvenlik olayları nelerdir? 

**Uyarılar** , güvenlik merkezi 'nin kaynaklarınızın tehditleri algıladığında oluşturduğu bildirimlerdir. Güvenlik Merkezi, uyarıları hızlı bir şekilde araştırmanız için gereken bilgilerle birlikte, uyarıları önceliklendirir ve listeler. Güvenlik Merkezi ayrıca bir saldırıyı nasıl düzeltebileceğiniz konusunda öneriler sağlar.

**Güvenlik olayı** , her uyarıyı ayrı ayrı listelemek yerine ilgili uyarıların bir koleksiyonudur. Güvenlik Merkezi, farklı uyarıların ve düşük uygunlukta sinyallerin güvenlik olaylarına ilişkilendirilmesi için [bulut Akıllı uyarı bağıntısını](#cloud-smart-alert-correlation-in-azure-security-center-incidents) kullanır.

Güvenlik Merkezi, olayları kullanarak bir saldırı kampanyasının ve tüm ilgili uyarıların tek bir görünümünü sağlar. Bu görünüm, saldırganın hangi eylemleri yaptığını ve hangi kaynakların etkilendiğini hızlıca anlamanıza olanak sağlar.

## <a name="respond-to-todays-threats"></a>Bugünün tehditlerine yanıt verme

Son 20 yılda tehdit kapsamında önemli değişiklikler olmuştur. Geçmişte, şirketler genellikle "neler yapabileceklerini" görmekte olan bireysel saldırganlar tarafından yalnızca Web sitesi savunma hakkında endişelenmek zorunda kalmıştı. Günümüzde saldırganlar çok daha gelişmiş ve düzenlenmiştir. Saldırganlar genellikle belirli finansal ve stratejik hedeflere sahiptir. Ayrıca, bu kaynaklar, nasyon durumları veya düzenlenmiş SUI tarafından komik olabileceği için daha fazla kaynak kullanabilir.

Bu değişen bu değişiklik, saldırgan derecelendirmelerinin dışı bir professionalism düzeyine kadar LED 'e sahiptir. Saldırganlar artık web tahrifatı ile ilgilenmemektedir. Bunlar artık bilgileri, finansal hesapları ve özel verileri çalmaya ve bunların tümünün açık pazar üzerinde nakit oluşturmak veya belirli bir iş, siyasi veya askeri bir konumdan yararlanmak için kullanabileceği bir şekilde ilgileniyor. Mali hedefleri olan saldırganlardan çok daha fazla endişe verici olan ise altyapıya ve insanlara zarar vermek için ağları ihlal eden saldırganlardır.

Buna yanıt olarak, kuruluşlar çoğunlukla bilinen saldırı imzalarını arayarak kurumsal çevrelerini veya uç noktalarını savunmaya odaklanan çeşitli nokta çözümleri dağıtmaktadır. Bu çözümler, güvenlik analizi uzmanının belirlemesini ve araştırmasını gerektiren yüksek miktarda düşük güvenilirlik uyarıları oluşturma eğilimindedir. Çoğu kurum bu uyarılara yanıt verecek zaman ve uzmanlığa sahip olmadığından birçoğu çözüme kavuşmamaktadır.  

Ayrıca saldırganlar, çok sayıda imza tabanlı savunma ve [bulut ortamlarına uyum](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/)sağlamak için kendi yöntemlerini geliştirmiştir. Ortaya çıkan tehditleri daha hızlı belirlemek ve algılama ile yanıtı hızlandırmak için yeni yaklaşımlar gereklidir.


## <a name="continuous-monitoring-and-assessments"></a>Sürekli izleme ve değerlendirmeler

Azure Güvenlik Merkezi, Microsoft 'un tamamında güvenlik araştırması ve veri bilimi ekiplerine sahip olmanın yanı sıra tehdit yatadaki değişiklikleri sürekli olarak izlemeye yöneliktir. Buna aşağıdaki girişimler dahildir:

* **Tehdit bilgileri izleme**: tehdit bilgileri, mevcut veya ortaya çıkan tehditler hakkında mekanizmalar, göstergeler, etkiler ve eyleme dönüştürülebilir öneriler içerir. Bu bilgileri güvenlik topluluğu içinde paylaşılır ve Microsoft, iç ve dış kaynaklardan gelen tehdit bilgisi akışlarını sürekli olarak izler.
* **Sinyal paylaşımı**: Microsoft 'un genel bulut ve şirket içi hizmetler, sunucular ve istemci uç noktası cihazlarının geniş portföyündeki güvenlik ekiplerinden Öngörüler paylaşılır ve çözümlenir.
* **Microsoft güvenlik uzmanları**: Microsoft’ta hukuk ve web saldırıcı algılama gibi uzman güvenlik alanlarında çalışan ekiplerle sürekli iletişim.
* **Algılama ayarı**: Gerçek müşteri veri kümelerine göre algoritmalar çalıştırılır ve güvenlik araştırmacıları, sonuçları doğrulamak üzere müşterilerle işbirliği yapar. Doğru ve yanlış pozitifler kullanılarak machine learning algoritmaları iyileştirilir.

Bu Birleşik çabalar, anında yararlanabilmeniz için yeni ve geliştirilmiş Algılamalarda bulunur.

## <a name="how-does-security-center-detect-threats"></a>Güvenlik Merkezi tehditleri nasıl algılar? <a name="detect-threats"> </a>

Microsoft güvenlik araştırmacıları sürekli olarak tehditleri araştırmaktadır. Bulutta ve şirket içinde küresel varlığımız nedeniyle, expantik bir telemetri kümesine eriştik. Geniş çaplı ve farklı veri kümeleri koleksiyonu, şirket içi tüketicimiz ve Kurumsal Ürünlerimiz genelinde yeni saldırı desenleri ve eğilimleri bulmamızı ve çevrimiçi hizmetler. Sonuç olarak, saldırganlar yeni ve giderek karmaşık hale gelen sömürülerini ortaya çıkardıkça Güvenlik Merkezi algılama algoritmalarını hızlı bir şekilde güncelleştirebilmektedir. Bu yaklaşık hızlı hareket eden bir ortama ayak uydurmanıza yardımcı olmaktadır.

Güvenlik Merkezi, gerçek tehditleri algılamak ve hatalı pozitif sonuçları azaltmak için Azure kaynaklarınızdan ve ağınızdan gelen günlük verilerini toplar, çözümler ve tümleştirir. Ayrıca, güvenlik duvarı ve uç nokta koruma çözümleri gibi bağlı iş ortağı çözümleri ile de birlikte kullanılabilir. Güvenlik Merkezi, tehditleri belirlemek için genellikle birden çok kaynaktan gelen bilgileri ilişkilendirmek üzere bu bilgileri analiz eder.

![Güvenlik Merkezi Veri toplama ve sunumu](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

Güvenlik Merkezi, imza tabanlı yaklaşımların ötesine geçen gelişmiş güvenlik analizleri kullanır. Büyük veri ve [machine learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) teknolojilerindeki sıçramalar, tüm bulut yapısındaki olayları değerlendirmek için kullanılır: elle yaklaşımlar kullanılarak ve saldırıların gelişimi tahmin edilerek belirlenmesi imkansız olan tehditler algılanır. Bu güvenlik analizleri şunlardır:

- **Tümleşik tehdit bilgileri**: Microsoft, küresel tehdit zekasının miktarına sahiptir. Azure, Microsoft 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes birimi (DCU) ve Microsoft Güvenlik Yanıt Merkezi (MSRC) gibi birden fazla kaynaktan telemetri akışı. Araştırmacılar, büyük bulut hizmeti sağlayıcıları ve diğer üçüncü taraflardan akışlar arasında paylaşılan tehdit bilgileri bilgilerini de alır. Azure Güvenlik Merkezi bilinen kötü aktörlerden gelen tehditler konusunda sizi uyarmak için bu bilgileri kullanabilir.

- **Davranış analizi**: davranış analizi, verileri bir bilinen desenler koleksiyonuyla çözümleyen ve karşılaştıran bir tekniktir. Ancak, bu modeller basit imzalar değildir. Bunlar büyük veri kümelerine uygulanan karmaşık machine learning algoritmaları aracılığıyla belirlenir. Bunlar, kötü amaçlı davranışların uzman analistler tarafından dikkatlice çözümlenmesiyle de belirlenir. Azure Güvenlik Merkezi; sanal makine günlükleri, sanal ağ cihaz günlükleri, yapı günlükleri ve diğer kaynakların analizine göre tehlike giren kaynaklarını belirlemek amacıyla davranış analizini kullanabilir.

- **Anomali algılama**: Azure Güvenlik Merkezi, tehditleri belirlemek için anomali algılama de kullanır. Davranış analizinin aksine (büyük veri kümelerinden türetilmiş bilinen desenlere bağlıdır), anomali algılama daha fazla "kişiselleştirilmiş" olur ve dağıtımlarınıza özgü taban çizgilerine odaklanır. Dağıtımlarınızın normal etkinliğini belirlemek için machine learning uygulanır ve sonra bir güvenlik olayını gösterebilecek aykırı değer koşullarını tanımlamak üzere kurallar oluşturulur.

## <a name="how-are-alerts-classified"></a>Uyarılar nasıl sınıflandırıldı?

Güvenlik Merkezi, her uyarıya katılma sırasını önceliklendirmenize yardımcı olmak için uyarılara önem derecesi atar. böylece, bir kaynak tehlikeye girdiğinde, hemen bu işe başlayabilirsiniz. Önem derecesi, uyarı veren etkinliğin arkasında kötü amaçlı bir amaç olduğunu ve uyarıyı vermek için kullanılan analitik düzeyini, ne kadar güvenli bir güvenlik merkezinin olduğunu temel alır.

> [!NOTE]
> Uyarı önem derecesi, Portal 'da ve 01-01-2019 ön tarihlik REST API sürümlerinde farklı şekilde görüntülenir. API 'nin eski bir sürümünü kullanıyorsanız, aşağıda açıklanan tutarlı deneyime yükseltin.

| Önem derecesi          | Önerilen yanıt      |
|---|---|
| **Yüksek**          | Kaynağınızın güvenliğinin tehlikeye girdiği büyük bir olasılık vardır. Hemen bir yere bakmanız gerekir. Güvenlik Merkezi, hem kötü amaçlı amaç hem de uyarı vermek için kullanılan bulgularda yüksek güvenilirliğe sahiptir. Örneğin, Mimikatz gibi bilinen kötü amaçlı bir aracın yürütülmesini algılayan bir uyarı, kimlik bilgilerinin çalınması için kullanılan ortak bir araçtır.                                                                                                               |
| **Medium**        | Bu muhtemelen şüpheli bir etkinlik, bir kaynağın tehlikede olduğunu gösteriyor olabilir. Güvenlik Merkezi 'nin analitik veya bulma 'daki güvenilirliği orta ve kötü amaçlı amaç 'nin güvenilirliği orta ile yüksektir. Bunlar genellikle makine öğrenimi veya anomali tabanlı algılamalar olur. Örneğin, anormal bir konumdan oturum açma girişimi.                                                                                                                |
| **Düşük**           | Bu bir zararsız veya engellenmiş saldırı olabilir. Güvenlik Merkezi, amacın kötü amaçlı olduğu ve etkinliğin masum olabileceğinden emin olmak için yeterli değildir. Örneğin, günlük Temizleme, bir saldırgan parçaları gizlemeyi denediğinde oluşabilecek bir eylemdir, ancak çoğu durumda Yöneticiler tarafından gerçekleştirilen bir rutin işlemdir. Güvenlik Merkezi, baktığımız ilginç bir durum olmadığı takdirde saldırı ne zaman engellendiğine ilişkin bilgi vermez. |
| **Bilgilendirici** | Bir olay genellikle bilgilendirici bir dizi uyarıdan oluşur, bazıları yalnızca bilgilendirme amaçlıdır, ancak diğer uyarıların bağlamında daha yakından bir görünmeyebilir.                                                                                                                         |

## <a name="export-alerts"></a>Uyarıları dışarı aktarma

Uyarıları Güvenlik Merkezi dışında görüntülemek için kullanabileceğiniz bir dizi seçeneğiniz vardır; örneğin:

- Uyarı panosunda **CSV raporu indir** , CSV 'ye bir kerelik dışarı aktarma sağlar.
- Fiyatlandırma & ayarlarından **sürekli dışarı aktarma** , güvenlik uyarıları ve önerilerin akışlarını Log Analytics çalışma alanları ve Event Hubs için yapılandırmanıza olanak tanır. [Sürekli dışarı aktarma hakkında daha fazla bilgi edinin](continuous-export.md)
- **Azure Sentinel Bağlayıcısı** , Azure Güvenlik Merkezi 'ndeki güvenlik uyarılarını Azure Sentinel 'e akışlar. [Azure Güvenlik Merkezi 'Ni Azure Sentinel ile bağlama hakkında daha fazla bilgi edinin](../sentinel/connect-azure-security-center.md)

## <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Azure Güvenlik Merkezi 'nde bulut Akıllı uyarı bağıntısı (olaylar)

Azure Güvenlik Merkezi, kötü amaçlı etkinlikler hakkında sizi uyarmak için gelişmiş analiz ve tehdit bilgilerini kullanarak karma bulut iş yüklerini sürekli olarak analiz eder.

Tehdit kapsamının kapsamı artıyor. Farklı uyarıları önceliklendirmek ve gerçek bir saldırıyı belirlemek Güvenlik analistlerinin de belirlenmesi gerekir. Güvenlik Merkezi bu uyarı ile analistlere yardımcı olur. Farklı uyarıların ve düşük doğruluk sinyallerinin güvenlik olaylarına sahip olduğu gibi saldırıları tanılamanıza yardımcı olur.

Fusion Analytics, güvenlik merkezi olaylarını güçlendirir ve farklı uyarıların ve bağlamsal sinyallerin birlikte ilişkilendirilene olanak tanıyan teknoloji ve analitik arka bitidir. Fusion, kaynaklar genelinde bir abonelikte bildirilen farklı sinyallere bakar. Fusion, paylaşılan bağlamsal bilgilerle saldırı ilerlemesini veya sinyalleri açığa çıkarmak için birleştirilmiş bir yanıt prosedürü kullanacağınızı belirten desenler bulur.

Fusion Analytics, uyarıları analiz etmek ve yeni saldırı desenlerini keşfetmek üzere AI ile güvenlik etki alanı bilgisini birleştirir. 

Güvenlik Merkezi, uyarıları algılanan amaçla ilişkilendirmek için MITRE saldırı matrisini kullanır, bu da güvenlik etki alanı bilgisini şekillendirmeye yardımcı olur. Ayrıca, güvenlik merkezi bir saldırının her adımında toplanan bilgileri kullanarak bir saldırının adımları gibi görünen etkinliği ayarlayabilir ancak aslında değildir.

Saldırı genellikle farklı kiracılar genelinde gerçekleştiğinden, güvenlik merkezi her abonelikte bildirilen saldırı dizilerini çözümlemek için AI algoritmalarını birleştirebilir. Bu teknik, saldırı dizilerini, yalnızca birbirleriyle ilişkilendirilmiş arada yerine yaygın olarak karşılaşılan uyarı desenleri olarak tanımlar.

Bir olayın araştırılması sırasında Analistler genellikle tehdit doğası ve bunun nasıl azaltılacağını öğrenmek için ek bağlam gerektirir. Örneğin, bir ağ anomali algılandığında, ağda başka ne olduğunu veya hedeflenen kaynakla ilgili olduğunu anlamadan bile, ileri yapılacak işlemleri anlamak zordur. Yardım için, bir güvenlik olayının yapıtları, ilgili olayları ve bilgileri içermesi olabilir. Güvenlik olayları için kullanılabilen ek bilgiler, algılanan tehdit türüne ve ortamınızın yapılandırmasına bağlı olarak farklılık gösterir. 

> [!TIP]
> Fusion Analytics tarafından üretilebilen güvenlik olay uyarılarının bir listesi için, [Uyarı tablosuna](alerts-reference.md#alerts-fusion)bakın.

:::image type="content" source="./media/security-center-alerts-cloud-smart/security-incident.png" alt-text="Güvenlik olayı algılanan raporunun ekran görüntüsü":::

Güvenlik olaylarınızı yönetmek için bkz. [Azure Güvenlik Merkezi 'nde güvenlik olaylarını yönetme](security-center-incident.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, güvenlik merkezi 'nde bulunan farklı Uyarı türleri hakkında bilgi edindiniz. Daha fazla bilgi için bkz.

- [Azure etkinlik günlüğündeki güvenlik uyarıları](https://go.microsoft.com/fwlink/?linkid=2114113) -Azure Portal veya program aracılığıyla kullanılabilir olmasının yanı sıra, güvenlik uyarıları ve olayları Azure etkinlik günlüğünde olay olarak denetlenir
- [Azure Defender uyarıları başvuru tablosu](alerts-reference.md)
- [Güvenlik uyarılarını yanıtlama](security-center-managing-and-responding-alerts.md#respond-to-security-alerts)