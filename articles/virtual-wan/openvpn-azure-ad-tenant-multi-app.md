---
title: 'Sanal WAN: farklı Kullanıcı grupları için Azure AD kiracısı: Azure AD kimlik doğrulaması'
description: P2S OpenVPN kimlik doğrulaması için bir Azure AD kiracısı ayarlayın ve farklı kullanıcılara ve gruplara farklı erişim sağlamak için Azure AD 'de birden çok uygulama oluşturun ve kaydedin.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: e25ef7f55492be4ee491b9ebbbef4aa1eb03c80b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98898096"
---
# <a name="create-an-azure-active-directory-ad-tenant-for-p2s-openvpn-protocol-connections"></a>P2S OpenVPN Protokolü bağlantıları için Azure Active Directory (AD) kiracısı oluşturma

Sanal ağınıza bağlanırken sertifika tabanlı kimlik doğrulama veya RADIUS kimlik doğrulaması kullanabilirsiniz. Ancak, açık VPN protokolünü kullandığınızda Azure Active Directory kimlik doğrulamasını da kullanabilirsiniz. Farklı kullanıcı kümesinin farklı ağ geçitlerine bağlanabilmelerini istiyorsanız, AD 'de birden çok uygulamayı kaydedebilir ve bunları farklı ağ geçitlerine bağlayabilirsiniz.

Bu makale, P2S OpenVPN kimlik doğrulaması için bir Azure AD kiracısı ayarlamanıza ve farklı kullanıcılara ve gruplara farklı erişim sağlamak için Azure AD 'de birden çok uygulama oluşturup kaydetmenize yardımcı olur.

> [!NOTE]
> Azure AD kimlik doğrulaması yalnızca OpenVPN &reg; Protokolü bağlantıları için desteklenir.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-create-a-new-p2s-configuration"></a><a name="site"></a>6. yeni bir P2S yapılandırması oluşturma

P2S yapılandırması, uzak istemcilerin bağlanmasına yönelik parametreleri tanımlar.

1. Ortamınız için gereken değerleri değiştirerek aşağıdaki değişkenleri ayarlayın.

   ```azurepowershell-interactive
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Yapılandırmayı oluşturmak için aşağıdaki komutları çalıştırın:

   ```azurepowershell-interactive
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

   > [!NOTE]
   > Yukarıdaki komutlarda Azure VPN istemcisinin uygulama KIMLIĞINI kullanmayın: tüm kullanıcılara ağ geçidine erişim izni verir. Kaydettiğiniz uygulamaların KIMLIĞINI kullanın.

## <a name="7-edit-hub-assignment"></a><a name="hub"></a>7. hub atamasını Düzenle

1. Sanal WAN altındaki **hub** + dikey penceresine gidin.

2. VPN sunucusu yapılandırmasını ilişkilendirmek istediğiniz hub 'ı seçin ve üç nokta (...) simgesine tıklayın.

    ![Ekran görüntüsü, menüden sanal hub 'ı Düzenle ' nin seçili olduğunu gösterir.](media/openvpn-azure-ad-tenant-multi-app/p2s4.jpg)

3. **Sanal hub 'ı Düzenle**' ye tıklayın.

4. **Noktadan siteye ağ geçidini dahil** et onay kutusunu işaretleyin ve Istediğiniz **ağ geçidi ölçek birimini** seçin.

    ![Ekran görüntüsü, ağ geçidi ölçek birimini seçebileceğiniz sanal hub 'ı Düzenle iletişim kutusunu gösterir.](media/openvpn-azure-ad-tenant-multi-app/p2s2.jpg)

5. VPN istemcilerine atanan IP adreslerine ait **adres havuzunu** girin.

6. **Onayla**'ya tıklayın.

7. İşlemin tamamlanması 30 dakika kadar sürebilir.

## <a name="8-download-vpn-profile"></a><a name="device"></a>8. VPN profilini indir

İstemcilerinizi yapılandırmak için VPN profilini kullanın.

1. Sanal WAN 'nizin sayfasında, **Kullanıcı VPN yapılandırması**' na tıklayın.

2. Sayfanın üst kısmında, **Kullanıcı VPN yapılandırması 'Nı indir**' e tıklayın.

3. Dosya oluşturulduktan sonra bağlantıya tıklayarak indirebilirsiniz.

4. VPN istemcilerini yapılandırmak için profil dosyasını kullanın.

5. İndirilen ZIP dosyasını ayıklayın.

6. Sıkıştırılmış olmayan "AzureVPN" klasörüne gidin.

7. "azurevpnconfig.xml" dosyasının konumunu bir yere göz önünde alın. azurevpnconfig.xml, VPN bağlantısı ayarını içerir ve doğrudan Azure VPN Istemci uygulamasına aktarılabilir. Ayrıca, bu dosyayı e-posta veya başka yollarla bağlanması gereken tüm kullanıcılara dağıtabilirsiniz. Kullanıcının başarıyla bağlanması için geçerli bir Azure AD kimlik bilgilerine ihtiyacı olacak.

## <a name="9-configure-user-vpn-clients"></a>9. Kullanıcı VPN istemcilerini yapılandırma

Bağlanmak için, Azure VPN Istemcisini indirmeniz ve sanal ağa bağlanmak isteyen her bilgisayarda önceki adımlarda indirilen VPN istemci profilini içeri aktarmanız gerekir.

> [!NOTE]
> Azure AD kimlik doğrulaması yalnızca OpenVPN &reg; Protokolü bağlantıları için desteklenir.
>

#### <a name="to-download-the-azure-vpn-client"></a>Azure VPN istemcisini indirmek için

Azure VPN Istemcisini indirmek için bu [bağlantıyı](https://go.microsoft.com/fwlink/?linkid=2117554) kullanın.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>İstemci profilini içeri aktarmak için

1. Sayfasında **Içeri aktar**' ı seçin.

    ![Ekran görüntüsü artı menüsünden Seçili Içeri aktarmayı gösterir.](./media/openvpn-azure-ad-tenant-multi-app/import/import1.jpg)

2. Profil XML dosyasına gidin ve seçin. Dosya seçili olduğunda **Aç**' ı seçin.

    ![Ekran görüntüsü, bir dosyayı seçebileceğiniz açık bir iletişim kutusu gösterir.](./media/openvpn-azure-ad-tenant-multi-app/import/import2.jpg)

3. Profilin adını belirtin ve **Kaydet**' i seçin.

    ![Ekran görüntüsünde, eklenen bağlantı adı ve Kaydet düğmesi seçili görüntülenir.](./media/openvpn-azure-ad-tenant-multi-app/import/import3.jpg)

4. VPN 'ye bağlanmak için **Bağlan** ' ı seçin.

    ![Ekran görüntüsü, yeni oluşturduğunuz bağlantı için Bağlan düğmesini gösterir.](./media/openvpn-azure-ad-tenant-multi-app/import/import4.jpg)

5. Bağlandıktan sonra simge yeşil ve **bağlı** olarak görünür.

    ![Ekran görüntüsü, bağlantıyı kesme seçeneğiyle bağlı bir durumda gösterir.](./media/openvpn-azure-ad-tenant-multi-app/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>İstemci profilini silmek için

1. Silmek istediğiniz istemci profilinin yanındaki üç nokta (...) simgesini seçin. Ardından **Kaldır**' ı seçin.

    ![Ekran görüntüsü menüden Seçileni kaldır 'ı gösterir.](./media/openvpn-azure-ad-tenant-multi-app/delete/delete1.jpg)

2. Silmek için **Kaldır** ' ı seçin.

    ![Ekran görüntüsü, kaldırma veya Iptal etme seçeneğiyle bir onay iletişim kutusu gösterir.](./media/openvpn-azure-ad-tenant-multi-app/delete/delete2.jpg)

#### <a name="to-diagnose-connection-issues"></a><a name="diagnose"></a>Bağlantı sorunlarını tanılamak için

1. Bağlantı sorunlarını tanılamak için **Tanılama** aracını kullanabilirsiniz. Menüyü açığa çıkarmak için tanılamak istediğiniz VPN bağlantısının yanındaki üç nokta (...) simgesini seçin. Ardından **Tanıla**' yı seçin.

    ![Ekran görüntüsü, menüden seçili tanılamayı gösterir.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose1.jpg)

2. **Bağlantı özellikleri** sayfasında, **tanı Çalıştır**' ı seçin.

    ![Ekran görüntüsü, bir bağlantı için tanılama Çalıştır düğmesini gösterir.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Kimlik bilgilerinizle oturum açın.

    ![Tanıla 3](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Tanılama sonuçlarını görüntüleyin.

    ![Ekran görüntüsü, bir bağlantı için tanılama Çalıştır düğmesini gösterir.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Kimlik bilgilerinizle oturum açın.

    ![Ekran görüntüsü bu eylem için oturum açma iletişim kutusunu gösterir.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Tanılama sonuçlarını görüntüleyin.

    ![Ekran görüntüsü, tanılamada sonuçları gösterir.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose4.jpg)

## <a name="10-view-your-virtual-wan"></a><a name="viewwan"></a>10. sanal WAN 'nizi görüntüleyin

1. Sanal WAN'a gidin.

2. Genel bakış sayfasında haritadaki her bir nokta bir hub'ı temsil eder.

3. Hub'lar ve bağlantılar bölümünde herhangi bir hub'ın durumunu, sitesini, bölgesini, VPN bağlantısı durumunu ve gelen/giden baytları görüntüleyebilirsiniz.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Kaynakları temizleme

Bu kaynaklar artık gerekli değilse, [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) komutunu kullanarak kaynak grubunu ve içerdiği tüm kaynakları kaldırabilirsiniz. "myResourceGroup" yerine kaynak grubunuzun adını yazın ve aşağıdaki PowerShell komutunu çalıştırın:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Sonraki adımlar

Sanal WAN hakkında daha fazla bilgi için [Sanal WAN'a Genel Bakış](virtual-wan-about.md) sayfasına bakın.
