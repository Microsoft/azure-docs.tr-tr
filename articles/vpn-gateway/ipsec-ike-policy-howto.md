---
title: 'S2S VPN & VNet-VNet bağlantıları için IPSec/ıKE ilkesi: Azure portal'
titleSuffix: Azure VPN Gateway
description: Azure Resource Manager ve Azure portal kullanarak Azure VPN ağ geçitleriyle S2S veya VNet-VNet bağlantıları için IPSec/ıKE ilkesi yapılandırın.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/18/2020
ms.author: yushwang
ms.openlocfilehash: 2b298185866d16da02fe8d3b3fdb41f0b0b1f726
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878553"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections-azure-portal"></a>S2S VPN veya VNet-VNet bağlantıları için IPSec/ıKE ilkesini yapılandırma: Azure portal

Bu makale, Azure portal kullanarak siteden siteye VPN veya VNet-VNet bağlantıları VPN Gateway için IPSec/ıKE ilkesini yapılandırma adımlarında size yol gösterir. Aşağıdaki bölümler bir IPSec/ıKE İlkesi oluşturup yapılandırmanıza ve ilkeyi yeni veya var olan bir bağlantıya uygulamanıza yardımcı olur.

## <a name="about-ipsec-and-ike-policy-parameters"></a><a name="about"></a>IPSec ve ıKE ilke parametreleri hakkında

IPSec ve ıKE protokol standardı çeşitli birleşimlerde çok sayıda şifreleme algoritmasını destekler. Bunun, uyumluluk veya güvenlik gereksinimlerinizi karşılamak üzere şirket içi ve VNet 'ten VNet 'e bağlantısının sağlanmasına nasıl yardımcı olduğunu görmek için [Şifreleme gereksinimleri ve Azure VPN ağ geçitleri hakkında](vpn-gateway-about-compliance-crypto.md) bölümüne bakın.

Bu makalede bir IPSec/ıKE İlkesi oluşturup yapılandırma ve bunu yeni veya mevcut bir VPN Gateway bağlantısına uygulama yönergeleri sağlanır.

### <a name="considerations"></a>Dikkat edilmesi gerekenler

* IPSec/ıKE ilkesi yalnızca aşağıdaki ağ geçidi SKU 'Larında kullanılabilir:
  * ***VpnGw1 ~ 5 ve VpnGw1AZ ~ 5AZ***
  * ***Standart** _ ve _ *_HighPerformance_**
* Belirli bir bağlantı için yalnızca ***bir*** ilke birleşimi belirtebilirsiniz.
* IKE (ana mod) ve IPSec (hızlı mod) için tüm algoritmaları ve parametreleri belirtmeniz gerekir. Kısmi ilke belirtimine izin verilmez.
* İlkenin şirket içi VPN cihazlarınızda desteklendiğinden emin olmak için, VPN cihazınızın satıcı belirtimlerinize başvurun. İlkeler uyumsuz ise, S2S veya VNet-VNet bağlantıları kurulamaz.

## <a name="workflow"></a><a name ="workflow"></a>İş akışı

Bu bölümde, bir S2S VPN veya VNet-VNet bağlantısında IPSec/ıKE ilkesi oluşturma ve güncelleştirme için iş akışı özetlenmektedir:

1. Bir sanal ağ ve VPN Ağ Geçidi oluşturun.
2. Şirketler arası bağlantı için yerel ağ geçidi veya VNet-VNet bağlantısı için başka bir sanal ağ ve Ağ Geçidi oluşturun.
3. Bağlantı oluşturun (IPSec veya VNet2VNet).
4. Bağlantı kaynaklarında IPSec/ıKE ilkesini yapılandırma/güncelleştirme/kaldırma.

Bu makaledeki yönergeler, diyagramda gösterildiği gibi IPSec/ıKE ilkelerini ayarlamanıza ve yapılandırmanıza yardımcı olur:

:::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="IPSec/ıKE ilke diyagramı" border="false":::

## <a name="supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>Desteklenen şifreleme algoritmaları & anahtar güçleri

### <a name="algorithms-and-keys"></a><a name ="table1"></a>Algoritmalar ve anahtarlar

Aşağıdaki tabloda, müşteriler tarafından yapılandırılabilen desteklenen şifreleme algoritmaları ve anahtar güçleri listelenmektedir:

| **IPSec/ıKE**    | **Seçenekler**    |
| ---              | ---            |
| IKE şifrelemesi   | AES256, AES192, AES128, DES3, DES                  |
| IKE bütünlüğü    | SHA384, SHA256, SHA1, MD5                          |
| DH Grubu         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None |
| IPsec Şifrelemesi | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, None    |
| IPsec Bütünlüğü  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| PFS Grubu        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Hiçbiri   |
| QM SA Yaşam Süresi   | (**Isteğe bağlı**: belirtilmemişse varsayılan değerler kullanılır)<br>Saniye (tamsayı; **en az 300**/varsayılan 27000 saniye)<br>Kilobayt (tamsayı; **en az 1024**/varsayılan 102400000 kilobayt)    |
| Trafik Seçicisi | UsePolicyBasedTrafficSelectors * * ($True/$False; **Isteğe bağlı**, varsayılan $false belirtilmemişse)    |
| DPD zaman aşımı      | Saniye (tamsayı: min. 9/Max. 3600; varsayılan 45 saniye) |
|  |  |

#### <a name="important-requirements"></a>Önemli gereksinimler

* Şirket içi VPN cihazı yapılandırmanızın Azure IPsec/IKE ilkesinde belirttiğiniz şu algoritmalar ve parametrelerle eşleşmesi ya da bunları içermesi gerekir:
  * IKE şifreleme algoritması (ana mod/Aşama 1)
  * IKE bütünlük algoritması (ana mod/Aşama 1)
  * DH grubu (ana mod/Aşama 1)
  * IPSec şifreleme algoritması (hızlı mod/aşama 2)
  * IPSec bütünlük algoritması (hızlı mod/aşama 2)
  * PFS Grubu (hızlı mod/aşama 2) > * trafik seçicisi (UsePolicyBasedTrafficSelectors kullanılıyorsa)
  * SA yaşam süreleri yalnızca yerel belirtimlerdir ve bunların eşleşmesi gerekmez.

* IPSec şifreleme algoritması için GCMAES kullanılıyorsa, IPSec bütünlüğü için aynı GCMAES algoritmasını ve anahtar uzunluğunu seçmeniz gerekir; Örneğin, her ikisi için GCMAES128 kullanma.

* Yukarıdaki [algoritmalar ve anahtarlar tablosunda](#table1) :
  * IKE, ana moda veya 1 aşamasına karşılık gelir
  * IPSec, hızlı moda veya Aşama 2 ' ye karşılık gelir
  * DH grubu, ana modda veya 1. aşamada kullanılan Diffie-Hellmen grubunu belirtir
  * PFS Grubu, Hızlı modda veya Aşama 2 ' de kullanılan Diffie-Hellmen grubunu belirtti

* IKE ana mod SA yaşam süresi, Azure VPN ağ geçitlerinde 28.800 saniye içinde düzeltilir.

* Bir bağlantıda $True için **UsePolicyBasedTrafficSelectors** ayarlarsanız, Azure VPN ağ geçidini şirket içi Ilke tabanlı VPN güvenlik duvarına bağlanacak şekilde yapılandırır. PolicyBasedTrafficSelectors ' ı etkinleştirirseniz, VPN cihazınızın, şirket içi ağ (yerel ağ geçidi) öneklerinizin tüm birleşimleri ile, Azure sanal ağ öneklerinden herhangi birine ve bunlardan herhangi birine kadar tanımlanmış olan eşleşen trafik seçicileri olduğundan emin olmanız gerekir. Örneğin, şirket içi ağınızın ön ekleri 10.1.0.0/16 ve 10.2.0.0/16; sanal ağınızın ön ekleriyse 192.168.0.0/16 ve 172.16.0.0/16 şeklindeyse, aşağıdaki trafik seçicileri belirtmeniz gerekir:
  * 10.1.0.0/16 <====> 192.168.0.0/16
  * 10.1.0.0/16 <====> 172.16.0.0/16
  * 10.2.0.0/16 <====> 192.168.0.0/16
  * 10.2.0.0/16 <====> 172.16.0.0/16

   İlke tabanlı trafik seçicileri hakkında daha fazla bilgi için bkz. [birden çok şirket içi ilke tabanlı VPN cihazlarını bağlama](vpn-gateway-connect-multiple-policybased-rm-ps.md).

* DPD zaman aşımı-Azure VPN ağ geçitlerinde varsayılan değer 45 saniyedir. Zaman aşımını daha kısa dönemler olarak ayarlamak, ıKE 'nin daha fazla kararlılığı yeniden oluşturmasına neden olur ve bu da bağlantının bazı örneklerde bağlantısının kesilmesine neden olur. Şirket içi konumlarınızın VPN ağ geçidinin bulunduğu Azure bölgesinden daha fazla olması veya fiziksel bağlantı koşulunun paket kaybına neden olabileceği, bu durum istenmeyebilir. Genel öneri, zaman aşımını **30 ila 45** saniye olarak ayarlamak.

### <a name="diffie-hellman-groups"></a>Diffie-Hellman grupları

Aşağıdaki tabloda, özel ilke tarafından desteklenen karşılık gelen Diffie-Hellman grupları listelenmektedir:

| **Diffie-Hellman Grubu**  | **DHGroup**              | **PFSGroup** | **Anahtar uzunluğu** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768 bit MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024 bit MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048 bit MODP  |
| 19                        | ECP256                   | ECP256       | 256 bit ECP    |
| 20                        | ECP384                   | ECP384       | 384 bit ECP    |
| 24                        | DHGroup24                | PFS24        | 2048 bit MODP  |

Diğer ayrıntılar için [RFC3526](https://tools.ietf.org/html/rfc3526)ve [RFC5114](https://tools.ietf.org/html/rfc5114)’e bakın.

## <a name="s2s-vpn-with-ipsecike-policy"></a><a name ="S2S"></a>IPSec/ıKE ilkesiyle S2S VPN

Bu bölüm, IPSec/ıKE ilkesiyle siteden siteye VPN bağlantısı oluşturma adımlarında size yol gösterir. Aşağıdaki adımlar, aşağıdaki diyagramda gösterildiği gibi bağlantıyı oluşturur:

:::image type="content" source="./media/ipsec-ike-policy-howto/site-to-site-diagram.png" alt-text="Siteden siteye ilkesi" border="false":::

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>1. adım-sanal ağ, VPN Gateway ve yerel ağ geçidi oluşturma

Aşağıdaki kaynakları aşağıdaki ekran görüntülerinde gösterildiği gibi oluşturun. Adımlar için bkz. [siteden sıteye VPN bağlantısı oluşturma](./tutorial-site-to-site-portal.md).

* **Sanal ağ:**  TestVNet1

   :::image type="content" source="./media/ipsec-ike-policy-howto/testvnet-1.png" alt-text="Adlı":::

* **VPN ağ geçidi:** VNet1GW

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-1-gateway.png" alt-text="Ağ geçidi":::

* **Yerel ağ geçidi:** Site6

   :::image type="content" source="./media/ipsec-ike-policy-howto/lng-site-6.png" alt-text="Site":::

* **Bağlantı:** VNet1 to Site6

    :::image type="content" source="./media/ipsec-ike-policy-howto/connection-site-6.png" alt-text="Bağlantı":::

### <a name="step-2---configure-ipsecike-policy-on-the-s2s-vpn-connection"></a><a name="s2sconnection"></a>2. adım-S2S VPN bağlantısında IPSec/ıKE ilkesini yapılandırma

Bu bölümde, aşağıdaki algoritmalar ve parametrelerle bir IPSec/ıKE ilkesi yapılandırın:

* IKE: AES256, SHA384, DHGroup24, DPD zamanaşımı 45 saniye
* IPSec: AES256, SHA256, PFS yok, SA ömür 30000 saniye ve 102400000KB

1. Azure portal bağlantı kaynağına gidin, **VNet1toSite6**. Yapılandırma **sayfası '** nı seçin ve tüm yapılandırma seçeneklerini göstermek için **özel** IPSec/IKE ilkesi ' ni seçin. Aşağıdaki ekran görüntüsünde, listeye göre yapılandırma gösterilmektedir:

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-site-6.png" alt-text="Site 6":::

1. IPSec için GCMAES kullanıyorsanız, hem IPSec şifrelemesi hem de bütünlüğü için aynı GCMAES algoritmasını ve anahtar uzunluğunu kullanmanız gerekir. Örneğin, aşağıdaki ekran görüntüsünde hem IPSec şifrelemesi hem de IPSec bütünlüğü için GCMAES128 verilmiştir:

   :::image type="content" source="./media/ipsec-ike-policy-howto/gcmaes.png" alt-text="IPSec için GCMAES":::

1. İsteğe bağlı olarak, yukarıda açıklandığı gibi, Azure VPN ağ geçidinin şirket içi ilke tabanlı VPN cihazlarına bağlanmasını sağlamak için **ilke tabanlı trafik seçicileri kullan** seçeneği için **Etkinleştir** seçeneğini belirleyebilirsiniz.

   :::image type="content" source="./media/ipsec-ike-policy-howto/policy-based-selector.png" alt-text="İlke tabanlı trafik Seçicisi":::

1. Tüm seçenekler seçildikten sonra, değişiklikleri bağlantı kaynağına uygulamak için **Kaydet** ' i seçin. İlke yaklaşık bir dakika içinde zorunlu kılınır.

> [!IMPORTANT]
>
> * Bir bağlantı üzerinde IPSec/ıKE ilkesi belirtilmişse, Azure VPN ağ geçidi yalnızca belirtilen şifreleme algoritmalarıyla IPSec/ıKE teklifini ve söz konusu bağlantıda anahtar güçlerini gönderir veya kabul eder. Bağlantı için şirket içi VPN cihazınızın, tam ilke birleşimini kullandığından veya kabul ettiğinden emin olun, aksi takdirde S2S VPN tüneli kurmaz.
>
> * **İlke tabanlı trafik Seçicisi** ve **DPD zaman aşımı** seçenekleri, yukarıdaki ekran görüntüsünde gösterildiği gıbı özel IPSec/IKE ilkesi olmadan **varsayılan** ilkeyle belirtilebilir.
>

## <a name="vnet-to-vnet-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>IPSec/ıKE ilkesiyle VNet 'ten VNet 'e

IPSec/ıKE ilkesiyle VNet-VNet bağlantısı oluşturma adımları S2S VPN bağlantısıyla benzerdir.

:::image type="content" source="./media/ipsec-ike-policy-howto/vnet-policy.png" alt-text="VNet-VNet ilke diyagramı" border="false":::

1. VNET 'ten VNET 'e bağlantı oluşturmak için VNET- [VNet bağlantısı oluşturma](vpn-gateway-vnet-vnet-rm-ps.md) makalesindeki adımları kullanın.

2. Adımları tamamladıktan sonra, VNet2GW kaynağından aşağıdaki ekran görüntüsünde gösterildiği gibi iki VNet-VNet bağlantısı görürsünüz:

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-connections.png" alt-text="Sanal Ağdan Sanal Ağa bağlantılar":::

3. Bağlantı kaynağına gidin ve portaldaki **yapılandırma** sayfasına gidin. Özel ilke seçeneklerini göstermek için **IPSec/IKE Ilkesinde** **özel** ' i seçin. Karşılık gelen anahtar uzunluklarına sahip şifreleme algoritmalarını seçin.

   Ekran görüntüsünde, aşağıdaki algoritmalara ve parametrelere sahip farklı bir IPSec/ıKE ilkesi gösterilmektedir:
   * IKE: AES128, SHA1, DHGroup14, DPD zamanaşımı 45 saniye
   * IPSec: GCMAES128, GCMAES128, PFS14, SA yaşam 14400 saniye & 102400000KB

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-policy.png" alt-text="Bağlantı ilkesi":::

4. İlke değişikliklerini bağlantı kaynağına uygulamak için **Kaydet** ' i seçin.

5. Aynı ilkeyi diğer bağlantı kaynağına uygulayın, VNet2toVNet1. Aksi takdirde, IPSec/ıKE VPN tüneli ilke uyumsuzluğu nedeniyle bağlanmayacak.

   > [!IMPORTANT]
   > Bir bağlantı üzerinde IPSec/ıKE ilkesi belirtilmişse, Azure VPN ağ geçidi yalnızca belirtilen şifreleme algoritmalarıyla IPSec/ıKE teklifini ve söz konusu bağlantıda anahtar güçlerini gönderir veya kabul eder. Her iki bağlantı için de IPSec ilkelerinin aynı olduğundan emin olun, aksi takdirde VNET-VNet bağlantısı kurmayacak.

6. Bu adımları tamamladıktan sonra bağlantı birkaç dakika içinde oluşturulur ve aşağıdaki ağ topolojisine sahip olursunuz:

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="IPSec/ıKE ilke diyagramı" border="false":::

## <a name="to-remove-custom-ipsecike-policy-from-a-connection"></a><a name ="deletepolicy"></a>Özel IPSec/ıKE ilkesini bir bağlantıdan kaldırmak için

1. Bir bağlantıdan özel bir ilkeyi kaldırmak için bağlantı kaynağına gidin ve geçerli ilkeyi görmek için **yapılandırma** sayfasına gidin.

2. **IPSec/IKE ilkesi** seçeneğinde **varsayılan** ' ı seçin. Bu işlem, bağlantıda daha önce belirtilen tüm özel ilkeyi kaldırır ve varsayılan IPSec/ıKE ayarlarını bu bağlantıda geri yükler:

   :::image type="content" source="./media/ipsec-ike-policy-howto/delete-policy.png" alt-text="İlkeyi sil":::

3. Özel ilkeyi kaldırmak ve bağlantıda varsayılan IPSec/ıKE ayarlarını geri yüklemek için **Kaydet** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

İlke tabanlı trafik seçicileri hakkında daha fazla bilgi için bkz. [birden çok şirket içi ilke tabanlı VPN cihazını bağlama](vpn-gateway-connect-multiple-policybased-rm-ps.md) .