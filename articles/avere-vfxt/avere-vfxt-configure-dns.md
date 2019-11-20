---
title: Avere vFXT DNS-Azure
description: Azure için avere vFXT ile hepsini bir kez deneme yük dengelemesi için bir DNS sunucusu yapılandırma
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: c28189bf227a6a81ae9e72e889a0dc598cd7949e
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256271"
---
# <a name="avere-cluster-dns-configuration"></a>Avere kümesi DNS yapılandırması

Bu bölümde, avere vFXT kümenizin yük dengelemesi için bir DNS sisteminin yapılandırılmasına ilişkin temel bilgiler açıklanmaktadır. 

Bu belge, Azure ortamında bir DNS sunucusu ayarlamaya ve yönetmeye yönelik yönergeler *içermez* . 

Azure 'da bir vFXT kümesini yük dengelemek için hepsini bir kez deneme DNS kullanmak yerine, bağlandıkları zaman istemciler arasında IP adreslerini eşit bir şekilde atamak için el ile gerçekleştirilen yöntemleri kullanmayı düşünün. Çeşitli yöntemler [avere kümesini bağlama](avere-vfxt-mount-clients.md)bölümünde açıklanmaktadır. 

Bir DNS sunucusunun kullanılıp kullanılmayacağını saptarken şunları göz önünde bulundurun: 

* Sisteminiz yalnızca NFS istemcileri tarafından erişiliyorsa DNS kullanımı gerekli değildir-sayısal IP adresleri kullanarak tüm ağ adreslerini belirtmek mümkündür. 

* Sisteminiz SMB (CIFS) erişimini destekliyorsa, DNS gerekir, çünkü Active Directory sunucusu için bir DNS etki alanı belirtmeniz gerekir.

* Kerberos kimlik doğrulaması kullanmak istiyorsanız DNS gereklidir.

## <a name="load-balancing"></a>Yük Dengeleme

Genel yükü dağıtmak için DNS etki alanınızı, istemciye yönelik IP adresleri için hepsini bir kez deneme yük dağıtımı kullanacak şekilde yapılandırın.

## <a name="configuration-details"></a>Yapılandırma ayrıntıları

İstemciler kümeye erişirken, RRDNS, tüm kullanılabilir arabirimler arasında isteklerini otomatik olarak dengeler.

En iyi performans için, DNS sunucunuzu aşağıdaki diyagramda gösterildiği gibi istemciye yönelik küme adreslerini işleyecek şekilde yapılandırın.

Sol tarafta bir küme vServer gösterilir ve IP adresleri ortadaki ve sağ tarafta görüntülenir. Her bir istemci erişim noktasını, gösterildiği gibi bir kayıt ve işaretçilerle yapılandırın.

![Avere Cluster hepsini bir kez deneme DNS diyagramı @ no__t-1<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

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

Aşağıdaki nsupdate komutları DNS yapılandırmasına doğru bir örnek sağlar:

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

## <a name="cluster-dns-settings"></a>Küme DNS ayarları

VFXT kümesinin **küme** > **Yönetim ağı** ayarları sayfasında kullandığı DNS sunucusunu belirtin. Bu sayfadaki ayarlar şunlardır:

* DNS sunucusu adresi
* DNS etki alanı adı
* DNS arama etki alanları

Bu sayfayı kullanma hakkında daha fazla bilgi için avere kümesi yapılandırma kılavuzundaki [DNS ayarlarını](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) okuyun.


