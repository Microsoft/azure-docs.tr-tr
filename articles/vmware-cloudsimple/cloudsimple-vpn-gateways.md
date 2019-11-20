---
title: CloudSimple-VPN ağ geçitleri tarafından Azure VMware çözümü
description: CloudSimple siteden siteye VPN ve Noktadan siteye VPN kavramları hakkında bilgi edinin
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 662fa342b3a18f726b418c496ff3fda937445301
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244777"
---
# <a name="vpn-gateways-overview"></a>VPN ağ geçitlerine genel bakış

VPN ağ geçidi, şirket içi konumdaki bir CloudSimple bölgesi ağı veya genel İnternet üzerinden bir bilgisayar arasında şifrelenmiş trafik göndermek için kullanılır.  Her bölge, birden çok bağlantıyı destekleyebilen bir VPN ağ geçidine sahip olabilir. Aynı VPN ağ geçidine birden fazla bağlantı oluşturduğunuzda, tüm VPN tünelleri kullanılabilir ağ geçidi bant genişliğini paylaşır.

CloudSimple, iki tür VPN ağ geçidi sağlar:

* Siteden siteye VPN ağ geçidi
* Noktadan siteye VPN ağ geçidi

## <a name="site-to-site-vpn-gateway"></a>Siteden siteye VPN ağ geçidi

Bir CloudSimple bölgesi ağı ile şirket içi veri merkezi arasında şifrelenmiş trafik göndermek için siteden siteye VPN ağ geçidi kullanılır. Şirket içi ağınız ve CloudSimple bölgesi ağı arasındaki ağ trafiği için alt ağları/CıDR aralığını tanımlamak üzere bu bağlantıyı kullanın.

VPN ağ geçidi, şirket içi ağdan özel bulutunuzda ve özel bulutunuzda bulunan hizmetlerden Hizmetleri kullanmanıza olanak sağlar.  CloudSimple, şirket içi ağınızdan bağlantı kurmak için ilke tabanlı bir VPN sunucusu sağlar.

Siteden siteye VPN için kullanım örnekleri:

* Şirket içi ağınızdaki herhangi bir iş istasyonundan özel bulut vCenter ' nin erişilebilirliği.
* Şirket içi Active Directory vCenter Identity kaynağı olarak kullanın.
* VM şablonlarının, IOS 'nin ve diğer dosyaların şirket içi kaynaklarınızdan özel bulut vCenter 'a uygun şekilde aktarılması.
* Şirket içi ağınızdan özel bulutunuzda çalışan iş yüklerinin erişilebilirliği.

![Siteden siteye VPN bağlantı topolojisi](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Şifreleme parametreleri

Siteden siteye VPN bağlantısı, güvenli bir bağlantı kurmak için aşağıdaki varsayılan şifreleme parametrelerini kullanır.  Şirket içi VPN cihazınızdan bir bağlantı oluşturduğunuzda, şirket içi VPN ağ geçidiniz tarafından desteklenen aşağıdaki parametrelerden birini kullanın.

#### <a name="phase-1-proposals"></a>1\. aşama teklifleri

| Parametre | Teklif 1 | Teklif 2 | Teklif 3 |
|-----------|------------|------------|------------|
| IKE sürümü | IKEv1 | IKEv1 | IKEv1 |
| Şifreleme | AES 128 | AES 256 | AES 256 |
| Karma algoritması| SHA 256 | SHA 256 | SHA 1 |
| Diffie Hellman grubu (DH grubu) | 2 | 2 | 2 |
| Yaşam süresi | 28.800 saniye | 28.800 saniye | 28.800 saniye |
| Veri boyutu | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>2\. aşama teklifleri

| Parametre | Teklif 1 | Teklif 2 | Teklif 3 |
|-----------|------------|------------|------------|
| Şifreleme | AES 128 | AES 256 | AES 256 |
| Karma algoritması| SHA 256 | SHA 256 | SHA 1 |
| Kusursuz Iletme gizliliği grubu (PFS Grubu) | Yok. | Yok. | Yok. |
| Yaşam süresi | 1\.800 saniye | 1\.800 saniye | 1\.800 saniye |
| Veri boyutu | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> VPN cihazınızda 1200 ' de TCP ve üst sınırı ayarlayın. Ya da VPN cihazlarınız, sahip olma özelliğini desteklemiyorsa, bunun yerine tünel arabirimindeki MTU değerini 1240 bayta ayarlayabilirsiniz.

## <a name="point-to-site-vpn-gateway"></a>Noktadan siteye VPN ağ geçidi

Bir CloudSimple bölgesi ağı ile istemci bilgisayar arasında şifrelenmiş trafik göndermek için Noktadan siteye VPN kullanılır.  Noktadan siteye VPN, özel bulut vCenter ve iş yükü VM 'Leri dahil olmak üzere özel bulut ağınıza erişmenin en kolay yoludur.  Özel buluta uzaktan bağlanıyorsanız Noktadan siteye VPN bağlantısı kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* [VPN ağ geçidini ayarlama](vpn-gateway.md)
