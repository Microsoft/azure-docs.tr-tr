---
title: Azure Container Instances güvenlik konuları
description: Azure Container Instances için güvenli görüntü ve gizli dizileri ve herhangi bir kapsayıcı platformu için genel güvenlik konularını korumanıza yönelik öneriler
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/29/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 618d3a901698e46760d970f6d4fbc4157c5d2ea3
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325916"
---
# <a name="security-considerations-for-azure-container-instances"></a>Azure Container Instances için güvenlik konuları

Bu makalede, kapsayıcı uygulamalarını çalıştırmak için Azure Container Instances kullanmaya yönelik güvenlik konuları açıklanır. Konu başlıkları şunlardır:

> [!div class="checklist"]
> * Azure Container Instances için görüntü ve gizli dizileri yönetmeye yönelik **güvenlik önerileri**
> * Kapsayıcı platformu için kapsayıcı ekosisteminin her türlü kapsayıcı platformu için **dikkat edilmesi gerekenler**

## <a name="security-recommendations-for-azure-container-instances"></a>Azure Container Instances için güvenlik önerileri

### <a name="use-a-private-registry"></a>Özel kayıt defteri kullan

Kapsayıcı, bir veya daha fazla depoda depolanan görüntülerden oluşturulur. Bu depolar, [Docker Hub](https://hub.docker.com)veya özel bir kayıt defteri gibi genel bir kayıt defterine ait olabilir. Özel bir kayıt defteri örneği olarak şirket içinde veya sanal özel bulutta kullanılabilen [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/) gösterilebilir. Bulut tabanlı özel kapsayıcı kayıt defteri hizmetlerini de [Azure Container Registry](../container-registry/container-registry-intro.md)de kullanabilirsiniz. 

Genel olarak kullanılabilir bir kapsayıcı görüntüsü güvenliği garanti etmez. Kapsayıcı görüntüleri birden çok yazılım katmanından oluşur ve her yazılım katmanında güvenlik açıkları olabilir. Saldırılara yönelik tehditleri azaltmaya yardımcı olmak için, Azure Container Registry veya Docker güvenilen kayıt defteri gibi özel bir kayıt defterinden görüntü depolamanız ve almanız gerekir. Azure Container Registry yönetilen özel bir kayıt defteri sağlamaya ek olarak, temel kimlik doğrulama akışları için Azure Active Directory aracılığıyla [hizmet sorumlusu tabanlı kimlik doğrulamasını](../container-registry/container-registry-authentication.md) destekler. Bu kimlik doğrulaması, salt okuma (çekme), yazma (gönderme) ve sahip izinleri için rol tabanlı erişimi içerir.

### <a name="monitor-and-scan-container-images"></a>Kapsayıcı görüntülerini izleme ve tarama

[Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) ve [deniz mavisi güvenlik](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) gibi güvenlik Izleme ve tarama çözümleri Azure Marketi aracılığıyla kullanılabilir. Bunları özel bir kayıt defterinde kapsayıcı görüntülerini taramak ve olası güvenlik açıklarını belirlemek için kullanabilirsiniz. Farklı çözümlerin sağladığı tarama derinliğini anlamak önemlidir. 

### <a name="protect-credentials"></a>Kimlik bilgilerini koruma

Kapsayıcılar, çeşitli kümeler ve Azure bölgelerine yayılabilir. Bu nedenle, oturum açma işlemleri veya parola veya belirteçler gibi API erişimi için gereken kimlik bilgilerini güvence altına almalısınız. Yalnızca ayrıcalıklı kullanıcıların, aktarımda ve geri kalanında bu kapsayıcılara erişebildiğinden emin olun. Tüm kimlik bilgisi gizli dizileri envanterini çıkarın ve geliştiricilerin kapsayıcı platformları için tasarlanan, gelişen gizli dizi yönetim araçlarını kullanmasını gerektirir.  Çözümünüz şifreli veritabanları, aktarım sırasında gizli veriler için TLS şifreleme ve en düşük ayrıcalıklı [rol tabanlı erişim denetimi](../role-based-access-control/overview.md)içerdiğinden emin olun. [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) , Kapsayıcılı uygulamalar için şifreleme anahtarlarını ve gizli dizileri (sertifikalar, bağlantı dizeleri ve parolalar gibi) korumalarını sağlayan bir bulut hizmetidir. Bu veriler hassas ve iş açısından kritik olduğundan, yalnızca yetkili uygulamaların ve kullanıcıların erişebilmesi için anahtar kasalarınıza güvenli bir şekilde erişin.

## <a name="considerations-for-the-container-ecosystem"></a>Kapsayıcı ekosistemi ile ilgili konular

Uygun ve iyi şekilde uygulanan aşağıdaki güvenlik önlemleri, kapsayıcı ekosisteminizi güvenli hale getirmenize ve korumanıza yardımcı olabilir. Bu ölçüler, üretim dağıtımı aracılığıyla geliştirme aşamasından ve kapsayıcı yöneticileri, ana bilgisayarlar ve platformlar arasında, kapsayıcı yaşam döngüsü boyunca geçerlidir. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Kapsayıcı geliştirme yaşam döngüsünün bir parçası olarak güvenlik açığı yönetimi 'ni kullanın 

Kapsayıcı geliştirme yaşam döngüsü boyunca geçerli güvenlik açığı yönetimini kullanarak, daha ciddi bir sorun haline gelmeden önce, güvenlik sorunlarını belirleyip çöztireceğiniz gürültü 'yi geliştirir. 

### <a name="scan-for-vulnerabilities"></a>Güvenlik açıklarını Tara 

Her zaman yeni güvenlik açıkları bulunur, bu nedenle güvenlik açıklarını taramak ve tanımlamak sürekli bir işlemdir. Kapsayıcı yaşam döngüsü boyunca güvenlik açığı taramasını dahil edin:

* Geliştirme işlem hattınızda son bir denetim olarak, görüntüleri ortak veya özel bir kayıt defterine göndermeden önce kapsayıcılar üzerinde bir güvenlik açığı taraması gerçekleştirmeniz gerekir. 
* Geliştirme sırasında bir şekilde kaçırılmış olan ve kapsayıcı görüntülerinde kullanılan kodda mevcut olabilecek yeni keşfedilen güvenlik açıklarını gidermeye yönelik tüm kusurlar belirlemek için kayıt defterindeki kapsayıcı görüntülerini taramaya devam edin.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Çalışan kapsayıcılarla harita görüntüsü güvenlik açıkları 

Kapsayıcıları çalıştırmak için kapsayıcı görüntülerinde tanımlanan güvenlik açıklarını eşleştirmenin bir yolu olması gerekir, bu nedenle güvenlik sorunları azaltılabilir veya çözülebilir.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Ortamınızda yalnızca onaylanan görüntülerin kullanıldığından emin olun 

Bir kapsayıcı ekosisteminde, bilinmeyen kapsayıcılara da izin verilmeden yeterli sayıda değişiklik ve volajyer vardır. Yalnızca onaylanan kapsayıcı görüntülerine izin verin. İçin, onaylanmamış kapsayıcı görüntülerinin kullanımını izlemek ve bunları engellemek için araçlar ve süreçler vardır. 

Saldırı yüzeyini azaltmanın etkili bir yolu ve geliştiricilerin kritik güvenlik hataları yapmasını önlemek, kapsayıcı görüntülerinin akışını geliştirme ortamınıza göre denetlemedir. Örneğin, olası saldırılara karşı yüzeyi en aza indirmek için, tercihen bir temel görüntü olarak tek bir Linux dağıtımını tasdik (Ubuntu yerine alp veya CoreOS) olarak düşünebilirsiniz. 

Görüntü imzalama veya parmak izi, kapsayıcıların bütünlüğünü doğrulamanızı sağlayan bir gözetim zinciri sağlayabilir. Örneğin, Azure Container Registry Docker 'ın [içerik güven](https://docs.docker.com/engine/security/trust/content_trust) modelini destekler, bu da görüntü yayımcılarının bir kayıt defterine gönderilen görüntüleri imzalamasını ve görüntü tüketicilerini yalnızca imzalı görüntüleri çekmesini sağlar.

### <a name="permit-only-approved-registries"></a>Yalnızca onaylanan kayıt defterlerine izin ver 

Ortamınızın yalnızca onaylanmış görüntüleri kullandığından emin olmanın bir uzantısı, yalnızca onaylanan kapsayıcı kayıt defterlerinin kullanılmasına izin versağlamaktır. Onaylanan kapsayıcı kayıt defterlerinin kullanımını zorunlu kılmak, bilinmeyen güvenlik açıklarına veya güvenlik sorunlarından sorumlu olma olasılığını sınırlayarak riske maruz kalmayı azaltır. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Yaşam döngüsü boyunca görüntülerin bütünlüğünden emin olun 

Kapsayıcı yaşam döngüsü boyunca güvenlik yönetiminin bir parçası, kayıt defterindeki kapsayıcı görüntülerinin bütünlüğünden ve bunlar tarafından değiştirildikleri veya üretimde dağıtıldığı şekilde sağlamaktır. 

* Küçük ve güvenlik açıklarına sahip görüntülerin bir üretim ortamında çalıştırılmasına izin verilmemelidir. İdeal olarak, üretimde dağıtılan tüm görüntülerin, bir seçimi birkaç kez erişilebilen özel bir kayıt defterine kaydedilmesi gerekir. Etkili bir şekilde yönetilebilmeleri için üretim görüntülerinin sayısını küçük tutun.

* Genel kullanıma açık bir kapsayıcı görüntüsünden yazılımın kaynağını belirlemek zor olduğundan, katmanın kaynağı bilgisini sağlamak için kaynaktan görüntü oluşturun. Kendi oluşturdukları bir kapsayıcı görüntüsünde bir güvenlik açığı olduğunda müşteriler çözüme daha hızlı ulaşabilir. Ortak bir görüntüyle, müşterilerin bu görüntüyü onarmak için ortak bir görüntünün kökünü bulması veya yayımcıdan başka bir güvenli görüntü alması gerekir. 

* Üretimde dağıtılan ve tamamen taranan bir görüntünün, uygulamanın kullanım ömrü için güncel olması garanti edilmez. Görüntünün önceden bilinmeyen veya üretim dağıtımından sonra sunulan katmanlarında güvenlik açıkları bildirilebilir. 

  Güncel olmayan veya bir süredir güncelleştirilmemiş resimleri belirlemek için üretimde dağıtılan görüntüleri düzenli olarak denetleyin. Kapalı kalma süresi olmadan kapsayıcı görüntülerini güncelleştirmek için mavi yeşil dağıtım yöntemleri ve sıralı yükseltme mekanizmaları kullanabilirsiniz. Önceki bölümde açıklanan araçları kullanarak görüntüleri tarayabilirsiniz. 

* Güvenli görüntüler oluşturmak ve bunları özel kayıt defterinize göndermek için tümleşik güvenlik taramasıyla sürekli tümleştirme (CI) işlem hattı kullanın. CI çözümüne yerleşik güvenlik açığı taraması, tüm sınamaları geçen görüntülerin üretim iş yüklerinin dağıtıldığı özel kayıt defterine gönderilmesini sağlar. 

  CI ardışık düzen hatası, güvenlik açığı bulunan görüntülerin üretim iş yükü dağıtımları için kullanılan özel kayıt defterine itilmemesini sağlar. Ayrıca, önemli sayıda görüntü varsa görüntü güvenliği taramasını otomatikleştirir. Aksi durumda, görüntüleri güvenlik açıkları için el ile denetlemek çok zor olabileceği gibi hata riskini de artırır. 

### <a name="enforce-least-privileges-in-runtime"></a>Çalışma zamanında en az ayrıcalıkları zorla 

En az ayrıcalık kavramı, kapsayıcılar için de geçerli olan temel bir en iyi güvenlik uygulamasıdır. Bir güvenlik açığından yararlandığında, genellikle güvenliği aşılan uygulama veya işlemden birine eşit olan saldırgan erişimi ve ayrıcalıkları verir. Kapsayıcının, işin tamamlanması için gereken en düşük ayrıcalıklarla ve erişimlerle çalışmasını sağlamak, riske maruz kalmayı azaltır. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Gereksiz ayrıcalıkları kaldırarak kapsayıcı saldırısı yüzeyini azaltma 

Ayrıca, kapsayıcı çalışma zamanından kullanılmayan veya gereksiz süreçler veya ayrıcalıklar kaldırarak olası saldırı yüzeyini en aza indirebilirsiniz. Ayrıcalıklı kapsayıcılar kök olarak çalışır. Kötü amaçlı bir kullanıcı veya iş yükü ayrıcalıklı bir kapsayıcıda çıkar, kapsayıcı o sistemde kök olarak çalıştırılır.

### <a name="whitelist-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Kapsayıcının erişimine veya çalıştırmasına izin verilen dosya ve yürütülebilir dosyaları beyaz listeye ekleme 

Değişken sayısının azaltılması veya bilinmeyenler, kararlı ve güvenilir bir ortamı korumanıza yardımcı olur. Kapsayıcıları yalnızca önceden onaylanmış veya beyaz listelenmiş dosyalara erişebilmeleri veya çalıştırmasına izin verecek şekilde sınırlandırma, risk düzeyini sınırlayan kanıtlanmış bir yöntemdir.  

Başlangıçtan itibaren bir beyaz listeyi yönetmek çok daha kolay. Beyaz liste, uygulamanın düzgün çalışması için hangi dosya ve yürütülebilir dosyaların gerekli olduğunu öğrenecek şekilde denetim ve yönetilebilirlik ölçümü sağlar. 

Beyaz liste yalnızca saldırı yüzeyini azaltmaz, ancak aynı zamanda anomali için bir taban çizgisi sağlayabilir ve "gürültülü komşu" ve kapsayıcı ayırıcı senaryolarının kullanım örneklerinin oluşmasını önler. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Çalışan kapsayıcılar üzerinde ağ segmentlemesini zorla  

Bir alt ağdaki kapsayıcıları başka bir alt ağdaki güvenlik risklerine karşı korumaya yardımcı olmak için ağ kesimlemesini (veya nano kesimlemeye) veya çalışan kapsayıcılar arasında ayırmayı koruyun. Ağ segmentlemesini sürdürmek, Ayrıca, uyumluluk mantarihlerini karşılamak için gereken sektörlerdeki kapsayıcıları kullanmak için de gerekli olabilir.  

Örneğin, partner aracı [deniz mavisi](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) , nano kesimlemeye yönelik otomatik bir yaklaşım sağlar. Deniz mavisi, çalışma zamanında kapsayıcı ağ etkinliklerini izler. Diğer kapsayıcılardan, hizmetlerden, IP adreslerinden ve genel İnternet 'e giden tüm gelen ve giden ağ bağlantılarını tanımlar. Nano-segmentleme, izlenen trafiğe göre otomatik olarak oluşturulur. 

### <a name="monitor-container-activity-and-user-access"></a>Kapsayıcı etkinliğini ve Kullanıcı erişimini izleme 

Her türlü BT ortamında olduğu gibi, tüm şüpheli veya kötü amaçlı etkinlikleri hızlı bir şekilde belirlemek için, kapsayıcı ekosisteminize düzenli olarak etkinlik ve Kullanıcı erişimi izlemeniz gerekir. Azure, aşağıdakiler dahil olmak üzere kapsayıcı izleme çözümleri sağlar:

* Azure Kubernetes Service (AKS) üzerinde barındırılan Kubernetes ortamlarına dağıtılan iş yüklerinizin performansını izlemek için [kapsayıcılar Için Azure izleyici](../azure-monitor/insights/container-insights-overview.md) . Kapsayıcılar için Azure İzleyici denetleyicileri, düğümleri ve Kubernetes ölçümler API aracılığıyla kullanılabilir olan kapsayıcıları bellekten toplanması ve işlemci ölçümleri performans görünürlük sağlar. 

* [Azure Kapsayıcı izleme çözümü](../azure-monitor/insights/containers.md) , diğer Docker ve Windows kapsayıcı konaklarının tek bir konumda görüntülemenize ve yönetilmesine yardımcı olur. Örneğin:

  * Kapsayıcılarla kullanılan komutları gösteren ayrıntılı denetim bilgilerini görüntüleyin. 
  * Docker veya Windows konaklarını uzaktan görüntülemek zorunda kalmadan merkezi günlükleri görüntüleyip arayarak kapsayıcılarla ilgili sorunları giderin.  
  * Gürültülü olabilecek ve bir konakta fazla kaynak tüketen kapsayıcılar bulun.
  * Kapsayıcılar için merkezi CPU, bellek, depolama ve ağ kullanımını ve performans bilgilerini görüntüleyin.  

  Çözüm, Docker Sısınma, DC/OS, yönetilmeyen Kubernetes, Service Fabric ve Red Hat OpenShift gibi kapsayıcı düzenleyicilerinin kullanılmasını destekler. 

### <a name="monitor-container-resource-activity"></a>Kapsayıcı kaynak etkinliğini izleme 

Dosyalar, ağ ve kapsayıcılarınızın erişebileceği diğer kaynaklar gibi kaynak etkinliğinizi izleyin. İzleme kaynak etkinliği ve tüketimi, hem performans izleme hem de güvenlik önlemi olarak faydalıdır. 

[Azure izleyici](../azure-monitor/overview.md) , ölçümler, etkinlik günlükleri ve tanılama günlükleri koleksiyonuna Izin vererek Azure hizmetleri için çekirdek izlemeye olanak tanır. Örneğin, etkinlik günlüğü size yeni kaynakların ne zaman oluşturulduğunu veya değiştirildiğini bildirir. 

Farklı kaynaklar için ve hatta bir sanal makinenin içindeki işletim sistemi için bile performans istatistikleri sağlayan ölçümler kullanılabilir. Azure portalında gezginlerden biriyle bu verileri görüntüleyebilir ve bu ölçümlere dayalı uyarılar oluşturabilirsiniz. Azure Izleyici en hızlı ölçüm işlem hattını (5 dakika ila 1 dakika) sağlar; bu nedenle, zaman açısından kritik uyarılar ve bildirimler için onu kullanmanız gerekir. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Denetim için tüm kapsayıcı Yönetici Kullanıcı erişimini günlüğe kaydet 

Kapsayıcı ekosisteminize, kapsayıcı kayıt defterine ve kapsayıcı görüntülerine yönetim erişiminin doğru bir denetim izini saklayın. Bu Günlükler, denetim amaçlarıyla gerekli olabilir ve herhangi bir güvenlik olayından sonra Forli kanıt olarak yararlı olacaktır. Bu amaçla elde etmek için [Azure Kapsayıcı izleme çözümünü](../azure-monitor/insights/containers.md) kullanabilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

* [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) ve [deniz mavisi güvenlik](https://www.aquasec.com/solutions/azure-container-security/)çözümleriyle kapsayıcı güvenlik açıklarını yönetme hakkında daha fazla bilgi edinin.

* [Azure 'da kapsayıcı güvenliği](https://azure.microsoft.com/resources/container-security-in-microsoft-azure/)hakkında daha fazla bilgi edinin.