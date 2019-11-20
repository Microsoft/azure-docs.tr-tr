---
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: c7b9e3a9eb5c906fa598da9c8c547cb542ecb719
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036795"
---
# <a name="deprecated-securing-docker-containers-in-azure-container-service"></a>Kullanım DıŞı Azure Container Service Docker kapsayıcılarının güvenliğini sağlama

[!INCLUDE [ACS deprecation](container-service-deprecation.md)]

Bu makalede, Azure Container Service'de dağıtılan Docker kapsayıcılarının güvenliğini sağlamak için önemli bilgiler ve öneriler yer almaktadır. Bu noktaların çoğu Azure veya diğer ortamlara dağıtılmış Docker kapsayıcıları için de genel olarak geçerlidir. 

## <a name="image-security"></a>Görüntü güvenliği

Kapsayıcı, bir veya daha fazla depoda depolanan görüntülerden oluşturulur. Bu depolar genel veya özel kapsayıcı kayıt defterlerine ait olabilir. [Docker Hub](https://hub.docker.com/) genel bir kayıt defteri örneğidir. Özel bir kayıt defteri örneği olarak şirket içinde veya sanal özel bulutta kullanılabilen [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/) gösterilebilir. Ayrıca [Azure Container Registry](../articles/container-registry/container-registry-intro.md) gibi özel kapsayıcı bulut tabanlı kayıt defteri hizmetleri de vardır.

### <a name="public-and-private-images"></a>Genel ve özel görüntüler
Temelde, tüm genel olarak yayımlanan yazılım paketlerinde olduğu gibi genel kullanıma açık kapsayıcı görüntülerinde de güvenlik garanti edilemez. Her yazılım katmanında güvenlik açıkları olabilir ve kapsayıcı görüntüleri birden çok yazılım katmanından oluşur. Kapsayıcı görüntüsünün kaynağını, görüntünün sahibini (güvenilir bir kaynak olup olmadığını belirlemek için), içerdiği yazılım katmanlarını ve yazılım sürümlerini anlamak önemlidir. 

Örneğin, Docker Hub’da [nginx deposuna](https://hub.docker.com/_/nginx/) ve **Etiketler** sekmesine gittiğinizde her görüntü için renk kodlu güvenlik açıkları görürsünüz. Her renk görüntünün yazılım katmanında bir güvenlik açığını göstermektedir. Docker Hub tarama güvenlik açığı hakkında daha fazla bilgi için bkz. [Docker Hub'daki resmi depoları anlama](https://blog.docker.com/2015/06/understanding-official-repos-docker-hub/).

![Docker Hub'daki Nginx görüntüleri](./media/container-service-security/docker-hub-nginx.png)

Kuruluşlar güvenliğe büyük önem verir ve kendilerini güvenlik saldırılardan korumak için görüntüleri Azure Container Registry veya Docker Trusted Registry gibi özel bir kayıt defterinde saklayıp buradan almaları gerekir. Azure Container Registry, yönetilen özel kayıt defteri sağlamanın yanı sıra temel kimlik doğrulaması akışları için Azure Active Directory üzerinden salt okuma, yazma ve sahip izinleri için rol tabanlı erişim de dahil olmak üzere [hizmet sorumlusu tabanlı kimlik doğrulamayı](../articles/container-registry/container-registry-authentication.md) destekler.

### <a name="image-security-scanning"></a>Görüntü güvenliği tarama

Özel bir kayıt defteri kullanırken bile, ek güvenlik doğrulaması için görüntü tarama çözümleri kullanmak iyi bir fikirdir. Kapsayıcı görüntüsündeki her yazılım katmanı, kapsayıcı görüntüsündeki diğer katmanlardan bağımsız olarak güvenlik açıklarına potansiyel olarak yatkındır. Şirketler üretim iş yüklerini kapsayıcı teknolojilerini temel alarak dağıtmaya başladıkça kuruluşları güvenlik tehditlerinden koruyan görüntü tarama özelliği de önem kazanmaktadır. 

[Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry), [Aqua Security](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry) ve benzeri güvenlik izleme ve tarama çözümleri, özel bir kayıt defterindeki kapsayıcı görüntülerini taramak ve olası güvenlik açıklarını tanımlamak için kullanılabilir. Farklı çözümlerin sağladığı tarama derinliğini anlamak önemlidir. Örneğin, bazı çözümleri görüntü katmanlarını yalnızca bilinen güvenlik açıklarına karşı doğrulayabilir. Bu çözümler belirli paket yöneticisi yazılımları aracılığıyla oluşturulmuş görüntü katmanı yazılımlarını doğrulayamayabilir. Diğer çözümler daha ayrıntılı tarama tümleştirmesine sahiptir ve tüm paketlenmiş yazılımlarda güvenlik açıkları bulabilir.

### <a name="production-deployment-rules-and-audit"></a>Üretim dağıtım kuralları ve denetim
Bir uygulama üretime dağıtıldığında, üretim ortamlarında kullanılan görüntülerin güvenli olduğundan ve güvenlik açığı içermediğinden emin olmak için birkaç kural ayarlamak gerekir.

* Bir kural olarak, küçük bile olsa güvenlik açıklarına sahip görüntülerin üretim ortamında çalıştırılmasına izin verilmemelidir. Ayrıca, üretimde dağıtılan tüm görüntüler ideal olarak sadece birkaç kişinin erişebileceği özel bir kayıt defterine kaydedilmelidir. Etkili bir şekilde yönetilebilir olduklarından emin olmak için üretim görüntülerinin sayısını küçük tutmak önemlidir.

* Genel kullanıma açık kapsayıcı görüntüden yazılım kökeninin belirlenmesi zor olduğundan, katman kaynağı bilgilerinin elde edilmesi için görüntüleri kaynaktan oluşturmak iyi bir uygulamadır. Kendi oluşturdukları bir kapsayıcı görüntüsünde bir güvenlik açığı olduğunda müşteriler çözüme daha hızlı ulaşabilir. Genel görüntülerde müşterilerin genel görüntünün kökünü bulup düzeltmesi veya yayımcıdan başka bir güvenli görüntü alması gerekir.

* Üretimde dağıtılan baştan sona taranmış bir görüntünün uygulama ömrü boyunca güncel olması garanti edilmez. Görüntünün önceden bilinmeyen veya üretim dağıtımından sonra sunulan katmanlarında güvenlik açıkları bildirilebilir. Üretimde dağıtılan görüntülerin güncel veya bir süredir güncelleştirilmeyen görüntülerin belirlenmesi için düzenli olarak denetlenmesi gerekir. Kapalı kalma süresi olmadan kapsayıcı görüntülerini güncelleştirmek için mavi yeşil dağıtım yöntemlerini ve sıralı yükseltme mekanizmalarını kullanabilirsiniz. Görüntü tarama önceki bölümde açıklanan araçlar ile yapılabilir. 

* Görüntüleri ve tümleşik güvenlik taramasını oluşturmak için sürekli tümleştirme (CI) işlem hattı kullanmak güvenli kapsayıcı görüntüleri içeren güvenli özel kayıt defterlerini korumanıza yardımcı olabilir. CI çözümüne yerleşik güvenlik açığı taraması, tüm sınamaları geçen görüntülerin üretim iş yüklerinin dağıtıldığı özel kayıt defterine gönderilmesini sağlar. CI işlem hattı hatası, güvenlik açığı olan görüntülerin üretim iş yükü dağıtımları için kullanılan özel kayıt defterine gönderilmemesini sağlar. Ayrıca, çok sayıda görüntü varsa görüntü güvenliği taramasını otomatikleştirir. Aksi durumda, görüntüleri güvenlik açıkları için el ile denetlemek çok zor olabileceği gibi hata riskini de artırır.

## <a name="host-level-container-isolation"></a>Konak düzeyinde kapsayıcı yalıtımı
Bir müşteri Azure kaynaklarına kapsayıcı uygulamaları dağıttığında, bu uygulamalar kaynak gruplarında abonelik düzeyinde dağıtılır ve çok kiracılı değildir. Bu, bir müşteri başkalarıyla bir abonelik paylaşıyorsa, bu durum aynı abonelikte iki dağıtım arasında yerleşik hiçbir sınır olmadığı anlamına gelir. Bu nedenle, kapsayıcı düzeyi güvenliği garanti edilemez. 

Kapsayıcıların konağın (Azure Container Service'de bir kümedeki Azure sanal makinesidir) çekirdeğini ve kaynaklarını paylaştığını anlamak da önemlidir. Bu nedenle, üretimde çalışan kapsayıcılar ayrıcalıklı olmayan kullanıcı modunda çalıştırılmalıdır. Bir kapsayıcı kök ayrıcalıklarıyla çalıştırmak tüm ortamı tehlikeye atabilir. Kapsayıcıda kök düzeyinde erişim olduğunda bilgisayar korsaları, konağa tam kök ayrıcalıklarıyla erişebilir. Kapsayıcıları, salt okunur dosya sistemleri ile çalıştırmak da önemlidir. Bu, güvenliği aşılmış kapsayıcıya erişimi olan birinin dosya sistemine kötü amaçlı betikler yazmasını ve diğer dosyalara erişmesini önler. Benzer şekilde, bir kapsayıcıya ayrılan kaynakları (örneğin bellek, CPU ve ağ bant genişliği) sınırlamak da önemlidir. Bu, bilgisayar korsanlarının kaynakları işgal etmesini, kredi kartı sahtekarlığı veya bitcoin madenciliği gibi diğer kapsayıcıların ana bilgisayar veya küme üzerinde çalışmasını önleyebilecek yasadışı etkinlikler yürütmesini önlemeye yardımcı olur.

## <a name="orchestrator-considerations"></a>Düzenleyicide dikkat edilmesi gerekenler

Azure Container Service'de kullanılabilir her düzenleyicinin kendi güvenlik öncelikleri vardır. Örneğin, Container Service’de düzenleyici düğümlerine doğrudan SSH erişimini sınırlamanız gerekir. Bunun yerine, konaklara erişmeden kapsayıcı ortamınızı yönetmek için her düzenleyicinin kullanıcı arabirimi veya komut satırı araçlarını kullanmalısınız (Kubernetes için `kubectl` gibi). Daha fazla bilgi için bkz. [Kubernetes, DC/OS veya Docker Swarm kümesine uzak bağlantı kurma](../articles/container-service/kubernetes/container-service-connect.md).

Düzenleyicilere özgü diğer güvenlik bilgileri için aşağıdaki kaynaklara bakın:

* **Kubernetes**: [Kubernetes dağıtımı için En Iyi güvenlik uygulamaları](https://kubernetes.io/blog/2016/08/security-best-practices-kubernetes-deployment/)

* **DC/OS**: [Kümenizin güvenliğini sağlama](http://docs.mesosphere.com/1.12/administering-clusters/securing-your-cluster)

* **Docker Sısınma**: [Docker güvenliği](https://www.docker.com/docker-security)

## <a name="next-steps"></a>Sonraki adımlar

* Docker mimarisi ve kapsayıcı güvenliği hakkında daha fazla bilgi için bkz. [Kapsayıcı Güvenliğine Giriş](https://www.docker.com/sites/default/files/WP_IntrotoContainerSecurity_08.19.2016.pdf).

* Azure'un platform güvenliği hakkında bilgi edinmek için [Azure Güvenlik Merkezi](https://www.microsoft.com/en-us/trustcenter/cloudservices/azure)'ni ziyaret edin.
