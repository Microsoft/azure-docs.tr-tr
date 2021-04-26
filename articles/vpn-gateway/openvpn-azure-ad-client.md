---
title: 'VPN Gateway: P2S OpenVPN Protokolü bağlantıları için VPN istemcisi: Azure AD kimlik doğrulaması'
description: VPN istemcisini, Noktadan siteye VPN ve Azure Active Directory kimlik doğrulaması kullanarak bir sanal ağa bağlanmak üzere nasıl yapılandıracağınızı öğrenin.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2020
ms.author: alzam
ms.openlocfilehash: 02ce8e1809c5dd404e7afa25178acf37e7346cab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548419"
---
# <a name="azure-active-directory-authentication-configure-a-vpn-client-for-p2s-openvpn-protocol-connections"></a>Azure Active Directory kimlik doğrulaması: P2S OpenVPN Protokolü bağlantıları için VPN istemcisi yapılandırma

Bu makale, bir VPN istemcisini Noktadan siteye VPN ve Azure Active Directory kimlik doğrulaması kullanarak bir sanal ağa bağlanacak şekilde yapılandırmanıza yardımcı olur. Azure AD 'yi kullanarak bağlanabilmek ve kimlik doğrulayabilmeniz için önce Azure AD kiracınızı yapılandırmanız gerekir. Daha fazla bilgi için bkz. [Azure AD kiracısı yapılandırma](openvpn-azure-ad-tenant.md).

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="working-with-client-profiles"></a><a name="profile"></a>İstemci profilleriyle çalışma

Bağlanmak için, Azure VPN Istemcisini indirmeniz ve sanal ağa bağlanmak isteyen her bilgisayarda bir VPN istemci profili yapılandırmanız gerekir. Bir bilgisayarda istemci profili oluşturabilir, dışarı aktarabilir ve daha sonra ek bilgisayarlara aktarabilirsiniz.

### <a name="to-download-the-azure-vpn-client"></a>Azure VPN istemcisini indirmek için

Azure VPN Istemcisini indirmek için bu [bağlantıyı](https://go.microsoft.com/fwlink/?linkid=2117554) kullanın. Lütfen Azure VPN Istemcisinin arka planda çalıştırma izni olduğundan emin olun. İzni denetlemek/etkinleştirmek için aşağıdaki adımları izleyin:

1. Başlat ' a gidin, ardından ayarlar > gizlilik > arka plan uygulamaları ' nı seçin.
2. Arka plan uygulamaları altında, **arka planda uygulamaların çalışmasına Izin ver** ' in açık olduğundan emin olun.
3. Arka planda hangi uygulamaların çalışabileceğini seçin altında, Azure VPN Istemcisi ayarlarını **Açık** olarak açın.

  ![yetkisi](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>Sertifika tabanlı bir istemci profili oluşturmak için

Sertifika tabanlı bir profille çalışırken, istemci bilgisayarda uygun sertifikaların yüklü olduğundan emin olun. Sertifikalar hakkında daha fazla bilgi için bkz. [istemci sertifikalarını Install](point-to-site-how-to-vpn-client-install-azure-cert.md).

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>RADIUS istemci profili oluşturmak için

  ![La](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> Sunucu parolası P2S VPN istemci profiline aktarılabilir.  İstemci profilini dışarı aktarma yönergeleri [burada](about-vpn-profile-download.md)bulunabilir.
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>Bir istemci profilini dışarı ve dağıtım

Çalışma profiliniz varsa ve diğer kullanıcılara dağıtmanız gerekiyorsa, aşağıdaki adımları kullanarak dışarı aktarabilirsiniz:

1. Dışarı aktarmak istediğiniz VPN istemci profilini vurgulayın, **...** öğesini seçin ve ardından **dışarı aktar**' ı seçin.

    !["Azure VPN Istemcisi" sayfasını, seçilen üç nokta ve "dışarı aktar" vurgulanmış şekilde gösteren ekran görüntüsü.](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Bu profili kaydetmek istediğiniz konumu seçin, dosya adını olduğu gibi bırakın ve **Kaydet** ' i seçerek XML dosyasını kaydedin.

    ![dışarı aktarma](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>İstemci profilini içeri aktarmak için

1. Sayfasında **Içeri aktar**' ı seçin.

    ![Pencerenin sol alt tarafında "Ekle" düğmesinin seçili olduğunu ve "Içeri aktar" eylemini gösteren ekran görüntüsü.](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Profil XML dosyasına gidin ve seçin. Dosya seçili olduğunda **Aç**' ı seçin.

    ![Seçili bir profil x m l dosyası gösteren ekran görüntüsü.](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Profilin adını belirtin ve **Kaydet**' i seçin.

    !["Bağlantı adı" vurgulanmasını ve "Kaydet" düğmesinin seçili olduğunu gösteren ekran görüntüsü.](./media/openvpn-azure-ad-client/import/import3.jpg)

4. VPN 'ye bağlanmak için **Bağlan** ' ı seçin.

    ![VPN ve "Bağlan" düğmesinin seçili olduğunu gösteren ekran görüntüsü.](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Bağlandıktan sonra simge yeşil ve **bağlı** olarak görünür.

    ![içeri aktar](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>İstemci profilini silmek için

1. Silmek istediğiniz istemci profilinin yanındaki üç nokta simgesini seçin. Ardından **Kaldır**' ı seçin.

    ![Üç nokta ve "Kaldır" seçeneğinin seçili olduğunu gösteren ekran görüntüsü.](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Silmek için **Kaldır** ' ı seçin.

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Bağlantı oluşturma

1. Sayfasında **+** , ve ardından **+ Ekle**' yi seçin.

    !["Ekle" düğmesinin seçili olduğunu gösteren ekran görüntüsü.](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Bağlantı bilgilerini doldurun. Değerlerden emin değilseniz yöneticinize başvurun. Değerleri doldurduktan sonra **Kaydet**' i seçin.

    ![Vurgulanan VPN bağlantı özelliklerini ve "Kaydet" düğmesinin seçili olduğunu gösteren ekran görüntüsü.](./media/openvpn-azure-ad-client/create/create2.jpg)

3. VPN 'ye bağlanmak için **Bağlan** ' ı seçin.

    !["Bağlan" düğmesinin seçili olduğunu gösteren ekran görüntüsü.](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Doğru kimlik bilgilerini seçip **devam**' ı seçin.

    ![Vurgulanmış örnek kimlik bilgilerini ve "devam" düğmesinin seçili olduğunu gösteren ekran görüntüsü.](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Başarılı bir şekilde bağlandıktan sonra simge yeşil ve **bağlı** olarak görünür.

    ![bağlantı](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>Otomatik olarak bağlanmak için

Bu adımlar, bağlantınızı her zaman açık ile otomatik olarak bağlanacak şekilde yapılandırmanıza yardımcı olur.

1. VPN istemcinizin ana sayfasında **VPN ayarları**' nı seçin.

    !["VPN ayarları" seçiliyken VPN giriş sayfasının ekran görüntüsü.](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Uygulamalar arasında geçiş yap iletişim kutusunda **Evet** ' i seçin.

    !["Uygulama değiştirmek mı Istediniz?" ekran görüntüsü "Evet" düğmesi seçili iletişim kutusu.](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Ayarlamak istediğiniz bağlantının zaten bağlı olmadığından emin olun, sonra profili vurgulayın ve **otomatik olarak bağlan** onay kutusunu işaretleyin.

    !["Otomatik bağlan" kutusu işaretli "Ayarlar" penceresinin ekran görüntüsü.](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. VPN bağlantısını başlatmak için **Bağlan** ' ı seçin.

    ![auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Bağlantı sorunlarını Tanıla

1. Bağlantı sorunlarını tanılamak için **Tanılama** aracını kullanabilirsiniz. Menüyü açığa çıkarmak için tanılamak istediğiniz VPN bağlantısının yanındaki **...** seçeneğini belirleyin. Ardından **Tanıla**' yı seçin.

    ![Üç nokta ve "Seçileni Tanıla" ekran görüntüsü.](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. **Bağlantı özellikleri** sayfasında, **tanı Çalıştır**' ı seçin.

    !["Tanılama Çalıştır" seçiliyken "bağlantı özellikleri" sayfasını gösteren ekran görüntüsü.](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Kimlik bilgilerinizle oturum açın.

    !["Iş veya okul hesabı" seçiliyken "oturum açalım" iletişim kutusunu gösteren ekran görüntüsü.](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Tanılama sonuçlarını görüntüleyin.

    ![tanın](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>SSS

### <a name="is-the-azure-vpn-client-supported-with-windows-fips-mode"></a>Azure VPN Istemcisi Windows FIPS modunda destekleniyor mu?

Evet, [KB4577063](https://support.microsoft.com/help/4577063/windows-10-update-kb4577063) düzeltmesi ile.

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Nasıl yaparım? VPN istemcisine DNS sonekleri eklensin mi?

İndirilen profil XML dosyasını değiştirebilir ve **\<dnssuffixes> \<dnssufix> \</dnssufix> \</dnssuffixes>** etiketleri ekleyebilirsiniz

```
<azvpnprofile>
<clientconfig>

    <dnssuffixes>
          <dnssuffix>.mycorp.com</dnssuffix>
          <dnssuffix>.xyz.com</dnssuffix>
          <dnssuffix>.etc.net</dnssuffix>
    </dnssuffixes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>Nasıl yaparım? VPN istemcisine özel DNS sunucuları eklensin mi?

İndirilen profil XML dosyasını değiştirebilir ve **\<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers>** etiketleri ekleyebilirsiniz

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

> [!NOTE]
> OpenVPN Azure AD İstemcisi, DNS ad çözümleme Ilkesi tablosu (NRPT) girdilerini kullanır, bu da DNS sunucularının çıktının altında listelenmeyeceği anlamına gelir `ipconfig /all` . Kullanımdaki DNS ayarlarınızı onaylamak için lütfen PowerShell 'de [Get-DnsClientNrptPolicy](/powershell/module/dnsclient/get-dnsclientnrptpolicy) bölümüne başvurun.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Nasıl yaparım? VPN istemcisine özel yollar eklensin mi?

İndirilen profil XML dosyasını değiştirebilir ve **\<includeroutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</includeroutes>** etiketleri ekleyebilirsiniz

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>VPN istemcisinden gelen yollar engellensin mi (hariç) Nasıl yaparım??

İndirilen profil XML dosyasını değiştirebilir ve **\<excluderoutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</excluderoutes>** etiketleri ekleyebilirsiniz

```
<azvpnprofile>
<clientconfig>

    <excluderoutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </excluderoutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="can-i-import-the-profile-from-a-command-line-prompt"></a>Profili bir komut satırı isteminden içeri aktarabilir miyim?

İndirilen **azurevpnconfig.xml** dosyasını **%USERPROFILE%\appdata\local\packages\ Microsoft.AzureVpn_8wekyb3d8bbwe \localstate** klasörüne yerleştirerek ve aşağıdaki komutu çalıştırarak profili bir komut satırı isteminden içeri aktarabilirsiniz:

```
azurevpn -i azurevpnconfig.xml 
```
içeri aktarmaya zorlamak için **-f** anahtarını da kullanın


## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [Azure AD kimlik doğrulaması kullanan P2S Open VPN bağlantıları için Azure Active Directory kiracı oluşturma](openvpn-azure-ad-tenant.md).