---
title: 'Kullanıcı VPN bağlantıları için Azure AD kiracısı: Azure AD kimlik doğrulaması'
description: Azure AD kimlik doğrulaması kullanarak VNet 'nize bağlanmak için Azure sanal WAN Kullanıcı VPN 'yi (Noktadan siteye) kullanabilirsiniz
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: a54397fe7cfecef6813105645b7f2b218894875e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91367861"
---
# <a name="prepare-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Azure Active Directory kiracıyı Kullanıcı VPN OpenVPN Protokolü bağlantıları için hazırla

Sanal hub 'ınıza Ikev2 protokolü üzerinden bağlanırken, sertifika tabanlı kimlik doğrulaması veya RADIUS kimlik doğrulaması kullanabilirsiniz. Ancak, OpenVPN protokolünü kullandığınızda Azure Active Directory kimlik doğrulamasını da kullanabilirsiniz. Bu makale, OpenVPN kimlik doğrulaması kullanarak sanal WAN Kullanıcı VPN (Noktadan siteye) için bir Azure AD kiracısı ayarlamanıza yardımcı olur.

> [!NOTE]
> Azure AD kimlik doğrulaması yalnızca OpenVPN &reg; Protokolü bağlantıları için desteklenir.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Azure AD kiracısı oluşturma

Bir Azure AD kiracınız olduğunu doğrulayın. Bir Azure AD kiracınız yoksa, [Yeni bir kiracı oluşturma](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) makalesindeki adımları kullanarak bir tane oluşturabilirsiniz:

* Kuruluş adı
* İlk etki alanı adı

Örnek:

   ![Yeni Azure AD kiracısı](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Azure AD kiracı kullanıcıları oluşturun

Ardından, yeni oluşturulan Azure AD kiracısında bir genel yönetici hesabı ve bir kullanıcı hesabı olmak üzere iki kullanıcı hesabı oluşturun. Kullanıcı hesabı, OpenVPN kimlik doğrulamasını test etmek için kullanılabilir ve Azure VPN uygulama kaydına onay vermek için genel yönetici hesabı kullanılır. Bir Azure AD Kullanıcı hesabı oluşturduktan sonra, yönetici izinlerini devretmek için kullanıcıya bir **Dizin rolü** atarsınız.

Azure AD kiracınız için iki kullanıcı oluşturmak üzere [Bu makaledeki](../active-directory/fundamentals/add-users-azure-active-directory.md) adımları kullanın. Oluşturulan hesaplardan birindeki **Dizin rolünü** **genel yöneticiye** değiştirdiğinizden emin olun.

## <a name="3-grant-consent-to-the-azure-vpn-app-registration"></a><a name="enable-authentication"></a>3. Azure VPN uygulama kaydına izin verin

1. Azure portalında **genel yönetici** rolüne atanan bir kullanıcı olarak oturum açın.

2. Ardından, kuruluşunuz için yönetici onayı verin, Azure VPN uygulamasının oturum açmasını ve Kullanıcı profillerini okumasını sağlar. Tarayıcınızın adres çubuğuna dağıtım konumunuza ait URL 'YI kopyalayıp yapıştırın:

    Genel

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Kamu

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Almanya Microsoft Bulut

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

3. İstenirse **genel yönetici** hesabını seçin.

    ![Dizin KIMLIĞI](./media/openvpn-create-azure-ad-tenant/pick.png)

4. İstendiğinde **kabul et** ' i seçin.

    ![Ekran görüntüsü, kuruluşunuz için kabul edilen ileti Izinlerine ve ek bilgilere sahip iletişim kutusunu gösterir.](./media/openvpn-create-azure-ad-tenant/accept.jpg)

5. Azure AD 'nizin altında, **Kurumsal uygulamalarda**, artık LISTELENEN **Azure VPN** 'yi görmeniz gerekir.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

## <a name="next-steps"></a>Sonraki adımlar

Azure AD kimlik doğrulaması 'nı kullanarak sanal ağlarınıza bağlanmak için bir kullanıcı VPN yapılandırması oluşturmanız ve bunu bir sanal hub ile ilişkilendirmeniz gerekir. Bkz. Azure [Ile Noktadan siteye bağlantı Için Azure AD kimlik doğrulamasını yapılandırma](virtual-wan-point-to-site-azure-ad.md).
