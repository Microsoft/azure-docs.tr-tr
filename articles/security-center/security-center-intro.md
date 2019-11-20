---
title: Azure Güvenlik Merkezi nedir?| Microsoft Docs
description: Azure Güvenlik Merkezi, önemli işlevleri ve nasıl çalıştığı hakkında bilgi edinin.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2019
ms.author: memildin
ms.openlocfilehash: e02fd0de00b8feed15fcd51b657afcc015d816f2
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202099"
---
# <a name="what-is-azure-security-center"></a>Azure Güvenlik Merkezi nedir?

Azure Güvenlik Merkezi, veri merkezlerinizin güvenlik duruşunu güçlendirir ve bulutta karma iş yükleriniz genelinde gelişmiş tehdit koruması sağlar ve bunların Azure 'da olup olmadığı ve ayrıca Şirket içinde.

Kaynaklarınızın güvende tutulması, bulut sağlayıcınız, Azure ve siz müşteri arasındaki bir birleşme çabadır. Buluta taşıdığınız sırada iş yüklerinizin güvende olduğundan emin olmanız gerekir ve aynı zamanda IaaS 'ye (hizmet olarak altyapı) geçiş yaparken PaaS (hizmet olarak platform) ve SaaS (hizmet olarak yazılım) ' de bulunandan daha fazla müşteri sorumluluğu vardır. Azure Güvenlik Merkezi, ağınızı güvenli hale getirmek, hizmetlerinizin güvenliğini sağlamak ve güvenlik duruşunuzu en üstünde olduğunuzdan emin olmak için gereken araçları sağlar.

Azure Güvenlik Merkezi, en çok üç acil güvenlik sorunlarını giderir:

-   **İş yüklerini hızla değiştirme** : Bu, bulutun gücü ve bir zorluktur. Tek bir yandan, son kullanıcıların daha fazla bilgi almak için güç vardır. Diğer bir deyişle, herkesin kullandığı ve oluşturmakta olduğu sürekli değişen hizmetlerin güvenlik standartlarınıza kadar ve en iyi güvenlik uygulamalarını takip ettiğinizden nasıl emin olabilirsiniz?

-   **Giderek daha karmaşık saldırılar** -iş yüklerinizi çalıştırdığınız her yerde saldırı daha karmaşık hale getirmeye devam edersiniz. En iyi güvenlik uygulamalarını takip etmezseniz, daha savunmasız bırakabilen Internet 'e yönelik bir iş yükünüz olan genel bulut iş yüklerinizi güvenli hale getirebilirsiniz.

-   **Güvenlik becerileri kısa bir arz içinde** , ortamların korunduğundan emin olmak için gerekli arka plan ve deneyimle yönetici sayısını ve uyarı sistemi sayısını en az bir şekilde numaralandırır. En son saldırılarla güncel kalmak, güvenlikle ilgili bir ön değişiklik olduğu sürece sürekli bir zorluk sergilemektir.

Bu güçlüklere karşı kendinizi korumanıza yardımcı olmak için, güvenlik merkezi şunları yapmak üzere araçlar sağlar:

-   **Güvenlik sonrası güvenliği güçlendir**: Güvenlik Merkezi ortamınızı değerlendirir ve kaynaklarınızın durumunu anlamanıza ve güvenli olup olmadıkları konusunda bilgi sağlamanıza olanak sağlar.

-   **Tehditlere karşı koruma**: Güvenlik Merkezi, iş yüklerinizi değerlendirir ve tehdit önleme önerileri ve tehdit algılama uyarıları oluşturur.

-   **Daha hızlı**sağlayın: Güvenlik Merkezi 'nde her şey bulut hızında yapılır. Yerel olarak tümleşik olduğundan, güvenlik merkezi 'nin dağıtımı kolay hale getirilmiştir ve Azure hizmetleri ile kolayca sağlama ve koruma sağlar.

## <a name="architecture"></a>Mimari

Güvenlik Merkezi Azure 'un yerel olarak bir parçası olduğundan, Azure 'da Service Fabric, SQL veritabanları ve depolama hesapları dahil olmak üzere PaaS hizmetleri, tasarımda hiçbir dağıtım olmadan Güvenlik Merkezi tarafından izlenir ve korunur.

Ayrıca, Güvenlik Merkezi, Azure dışı sunucuları ve bulutta veya Şirket içindeki sanal makineleri, Microsoft Monitoring Agent yükleyerek hem Windows hem de Linux sunucuları için korur. Azure sanal makineleri, güvenlik merkezi 'nde otomatik olarak sağlanır.

Aracılardan ve Azure 'dan toplanan olaylar, iş yüklerinizin güvende olduğundan ve tehdit algılama uyarılarından emin olmak için izlemeniz gereken özel öneriler (sağlamlaştırma görevleri) sağlamak üzere güvenlik Analizi altyapısına bağıntılı. Kötü amaçlı saldırıların iş yüklerinizde gerçekleşmediklerinden emin olmak için bu uyarıları mümkün olan en kısa sürede araştırmanız gerekir.

Güvenlik Merkezi 'ni etkinleştirdiğinizde güvenlik merkezi 'nde yerleşik güvenlik ilkesi, güvenlik merkezi kategorisi altında yerleşik bir girişim olarak Azure Ilkesine yansıtılır. Yerleşik girişim, tüm güvenlik merkezi kayıtlı aboneliklerine (ücretsiz veya standart katmanlara) otomatik olarak atanır. Yerleşik girişim yalnızca denetim ilkelerini içerir. Azure Ilkesindeki Güvenlik Merkezi ilkeleri hakkında daha fazla bilgi için bkz. [güvenlik Ilkeleriyle çalışma](tutorial-security-policy.md).

## <a name="strengthen-security-posture"></a>Güvenlik duruşunu güçlendirme

Azure Güvenlik Merkezi, güvenlik duruşunuzu güçlendirin. Bu, en iyi güvenlik uygulamaları olarak önerilen sağlamlaştırma görevlerini belirleyip gerçekleştirmenize ve bunları makinelerinize, veri hizmetlerine ve uygulamalarınıza uygulamanıza yardımcı olur. Bu, güvenlik ilkelerinizi yönetmeyi ve zorunlu tutmayı ve Azure sanal makinelerinizin, Azure olmayan sunucularınızın ve Azure PaaS hizmetlerinin uyumlu olduğundan emin olmanızı içerir. Güvenlik Merkezi, iş yüklerinizde bir kuşbakışı görünümü sağlamak için ihtiyacınız olan araçları sağlar. Bu araçlar, ağ güvenliği konusunda odaklanmış görünürlük sağlar. 

### <a name="manage-organization-security-policy-and-compliance"></a>Kuruluş güvenlik ilkesini ve uyumluluğunu yönetme

Bu bir güvenlik temel olanıdır ve iş yüklerinizin güvende olduğundan emin olur ve özel güvenlik ilkelerine sahip olmaya başlar. Güvenlik Merkezi 'ndeki tüm ilkeler Azure ilke denetimleri üzerine oluşturulduğundan, birinci **sınıf bir ilke çözümünün**tam aralığını ve esnekliğini elde edersiniz. Güvenlik Merkezi 'nde ilkelerinize yönetim gruplarında, aboneliklerde ve hatta tüm kiracı için çalışacak şekilde ayarlayabilirsiniz.

![Güvenlik Merkezi panosu](media/security-center-intro/sc-dashboard.png)

Güvenlik Merkezi, **Gölge BT aboneliklerini belirlemenize**yardımcı olur. Panonuzda **kapsanmayan** etiketli aboneliklere bakarak, yeni oluşturulan abonelikler olduğunda hemen haberdar edebilir ve ilkeleriniz kapsamında olduklarından ve Azure Güvenlik Merkezi tarafından korunduğunuzdan emin olabilirsiniz.

![Güvenlik Merkezi ilke panosu](media/security-center-intro/sc-policy-dashboard.png)

Güvenlik Merkezi 'ndeki Gelişmiş izleme özellikleri, **zaman içinde uyumluluğu ve idare izlemenizi ve yönetmenizi**sağlar. **Genel uyumluluk** size, aboneliklerinizin iş yüklerinizle ilişkili ilkelerle ne kadar uyumlu olduğunu size sağlar. 

![Zamana göre güvenlik merkezi ilkesi](media/security-center-intro/sc-policy-time.png)

### <a name="continuous-assessments"></a>Sürekli değerlendirmeler

Güvenlik Merkezi, iş yükleriniz genelinde dağıtılan yeni kaynakları sürekli olarak bulur ve bunların en iyi güvenlik uygulamalarına göre yapılandırılıp yapılandırılmadığını değerlendirir, bunlar bayrak eklenmiş olup makinelerinizi korumak için ' i çözmeniz gerekir.

En güçlü araçlardan biri olan Güvenlik Merkezi, ağınızın güvenlik durumunu sürekli olarak izlemek için tarafından sağlanan **ağ haritadır**. Eşleme, iş yüklerinizin topolojisini görmenizi sağlar, böylece her bir düğümün düzgün şekilde yapılandırılıp yapılandırılmadığını görebilirsiniz. Düğümlerinizin nasıl bağlandığını görebilirsiniz. Bu, bir saldırganın ağınızı daha kolay bir şekilde yayılmasını sağlamak için istenmeyen bağlantıları engellemenize yardımcı olur.

![Güvenlik Merkezi ağ haritası](media/security-center-intro/sc-net-map.png)

Güvenlik Merkezi, **güvenli bir puan**ekleyerek güvenlik uyarılarınızı bir adım daha kolay hale getirir. Güvenli puanlar, her bir önerinin genel güvenlik duruşumıza ne kadar önemli olduğunu anlamanıza yardımcı olmak için, aldığınız her öneriye göre ilişkilendirilir. Bu, **güvenlik çalışmalarınızı önceliklendirmenize olanak tanımak**için önemlidir.

![Güvenlik Merkezi güvenli puanı](media/security-center-intro/sc-secure-score.png)

### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>Önerilen denetimleri yapılandırarak güvenliği iyileştirin ve iyileştirin

Azure Güvenlik Merkezi 'nin değerinin kalbi, önerilerinde yer alıyor. Öneriler, iş yüklerinizde bulunan belirli güvenlik kaygılarına göre uyarlanmıştır ve Güvenlik Merkezi Güvenlik Yöneticisi sizin için yalnızca güvenlik açıklarını bulamayıp sizin için çalışır, ancak bunlardan nasıl kurtuleceğiyle ilgili özel yönergeler sağlar.

![Güvenlik Merkezi önerileri](media/security-center-intro/sc-recommendations.png)

Bu şekilde, güvenlik merkezi yalnızca güvenlik ilkelerini ayarlamanıza, ancak kaynaklarınızın tamamında güvenli yapılandırma standartları uygulamanıza olanak sağlar.

Öneriler, kaynaklarınızın her birinde saldırı yüzeyini azaltmanıza yardımcı olur. Bu, Azure sanal makineleri, Azure olmayan sunucular ve SQL ve depolama hesapları gibi Azure PaaS hizmetlerini ve her kaynak türünün farklı şekilde değerlendirileceğini ve kendi standartlarına sahip olduğunu içerir.

![Güvenlik Merkezi öneri örneği](media/security-center-intro/sc-recommendation-example.png)

## <a name="protect-against-threats"></a>Tehditlere karşı koruma sağlayın

Güvenlik Merkezi 'nin tehdit koruması, hizmet olarak altyapı (IaaS) katmanı, Azure dışı sunucuların yanı sıra Azure 'da hizmet olarak platformlar (PaaS) için tehditleri algılamanıza ve engellemenizi sağlar.

Güvenlik Merkezi 'nin tehdit koruması, bir saldırı kampanyasının tam metnini ve nerede başlatıldığını ve ne yaptığını daha iyi anlamanıza yardımcı olmak amacıyla, ortamınızdaki uyarıları otomatik olarak, siber sonlandırma zinciri analizini temel alan Fusion Kill Zinciri Analizi içerir. kaynaklarınızda bulunan etki türü.



![Güvenlik Merkezi saldırı önerisi](media/security-center-intro/sc-attack-recommendation.png)

### <a name="advanced-threat-protection"></a>Gelişmiş tehdit koruması

Güvenlik Merkezi ile, Windows Defender Gelişmiş tehdit koruması ile yerel tümleştirmeyi kullanıma hazır olursunuz. Bu, herhangi bir yapılandırma olmadan Windows sanal makinelerinizin ve sunucularınızın güvenlik merkezi 'nin önerileri ve değerlendirmeleri ile tam olarak tümleştirildiği anlamına gelir. Gelişmiş tehdit algılama, Linux sanal makineleri ve sunucuları için kutudan de sunulur.

Ayrıca, Güvenlik Merkezi, sunucu ortamlarında uygulama denetim ilkelerini otomatikleştirmenize imkan tanır. Güvenlik Merkezi 'ndeki Uyarlamalı uygulama denetimleri, Windows sunucularınız genelinde uçtan uca uygulama beyaz listesini etkinleştirir. Kurallar oluşturmanız ve ihlalleri denetlemeniz gerekmez, bu işlem sizin için otomatik olarak yapılır.

### <a name="protect-paas"></a>PaaS 'yi koruma

Güvenlik Merkezi, Azure PaaS hizmetleri genelinde tehditleri algılamanıza yardımcı olur. Azure hizmetlerini hedefleyen Azure App Service, Azure SQL, Azure depolama hesabı ve daha fazla veri hizmeti dahil tehditleri tespit edebilirsiniz. Ayrıca, Azure etkinlik günlüklerinizin anomali algılama işlemini gerçekleştirmek için Microsoft Cloud App Security Kullanıcı ve varlık davranış analizi (UEBA) ile yerel tümleştirmeden yararlanabilirsiniz.

### <a name="block-brute-force-attacks"></a>Deneme yanılma saldırılarını engelle

Güvenlik Merkezi, deneme yanılma saldırıları ile olan pozlamayı sınırlamanıza yardımcı olur. Tam zamanında VM erişimini kullanarak sanal makine bağlantı noktalarına erişimi azaltarak, gereksiz erişimi engellemek için ağınızı kullanabilirsiniz. Yalnızca yetkili kullanıcılar, izin verilen kaynak IP adresi aralıkları veya IP adresleri ve sınırlı bir süre için, seçilen bağlantı noktalarında güvenli erişim ilkeleri ayarlayabilirsiniz.

![Güvenlik Merkezi deneme yanılma](media/security-center-intro/sc-brute-force.png)

### <a name="protect-data-services"></a>Veri hizmetlerini koruma

Güvenlik Merkezi, Azure SQL 'de verilerinizin otomatik sınıflandırmasını gerçekleştirmenize yardımcı olan özellikleri içerir. Ayrıca Azure SQL ve depolama hizmetleri 'ndeki olası güvenlik açıklarına yönelik değerlendirmeler alabilir ve bunların nasıl azaltılacağını önerirsiniz.

### <a name="protect-iot-and-hybrid-cloud-workloads"></a>IoT ve hibrit bulut iş yüklerini koruma

IoT için Azure Güvenlik Merkezi (Nesnelerin İnterneti) karma görünürlük ve denetim, uyarlamalı tehdit önleme ve akıllı tehdit algılama ve şirket içi, şirket içinde çalışan iş yükleri genelinde Azure ve diğer bulutlarda. Daha fazla bilgi için bkz. [IoT Için Azure Güvenlik Merkezi](https://docs.microsoft.com/azure/asc-for-iot/).

## <a name="get-secure-faster"></a>Daha hızlı bir şekilde güvende edin

Yerel Azure tümleştirmesi (Azure Ilkesi ve Azure Izleyici günlükleri dahil), Microsoft Cloud App Security ve Windows Defender Gelişmiş tehdit koruması gibi diğer Microsoft güvenlik çözümleriyle sorunsuz tümleştirme ile birleştirilmiştir güvenlik çözümünün yanı sıra, kolayca kullanıma sunulmasına ve kullanıma sunulmasına yönelik kapsamlı bir işlemdir.

Ayrıca, Azure 'un ötesinde diğer bulutlarda ve şirket içi veri merkezlerinde çalışan iş yüklerine kadar tam çözümü genişletebilirsiniz.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Azure kaynaklarını otomatik olarak bulma ve ekleme

Güvenlik Merkezi, Azure ve Azure kaynaklarıyla sorunsuz, yerel bir tümleştirme sağlar. Diğer bir deyişle, Azure Ilkesi ve tüm Azure kaynaklarınız genelinde yerleşik güvenlik merkezi ilkeleri içeren eksiksiz bir güvenlik hikayesini çekebilir ve tüm bu kaynakları oluştururken yeni bulunan kaynaklara otomatik olarak uygulandığından emin olabilirsiniz Azure 'da.

Kapsamlı günlük koleksiyonu-Windows ve Linux 'daki Günlükler, güvenlik Analizi altyapısında yararlanılabilir ve öneriler ve uyarılar oluşturmak için kullanılır.

## <a name="next-steps"></a>Sonraki adımlar

- Güvenlik Merkezi ile çalışmaya başlamak için bir Microsoft Azure aboneliğinizin olması gerekir. Aboneliğiniz yoksa [ücretsiz deneme sürümü](https://azure.microsoft.com/free/) için kaydolabilirsiniz.
- Güvenlik Merkezi’nin Ücretsiz fiyatlandırma katmanı, Azure aboneliğiniz ile etkinleştirilir. Gelişmiş güvenlik yönetimi ve tehdit algılama yeteneklerinden yararlanmak için Standart fiyatlandırma katmanına yükseltmeniz gerekir. Standart katman ücretsiz olarak denenolabilir. Daha fazla bilgi için [Güvenlik Merkezi fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/security-center/) bakın.
- Güvenlik Merkezi standardını şimdi [etkinleştirmek için hazır olduğunuzda hızlı başlangıç: Azure aboneliğinizi Güvenlik Merkezi standardına](security-center-get-started.md) eklemek, adımlarda size yol gösterir.

