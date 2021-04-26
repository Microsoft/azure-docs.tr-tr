---
title: 'Azure ExpressRoute: ağ mühendisleri için Azure'
description: Bu sayfa geleneksel ağ mühendislerine, ağların Azure 'da nasıl çalıştığını açıklar.
documentationcenter: na
services: networking
author: osamaz
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/25/2020
ms.author: osamaz
ms.openlocfilehash: 9a22e58e4407897fb9418cae0ba9f32408cda8e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98234214"
---
# <a name="azure-for-network-engineers"></a>Ağ mühendisleri için Azure
Altyapı oluşturmak için yönlendiriciler, anahtarlar, kablolar ve güvenlik duvarları gibi fiziksel varlıklarla ilgilenmeniz için geleneksel bir ağ mühendisi olarak. Sanal LAN (VLAN), kapsayıcı ağacı Protokolü (STP), yönlendirme protokolleri (RIP, OSPF, BGP) yapılandırdığınız bir mantıksal katmanda. Ağınızı yönetim araçları ve CLı kullanarak yönetilecektir. Bulutta ağ, ağ uç noktalarının mantıksal olduğu ve yönlendirme protokollerinin kullanımı en az olduğunda farklıdır. Azure 'da varlıkları yapılandırmak ve yönetmek için Azure Resource Manager API, Azure CLı ve PowerShell ile çalışacaksınız. Azure ağ iletişimi 'nin temel kiracılarını anlamak için ağ yolculuğuna buluta başlayacaksınız. 
## <a name="virtual-network"></a>Sanal ağ
Aşağıdan bir ağ tasarladığınızda, bazı temel bilgileri toplamanız gerekir. Bu bilgiler ana bilgisayar, ağ aygıtı, alt ağ sayısı, alt ağlar arasında yönlendirme, VLAN 'Lar gibi yalıtım etki alanları olabilir. Bu bilgiler ağ ve güvenlik cihazlarını boyutlandırmanıza ve uygulama ve Hizmetleri desteklemek için mimari oluşturmaya yardımcı olur.

Uygulamalarınızı ve hizmetlerinizi Azure 'da dağıtmayı planlarken, Azure 'da sanal ağ olarak adlandırılan bir mantıksal sınır oluşturarak başlayabilirsiniz. Bu sanal ağ bir fiziksel ağ sınırına oturum ediyor. Sanal bir ağ olduğu için fiziksel dişli gerekmez, ancak yine de IP adresleri, IP alt ağları, Yönlendirme ve ilkeler gibi mantıksal varlıklar için plan yapmanız gerekir.

Azure 'da bir sanal ağ oluşturduğunuzda, bir IP aralığı (10.0.0.0/16) ile önceden yapılandırılmıştır. Bu Aralık düzeltilmez, kendi IP aralığınızı tanımlayabilirsiniz. Hem IPv4 hem de IPv6 adres aralıklarını tanımlayabilirsiniz. Sanal ağ için tanımlanan IP aralıkları Internet 'e tanıtılmaz. IP aralığınızdan birden çok alt ağ oluşturabilirsiniz. Bu alt ağlar, sanal ağ arabirimlerine (vNIC 'ler) IP adresleri atamak için kullanılacaktır. Her alt ağdan ilk dört IP adresi ayrılır ve IP ayırması için kullanılamaz. Genel bulutta VLAN kavramı yoktur. Ancak, tanımlı alt ağlarınıza göre bir sanal ağ içinde yalıtım oluşturabilirsiniz.

Tüm sanal ağ adresi alanını çevreleyen bir büyük alt ağ oluşturabilir veya birden çok alt ağ oluşturmayı seçebilirsiniz. Ancak, bir sanal ağ geçidi kullanıyorsanız, Azure "ağ geçidi alt ağı" adlı bir alt ağ oluşturmanızı gerektirir. Azure, sanal ağ geçitleri için IP adresleri atamak üzere bu alt ağı kullanacaktır. 

## <a name="ip-allocation"></a>IP ayırma

Bir konağa bir IP adresi atadığınızda, aslında IP 'yi bir ağ arabirimi kartına (NIC) atarsınız. Azure 'da bir NIC 'ye iki tür IP adresi atayabilirsiniz:

- Genel IP adresleri-Internet ve bir sanal ağa bağlı olmayan diğer Azure kaynaklarıyla gelen ve giden (ağ adresi çevirisi (NAT) olmadan) iletişim kurmak için kullanılır. Bir NIC’ye genel IP adresi atanıp atanmayacağı isteğe bağlıdır. Genel IP adresleri Microsoft IP adresi alanına aittir.
- Özel IP adresleri-bir sanal ağ, şirket içi ağınız ve Internet (NAT ile) içinde iletişim için kullanılır. Genel IP adresi alanınızı yapılandırsanız bile, sanal ağda tanımladığınız IP adresi alanı özel olarak değerlendirilir. Microsoft bu alanı Internet 'e bildirmez. VM’ye en az bir özel IP adresi atamalısınız.

Fiziksel konaklarda veya cihazlarda olduğu gibi, bir kaynağa bir IP adresi ayıramanın iki yolu vardır. Azure 'da, varsayılan ayırma yöntemi, bir sanal makine (VM) oluşturduğunuzda veya durdurulmuş bir VM başlattığınızda bir IP adresinin ayrıldığı dinamik bir yöntemdir. VM’yi durdurduğunuzda veya sildiğinizde IP adresi serbest kalır. VM’nin IP adresinin aynı kalmasını sağlamak için ayırma yöntemini açıkça statik olarak ayarlayabilirsiniz. Bu durumda, anında bir IP adresi atanır. Adres, yalnızca VM’yi sildiğinizde veya ayırma yöntemini dinamik olarak değiştirdiğinizde serbest kalır. 

Özel IP adresleri, bir sanal ağ içinde tanımladığınız alt ağlardan ayrılır. Bir VM için IP ayırması için bir alt ağ seçersiniz. Bir VM birden çok NIC içeriyorsa, her NIC için farklı bir alt ağ seçebilirsiniz.

## <a name="routing"></a>Yönlendirme
Bir sanal ağ oluşturduğunuzda, Azure ağınız için bir yönlendirme tablosu oluşturur. Bu yönlendirme tablosu aşağıdaki yol türlerini içerir.
- Sistem yolları
- Alt ağ varsayılan yolları
- Diğer sanal ağlardan yollar
- BGP yolları
- Hizmet uç noktası yolları
- Kullanıcı tanımlı yollar (UDR)

Herhangi bir alt ağ tanımlamadan ilk kez bir sanal ağ oluşturduğunuzda, Azure yönlendirme tablosunda yönlendirme girişleri oluşturur. Bu yollara sistem yolları denir. Sistem yolları bu konumda tanımlanmıştır. Bu yolları değiştiremezsiniz. Ancak UDRs 'yi yapılandırarak sistem yollarını geçersiz kılabilirsiniz.

Bir sanal ağ içinde bir veya birden çok alt ağ oluşturduğunuzda, Azure bir sanal ağ içindeki bu alt ağlar arasında iletişimi etkinleştirmek için yönlendirme tablosunda varsayılan girişleri oluşturur. Bu yollar, Azure 'daki Kullanıcı tanımlı yollar (UDR) olan statik yollar kullanılarak değiştirilebilir.

İki sanal ağ arasında bir sanal ağ eşlemesi oluşturduğunuzda her bir adres aralığı için bir eşleme oluşturulan her bir sanal ağın adres alanı içinde bir yol eklenir.

Şirket içi ağ geçidiniz Sınır Ağ Geçidi Protokolü (BGP) yollarını bir Azure sanal ağ geçidi ile değiş tokuş ediyorsanız, şirket içi ağ geçidinden yayılan her bir rota için bir yol eklenir. Bu yollar, yönlendirme tablosunda BGP yolları olarak görünür.

Hizmete hizmet uç noktası etkinleştirdiğinizde, belirli hizmetlerin genel IP adresleri Azure tarafından yol tablosuna eklenir. Hizmet uç noktaları, bir sanal ağ içindeki tek alt ağlar için etkinleştirilir. Bir hizmet uç noktasını etkinleştirdiğinizde, rota yalnızca bu hizmete ait alt ağ için yol tablosuna eklenir. Adresler değiştiğinde Azure, yol tablosundaki adresleri otomatik olarak yönetir.

Kullanıcı tanımlı yollara özel yollar da denir. Azure 'un varsayılan sistem yollarını geçersiz kılmak veya bir alt ağın yol tablosuna ek yollar eklemek için Azure 'da UDR oluşturursunuz. Azure 'da bir yol tablosu oluşturun ve ardından yol tablosunu sanal ağ alt ağları ile ilişkilendirin.

Bir yönlendirme tablosunda bir veya daha fazla giriş varsa, Azure geleneksel yönlendiricilerle benzer en uzun ön ek eşleşmesi temelinde sonraki atlamayı seçer. Ancak, aynı adres ön ekine sahip birden fazla sonraki atlama girişi varsa Azure, yolları aşağıdaki sırayla seçer.
1. Kullanıcı tanımlı yollar (UDR)
1. BGP yolları
1. Sistem yolları

## <a name="security"></a>Güvenlik

Ağ güvenlik gruplarını kullanarak bir sanal ağdaki kaynaklara veya ağ trafiğini filtreleyebilirsiniz. Ayrıca, diğer satıcıların Azure Güvenlik Duvarı veya güvenlik duvarları gibi ağ sanal gereçlerini (NVA) de kullanabilirsiniz. Azure 'un alt ağlardan gelen trafiği nasıl yönlendirdiğini denetleyebilirsiniz. Ayrıca, kuruluşunuzdaki kimlerin sanal ağlardaki kaynaklarla çalışmasını sınırlayabilirsiniz.

Ağ güvenlik grubu (NSG), alt ağlara, NIC’lere veya her ikisine yönelik ağ trafiğine izin veren veya trafiği reddeden Erişim Denetimi Listesi (ACL) kurallarının bir listesini içerir. NSG’ler alt ağlarla veya bir alt ağa bağlı tekil NIC’lerle örnekleriyle ilişkili olabilir. NSG bir alt ağ ile ilişkili olduğunda ACL kuralları bu alt ağdaki tüm VM’ler için geçerli olur. Ayrıca, tekil bir NIC’ye yönelik trafik, bir NSG’nin doğrudan bu NIC ile ilişkilendirilmesi yoluyla sınırlanabilir.

NSG'ler iki kural kümesi içerir: gelen ve giden. Bir kurala ait öncelik her küme içinde benzersiz olmalıdır. Her kuralın protokol, kaynak ve hedef bağlantı noktası aralıkları, adres ön ekleri, trafik yönü, öncelik ve erişim türü özellikleri vardır. Tüm NSG'ler bir varsayılan kurallar kümesini içerir. Varsayılan kurallar silinemez ancak en düşük önceliğe atanmış oldukları için sizin oluşturduğunuz kurallar tarafından geçersiz kılınabilirler.

## <a name="verification"></a>Doğrulama
### <a name="routes-in-virtual-network"></a>Sanal ağdaki rotalar
Oluşturduğunuz yolların birleşimi, Azure 'un varsayılan yolları ve şirket içi ağınızdan bir Azure VPN ağ geçidi üzerinden (sanal ağınız şirket içi ağınıza bağlıysa), bir alt ağdaki tüm ağ arabirimleri için etkili yollardır). Portal, PowerShell veya CLı aracılığıyla NIC 'ye giderek bu etkin yolları görebilirsiniz.
### <a name="network-security-groups"></a>Ağ Güvenlik Grupları
Bir ağ arabirimine uygulanan etkin güvenlik kuralları, bir ağ arabirimiyle ilişkili NSG 'de bulunan kuralların toplamı ve ağ arabiriminin bulunduğu alt ağ. Portal, PowerShell veya CLı aracılığıyla NIC 'ye giderek VM 'nin ağ arabirimlerine uygulanan NSG 'lerdeki tüm etkin güvenlik kurallarını görüntüleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Sanal ağ][VNet]hakkında bilgi edinin.

[Sanal ağ yönlendirmesi][vnet-routing]hakkında bilgi edinin.

[Ağ güvenlik grupları][network-security]hakkında bilgi edinin.

<!--Link References-->
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[vnet-routing]: ../virtual-network/virtual-networks-udr-overview.md
[network-security]: ../virtual-network/network-security-groups-overview.md