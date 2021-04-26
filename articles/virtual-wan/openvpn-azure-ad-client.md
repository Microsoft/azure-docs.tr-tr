---
title: 'VPN Gateway: OpenVPN Protokolü P2S bağlantıları için VPN istemcisi: Azure AD kimlik doğrulaması'
description: P2S VPN kullanarak Azure AD kimlik doğrulaması kullanarak VNet 'nize nasıl bağlanacağınızı öğrenin.
services: vpn-gateway
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 3dbdfb461bc2ae79439f39df3efbc2c564782110
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548402"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>P2S OpenVPN protokolü bağlantıları için VPN istemcisi yapılandırma: Azure AD kimlik doğrulaması

Bu makale, bir VPN istemcisini Noktadan siteye VPN ve Azure Active Directory kimlik doğrulaması kullanarak bir sanal ağa bağlanacak şekilde yapılandırmanıza yardımcı olur. Azure AD 'yi kullanarak bağlanabilmek ve kimlik doğrulayabilmeniz için önce Azure AD kiracınızı yapılandırmanız gerekir. Daha fazla bilgi için bkz. [Azure AD kiracısı yapılandırma](openvpn-azure-ad-tenant.md).

> [!NOTE]
> Azure AD kimlik doğrulaması yalnızca OpenVPN® Protokolü bağlantılarında desteklenir.
>

## <a name="working-with-client-profiles"></a><a name="profile"></a>İstemci profilleriyle çalışma

Bağlanmak için, Azure VPN Istemcisini indirmeniz ve sanal ağa bağlanmak isteyen her bilgisayarda bir VPN istemci profili yapılandırmanız gerekir. Bir bilgisayarda istemci profili oluşturabilir, dışarı aktarabilir ve daha sonra ek bilgisayarlara aktarabilirsiniz.

### <a name="to-download-the-azure-vpn-client"></a>Azure VPN istemcisini indirmek için

Azure VPN Istemcisini indirmek için bu [bağlantıyı](https://go.microsoft.com/fwlink/?linkid=2117554) kullanın. Lütfen Azure VPN Istemcisinin arka planda çalıştırma izni olduğundan emin olun. İzni denetlemek/etkinleştirmek için aşağıdaki adımları izleyin:

1. Başlat ' a gidin, ardından ayarlar > gizlilik > arka plan uygulamaları ' nı seçin.
2. Arka plan uygulamaları altında, **arka planda uygulamaların çalışmasına Izin ver** ' in açık olduğundan emin olun.
3. Arka planda hangi uygulamaların çalışabileceğini seçin altında, Azure VPN Istemcisi ayarlarını **Açık** olarak açın.

  ![yetkisi](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>Sertifika tabanlı bir istemci profili oluşturmak için

Sertifika tabanlı bir profille çalışırken, istemci bilgisayarda uygun sertifikaların yüklü olduğundan emin olun. Sertifikalar hakkında daha fazla bilgi için bkz. [istemci sertifikalarını Install](certificates-point-to-site.md).

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>RADIUS istemci profili oluşturmak için

  ![La](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> Sunucu parolası P2S VPN istemci profiline aktarılabilir.  İstemci profilini dışarı aktarma yönergeleri [burada](about-vpn-profile-download.md)bulunabilir.
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>Bir istemci profilini dışarı ve dağıtım

Çalışma profiliniz varsa ve diğer kullanıcılara dağıtmanız gerekiyorsa, aşağıdaki adımları kullanarak dışarı aktarabilirsiniz:

1. Dışarı aktarmak istediğiniz VPN istemci profilini vurgulayın, **...** öğesini seçin ve ardından **dışarı aktar**' ı seçin.

    ![Ekran görüntüsünde, menüden dışa aktarma seçili görüntülenir.](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Bu profili kaydetmek istediğiniz konumu seçin, dosya adını olduğu gibi bırakın ve **Kaydet** ' i seçerek XML dosyasını kaydedin.

    ![Ekran görüntüsü, bir dosya adı girebileceğiniz bir farklı Kaydet iletişim kutusu gösterir.](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>İstemci profilini içeri aktarmak için

1. Sayfasında **Içeri aktar**' ı seçin.

    ![Ekran görüntüsü artı menüsünden Seçili Içeri aktarmayı gösterir.](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Profil XML dosyasına gidin ve seçin. Dosya seçili olduğunda **Aç**' ı seçin.

    ![Ekran görüntüsü, bir dosyayı seçebileceğiniz açık bir iletişim kutusu gösterir.](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Profilin adını belirtin ve **Kaydet**' i seçin.

    ![Ekran görüntüsünde, eklenen bağlantı adı ve Kaydet düğmesi seçili görüntülenir.](./media/openvpn-azure-ad-client/import/import3.jpg)

4. VPN 'ye bağlanmak için **Bağlan** ' ı seçin.

    ![Ekran görüntüsü, yeni oluşturduğunuz bağlantı için Bağlan düğmesini gösterir.](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Bağlandıktan sonra simge yeşil ve **bağlı** olarak görünür.

    ![Ekran görüntüsü, bağlantıyı kesme seçeneğiyle bağlı bir durumda gösterir.](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>İstemci profilini silmek için

1. Silmek istediğiniz istemci profilinin yanındaki üç nokta simgesini seçin. Ardından **Kaldır**' ı seçin.

    ![Ekran görüntüsü menüden Seçileni kaldır 'ı gösterir.](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Silmek için **Kaldır** ' ı seçin.

    ![Ekran görüntüsü, kaldırma veya Iptal etme seçeneğiyle bir onay iletişim kutusu gösterir.](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Bağlantı oluşturma

1. Sayfasında **+** , ve ardından **+ Ekle**' yi seçin.

    ![Ekran görüntüsü artı menüsünden Seçileni Ekle ' ye gösterir.](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Bağlantı bilgilerini doldurun. Değerlerden emin değilseniz yöneticinize başvurun. Değerleri doldurduktan sonra **Kaydet**' i seçin.

    ![Ekran görüntüsü, gerekli değerleri girebileceğiniz bölmeyi gösterir.](./media/openvpn-azure-ad-client/create/create2.jpg)

3. VPN 'ye bağlanmak için **Bağlan** ' ı seçin.

    ![Ekran görüntüsü, bağlantınızın Bağlan düğmesini gösterir.](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Doğru kimlik bilgilerini seçip **devam**' ı seçin.

    ![Ekran görüntüsü oturum aç iletişim kutusunu gösterir.](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Başarılı bir şekilde bağlandıktan sonra simge yeşil ve **bağlı** olarak görünür.

    ![Ekran görüntüsü bağlı durumdaki bağlantıyı gösterir.](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>Otomatik olarak bağlanmak için

Bu adımlar, bağlantınızı her zaman açık ile otomatik olarak bağlanacak şekilde yapılandırmanıza yardımcı olur.

1. VPN istemcinizin ana sayfasında **VPN ayarları**' nı seçin.

    ![Ekran görüntüsü, v p n ayarlarını seçebileceğiniz V P N bağlantılarını gösterir.](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Uygulamalar arasında geçiş yap iletişim kutusunda **Evet** ' i seçin.

    ![Ekran görüntüsü, uygulama değiştirme hakkında bir doğrulama iletisi gösterir.](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Ayarlamak istediğiniz bağlantının zaten bağlı olmadığından emin olun, sonra profili vurgulayın ve **otomatik olarak bağlan** onay kutusunu işaretleyin.

    ![Ekran görüntüsü, otomatik olarak bağlan ' ı seçebileceğiniz bir ayarlar iletişim kutusu gösterir.](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. VPN bağlantısını başlatmak için **Bağlan** ' ı seçin.

    ![Ekran görüntüsü Bağlan düğmesini gösterir.](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Bağlantı sorunlarını Tanıla

1. Bağlantı sorunlarını tanılamak için **Tanılama** aracını kullanabilirsiniz. Menüyü açığa çıkarmak için tanılamak istediğiniz VPN bağlantısının yanındaki **...** seçeneğini belirleyin. Ardından **Tanıla**' yı seçin.

    ![Ekran görüntüsü, menüden seçili tanılamayı gösterir.](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. **Bağlantı özellikleri** sayfasında, **tanı Çalıştır**' ı seçin.

    ![Ekran görüntüsü, bir bağlantı için tanılama Çalıştır düğmesini gösterir.](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Kimlik bilgilerinizle oturum açın.

    ![Ekran görüntüsü bu eylem için oturum açma iletişim kutusunu gösterir.](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Tanılama sonuçlarını görüntüleyin.

    ![Ekran görüntüsü, tanılamada sonuçları gösterir.](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>SSS

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
### <a name="how-do-i-direct-all-traffic-to-the-vpn-tunnel-force-tunnel"></a>Tüm trafiği VPN tüneline (Zorlamalı tünel) yönlendirmek Nasıl yaparım??

İndirilen profil XML dosyasını değiştirebilir ve **\<includeroutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</includeroutes>** etiketleri ekleyebilirsiniz

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>0.0.0.0</destination><mask>1</mask>
        </route>
        <route>
            <destination>128.0.0.0</destination><mask>1</mask>
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