---
title: SSTP 'den OpenVPN veya Ikev2 'e geçiş | Azure VPN Gateway
description: Bu makale, SSTP 'nin 128 eşzamanlı bağlantı sınırını aşmanın yollarını anlamanıza yardımcı olur.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: e2fa265e580bc0e752498284ed50e398b59423fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97657150"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>SSTP 'den OpenVPN protokolüne veya Ikev2 'e geçiş

Noktadan Siteye (P2S) VPN ağ geçidi bağlantısı, ayrı bir istemci bilgisayardan sanal ağınıza güvenli bir bağlantı oluşturmanıza olanak sağlar. P2S bağlantısı, istemci bilgisayardan başlatılarak oluşturulur. Bu makale, Kaynak Yöneticisi dağıtım modeli için geçerlidir ve OpenVPN protokolüne veya Ikev2 'e geçiş yaparak SSTP 'nin 128 eşzamanlı bağlantı sınırını aşmanın yollarını ele alınmaktadır.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>P2S hangi protokol kullanır?

Noktadan siteye VPN aşağıdaki protokollerden birini kullanabilir:

* **OpenVPN &reg; Protokol**, SSL/TLS tabanlı VPN protokolü. Çoğu güvenlik duvarı, SSL tarafından kullanılan TCP bağlantı noktası 443 giden ' ü açık olduğundan, bir SSL VPN çözümü, güvenlik duvarlarını sızma edebilir. OpenVPN, Android, iOS (sürüm 11,0 ve üzeri), Windows, Linux ve Mac cihazları (OSX sürümleri 10,13 ve üzeri) ile bağlantı kurmak için kullanılabilir.

* Özel bir SSL tabanlı VPN protokolü olan **Güvenli Yuva Tünel Protokolü (SSTP)**. Çoğu güvenlik duvarı, SSL tarafından kullanılan TCP bağlantı noktası 443 giden ' ü açık olduğundan, bir SSL VPN çözümü, güvenlik duvarlarını sızma edebilir. SSTP yalnızca Windows cihazlarında desteklenir. Azure, SSTP (Windows 7 ve üzeri) olan tüm Windows sürümlerini destekler. **SSTP, yalnızca ağ geçidi SKU 'su ne olursa olsun, en fazla 128 eşzamanlı bağlantıyı destekler**.

* IKEv2 VPN, standart tabanlı bir IPsec VPN çözümüdür. IKEv2 VPN, Mac cihazlardan (OSX sürüm 10.11 ve üzeri) bağlantı kurmak için kullanılabilir.


>[!NOTE]
>P2S için Ikev2 ve OpenVPN yalnızca Kaynak Yöneticisi dağıtım modelinde kullanılabilir. Klasik dağıtım modeli için kullanılamaz. Temel ağ geçidi SKU 'SU Ikev2 veya OpenVPN protokollerini desteklemez. Temel SKU kullanıyorsanız, bir üretim SKU 'SU sanal ağ geçidini silip yeniden oluşturmanız gerekir.
>

## <a name="migrating-from-sstp-to-ikev2-or-openvpn"></a>SSTP 'den Ikev2 'e veya OpenVPN 'e geçiş

VPN Gateway 'e 128 'den fazla eşzamanlı P2S bağlantısı desteklemek istediğinizde, ancak SSTP kullanılıyorsa, bu durumlar olabilir. Böyle bir durumda, Ikev2 veya OpenVPN protokolüne taşımanız gerekir.

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>Seçenek 1-ağ geçidinde SSTP 'ye ek olarak Ikev2 ekleme

Bu en basit seçenektir. SSTP ve IKEv2 aynı ağ geçidinde bulunabilir ve size daha fazla sayıda eş zamanlı bağlantı sağlar. Mevcut ağ geçidinde Ikev2 'yi etkinleştirebilir ve istemciyi yeniden indirebilirsiniz.

Ikev2 'yi var olan SSTP VPN ağ geçidine eklemek mevcut istemcileri etkilemez ve bunları küçük toplu işlerle Ikev2 kullanacak şekilde yapılandırabilir veya yeni istemcileri Ikev2 kullanacak şekilde yapılandırabilirsiniz. Bir Windows istemcisi hem SSTP hem de IKEv2 için yapılandırılmışsa, önce ıKEV2 kullanarak bağlanmaya çalışır ve bu başarısız olursa SSTP 'ye geri döner.

**Ikev2 standart olmayan UDP bağlantı noktalarını kullanır, bu nedenle bu bağlantı noktalarının kullanıcının güvenlik duvarında engellenmediğinden emin olmanız gerekir. Kullanımdaki bağlantı noktaları UDP 500 ve 4500 ' dir.**

Mevcut bir ağ geçidine Ikev2 eklemek için, portalda sanal ağ geçidinin altındaki "Noktadan siteye yapılandırma" sekmesine gidin ve açılan kutudan **Ikev2 ve SSTP (SSL)** öğesini seçin.

!["Tünel türü" açılan, açık ve "Ikev2 ve SSTP (SSL)" seçiliyken "Noktadan siteye yapılandırma" sayfasını gösteren ekran görüntüsü.](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>Seçenek 2-SSTP 'yi kaldırın ve ağ geçidinde OpenVPN 'i etkinleştirin

SSTP ve OpenVPN her ikisi de TLS tabanlı protokol olduğundan, aynı ağ geçidinde birlikte bulunamaz. SSTP 'den OpenVPN 'e taşımaya karar verirseniz, SSTP 'yi devre dışı bırakmanız ve ağ geçidinde OpenVPN 'i etkinleştirmeniz gerekir. Bu işlem, yeni profil istemcide yapılandırılana kadar mevcut istemcilerin VPN ağ geçidiyle bağlantı kaybetmesine neden olur.

İsterseniz, Ikev2 ile birlikte OpenVPN 'i etkinleştirebilirsiniz. OpenVPN, TLS tabanlıdır ve standart TCP 443 bağlantı noktasını kullanır. OpenVPN 'e geçiş yapmak için, portalda sanal ağ geçidi altındaki "Noktadan siteye yapılandırma" sekmesine gidin ve açılan kutudan **OpenVPN (SSL)** veya **Ikev2 ve OpenVPN (SSL)** öğesini seçin.

![Noktadan siteye](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

Ağ Geçidi yapılandırıldıktan sonra, [OpenVPN istemcilerini dağıtana ve yapılandırmadan](./vpn-gateway-howto-openvpn-clients.md), mevcut istemciler bağlanamayacak.

Windows 10 kullanıyorsanız, [Windows Için Azure VPN istemcisini](./openvpn-azure-ad-client.md#to-download-the-azure-vpn-client) de kullanabilirsiniz.


## <a name="frequently-asked-questions"></a>Sık sorulan sorular
### <a name="what-are-the-client-configuration-requirements"></a>İstemci yapılandırma gereksinimleri nelerdir?

>[!NOTE]
>Windows istemcileri için istemci cihazından Azure 'a VPN bağlantısını başlatmak üzere istemci cihazda yönetici haklarına sahip olmanız gerekir.
>

Kullanıcılar, P2S için Windows ve Mac cihazlarda yerel VPN istemcilerini kullanır. Azure, bu yerel istemciler tarafından Azure 'a bağlanmak için gereken ayarları içeren bir VPN istemcisi yapılandırma zip dosyası sağlar.

* Windows cihazlarında, VPN istemci yapılandırması kullanıcıların cihazlarına yükleyen bir yükleyici paketinden oluşur.
* Mac cihazlar için, kullanıcıların cihazlarına yükleyen mobileconfig dosyasından oluşur.

ZIP dosyası Ayrıca, Azure tarafında bu cihazların kendi profilinizi oluşturmak için kullanabileceğiniz bazı önemli ayarların değerlerini de sağlar. Bazı değerler arasında VPN ağ geçidi adresi, yapılandırılmış tünel türleri, rotalar ve ağ geçidi doğrulaması için kök sertifika bulunur.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Hangi ağ geçidi SKU 'Ları P2S VPN 'yi destekliyor?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Ağ Geçidi SKU önerileri için bkz. [VPN Gateway ayarları hakkında](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>Temel SKU, IKEv2 veya RADIUS kimlik doğrulamasını desteklemez.
>

### <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>P2S için VPN ağ geçitlerinde hangi ıKE/IPSec ilkeleri yapılandırılır?


**IKEv2**

| **Şifre** | **Bütünlük** | **PRF** | **DH Grubu** |
|--|--|--|--|
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP256 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA384 | SHA384 | GROUP_24 |
| AES256 | SHA384 | SHA384 | GROUP_14 |
| AES256 | SHA384 | SHA384 | GROUP_ECP384 |
| AES256 | SHA384 | SHA384 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_2 |

**IPsec**

| **Şifre** | **Bütünlük** | **PFS Grubu** |
|--|--|--|
| GCM_AES256 | GCM_AES256 | GROUP_NONE |
| GCM_AES256 | GCM_AES256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP256 |
| AES256 | SHA256 | GROUP_NONE |
| AES256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA1 | GROUP_NONE |

### <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>P2S için VPN ağ geçitlerinde hangi TLS ilkeleri yapılandırıldı?
**TLS**

|**İlkeler** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

### <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>P2S bağlantısı Nasıl yaparım? mı?

Bir P2S yapılandırması, çok sayıda belirli adım gerektirir. Aşağıdaki makalelerde P2S yapılandırmasına kılavuzluk eden adımlar ve VPN istemci cihazlarını yapılandırma bağlantıları bulunur:

* [P2S bağlantısı yapılandırma-RADIUS kimlik doğrulaması](point-to-site-how-to-radius-ps.md)

* [P2S Bağlantısı Yapılandırma-Azure yerel sertifikası kimlik doğrulaması](vpn-gateway-howto-point-to-site-rm-ps.md)

* [OpenVPN yapılandırması](vpn-gateway-howto-openvpn.md)

## <a name="next-steps"></a>Sonraki adımlar

* [P2S bağlantısı yapılandırma-RADIUS kimlik doğrulaması](point-to-site-how-to-radius-ps.md)

* [P2S Bağlantısı Yapılandırma-Azure yerel sertifikası kimlik doğrulaması](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN", OpenVPN Inc 'nin ticari markasıdır.**