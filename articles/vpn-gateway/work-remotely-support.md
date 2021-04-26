---
title: 'P2S ile uzak çalışma: Azure VPN Gateway'
description: Bu sayfada COVıD-19 pandemıc nedeniyle uzaktan çalışmayı etkinleştirmek üzere VPN Gateway nasıl yararlanabileceğiniz açıklanmaktadır.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: eb0291b18952efc643de18e111154ebe58f041af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880093"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Azure VPN Gateway Noktadan siteye kullanarak uzak çalışma

>[!NOTE]
>Bu makalede, Azure VPN Gateway, Azure, Microsoft ağı ve Azure iş ortağı ekosisteminden yararlanarak uzaktan nasıl çalışabileceğinizi ve COVı-19 çapraz olduğu için kullanıma sunulacak ağ sorunlarını azaltmayı nasıl kullanabileceğiniz açıklanmaktadır.
>

Bu makalede, kuruluşların kullanıcıları için uzaktan erişimi ayarlama veya var olan çözümlerini COVıD-19 epidemıc sırasında ek kapasiteye göre ekleme amacıyla sunulan seçenekler açıklanmaktadır.

Azure Noktadan siteye çözümü, bulut tabanlıdır ve kullanıcıların evden çalışması için artan talebe göre hızlı bir şekilde sağlanabilir. Artık daha fazla kapasite gerekli olmadığında kolayca ve hızlı bir şekilde ölçeklenebilir ve kapatılabilir.

## <a name="about-point-to-site-vpn"></a><a name="p2s"></a>Noktadan siteye VPN hakkında

Noktadan Siteye (P2S) VPN ağ geçidi bağlantısı, ayrı bir istemci bilgisayardan sanal ağınıza güvenli bir bağlantı oluşturmanıza olanak sağlar. P2S bağlantısı, istemci bilgisayardan başlatılarak oluşturulur. Bu çözüm, Azure sanal ağlarına veya şirket içi veri merkezlerine, evden veya bir konferanstan gibi uzak bir konumdan bağlanmak isteyen kişiler için kullanışlıdır. Bu makalede, kullanıcıların çeşitli senaryolara göre uzaktan çalışmasını sağlama açıklanır.

Aşağıdaki tabloda, istemci işletim sistemleri ve bunların kullanabildiği kimlik doğrulama seçenekleri gösterilmektedir. Zaten kullanımda olan istemci işletim sistemine dayalı kimlik doğrulama yöntemini seçmek yararlı olacaktır. Örneğin, bağlanması gereken istemci işletim sistemlerinin bir karışımını varsa sertifika tabanlı kimlik doğrulamasıyla OpenVPN ' i seçin. Ayrıca, Noktadan siteye VPN 'nin yalnızca rota tabanlı VPN ağ geçitlerinde desteklendiğini unutmayın.

![İstemci işletim sistemlerini ve kullanılabilir kimlik doğrulama seçeneklerini gösteren ekran görüntüsü.](./media/working-remotely-support/ostable.png "İşletim Sistemi")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a><a name="scenario1"></a>Senaryo 1-kullanıcıların yalnızca Azure 'da kaynaklara erişmesi gerekir

Bu senaryoda, uzak kullanıcıların yalnızca Azure 'daki kaynaklara erişmesi gerekir.

![Yalnızca Azure 'daki kaynaklara erişmesi gereken kullanıcılar için Noktadan siteye senaryo gösteren diyagram.](./media/working-remotely-support/scenario1.png "1\. Senaryo")

Yüksek düzeyde, kullanıcıların Azure kaynaklarına güvenli bir şekilde bağlanmasını sağlamak için aşağıdaki adımlar gereklidir:

1. Bir sanal ağ geçidi oluşturun (yoksa).
2. Ağ geçidinde Noktadan siteye VPN 'yi yapılandırın.

   * Sertifika kimlik doğrulaması için [Bu bağlantıyı](vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw)izleyin.
   * OpenVPN için [Bu bağlantıyı](vpn-gateway-howto-openvpn.md)izleyin.
   * Azure AD kimlik doğrulaması için [Bu bağlantıyı](openvpn-azure-ad-tenant.md)izleyin.
   * Noktadan siteye bağlantılar sorunlarını gidermek için [Bu bağlantıyı](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)izleyin.
3. VPN istemci yapılandırmasını indirin ve dağıtın.
4. İstemcilere (sertifika kimlik doğrulaması seçildiyse) sertifikaları dağıtın.
5. Azure VPN 'ye bağlanın.

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a><a name="scenario2"></a>Senaryo 2-kullanıcıların Azure ve/veya şirket içi kaynaklarda kaynaklara erişmesi gerekir

Bu senaryoda, uzak kullanıcıların Azure 'daki ve şirket içi veri merkezinde bulunan kaynaklara erişmesi gerekir.

![Azure 'daki kaynaklara erişmesi gereken kullanıcılar için Noktadan siteye senaryo gösteren diyagram.](./media/working-remotely-support/scenario2.png "2\. Senaryo")

Yüksek düzeyde, kullanıcıların Azure kaynaklarına güvenli bir şekilde bağlanmasını sağlamak için aşağıdaki adımlar gereklidir:

1. Bir sanal ağ geçidi oluşturun (yoksa).
2. Ağ geçidinde Noktadan siteye VPN 'yi yapılandırın (bkz. [Senaryo 1](#scenario1)).
3. BGP etkin olan Azure sanal ağ geçidinde siteden siteye tünel yapılandırın.
4. Şirket içi cihazı Azure sanal ağ geçidine bağlanacak şekilde yapılandırın.
5. Azure portal Noktadan siteye profilini indirin ve istemcilere dağıtın

Siteden siteye VPN tüneli ayarlama hakkında bilgi edinmek için [Bu bağlantıya](./tutorial-site-to-site-portal.md)bakın.

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Yerel Azure sertifika kimlik doğrulaması hakkında SSS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>RADIUS kimlik doğrulaması için SSS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Sonraki Adımlar

* [P2S Bağlantısı Yapılandırma-Azure AD kimlik doğrulaması](openvpn-azure-ad-tenant.md)

* [P2S bağlantısı yapılandırma-RADIUS kimlik doğrulaması](point-to-site-how-to-radius-ps.md)

* [P2S Bağlantısı Yapılandırma-Azure yerel sertifikası kimlik doğrulaması](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN", OpenVPN Inc 'nin ticari markasıdır.**