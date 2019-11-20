---
title: Microsoft Azure FXT Edge Filer kümesi için ağ ayarlarını ayarla
description: Azure FXT Edge Filer kümesi oluşturduktan sonra ağ ayarlarını özelleştirme
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: d250e566d884760244ee25e4c43d30fbe5323a7c
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254901"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Öğretici: kümenin Ağ ayarlarını yapılandırma 

Yeni oluşturulan bir Azure FXT Edge filigran kümesini kullanmadan önce, iş akışınız için çeşitli ağ ayarlarını denetlemeniz ve özelleştirmeniz gerekir. 

Bu öğreticide, yeni bir küme için ayarlamanız gerekebilecek ağ ayarları açıklanmaktadır. 

Şunları öğreneceksiniz: 

> [!div class="checklist"]
> * Küme oluşturulduktan sonra hangi ağ ayarlarının güncellenmesi gerekebilir
> * Hangi Azure FXT Edge filigran kullanım örnekleri bir AD sunucusu veya DNS sunucusu gerektiriyor 
> * İstemci isteklerinin yükünü otomatik olarak FXT kümesine dengelemek için hepsini bir kez deneme DNS (RRDNS) yapılandırma

Bu adımları tamamlamak için gereken süre, sisteminizde kaç yapılandırma değişikliğini gerekli hale göre değişir:

* Yalnızca öğreticiyi okumanız ve birkaç ayarı denetlemeniz gerekiyorsa, 10 ila 15 dakika sürer. 
* Hepsini bir kez deneme DNS 'yi yapılandırmanız gerekiyorsa, bu görev bir saat veya daha fazla sürebilir.

## <a name="adjust-network-settings"></a>Ağ ayarlarını ayarla

Ağ ile ilgili birkaç görev, yeni bir Azure FXT Edge Filer kümesi ayarlamanın bir parçasıdır. Bu listeyi kontrol edin ve sisteminize hangi olanların uygulanacağına karar verin.

Küme için ağ ayarları hakkında daha fazla bilgi edinmek için, küme yapılandırma kılavuzunda [ağ hizmetlerini yapılandırma](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) makalesini okuyun.

* İstemciye yönelik ağ için hepsini bir kez deneme DNS yapılandırma (isteğe bağlı)

  DNS sistemini, [FXT Edge Filer kümesi IÇIN DNS yapılandırma](#configure-dns-for-load-balancing)bölümünde açıklandığı gibi yapılandırarak Yük Dengeleme kümesi trafiği.

* NTP ayarlarını doğrulama

* Active Directory ve Kullanıcı adı/grup adı İndirmeleri yapılandırma (gerekirse)

  Ağınız Active Directory veya başka bir tür dış dizin hizmeti kullanıyorsa, kümenin Kullanıcı adı ve grup bilgilerini nasıl indirdiği ayarlamak için kümenin Dizin Hizmetleri yapılandırmasını değiştirmeniz gerekir. Ayrıntılar **Için küme yapılandırma kılavuzundaki @no__t-** 1**dizin hizmetlerini** okuyun.

  SMB desteğini istiyorsanız bir AD sunucusu gereklidir. SMB 'yi ayarlamaya başlamadan önce AD 'yi yapılandırın.

* VLAN 'Ları tanımlama (isteğe bağlı)
  
  Kümenizin sanal sunucularını ve genel ad alanını tanımlamadan önce gereken ek VLAN 'Ları yapılandırın. Daha fazla bilgi edinmek için küme yapılandırması kılavuzundaki [VLAN 'Larla çalışma](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) bölümünü okuyun.

* Proxy sunucularını yapılandırma (gerekirse)

  Kümeniz dış adreslere ulaşmak için bir proxy sunucusu kullanıyorsa, ayarlamak için aşağıdaki adımları izleyin:

  1. Ara sunucu **yapılandırma** ayarları sayfasında proxy sunucusunu tanımlama
  1. Proxy sunucusu yapılandırmasını **küme** > **genel kurulum** sayfası veya **çekirdek Filer ayrıntıları** sayfası ile uygulayın.
  
  Daha fazla bilgi için, küme yapılandırma kılavuzunda [Web proxy 'Leri kullanma](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) makalesini okuyun.

* Kümenin kullanması için [şifreleme sertifikalarını](#encryption-certificates) karşıya yükleme (isteğe bağlı)

### <a name="encryption-certificates"></a>Şifreleme sertifikaları

FXT Edge Filer kümesi, bu işlevler için X. 509.440 sertifikalarını kullanır:

* Küme yönetim trafiğini şifrelemek için

* Üçüncü taraf KMıP sunucuları adına istemci adına kimlik doğrulaması yapmak için

* Bulut sağlayıcılarının sunucu sertifikalarını doğrulamak için

Sertifikaları kümeye yüklemeniz gerekiyorsa, **küme** > **sertifikaları** ayarları sayfasını kullanın. Ayrıntılar, küme yapılandırma kılavuzunun [küme > sertifikaları](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) sayfasında bulunur.

Küme yönetimi iletişimini şifrelemek için, yönetim SSL için kullanılacak sertifikayı seçmek üzere **cluster** > **genel kurulum** ayarları sayfasını kullanın.

> [!Note] 
> Bulut hizmeti erişim anahtarları, **bulut kimlik bilgileri** yapılandırma sayfası kullanılarak depolanır. Yukarıdaki [çekirdek dosyalayıcı ekleme](fxt-add-storage.md#add-a-core-filer) bölümü bir örnek gösterir; Ayrıntılar için küme yapılandırma kılavuzu [bulut kimlik bilgileri](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) bölümünü okuyun. 

## <a name="configure-dns-for-load-balancing"></a>Yük Dengeleme için DNS yapılandırma

Bu bölümde, istemci yükünü FXT Edge Filer kümenizdeki istemciye yönelik tüm IP adresleri arasında dağıtmak üzere bir hepsini bir kez deneme DNS (RRDNS) sistemi yapılandırmanın temelleri açıklanmaktadır. 

### <a name="decide-whether-or-not-to-use-dns"></a>DNS kullanılıp kullanılmayacağını belirleyin

Yük Dengeleme her zaman önerilir, ancak DNS 'yi her zaman kullanmanız gerekmez. Örneğin, bazı tür istemci iş akışlarıyla, kümeyi bağlandıklarında istemciler arasında küme IP adreslerini eşit olarak atamak için bir komut dosyası kullanmayı daha anlamlı hale getirir. Bazı yöntemler [kümeyi bağlama](fxt-mount-clients.md)bölümünde açıklanmaktadır. 

Bir DNS sunucusunun kullanılıp kullanılmayacağını saptarken şunları göz önünde bulundurun: 

* Sisteme yalnızca NFS istemcileri eriştiğinde DNS gerekli değildir. Sayısal IP adresleri kullanarak tüm ağ adreslerini belirtmek mümkündür. 

* Sisteminiz SMB (CIFS) erişimini destekliyorsa, DNS gerekir, çünkü Active Directory sunucusu için bir DNS etki alanı belirtmeniz gerekir.

* Kerberos kimlik doğrulaması kullanmak istiyorsanız DNS gereklidir.

### <a name="round-robin-dns-configuration-details"></a>Hepsini bir kez deneme DNS yapılandırma ayrıntıları

İstemciler kümeye erişirken, RRDNS, tüm kullanılabilir arabirimler arasında isteklerini otomatik olarak dengeler.

En iyi performans için, DNS sunucunuzu aşağıdaki diyagramda gösterildiği gibi istemciye yönelik küme adreslerini işleyecek şekilde yapılandırın.

Sol tarafta bir küme vServer gösterilir ve IP adresleri ortadaki ve sağ tarafta görüntülenir. Her bir istemci erişim noktasını, gösterildiği gibi bir kayıt ve işaretçilerle yapılandırın.

![Cluster hepsini bir kez deneme DNS diyagramı-ayrıntılı alt metin bağlantısı resmi şu şekilde izler @ no__t-1[ayrıntılı metin açıklaması](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html)

Her bir istemciye yönelik IP adresinin, küme tarafından iç kullanım için benzersiz bir adı olmalıdır. (Bu diyagramda istemci IP 'Leri, açıklık için VS1-Client-IP-* olarak adlandırılır, ancak üretimde, istemci * gibi daha kısa bir ad kullanmanız gerekir.)

İstemciler, sanal sunucu adını sunucu bağımsız değişkeni olarak kullanarak kümeyi bağlayabilir. 

DNS sunucunuzun ``named.conf`` dosyasını değiştirip sanal sunucunuza sorgular için döngüsel sıra ayarlayın. Bu seçenek, tüm kullanılabilir değerlerin üzerinden kaydırılmasını sağlar. Aşağıdakine benzer bir ifade ekleyin:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

Aşağıdaki ``nsupdate`` komutları, DNS yapılandırmasına doğru bir örnek sağlar:

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

### <a name="enable-dns-in-the-cluster"></a>Kümede DNS 'yi etkinleştirme 

**Kümenin @no__t-** 1**Yönetim ağı** ayarları sayfasında kullandığı DNS sunucusunu belirtin. Bu sayfadaki ayarlar şunlardır:

* DNS sunucusu adresi
* DNS etki alanı adı
* DNS arama etki alanları

Daha fazla ayrıntı için, küme yapılandırma kılavuzundaki [DNS ayarlarını](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) okuyun.

## <a name="next-steps"></a>Sonraki adımlar

Bu, Azure FXT Edge Filer kümesi için son temel yapılandırma adımıdır. 

* [Donanım durumunu izlemek](fxt-monitor.md)Için sistemin LED 'leri ve diğer göstergeleri hakkında bilgi edinin.
* İstemcilerin [, kümeyi bağlama](fxt-mount-clients.md)bölümünde FXT Edge Filer kümesini nasıl bağlayabilmelidir hakkında daha fazla bilgi edinin. 
* Bir FXT Edge filigran kümesini çalıştırma ve yönetme hakkında daha fazla bilgi için bkz. [küme yapılandırma kılavuzu](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html). 