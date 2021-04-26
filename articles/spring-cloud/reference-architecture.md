---
ms.date: 02/16/2021
ms.topic: reference-architecture
author: kriation
title: Azure yay bulut başvurusu mimarisi
ms.author: akaleshian
ms.service: spring-cloud
description: Bu başvuru mimarisi, Azure Spring Cloud kullanımı için tipik bir kurumsal hub ve bağlı bileşen tasarımı kullanan bir temelidir.
ms.openlocfilehash: 74183ca2decf8487e5c41cf36d5784538021077f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878764"
---
# <a name="azure-spring-cloud-reference-architecture"></a>Azure yay bulut başvurusu mimarisi

Bu başvuru mimarisi, Azure Spring Cloud kullanımı için tipik bir kurumsal hub ve bağlı bileşen tasarımı kullanan bir temelidir. Tasarımda Azure yay bulutu, hub 'da barındırılan paylaşılan hizmetlere bağımlı tek bir bağlı bileşen ile dağıtılır. Mimari, [Microsoft Azure Well-Architected çerçevesinde][16]tenetleri başarmak için bileşenlerle oluşturulmuştur.

Bu mimarinin uygulanması için GitHub 'daki [Azure yay bulut başvurusu mimari][10] deposuna bakın.

Bu mimari için dağıtım seçenekleri Azure Resource Manager (ARM), Terkform ve Azure CLı içerir. Bu depodaki yapıtlar, ortamınız için özelleştirebileceğiniz bir temel sağlar. Azure Güvenlik Duvarı veya Application Gateway gibi kaynakları farklı kaynak gruplarına veya aboneliklerine gruplayabilirsiniz. Bu gruplandırma, BT altyapısı, güvenlik, iş uygulaması takımları vb. gibi farklı işlevlerin ayrı tutulmasına yardımcı olur.

## <a name="planning-the-address-space"></a>Adres alanını planlama

Azure yay bulutu iki ayrılmış alt ağ gerektirir:

* Hizmet çalışma zamanı
* Spring Boot uygulamaları

Bu alt ağların her biri adanmış bir küme gerektirir. Birden çok küme aynı alt ağları paylaşamaz. Her alt ağın en küçük boyutu/28 ' dir. Azure Spring Cloud tarafından desteklenen uygulama örneklerinin sayısı alt ağın boyutuna göre farklılık gösterir. Ayrıntılı sanal ağ (VNET) gereksinimlerini [bir sanal ağda Azure yay bulutu dağıtma][17]konusunun [sanal ağ gereksinimleri][11] bölümünde bulabilirsiniz.

> [!WARNING]
> Seçilen alt ağ boyutu var olan VNET adres alanıyla çakışamaz ve eşlenmiş veya şirket içi alt ağ adres aralıklarıyla çakışmamalıdır.

## <a name="use-cases"></a>Uygulama alanları

Bu mimarinin tipik kullanımları şunlardır:

* Karma bulut ortamlarında dağıtılan iç uygulamalar
* Dışarıdan yönelik uygulamalar

Bu kullanım örnekleri, güvenlik ve ağ trafiği kuralları dışında benzerdir. Bu mimari, her birinin nuslarını destekleyecek şekilde tasarlanmıştır.

## <a name="private-applications"></a>Özel uygulamalar

Aşağıdaki listede özel uygulamalar için altyapı gereksinimleri açıklanmaktadır. Bu gereksinimler, yüksek oranda düzenlenen ortamlarda tipik olarak yapılır.

* Denetim düzlemi trafiği dışında genel Internet 'e doğrudan çıkış yapılmaz.
* Çıkış trafiği bir merkezi ağ sanal gereci (NVA) aracılığıyla (örneğin, Azure Güvenlik Duvarı) gezinmelidir.
* Bekleyen veriler şifrelenmelidir.
* Yoldaki veriler şifrelenmelidir.
* DevOps dağıtım işlem hatları kullanılabilir (örneğin, Azure DevOps) ve Azure Spring Cloud ile ağ bağlantısı gerektirir.
* Microsoft 'un sıfır güven güvenlik yaklaşımı, parolaların, sertifikaların ve kimlik bilgilerinin güvenli bir kasada depolanmasını gerektirir. Önerilen hizmet Azure Key Vault.
* Uygulama ana bilgisayar etki alanı adı hizmeti (DNS) kayıtları Azure Özel DNS 'de depolanmalıdır.
* Şirket içindeki ve buluttaki konaklara ait ad çözümlemesi çift yönlü olmalıdır.
* En az bir güvenlik karşılaştırmalı karşılaştırmaya uyulması gerekir.
* Azure hizmet bağımlılıkları, hizmet uç noktaları veya özel bağlantı aracılığıyla iletişim kurmalıdır.
* Azure yay bulutu dağıtımı tarafından yönetilen kaynak gruplarının değiştirilmemesi gerekir.
* Azure yay bulutu dağıtımı tarafından yönetilen alt ağlar değiştirilmemelidir.
* Bir alt ağ, Azure yay bulutu 'nın yalnızca bir örneğine sahip olmalıdır.
* [Azure Spring Cloud config Server][8] , bir depodan yapılandırma özelliklerini yüklemek için kullanılıyorsa, deponun özel olması gerekir.

Aşağıdaki listede, tasarımı oluşturan bileşenler gösterilmektedir:

* Şirket içi ağı
  * Etki alanı adı hizmeti (DNS)
  * Ağ geçidi
* Hub aboneliği
  * Azure Güvenlik Duvarı alt ağı
  * Application Gateway alt ağ
  * Paylaşılan hizmetler alt ağı
* Bağlı abonelik
  * Sanal ağ eşi
  * Azure savunma alt ağı

Aşağıdaki listede, bu başvuru mimarisinde bulunan Azure hizmetleri açıklanmaktadır:

* [Azure yay bulutu][1]: Java tabanlı Spring Boot uygulamaları ve için özel olarak tasarlanan ve iyileştirilmiş yönetilen bir hizmet. NET tabanlı [Steeltoe][9] uygulamaları.

* [Azure Key Vault][2]: Microsoft Identity Services ve işlem kaynaklarıyla sıkı bir şekilde tümleştirilmesi olan, donanım tarafından desteklenen bir kimlik bilgisi yönetimi hizmeti.

* [Azure izleyici][3]: hem Azure 'da hem de şirket içinde dağıtan uygulamalar için tek bir izleme hizmeti paketi.

* [Azure Güvenlik Merkezi][4]: şirket içi, birden çok bulut ve Azure arasında iş yükleri için Birleşik bir güvenlik yönetimi ve tehdit koruması sistemi.

* [Azure Pipelines][5]: güncel bir sürekli tümleştirme/sürekli GELIŞTIRME (CI/CD) hizmeti, güncelleştirilmiş Spring Boot uygulamalarını otomatik olarak Azure Spring Cloud 'a dağıtabilecek bir hizmettir.

Aşağıdaki diyagramda, yukarıdaki gereksinimlere yönelik iyi şekilde tasarlanmış bir hub ve bağlı bileşen tasarımı temsil etmektedir:

![Özel uygulamalar için başvuru mimarisi diyagramı](./media/spring-cloud-reference-architecture/architecture-private.png)

## <a name="public-applications"></a>Ortak uygulamalar

Aşağıdaki listede, ortak uygulamalar için altyapı gereksinimleri açıklanmaktadır. Bu gereksinimler, yüksek oranda düzenlenen ortamlarda tipik olarak yapılır.

* Giriş trafiği, en az Application Gateway veya Azure ön kapısı tarafından yönetilmelidir.
* Azure DDoS koruması standardı etkinleştirilmelidir.
* Denetim düzlemi trafiği dışında genel Internet 'e doğrudan çıkış yapılmaz.
* Çıkış trafiği bir merkezi ağ sanal gereci (NVA) (örneğin, Azure Güvenlik Duvarı) çapraz geçiş yapılmalıdır.
* Bekleyen veriler şifrelenmelidir.
* Yoldaki veriler şifrelenmelidir.
* DevOps dağıtım işlem hatları kullanılabilir (örneğin, Azure DevOps) ve Azure Spring Cloud ile ağ bağlantısı gerektirir.
* Microsoft 'un sıfır güven güvenlik yaklaşımı, parolaların, sertifikaların ve kimlik bilgilerinin güvenli bir kasada depolanmasını gerektirir. Önerilen hizmet Azure Key Vault.
* Uygulama ana bilgisayarı DNS kayıtları Azure Özel DNS depolanmalıdır.
* Internet yönlendirilebilir adresleri Azure genel DNS 'de depolanmalıdır.
* Şirket içindeki ve buluttaki konaklara ait ad çözümlemesi çift yönlü olmalıdır.
* En az bir güvenlik karşılaştırmalı karşılaştırmaya uyulması gerekir.
* Azure hizmet bağımlılıkları, hizmet uç noktaları veya özel bağlantı aracılığıyla iletişim kurmalıdır.
* Azure yay bulutu dağıtımı tarafından yönetilen kaynak gruplarının değiştirilmemesi gerekir.
* Azure yay bulutu dağıtımı tarafından yönetilen alt ağlar değiştirilmemelidir.
* Bir alt ağ, Azure yay bulutu 'nın yalnızca bir örneğine sahip olmalıdır.

Aşağıdaki listede, tasarımı oluşturan bileşenler gösterilmektedir:

* Şirket içi ağı
  * Etki alanı adı hizmeti (DNS)
  * Ağ geçidi
* Hub aboneliği
  * Azure Güvenlik Duvarı alt ağı
  * Application Gateway alt ağ
  * Paylaşılan hizmetler alt ağı
* Bağlı abonelik
  * Sanal ağ eşi
  * Azure savunma alt ağı

Aşağıdaki listede, bu başvuru mimarisinde bulunan Azure hizmetleri açıklanmaktadır:

* [Azure yay bulutu][1]: Java tabanlı Spring Boot uygulamaları ve için özel olarak tasarlanan ve iyileştirilmiş yönetilen bir hizmet. NET tabanlı [Steeltoe][9] uygulamaları.

* [Azure Key Vault][2]: Microsoft Identity Services ve işlem kaynaklarıyla sıkı bir şekilde tümleştirilmesi olan, donanım tarafından desteklenen bir kimlik bilgisi yönetimi hizmeti.

* [Azure izleyici][3]: hem Azure 'da hem de şirket içinde dağıtan uygulamalar için tek bir izleme hizmeti paketi.

* [Azure Güvenlik Merkezi][4]: şirket içi, birden çok bulut ve Azure arasında iş yükleri için Birleşik bir güvenlik yönetimi ve tehdit koruması sistemi.

* [Azure Pipelines][5]: güncel bir sürekli tümleştirme/sürekli GELIŞTIRME (CI/CD) hizmeti, güncelleştirilmiş Spring Boot uygulamalarını otomatik olarak Azure Spring Cloud 'a dağıtabilecek bir hizmettir.

* [Azure Application Gateway][6]: katman 7 ' de Aktarım katmanı GÜVENLIĞI (TLS) yük boşaltma ile uygulama trafiğinden sorumlu yük dengeleyici.

* [Azure uygulama güvenlik duvarı][7]: Azure Application Gateway, yaygın güvenlik açıklarından ve güvenlik açıklarından uygulamalar için merkezi koruma sağlayan bir özelliktir.

Aşağıdaki diyagramda, yukarıdaki gereksinimlere yönelik iyi şekilde tasarlanmış bir hub ve bağlı bileşen tasarımı temsil etmektedir:

![Ortak uygulamalar için başvuru mimarisi diyagramı](./media/spring-cloud-reference-architecture/architecture-public.png)

## <a name="azure-spring-cloud-on-premises-connectivity"></a>Azure Spring Cloud şirket içi bağlantı

Azure yay bulutu 'nda çalışan uygulamalar, çeşitli Azure, şirket içi ve dış kaynaklarla iletişim kurabilir. Merkez ve bağlı bileşen tasarımını kullanarak, uygulamalar trafiği dışarıdan veya şirket içi ağ üzerinden, Express Route veya siteden siteye sanal özel ağ (VPN) kullanarak yönlendirebilir.

## <a name="azure-well-architected-framework-considerations"></a>Azure Well-Architected Framework konuları

[Azure Well-Architected Framework][16] , güçlü bir altyapı temeli oluştururken izlenecek bir dizi temel bir kümesidir. Framework şu kategorileri içerir: maliyet iyileştirme, işlemsel üstün, performans verimliliği, güvenilirlik ve güvenlik.

### <a name="cost-optimization"></a>Maliyet iyileştirmesi

Dağıtılmış sistem tasarımının doğası nedeniyle altyapı genişlemesine bir gerçeklik. Bu gerçeklik beklenmedik ve denetlenebilir maliyetlere neden olur. Azure yay bulutu, talep ve maliyeti iyileştirmek için ölçeklendirilen bileşenler kullanılarak oluşturulmuştur. Bu mimarinin çekirdeği Azure Kubernetes hizmetidir (AKS). Hizmet, Kubernetes 'in, kümenin işletimsel maliyetlerine yönelik verimlilik içeren ek yükünü azaltmak için tasarlanmıştır.

Azure yay bulutu 'nın tek bir örneğine farklı uygulamalar ve uygulama türleri dağıtabilirsiniz. Hizmet, kullanım ve maliyet verimliliğini iyileştirebilecek ölçümler veya zamanlamalar tarafından tetiklenen uygulamaların otomatik ölçeklendirmeyi destekler.

İşletimsel maliyeti düşürmek için Application Insights ve Azure Izleyici 'yi de kullanabilirsiniz. Kapsamlı günlük çözümü tarafından sunulan görünürlük sayesinde, sistemin bileşenlerini gerçek zamanlı olarak ölçeklendirmek için Otomasyon uygulayabilirsiniz. Ayrıca, sistemin genel maliyetini ve performansını geliştirmek için adresleyebileceğiniz uygulama kodunda verimsizlikleri açığa çıkarmak üzere günlük verilerini çözümleyebilirsiniz.

### <a name="operational-excellence"></a>Operasyonel Mükemmellik

Azure yay bulutu, işlemsel üstün bir çok yönüyle yöneliktir. Aşağıdaki listede açıklandığı gibi, hizmetin üretim ortamlarında verimli bir şekilde çalıştığından emin olmak için bu noktaları birleştirebilirsiniz:

* Azure Pipelines, insan hatasından kaçınmanıza yardımcı olurken dağıtımların güvenilir ve tutarlı olmasını sağlamak için kullanabilirsiniz.
* Günlük ve telemetri verilerini depolamak için Azure Izleyici 'yi ve Application Insights kullanabilirsiniz.
  Uygulamalarınızın sistem durumunu ve performansını güvence altına almak için toplanan günlük ve ölçüm verilerini değerlendirebilirsiniz. Uygulama performansı Izleme (APM), bir Java Aracısı aracılığıyla hizmetle tamamen tümleşiktir. Bu aracı, ek kod gerekmeden dağıtılan tüm uygulamalara ve bağımlılıklara yönelik görünürlük sağlar. Daha fazla bilgi için bkz. blog gönderisi [Azure Spring Cloud 'da uygulamalara ve bağımlılıklara kolay bir şekilde izleme][15].
* Sağlanan verileri analiz etmek ve değerlendirmek için bir platform sağlayarak uygulamaların güvenliği korumasını sağlamak için Azure Güvenlik Merkezi 'ni kullanabilirsiniz.
* Hizmet çeşitli dağıtım düzenlerini destekler. Daha fazla bilgi için bkz. [Azure yay bulutu 'nda hazırlama ortamı ayarlama][14].

### <a name="reliability"></a>Güvenilirlik

Azure yay bulutu, bir temel bileşen olarak AKS ile tasarlanmıştır. AKS, kümeleme aracılığıyla bir dayanıklılık düzeyi sağlarken, bu başvuru mimarisi, bileşen arızası durumunda uygulamanın kullanılabilirliğini artırmanın Hizmetleri ve mimari hususlar içerir.

İyi tanımlanmış bir hub ve bağlı bileşen tasarımının üzerine bağlanarak, bu mimarinin temelini birden çok bölgeye dağıtmanıza olanak sağlar. Özel uygulama kullanım durumu için, mimari bir coğrafi hata sırasında sürekli kullanılabilirlik sağlamak için Azure Özel DNS kullanır. Genel uygulama kullanım örneği için Azure ön kapısı ve Azure Application Gateway kullanılabilirliği güvence altına aldığından emin olun.

### <a name="security"></a>Güvenlik

Bu mimarinin güvenliği, sektör tarafından tanımlanan denetimler ve kıyaslamalar tarafından karşılanır. Bu mimarideki denetimler, bulut [güvenlik Birliği][18] (CSA) tarafından [bulut denetim matrisi][19] (CCM) ve Internet güvenliği (CIS) [için merkezi][21] tarafından kıyaslama (MAFB) [Microsoft Azure][20] . Uygulanan denetimlerde, odak, idare, ağ ve uygulama güvenliğinin birincil güvenlik tasarımı ilkelerine bağlıdır. Hedef altyapınızla bağlantılı olarak, kimlik, erişim yönetimi ve depolamanın tasarım ilkelerini işlemek sizin sorumluluğunuzdadır.

#### <a name="governance"></a>İdare

Bu mimari adresleyen en önemli idare, ağ kaynaklarının yalıtımına göre belirlenir. CCM 'de, DCS-08 veri merkezi için giriş ve çıkış denetimini öneriyor. Mimari, denetimi karşılamak için ağ güvenlik grupları (NSG 'ler) kullanan bir hub ve bağlı bileşen tasarımını, kaynaklar arasındaki Doğu Batı trafiğini filtrelemek için kullanır. Mimari Ayrıca hub 'daki Merkezi Hizmetler ve bağlı bileşen arasındaki trafiği filtreler. Mimari, Internet ve mimarideki kaynaklar arasındaki trafiği yönetmek için bir Azure Güvenlik Duvarı örneğini kullanır.

Aşağıdaki listede, bu başvurudaki veri merkezi güvenliğinin ele aldığı denetim gösterilmektedir:

| CSA CCM denetim KIMLIĞI | CSA CCM denetim etki alanı |
| :----------------- | :----------------------|
| DCS-08 | Veri Merkezi güvenlik yetkisiz kişiler girdisi |

#### <a name="network"></a>Ağ

Bu mimariyi destekleyen ağ tasarımı geleneksel hub ve bağlı bileşen modelinden türetilir. Bu karar, ağ yalıtımının temel bir yapı olmasını sağlar. CCM Control IVS-06, ağlar ve sanal makineler arasındaki trafiğin kısıtlanması ve güvenilen ve güvenilmeyen ortamlar arasında izlenmesini önerir. Bu mimari, Doğu Batı trafiği için NSG 'ler ve Kuzey-Güney trafiği için Azure Güvenlik Duvarı uygulamalarına göre denetimi benimsemektedir. CCM denetimi IPY-04, altyapının hizmetler arasında veri alışverişi için güvenli ağ protokolleri kullanmasını önerir. Bu mimariyi destekleyen Azure Hizmetleri, HTTP ve SQL için TLS gibi standart güvenli protokoller kullanır.

Aşağıdaki listede, bu başvurudaki ağ güvenliğine yönelik CCM denetimleri gösterilmektedir:

| CSA CCM denetim KIMLIĞI | CSA CCM denetim etki alanı |
| :----------------- | :----------------------|
| IPY-04             | Ağ protokolleri      |
| IVS-06             | Ağ Güvenliği       |

Ağ uygulamasının, MAFB 'den denetimler tanımlayarak daha da güvenli hale getirilir. Denetimler, ortama gelen trafiğin genel Internet 'ten kısıtlanmasını güvence altına aldığından emin olur.

Aşağıdaki listede, bu başvurudaki ağ güvenliğine yönelik olan CIS denetimleri gösterilmektedir:

| CIS denetim KIMLIĞI | CIS denetim açıklaması |
| :------------- | :---------------------- |
| 6.2 | SSH erişiminin internet 'ten kısıtlanmasını sağlayın. |
| 6.3 | Hiçbir SQL veritabanı için 0.0.0.0/0 (herhangi bir IP) kullanılmasına izin vermediğinden emin olun. |
| 6.5 | Ağ Izleyicisi 'nin ' etkin ' olduğundan emin olun. |
| 6.6 | UDP kullanan giriş 'nin internet 'ten kısıtlanmasını sağlayın. |

Azure yay bulutu, güvenli bir ortamda dağıtıldığında Azure 'dan çıkış yapmak için yönetim trafiği gerektirir. Bunu gerçekleştirmek için, [VNET 'Te Azure yay bulutu 'nı çalıştırmaya yönelik müşteri sorumlulukları](./spring-cloud-vnet-customer-responsibilities.md)bölümünde listelenen ağ ve uygulama kurallarına izin vermeniz gerekir.

#### <a name="application-security"></a>Uygulama güvenliği

Bu tasarım sorumlusu, kimlik, veri koruma, anahtar yönetimi ve uygulama yapılandırmasının temel bileşenlerini ele alır. Tasarıma göre, Azure yay bulutu 'nda dağıtılan bir uygulama, çalışması için gereken en az ayrıcalıkla çalışır. Yetkilendirme denetimleri kümesi, hizmet kullanılırken doğrudan veri koruma ile ilgilidir. Anahtar yönetimi, bu katmanlı uygulama güvenliği yaklaşımını güçlendirir.

Aşağıdaki listede, bu başvurudaki anahtar yönetimine yönelik CCM denetimleri gösterilmektedir:

| CSA CCM denetim KIMLIĞI | CSA CCM denetim etki alanı |
| :----------------- | :--------------------- |
| EKM-01 | Şifreleme ve anahtar yönetimi yetkilendirme |
| EKM-02 | Şifreleme ve anahtar yönetimi anahtar üretimi |
| EKM-03 | Şifreleme ve anahtar yönetimine duyarlı veri koruma |
| EKM-04 | Şifreleme ve anahtar yönetimi depolama ve erişim |

CCM, EKM-02 ve EKM-03, anahtarları yönetmeye ve gizli verileri korumak için şifreleme protokollerini kullanmaya yönelik ilkeler ve yordamlar önerir. EKM-01, yönetilebilmesi için tüm şifreleme anahtarlarının tanınabilir sahipler sahibi olmasını önerir. EKM-04 standart algoritmaların kullanımını öneriyor.

Aşağıdaki listede, bu başvurudaki anahtar yönetimine yönelik olan CIS denetimleri gösterilmektedir:

| CIS denetim KIMLIĞI | CIS denetim açıklaması |
| :------------- | :---------------------- |
| 8.1 | Süre sonu tarihinin tüm anahtarlarda ayarlandığından emin olun. |
| 8.2 | Süre sonu tarihinin tüm gizli dizi üzerinde ayarlandığından emin olun. |
| 8.4 | Anahtar kasasının kurtarılabilir olduğundan emin olun. |

CIS denetimleri 8,1 ve 8,2, döndürme 'nin uygulanmasını sağlamak üzere kimlik bilgileri için süre sonu tarihlerinin ayarlanmış olmasını önerir. CIS denetimi 8,4, Anahtar Kasası içeriğinin iş sürekliliği sağlamak için geri yüklenebilmesini sağlar.

Uygulama güvenliğinin yönleri, Azure 'daki bir yay iş yükünü desteklemek için bu başvuru mimarisinin kullanılması için bir temel kümesi sağlar.

## <a name="next-steps"></a>Sonraki adımlar

[Azure yay bulutu başvuru mimarisi][10] deposunda bulunan ARM, Terkform ve Azure CLI dağıtımları aracılığıyla bu başvuru mimarisini bulun.

<!-- Reference links in article -->
[1]: ./index.yml
[2]: ../key-vault/index.yml
[3]: ../azure-monitor/index.yml
[4]: ../security-center/index.yml
[5]: /azure/devops/pipelines/
[6]: ../application-gateway/index.yml
[7]: ../web-application-firewall/index.yml
[8]: ./spring-cloud-tutorial-config-server.md
[9]: https://steeltoe.io/
[10]: https://github.com/Azure/azure-spring-cloud-reference-architecture
[11]: ./spring-cloud-tutorial-deploy-in-azure-virtual-network.md#virtual-network-requirements
[12]: ./spring-cloud-vnet-customer-responsibilities.md#azure-spring-cloud-network-requirements
[13]: ./spring-cloud-vnet-customer-responsibilities.md#azure-spring-cloud-fqdn-requirements--application-rules
[14]: ./spring-cloud-howto-staging-environment.md
[15]: https://devblogs.microsoft.com/java/monitor-applications-and-dependencies-in-azure-spring-cloud/
[16]: /azure/architecture/framework/
[17]: ./spring-cloud-tutorial-deploy-in-azure-virtual-network.md#virtual-network-requirements
[18]: https://cloudsecurityalliance.org/
[19]: https://cloudsecurityalliance.org/research/working-groups/cloud-controls-matrix
[20]: /azure/security/benchmarks/v2-cis-benchmark
[21]: https://www.cisecurity.org/