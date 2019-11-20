---
title: Azure Kubernetes hizmeti (AKS) için destek ilkeleri
description: Önizleme aşamasında olan Azure Kubernetes hizmeti (AKS) destek ilkeleri, paylaşılan sorumluluk ve özellikler hakkında bilgi edinin (veya Alpha veya beta).
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: 8c1736fcd4356e0778c3b36301b4490764efab76
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240877"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Azure Kubernetes hizmeti için destek ilkeleri

Bu makalede, Azure Kubernetes Service (AKS) için teknik destek ilkeleri ve sınırlamaları hakkında ayrıntılar sağlanmaktadır. Makalede, çalışan düğüm yönetimi, yönetilen denetim düzlemi bileşenleri, üçüncü taraf açık kaynaklı bileşenler ve güvenlik ya da yama yönetimi de ayrıntılı olarak anlatılmaktadır.

## <a name="service-updates-and-releases"></a>Hizmet güncelleştirmeleri ve yayınlar

* Sürüm bilgileri için bkz. [aks sürüm notları](https://github.com/Azure/AKS/releases).
* Önizlemedeki özellikler hakkında daha fazla bilgi için bkz. [aks Önizleme özellikleri ve ilgili projeler](https://github.com/Azure/AKS/blob/master/previews.md).

## <a name="managed-features-in-aks"></a>AKS 'deki yönetilen özellikler

İşlem veya ağ bileşenleri gibi bir hizmet olarak altyapı (IaaS) bulut bileşenleri, kullanıcılara alt düzey denetimler ve özelleştirme seçenekleri erişimi sağlar. Buna karşılık, AKS, müşterilere ihtiyaç duydukları ortak yapılandırma ve yetenekler kümesini veren bir anahtar oluşturma Kubernetes dağıtımı sağlar. AKS müşterilerinin özelleştirme, dağıtım ve diğer seçeneklere sınırlı bir şekilde sahip olması gerekir. Bu müşterilerin Kubernetes kümelerini doğrudan veya yönetmesi gerekmez.

AKS ile müşteri, tam olarak yönetilen bir *Denetim düzlemi*alır. Denetim düzlemi, müşterinin çalışması ve son kullanıcılara Kubernetes kümeleri sağlaması için ihtiyaç duyacağı tüm bileşenleri ve hizmetleri içerir. Tüm Kubernetes bileşenleri Microsoft tarafından korunur ve çalıştırılır.

Microsoft, Denetim bölmesi aracılığıyla aşağıdaki bileşenleri yönetir ve izler:

* Kubelet veya Kubernetes API sunucuları
* Hizmet kalitesi (QoS), ölçeklenebilirlik ve çalışma zamanı sağlayan, etcd veya uyumlu bir anahtar-değer deposu
* DNS hizmetleri (örneğin, KUIN-DNS veya CoreDNS)
* Kubernetes proxy veya ağ

AKS tamamen yönetilen bir küme çözümüdür. Çalışan düğümleri gibi bazı bileşenler, kullanıcıların AKS kümesini korumanızda yardımcı olması gereken, *paylaşılan sorumluluğa*sahiptir. Kullanıcı girişi, örneğin bir çalışan düğümü işletim sistemi (OS) güvenlik düzeltme eki uygulamak için gereklidir.

Hizmetler, Microsoft 'un ve AKS ekibinin hizmet kullanılabilirliği ve işlevleri dağıttığı, çalıştığı ve sorumlu olduğu konusunda bir şekilde *yönetilir* . Müşteriler bu yönetilen bileşenleri değiştiremezler. Microsoft, tutarlı ve ölçeklenebilir bir kullanıcı deneyimi sağlamak için özelleştirmeyi kısıtlar. Tamamen özelleştirilebilir bir çözüm için bkz. [aks motoru](https://github.com/Azure/aks-engine).

> [!NOTE]
> AKS çalışan düğümleri Azure portal normal Azure IaaS kaynakları olarak görünür. Ancak bu sanal makineler özel bir Azure Kaynak grubuna dağıtılır (MC\\* önekli). AKS çalışan düğümlerini değiştirmek mümkündür. Örneğin, normal sanal makineleri değiştirdiğiniz şekilde AKS çalışan düğümlerini değiştirmek için Secure Shell (SSH) kullanabilirsiniz (ancak, temel işletim sistemi görüntüsünü değiştirebilirsiniz ve değişiklikler bir güncelleştirme veya yeniden başlatma ile devam edebilir) ve diğer Azure kaynaklarını AKS 'e ekleyebilirsiniz çalışan düğümleri. Ancak *, bant yönetimi ve özelleştirme dışında* değişiklikler yaptığınızda aks kümesi, desteklenmeyen tablo haline gelebilir. Microsoft Desteği değişiklik yapmaya yönlendirmedikleri takdirde çalışan düğümlerini değiştirmekten kaçının.

## <a name="shared-responsibility"></a>Paylaşılan sorumluluk

Bir küme oluşturulduğunda müşteri, AKS 'in oluşturduğu Kubernetes çalışan düğümlerini tanımlar. Müşteri iş yükleri bu düğümlerde yürütülür. Müşteriler, çalışan düğümlerini görüntüleyebilir ve değiştirebilir.

Müşteri kümesi düğümleri özel kodu yürüttiğinden ve hassas verileri depoladığı için Microsoft Desteği bunlara yalnızca sınırlı bir şekilde erişebilir. Microsoft Desteği, ' de oturum açamaz, içindeki komutları yürütemiyor veya Express Müşteri izni veya yardımı olmadan bu düğümlere ait günlükleri görüntüleyemez.

Çalışan düğümleri hassas olduğundan, Microsoft, arka plan yönetimini sınırlandırmaya yönelik harika bir fikir alıyor. Çoğu durumda, Kubernetes ana düğümleri, etcd ve diğer Microsoft tarafından yönetilen bileşenler başarısız olsa bile iş yükünüz çalışmaya devam edecektir. Gereksiz şekilde değiştirilen çalışan düğümleri veri ve iş yüklerinin zararına neden olabilir ve küme tarafından desteklenmeyen bir şekilde oluşturulabilir.

## <a name="aks-support-coverage"></a>AKS Destek kapsamı

Microsoft aşağıdakiler için teknik destek sağlar:

* Kubernetes hizmetinin API sunucusu gibi sağladığı ve desteklediği tüm Kubernetes bileşenlerine bağlantı.
* Kubernetes denetim düzlemi Hizmetleri 'nin yönetimi, çalışma süresi, QoS ve işlemleri (örneğin, Kubernetes Master Nodes, API Server, etcd ve Kuto-DNS).
* Etcd. Destek, olağanüstü durum planlama ve küme durumu geri yüklemesi için her 30 dakikada bir tüm etcd verilerine yönelik otomatik, saydam yedeklemeler içerir. Bu yedeklemeler doğrudan müşteriler veya kullanıcılar tarafından kullanılamaz. Veri güvenilirliğini ve tutarlılığını güvence altına alırlar.
* Kubernetes için Azure bulut sağlayıcısı sürücüsünde herhangi bir tümleştirme noktası. Bunlar, yük dengeleyiciler, kalıcı birimler veya ağ (Kubernetes ve Azure CNı) gibi diğer Azure hizmetleriyle tümleştirmeler içerir.
* Kubernetes API Server, etcd ve Kuto-DNS gibi denetim düzlemi bileşenlerinin özelleştirilmesi hakkında sorular veya sorunlar.
* Azure CNı, kubenet gibi ağ ile ilgili sorunlar veya diğer ağ erişimi ve işlevsellik sorunları. Sorunlar DNS çözümlemesi, paket kaybı, yönlendirme vb. içerebilir. Microsoft çeşitli ağ senaryolarını destekler:
  * Küme içindeki kubenet (temel) ve gelişmiş ağ (Azure CNı) ve ilişkili bileşenler
  * Diğer Azure hizmetlerine ve uygulamalarına yönelik bağlantı
  * Giriş denetleyicileri ve giriş veya yük dengeleyici yapılandırması
  * Ağ performansı ve gecikme süresi

Microsoft aşağıdakiler için teknik destek sağlamaz:

* Kubernetes 'i kullanma hakkında sorular. Örneğin, Microsoft Desteği özel giriş denetleyicileri oluşturma, uygulama iş yüklerini kullanma veya üçüncü taraf ya da açık kaynaklı yazılım paketlerini veya araçları uygulama hakkında öneri sağlamaz.
  > [!NOTE]
  > Microsoft Desteği, AKS kümesi işlevselliği, özelleştirmesi ve ayarlama (örneğin, Kubernetes işlemleri ve yordamları) konusunda öneri verebilir.
* Kubernetes denetim düzlemi kapsamında sağlanmayan veya AKS kümeleriyle dağıtılan üçüncü taraf açık kaynaklı projeler. Bu projeler, Istio, Helm, Envoy veya diğerleri içerebilir.
  > [!NOTE]
  > Microsoft, Held ve Kured gibi üçüncü taraf açık kaynaklı projeler için en iyi desteği sağlayabilir. Üçüncü taraf açık kaynak aracının, Kubernetes Azure bulut sağlayıcısıyla veya diğer AKS ile ilgili hatalarla tümleştirildiği durumlarda Microsoft, Microsoft belgelerindeki örnekleri ve uygulamaları destekler.
* Üçüncü taraf kapalı kaynaklı yazılım. Bu yazılım, güvenlik tarama araçları ve ağ aygıtlarını veya yazılımlarını içerebilir.
* Birden çok yüksek veya çok satıcılı derleme hakkında sorunlar. Örneğin, Microsoft, bir Federasyon çoğulublik bulut satıcısı çözümünü çalıştırmaya ilişkin sorunları desteklemez.
* [Aks belgelerinde](https://docs.microsoft.com/azure/aks/)listelenenler dışındaki ağ özelleştirmeleri.
  > [!NOTE]
  > Microsoft, ağ güvenlik grupları (NSG 'ler) ile ilgili sorunları ve hataları destekler. Örneğin Microsoft Desteği, bir NSG hatası veya beklenmeyen NSG veya yük dengeleyici davranışı hakkındaki soruları yanıtlayabilir.

## <a name="aks-support-coverage-for-worker-nodes"></a>Çalışan düğümleri için AKS Destek kapsamı

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>AKS çalışan düğümleri için Microsoft sorumlulukları

Microsoft ve müşterileri, Kubernetes çalışan düğümlerinin sorumluluğunu şu durumlarda paylaşır:

* Temel işletim sistemi görüntüsünün gerekli eklemeleri (izleme ve ağ aracıları gibi) vardır.
* Çalışan düğümleri işletim sistemi düzeltme eklerini otomatik olarak alır.
* Çalışan düğümlerinde çalışan Kubernetes denetim düzlemi bileşenleriyle ilgili sorunlar otomatik olarak düzeltildi. Bileşenler şunlardır:
  * Kuin-proxy
  * Kubernetes ana bileşenlerine iletişim yolları sağlayan ağ tünelleri
  * Kubelet
  * Docker veya Moby Daemon

> [!NOTE]
> Bir çalışan düğümünde, bir denetim düzlemi bileşeni çalışır durumda değilse, AKS ekibinin tek tek bileşenleri veya tüm çalışan düğümünü yeniden başlatması gerekebilir. Bu yeniden başlatma işlemleri otomatikleştirilir ve genel sorunlar için otomatik düzeltme sağlar. Bu önyüklemeler, bir acil bakım veya kesinti olmadığı durumlar dışında yalnızca _düğüm_ düzeyinde oluşur.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>AKS çalışan düğümleri için müşteri sorumlulukları

Microsoft, işletim sistemi düzeyi düzeltme eklerini uygulamak için çalışan düğümlerini otomatik olarak yeniden başlatır. İşletim sistemi düzeltme ekleri çalışan düğümlerine teslim edilse de, *Müşteri* , değişiklikleri uygulamak için çalışan düğümlerin yeniden başlatılması sorumludur. Daemon 'ları, katı hal karma sürücü (SSHD) gibi paylaşılan kitaplıklar ve sistem veya IŞLETIM sistemi düzeyindeki diğer bileşenler otomatik olarak düzeltme eki uygulanır.

Müşteriler, Kubernetes yükseltmelerini yürütmekten sorumludur. Azure Denetim Masası veya Azure CLı aracılığıyla yükseltmeleri yürütebilirler. Bu, Kubernetes için güvenlik veya işlevsellik iyileştirmeleri içeren güncelleştirmeler için geçerlidir.

> [!NOTE]
> AKS *yönetilen bir hizmet*olduğundan, son hedefleri hizmet yönetiminin daha kapsamlı ve uygulamalı hale getirmek için düzeltme ekleri, güncelleştirmeler ve günlük toplama sorumluluğunu kaldırmayı içerir. Hizmetin uçtan uca yönetim kapasitesi arttıkça, gelecek sürümler bazı işlevleri (örneğin, düğüm yeniden başlatma ve otomatik düzeltme eki uygulama) atlayabilir.

### <a name="security-issues-and-patching"></a>Güvenlik sorunları ve düzeltme eki uygulama

AKS 'nin bir veya daha fazla bileşeninden güvenlik kusuru bulunursa, AKS ekibi, sorunu azaltmak için etkilenen tüm kümeleri yayacaktır. Alternatif olarak, takım kullanıcılara Yükseltme Kılavuzu sağlayacaktır.

Bir güvenlik kusuru etkilediği çalışan düğümleri için, sıfır kesinti yaması varsa, AKS ekibi bu düzeltme ekini uygular ve bu değişikliği kullanıcılara bildirir.

Bir güvenlik düzeltme eki çalışan düğümü yeniden başlatmaları gerektirdiğinde, Microsoft bu gereksinimin müşterilerine bildirim gönderir. Müşteri, küme düzeltme ekini almak için yeniden başlatma veya güncelleştirme sorumludur. Kullanıcılar, AKS yönergelerine göre düzeltme eklerini uygulamayamıyorsanız, bunların kümesi güvenlik sorunundan etkilenmeye devam edecektir.

### <a name="node-maintenance-and-access"></a>Düğüm bakımı ve erişimi

Çalışan düğümleri paylaşılan bir sorumluluktur ve müşterilere aittir. Bu nedenle, müşteriler, çalışan düğümlerinde oturum açma ve çekirdek güncelleştirmeleri ve paketleri yükleme veya kaldırma gibi zararlı olabilecek değişiklikler yapabilirler.

Müşteriler bozucu değişiklikler yapar veya denetim düzlemi bileşenlerinin çevrimdışı olmasına veya işlevsel olmasına neden olursa, AKS bu hatayı algılar ve çalışan düğümünü otomatik olarak önceki çalışma durumuna geri yükler.

Müşteriler, çalışan düğümlerinde oturum açıp değiştirebilse de, değişiklikler küme tarafından desteklenmeyen bir tablo yapabildiğinden bunun yapılması önerilmez.

## <a name="network-ports-access-and-nsgs"></a>Ağ bağlantı noktaları, erişim ve NSG 'ler

Yönetilen bir hizmet olarak, AKS 'nin belirli ağ ve bağlantı gereksinimleri vardır. Bu gereksinimler normal IaaS bileşenleri gereksinimlerinden daha az esnektir. AKS 'de, NSG kurallarını özelleştirme, belirli bir bağlantı noktasını engelleme (örneğin, giden bağlantı noktası 443 ' i engelleyen güvenlik duvarı kurallarını kullanma) ve beyaz listeye ekleme URL 'Leri, kümenizin desteklenemez olmasını sağlayabilir.

> [!NOTE]
> Şu anda AKS, çıkış trafiğini kümenizdeki tamamen kilitlemenize izin vermez. Kümenizin giden trafik için kullanabileceği URL 'Lerin ve bağlantı noktalarının listesini denetlemek için, [çıkış trafiğini sınırla](limit-egress-traffic.md)' ya bakın.

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Desteklenmeyen Alfa ve Beta Kubernetes özellikleri

AKS yalnızca yukarı akış Kubernetes projesi içindeki kararlı özellikleri destekler. Aksi belirtilmedikçe, AKS, yukarı akış Kubernetes projesinde bulunan Alpha ve Beta özelliklerini desteklemez.

İki senaryoda, Alfa veya beta özellikleri genel kullanıma açılmadan önce kullanıma alınmış olabilir:

* Müşteriler, AKS ürünü, destek veya mühendislik ekipleriyle karşılanır ve bu yeni özellikleri denemeleri istendi.
* Bu özellikler [bir özellik bayrağı tarafından etkinleştirildi](https://github.com/Azure/AKS/blob/master/previews.md). Müşteriler bu özellikleri kullanmak için açıkça kabul etmelidir.

## <a name="preview-features-or-feature-flags"></a>Önizleme özellikleri veya özellik bayrakları

Genişletilmiş test ve Kullanıcı geri bildirimi gerektiren özellikler ve işlevler için, Microsoft yeni Önizleme özellikleri veya özellik bayrağının arkasında Özellikler yayınlar. Bu özellikleri, ön sürüm veya beta özellikleri olarak değerlendirin.

Önizleme özellikleri veya özellik bayrağı özellikleri üretime yönelik değildir. API 'lerde ve davranıştaki devam eden değişiklikler, hata düzeltmeleri ve diğer değişiklikler kararsız kümeler ve kapalı kalma süresine yol açabilir.

Genel önizlemede bulunan özellikler, bu özellikler önizleme aşamasındadır ve üretime yönelik değildir ve yalnızca iş saatleri boyunca AKS teknik destek ekipleri tarafından desteklenmeye yöneliktir. Daha fazla bilgi için lütfen bkz.

* [Azure desteği SSS](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> Önizleme özellikleri Azure *abonelik* düzeyinde etkili olur. Önizleme özelliklerini bir üretim aboneliğine yüklemeyin. Bir üretim aboneliğinde, Önizleme özellikleri varsayılan API davranışını değiştirebilir ve normal işlemleri etkileyebilir.

## <a name="upstream-bugs-and-issues"></a>Yukarı akış hataları ve sorunları

Yukarı akış Kubernetes projesinde geliştirme hızı verildiğinde, hatalar bağımsız olarak ortaya çıkar. Bu hataların bazıları, AKS sisteminde düzeltme eki uygulanabilir veya bu hataların içinde çalışılmaz. Bunun yerine, hata düzeltmeleri yukarı akış projelerine (Kubernetes, düğüm veya çalışan işletim sistemleri ve kernels gibi) daha büyük yamalar gerektirir. Microsoft 'un sahip olduğu bileşenler (örneğin, Azure bulut sağlayıcısı), AKS ve Azure personeli, topluluk içindeki sorunları gidermeye yönelik olarak işlenir.

Teknik destek sorunu bir veya daha fazla yukarı akış hatasından kaynaklandığında, AKS desteği ve mühendislik ekipleri şunları yapar:

* Bu sorunun kümenizi veya iş yükünüzü neden etkilediğinin açıklanmasına yardımcı olmak için, tüm destekleyici ayrıntılarla yukarı akış hatalarını belirleyip bağlayın. Müşteriler, sorunları izlemek ve yeni bir sürümün düzeltmeler sağlayabilmesi için gerekli depoların bağlantılarını alırlar.
* Olası geçici çözümler veya azaltmaları sağlar. Sorun azaltılyabilirse, bilinen bir [sorun](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) aks deposunda dosyalanır. Bilinen sorun sorunu şunları açıklar:
  * Bu sorun, yukarı akış hatalarının bağlantıları da dahil olmak üzere.
  * Geçici çözüm ve bir yükseltme veya çözümün başka bir kalıcılığı ile ilgili ayrıntılar.
  * Yukarı akış sürümü temposunda temel alınarak sorunun dahil edilmesi için kaba zaman çizelgeleri.
