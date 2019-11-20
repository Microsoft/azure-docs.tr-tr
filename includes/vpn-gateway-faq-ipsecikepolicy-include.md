---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/06/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 345822847ddd60794cd912ccb52c14f6e240cd66
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075400"
---
### <a name="is-custom-ipsecike-policy-supported-on-all-azure-vpn-gateway-skus"></a>Özel IPsec/IKE ilkesi tüm Azure VPN Gateway SKU’larında desteklenir mi?
Özel IPSec/ıKE ilkesi, temel SKU dışında tüm Azure SKU 'Larında desteklenir.

### <a name="how-many-policies-can-i-specify-on-a-connection"></a>Bir bağlantıda kaç tane ilke belirtebilirim?
Belirli bir bağlantı için yalnızca ***bir*** ilke birleşimi belirtebilirsiniz.

### <a name="can-i-specify-a-partial-policy-on-a-connection-for-example-only-ike-algorithms-but-not-ipsec"></a>Bir bağlantıda kısmi bir ilke belirtebilir miyim? (örneğin IPsec olmadan yalnızca IKE algoritmaları)
Hayır, hem IKE (Ana Mod) hem de IPsec (Hızlı Mod) için tüm algoritmaları ve parametreleri belirtmeniz gerekir. Kısmi ilke belirtimine izin verilmez.

### <a name="what-are-the-algorithms-and-key-strengths-supported-in-the-custom-policy"></a>Özel ilkede desteklenen algoritmalar ve anahtar güçleri nelerdir?
Aşağıdaki tabloda, müşteriler tarafından yapılandırılabilecek şifreleme algoritmaları ve anahtar güçleri listelenmiştir. Her alan için bir seçeneği belirlemeniz gerekir.

| **IPsec/IKEv2**  | **Seçenekler**                                                                   |
| ---              | ---                                                                           |
| IKEv2 Şifrelemesi | AES256, AES192, AES128, DES3, DES                                             |
| IKEv2 Bütünlüğü  | SHA384, SHA256, SHA1, MD5                                                     |
| DH Grubu         | DHGroup24, ECP384, ECP256, DHGroup14 (DHGroup2048), DHGroup2, DHGroup1, Hiçbiri |
| IPsec Şifrelemesi | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, None      |
| IPsec Bütünlüğü  | GCMAES256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                            |
| PFS Grubu        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Hiçbiri                              |
| QM SA Yaşam Süresi   | Saniye (tamsayı; **en az 300**/varsayılan 27000 saniye)<br>Kilobayt (tamsayı; **en az 1024**/varsayılan 102400000 kilobayt)           |
| Trafik Seçicisi | UsePolicyBasedTrafficSelectors ($True/$False; varsayılan $False)                 |
|                  |                                                                               |

> [!IMPORTANT]
> 1. DHGroup2048 ve PFS2048, IKE ve IPsec PFS’de Diffie-Hellman Grubu **14** ile aynıdır. Eşlemelerin tamamı için [Diffie-Hellman Grupları](#DH) konusuna bakın.
> 2. GCMAES algoritmalarında, hem IPsec Şifrelemesi hem de Bütünlüğü için aynı GCMAES algoritmasını belirtmelisiniz.
> 3. Ikev2 ana mod SA yaşam süresi, Azure VPN ağ geçitlerinde 28.800 saniye içinde düzeltilir.
> 4. QM SA Yaşam Süreleri isteğe bağlı parametrelerdir. Hiçbiri belirtilmemişse, varsayılan 27.000 saniye (7,5 saat) ve 102400000 kilobayt (102 GB) değerleri kullanılır.
> 5. UsePolicyBasedTrafficSelector, bağlantıda bir seçenek parametresidir. "UsePolicyBasedTrafficSelectors" için bir sonraki SSS maddesine bakın.

### <a name="does-everything-need-to-match-between-the-azure-vpn-gateway-policy-and-my-on-premises-vpn-device-configurations"></a>Azure VPN ağ geçidi ilkesi ile şirket içi VPN cihazı yapılandırmalarım arasında her şey eşleşmeli mi?
Şirket içi VPN cihazı yapılandırmanızın Azure IPsec/IKE ilkesinde belirttiğiniz şu algoritmalar ve parametrelerle eşleşmesi ya da bunları içermesi gerekir:

* IKE şifreleme algoritması
* IKE bütünlük algoritması
* DH Grubu
* IPsec şifreleme algoritması
* IPsec bütünlük algoritması
* PFS Grubu
* Trafik Seçicisi ( * )

SA yaşam süreleri yalnızca yerel belirtimlerdir ve bunların eşleşmesi gerekmez.

**UsePolicyBasedTrafficSelectors** ilkesini etkinleştirirseniz, VPN cihazınızın herhangi bir noktadan herhangi bir noktaya bağlantı yerine şirket içi ağınızın (yerel ağ geçidi) tüm ön ekleri ile Azure sanal ağ ön ekleri arasındaki tüm birleşimlerle tanımlanmış, eşleşen trafik seçicilerine sahip olduğundan emin olmanız gerekir. Örneğin, şirket içi ağınızın ön ekleri 10.1.0.0/16 ve 10.2.0.0/16; sanal ağınızın ön ekleriyse 192.168.0.0/16 ve 172.16.0.0/16 şeklindeyse, aşağıdaki trafik seçicileri belirtmeniz gerekir:
* 10.1.0.0/16 <====> 192.168.0.0/16
* 10.1.0.0/16 <====> 172.16.0.0/16
* 10.2.0.0/16 <====> 192.168.0.0/16
* 10.2.0.0/16 <====> 172.16.0.0/16

Daha fazla bilgi için bkz. [Birden çok şirket içi ilke tabanlı VPN cihazını bağlama](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).

### <a name ="DH"></a>Hangi Diffie-Hellman Grupları desteklenir?
Aşağıdaki tabloda IKE (DHGroup) ve IPsec (PFSGroup) için desteklenen Diffie-Hellman Grupları listelenmiştir:

| **Diffie-Hellman Grubu**  | **DHGroup**              | **PFSGroup** | **Anahtar uzunluğu** |
| ---                       | ---                      | ---          | ---            |
| 1                         | DHGroup1                 | PFS1         | 768 bit MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024 bit MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048 bit MODP  |
| 19                        | ECP256                   | ECP256       | 256 bit ECP    |
| 20                        | ECP384                   | ECP384       | 384 bit ECP    |
| 24                        | DHGroup24                | PFS24        | 2048 bit MODP  |
|                           |                          |              |                |

Daha fazla bilgi için bkz. [RFC3526](https://tools.ietf.org/html/rfc3526) ve [RFC5114](https://tools.ietf.org/html/rfc5114).

### <a name="does-the-custom-policy-replace-the-default-ipsecike-policy-sets-for-azure-vpn-gateways"></a>Özel ilke, Azure VPN ağ geçitleri için IPsec/IKE ilke kümelerinin yerini mi alır?
Evet, bir bağlantıda özel bir ilke belirtildiğinde VPN ağ geçidi, bağlantıda hem IKE başlatıcı hem de IKE yanıtlayıcısı olarak yalnızca bu ilkeyi kullanır.

### <a name="if-i-remove-a-custom-ipsecike-policy-does-the-connection-become-unprotected"></a>Özel bir IPsec/IKE ilkesini kaldırırsam bağlantı korumasız hale mi gelir?
Hayır, bağlantı IPsec/IKE tarafından korunmaya devam eder. Bir bağlantıdan özel bir ilkeyi kaldırdığınızda, Azure VPN ağ geçidi [varsayılan IPsec/IKE teklifleri listesine](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) döner ve şirket içi VPN cihazınızla IKE el sıkışmasını yeniden başlatır.

### <a name="would-adding-or-updating-an-ipsecike-policy-disrupt-my-vpn-connection"></a>Bir IPsec/IKE ilkesinin eklenmesi veya güncelleştirilmesi, VPN bağlantımı kesintiye uğratır mı?
Evet, Azure VPN ağ geçidi tarafından mevcut bağlantı yıkılıp yeni şifreleme algoritmaları ve parametrelerle IPsec tünelini yeniden kurmak üzere IKE el sıkışması yeniden başlatılırken kısa bir kesinti (birkaç saniye) gerçekleşebilir. Kesinti süresini mümkün olduğunca azaltmak için şirket içi VPN cihazınızın da eşleşen algoritmalar ve anahtar güçleriyle yapılandırıldığından emin olun.

### <a name="can-i-use-different-policies-on-different-connections"></a>Farklı bağlantılarda farklı ilkeler kullanabilir miyim?
Evet. Özel ilkeler, bağlantı başına bir ilke temelinde uygulanır. Farklı bağlantılarda farklı IPsec/IKE ilkeleri oluşturabilir ve uygulayabilirsiniz. Bağlantıların bir alt kümesinde özel ilkeler uygulamayı da tercih edebilirsiniz. Geriye kalan bağlantılar, Azure’daki varsayılan IPsec/IKE ilke kümelerin kullanır.

### <a name="can-i-use-the-custom-policy-on-vnet-to-vnet-connection-as-well"></a>Özel ilkeyi VNet-VNet bağlantısında da kullanabilir miyim?
Evet, hem IPsec şirket içi ve dışı karışık bağlantılarda hem de Vnet-Vnet bağlantılarında özel ilke uygulayabilirsiniz.

### <a name="do-i-need-to-specify-the-same-policy-on-both-vnet-to-vnet-connection-resources"></a>Her iki VNet-VNet bağlantı kaynağında aynı ilkeyi belirtmem mi gerekir?
Evet. VNet-VNet tüneli, her iki yön için birer tane olmak üzere Azure’daki iki bağlantı kaynağından oluşur. Her iki bağlantı kaynağının da aynı ilkeye sahip olduğundan emin olun, aksi takdirde VNet-VNet bağlantısı kurulamaz.

### <a name="does-custom-ipsecike-policy-work-on-expressroute-connection"></a>Özel IPsec/IKE ilkesi ExpressRoute bağlantısında çalışır mı?
Hayır. IPsec/IKE ilkesi yalnızca Azure VPN ağ geçitleri aracılığıyla kurulan S2S VPN ve VNet-VNet bağlantılarında çalışır.

### <a name="how-do-i-create-connections-with-ikev1-or-ikev2-protocol-type"></a>Nasıl yaparım? IKEv1 veya Ikev2 protokol türüyle bağlantı oluşturulsun mu?
IKEv1 bağlantısı, temel SKU dışında tüm RouteBased VPN türü SKU 'Larında oluşturulabilir. Bağlantılar oluştururken, IKEv1 veya Ikev2 bağlantı protokolü türünü belirtebilirsiniz. Bağlantı protokolü türü belirtmezseniz, Ikev2 varsayılan seçenek olarak kullanılır. Daha fazla bilgi için bkz. [PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection?) belgeleri. SKU türleri ve IKEv1/Ikev2 desteği için bkz. [ağ geçitlerini ilke tabanlı VPN cihazlarına bağlama](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).

### <a name="is-transit-between-between-ikev1-and-ikev2-connections-allowed"></a>IKEv1 ve IKEv2 bağlantıları arasında aktarım yapılmasına izin veriliyor mu?
Evet. IKEv1 ve IKEv2 bağlantıları arasındaki aktarım desteklenir.

### <a name="can-i-have-ikev1-site-to-site-connections-on-basic-skus-of-routebased-vpn-type"></a>RouteBased VPN türünün temel SKU 'Larında siteden siteye bağlantıları IKEv1 olabilir mi?
Hayır. Rota tabanlı VPN türünün temel SKU 'Ları dışındaki tüm RouteBased VPN SKU 'ları siteden siteye bağlantı için IKEv1 bağlantılarını destekler.

### <a name="can-i-change-the-connection-protocol-type-after-the-connection-is-created-ikev1-to-ikev2-and-vice-versa"></a>Bağlantı oluşturulduktan sonra bağlantı protokolü türünü değiştirebilir miyim (Ikev2 'e IKEv1 ve tam tersi)?
Hayır. Bağlantı oluşturulduktan sonra, IKEv1/Ikev2 protokolleri değiştirilemez. İstenen protokol türüyle yeni bir bağlantı silip yeniden oluşturmanız gerekir.

### <a name="where-can-i-find-more-configuration-information-for-ipsec"></a>IPSec için daha fazla yapılandırma bilgilerini nerede bulabilirim?
Bkz. [S2S veya VNET-VNET bağlantıları Için IPSec/IKE Ilkesini yapılandırma](../articles/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell.md)