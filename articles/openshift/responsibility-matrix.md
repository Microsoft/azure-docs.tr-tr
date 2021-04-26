---
title: Azure Red Hat OpenShift sorumluluğu atama matrisi
description: Azure Red Hat OpenShift kümesi işleminin sorumluluklarına ait mülkiyet hakkında bilgi edinin
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 4/12/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: Aro, OpenShift, az Aro, Red hat, CLI, KCı, destek
ms.openlocfilehash: 4bb00cb533d0065a992831f09ed8280c96efcdee
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537005"
---
# <a name="overview-of-responsibilities-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift için sorumluluklara genel bakış

Bu belgede, Azure Red Hat OpenShift kümeleri için Microsoft, Red Hat ve müşterilerinin sorumlulukları özetlenmektedir. Azure Red Hat OpenShift ve bileşenleri hakkında daha fazla bilgi için bkz. Azure Red Hat OpenShift hizmet tanımı.

Azure Red Hat OpenShift hizmetini Microsoft ve Red Hat ile yönetirken, müşteri, kümesinin işlevselliğine yönelik sorumluluğu paylaşır. Azure Red Hat OpenShift kümeleri, müşteri Azure aboneliklerinde Azure kaynakları üzerinde barındırılırken, uzaktan erişilir. Temel alınan platform ve veri güvenliği Microsoft ve Red Hat 'e aittir.

## <a name="overview"></a>Genel Bakış
<table>
  <tr>
   <td><strong>Kaynak</strong>
   </td>
   <td><strong><a href="#incident-and-operations-management">Olay ve Işlem yönetimi</a></strong>
   </td>
   <td><strong><a href="#change-management">Değişiklik yönetimi</a></strong>
   </td>
   <td><strong><a href="#identity-and-access-management">Kimlik ve erişim yönetimi</a></strong>
   </td>
   <td><strong><a href="#security-and-regulation-compliance">Güvenlik ve düzenleme uyumluluğu</a></strong>
   </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Müşteri verileri</a>
   </td>
   <td>Müşteri </td>
   <td>Müşteri </td>
   <td>Müşteri </td>
   <td>Müşteri </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Müşteri Uygulamaları</a>
   </td>
   <td>Müşteri </td>
   <td>Müşteri </td>
   <td>Müşteri </td>
   <td>Müşteri </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Geliştirici Hizmetleri </a>
   </td>
   <td>Müşteri </td>
   <td>Müşteri </td>
   <td>Müşteri </td>
   <td>Müşteri </td>
  </tr>
  <tr>
   <td>Platform izleme </td>
   <td>Microsoft ve Red Hat </td>
   <td>Microsoft ve Red Hat </td>
   <td>Microsoft ve Red Hat </td>
   <td>Microsoft ve Red Hat </td>
  </tr>
  <tr>
   <td>Günlüğe Kaydetme </td>
   <td>Microsoft ve Red Hat </td>
   <td>Paylaşılan </td>
   <td>Paylaşılan </td>
   <td>Paylaşılan </td>
  </tr>
  <tr>
   <td>Uygulama Ağı </td>
   <td>Paylaşılan </td>
   <td>Paylaşılan </td>
   <td>Paylaşılan </td>
   <td>Microsoft ve Red Hat </td>
  </tr>
  <tr>
   <td>Küme ağı </td>
   <td>Microsoft ve Red Hat </td>
   <td>Paylaşılan </td>
   <td>Paylaşılan </td>
   <td>Microsoft ve Red Hat </td>
  </tr>
  <tr>
   <td>Sanal ağ </td>
   <td>Paylaşılan </td>
   <td>Paylaşılan </td>
   <td>Paylaşılan </td>
   <td>Paylaşılan </td>
  </tr>
  <tr>
   <td>Denetim düzlemi düğümleri </td>
   <td>Microsoft ve Red Hat </td>
   <td>Microsoft ve Red Hat </td>
   <td>Microsoft ve Red Hat </td>
   <td>Microsoft ve Red Hat </td>
  </tr>
  <tr>
   <td>Çalışan düğümleri </td>
   <td>Microsoft ve Red Hat </td>
   <td>Microsoft ve Red Hat </td>
   <td>Microsoft ve Red Hat </td>
   <td>Microsoft ve Red Hat </td>
  </tr>
  <tr>
   <td>Küme sürümü </td>
   <td>Microsoft ve Red Hat </td>
   <td>Paylaşılan </td>
   <td>Microsoft ve Red Hat </td>
   <td>Microsoft ve Red Hat </td>
  </tr>
  <tr>
   <td>Kapasite Yönetimi </td>
   <td>Microsoft ve Red Hat </td>
   <td>Paylaşılan </td>
   <td>Microsoft ve Red Hat </td>
   <td>Microsoft ve Red Hat </td>
  </tr>
  <tr>
   <td>Sanal depolama </td>
   <td>Microsoft ve Red Hat </td>
   <td>Microsoft ve Red Hat </td>
   <td>Microsoft ve Red Hat </td>
   <td>Microsoft ve Red Hat </td>
  </tr>
  <tr>
   <td>Fiziksel altyapı ve güvenlik </td>
   <td>Microsoft ve Red Hat </td>
   <td>Microsoft ve Red Hat </td>
   <td>Microsoft ve Red Hat </td>
   <td>Microsoft ve Red Hat </td>
  </tr>
</table>


Tablo 1. Kaynağa göre sorumluluklar


## <a name="tasks-for-shared-responsibilities-by-area"></a>Alana göre paylaşılan sorumlulukların görevleri 

### <a name="incident-and-operations-management"></a>Olay ve işlem yönetimi 

Müşteri ve Microsoft ve Red hat, bir Azure Red Hat OpenShift kümesi izleme ve bakımı için sorumluluğu paylaşır. Müşteri, müşteri [uygulama verilerinin](#customer-data-and-applications) olay ve işlem yönetiminden ve müşterinin yapılandırmış olabileceği tüm özel ağlarla sorumludur.

<table>
  <tr>
   <td><strong>Kaynak</strong>
   </td>
   <td><strong>Microsoft ve Red Hat sorumlulukları</strong>
   </td>
   <td><strong>Müşteri sorumlulukları</strong>
   </td>
  </tr>
  <tr>
   <td>Uygulama Ağı </td>
   <td>
<ul>

<li>Bulut yük dengeleyiciler ve yerel OpenShift yönlendirici hizmetini izleyin ve uyarılara yanıt verin.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Hizmet yük dengeleyici uç noktalarının durumunu izleyin.

<li>Uygulama yollarının ve bunların arkasındaki uç noktaların durumunu izleyin.

<li>Microsoft ve Red Hat 'e kesintiler bildirin.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Sanal ağ
   </td>
   <td>
<ul>

<li>Varsayılan platform ağı için gereken bulut yük dengeleyiciler, alt ağlar ve Azure bulut bileşenlerini izleyin ve uyarılara yanıt verin.
</li>
</ul>
   </td>
   <td>
<ul>

<li>İsteğe bağlı olarak VNet 'ten VNet 'e bağlantı, VPN bağlantısı veya olası sorunlar veya güvenlik tehditleri için özel bağlantı bağlantısı ile yapılandırılan ağ trafiğini izleyin.
</li>
</ul>
   </td>
  </tr>
</table>


Tablo 2. Olay ve operasyon yönetimi için paylaşılan sorumluluklar


### <a name="change-management"></a>Değişiklik yönetimi

Microsoft ve Red hat, müşterinin denetleyebileceği küme altyapısında ve hizmetlerinde değişiklik yapmaktan ve ana düğümler, altyapı hizmetleri ve çalışan düğümleri için kullanılabilir sürümlerinin korunmasından sorumludur. Müşteri, altyapı değişikliklerini başlatmaktan ve isteğe bağlı hizmetler ve ağ yapılandırmalarının kümeye yüklenmesi ve bakımının yanı sıra müşteri verilerinde ve müşteri uygulamalarında yapılan tüm değişikliklere karşı sorumludur.


<table>
  <tr>
   <td><strong>Kaynak</strong>
   </td>
   <td><strong>Microsoft ve Red Hat sorumlulukları</strong>
   </td>
   <td><strong>Müşteri sorumlulukları</strong>
   </td>
  </tr>
  <tr>
   <td>Günlüğe Kaydetme </td>
   <td>
<ul>

<li>Merkezi olarak toplama ve platform denetim günlüklerini izleme.

<li>Kapsayıcılar için Azure Izleyici aracılığıyla Log Analytics kullanarak uygulama günlüğünü etkinleştirmek üzere müşterinin belgelerini sağlayın.

<li>Müşteri isteğiyle denetim günlükleri sağlayın.
</li>
</ul>
   </td>
   <td>
<ul>

<li>İsteğe bağlı varsayılan uygulama günlüğü işlecini kümeye yükler.

<li>Dışarıdan yükleme kapsayıcıları veya üçüncü taraf günlüğü uygulamalarını günlüğe kaydetme gibi isteğe bağlı uygulama günlüğü çözümlerini yükleyin, yapılandırın ve saklayın.

<li>Kümenin kararlılığını etkiledikleri takdirde, müşteri uygulamaları tarafından üretilen uygulama günlüklerinin boyut ve sıklığını ayarlayın.

<li>Belirli olayları araştırmak için bir destek talebi aracılığıyla platform denetim günlüklerini isteyin.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Uygulama Ağı
   </td>
   <td>
<ul>

<li>Genel bulut yük dengeleyicileri ayarlama

<li>Yerel OpenShift yönlendirici hizmetini ayarlayın. Yönlendiriciyi özel olarak ayarlama ve bir ek yönlendirici parça ekleme özelliği sağlar.

<li>Varsayılan iç Pod trafiği için OpenShift SDN bileşenlerini yükleyip bakımını yapın.
</li>
</ul>
   </td>
   <td>
<ul>

<li>NetworkPolicy nesnelerini kullanarak proje ve pod ağları, Pod giriş ve pod çıkış için varsayılan olmayan Pod ağ izinlerini yapılandırın.

<li>Belirli hizmetler için ek hizmet yük dengeleyicileri isteyin ve yapılandırın.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Küme ağı
   </td>
   <td>
<ul>

<li>Ortak veya özel hizmet uç noktaları gibi küme yönetimi bileşenlerini ve sanal ağ bileşenleriyle gereken tümleştirmeyi ayarlayın.

<li>Çalışan ve ana düğümler arasındaki iç küme iletişimi için gereken iç ağ bileşenlerini ayarlayın.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Küme sağlandığında OpenShift Küme Yöneticisi ile gerekirse, makine CıDR, hizmet CıDR ve pod CıDR için isteğe bağlı varsayılan olmayan IP adresi aralıklarını sağlayın.

<li>API hizmeti uç noktasının küme oluşturma sırasında veya Azure CLı aracılığıyla küme oluşturulduktan sonra genel veya özel hale getirilme isteği.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Sanal ağ
   </td>
   <td>
<ul>

<li>Sanal özel bulut, alt ağlar, yük dengeleyiciler, internet ağ geçitleri, NAT ağ geçitleri vb. dahil olmak üzere kümeyi sağlamak için gereken sanal ağ bileşenlerini ayarlayın ve yapılandırın.

<li>Müşterinin Şirket içi kaynaklarla VPN bağlantısını yönetmesine, VNet 'e VNet 'e ve OpenShift Cluster Manager aracılığıyla gereken özel bağlantı bağlantısına sahip olması için bir özellik sağlayın.

<li>Müşterilerin hizmet yük dengeleyiciler ile kullanım için genel bulut yük dengeleyiciler oluşturmasını ve dağıtmasını sağlar.
</li>
</ul>
   </td>
   <td>
<ul>

<li>VNET-VNet bağlantısı, VPN bağlantısı veya özel bağlantı bağlantısı gibi isteğe bağlı genel bulut ağ bileşenlerini ayarlayın ve saklayın.

<li>Belirli hizmetler için ek hizmet yük dengeleyicileri isteyin ve yapılandırın.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Küme sürümü
   </td>
   <td>
<ul>

<li>Küçük ve bakım sürümleri için zamanlamayı ve yükseltmelerin durumunu iletişim kurun

<li>Küçük ve bakım yükseltmeleri için Changelog 'leri ve sürüm notlarını yayımlayın
</li>
</ul>
   </td>
   <td>
<ul>

<li>Küme yükseltmesini Başlat

<li>Uyumluluk sağlamak için, küçük ve bakım sürümlerindeki müşteri uygulamalarını test edin
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Kapasite Yönetimi
   </td>
   <td>
<ul>

<li>Denetim düzlemi kullanımını izleme (ana düğümler)

<li>Hizmet kalitesini korumak için denetim düzlemi düğümlerini ölçeklendirin ve/veya yeniden boyutlandırın

<li>Ağ, depolama ve Işlem kapasitesi dahil olmak üzere müşteri kaynaklarının kullanımını izleyin. Küme kaynaklarına gereken değişiklikler için otomatik ölçeklendirme özellikleri etkin değil uyarı müşterisi (örn. ölçeklendirilen yeni işlem düğümleri, ek depolama vb.)
</li>
</ul>
   </td>
   <td>
<ul>

<li>Gerektiğinde ek çalışan düğümleri eklemek veya kaldırmak için, belirtilen OpenShift kümesi Yöneticisi denetimlerini kullanın.

<li>Küme kaynağı gereksinimleriyle ilgili Microsoft ve Red Hat bildirimlerine yanıt verin.
</li>
</ul>
   </td>
  </tr>
</table>


Tablo 3. Değişiklik yönetimi için paylaşılan sorumluluklar


### <a name="identity-and-access-management"></a>Kimlik ve Erişim Yönetimi 

Kimlik ve erişim yönetimi, yalnızca uygun kişilerin küme, uygulama ve altyapı kaynaklarına erişmesini sağlamaya yönelik tüm sorumlulukları içerir. Bu, erişim denetimi mekanizmaları, kimlik doğrulama, yetkilendirme ve kaynaklara erişimi yönetme gibi görevleri içerir.


<table>
  <tr>
   <td><strong>Kaynak</strong>
   </td>
   <td><strong>Microsoft ve Red Hat sorumlulukları</strong>
   </td>
   <td><strong>Müşteri sorumlulukları</strong>
   </td>
  </tr>
  <tr>
   <td>Günlüğe Kaydetme </td>
   <td>
<ul>

<li>Platform denetim günlükleri için endüstri standartlarına dayalı bir katmanlı iç erişim işlemine bağlı olarak.

<li>Yerel OpenShift RBAC özellikleri sağlayın.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Projelere erişimi denetlemek ve projenin uygulama günlükleri uzantısına göre OpenShift RBAC 'i yapılandırın.

<li>Üçüncü taraf veya özel uygulama günlüğü çözümleri için, müşteri erişim yönetiminden sorumludur.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Uygulama Ağı
   </td>
   <td>
<ul>

<li>Yerel OpenShift RBAC özellikleri sağlayın.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Yol yapılandırmasına erişimi gerektiği şekilde denetlemek için OpenShift RBAC 'i yapılandırın.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Küme ağı
   </td>
   <td>
<ul>

<li>Yerel OpenShift RBAC özellikleri sağlayın.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Red Hat hesaplarının Red Hat kuruluş üyeliğini yönetin.

<li>OpenShift Küme Yöneticisi 'ne erişim izni vermek için, Red Hat organizasyonu için kuruluş yöneticilerini yönetin.

<li>Yol yapılandırmasına erişimi gerektiği şekilde denetlemek için OpenShift RBAC 'i yapılandırın.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Sanal ağ
   </td>
   <td>
<ul>

<li>OpenShift Küme Yöneticisi aracılığıyla müşteri erişim denetimleri sağlayın.
</li>
</ul>
   </td>
   <td>
<ul>

<li>OpenShift Cluster Manager aracılığıyla genel bulut bileşenlerine isteğe bağlı Kullanıcı erişimini yönetin.
</li>
</ul>
   </td>
  </tr>
</table>


Tablo 4. Kimlik ve erişim yönetimi için paylaşılan sorumluluklar


### <a name="security-and-regulation-compliance"></a>Güvenlik ve düzenleme uyumluluğu 

Güvenlik ve uyumluluk, ilgili yasalar, ilkeler ve yönetmeliklerle uyumluluğu güvence altına alan tüm sorumlulukları ve denetimleri içerir.


<table>
  <tr>
   <td><strong>Kaynak</strong>
   </td>
   <td><strong>Microsoft ve Red Hat sorumlulukları</strong>
   </td>
   <td><strong>Müşteri sorumlulukları</strong>
   </td>
  </tr>
  <tr>
   <td>Günlüğe Kaydetme </td>
   <td>
<ul>

<li>Güvenlik olaylarını çözümlemek için küme denetim günlüklerini Microsoft ve Red Hat SıEM 'ye gönderin. Tanımlanan bir süre için Denetim günlüklerini koruyun ve bu da analiz analizini destekler.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Güvenlik olayları için uygulama günlüklerini çözümleyin. Varsayılan günlük yığını tarafından sunulmadan daha uzun bir saklama olması gerekiyorsa, sepet kapsayıcılarını veya üçüncü taraf günlüğü uygulamalarını günlüğe kaydederek uygulama günlüklerini bir dış uç noktaya gönderin.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Sanal ağ
   </td>
   <td>
<ul>

<li>Olası sorunlar ve güvenlik tehditleri için sanal ağ bileşenlerini izleyin.

<li>Ek izleme ve koruma için ek genel Microsoft ve Red Hat Azure araçları kullanın.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Olası sorunlar ve güvenlik tehditleri için isteğe bağlı olarak yapılandırılmış sanal ağ bileşenlerini izleyin.

<li>Gerekli güvenlik duvarı kurallarını veya veri merkezi korumalarını gereken şekilde yapılandırın.
</li>
</ul>
   </td>
  </tr>
</table>


Tablo 5. Güvenlik ve düzenleme uyumluluğu için paylaşılan sorumluluklar


## <a name="customer-responsibilities-when-using-azure-red-hat-openshift"></a>Azure Red Hat OpenShift kullanılırken müşteri sorumlulukları 


### <a name="customer-data-and-applications"></a>Müşteri verileri ve uygulamaları

Müşteri, Azure Red Hat OpenShift 'e dağıttıkları uygulamalardan, iş yüklerinden ve verilerden sorumludur. Ancak, Microsoft ve Red hat, müşterilerin platformda veri ve uygulamaları yönetmesine yardımcı olmak için çeşitli araçlar sağlar.


<table>
  <tr>
   <td><strong>Kaynak</strong>
   </td>
   <td><strong>Microsoft ve Red Hat nasıl yardımcı olur</strong>
   </td>
   <td><strong>Müşteri sorumlulukları</strong>
   </td>
  </tr>
  <tr>
   <td>Müşteri Verileri </td>
   <td>
<ul>

<li>Sektör güvenliği ve uyumluluk standartları tarafından tanımlanan veri şifreleme için platform düzeyi standartları koruyun. 

<li>Gizli dizileri gibi uygulama verilerinin yönetilmesine yardımcı olmak için OpenShift bileşenleri sağlayın.

<li>Verilerin küme dışındaki ve/veya Microsoft ve Red Hat Azure 'da depolanması ve yönetilmesi için üçüncü taraf veri hizmetleriyle (Azure SQL gibi) tümleştirmeyi etkinleştirin.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Platformda depolanan tüm müşteri verileri ve müşteri uygulamalarının bu verileri tüketme ve kullanıma sunma sorumluluğunu koruyun.

<li>Etcd şifrelemesi
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Müşteri Uygulamaları
   </td>
   <td>
<ul>

<li>Müşterilerin Kapsayıcılı uygulamaları dağıtmak ve yönetmek için OpenShift ve Kubernetes API 'Lerine erişebilmesi için OpenShift bileşenleri yüklüyken kümeler sağlayın.

<li>Bir müşterinin, kümeye topluluk, üçüncü taraf, Microsoft ve Red Hat ve Red hat hizmetleri eklemek üzere Işleçler ayarlamak için kullanabileceği OpenShift API 'Lerine erişim sağlama. 

<li>Müşteri uygulamalarıyla kullanılmak üzere kalıcı birimleri desteklemek için depolama sınıfları ve eklentileri sağlayın.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Müşterinin ve üçüncü taraf uygulamaların, verilerin ve bunların tam yaşam döngüsünün sorumluluğunu koruyun.

<li>Bir müşteri, Işleçler veya dış görüntüler kullanarak bir Red hat, topluluk, üçüncü taraf, kendi veya diğer hizmetlerini kümeye eklerse, müşteri bu hizmetlerden sorumludur ve ilgili sağlayıcıyla (Red Hat dahil) çalışarak sorunları gidermeye çalışır.

<li><a href="https://docs.openshift.com/aro/4/architecture/understanding-development.html#application-types">Yapılandırmak ve dağıtmak</a>için sunulan araçları ve özellikleri kullanın; güncel <a href="https://docs.openshift.com/aro/4/applications/deployments/deployment-strategies.html">tutun</a>; <a href="https://docs.openshift.com/aro/4/applications/working-with-quotas.html">kaynak isteklerini ve sınırlarını ayarlayın</a>; <a href="https://docs.openshift.com/aro/4/getting_started/scaling-your-cluster.html">kümeyi, uygulamaları çalıştırmak için yeterli kaynağa sahip olacak şekilde boyutlandırın</a>; <a href="https://docs.openshift.com/aro/4/administering_a_cluster/">izinleri ayarlama</a>; diğer hizmetlerle tümleştirin; <a href="https://docs.openshift.com/aro/4/openshift_images/images-understand.html">müşterinin dağıttığı tüm görüntü akışlarını veya şablonlarını yönetin</a>; <a href="https://docs.openshift.com/aro/4/cloud_infrastructure_access">dışarıdan hizmeti</a>; verileri kaydetme, yedekleme ve geri yükleme; ve bunların yüksek oranda kullanılabilir ve dayanıklı iş yüklerini yönetin.

<li>Azure Red Hat OpenShift üzerinde çalışan uygulamaların izlenmesi sorumluluğunu koruyun; ölçümleri toplamak ve uyarı oluşturmak için yükleme ve işletim yazılımı dahil.
</li>
</ul>
   </td>
  </tr>
</table>


Tablo 7. Müşteri verileri, müşteri uygulamaları ve hizmetler için müşteri sorumlulukları
