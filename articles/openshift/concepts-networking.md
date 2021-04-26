---
title: Kavramlar-Azure Red Hat 'te OpenShift 4 üzerinde ağ diyagramı
description: Azure Red Hat OpenShift ağı için ağ diyagramı ve genel bakış
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 11/23/2020
ms.openlocfilehash: 5d69aacb6e3f25e3414aa446c4c5ae7852cabdfc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101720911"
---
# <a name="network-concepts-for-azure-red-hat-openshift-aro"></a>Azure Red Hat OpenShift (ARO) için ağ kavramları

Bu kılavuzda, OpenShift 4 kümelerinde Azure Red Hat OpenShift 'teki ağa genel bakış ve bir diyagram ve önemli uç noktaların listesi ele alınmaktadır. Core OpenShift ağ kavramları hakkında daha fazla bilgi için bkz. [Azure Red Hat OpenShift 4 ağ belgeleri](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html).

![Azure Red Hat OpenShift 4 ağ diyagramı](./media/concepts-networking/aro4-networking-diagram.png)

OpenShift 4 ' te Azure Red Hat dağıttığınızda, tüm kümeniz bir sanal ağ içinde yer alır. Bu sanal ağ içinde, her biri kendi alt ağında yaşayan ana düğümleriniz ve çalışan düğümleriniz. Her alt ağ, bir iç yük dengeleyici ve bir ortak yük dengeleyici kullanır.

## <a name="networking-components"></a>Ağ bileşenleri

Aşağıdaki listede, bir Azure Red Hat OpenShift kümesindeki önemli ağ bileşenleri ele alınmaktadır.

* **Aro-pls**
    * Bu, kümeyi yönetmek için Microsoft ve Red Hat site güvenilirliği mühendisleri tarafından kullanılan bir Azure özel bağlantı uç noktasıdır.
* **Aro-iç-lb**
    * Bu uç nokta, trafiği API sunucusuyla dengeler. Bu yük dengeleyici için ana düğümler arka uç havuzudur.
* **Aro-genel-lb**
    * API genel ise, bu uç nokta trafiği API sunucusuyla yönlendirir ve dengeler. Bu uç nokta genel bir giden IP atar ve bu sayede ana bilgisayarlar Azure Resource Manager ve rapor durumu üzerinde yeniden rapor verebilir.
* **Aro-iç**
    * Bu uç nokta iç hizmet trafiğini dengeler. Bu yük dengeleyici için çalışan düğümleri arka uç havuzudur.
    * Bu yük dengeleyici varsayılan olarak oluşturulmaz. Bu yük dengeleyici, doğru ek açıklamalarla yük dengeleyici türünde bir hizmet oluşturduğunuzda oluşturulur. Örneğin: service.beta.kubernetes.io/azure-load-balancer-internal: "true".
* **Aro-iç-lb**
    * Bu uç nokta tüm ortak trafik için kullanılır. Ve uygulama ve bir yol oluşturduğunuzda bu, giriş trafiğinin yoludur.
    * Bu yük dengeleyici Ayrıca, Azure Load Balancer giden kuralları aracılığıyla çalışan düğümlerinde çalışan herhangi bir pod 'tan çıkış Internet bağlantısını da içerir.
        * Şu anda giden kurallar yapılandırılamaz. Her düğüme 1.024 TCP bağlantı noktası ayırır.
        * DisableOutboundSnat, LB kurallarında yapılandırılmamış, bu nedenle bu ALB 'de yapılandırılmış çıkış IP 'leri genel IP olarak alabilir.
        * Önceki iki noktasının bir sonucu olarak, kısa ömürlü SNAT bağlantı noktaları eklemenin tek yolu, ARO 'ye ortak yük dengeleyici-tür hizmetleri eklemektir.
* **Aro-giden-PIP**
    * Bu uç nokta, çalışan düğümleri için genel IP (PıP) görevi görür.
    * Bu uç nokta, hizmetlerin bir Azure Red Hat OpenShift kümesinden izin verilenler listesine gelen belirli bir IP eklemesini sağlar.
* **Aro-NSG**
    * Bir hizmeti kullanıma sundığınızda, API bu ağ güvenlik grubunda bir kural oluşturarak trafik akışını ve denetim düzlemine ve düğümlerine ulaşın.
    * Varsayılan olarak, bu ağ güvenlik grubu tüm giden trafiğe izin verir. Şu anda giden trafik yalnızca Azure Red Hat OpenShift denetim düzlemi ile kısıtlanabilir.
* **Aro-controldüzlemi-NSG**
  * Bu uç nokta yalnızca ana düğümler için bağlantı noktası 6443 üzerinden trafiğe izin verir.
* **Azure Container Registry**
    * Bu, Microsoft tarafından dahili olarak sağlanmış ve kullanılan bir kapsayıcı kayıt defteridir. Bu kayıt defteri salt okunurdur ve Azure Red Hat OpenShift kullanıcıları tarafından kullanılmaya yönelik değildir.
        * Bu kayıt defteri, konak platformu görüntülerini ve küme bileşenlerini sağlar. Örneğin, kapsayıcıları izleme veya günlüğe kaydetme.
        * Bu kayıt defterine yönelik bağlantılar hizmet uç noktası üzerinden (Azure hizmetleri arasında iç bağlantı) oluşur.
        * Bu iç kayıt defteri varsayılan olarak küme dışında kullanılamaz.
* **Özel Bağlantı**
    * Kümenizin yönetilmesine yardımcı olmak için yönetim düzlemine, Microsoft ve Red Hat site güvenilirliği mühendislerine yönelik bir kümeye Ağ bağlantısının yapılmasına izin verir.

## <a name="networking-policies"></a>Ağ ilkeleri

* Giriş **: giriş** ağı Ilkesi, [OpenShift Sdn](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/about-openshift-sdn.html)'nin bir parçası olarak desteklenir. Bu ağ ilkesi varsayılan olarak etkindir ve zorlama kullanıcılar tarafından yürütülür. Giriş ağ ilkesi v1 NetworkPolicy uyumluysa, çıkış ve IpBlock türleri desteklenmez.

* **Çıkış**: çıkış ağı Ilkeleri, OpenShift 'teki [Çıkış güvenlik duvarı](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/configuring-egress-firewall.html) özelliği kullanılarak desteklenir. Ad alanı/proje başına yalnızca bir çıkış ilkesi vardır. Çıkış ilkeleri "varsayılan" ad alanında desteklenmez ve sırasıyla değerlendirilir (öncelikle en son).

## <a name="networking-basics-in-openshift"></a>OpenShift 'te Ağ temelleri

OpenShift yazılım tanımlı ağ [(SDN)](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/about-openshift-sdn.html) , kapsayıcı ağ arabirimi (CNI) belirtimine dayalı bir OpenFlow uygulamasının açık VSwitch [(OVS)](https://www.openvswitch.org/)kullanılarak bir yer paylaşımı ağını yapılandırmak için kullanılır. SDN farklı eklentileri destekler--ağ Ilkesi, OpenShift 4 üzerinde Azure Red Hat 'te kullanılan eklentidir. Tüm ağ iletişimleri SDN tarafından yönetiliyor, bu yüzden Pod 'e Pod iletişimine ulaşmak için sanal ağlarınızda ek bir yol gerekli değildir.

## <a name="networking--for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift için ağ oluşturma

Aşağıdaki ağ özellikleri Azure Red Hat OpenShift 'e özgüdür:  
* Kullanıcılar, Aro kümesini var olan bir sanal ağda oluşturabilir veya bir sanal ağı, ARO kümesini oluştururken oluşturabilir.
* Pod ve hizmet ağı Cıdrs yapılandırılabilir.
* Düğümler ve ana bilgisayarlar farklı alt ağlardır.
* Düğümler ve ana sanal ağ alt ağları en az/27 olmalıdır.
* Varsayılan Pod CıDR, 10.128.0.0/14 ' dir.
* Varsayılan hizmet CıDR, 172.30.0.0/16 ' dır.
* Pod ve hizmet ağı Cıdrs, ağınızda kullanılan diğer adres aralıklarıyla çakışmamalıdır ve kümenizin sanal ağ IP adresi aralığında olmaması gerekir.
* Pod CıDR değeri en az/18 boyutunda olmalıdır. (Pod ağı yönlendirilemeyen IP 'Ler ve yalnızca OpenShift SDN içinde kullanılır.)
* Her düğüm, IP 'Leri için ayrılan/23 alt ağı (512 IP). Bu değer değiştirilemez.
* Birden çok ağa Pod ekleyemezsiniz.
* Çıkış statik IP 'yi yapılandıramazsınız. (Bu bir OpenShift özelliğidir. Bilgi için bkz. [Çıkış IP 'lerini yapılandırma](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/assigning-egress-ips.html)).

## <a name="network-settings"></a>Ağ ayarları

Azure Red Hat OpenShift 4 kümelerinde aşağıdaki ağ ayarları kullanılabilir:

* **API görünürlüğü** - [az Aro Create komutunu](tutorial-create-cluster.md#create-the-cluster)çalıştırırken API görünürlüğünü ayarlayın.
    * "Genel"-API sunucusuna dış ağlar tarafından erişilebilir.
    * "Özel"-API sunucusu, ana alt ağdan özel bir IP atamıştır, yalnızca bağlı ağlar (eşlenen sanal ağlar, kümedeki diğer alt ağlar) kullanılarak erişilebilir. Müşterinin adına özel bir DNS bölgesi oluşturulacaktır.
* Giriş **görünürlüğü** - [az Aro Create komutunu](tutorial-create-cluster.md#create-the-cluster)çalıştırırken API görünürlüğünü ayarlayın.
    * "Genel" yollar varsayılan olarak bir genel Standart Load Balancer (Bu değiştirilebilir).
    * "Özel" yollar varsayılan olarak iç yük dengeleyiciye (bu değişebilir).

## <a name="network-security-groups"></a>Ağ güvenlik grupları
Ağ güvenlik grupları, düğümün, kullanıcılara kilitlenen kaynak grubunda oluşturulur. Ağ güvenlik grupları, düğümün NIC 'Leri üzerinde değil, doğrudan alt ağlara atanır. Ağ güvenlik grupları sabittir ve kullanıcıların bunları değiştirme izinleri yoktur.

Herkese açık bir API sunucusu ile ağ güvenlik grupları oluşturamaz ve bunları NIC 'lere atayamazsınız.

## <a name="domain-forwarding"></a>Etki alanı iletme
Azure Red Hat Openshıft, CoreDNS kullanır. Etki alanı iletme yapılandırılabilir. Kendi DNS 'nizi sanal ağlarınıza getiremezsiniz. Daha fazla bilgi için bkz. [DNS iletmeyi kullanma](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html#nw-dns-forward_dns-operator)hakkındaki belgeler.

## <a name="whats-new-in-openshift-45"></a>OpenShift 4,5 ' deki yenilikler

Openshıft 4,5 desteğiyle Azure Red Hat OpenShift birkaç önemli mimari değişiklik getirmiştir. Bu değişiklikler yalnızca OpenShift 4,5 çalıştıran yeni oluşturulan kümeler için geçerlidir. Openshıft 4,5 ' e yükseltilen mevcut kümeler, ağ mimarisine yükseltme işlemi tarafından değiştirilmeyecektir. Kullanıcıların bu yeni mimariyi kullanmak için kümelerini yeniden oluşturması gerekir.

![Azure Red Hat OpenShift 4,5 Ağ Diyagramı](./media/concepts-networking/aro-4-5-networking-diagram.png)

Yukarıdaki diyagrama dahil edilen birkaç değişiklik olduğunu fark edeceksiniz:
* Daha önce, ister API sunucusu için bir diğeri de çalışan düğüm havuzu için bir tane olmak üzere, ARO iki genel LoadBalancers kullanır. Bu mimari güncelleştirmesiyle bu, tek bir LoadBalancer kapsamında birleştirildi. 
* Karmaşıklığı azaltmak için, adanmış dış Pano IP adresi kaynakları kaldırılmıştır.
* ARO denetim düzlemi artık, ARO çalışan düğümleriyle aynı ağ güvenlik grubunu paylaşır.

Openshıft 4,5 hakkında daha fazla bilgi için [openshift 4,5 sürüm notlarına](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html)göz atın.

## <a name="next-steps"></a>Sonraki adımlar
Giden trafik hakkında daha fazla bilgi ve çıkış için Azure Red Hat OpenShift 'i destekleme hakkında daha fazla bilgi için bkz. [destek ilkeleri](support-policies-v4.md) belgeleri.
