---
title: Azure Güvenlik Merkezi nedir?| Microsoft Docs
description: Bu sayfa, güvenlik merkezi 'nin temel avantajlarını açıklar. güvenlik durumunuzun keşfedilmesinin yanı sıra bulut ve şirket içi kaynakların kapsamı ile geliştiriyoruz.
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
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 741cd68145b262c1f200ced9a7f28b25673b6925
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738928"
---
# <a name="what-is-azure-security-center"></a>Azure Güvenlik Merkezi nedir?

Azure Güvenlik Merkezi, veri merkezlerinizin güvenlik duruşunu güçlendirir ve Azure 'da olup olmadıkları ve şirket içi iş yükleriniz arasında gelişmiş tehdit koruması sağlayan Birleşik bir altyapı güvenliği yönetim sistemidir.

Kaynaklarınızın güvende tutulması, bulut sağlayıcınız, Azure ve siz müşteri arasındaki bir birleşme çabadır. Buluta taşıdığınız sırada iş yüklerinizin güvende olduğundan emin olmanız gerekir ve aynı zamanda IaaS 'ye (hizmet olarak altyapı) geçiş yaparken PaaS (hizmet olarak platform) ve SaaS (hizmet olarak yazılım) ' de bulunandan daha fazla müşteri sorumluluğu vardır. Azure Güvenlik Merkezi, ağınızı güvenli hale getirmek, hizmetlerinizin güvenliğini sağlamak ve güvenlik duruşunuzu en üstünde olduğunuzdan emin olmak için gereken araçları sağlar.

Azure Güvenlik Merkezi en acil üç güvenlik sorununu giderir:

-   **İş yüklerini hızla değiştirme** : Bu, bulutun gücü ve bir zorluktur. Bir yandan, son kullanıcıların daha fazlasını yapma yetkisi vardır. Diğer yandan, insanların kullandığı ve oluşturduğu sürekli değişen hizmetlerin güvenlik standartlarınıza uygun olduğundan ve en iyi güvenlik deneyimlerine uyduğundan nasıl emin olabilirsiniz?

-   **Giderek daha karmaşık olan saldırılar** - İş yüklerinizi çalıştırdığınız her yerde saldırılar daha karmaşık hale gelmektedir. Aslında İnternete yönelik bir iş yükü olan ve en iyi güvenlik deneyimlerini takip etmemeniz halinde sizi daha da savunmasız bırakabilecek genel bulut iş yüklerinizi güvenli hale getirmeniz zorunludur.

-   **Güvenlik becerileri az bulunur** - Ortamlarınızın korunduğundan emin olmak için, güvenlik uyarıları ile uyarı sistemleri sayısının, gerekli arka plan ve deneyime sahip yönetici sayısından çok daha fazla olması gerekir. En son saldırılardan haberdar olmak her zaman yaşanan bir sorundur ve güvenlik dünyası sürekli değişen bir cephe olduğu sürece yerinde kalmayı imkansız hale getirir.

Bu güçlüklere karşı kendinizi korumanıza yardımcı olmak için, Güvenlik Merkezi aşağıdakileri sağlayan araçlar sunar:

-   **Güvenliği güçlendirin**: Güvenlik Merkezi ortamınıza değerlendirir ve kaynaklarınızın durumunu anlamanıza ve güvenli olup olmadığınıza olanak sağlar.

-   **Tehditlere karşı koruma**: güvenlik merkezi iş yüklerinizi değerlendirir ve tehdit önleme önerilerini ve güvenlik uyarılarını yükseltir.

-   **Daha hızlı güvenlik sağlama**: Güvenlik Merkezi'nde her şey bulut hızında yapılır. Yerel olarak tümleşik olduğundan, Güvenlik Merkezi dağıtımı kolaydır ve Azure hizmetleriyle otomatik sağlama ve koruma olanağı tanır.

> [!NOTE]
> Bu hizmet, müşterilerin Temsilcili abonelikleri ve kaynak gruplarını yönetmek için hizmet sağlayıcılarının kendi kiracılarında oturum açmasını sağlayan [Azure Mathouse](../lighthouse/overview.md)hizmetini destekler. Azure Güvenlik Merkezi senaryolarında, bir aboneliğin ayrı kaynak grupları yerine temsilci seçilmiş olması gerekir.

## <a name="architecture"></a>Mimari

Güvenlik Merkezi Azure 'un yerel olarak bir parçası olduğundan, Azure 'da Service Fabric, SQL veritabanı, SQL yönetilen örneği ve depolama hesapları dahil olmak üzere PaaS hizmetleri, tasarımda hiçbir dağıtım olmadan Güvenlik Merkezi tarafından izlenir ve korunur.

Ayrıca, Güvenlik Merkezi, Azure dışı sunucuları ve bulutta veya şirket içinde bulunan sanal makineleri, hem Windows hem de Linux sunucuları için Log Analytics aracıyı yükleyerek korur. Azure sanal makineleri, güvenlik merkezi 'nde otomatik olarak sağlanır.

Aracılardan ve Azure 'dan toplanan olaylar, iş yüklerinizin güvende olduğundan ve güvenlik uyarılarında emin olmak için izlemeniz gereken özel öneriler (sağlamlaştırma görevleri) sağlamak için Güvenlik analizi altyapısına bağıntılı. Kötü amaçlı saldırıların iş yüklerinizde gerçekleşmediklerinden emin olmak için bu uyarıları mümkün olan en kısa sürede araştırmanız gerekir.

Güvenlik Merkezi 'ni etkinleştirdiğinizde güvenlik merkezi 'nde yerleşik güvenlik ilkesi, güvenlik merkezi kategorisi altında yerleşik bir girişim olarak Azure Ilkesine yansıtılır. Yerleşik girişim, tüm güvenlik merkezi kayıtlı aboneliklerine otomatik olarak atanır (Azure Defender 'ın etkin olup olmamasından bağımsız olarak). Yerleşik girişim yalnızca denetim ilkelerini içerir. Azure Ilkesindeki Güvenlik Merkezi ilkeleri hakkında daha fazla bilgi için bkz. [güvenlik Ilkeleriyle çalışma](tutorial-security-policy.md).

## <a name="strengthen-security-posture"></a>Güvenlik duruşunu güçlendirme

Azure Güvenlik Merkezi, güvenlik duruşunuzu güçlendirin. Bu, en iyi güvenlik uygulamaları olarak önerilen sağlamlaştırma görevlerini belirleyip gerçekleştirmenize ve bunları makinelerinize, veri hizmetlerine ve uygulamalarınıza uygulamanıza yardımcı olur. Bu, güvenlik ilkelerinizi yönetmeyi ve zorunlu tutmayı ve Azure sanal makinelerinizin, Azure olmayan sunucularınızın ve Azure PaaS hizmetlerinin uyumlu olduğundan emin olmanızı içerir. Güvenlik Merkezi, iş yüklerinizde bir kuşbakışı görünümü sağlamak için ihtiyacınız olan araçları sağlar. Bu araçlar, ağ güvenliği konusunda odaklanmış görünürlük sağlar. 

### <a name="manage-organization-security-policy-and-compliance"></a>Kuruluş güvenlik ilkesini ve uyumluluğunu yönetme

Bu bir güvenlik temel olanıdır ve iş yüklerinizin güvende olduğundan emin olur ve özel güvenlik ilkelerine sahip olmaya başlar. Güvenlik Merkezi 'ndeki tüm ilkeler Azure Ilke denetimleri üzerine oluşturulduğundan, birinci **sınıf bir ilke çözümünün** tam aralığını ve esnekliğini elde edersiniz. Güvenlik Merkezi 'nde ilkelerinize yönetim gruplarında, aboneliklerde ve hatta tüm kiracı için çalışacak şekilde ayarlayabilirsiniz.

:::image type="content" source="./media/security-center-intro/sc-dashboard.png" alt-text="İlke yönetimi sayfası":::

Güvenlik Merkezi, **Gölge BT aboneliklerini belirlemenize** yardımcı olur. Panonuzda **kapsanmayan** etiketli aboneliklere bakarak, yeni oluşturulan abonelikler olduğunda hemen haberdar edebilir ve ilkeleriniz kapsamında olduklarından ve Azure Güvenlik Merkezi tarafından korunduğunuzdan emin olabilirsiniz.

:::image type="content" source="./media/security-center-intro/sc-policy-dashboard.png" alt-text="Güvenlik Merkezi ilke panosu":::

### <a name="continuous-assessments"></a>Sürekli değerlendirmeler

Güvenlik Merkezi, iş yükleriniz genelinde dağıtılan yeni kaynakları sürekli olarak bulur ve bunların en iyi güvenlik uygulamalarına göre yapılandırılıp yapılandırılmadığını değerlendirir, bunlar işaretlenir ve makinelerinizi korumak için neleri çözmeniz gerektiği hakkında önerilerin öncelikli bir listesini alırsınız. Bu öneriler listesi, genel uyumluluk çerçevelerine dayalı olarak güvenlik ve uyum en iyi uygulamaları için Microsoft tarafından yazılan ve Azure 'a özgü bir dizi kılavuz olan [Azure Güvenlik kıyaslaması](https://docs.microsoft.com/security/benchmark/azure/introduction)tarafından etkinleştirilir ve desteklenir. Bu, yaygın olarak kullanılan kıyaslama, [Internet güvenliği (CIS) Için merkezden](https://www.cisecurity.org/benchmark/azure/) ve [ulusal standartlar ve teknolojı Enstitüsü (NIST)](https://www.nist.gov/) ile bulut merkezli güvenliğe odaklanarak yapılar oluşturur.

Güvenlik Merkezi, her bir önerinin genel güvenlik durinize ne kadar önemli olduğunu anlamanıza yardımcı olmak için önerileri güvenlik denetimlerine gruplandırır ve her denetime **güvenli bir puan** değeri ekler. Bu, **güvenlik çalışmalarınızı önceliklendirmenize olanak tanımak** için önemlidir.

:::image type="content" source="./media/security-center-intro/sc-secure-score.png" alt-text="Güvenlik Merkezi güvenli puanı":::

### <a name="network-map"></a>Ağ eşlemesi

En güçlü araçlardan biri olan Güvenlik Merkezi, ağınızın güvenlik durumunu sürekli olarak izlemek için tarafından sağlanan **ağ haritadır**. Eşleme, iş yüklerinizin topolojisini görmenizi sağlar, böylece her bir düğümün düzgün şekilde yapılandırılıp yapılandırılmadığını görebilirsiniz. Düğümlerinizin nasıl bağlandığını görebilirsiniz. Bu, bir saldırganın ağınızı daha kolay bir şekilde yayılmasını sağlamak için istenmeyen bağlantıları engellemenize yardımcı olur.

:::image type="content" source="./media/security-center-intro/sc-net-map.png" alt-text="Güvenlik Merkezi ağ haritası":::


### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>Önerilen denetimleri yapılandırarak güvenliği iyileştirin ve iyileştirin

Azure Güvenlik Merkezi 'nin değerinin kalbi, önerilerinde yer alıyor. Öneriler, iş yüklerinizde bulunan belirli güvenlik kaygılarına göre uyarlanmıştır ve Güvenlik Merkezi Güvenlik Yöneticisi sizin için yalnızca güvenlik açıklarını bulamayıp sizin için çalışır, ancak bunlardan nasıl kurtuleceğiyle ilgili özel yönergeler sağlar.

Bu şekilde, güvenlik merkezi yalnızca güvenlik ilkelerini ayarlamanıza, ancak kaynaklarınızın tamamında güvenli yapılandırma standartları uygulamanıza olanak sağlar.

Öneriler, kaynaklarınızın her birinde saldırı yüzeyini azaltmanıza yardımcı olur. Bu, Azure sanal makineleri, Azure olmayan sunucular ve SQL ve depolama hesapları gibi Azure PaaS hizmetlerini ve her kaynak türünün farklı şekilde değerlendirileceğini ve kendi standartlarına sahip olduğunu içerir.

:::image type="content" source="./media/security-center-intro/sc-recommendation-example.png" alt-text="Güvenlik Merkezi öneri örneği":::

## <a name="protect-against-threats"></a>Tehditlere karşı koruma sağlama

Güvenlik Merkezi 'nin tehdit koruması, hizmet olarak altyapı (IaaS) katmanı, Azure dışı sunucuların yanı sıra Azure 'da hizmet olarak platformlar (PaaS) için tehditleri algılamanıza ve engellemenizi sağlar.

Güvenlik Merkezi 'nin tehdit koruması, bir saldırı kampanyasının tam hikayesini, nerede başlatıldığını ve kaynaklarınızda ne tür etkileri olduğunu daha iyi anlamanıza yardımcı olmak amacıyla, ortamınızdaki uyarıları otomatik olarak, siber sonlandırma zinciri analizini temel alan Fusion Kill zinciri analizini içerir.

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure Güvenlik Merkezi 'nin güvenlik uyarıları listesi":::

### <a name="integration-with-microsoft-defender-for-endpoint"></a>Uç nokta için Microsoft Defender ile tümleştirme

Sunucular için Azure Defender, uç nokta için Microsoft Defender ile otomatik, yerel tümleştirme içerir. Daha fazla bilgi edinin, [Güvenlik Merkezi 'nin tümleşik EDR çözümü ile uç noktalarınızı koruyun: Endpoint Için Microsoft Defender](security-center-wdatp.md)


### <a name="protect-paas"></a>PaaS 'yi koruma

Güvenlik Merkezi, Azure PaaS hizmetleri genelinde tehditleri algılamanıza yardımcı olur. Azure hizmetlerini hedefleyen Azure App Service, Azure SQL, Azure depolama hesabı ve daha fazla veri hizmeti dahil tehditleri tespit edebilirsiniz. Ayrıca, Azure etkinlik günlüklerinizin anomali algılama işlemini gerçekleştirmek için Microsoft Cloud App Security Kullanıcı ve varlık davranış analizi (UEBA) ile yerel tümleştirmeden yararlanabilirsiniz.

### <a name="block-brute-force-attacks"></a>Deneme yanılma saldırılarını engelle

Güvenlik Merkezi, deneme yanılma saldırıları ile olan pozlamayı sınırlamanıza yardımcı olur. Tam zamanında VM erişimini kullanarak sanal makine bağlantı noktalarına erişimi azaltarak, gereksiz erişimi engellemek için ağınızı kullanabilirsiniz. Yalnızca yetkili kullanıcılar, izin verilen kaynak IP adresi aralıkları veya IP adresleri ve sınırlı bir süre için, seçilen bağlantı noktalarında güvenli erişim ilkeleri ayarlayabilirsiniz.

### <a name="protect-data-services"></a>Veri hizmetlerini koruma

Güvenlik Merkezi, Azure SQL 'de verilerinizin otomatik sınıflandırmasını gerçekleştirmenize yardımcı olan özellikleri içerir. Ayrıca Azure SQL ve depolama hizmetleri 'ndeki olası güvenlik açıklarına yönelik değerlendirmeler alabilir ve bunların nasıl azaltılacağını önerirsiniz.

## <a name="get-secure-faster"></a>Daha hızlı bir şekilde güvende edin

Yerel Azure tümleştirmesi (Azure Ilkesi ve Azure Izleyici günlükleri dahil), Microsoft Cloud App Security ve uç nokta için Microsoft Defender gibi diğer Microsoft güvenlik çözümleriyle sorunsuz tümleştirme ile birleştirilir

Ayrıca, Azure 'un ötesinde diğer bulutlarda ve şirket içi veri merkezlerinde çalışan iş yüklerine kadar tam çözümü genişletebilirsiniz.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Azure kaynaklarını otomatik olarak bulma ve ekleme

Güvenlik Merkezi, Azure ve Azure kaynaklarıyla sorunsuz, yerel bir tümleştirme sağlar. Diğer bir deyişle, Azure Ilkesi ve tüm Azure kaynaklarınız genelinde yerleşik güvenlik merkezi ilkeleri içeren eksiksiz bir güvenlik hikayesini çekebilir ve bu kaynakları Azure 'da oluştururken yeni bulunan kaynaklara otomatik olarak uygulandığından emin olabilirsiniz.

Kapsamlı günlük koleksiyonu-Windows ve Linux 'daki Günlükler, güvenlik Analizi altyapısında yararlanılabilir ve öneriler ve uyarılar oluşturmak için kullanılır.

## <a name="next-steps"></a>Sonraki adımlar

- Güvenlik Merkezi ile çalışmaya başlamak için bir Microsoft Azure aboneliğinizin olması gerekir. Aboneliğiniz yoksa [ücretsiz deneme sürümü](https://azure.microsoft.com/free/) için kaydolabilirsiniz.

- İlk kez Azure portal Azure Güvenlik Merkezi panosunu ziyaret ettiğinizde veya API aracılığıyla programlı olarak etkinleştirilmişse, güvenlik merkezi 'nin ücretsiz fiyatlandırma katmanı tüm mevcut Azure aboneliklerinizde etkindir. Gelişmiş güvenlik yönetimi ve tehdit algılama özellikleri avantajlarından yararlanmak için Azure Defender 'ı etkinleştirmeniz gerekir. Azure Defender, 30 gün boyunca ücretsiz olarak denenmelidir. Daha fazla bilgi için [Güvenlik Merkezi fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/security-center/) bakın.

- Azure Defender 'ı şimdi etkinleştirmeye hazırsanız [hızlı başlangıç: Azure Güvenlik Merkezi 'Nin kurulması](security-center-get-started.md) , adımlarda size yol gösterir.