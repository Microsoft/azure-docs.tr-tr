---
title: 'VPN Gateway: P2S VPN bağlantıları için Azure AD kiracısı: Azure AD kimlik doğrulaması'
description: Azure AD kimlik doğrulaması kullanarak sanal ağınıza bağlanmak için P2S VPN kullanabilirsiniz
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: alzam
ms.openlocfilehash: 73c379d914f37de351165c19e3d73425e9a202b2
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151875"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>P2S OpenVPN Protokolü bağlantıları için Azure Active Directory kiracısı oluşturma

Sanal ağınıza bağlanırken sertifika tabanlı kimlik doğrulama veya RADIUS kimlik doğrulaması kullanabilirsiniz. Ancak, açık VPN protokolünü kullandığınızda Azure Active Directory kimlik doğrulamasını da kullanabilirsiniz. Bu makale, P2S Open VPN kimlik doğrulaması için bir Azure AD kiracısı ayarlamanıza yardımcı olur.

> [!NOTE]
> Azure AD kimlik doğrulaması yalnızca OpenVPN® Protokolü bağlantılarında desteklenir.
>

## <a name="tenant"></a>1. Azure AD kiracısı oluşturma

[Yeni kiracı oluşturma](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) makalesindeki adımları kullanarak BIR Azure AD kiracısı oluşturun:

* Kuruluş adı
* İlk etki alanı adı

Örnek:

   ![Yeni Azure AD kiracısı](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="users"></a>2. Azure AD kiracı kullanıcıları oluşturun

Sonra, iki kullanıcı hesabı oluşturun. Bir genel yönetici hesabı ve bir ana Kullanıcı hesabı oluşturun. Ana Kullanıcı hesabı, ana ekleme hesabınız (hizmet hesabı) olarak kullanılır. Bir Azure AD Kiracı Kullanıcı hesabı oluşturduğunuzda, oluşturmak istediğiniz kullanıcı türü için Dizin rolünü ayarlayın.

Azure AD kiracınız için en az iki kullanıcı oluşturmak üzere [Bu makaledeki](../active-directory/fundamentals/add-users-azure-active-directory.md) adımları kullanın. Hesap türlerini oluşturmak için **Dizin rolünü** değiştirdiğinizden emin olun:

* Genel Yönetici
* Kullanıcı

## <a name="enable-authentication"></a>3. VPN Gateway 'de Azure AD kimlik doğrulamasını etkinleştirme

1. Kimlik doğrulaması için kullanmak istediğiniz dizinin dizin KIMLIĞINI bulun. Active Directory sayfasının Özellikler bölümünde listelenir.

    ![Dizin KIMLIĞI](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Dizin KIMLIĞINI kopyalayın.

3. Azure portal, **genel yönetici** rolüne atanan bir kullanıcı olarak oturum açın.

4. Sonra, yönetici onayı verin. Tarayıcınızın adres çubuğuna dağıtım konumunuza ait URL 'YI kopyalayıp yapıştırın:

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

    Azure Çin 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. İstenirse **genel yönetici** hesabını seçin.

    ![Dizin KIMLIĞI](./media/openvpn-create-azure-ad-tenant/pick.png)

6. İstendiğinde **kabul et** ' i seçin.

    ![Ettiğinizde](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Azure AD 'nizin altında, **Kurumsal uygulamalarda**LISTELENEN **Azure VPN** ' yi görürsünüz.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. Aşağıdaki komutları çalıştırarak VPN Gateway 'de Azure AD kimlik doğrulamasını etkinleştirin ve komutu kendi ortamınızı yansıtacak şekilde değiştirdiğinizden emin olun:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "41b23e61-6c1e-4545-b367-cd054e0ed4b4" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/"
    ```

9. Aşağıdaki komutları çalıştırarak profili oluşturun ve indirin. -ResourcGroupName ve-Name değerlerini kendi değerlerinizle eşleşecek şekilde değiştirin.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

10. Komutları çalıştırdıktan sonra, aşağıdakine benzer bir sonuç görürsünüz. Profil ZIP dosyasını indirmek için sonuç URL 'sini tarayıcınıza kopyalayın.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/profile.png)

11. İndirilen ZIP dosyasını ayıklayın.

12. Sıkıştırılmış olmayan "AzureVPN" klasörüne gidin.

13. "Azurevpnconfig. xml" dosyasının konumunu bir yere unutmayın. Azurevpnconfig. xml, VPN bağlantısı ayarını içerir ve doğrudan Azure VPN Istemci uygulamasına aktarılabilir. Ayrıca, bu dosyayı e-posta veya başka yollarla bağlanması gereken tüm kullanıcılara dağıtabilirsiniz. Kullanıcının başarıyla bağlanması için geçerli bir Azure AD kimlik bilgilerine ihtiyacı olacak.

## <a name="next-steps"></a>Sonraki adımlar

Sanal ağınıza bağlanmak için bir VPN istemci profili oluşturmanız ve yapılandırmanız gerekir. Bkz. [P2S VPN bağlantıları IÇIN VPN Istemcisi yapılandırma](openvpn-azure-ad-client.md).
