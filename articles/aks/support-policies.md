---
title: Azure Kubernetes hizmeti (AKS) için destek ilkeleri
description: Önizleme aşamasında olan Azure Kubernetes hizmeti (AKS) destek ilkeleri, paylaşılan sorumluluk ve özellikler hakkında bilgi edinin (veya Alpha veya beta).
services: container-service
ms.topic: article
ms.date: 09/18/2020
ms.openlocfilehash: 86b1c0bba30b41a2ee17cfbdf05286c4d2b3fb8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91892719"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Azure Kubernetes hizmeti için destek ilkeleri

Bu makalede, Azure Kubernetes Service (AKS) için teknik destek ilkeleri ve sınırlamaları hakkında ayrıntılar sağlanmaktadır. Makalede ayrıca Aracı düğüm yönetimi, yönetilen denetim düzlemi bileşenleri, üçüncü taraf açık kaynaklı bileşenler ve güvenlik ya da düzeltme eki yönetimi de ayrıntılı olarak açıklanır.

## <a name="service-updates-and-releases"></a>Hizmet güncelleştirmeleri ve yayınlar

* Sürüm bilgileri için bkz. [aks sürüm notları](https://github.com/Azure/AKS/releases).
* Önizlemedeki özellikler hakkında daha fazla bilgi için bkz. [aks Önizleme özellikleri ve ilgili projeler](https://awesomeopensource.com/projects/aks?categoryPage=11).

## <a name="managed-features-in-aks"></a>AKS 'deki yönetilen özellikler

İşlem veya ağ bileşenleri gibi temel altyapı hizmet olarak altyapı (IaaS) bulut bileşenleri, alt düzey denetimlere ve özelleştirme seçeneklerine erişmenize izin verir. Buna karşılık, AKS, kümeniz için ihtiyaç duyduğunuz ortak yapılandırma ve özellik kümesini sağlayan bir anahtar oluşturma Kubernetes dağıtımı sağlar. Bir AKS kullanıcısı olarak, özelleştirme ve dağıtım seçenekleriniz sınırlı olacaktır. Exchange 'de, Kubernetes kümelerini doğrudan veya yönetmeniz gerekmez.

AKS ile tam olarak yönetilen bir *Denetim düzlemi* alırsınız. Denetim düzlemi, çalıştırmak ve son kullanıcılara Kubernetes kümeleri sağlamak için ihtiyacınız olan tüm bileşenleri ve hizmetleri içerir. Tüm Kubernetes bileşenleri Microsoft tarafından korunur ve çalıştırılır.

Microsoft, Denetim bölmesi aracılığıyla aşağıdaki bileşenleri yönetir ve izler:

* Kubelet veya Kubernetes API sunucuları
* Hizmet kalitesi (QoS), ölçeklenebilirlik ve çalışma zamanı sağlayan, etcd veya uyumlu bir anahtar-değer deposu
* DNS hizmetleri (örneğin, KUIN-DNS veya CoreDNS)
* Kubernetes proxy veya ağ
* Kuin-System ad alanında çalışan ek eklenti veya sistem bileşeni

AKS, bir hizmet olarak platform (PaaS) çözümü değildir. Aracı düğümleri gibi bazı bileşenlerin, kullanıcıların AKS kümesini sürdürmeleri gereken *paylaşılan sorumluluğu* vardır. Kullanıcı girişi, örneğin, bir aracı düğümü işletim sistemi (OS) güvenlik düzeltme eki uygulamak için gereklidir.

Hizmetler, Microsoft 'un ve AKS ekibinin hizmet kullanılabilirliği ve işlevleri dağıttığı, çalıştığı ve sorumlu olduğu konusunda bir şekilde *yönetilir* . Müşteriler bu yönetilen bileşenleri değiştiremezler. Microsoft, tutarlı ve ölçeklenebilir bir kullanıcı deneyimi sağlamak için özelleştirmeyi kısıtlar. Tamamen özelleştirilebilir bir çözüm için bkz. [aks motoru](https://github.com/Azure/aks-engine).

## <a name="shared-responsibility"></a>Paylaşılan sorumluluk

Bir küme oluşturulduğunda, AKS 'nin oluşturduğu Kubernetes aracı düğümlerini tanımlarsınız. İş yükleriniz bu düğümlerde yürütülür.

Aracı düğümleriniz özel kod yürüttiğinden ve hassas verileri depoladığı için Microsoft Desteği bunlara yalnızca çok sınırlı bir şekilde erişebilir. Microsoft Desteği, Express izniniz veya yardımınız olmadan oturum açamaz, içindeki komutları yürütebilir veya bu düğümlere ait günlükleri görüntüleyemez.

IaaS API 'Lerinden herhangi birini kullanan doğrudan aracı düğümlerine yapılan herhangi bir değişiklik, küme tarafından desteklenmeyen tablo oluşturur. Aracı düğümlerinde yapılan herhangi bir değişiklik, gibi Kubernetes-Native mekanizmaları kullanılarak yapılmalıdır `Daemon Sets` .

Benzer şekilde, küme ve düğümlere Etiketler ve Etiketler gibi herhangi bir meta veri ekleyebilirsiniz, ancak sistem tarafından oluşturulan meta verilerin herhangi birini değiştirmek, kümeyi desteklenmeyen şekilde işleyebilir.

## <a name="aks-support-coverage"></a>AKS Destek kapsamı

Microsoft aşağıdaki örnekler için teknik destek sağlar:

* Kubernetes hizmetinin API sunucusu gibi sağladığı ve desteklediği tüm Kubernetes bileşenlerine bağlantı.
* Kubernetes denetim düzlemi Hizmetleri (örneğin, Kubernetes denetim düzlemi, API sunucusu, etcd ve coreDNS) yönetimi, çalışma süresi, QoS ve işlemler.
* Etcd veri deposu. Destek, olağanüstü durum planlama ve küme durumu geri yüklemesi için her 30 dakikada bir tüm etcd verilerine yönelik otomatik, saydam yedeklemeler içerir. Bu yedeklemeler sizin veya herhangi bir kullanıcı için doğrudan kullanılamaz. Veri güvenilirliğini ve tutarlılığını güvence altına alırlar. Etcd. isteğe bağlı geri alma veya geri yükleme özelliği olarak desteklenmez.
* Kubernetes için Azure bulut sağlayıcısı sürücüsünde herhangi bir tümleştirme noktası. Bunlar, yük dengeleyiciler, kalıcı birimler veya ağ (Kubernetes ve Azure CNı) gibi diğer Azure hizmetleriyle tümleştirmeler içerir.
* Kubernetes API Server, etcd ve coreDNS gibi denetim düzlemi bileşenlerinin özelleştirilmesi hakkındaki sorular veya sorunlar.
* Azure CNı, kubenet gibi ağ ile ilgili sorunlar veya diğer ağ erişimi ve işlevsellik sorunları. Sorunlar DNS çözümlemesi, paket kaybı, yönlendirme vb. içerebilir. Microsoft çeşitli ağ senaryolarını destekler:
  * Kubenet ve Azure CNı, yönetilen sanal ağlar veya özel (kendi kendi alt ağlarınızı getir) kullanarak.
  * Diğer Azure hizmetlerine ve uygulamalarına yönelik bağlantı
  * Giriş denetleyicileri ve giriş veya yük dengeleyici yapılandırması
  * Ağ performansı ve gecikme süresi


> [!NOTE]
> Microsoft/AKS tarafından gerçekleştirilen herhangi bir küme eylemi, yerleşik bir Kubernetes rolü `aks-service` ve yerleşik rol bağlama altında Kullanıcı onayı ile yapılır `aks-service-rolebinding` . Bu rol, AKS 'in küme sorunlarını giderme ve tanılama, ancak izinleri değiştiremeyeceği ya da roller ya da rol bağlamaları ya da diğer yüksek ayrıcalıklı eylemler oluşturmalarına olanak sağlar. Rol erişimi yalnızca, tam zamanında (JıT) erişime sahip etkin destek biletleri altında etkinleştirilir.

Microsoft aşağıdaki örnekler için teknik destek sağlamaz:

* Kubernetes 'i kullanma hakkında sorular. Örneğin, Microsoft Desteği özel giriş denetleyicileri oluşturma, uygulama iş yüklerini kullanma veya üçüncü taraf ya da açık kaynaklı yazılım paketlerini veya araçları uygulama hakkında öneri sağlamaz.
  > [!NOTE]
  > Microsoft Desteği, AKS kümesi işlevselliği, özelleştirmesi ve ayarlama (örneğin, Kubernetes işlemleri ve yordamları) konusunda öneri verebilir.
* Kubernetes denetim düzlemi kapsamında sağlanmayan veya AKS kümeleriyle dağıtılan üçüncü taraf açık kaynaklı projeler. Bu projeler, Istio, Helm, Envoy veya diğerleri içerebilir.
  > [!NOTE]
  > Microsoft, Held gibi üçüncü taraf açık kaynaklı projeler için en iyi desteği sağlayabilir. Üçüncü taraf açık kaynak aracının, Kubernetes Azure bulut sağlayıcısıyla veya diğer AKS ile ilgili hatalarla tümleştirildiği durumlarda Microsoft, Microsoft belgelerindeki örnekleri ve uygulamaları destekler.
* Üçüncü taraf kapalı kaynaklı yazılım. Bu yazılım, güvenlik tarama araçları ve ağ aygıtlarını veya yazılımlarını içerebilir.
* [Aks belgelerinde](./index.yml)listelenenlerden farklı ağ özelleştirmeleri.


## <a name="aks-support-coverage-for-agent-nodes"></a>Aracı düğümleri için AKS Destek kapsamı

### <a name="microsoft-responsibilities-for-aks-agent-nodes"></a>AKS aracı düğümleri için Microsoft sorumlulukları

Microsoft ve kullanıcılar, Kubernetes aracı düğümlerinin sorumluluğunu burada paylaşır:

* Temel işletim sistemi görüntüsünün gerekli eklemeleri (izleme ve ağ aracıları gibi) vardır.
* Aracı düğümleri, işletim sistemi düzeltme eklerini otomatik olarak alır.
* Aracı düğümlerinde çalışan Kubernetes denetim düzlemi bileşenleriyle ilgili sorunlar otomatik olarak düzeltildi. Bu bileşenler şunlardır:
  * `Kube-proxy`
  * Kubernetes ana bileşenlerine iletişim yolları sağlayan ağ tünelleri
  * `Kubelet`
  * `Moby` veya `ContainerD`

> [!NOTE]
> Bir aracı düğümü çalışır durumda değilse, AKS tek tek bileşenleri veya tüm aracı düğümünü yeniden başlatabilir. Bu yeniden başlatma işlemleri otomatikleştirilir ve genel sorunlar için otomatik düzeltme sağlar. Otomatik Düzeltme mekanizmaları hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [düğüm otomatik onarma](node-auto-repair.md)

### <a name="customer-responsibilities-for-aks-agent-nodes"></a>AKS aracı düğümleri için müşteri sorumlulukları

Microsoft, görüntü düğümleriniz için, haftalık düzeltme ekleri ve yeni görüntüler sağlar, ancak otomatik olarak bunları varsayılan olarak kullanmaz. Aracı düğümünüz işletim sistemi ve çalışma zamanı bileşenlerinizi düzeltme eki uygulamak için, normal bir [düğüm görüntüsü yükseltme](node-image-upgrade.md) zamanlaması tutmanız veya otomatikleştirmeniz gerekir.

Benzer şekilde, AKS 'ler düzenli olarak yeni Kubernetes yamaları ve ikincil sürümlerini yayınlar. Bu güncelleştirmeler Kubernetes için güvenlik veya işlev geliştirmeleri içerebilir. Kümelerinizin Kubernetes sürümünü, [aks Kubernetes destek sürümü ilkesine](supported-kubernetes-versions.md)göre güncel tutmanız ve bu sürümden sorumlu olursunuz.

#### <a name="user-customization-of-agent-nodes"></a>Aracı düğümlerinin Kullanıcı özelleştirmesi
> [!NOTE]
> AKS aracı düğümleri Azure portal normal Azure IaaS kaynakları olarak görünür. Ancak bu sanal makineler özel bir Azure Kaynak grubuna dağıtılır (genellikle MC_ önekli \* ). IaaS API 'Lerini veya kaynaklarını kullanarak, temel işletim sistemi görüntüsünü değiştiremez veya bu düğümlere doğrudan özelleştirmeler yapabilirsiniz. AKS API 'SI aracılığıyla gerçekleştirilen özel değişiklikler, yükseltme, ölçeklendirme, güncelleştirme veya yeniden başlatma aracılığıyla devam etmez. Microsoft Desteği, değişiklikler yapmaya yönlendirmedikleri takdirde aracı düğümlerinde değişiklik yapmaktan kaçının.

AKS aracı düğümlerinin yaşam döngüsünü ve işlemlerini sizin adınıza yönetmektedir. aracı düğümleriyle ilişkili IaaS kaynaklarını değiştirme **desteklenmez**. Desteklenmeyen bir işlem örneği, sanal makine ölçek kümesi portalı veya API 'SI aracılığıyla yapılandırma el ile değiştirilerek bir düğüm havuzu sanal makine ölçek kümesini özelleştirmektir.
 
İş yüküne özgü yapılandırmalarda veya paketlerde AKS, [Kubernetes `daemon sets` ](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)kullanmayı önerir.

Kubernetes ayrıcalıklı `daemon sets` ve init kapsayıcıları kullanmak, küme Aracısı düğümlerinde üçüncü taraf yazılımları ayarlamanıza/değiştirmenize veya yüklemenize olanak sağlar. Bu tür özelleştirmeler örnekleri, özel güvenlik taraması yazılımı eklemeyi veya sysctl ayarlarını güncelleştirmeyi içerir.

Yukarıdaki gereksinimlerin uygulanması durumunda bu yol önerilse de, AKS Mühendisliği ve desteği, özel olarak dağıtılan bir nedenle, düğümü işleyen değişiklikleri tanılamada veya sorunları gidermeye yardımcı olamaz `daemon set` .

### <a name="security-issues-and-patching"></a>Güvenlik sorunları ve düzeltme eki uygulama

AKS 'nin yönetilen bileşenlerinden birinde bir veya daha fazlası varsa, AKS ekibi, sorunu azaltmak için etkilenen tüm kümeleri yayacaktır. Alternatif olarak, takım kullanıcılara Yükseltme Kılavuzu sağlayacaktır.

Bir güvenlik sorunundan etkilenen aracı düğümleri için, Microsoft sizi etkiyle ilgili ayrıntıları ve güvenlik sorununu düzeltme ya da azaltma adımlarını (normalde bir düğüm görüntüsü yükseltmesi veya bir küme düzeltme eki yükseltmesi) bilgilendirecek.

### <a name="node-maintenance-and-access"></a>Düğüm bakımı ve erişimi

' De oturum açıp aracı düğümlerinde değişiklik yapabilseniz de, değişiklikler küme tarafından desteklenmeyen bir tablo yapabildiğinden bu işlem önerilmez.

## <a name="network-ports-access-and-nsgs"></a>Ağ bağlantı noktaları, erişim ve NSG 'ler

Yalnızca özel alt ağlardaki NSG 'leri özelleştirebilirsiniz. Yönetilen alt ağlarda veya aracı düğümlerinin NIC düzeyinde NSG 'leri özelleştiremeyebilirsiniz. AKS 'in belirli uç noktalara çıkış gereksinimleri vardır, çıkışı denetlemek ve gerekli bağlantıları sağlamak için bkz. [çıkış trafiğini sınırlandırma](limit-egress-traffic.md).

## <a name="stopped-or-de-allocated-clusters"></a>Durdurulmuş veya serbest ayrılmış kümeler

Daha önce belirtildiği gibi, IaaS API 'Leri/CLı/Portal aracılığıyla tüm küme düğümlerini el ile serbest olarak ayırmak, kümeyi destek dışında oluşturur. Tüm düğümleri durdurmak/serbest bırakmak için desteklenen tek yol, küme durumunu 12 aya kadar koruyan [AKS kümesini durdurmaktır](start-stop-cluster.md#stop-an-aks-cluster).

12 aydan uzun süre durdurulan kümeler artık durumu korumaz. 

AKS API 'Lerinden de ayrılmış kümelerin durum koruması garantisi yoktur. Bu durumdaki kümelerin denetim düzlemleri 30 gün sonra arşivlenecek ve 12 aydan sonra silinir.

AKS, 30 güne eşit ve daha fazla uzatılmış dönemler için destek yönergelerinden daha fazla yapılandırılmış olan denetim düzlemleri Arşivi saklı tutar. AKS, küme etcd meta verilerinin yedeklerini tutar ve kümeyi kolayca yeniden tahsis edebilir. Bu yeniden ayırma, bir yükseltme ya da etkin aracı düğümlerine ölçeklendirilmesi gibi, kümeyi tekrar desteğe getiren herhangi bir PUT işlemi tarafından başlatılabilir.

Aboneliğiniz askıya alındı veya silinirse, kümenizin denetim düzlemi ve durumu 90 gün sonra silinir.

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Desteklenmeyen Alfa ve Beta Kubernetes özellikleri

AKS yalnızca yukarı akış Kubernetes projesi içindeki kararlı ve Beta özelliklerini destekler. Aksi belirtilmedikçe, AKS, yukarı akış Kubernetes projesinde bulunan herhangi bir Alfa özelliğini desteklemez.

## <a name="preview-features-or-feature-flags"></a>Önizleme özellikleri veya özellik bayrakları

Genişletilmiş test ve Kullanıcı geri bildirimi gerektiren özellikler ve işlevler için, Microsoft yeni Önizleme özellikleri veya özellik bayrağının arkasında Özellikler yayınlar. Bu özellikleri, ön sürüm veya beta özellikleri olarak değerlendirin.

Önizleme özellikleri veya özellik bayrağı özellikleri üretime yönelik değildir. API 'lerde ve davranıştaki devam eden değişiklikler, hata düzeltmeleri ve diğer değişiklikler kararsız kümeler ve kapalı kalma süresine yol açabilir.

Genel önizlemede bulunan özellikler, bu özellikler önizleme aşamasındadır ve üretime yönelik değildir ve yalnızca iş saatleri boyunca AKS teknik destek ekipleri tarafından desteklenmeye yöneliktir. Daha fazla bilgi için bkz.

* [Azure desteği SSS](https://azure.microsoft.com/support/faq/)

## <a name="upstream-bugs-and-issues"></a>Yukarı akış hataları ve sorunları

Yukarı akış Kubernetes projesinde geliştirme hızı verildiğinde, hatalar bağımsız olarak ortaya çıkar. Bu hataların bazıları, AKS sisteminde düzeltme eki uygulanabilir veya bu hataların içinde çalışılmaz. Bunun yerine, hata düzeltmeleri yukarı akış projelerine (Kubernetes, düğüm veya aracı işletim sistemleri ve çekirdek gibi) daha fazla düzeltme eki gerektirir. Microsoft 'un sahip olduğu bileşenler (örneğin, Azure bulut sağlayıcısı), AKS ve Azure personeli, topluluk içindeki sorunları gidermeye yönelik olarak işlenir.

Teknik destek sorunu bir veya daha fazla yukarı akış hatasından kaynaklandığında, AKS desteği ve mühendislik ekipleri şunları yapar:

* Bu sorunun kümenizi veya iş yükünüzü neden etkilediğinin açıklanmasına yardımcı olmak için, tüm destekleyici ayrıntılarla yukarı akış hatalarını belirleyip bağlayın. Müşteriler, sorunları izlemek ve yeni bir sürümün düzeltmeler sağlayabilmesi için gerekli depoların bağlantılarını alırlar.
* Olası geçici çözümler veya hafifletme sağlar. Sorun azaltılyabilirse, bilinen bir [sorun](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) aks deposunda dosyalanır. Bilinen sorun sorunu şunları açıklar:
  * Bu sorun, yukarı akış hatalarının bağlantıları da dahil olmak üzere.
  * Geçici çözüm ve bir yükseltme veya çözümün başka bir kalıcılığı ile ilgili ayrıntılar.
  * Yukarı akış sürümü temposunda temel alınarak sorunun dahil edilmesi için kaba zaman çizelgeleri.
