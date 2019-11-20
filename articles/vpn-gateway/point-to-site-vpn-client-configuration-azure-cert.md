---
title: 'P2S VPN istemci yapılandırma dosyaları oluşturun & install: sertifika kimlik doğrulaması'
titleSuffix: Azure VPN Gateway
description: P2S sertifikası kimlik doğrulaması için Windows, Linux, Linux (Strongswa) ve Mac OS X VPN istemci yapılandırma dosyaları oluşturun ve bunları yükler.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/12/2019
ms.author: cherylmc
ms.openlocfilehash: ad6e912f976095ae7d8fd5276b0f1365566c181a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143784"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Yerel Azure sertifikası kimlik doğrulaması P2S yapılandırmaları için VPN istemcisi yapılandırma dosyalarını oluşturma ve yapılandırma

VPN istemcisi yapılandırma dosyaları bir ZIP dosyasında bulunur. Yapılandırma dosyaları, yerel bir Windows, Mac IKEv2 VPN veya Linux istemcilerinin yerel Azure sertifika kimlik doğrulaması kullanan Noktadan siteye bağlantılar üzerinden bir sanal ağa bağlanması için gereken ayarları sağlar.

İstemci yapılandırma dosyaları, sanal ağın VPN yapılandırmasına özeldir. VPN protokolü türü veya kimlik doğrulama türü gibi VPN istemcisi yapılandırma dosyalarını oluşturduktan sonra Noktadan siteye VPN yapılandırmasında herhangi bir değişiklik varsa, Kullanıcı cihazlarınız için yeni VPN istemci yapılandırma dosyaları üretdiğinizden emin olun. 

* Noktadan Siteye bağlantılar hakkında daha fazla bilgi edinmek için bkz. [Noktadan Siteye VPN hakkında](point-to-site-about.md).
* OpenVPN yönergeleri için bkz. [P2S Için OpenVPN yapılandırma](vpn-gateway-howto-openvpn.md) ve [OpenVPN istemcilerini yapılandırma](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate"></a>VPN istemcisi yapılandırma dosyalarını oluştur

Başlamadan önce, tüm bağlanan kullanıcıların, kullanıcının cihazında geçerli bir sertifika yüklü olduğundan emin olun. İstemci sertifikası yükleme hakkında daha fazla bilgi için bkz. [istemci sertifikası yükleme](point-to-site-how-to-vpn-client-install-azure-cert.md).

PowerShell kullanarak veya Azure portal kullanarak istemci yapılandırma dosyaları oluşturabilirsiniz. Her iki yöntem de aynı ZIP dosyasını döndürür. Aşağıdaki klasörleri görüntülemek için dosyayı sıkıştırmayı açın:

  * Sırasıyla Windows 32-bit ve 64-bit yükleyici paketlerini içeren **WindowsAmd64** ve **WindowsX86**. **WindowsAmd64** Installer paketi yalnızca AMD değil, desteklenen tüm 64-bit Windows istemcilerine yöneliktir.
  * **Genel**, kendi VPN istemci yapılandırmanızı oluşturmak için kullanılan genel bilgileri içerir. Ağ geçidinde Ikev2 veya SSTP + Ikev2 yapılandırılmışsa genel klasör sağlanır. Yalnızca SSTP yapılandırılmışsa, genel klasör mevcut değildir.

### <a name="zipportal"></a>Azure portal kullanarak dosya oluşturma

1. Azure portal, bağlanmak istediğiniz sanal ağ için sanal ağ geçidine gidin.
2. Sanal ağ geçidi sayfasında, **Noktadan siteye yapılandırma**' ya tıklayın.
3. Noktadan siteye yapılandırma sayfasının en üstünde **VPN Istemcisini indir**' e tıklayın. İstemci yapılandırma paketinin oluşturması birkaç dakika sürer.
4. Tarayıcınız, bir istemci yapılandırması ZIP dosyasının kullanılabilir olduğunu gösterir. Bu, ağ geçidiniz ile aynı adı taşır. Klasörleri görüntülemek için dosyayı sıkıştırmayı açın.

### <a name="zipps"></a>PowerShell kullanarak dosyalar oluşturma

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. VPN istemci yapılandırma dosyalarını oluştururken, '-AuthenticationMethod ' değeri ' EapTls ' olur. Aşağıdaki komutu kullanarak VPN istemci yapılandırma dosyalarını oluşturun:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. Zip dosyasını indirmek için URL 'YI tarayıcınıza kopyalayın ve ardından klasörleri görüntülemek için dosyayı açın.

## <a name="installwin"></a>Pencerelerin

Sürüm istemcinin mimarisiyle eşleştiği sürece, her bir Windows istemci bilgisayarında aynı VPN istemcisi yapılandırma paketini kullanabilirsiniz. Desteklenen istemci işletim sistemlerinin listesi için [VPN Gateway SSS](vpn-gateway-vpn-faq.md#P2S)konusunun Noktadan siteye bölümüne bakın.

>[!NOTE]
>Bağlanmak istediğiniz Windows istemci bilgisayarında yönetici haklarına sahip olmanız gerekir.
>
>

Sertifika kimlik doğrulaması için yerel Windows VPN istemcisini yapılandırmak için aşağıdaki adımları kullanın:

1. Windows bilgisayarın mimarisine karşılık gelen VPN istemcisi yapılandırma dosyalarını seçin. 64 bit işlemci mimarisi için 'VpnClientSetupAmd64' yükleyici paketini seçin. 32 bit işlemci mimarisi için 'VpnClientSetupX86' yükleyici paketini seçin. 
2. Yüklemek için pakete çift tıklayın. Bir SmartScreen açılır penceresi görürseniz **Daha fazla bilgi**’ye ve ardından **Yine de çalıştır**’a tıklayın.
3. İstemci bilgisayarda **Ağ Ayarları**’na gidin ve **VPN** öğesine tıklayın. VPN bağlantısı, bağlandığı sanal ağın adını gösterir. 
4. Bağlanmayı denemeden önce, istemci bilgisayara bir istemci sertifikası yüklediğinizi doğrulayın. Yerel Azure sertifika kimlik doğrulaması türü kullanılırken kimlik doğrulaması için bir istemci sertifikası gereklidir. Sertifika oluşturma hakkında daha fazla bilgi için bkz. [sertifika oluşturma](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). İstemci sertifikasını nasıl yükleyeceğiniz hakkında bilgi için bkz. [istemci sertifikası yüklemesi](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="installmac"></a>Mac (OS X)

 Yerel IKEv2 VPN istemcisini Azure’a bağlanacak her Mac cihazda el ile yapılandırmanız gerekir. Azure, yerel Azure sertifika kimlik doğrulaması için mobileconfig dosyası sağlamaz. **Genel** , yapılandırma için ihtiyaç duyduğunuz tüm bilgileri içerir. İndirdiğiniz pakette Genel klasörünü görmüyorsanız, tünel türü olarak IKEv2 seçilmemiş olabilir. VPN Gateway temel SKU 'sunun Ikev2 'yi desteklemediğini unutmayın. IKEv2 seçildikten sonra zip dosyasını tekrar oluşturarak Genel klasörünü alın.<br>Genel klasörü aşağıdaki dosyaları içerir:

* Sunucu adresi ve tünel türü gibi önemli ayarları içeren **Vpnsettings. xml**. 
* P2S bağlantı kurulumu sırasında Azure VPN Gateway doğrulamak için gereken kök sertifikayı içeren **Vpnserverroot. cer**.

Mac 'te sertifika kimlik doğrulaması için yerel VPN istemcisini yapılandırmak üzere aşağıdaki adımları kullanın. Azure 'a bağlanacak her Mac üzerinde bu adımları gerçekleştirmeniz gerekir:

1. **Vpnserverroot** kök sertifikasını Mac 'e aktarın. Bu işlem, dosyanın Mac 'nize kopyalanarak ve çift tıklanarak yapılabilir. İçeri aktarmak için **Ekle** ' ye tıklayın.

   ![sertifika ekle](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Sertifikaya çift tıkladığınızda **Ekle** iletişim kutusu görüntülenmeyebilir, ancak sertifika doğru depoya yüklenmiş olabilir. Sertifika kategorisi altındaki oturum açma anahtarlığınızdan sertifikayı kontrol edebilirsiniz.
    >
  
2. P2S ayarlarını yapılandırdığınız zaman Azure 'a yüklediğiniz kök sertifika tarafından verilen bir istemci sertifikası yüklediğinizi doğrulayın. Bu, önceki adımda yüklediğiniz VPNServerRoot öğesinden farklıdır. İstemci sertifikası kimlik doğrulaması için kullanılır ve gereklidir. Sertifika oluşturma hakkında daha fazla bilgi için bkz. [sertifika oluşturma](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). İstemci sertifikasını nasıl yükleyeceğiniz hakkında bilgi için bkz. [istemci sertifikası yüklemesi](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. **Ağ tercihleri** altında **ağ** Iletişim kutusunu açın ve Azure VNET 'e P2S BAĞLANTıSı için yeni bir VPN istemci bağlantı profili oluşturmak üzere **' + '** seçeneğine tıklayın.

   **Arabirim** DEĞERI ' VPN ' ve **VPN türü** değeri ' Ikev2 '. **Hizmet adı** alanında profil için bir ad belirtin ve ardından **Oluştur** ' a tıklayarak VPN istemci bağlantı profilini oluşturun.

   ![Network](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. **Genel** klasöründe, **vpnsettings. xml** dosyasından **VPNServer** etiket değerini kopyalayın. Bu değeri, profilin **sunucu adresine** ve **uzak kimlik** alanlarına yapıştırın.

   ![sunucu bilgileri](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. **Kimlik doğrulama ayarları** ' na tıklayıp **sertifika**' yı seçin. 

   ![kimlik doğrulama ayarları](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
6. **Seç...** öğesine tıklayın kimlik doğrulaması için kullanmak istediğiniz istemci sertifikasını seçmek için. Bu, adım 2 ' de yüklediğiniz sertifikadır.

   ![sertifika](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Bir kimlik seçin seçim** yapabileceğiniz sertifikaların listesini görüntüler. Doğru sertifikayı seçip **devam**' a tıklayın.

   ![identity](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. **Yerel kimlik** alanında, sertifikanın (adım 6 ' dan) adını belirtin. Bu örnekte, "ikev2Client.com" olur. Ardından, değişiklikleri kaydetmek için **Uygula** düğmesine tıklayın.

   ![uygulayabilirsiniz](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. Tüm değişiklikleri kaydetmek için **ağ** Iletişim kutusunda **Uygula** ' ya tıklayın. Sonra, P2S bağlantısını Azure VNet 'e başlatmak için **Bağlan** ' a tıklayın.

## <a name="linuxgui"></a>Linux (Strongswa GUI)

### <a name="installstrongswan"></a>Strongswa 'yi yükler

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="genlinuxcerts"></a>Sertifika oluşturma

Önceden sertifika oluşturduysanız aşağıdaki adımları kullanın:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install"></a>Yükleyip yapılandırın

Ubuntu 18.0.4 üzerinde aşağıdaki yönergeler oluşturulmuştur. Ubuntu 16.0.10, Strongswa GUI 'yi desteklemez. Ubuntu 16.0.10 kullanmak istiyorsanız, [komut satırını](#linuxinstallcli)kullanmanız gerekir. Aşağıdaki örnekler, Linux sürümünüze ve Strongswa sürümüne bağlı olarak gördüğünüz ekranlarla eşleşmeyebilir.

1. Örnekteki komutu çalıştırarak **Strongswa** ve Ağ yöneticisini yüklemek için **terminali** açın.

   ```
   sudo apt install network-manager-strongswan
   ```
2. **Ayarlar** ' ı ve ardından **ağ**' ı seçin.

   ![bağlantıları Düzenle](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Yeni bir bağlantı oluşturmak için **+** düğmesine tıklayın.

   ![bağlantı ekleme](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Menüden **IPSec/Ikev2 (Strongswa)** öğesini seçin ve çift tıklayın. Bu adımda, bağlantınızın adını verebilirsiniz.

   ![bir bağlantı türü seçin](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. İndirilen istemci yapılandırma dosyalarında bulunan **genel** klasörden **vpnsettings. xml** dosyasını açın. **VPNServer** adlı etiketi bulun ve ' azuregateway ' ile başlayıp '. cloudapp.net ' ile biten adı kopyalayın.

   ![adı Kopyala](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Bu adı, **ağ geçidi** bölümünde yer alan yeni VPN bağlantınızın **Adres** alanına yapıştırın. Sonra, **sertifika** alanının sonundaki klasör simgesini seçin, **genel** klasöre gidin ve **vpnserverroot** dosyasını seçin.
7. Bağlantının **istemci** bölümünde, **kimlik doğrulaması**için **sertifika/özel anahtar**' ı seçin. **Sertifika** ve **özel anahtar**için, daha önce oluşturulmuş sertifikayı ve özel anahtarı seçin. **Seçenekler**' de, **Iç IP adresi iste**' yi seçin. ' A tıklayarak **Ekle**.

   ![iç IP adresi iste](./media/point-to-site-vpn-client-configuration-azure-cert/turnon.png)
8. **Bağlantıyı açın**.

## <a name="linuxinstallcli"></a>Linux (strongSwan CLı)

### <a name="install-strongswan"></a>Strongswa 'yi yükler

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>İstemci sertifikaları oluşturma

Önceden sertifika oluşturduysanız aşağıdaki adımları kullanın:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Yükleme ve yapılandırma

1. Azure portal adresinden VPNClient paketini indirin.
2. Dosyayı ayıklayın.
3. **Genel** klasöründen, VpnServerRoot. cer dosyasını/etc/IPSec.exe dizinine kopyalayın veya taşıyın.
4. CP Client. p12 öğesini/etc/IPSec.exe dizinine kopyalayın veya taşıyın. Bu dosya, Azure VPN Gateway için istemci sertifikasıdır.
5. VpnSettings. xml dosyasını açın ve `<VpnServer>` değerini kopyalayın. Bu değeri bir sonraki adımda kullanacaksınız.
6. Aşağıdaki örnekteki değerleri ayarlayın ve ardından/etc/IPSec.exe yapılandırmasına örnek ekleyin.
  
   ```
   conn azure
         keyexchange=ikev2
         type=tunnel
         leftfirewall=yes
         left=%any
         leftauth=eap-tls
         leftid=%client # use the DNS alternative name prefixed with the %
         right= Enter the VPN Server value here# Azure VPN gateway address
         rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
         rightsubnet=0.0.0.0/0
         leftsourceip=%config
         auto=add
   ```
6. */Etc/IPSec.exe*öğesine aşağıdakileri ekleyin.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

7. Aşağıdaki komutları çalıştırın:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Sonraki adımlar

[P2S yapılandırmanızı tamamlamaya](vpn-gateway-howto-point-to-site-rm-ps.md)yönelik makaleye geri dönün.

P2S bağlantılarında sorun gidermek için aşağıdaki makalelere bakın:

  * [Azure Noktadan siteye bağlantıları sorunlarını giderme](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Mac OS X VPN istemcilerinden VPN bağlantıları sorunlarını giderme](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
