---
title: Güvenlik Merkezi planlama ve Işlemler Kılavuzu | Microsoft Docs
description: Bu belge, Azure Güvenlik Merkezi 'Ni benimseme ve günlük işlemlerle ilgili hususlar için plan yapmanıza yardımcı olur.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: abcd6dc8c50b819dd02347b938602af7f2152d04
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996627"
---
# <a name="azure-security-center-planning-and-operations-guide"></a>Azure Güvenlik Merkezi planlama ve işlemler Kılavuzu
Bu kılavuz, Azure Güvenlik Merkezi 'ni kullanmayı planlayan bilgi teknolojisi (BT) uzmanları, BT mimarları, bilgi güvenliği analistleri ve bulut yöneticileri içindir.


## <a name="planning-guide"></a>Planlama Kılavuzu
Bu kılavuzda, kuruluşunuzun güvenlik gereksinimlerine ve bulut yönetimi modeline göre güvenlik merkezi kullanımını iyileştirmek için izleyebileceğiniz görevler ele alınmaktadır. Güvenlik Merkezi 'Nden tam olarak yararlanabilmek için kuruluşunuzdaki farklı bireylerin veya takımların güvenli geliştirme ve işlemler, izleme, idare ve olay yanıtı gereksinimlerini karşılamak için hizmeti nasıl kullandığını anlamak önemlidir. Güvenlik Merkezi 'ni kullanmayı planlarken dikkate alınması gereken temel bölgeler şunlardır:

* Güvenlik rolleri ve erişim denetimleri
* Güvenlik Ilkeleri ve öneriler
* Veri toplama ve depolama
* Devam eden Azure dışı kaynaklar
* Devam eden güvenlik Izleme
* Olay yanıtı

Sonraki bölümde, bu alanlardan her birini nasıl planlayacağınızı ve gereksinimlerinize göre bu önerileri nasıl uygulayacağınızı öğreneceksiniz.


> [!NOTE]
> Tasarım ve planlama aşamasında de yararlı olabilecek yaygın soruların bir listesi için [Azure Güvenlik Merkezi sık sorulan sorular (SSS)](security-center-faq.md) makalesini okuyun.
>

## <a name="security-roles-and-access-controls"></a>Güvenlik rolleri ve erişim denetimleri
Kuruluşunuzun boyutuna ve yapısına bağlı olarak, birden çok kişi ve takımlar Güvenlik Merkezi 'ni kullanarak güvenlikle ilgili farklı görevleri gerçekleştirebilir. Aşağıdaki diyagramda, kurgusal bir kişinin ve ilgili rollerinin ve güvenlik sorumluluklarının bir örneğini bulabilirsiniz:

![Lerdir](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-new.png)

Güvenlik Merkezi bu kişilerin bu çeşitli sorumlulukları karşılamasına olanak sağlar. Örneğin:

**Jeff (Iş yükü sahibi)**

* Bulut iş yükünü ve ilgili kaynaklarını yönetme
* Şirket güvenlik ilkesine uygun olarak korumaların uygulanması ve bakımında sorumlu

**Ellen (CıSO/CIO)**

* Şirket güvenliğinin tüm yönleriyle sorumludur
* Şirket içindeki güvenlik duruşunu bulut iş yükleri genelinde anlamak istiyor
* Önemli saldırılar ve riskler hakkında bilgi sahibi olması gerekir

**David (BT güvenliği)**

* Uygun korumaların yerinde olduğundan emin olmak için şirket güvenlik ilkelerini ayarlar
* İlkelerle uyumluluğu izler
* Liderlik veya denetçiler için rapor oluşturur

**Gamze (güvenlik Işlemleri)**

* Güvenlik uyarılarını izler ve yanıtlar 24/7
* Bulut Iş yükü sahibine veya BT güvenlik analistini ilerletir

**Sam (güvenlik analisti)**

* Saldırıları araştır
* Düzeltmeyi uygulamak için bulut Iş yükü sahibiyle çalışma

Güvenlik Merkezi, Azure 'daki kullanıcılara, gruplara ve hizmetlere atanabilen [yerleşik roller](../role-based-access-control/built-in-roles.md) sağlayan [rol tabanlı Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md)kullanır. Bir Kullanıcı güvenlik merkezi 'ni açtığında yalnızca erişiminin olduğu kaynaklarla ilgili bilgileri görür. Bu, kullanıcıya bir kaynağın ait olduğu aboneliğe veya kaynak grubuna sahip, katkıda bulunan veya okuyucu rolü atanmış anlamına gelir. Bu rollere ek olarak, iki özel güvenlik merkezi rolü vardır:

- **Güvenlik okuyucusu**: Bu role ait olan bir Kullanıcı yalnızca öneriler, uyarılar, ilke ve sistem durumunu Içeren Güvenlik Merkezi yapılandırmasını görüntüleyebilir, ancak değişiklik yapamaz.
- **Güvenlik Yöneticisi**: güvenlik okuyucusu ile aynı, ancak güvenlik ilkesini güncelleştirebilir, önerileri ve uyarıları da kapatabilir.

Yukarıda açıklanan güvenlik merkezi rollerinin, Azure 'un depolama, Web & Mobile veya Nesnelerin İnterneti gibi diğer hizmet bölümlerine erişimi yoktur.

Önceki diyagramda açıklandığı gibi personbu 'yı kullanarak aşağıdaki RBAC gereklidir:

**Jeff (Iş yükü sahibi)**

* Kaynak grubu sahibi/katkıda bulunan

**David (BT güvenliği)**

* Abonelik sahibi/katkıda bulunan veya Güvenlik Yöneticisi

**Gamze (güvenlik Işlemleri)**

* Uyarıları görüntülemek için abonelik okuyucusu veya güvenlik okuyucusu
* Abonelik sahibi/katkıda bulunan veya güvenlik yöneticisi uyarıları kapatmak için gereklidir

**Sam (güvenlik analisti)**

* Uyarıları görüntülemek için abonelik okuyucusu
* Abonelik sahibi/bu katılımcı uyarıları kapatmak için gereklidir
* Çalışma alanına erişim gerekli olabilir

Göz önünde bulundurmanız gereken bazı önemli bilgiler:

* Bir güvenlik ilkesi yalnızca abonelik sahipleri/katkıda bulunanlar ve güvenlik yöneticileri tarafından düzenlenebilir.
* Yalnızca abonelik ve kaynak grubu sahipleri ve katkıda bulunanlar, bir kaynak için güvenlik önerileri uygulayabilir.

Güvenlik Merkezi için RBAC kullanarak erişim denetimini planlarken, kuruluşunuzdaki kim güvenlik merkezini kullandığınızı anladığınızdan emin olun. Ayrıca, hangi tür görevleri gerçekleştirdikleridir ve ardından RBAC 'yi uygun şekilde yapılandıracaksınız.

> [!NOTE]
> Kullanıcıların görevlerini tamamlaması için gereken en az izin veren rolü atamanızı öneririz. Örneğin, yalnızca kaynakların güvenlik durumu ile ilgili bilgileri görüntülemesi gereken ancak eylem uygulamama (öneriler veya ilkeleri Düzenle gibi) kullanıcıların okuyucu rolü atanmalıdır.
>
>

## <a name="security-policies-and-recommendations"></a>Güvenlik ilkeleri ve öneriler
Güvenlik ilkesi, iş yüklerinizin istenen yapılandırmasını tanımlar ve şirket veya mevzuat güvenlik gereksinimleriyle uyumluluğu sağlamaya yardımcı olur. Güvenlik Merkezi 'nde Azure abonelikleriniz için ilkeler tanımlayabilirsiniz. Bu, iş yükü türüne veya verilerin duyarlılığına göre ayarlanabilir.

Güvenlik Merkezi ilkeleri aşağıdaki bileşenleri içerir:
- [Veri toplama](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection): aracı sağlama ve veri toplama ayarları.
- [Güvenlik ilkesi](https://docs.microsoft.com/azure/security-center/security-center-policies): hangi denetimlerin güvenlik merkezi tarafından izleneceğini ve önerildiğini belirleyen bir [Azure ilkesidir](../governance/policy/overview.md) veya yeni tanımlar oluşturmak, ek ilkeler tanımlamak ve yönetim grupları arasında Ilkeler atamak için Azure ilkesi 'ni kullanın.
- [E-posta bildirimleri](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details): güvenlik kişileri ve bildirim ayarları.
- [Fiyatlandırma katmanı](https://docs.microsoft.com/azure/security-center/security-center-pricing): kapsamdaki kaynaklar Için hangi güvenlik merkezi özelliklerinin kullanılabildiğini belirleyen ücretsiz veya standart fiyatlandırma seçimi (abonelikler, kaynak grupları ve çalışma alanları için belirtilebilir).

> [!NOTE]
> Bir güvenlik kişisi belirtildiğinde, bir güvenlik olayı oluşursa Azure 'un kuruluşunuzdaki doğru kişiye erişebildiğinden emin olabilirsiniz. Bu öneriyi etkinleştirme hakkında daha fazla bilgi için bkz. [Azure Güvenlik Merkezi 'nde güvenlik iletişim bilgilerini sağlama](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) .

### <a name="security-policies-definitions-and-recommendations"></a>Güvenlik ilkeleri tanımları ve önerileri
Güvenlik Merkezi, Azure aboneliklerinizin her biri için otomatik olarak varsayılan bir güvenlik ilkesi oluşturur. İlkeyi Güvenlik Merkezi 'nde düzenleyebilir veya Azure Ilkesi ' ni kullanarak yeni tanımlar oluşturabilir, ek ilkeler tanımlayabilir ve Yönetim Grupları (kuruluşun tamamı, bir iş birimi vb.) ve uyumluluğu izlemek için kullanılabilir Bu ilkeler bu kapsamlar genelinde.

Güvenlik ilkelerini yapılandırmadan önce, [güvenlik önerilerini](https://docs.microsoft.com/azure/security-center/security-center-recommendations)gözden geçirin ve bu ilkelerin çeşitli abonelikleriniz ve kaynak gruplarınız için uygun olup olmadığını saptayın. Ayrıca, güvenlik önerilerini ele almak için hangi eylemin gerçekleştirilmesi gerektiğini ve yeni önerilerin izlenmesinden ve gerekli adımları gerçekleştirerek kuruluşunuzdaki kim sorumlu olacağını anlamak da önemlidir.

## <a name="data-collection-and-storage"></a>Veri toplama ve depolama
Azure Güvenlik Merkezi, Azure Izleyici hizmeti tarafından kullanılan aracı, sanal makinelerinizden güvenlik verilerini toplamak için Microsoft Monitoring Agent kullanır. Bu aracıdan [toplanan veriler](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) , Log Analytics çalışma alanınızda depolanacak.

### <a name="agent"></a>Aracı

Güvenlik ilkesinde otomatik sağlama etkinleştirildiğinde, desteklenen tüm Azure VM 'lerine ve oluşturulan tüm yeni sanal makinelere Microsoft Monitoring Agent ( [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) veya [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents)için) yüklenir. VM veya bilgisayarda zaten Microsoft Monitoring Agent yüklüyse, Azure Güvenlik Merkezi geçerli yüklü aracıyı kullanacaktır. Aracının işlemi, bir arada olmayan ve VM performansı üzerinde çok az etkiye sahip olacak şekilde tasarlanmıştır.

Windows için Microsoft Monitoring Agent, TCP bağlantı noktası 443 ' ün kullanılmasını gerektirir. Daha fazla bilgi için [sorun giderme makalesine](security-center-troubleshooting-guide.md) bakın.

Bir noktada veri toplamayı devre dışı bırakmak istiyorsanız, güvenlik ilkesinde devre dışı bırakabilirsiniz. Ancak, Microsoft Monitoring Agent diğer Azure yönetim ve izleme hizmetleri tarafından kullanılabilir olabileceğinden, güvenlik merkezi 'nde veri toplamayı kapattığınızda aracı otomatik olarak kaldırılmaz. Gerekirse aracıyı el ile kaldırabilirsiniz.

> [!NOTE]
> Desteklenen VM 'lerin listesini bulmak için, [Azure Güvenlik Merkezi sık sorulan sorular (SSS)](security-center-faq.md)makalesini okuyun.
>

### <a name="workspace"></a>Çalışma alanı

Çalışma alanı, veri için kapsayıcı görevi gören bir Azure kaynağıdır. Siz veya kuruluşunuzun diğer üyeleri, BT altyapınızın tümünün veya bölümlerinden toplanan farklı veri kümelerini yönetmek için birden çok çalışma alanı kullanabilir.

Microsoft Monitoring Agent toplanan veriler (Azure Güvenlik Merkezi adına), VM 'nin coğrafi bölge 'sini hesaba katarak Azure aboneliğinizle ilişkili mevcut bir Log Analytics çalışma alanında veya yeni çalışma alanlarında depolanır.

Azure portal, Azure Güvenlik Merkezi tarafından oluşturulanlar dahil olmak üzere Log Analytics çalışma alanlarınızın bir listesini görmek için tarama yapabilirsiniz. Yeni çalışma alanları için ilgili bir kaynak grubu oluşturulur. Her ikisi de şu adlandırma kuralını izler:

* Çalışma alanı: *Defaultworkspace-[abonelik-kimliği]-[coğrafi]*
* Kaynak grubu: *Defaultresourcegroup-[coğrafi]*

Azure Güvenlik Merkezi tarafından oluşturulan çalışma alanları için veriler 30 gün boyunca tutulur. Mevcut çalışma alanları için, bekletme çalışma alanı fiyatlandırma katmanını temel alır. İsterseniz, var olan bir çalışma alanını da kullanabilirsiniz.

> [!NOTE]
> Microsoft, bu verilerin gizlilik ve güvenliğini korumak için güçlü taahhüt sağlar. Microsoft, bir hizmeti çalıştırmak için kodlamadan sıkı uyumluluk ve güvenlik yönergelerine uyar. Veri işleme ve gizlilik hakkında daha fazla bilgi için [Azure Güvenlik Merkezi veri güvenliği](security-center-data-security.md)makalesini okuyun.
>

## <a name="onboarding-non-azure-resources"></a>Azure dışı kaynakları ekleme

Güvenlik Merkezi, Azure olmayan bilgisayarlarınızın güvenlik duruşunu izleyebilir, ancak önce bu kaynakları eklemeniz gerekir. Azure olmayan kaynakları ekleme hakkında daha fazla bilgi için [Gelişmiş güvenlik Için Azure Güvenlik Merkezi 'Ne ekleme](https://docs.microsoft.com/azure/security-center/security-center-onboarding#onboard-non-azure-computers) konusunu okuyun.

## <a name="ongoing-security-monitoring"></a>Devam eden güvenlik izleme
İlk yapılandırma ve Güvenlik Merkezi önerilerini uygulamadan sonra, bir sonraki adım Güvenlik Merkezi çalışma süreçlerini ele maktadır.

Güvenlik Merkezi 'Ne genel bakış, tüm Azure kaynaklarınızın yanı sıra bağladığınız tüm Azure dışı kaynaklarla ilgili Birleşik bir görünüm sağlar. Aşağıdaki örnekte, ele alınan birçok sorunu içeren bir ortam gösterilmektedir:

![panosunda](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig11.png)

> [!NOTE]
> Güvenlik Merkezi normal işlem yordamlarınıza müdahale etmez, dağıtımlarınızı büyük ölçüde izler ve etkinleştirdiğiniz güvenlik ilkelerine bağlı olarak öneriler sağlar.

Geçerli Azure ortamınız için Güvenlik Merkezi 'ni kullanmayı ilk kez tercih ettiğinizde, **öneriler** kutucuğunda veya kaynak başına (**işlem**, **ağ**, **Depolama & verileri) yapılabilecek tüm önerileri gözden geçirdiğinizden emin olun.** , **Uygulama**).

Tüm önerilerin adresledikten sonra, ele alınan tüm kaynaklar için **önleme** bölümünün yeşil olması gerekir. Bu noktada devam eden izleme, yalnızca kaynak güvenlik durumu ve öneriler kutucuklarında yapılan değişikliklere dayalı olarak eylemler gerçekleştirebileceğiniz için daha kolay hale gelir.

**Algılama** bölümü daha yeniden devreye girdiğinde, bunlar şimdi gerçekleşmiş olan veya geçmişte gerçekleşen ve Güvenlik Merkezi denetimleri ve 3. taraf sistemleri tarafından algılanan sorunlarla ilgili uyarılardır. Güvenlik uyarıları kutucuğunda, her gün bulunan tehdit algılama uyarılarının sayısını ve farklı önem dereceleri (düşük, orta, yüksek) arasında dağıtılması temsil eden çubuk grafikler görüntülenir. Güvenlik uyarıları hakkında daha fazla bilgi için [Azure Güvenlik Merkezi 'nde güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md)konusunu okuyun.

Günlük güvenlik işlemlerinizin bir parçası olarak [tehdit zekası](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) seçeneğini ziyaret etmeyi planlayın. Ortamda, belirli bir bilgisayarın botnet 'in bir parçası olup olmadığını belirlemek gibi güvenlik tehditlerini belirleyebilirsiniz.

### <a name="monitoring-for-new-or-changed-resources"></a>Yeni veya değiştirilmiş kaynakları izleme
Azure ortamlarının çoğu dinamik, düzenli olarak oluşturulan kaynaklar, artırma veya azaltma, yeniden yapılandırılmış ve değiştirilmiş kaynakları içeren dinamiktir. Güvenlik Merkezi, bu yeni kaynakların güvenlik durumunun görünebilirliğini sağlamanıza yardımcı olur.

Azure ortamınıza yeni kaynaklar (VM 'Ler, SQL veritabanları) eklediğinizde Güvenlik Merkezi bu kaynakları otomatik olarak bulur ve güvenliğini izlemeye başlar. Bu ayrıca PaaS Web rolleri ve çalışan rolleri içerir. [Güvenlik Ilkesinde](tutorial-security-policy.md)veri toplama etkinse, sanal makineleriniz için ek izleme özellikleri otomatik olarak etkinleştirilir.

![Anahtar bölgeler](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3-newUI.png)

1. Sanal makineler için, **kaynak güvenliği Hygiene** bölümü altında, **işlem & uygulamalar**' a tıklayın. Verilerin veya ilgili önerilerin etkinleştirilmesiyle ilgili herhangi bir sorun, **genel bakış** sekmesinde ve **izleme önerileri** bölümünde ortaya çıkmış olacaktır.
2. Varsa, yeni kaynak için güvenlik riskleri tanımlanmıştı.
3. Ortamınıza yeni VM 'Ler eklendiğinde, yalnızca işletim sisteminin başlangıçta yüklendiği oldukça yaygındır. Kaynak sahibinin, bu VM 'Ler tarafından kullanılacak diğer uygulamaların dağıtılması biraz zaman alabilir.  İdeal olarak, bu iş yükünün nihai hedefini bilmeniz gerekir. Uygulama sunucusu olacak mı? Bu yeni iş yükünün ne olduğuna bağlı olarak, bu iş akışındaki üçüncü adım olan uygun **güvenlik ilkesini**etkinleştirebilirsiniz.
4. Azure ortamınıza yeni kaynaklar eklendikçe, **güvenlik uyarıları** kutucuğunda yeni uyarılar görünebilir. Bu kutucukta yeni uyarılar olup olmadığına bakın ve önerileri izleyin.

Ayrıca, güvenlik riskleri oluşturan, önerilen taban çizgileri ve güvenlik uyarılarından oluşan yapılandırma değişiklikleri için de düzenli olarak mevcut kaynakları izlemeniz gerekir. Güvenlik Merkezi panosu ' nu başlatın. Buradan, tutarlı bir şekilde gözden geçirmeniz gereken üç ana alan vardır.

![İşlemler](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4-newUI.png)

1. **Önleme** bölüm paneli, anahtar kaynaklarınıza hızlı erişim sağlar. Işlem, ağ, depolama & verilerini ve uygulamaları izlemek için bu seçeneği kullanın.
2. **Öneriler** paneli, güvenlik merkezi önerilerini incelemenizi sağlar. Devam eden izleme sırasında, ilk güvenlik merkezi kurulumunda tüm önerilere değinmeniz durumunda, bir günlük olarak önerileriniz olmadığından, bu durum normaldir. Bu nedenle, bu bölümde her gün yeni bilgilere sahip olmayabilirsiniz ve yalnızca gerektiğinde ona erişmeniz gerekecektir.
3. **Algılama** bölümü, çok sık veya çok seyrek olarak değişebilir. Güvenlik uyarılarınızı her zaman gözden geçirin ve Güvenlik Merkezi önerilerine göre eylemler gerçekleştirin.

### <a name="hardening-access-and-applications"></a>Erişimi ve uygulamaları sağlamlaştırma

Güvenlik işlemlerinizin bir parçası olarak, VM 'lere erişimi kısıtlamak ve VM 'lerde çalışan uygulamaları denetlemek için önleyici ölçüleri de benimsemelisiniz. Azure VM 'lerinize gelen trafiği kilitleyerek, saldırılara maruz kalma olasılığını azaltmakta olursunuz ve gerektiğinde VM 'lere bağlanmak için kolay erişim sağlayan aynı zamanda. Sanal makinelerinize erişimi sağlamlaştırma için [tam ZAMANıNDA VM](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) erişimi özelliğini kullanın.

Azure 'da bulunan sanal makinelerinizdeki hangi uygulamaların çalıştırılacağını sınırlamak için [Uyarlamalı uygulama denetimleri](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application) kullanabilirsiniz. Diğer avantajların yanı sıra bu, VM 'lerinizi kötü amaçlı yazılımlara karşı korumanıza yardımcı olur. Güvenlik Merkezi, makine öğrenimini kullanarak beyaz listeleme kuralları oluşturmanıza yardımcı olması için VM 'de çalışan işlemlerin dökümünü yapar.


## <a name="incident-response"></a>Olay yanıtı
Güvenlik Merkezi, gerçekleştikleri sürece tehditleri algılar ve sizi uyarır. Kuruluşlar yeni güvenlik uyarılarını izlemeli ve daha fazla araştırma yapmak veya saldırıyı düzeltmek için gerekli işlemleri yapması gerekir. Güvenlik Merkezi tehdit algılama 'nın nasıl çalıştığı hakkında daha fazla bilgi için, [Azure Güvenlik Merkezi 'nin tehditleri nasıl algıladığı ve yanıt verdiğini](security-center-alerts-overview.md#detect-threats)okuyun.

Bu makale kendi olay yanıtı planınızı oluşturmanıza yardımcı olmaya yönelik bir amaç olmasa da, bulut yaşam döngüsünde olay yanıtı aşamaları için temel olarak Microsoft Azure Güvenlik yanıtı kullanacağız. Aşamalar aşağıdaki diyagramda gösterilmiştir:

![Şüpheli etkinlik](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> Ulusal Standartlar ve Teknoloji Enstitüsü (NıST) [bilgisayar güvenliği olay Işleme kılavuzunu](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) kendi oluşturduğunuz bir başvuru olarak kullanabilirsiniz.
>

Aşağıdaki aşamalar sırasında güvenlik merkezi uyarılarını kullanabilirsiniz:

* **Algıla**: bir veya daha fazla kaynak içinde şüpheli bir etkinlik tanımla.
* **Değerlendirme**: şüpheli etkinlik hakkında daha fazla bilgi edinmek için ilk değerlendirmeyi gerçekleştirin.
* **Tanıla**: sorunu gidermeye yönelik teknik yordamı gerçekleştirmek için düzeltme adımlarını kullanın.

Her güvenlik uyarısı, saldırının doğasını daha iyi anlamak ve olası azaltmaları önermek için kullanılabilecek bilgiler sağlar. Bazı uyarılar Ayrıca daha fazla bilgiye veya Azure 'daki diğer bilgi kaynaklarına bağlantılar sağlar. Daha fazla araştırma için sağlanan bilgileri kullanabilir ve hafifletme işlemini başlatabilir ve ayrıca çalışma alanınızda saklanan güvenlikle ilgili verileri de arayabilirsiniz.

Aşağıdaki örnekte, şüpheli bir RDP etkinliği gerçekleşirken gösterilmektedir:

![Şüpheli etkinlik](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

Bu sayfada, saldırının gerçekleştiği zamanla ilgili ayrıntılar, kaynak ana bilgisayar adı, hedef VM ve ayrıca öneri adımları sunulmaktadır. Bazı durumlarda, saldırının kaynak bilgileri boş olabilir. Bu tür bir davranış hakkında daha fazla bilgi için [Azure Güvenlik Merkezi 'Nde eksik kaynak bilgilerini](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) okuyun.

Bu sayfadan, saldırının zaman çizelgesini, saldırının nasıl gerçekleşmediğini, hangi sistemlerin tehlikede olduğunu, hangi kimlik bilgilerinin kullanıldığını ve bunun bir grafik gösterimini görmek için bir [araştırma](https://docs.microsoft.com/azure/security-center/security-center-investigation) başlatabilirsiniz. saldırı zinciri.

Güvenliği aşılmış bir sistemi tanımladıktan sonra, daha önce oluşturulmuş olan güvenlik [PlayBook](https://docs.microsoft.com/azure/security-center/security-center-playbooks) 'ları çalıştırabilirsiniz. Güvenlik PlayBook, seçili uyarıdan belirli bir PlayBook tetiklendikten sonra Güvenlik Merkezi 'nden yürütülebilecek yordamların bir koleksiyonudur.

[Azure Güvenlik Merkezi 'Nden yararlanma & bir olay yanıtı videosu için Microsoft Operations Management Suite](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) , bu aşamaların her birinde güvenlik merkezi 'nin nasıl kullanılabileceğini anlamanıza yardımcı olabilecek bazı gösteriler görebilirsiniz.

> [!NOTE]
> [Azure Güvenlik Merkezi 'nde güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md) hakkında daha fazla bilgi için bkz. Güvenlik Merkezi özelliklerini kullanarak olay yanıtlama sürecinizde size yardımcı olun.
>
>

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, güvenlik merkezi benimsemeyi nasıl planlayadığınızı öğrendiniz. Güvenlik Merkezi hakkında daha fazla bilgi edinmek için aşağıdakilere bakın:

* [Azure Güvenlik Merkezi 'nde güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md)
* [Azure Güvenlik Merkezi 'Nde güvenlik sistem durumu izleme](security-center-monitoring.md) — Azure kaynaklarınızın sistem durumunu nasıl izleyeceğinizi öğrenin.
* [Azure Güvenlik Merkezi ile iş ortağı çözümlerini izleme](security-center-partner-solutions.md) -iş ortağı çözümlerinizin sistem durumunu izlemeyi öğrenin.
* [Azure Güvenlik Merkezi hakkında SSS](security-center-faq.md) — hizmeti kullanma hakkında sık sorulan soruları bulun.
* [Azure Güvenlik blogu](https://blogs.msdn.com/b/azuresecurity/) : Azure güvenliği ve uyumluluğu ile ilgili blog gönderilerini bulun.
