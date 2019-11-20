---
title: Azure Gelişmiş tehdit algılama | Microsoft Docs
description: Azure AD Kimlik Koruması ve özellikleri hakkında bilgi edinin.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 6278e848a82fb31939117fa9b916a92a2fb74a3e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129274"
---
# <a name="azure-advanced-threat-detection"></a>Azure Gelişmiş tehdit algılama

Azure, Azure Active Directory (Azure AD), Azure Izleyici günlükleri ve Azure Güvenlik Merkezi gibi hizmetler aracılığıyla gelişmiş tehdit algılama işlevselliği sunar. Bu güvenlik hizmetleri ve özellikleri koleksiyonu, Azure dağıtımlarınızda neler olduğunu anlamak için basit ve hızlı bir yol sağlar.

Azure, uygulama dağıtımlarınızın gereksinimlerini karşılamak için güvenliği yapılandırmak ve özelleştirmek üzere çok sayıda seçenek sunar. Bu makalede, bu gereksinimlerin nasıl karşıladığı açıklanır.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Kimlik Koruması

[Azure AD kimlik koruması](../../active-directory/identity-protection/overview.md) , kuruluşunuzun kimliklerini etkileyebilecek risk algılamaları ve olası güvenlik açıklarına genel bir bakış sağlayan bir [Azure Active Directory Premium P2](../../active-directory/active-directory-whatis.md) Edition özelliğidir. Kimlik koruması, [Azure AD anormal etkinlik raporları](../../active-directory/active-directory-reporting-azure-portal.md)aracılığıyla kullanılabilen mevcut Azure AD anomali algılama yeteneklerini kullanır ve gerçek zamanlı anormallikleri tespit eden yeni risk algılama türlerini tanıtır.

![Azure AD Kimlik Koruması diyagramı](./media/threat-detection/azure-threat-detection-fig1.png)

Kimlik koruması, bir kimliğin tehlikede olduğunu gösterebilen anormallikleri ve risk algılamalarını tespit etmek için uyarlamalı makine öğrenme algoritmaları ve buluşsal yöntemler kullanır. Bu verileri kullanarak, kimlik koruması, bu risk algılamalarını araştırmak ve uygun düzeltme ya da risk azaltma eylemi gerçekleştirmek için raporlar ve uyarılar oluşturur.

Azure Active Directory Kimlik Koruması, bir izleme ve raporlama aracından daha fazla. Risk algılamaları temelinde, kimlik koruması her bir kullanıcı için bir Kullanıcı risk düzeyi hesapladığında, böylece kuruluşunuzun kimliklerini otomatik olarak korumak için risk tabanlı ilkeleri yapılandırabilirsiniz.

Bu risk tabanlı ilkeler, Azure Active Directory ve [EMS](../../active-directory/active-directory-conditional-access-azure-portal.md)tarafından sunulan diğer [koşullu erişim denetimlerine](../../active-directory/active-directory-conditional-access-azure-portal.md) ek olarak, parola sıfırlama ve Multi-Factor içeren Uyarlamalı düzeltme eylemlerini otomatik olarak engelleyebilir veya sunabilir kimlik doğrulama zorlaması.

### <a name="identity-protection-capabilities"></a>Kimlik koruma özellikleri

Azure Active Directory Kimlik Koruması, bir izleme ve raporlama aracından daha fazla. Kuruluşunuzun kimliklerini korumak için, belirtilen risk düzeyine ulaşıldığında algılanan sorunlara otomatik olarak yanıt veren risk tabanlı ilkeler yapılandırabilirsiniz. Bu ilkeler, Azure Active Directory ve EMS tarafından sunulan diğer koşullu erişim denetimlerine ek olarak, parola sıfırlama ve çok faktörlü kimlik doğrulama zorlaması dahil olmak üzere Uyarlamalı düzeltme eylemlerini otomatik olarak engelleyebilir veya başlatabilir.

Azure kimlik koruması 'nın hesaplarınız ve kimliklerinizin güvenliğini sağlamaya yardımcı olabilecek bazı yollarla ilgili örnekler şunlardır:

[Risk algılamaları ve riskli hesaplar algılanıyor](../../active-directory/identity-protection/overview.md)
-   Makine öğrenimi ve buluşsal kuralları kullanarak altı riskli algılama türünü tespit edin.
-   Kullanıcı risk düzeylerini hesaplayın.
-   Güvenlik açıklarını vurgulayarak genel güvenlik duruşunu geliştirmek için özel öneriler sağlar.

[Risk algılamalarını araştırma](../../active-directory/identity-protection/overview.md)
-   Risk algılamaları için bildirimleri gönderin.
-   İlgili ve bağlamsal bilgileri kullanarak risk algılamalarını araştırın.
-   Araştırmalar izlemek için temel iş akışları sağlayın.
-   Parola sıfırlama gibi Düzeltme eylemlerine kolay erişim sağlar.

[Risk tabanlı, koşullu erişim ilkeleri](../../active-directory/identity-protection/overview.md)
-   Oturum açma işlemlerini engelleyerek veya Multi-Factor Authentication sorunlarını isteyerek riskli oturum açma işlemlerini azaltabilirsiniz.
-   Riskli Kullanıcı hesaplarını engelleyin veya güvenli hale getirin.
-   Kullanıcıların Multi-Factor Authentication 'a kaydolmesini gerektir.

### <a name="azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management

[Azure Active Directory Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md)ile kuruluşunuzdaki erişimi yönetebilir, denetleyebilir ve izleyebilirsiniz. Bu özellik, Azure AD 'deki kaynaklara ve Office 365 veya Microsoft Intune gibi diğer Microsoft çevrimiçi hizmetler erişimi içerir.

![Azure AD Privileged Identity Management diyagramı](./media/threat-detection/azure-threat-detection-fig2.png)

PıM şunları yapmanıza yardımcı olur:

-   Office 365 ve Intune gibi Microsoft çevrimiçi hizmetler için Azure AD yöneticileri ve tam zamanında (JıT) yönetim erişimi hakkında uyarı ve raporlar alın.

-   Yönetici erişim geçmişi ve yönetici atamalarındaki değişiklikler hakkında rapor alın.

-   Ayrıcalıklı bir role erişim hakkında uyarı alın.

## <a name="azure-monitor-logs"></a>Azure İzleyici günlükleri

[Azure izleyici günlükleri](../../azure-monitor/index.yml) , şirket içi ve bulut altyapınızı yönetmenize ve korumanıza yardımcı olan, Microsoft bulut tabanlı bir BT yönetimi çözümüdür. Azure Izleyici günlükleri bulut tabanlı bir hizmet olarak uygulandığından, altyapı hizmetlerinde en az yatırım ile hızlı bir şekilde çalışır duruma getirebilirsiniz. Yeni güvenlik özellikleri otomatik olarak dağıtılır, devam eden bakım ve yükseltme maliyetleri kaydedilir.

Azure Izleyici günlükleri, değerli hizmetleri kendi kendine sağlamaya ek olarak, mevcut güvenlik yönetimi yatırımlarınızı buluta genişletmek için [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/)gibi System Center bileşenleriyle tümleştirilebilir. System Center ve Azure Izleyici günlükleri, eksiksiz bir karma yönetim deneyimi sağlamak için birlikte çalışabilir.

### <a name="holistic-security-and-compliance-posture"></a>Bütünsel güvenlik ve uyumluluk sonrası

[Log Analytics güvenlik ve denetim panosu](../../security-center/security-center-intro.md) , dikkat etmeniz gereken önemli sorunlar için yerleşik arama SORGULARıYLA kuruluşunuzun BT güvenlik duruşuna kapsamlı bir görünüm sağlar. Güvenlik ve Denetim Panosu, Azure Izleyici günlüklerinde güvenlikle ilgili her şeyin giriş ekranıdır. Bu pano, size bilgisayarlarınızın güvenlik durumuyla ilgili yüksek düzeyde öngörü sağlar. Son 24 saat, 7 gün veya başka bir özel zaman dilimi içindeki tüm olayları da görüntüleyebilirsiniz.

Azure Izleyici günlükleri, yazılım güncelleştirme değerlendirmesi, kötü amaçlı yazılımdan koruma değerlendirmesi ve yapılandırma temelleri de dahil olmak üzere herhangi bir ortamın genel güvenlik duruşunu hızlı ve kolay bir şekilde anlamanıza yardımcı olur. Güvenlik günlüğü verilerine, güvenlik ve uyumluluk denetim süreçlerini kolaylaştırmak için erişilebilir.

![Log Analytics Güvenlik ve Denetim Panosu](./media/threat-detection/azure-threat-detection-fig3.jpg)

Log Analytics Güvenlik ve Denetim Pano dört ana kategoride düzenlenmiştir:

-   **Güvenlik etki alanları**: Zaman içinde güvenlik kayıtlarını daha fazla araştırmanızı sağlar; kötü amaçlı yazılım değerlendirmelerine erişin; güncelleştirme değerlendirmeleri; Ağ güvenliği, kimlik ve erişim bilgilerini görüntüleyin; güvenlik olaylarının bulunduğu bilgisayarları görüntüleme; ve hızlı bir şekilde Azure Güvenlik Merkezi panosuna erişin.

-   **Önemli sorunlar**: Etkin sorunların sayısını ve sorunların önem derecesini hızlıca tanımlamanızı sağlar.

-   **Algılamalar (Önizleme)** : , Kaynaklarınızda oluşan güvenlik uyarılarını görüntüleyerek saldırı düzenlerini tanımlamanızı sağlar.

-   **Tehdit bilgileri**: Giden kötü amaçlı IP trafiği, kötü amaçlı tehdit türü ve IP konumları haritası olan toplam sunucu sayısını görüntüleyerek saldırı düzenlerini tanımlamanızı sağlar.

-   **Ortak güvenlik sorguları**: Ortamınızı izlemek için kullanabileceğiniz en yaygın güvenlik sorgularını listeler. Herhangi bir sorgu seçtiğinizde, arama bölmesi açılır ve bu sorgunun sonuçlarını görüntüler.

### <a name="insight-and-analytics"></a>Öngörüler ve analiz
[Azure izleyici günlüklerinin](../../log-analytics/log-analytics-queries.md) merkezinde Azure tarafından barındırılan depo vardır.

![Insight ve Analytics diyagramı](./media/threat-detection/azure-threat-detection-fig4.png)

Veri kaynaklarını yapılandırarak ve aboneliğinize çözümler ekleyerek bağlı kaynaklardan depoya veri toplayabilirsiniz.

![Azure Izleyici Günlükler panosu](./media/threat-detection/azure-threat-detection-fig5.png)

Veri kaynakları ve çözümleri, her biri kendi özellikler kümesiyle ayrı kayıt türleri oluşturur, ancak bunları depoya sorgularda yine de analiz edebilirsiniz. Çeşitli kaynaklarla toplanan çeşitli verilerle çalışmak için aynı araçları ve yöntemleri kullanabilirsiniz.


Azure Izleyici günlükleri ile etkileşimlerinizin çoğu, herhangi bir tarayıcıda çalışan Azure portal ve toplanan verileri çözümlemek ve üzerinde işlem yapmak için yapılandırma ayarlarına ve birden çok araca erişmenizi sağlar. Portalından şunları kullanabilirsiniz:
* Toplanan verileri çözümlemek için sorgular oluşturduğunuz yerde [günlük aramaları](../../log-analytics/log-analytics-queries.md) .
* En değerli aramalarınızın grafik görünümleriyle özelleştirebileceğiniz [panolar](../../azure-monitor/learn/tutorial-logs-dashboards.md).
* Ek işlevsellik ve analiz araçları sağlayan [çözümler](../../monitoring/monitoring-solutions.md).

![Analiz araçları](./media/threat-detection/azure-threat-detection-fig6.png)

Çözümler Azure Izleyici günlüklerine işlevsellik ekler. Bunlar öncelikle bulutta çalışır ve Log Analytics deposunda toplanan verilerin analizini sağlar. Çözümler ayrıca, günlük aramalarıyla çözümlenebilecek yeni kayıt türlerini veya çözümün Log Analytics panosunda sağladığı ek bir kullanıcı arabirimini kullanarak da tanımlayabilir.

Güvenlik ve Denetim Panosu, bu tür çözümlerin bir örneğidir.

### <a name="automation-and-control-alert-on-security-configuration-drifts"></a>Otomasyon ve denetim: Güvenlik Yapılandırması Drifts uyarı

Azure Otomasyonu, PowerShell 'i temel alan ve bulutta çalışan runbook 'larla yönetim işlemlerini otomatikleştirir. Runbook'lar yerel kaynakların yönetilmesi için yerel veri merkezinizdeki bir sunucuda da yürütülebilir. Azure Otomasyonu, PowerShell Istenen durum yapılandırması (DSC) ile yapılandırma yönetimi sağlar.

![Azure Otomasyonu diyagramı](./media/threat-detection/azure-threat-detection-fig7.png)

Azure 'da barındırılan DSC kaynaklarını oluşturabilir ve yönetebilir ve bunları bulut ve şirket içi sistemlere uygulayabilirsiniz. Bunu yaparak yapılandırmalarını tanımlayabilir ve otomatik olarak uygulayabilir ya da güvenlik yapılandırmalarının ilke içinde kalmasını sağlamaya yardımcı olmak için, DRFT üzerinde raporlar alabilir.

## <a name="azure-security-center"></a>Azure Güvenlik Merkezi

Azure Güvenlik Merkezi, Azure kaynaklarınızı korumanıza yardımcı olur. Azure abonelikleriniz genelinde tümleşik güvenlik izleme ve ilke yönetimi sağlar. Hizmet içinde, daha fazla ayrıntı elde etmek için ilkeleri hem Azure abonelikleriniz hem de [kaynak gruplarınız](../../azure-resource-manager/manage-resources-portal.md) için tanımlayabilirsiniz.

![Azure Güvenlik Merkezi diyagramı](./media/threat-detection/azure-threat-detection-fig8.png)

Microsoft güvenlik araştırmacıları sürekli olarak tehditleri araştırmaktadır. Bunlar Microsoft’un bulut ve şirket içindeki genel varlığından edinilen kapsamlı bir telemetri kümesine erişebilmektedir. Geniş kapsamlı ve çeşitlilik barındıran bu veri kümeleri Microsoft’un yeni saldırı modellerini ve şirket içi müşteri ve kuruluş ürünlerinin yanı sıra çevrimiçi hizmetleri üzerindeki eğilimleri keşfetmesini sağlamaktadır.

Bu nedenle, Güvenlik Merkezi, saldırganlar yeni ve giderek daha fazla gelişmiş açıklardan yararlanan algılama algoritmalarını hızla güncelleştirebilir. Bu yaklaşım, hızlı hareket eden bir tehdit ortamıyla hızınızı korumanıza yardımcı olur.

![Güvenlik Merkezi tehdit algılama](./media/threat-detection/azure-threat-detection-fig9.jpg)

Güvenlik Merkezi tehdit algılaması Azure kaynaklarınızdan, ağınızdan ve bağlı iş ortağı çözümlerinden güvenlik verilerini otomatik olarak toplayarak çalışır. Tehditleri belirlemek için, bu bilgileri, birden fazla kaynaktaki bilgileri bir ilişkilendirme ile analiz eder.

Güvenlik uyarıları, Güvenlik Merkezi’nde tehdidin nasıl düzeltileceğine ilişkin önerilerle birlikte öncelik sırasına koyulur.

Güvenlik Merkezi, imza tabanlı yaklaşımların ötesine geçen gelişmiş güvenlik analizleri kullanır. Büyük veri ve [makine öğrenimi](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) teknolojilerinde sıçramalar, tüm bulut dokusu genelinde olayları değerlendirmek için kullanılır. Gelişmiş analizler, el ile yaklaşımlar aracılığıyla belirlenmesi ve saldırıların gelişini tahmin etmek için imkansız olabilecek tehditleri algılayabilir. Bu güvenlik analizi türleri sonraki bölümlerde ele alınmıştır.

### <a name="threat-intelligence"></a>Tehdit bilgileri

Microsoft, bir dizi küresel tehdit zekası erişimine sahiptir.

Azure, Office 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes birimi (DCU) ve Microsoft Güvenlik Yanıt Merkezi (MSRC) gibi birden fazla kaynaktan telemetri akışı.

![Tehdit bilgileri bulguları](./media/threat-detection/azure-threat-detection-fig10.jpg)

Araştırmacılar ayrıca, büyük bulut hizmeti sağlayıcıları arasında paylaşılan tehdit bilgileri bilgilerini alır ve üçüncü taraflardan gelen tehdit zekası akışlarına abone olurlar. Azure Güvenlik Merkezi bilinen kötü aktörlerden gelen tehditler konusunda sizi uyarmak için bu bilgileri kullanabilir. Bazı örnekler:

-   **Makine öğrenmesinin gücünü Sağlamtık**: Azure Güvenlik Merkezi, Azure dağıtımlarınızı hedefleyen tehditleri algılamak için kullanılabilen bulut ağı etkinliğiyle ilgili büyük miktarda veriye erişebilir.

-   **Deneme yanılma algılaması**: Makine öğrenimi, Secure Shell (SSH), Uzak Masaüstü Protokolü (RDP) ve SQL bağlantı noktalarına karşı deneme yanılma saldırılarını algılamaya izin veren uzaktan erişim denemelerinin geçmiş bir modelini oluşturmak için kullanılır.

-   **Giden DDoS ve Botnet algılama**: Bulut kaynaklarını hedefleyen saldırılara yönelik yaygın bir amaç, diğer saldırıları yürütmek için bu kaynakların işlem gücünü kullanmaktır.

-   **Yeni davranış analizi sunucuları ve VM 'leri**: Bir sunucu veya sanal makinenin güvenliği tehlikeye alındıktan sonra saldırganlar, algılama, kalıcılığı sağlama ve gereksinimini gidererek güvenlik denetimleri sağlayarak bu sistemde kötü amaçlı kod yürütmeye yönelik çok çeşitli teknikler sunmaktadır.

-   **Azure SQL veritabanı tehdit algılama**: Veritabanları için olağandışı ve potansiyel olarak zararlı girişimleri belirten anormal veritabanı etkinliklerini belirleyen Azure SQL veritabanı için tehdit algılama.

### <a name="behavioral-analytics"></a>Davranış analizi

Davranış analizi, verileri analiz eden ve bilinen modeller koleksiyonuyla karşılaştıran bir tekniktir. Ancak, bu modeller basit imzalar değildir. Bunlar büyük veri kümelerine uygulanan karmaşık machine learning algoritmaları aracılığıyla belirlenir.

![Davranış analizi bulguları](./media/threat-detection/azure-threat-detection-fig11.jpg)

Desenler, uzman analistlerine göre kötü amaçlı davranışların dikkatle çözümlenmesi aracılığıyla da belirlenir. Azure Güvenlik Merkezi, sanal makine günlüklerinin, sanal ağ cihaz günlüklerinin, yapı günlüklerinin, kilitlenme dökümlerinin ve diğer kaynakların analizine dayalı olarak güvenliği aşılmış kaynakları belirlemek için davranış analizini kullanabilir.

Ayrıca, desenler, geniş bir kampanyanın destek kanıtını denetlemek için diğer sinyallerle bağıntılı bir şekilde yapılır. Bu bağıntı yerleşik tehlike göstergeleriyle tutarlı olayları tanımlamaya yardımcı olur.

Bazı örnekler:
-   **Şüpheli işlem yürütme**: Saldırganlar, algılama olmadan kötü amaçlı yazılım yürütmek için çeşitli teknikler sağlar. Örneğin, bir saldırgan kötü amaçlı yazılıma yasal sistem dosyalarıyla aynı adı verebilir, ancak bu dosyaları alternatif bir konuma yerleştirebilir, bir zararsız dosyası ile benzer bir ad kullanabilir ya da dosyanın gerçek uzantısını maskeleyebilir. Güvenlik Merkezi modelleri süreç davranışlarını ve bu gibi aykırı değerleri tespit etmek için işlem yürütmelerini izler.

-   **Gizli kötü amaçlı yazılım ve yararlanma denemeleri**: Karmaşık kötü amaçlı yazılım, diskte depolanan yazılım bileşenlerini hiçbir şekilde yazmadan veya şifreleyerek geleneksel kötü amaçlı yazılımdan koruma ürünleri oluşturabilir. Bununla birlikte, kötü amaçlı yazılımın izleme için bellekte yer bırakması gerektiğinden, bu tür kötü amaçlı yazılımlar bellek analizi kullanılarak algılanabilir. Yazılım kilitlendiğinde bir kilitlenme dökümü kilitlenme sırasında belleğin bir kısmını yakalar. Azure Güvenlik Merkezi, kilitlenme dökümündeki belleği analiz ederek, yazılım içindeki güvenlik açıklarını kötüye kullanma, gizli verilere erişme ve güvenli hale getirilebilen bir makine dahilinde, Makin.

-   **Yan yana hareket ve iç keşif**: Saldırganlar, güvenliği tehlikeye giren bir ağda kalıcı hale getirmek ve değerli verileri bulmak ve toplamak için, saldırganlar genellikle güvenliği aşılmış makineden aynı ağ içindeki diğer kullanıcılara geçici olarak geçiş yapmayı dener. Güvenlik Merkezi, uzak komut yürütme, ağ yoklama ve hesap listeleme gibi bir saldırganın ağ içindeki Saldırganlarını genişletme girişimlerini saptamak için işlem ve oturum açma etkinliklerini izler.

-   **Kötü amaçlı PowerShell betikleri**: PowerShell, saldırganlar tarafından çeşitli amaçlarla hedef sanal makinelerde kötü amaçlı kod yürütmek için kullanılabilir. Güvenlik Merkezi şüpheli etkinliklerin kanıtı için PowerShell etkinliğini inceler.

-   **Giden saldırılar**: Saldırganlar genellikle bulut kaynaklarını ek saldırıları bağlamak üzere bu kaynakları kullanma amacını hedefler. Örneğin, güvenliği aşılmış sanal makineler, diğer sanal makinelere karşı deneme yanılma saldırılarını başlatmak, istenmeyen posta göndermek veya açık bağlantı noktalarını ve diğer cihazları taramak için kullanılabilir. Ağ trafiğine machine learning uygulayan Güvenlik Merkezi giden ağ iletişimlerinin normu aştığını algılayabilir. İstenmeyen posta algılandığında, Güvenlik Merkezi, e-postanın büyük olasılıkla veya yasal bir e-posta kampanyasının sonucu olup olmadığını anlamak üzere Office 365 ile alışılmadık e-posta trafiğini da ilişkilendirir.

### <a name="anomaly-detection"></a>Anomali algılama

Azure Güvenlik Merkezi, tehditleri tanımlamak için anormallik algılamayı da kullanır. Davranış analizinden (büyük veri kümelerinden türetilmiş bilinen modellere bağlıdır) farklı olarak anormallik algılama daha fazla “kişiselleştirilmiştir” ve dağıtımlarınıza özel taban çizgilerine odaklanır. Dağıtımlarınızın normal etkinliklerini belirlemek için makine öğrenimi uygulanır ve ardından güvenlik olayını temsil eden aykırı değer koşullarını tanımlamak için kurallar oluşturulur. Örnek aşağıda verilmiştir:

-   **Gelen RDP/SSH deneme yanılma saldırıları**: Dağıtımlarınız, her gün ve az sayıda oturum açma durumunda olan diğer sanal makineler için çok sayıda oturum açma ile sanal makinelere meşgul olabilir. Azure Güvenlik Merkezi, bu sanal makineler için temel oturum açma etkinliğini belirleyebilir ve makine öğrenimini kullanarak normal oturum açma etkinliklerini tanımlayabilir. Oturum açma ile ilgili özellikler için tanımlanan taban çizgisiyle bir tutarsızlık varsa, bir uyarı oluşturulabilir. Yine machine learning neyin önemli olduğunu belirler.

### <a name="continuous-threat-intelligence-monitoring"></a>Sürekli tehdit bilgisi izleme

Azure Güvenlik Merkezi, tehdit yatadaki değişiklikleri sürekli olarak izleyen dünya genelinde güvenlik araştırması ve veri bilimi ekipleriyle çalışır. Buna aşağıdaki girişimler dahildir:

-   **Tehdit bilgileri izleme**: Tehdit bilgileri, mevcut veya ortaya çıkan tehditler hakkında mekanizmalar, göstergeler, etkiler ve eyleme dönüştürülebilir öneriler içerir. Bu bilgiler güvenlik topluluğunda paylaşılır ve Microsoft, iç ve dış kaynaklardan gelen tehdit bilgileri akışlarını sürekli olarak izler.

-   **Sinyal paylaşımı**: Bulut ve şirket içi hizmetler, sunucular ve istemci uç noktası cihazlarının geniş Microsoft portföyündeki güvenlik ekiplerinden Öngörüler paylaşılır ve çözümlenir.

-   **Microsoft güvenlik uzmanları**: Microsoft 'un, adli ve Web saldırısı algılaması gibi özelleştirilmiş güvenlik alanlarında çalışan ekiplerle sürekli katılım.

-   **Algılama ayarı**: Algoritmalar gerçek müşteri veri kümelerine karşı çalıştırılır ve güvenlik araştırmacıları, sonuçları doğrulamak için müşterilerle birlikte çalışır. Doğru ve yanlış pozitifler kullanılarak machine learning algoritmaları iyileştirilir.

Bu birleştirilmiş çabalar, anında yararlanabilmeniz için yeni ve geliştirilmiş algılamalara sahiptir. Yapmanız gereken bir işlem yok.

## <a name="advanced-threat-detection-features-other-azure-services"></a>Gelişmiş tehdit algılama özellikleri: Diğer Azure hizmetleri

### <a name="virtual-machines-microsoft-antimalware"></a>Sanal makineler: Microsoft kötü amaçlı yazılımdan koruma

Azure için [Microsoft kötü amaçlı yazılımdan koruma](antimalware.md) , uygulamalar ve kiracı ortamları için, insan müdahalesi olmadan arka planda çalışacak şekilde tasarlanan tek bir aracı çözümüdür. Kötü amaçlı yazılımdan koruma izleme de dahil olmak üzere temel güvenli veya gelişmiş özel yapılandırma ile uygulama iş yüklerinizin ihtiyaçlarına göre koruma dağıtabilirsiniz. Azure kötü amaçlı yazılımdan koruma, Azure sanal makineleri için tüm Azure PaaS sanal makinelerine otomatik olarak yüklenen bir güvenlik seçeneğidir.

#### <a name="microsoft-antimalware-core-features"></a>Microsoft kötü amaçlı yazılımdan koruma temel özellikleri

Azure 'un uygulamalarınız için Microsoft Antimalware 'i dağıtan ve etkinleştiren özellikleri şunlardır:

-   **Gerçek zamanlı koruma**: Kötü amaçlı yazılım yürütme ve engelleme için bulut hizmetlerindeki ve sanal makinelerdeki etkinlikleri izler.

-   **Zamanlanmış tarama**: , Etkin olarak çalışan programlar da dahil olmak üzere, kötü amaçlı yazılımları algılamak için hedeflenen taramayı

-   **Kötü amaçlı yazılım düzeltmesi**: , Kötü amaçlı dosyaları silme veya karantinaya alma ve kötü amaçlı kayıt defteri girişlerini temizleme gibi algılanan kötü amaçlı yazılımlara otomatik olarak davranır.

-   **İmza güncelleştirmeleri**: , Korumanın önceden belirlenmiş bir sıklıkta güncel olduğundan emin olmak için en son koruma imzalarını (virüs tanımları) otomatik olarak kurar.

-   **Antimalware Engine güncelleştirmeler**: , Microsoft Antimalware Engine otomatik olarak güncelleştirir.

-   **Kötü amaçlı yazılımdan koruma platformu güncelleştirmeleri**: , Microsoft kötü amaçlı yazılımdan koruma platformunu otomatik olarak güncelleştirir.

-   **Etkin koruma**: , Microsoft etkin koruma sistemi aracılığıyla gerçek zamanlı zaman uyumlu imza teslimi sağlayan, gelişen tehdit yatakına hızlı yanıt sağlamak için Microsoft Azure algılanan tehditler ve şüpheli kaynaklarla ilgili telemetri meta verilerini raporlar.

-   **Örnek raporlama**: Hizmeti iyileştirmenize ve sorun gidermeye yardımcı olmak için Microsoft kötü amaçlı yazılımdan koruma hizmeti 'ne örnekler sağlar ve rapor verir.

-   **Dışlamaları**: Uygulama ve hizmet yöneticilerinin belirli dosya, işlem ve sürücüleri korumadan dışlama ve performans ve diğer nedenlerle tarama için yapılandırmasına izin verir.

-   **Kötü amaçlı yazılımdan koruma olayı koleksiyonu**: İşletim sistemi olay günlüğünde gerçekleştirilen kötü amaçlı yazılımdan koruma hizmeti durumunu, şüpheli etkinlikleri ve düzeltme eylemlerini kaydeder ve bunları müşterinin Azure depolama hesabına toplar.

### <a name="azure-sql-database-threat-detection"></a>Azure SQL veritabanı tehdit algılama

[Azure SQL veritabanı tehdit algılama](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) , Azure SQL veritabanı hizmetinde yerleşik olarak bulunan yeni bir güvenlik zekası özelliğidir. Azure SQL veritabanı tehdit algılama, anormal veritabanı etkinliklerini öğrenmek, profil ve algılamak için saat içinde çalışarak, veritabanı için olası tehditleri tanımlar.

Güvenlik ofisleri veya diğer belirlenen yöneticiler, oluşan şüpheli veritabanı etkinlikleri hakkında anında bildirim alabilir. Her bildirim şüpheli etkinliğin ayrıntılarını sağlar ve tehdidi nasıl daha fazla araştırıp azaltılacağını önerir.

Şu anda Azure SQL veritabanı tehdit algılama, olası güvenlik açıklarını ve SQL ekleme saldırılarını ve anormal veritabanı erişim düzenlerini algılar.

Tehdit algılama e-posta bildirimi alındığında, kullanıcılar postadaki ayrıntılı bir bağlantı aracılığıyla ilgili denetim kayıtlarını gezinebiliyor ve görüntüleyebilir. Bu bağlantı bir denetim görüntüleyicisini veya şüpheli olay süresi etrafında ilgili denetim kayıtlarını aşağıdakilere göre gösteren önceden yapılandırılmış bir denetim Excel şablonu açar:

-   Anormal veritabanı etkinlikleriyle veritabanı/sunucu için depolama alanını denetleyin.

-   Denetim günlüğünü yazmak için olay sırasında kullanılan ilgili denetim depolama tablosu.

-   Olay oluşumunda hemen sonraki saatin denetim kayıtları.

-   Olay sırasında benzer bir olay KIMLIĞI ile kayıtları denetleme (bazı detektörler için isteğe bağlı).

SQL veritabanı tehdit algılayıcıları aşağıdaki algılama yöntemlerden birini kullanır:

-   **Belirleyici algılama**: Bilinen saldırılardan eşleşen SQL istemci sorgularında şüpheli desenleri (kurallar tabanlı) algılar. Bu metodolojide yüksek algılama ve düşük yanlış pozitif, ancak sınırlı kapsam, "atomik algılamalar" kategorisinde yer aldığından sınırlıdır.

-   **Davranış algılama**: Veritabanında en son 30 gün içinde görülmeyen olağan dışı davranışlar olan anormal etkinlikleri algılar. SQL istemci anomus etkinliğine örnek olarak, başarısız oturum açma işlemleri veya sorgular, ayıklanan yüksek miktarda veri hacmi, alışılmadık kurallı sorgular veya veritabanına erişmek için bilinmeyen IP adresleri olabilir.

### <a name="application-gateway-web-application-firewall"></a>Application Gateway Web uygulaması güvenlik duvarı

[Web uygulaması güvenlik duvarı (WAF)](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md) , standart [uygulama teslim denetimi](https://kemptechnologies.com/in/application-delivery-controllers) işlevleri için bir Application Gateway kullanan Web uygulamalarına koruma sağlayan bir [Azure Application Gateway](../../application-gateway/application-gateway-web-application-firewall-overview.md) özelliğidir. Web uygulaması güvenlik duvarı, bunu [Açık Web uygulaması güvenlik projesi (OWASP) en çok 10 genel Web güvenlik açığına](https://www.owasp.org/index.php/Top_10_2010-Main)karşı koruyarak yapar.

![Application Gateway Web uygulaması güvenlik duvarı diyagramı](./media/threat-detection/azure-threat-detection-fig13.png)

Korumalar şunları içerir:

-   SQL ekleme koruması.

-   Çapraz site betik koruması.

-   Komut ekleme, HTTP isteği atılama, HTTP yanıtı bölme ve uzak dosya ekleme saldırısı gibi yaygın web saldırıları koruması.

-   HTTP protokol ihlallerine karşı koruma.

-   Eksik konak Kullanıcı Aracısı ve kabul başlıkları gibi HTTP protokol anormallara karşı koruma.

-   Botlar, gezginler ve tarayıcılarla karşı korunma.

-   Yaygın uygulama yapılandırması hataları (Apache, IIS vb.) tespit edilir.

Uygulama ağ geçidinizdeki WAF 'yi yapılandırmak aşağıdaki avantajları sağlar:

-   Web uygulamalarınızı, arka uç kodunu değiştirmeden Web güvenlik açıklarına ve saldırılarına karşı korur.

-   Birden çok Web uygulamasını bir uygulama ağ geçidinin arkasında aynı anda korur. Uygulama ağ geçidi, 20 ' ye kadar Web sitesini barındırmayı destekler.

-   Application Gateway WAF günlükleri tarafından oluşturulan gerçek zamanlı raporları kullanarak Web uygulamalarını saldırılara karşı izler.

-   Uyumluluk gereksinimlerini karşılamaya yardımcı olur. Belirli uyumluluk denetimleri, internet 'e yönelik tüm uç noktaların bir WAF çözümü tarafından korunmasını gerektirir.

### <a name="anomaly-detection-api-built-with-azure-machine-learning"></a>Anomali algılama API 'SI: Azure Machine Learning ile oluşturuldu

Anomali algılama API 'si, zaman serisi verilerinizde çeşitli anormal desenleri algılamada yararlı olan bir API 'dir. API, uyarı oluşturmak, panolar aracılığıyla izlemek veya bilet oluşturma sistemlerinizle bağlantı oluşturmak için kullanılabilecek zaman serisindeki her bir veri noktasına bir anomali puanı atar.

[Anomali algılama API 'si](../../machine-learning/team-data-science-process/apps-anomaly-detection-api.md) , zaman serisi verilerinde aşağıdaki tür bozukluklar algılayabilir:

-   **Ani artışlar ve DIB 'ler**: Bir hizmette oturum açma hatalarının sayısını veya bir e-ticaret sitesinde kullanıma alma sayısını izlerken, olağan dışı artışlar veya DIB 'ler güvenlik saldırılarını veya hizmet kesintilerini gösterebilir.

-   **Pozitif ve olumsuz eğilimler**: Bellek kullanımını bilgi işlem sırasında izlerken boş bellek boyutunu küçültme olası bir bellek sızıntısı olduğunu gösterir. Hizmet sırası uzunluğu izleme için, kalıcı bir yukarı doğru eğilim, temeldeki bir yazılım sorununa işaret edebilir.

-   **Dinamik değer aralığındaki düzey değişiklikleri ve değişiklikler**: Bir hizmet yükseltmesinden sonra bir hizmetin gecikme süreleriyle ilgili düzey değişiklikleri veya yükseltmeden sonra bir özel durum düzeyinden daha düşük olması, izlemenin ilgi çekici olması olabilir.

Machine Learning tabanlı API şunları sunar:

-   **Esnek ve sağlam algılama**: Anomali algılama modelleri, kullanıcıların duyarlık ayarlarını yapılandırmalarına ve mevsimler ve mevsimsiz veri kümeleri arasındaki anormallikleri algılamasına olanak tanır. Kullanıcılar, algılama API 'sini gereksinimlerine göre daha az veya daha hassas hale getirmek için anomali algılama modelini ayarlayabilir. Bu, dönemsel desenler olmadan ve olmayan verilerde daha az veya daha fazla görünür bozukluklar tespit ediyor demektir.

-   **Ölçeklenebilir ve zamanında algılama**: Uzmanların etki alanı bilgisi tarafından ayarlanan mevcut eşiklerle izlemenin geleneksel bir yolu pahalıdır ve dinamik olarak değişen milyonlarca veri kümesine ölçeklenebilir değildir. Bu API 'deki anomali algılama modelleri öğrenilir ve modeller hem geçmiş hem de gerçek zamanlı verilerden otomatik olarak ayarlanır.

-   **Proaktif ve**eyleme dönüştürülebilir algılama: Yavaş eğilim ve düzey değişiklik algılama, erken anomali algılama için uygulanabilir. Algılanan erken olağan dışı sinyaller, insanların sorun alanlarının araştırılması ve üzerinde işlem yapması için kullanılabilir. Buna ek olarak, kök neden analiz modelleri ve uyarı araçları bu anomali algılama API hizmetinin üzerine geliştirilebilir.

Anomali algılama API 'SI, hizmet durumu ve KPI izleme, IoT, performans izleme ve ağ trafiği izleme gibi çok çeşitli senaryolar için etkili ve verimli bir çözümdür. Bu API 'nin yararlı olabilecek bazı popüler senaryolar aşağıda verilmiştir:

- BT departmanlarının olayları, hata kodunu, kullanım günlüğünü ve performansı (CPU, bellek vb.) zamanında izlemek için araçlara ihtiyacı vardır.

-   Çevrimiçi ticaret siteleri, müşteri etkinliklerini, sayfa görünümlerini, tıklamalarını vb. izlemek ister.

-   Yardımcı program şirketleri su, gaz, elektrik ve diğer kaynakların tüketimini izlemek ister.

-   Tesis veya yapı Yönetim Hizmetleri, sıcaklık, Moisture, trafik vb. izlemek istiyor.

-   IoT/Üreticiler iş akışını, kaliteyi ve benzerlerini izlemek için zaman serisinde algılayıcı verilerini kullanmak ister.

-   Çağrı merkezleri, hizmet talep eğilimi, olay hacmi, bekleme sırası uzunluğu vb. gibi hizmet sağlayıcılarının izlenmesi gerekir.

-   İş Analizi grupları, iş KPI 'Larını (örn. satış hacmi, müşteri duymaları veya fiyatlandırma) olağan dışı hareketleri gerçek zamanlı olarak izlemek istiyor.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) , Microsoft bulut güvenlik yığınının kritik bir bileşenidir. Bu, bulut uygulamalarının taahhüdünden tam olarak yararlanabilmeniz için kuruluşunuz hareket etmenize yardımcı olabilecek kapsamlı bir çözümdür. Bu, daha fazla etkinlik halinde geliştirilmiş görünürlük aracılığıyla denetim içinde kalır. Ayrıca, bulut uygulamaları genelinde kritik veri korumasını artırmaya da yardımcı olur.

Kuruluşunuz, gölge BT’yi ortaya çıkarmaya, riskleri değerlendirmeye, ilkeleri zorunlu tutmaya, etkinlikleri araştırmaya ve tehditleri durdurmaya yardımcı olan araçlarla kritik verilerin denetimini elde tutarak buluta daha güvenli bir şekilde geçebilir.

| | |
|---|---|
| Keşif | Cloud App Security ile gölgeyi açın. Bulut ortamınızdaki uygulamaları, etkinlikleri, kullanıcıları, verileri ve dosyaları bularak görünürlük elde edin. Buluta bağlı üçüncü taraf uygulamaları bulun.|
|Araştırma | Bulutta riskli uygulamalar, belirli kullanıcılar ve ağınızdaki dosyaları ayrıntılı bir şekilde incelemek için Cloud adli araçlarını kullanarak bulut uygulamalarınızı araştırın. Buluttan toplanan verilerdeki desenleri bulun. Bulutunuzu izlemek için raporlar oluşturun. |
| Denetim | Ağ bulut trafiği üzerinde en yüksek denetim düzeyine ulaşmak için ilkeler ve uyarılar ayarlayarak riski azaltın. Kullanıcılarınızı güvenli, tasdikli bulut uygulaması alternatiflerine geçirmek için Cloud App Security kullanın. |
| koruma | Uygulamaları tasdik etmek veya önlemek, veri kaybı engellemesini zorlamak, izinleri ve paylaşımı denetlemek ve özel raporlar ve uyarılar oluşturmak için Cloud App Security kullanın. |
| Denetim | Ağ bulut trafiği üzerinde en yüksek denetim düzeyine ulaşmak için ilkeler ve uyarılar ayarlayarak riski azaltın. Kullanıcılarınızı güvenli, tasdikli bulut uygulaması alternatiflerine geçirmek için Cloud App Security kullanın. |
| | |


![Cloud App Security diyagramı](./media/threat-detection/azure-threat-detection-fig14.png)

Cloud App Security, görünürlüğünü bulutla tümleştirerek şu şekilde tümleşir:

-   Bulut ortamınızı ve kuruluşunuzun kullandığı bulut uygulamalarını eşlemek ve tanımlamak için Cloud Discovery kullanma.

-   Bulutta uygulamaları tasdikleme ve uygulama

-   , Bağlandığınız uygulamaların görünürlüğü ve idaresi için sağlayıcı API 'Lerinden yararlanan, dağıtımı kolay uygulama bağlayıcılarını kullanma.

-   ' İ ayarlayarak sürekli denetim sağlamanıza ve sonra sürekli olarak ince ayar yapmanıza yardımcı olur.

Bu kaynaklardan veri toplamada Cloud App Security, üzerinde gelişmiş analizler çalıştırır. Bu, sizi anında etkinliklere karşı uyarır ve bulut ortamınız için ayrıntılı görünürlük sağlar. Cloud App Security bir ilkeyi yapılandırabilir ve bulut ortamınızdaki her şeyi korumak için kullanabilirsiniz.

## <a name="third-party-advanced-threat-detection-capabilities-through-the-azure-marketplace"></a>Azure Marketi aracılığıyla üçüncü taraf Gelişmiş tehdit algılama özellikleri

### <a name="web-application-firewall"></a>Web Uygulaması Güvenlik Duvarı

Web uygulaması güvenlik duvarı gelen Web trafiğini inceler ve SQL eklemelerini, siteler arası betikleri, kötü amaçlı yazılım yüklemelerini, uygulama DDoS saldırılarını ve Web uygulamalarınıza hedeflenmiş diğer saldırıları engeller. Ayrıca, arka uç Web sunucularından gelen ve veri kaybı önleme (DLP) için yanıtları inceler. Tümleşik Erişim denetimi altyapısı, yöneticilerin güçlü kimlik doğrulaması ve Kullanıcı denetimi sağlayan kimlik doğrulama, yetkilendirme ve hesaplama (AAA) için ayrıntılı erişim denetimi ilkeleri oluşturmalarına olanak tanır.

Web uygulaması güvenlik duvarı aşağıdaki avantajları sağlar:

-   Uygulamanıza karşı SQL 'leri, siteler arası betikleri, kötü amaçlı yazılım yüklemelerini, uygulama DDoS 'yi veya diğer saldırıları algılar ve engeller.

-   Kimlik doğrulama ve erişim denetimi.

-   Hassas verileri algılamak için giden trafiği tarar ve bilgilerin sızmasını maskeleyebilir ya da engelleyebilir.

-   Önbelleğe alma, sıkıştırma ve diğer trafik iyileştirmeleri gibi özellikleri kullanarak Web uygulaması içeriklerinin teslimini hızlandırır.

Azure Marketi 'nde bulunan Web uygulaması güvenlik duvarları örnekleri için bkz. [barırcuda WAF, brokar sanal Web uygulaması güvenlik duvarı (vWAF), Rpva SecureSphere ve ThreatSTOP IP Firewall](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf).

## <a name="next-steps"></a>Sonraki adımlar

- [Bugünün tehditlerine yanıt verme](../../security-center/security-center-alerts-overview.md#respond-threats): Azure kaynaklarınızı hedefleyen ve hızlı yanıt vermesi gereken öngörüleri sağlayan etkin tehditleri belirlemenize yardımcı olur.

- [Azure SQL veritabanı tehdit algılama](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/): Veritabanlarınıza yönelik olası tehditler hakkındaki kaygılarınızı ele almanıza yardımcı olur.
