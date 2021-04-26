---
title: 'VPN Gateway: P2S VPN bağlantıları için Azure AD kiracısı: Azure AD kimlik doğrulaması'
description: P2S Open VPN kimlik doğrulaması için bir Azure AD kiracısı ayarlamayı öğrenin.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/27/2020
ms.author: cherylmc
ms.openlocfilehash: bff1eec0152ab0f57edd212adf6b14f7b588fb51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100390173"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>P2S OpenVPN protokolü bağlantıları için Azure Active Directory kiracısı oluşturma

Sanal ağınıza bağlanırken sertifika tabanlı kimlik doğrulama veya RADIUS kimlik doğrulaması kullanabilirsiniz. Ancak, açık VPN protokolünü kullandığınızda Azure Active Directory kimlik doğrulamasını da kullanabilirsiniz. Bu makale, P2S Open VPN kimlik doğrulaması için bir Azure AD kiracısı ayarlamanıza yardımcı olur.

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. Azure AD kiracısını doğrulama

Bir Azure AD kiracınız olduğunu doğrulayın. Bir Azure AD kiracınız yoksa, [Yeni bir kiracı oluşturma](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) makalesindeki adımları kullanarak bir tane oluşturabilirsiniz:

* Kuruluş adı
* İlk etki alanı adı

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/newtenant.png" alt-text="Yeni Azure AD kiracısı" border="false":::

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Azure AD kiracı kullanıcıları oluşturun

Azure AD kiracınız aşağıdaki hesaplara ihtiyaç duyuyor: genel yönetici hesabı ve bir ana Kullanıcı hesabı. Ana Kullanıcı hesabı, ana ekleme hesabınız (hizmet hesabı) olarak kullanılır. Bir Azure AD Kiracı Kullanıcı hesabı oluşturduğunuzda, oluşturmak istediğiniz kullanıcı türü için Dizin rolünü ayarlayın.

Azure AD kiracınız için en az iki kullanıcı oluşturmak üzere [Kullanıcı ekleme veya silme-Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md) bölümündeki adımları kullanın. Hesap türlerini oluşturmak için **Dizin rolünü** değiştirdiğinizden emin olun:

* Genel Yönetici
* Kullanıcı

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. VPN Gateway 'de Azure AD kimlik doğrulamasını etkinleştirme

1. Kimlik doğrulaması için kullanmak istediğiniz dizinin dizin KIMLIĞINI bulun. Active Directory sayfanın Özellikler bölümünde listelenir.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/directory-id.png" alt-text="Dizin özelliklerini gösteren ekran görüntüsü" lightbox="./media/openvpn-create-azure-ad-tenant/directory-id.png":::

1. Dizin kimliği'ni kopyalayın.

1. Azure portal, **genel yönetici** rolüne atanan bir kullanıcı olarak oturum açın.

1. Sonra, yönetici onayı verin. Tarayıcınızın adres çubuğuna dağıtım konumunuza ait URL 'YI kopyalayıp yapıştırın:

   Genel

   ```
   https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
   ````

   Azure Kamu

   ```
   https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
   ````

   Almanya Microsoft Bulut

   ```
   https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
   ````

    Azure China 21Vianet

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

   > [!NOTE]
   > Onay sağlamak için Azure AD kiracısına yerel olmayan bir genel yönetici hesabı kullanıyorsanız, lütfen "ortak" değerini URL 'deki Azure AD dizin kimliğiyle değiştirin. Ayrıca, "ortak" i de belirli diğer durumlarda Dizin kimliğinizle de değiştirmeniz gerekebilir.
   >

1. İstenirse **genel yönetici** hesabını seçin.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/pick.png" alt-text="Bir hesap seçin" border="false":::
1. İstendiğinde **kabul et** ' i seçin.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/accept.jpg" alt-text="Ekran görüntüsünde, Ayrıntılar ve kabul etme seçeneği sayesinde kuruluşunuz için kabul edilen ileti Izinleri gösterilir." border="false":::
1. Azure AD 'nizin altında, **Kurumsal uygulamalarda** LISTELENEN **Azure VPN** ' yi görürsünüz.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/azurevpn.png" alt-text="Tüm uygulamalar sayfasını gösteren ekran görüntüsü." lightbox="./media/openvpn-create-azure-ad-tenant/azurevpn.png" :::
1. Zaten çalışan bir noktadan siteye ortamınız yoksa, bir tane oluşturmak için yönergeyi izleyin. Noktadan siteye VPN ağ geçidi oluşturmak ve yapılandırmak için bkz. [noktadan sıteye VPN oluşturma](vpn-gateway-howto-point-to-site-resource-manager-portal.md) .

    > [!IMPORTANT]
    > Temel SKU, OpenVPN için desteklenmez.

1. **Noktadan siteye yapılandırmaya** giderek ve **tünel türü** olarak **OpenVPN (SSL)** seçerek VPN ağ geçidinde Azure AD kimlik doğrulamasını etkinleştirin. **Kimlik doğrulaması türü** olarak **Azure Active Directory** öğesini seçin ve ardından **Azure Active Directory** bölümünün altındaki bilgileri girin.

   * **Kiracı:** Azure AD kiracısı için Tenantıd ```https://login.microsoftonline.com/{AzureAD TenantID}/```

   * **Hedef kitle:** "Azure VPN" Azure AD kurumsal uygulamasının ApplicationId 'i ```{AppID of the "Azure VPN" AD Enterprise app}```

   * **Veren**: güvenli belirteç hizmeti 'nin URL 'si ```https://sts.windows.net/{AzureAD TenantID}/```


   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/azure-ad-auth-portal.png" alt-text="SAzure VPN" border="false":::

   > [!NOTE]
   > Değerin sonuna eğik çizgi eklediğinizden emin olun `AadIssuerUri` . Aksi takdirde bağlantı başarısız olabilir.
   >

1. **VPN Istemcisini indir** bağlantısına tıklayarak profili oluşturun ve indirin.

1. İndirilen ZIP dosyasını ayıklayın.

1. Sıkıştırılmış olmayan "AzureVPN" klasörüne gidin.

1. "azurevpnconfig.xml" dosyasının konumunu bir yere göz önünde alın. azurevpnconfig.xml, VPN bağlantısı ayarını içerir ve doğrudan Azure VPN Istemci uygulamasına aktarılabilir. Ayrıca, bu dosyayı e-posta veya başka yollarla bağlanması gereken tüm kullanıcılara dağıtabilirsiniz. Kullanıcının başarıyla bağlanması için geçerli bir Azure AD kimlik bilgilerine ihtiyacı olacak.

## <a name="next-steps"></a>Sonraki adımlar

Bir VPN istemci profili oluşturun ve yapılandırın. Bkz. [P2S VPN bağlantıları IÇIN VPN Istemcisi yapılandırma](openvpn-azure-ad-client.md).
