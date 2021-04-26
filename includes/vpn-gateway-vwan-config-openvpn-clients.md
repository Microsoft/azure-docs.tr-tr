---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ee9dc61169b182356e8ed272bc64785e9c3f6384
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106073080"
---
## <a name="windows-clients"></a><a name="windows"></a>Windows istemcileri

1. OpenVPN istemcisini (sürüm 2,4 veya üzeri) resmi [OpenVPN Web sitesinden](https://openvpn.net/index.php/open-source/downloads.html)indirip yükleyin.
2. Ağ geçidinin VPN profilini indirin. Bu, Azure portal ' deki Noktadan siteye Yapılandırma sekmesinden veya PowerShell 'deki ' New-AzVpnClientConfiguration ' öğesinden yapılabilir.
3. Profilin sıkıştırmasını açın. Ardından, Not defteri 'Ni kullanarak OpenVPN klasöründen *vpnconfig. ovpn* yapılandırma dosyasını açın.
4. Oluşturduğunuz ve ağ geçidinde P2S yapılandırmanıza yüklediğiniz noktadan siteye istemci sertifikasını dışarı aktarın. Aşağıdaki makale bağlantılarını kullanın:

   * [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) yönergeler
   
   * [Sanal WAN](../articles/virtual-wan/certificates-point-to-site.md#clientexport) yönergeleri
5. Özel anahtarı ve Base64 parmak izini *. pfx* dosyasından ayıklayın. Bunu yapmanın birden çok yolu vardır. Makinenizde OpenSSL kullanmak tek bir yoldur. *profileinfo.txt* dosyası, CA ve istemci sertifikası için özel anahtarı ve parmak izini içerir. İstemci sertifikasının parmak izini kullandığınızdan emin olun.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. *profileinfo.txt* Not defteri 'nde açın. İstemci (alt) sertifikasının parmak izini almak için, alt sertifika için "-----başlangıç SERTIFIKASı-----" ve "-----son SERTIFIKA-----" gibi metni seçin ve kopyalayın. Konu =/satırına bakarak alt sertifikayı belirleyebilirsiniz.
7. Adım 3 ' teki Not defteri 'nde açtığınız *vpnconfig. ovpn* dosyasına geçin. Aşağıda gösterilen bölümü bulun ve "CERT" ve "/CERT" arasındaki her şeyi değiştirin.

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. *profileinfo.txt* Not defteri 'nde açın. Özel anahtarı almak için, "özel anahtar----------" ve "-----son özel anahtar-----
9. Not defteri 'nde vpnconfig. ovpn dosyasına dönün ve bu bölümü bulun. Özel anahtarı, ve "Key" ve "/Key" arasındaki her şeyi değiştirerek yapıştırın.

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```
10. Başka bir alanı değiştirmeyin. VPN’e bağlanmak için istemci girişinde doldurulmuş yapılandırmayı kullanın.
11. vpnconfig.ovpn dosyasını C:\Program Files\OpenVPN\config klasörüne kopyalayın.
12. Sistem tepsisindeki OpenVPN simgesine sağ tıklayın ve Bağlan’a tıklayın.

## <a name="mac-clients"></a><a name="mac"></a>Mac istemcileri

1. [Tunnelblick](https://tunnelblick.net/downloads.html)gibi bir OpenVPN istemcisini indirip yükleyin. 
2. Ağ geçidinin VPN profilini indirin. Bu, Azure portal Noktadan siteye Yapılandırma sekmesinden veya PowerShell 'de ' New-AzVpnClientConfiguration ' kullanılarak yapılabilir.
3. Profilin sıkıştırmasını açın. Bir metin düzenleyicisinde OpenVPN klasöründen vpnconfig. ovpn yapılandırma dosyasını açın.
4. P2S istemci sertifikası bölümünü base64’teki P2S istemci sertifikası genel anahtarı ile doldurun. PEM biçimli bir sertifikada .cer dosyasını açıp base64 anahtarını sertifika üst bilgileri arasına kopyalamanız yeterlidir. Kodlanmış ortak anahtarı almak için bir sertifikanın nasıl dışarı aktarılacağı hakkında bilgi için aşağıdaki makale bağlantılarını kullanın:

   * [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) yönergeler 
   
   * [Sanal WAN](../articles/virtual-wan/certificates-point-to-site.md#cer) yönergeleri
5. Özel anahtar bölümünü, base64’teki P2S istemci sertifikası özel anahtarı ile doldurun. Özel bir anahtarı ayıklama hakkında bilgi için bkz. OpenVPN sitesinde [özel anahtarınızı dışarı aktarma](https://openvpn.net/community-resources/how-to/#pki) .
6. Başka bir alanı değiştirmeyin. VPN’e bağlanmak için istemci girişinde doldurulmuş yapılandırmayı kullanın.
7. Profili oluşturmak için profil dosyasına çift tıklayın Tunnelblick içinde profil oluşturun.
8. Uygulamalar klasöründen Tunnelblick başlatın.
9. Sistem tepsisindeki Tunnelblick simgesine tıklayın ve Bağlan ' ı seçin.

> [!IMPORTANT]
>OpenVPN protokolünde yalnızca iOS 11,0 ve üzeri ve MacOS 10,13 ve üzeri desteklenir.
>
## <a name="ios-clients"></a><a name="iOS"></a>iOS istemcileri

1. App Store 'dan OpenVPN istemcisini (sürüm 2,4 veya üzeri) yükler.
2. Ağ geçidinin VPN profilini indirin. Bu, Azure portal Noktadan siteye Yapılandırma sekmesinden veya PowerShell 'de ' New-AzVpnClientConfiguration ' kullanılarak yapılabilir.
3. Profilin sıkıştırmasını açın. Bir metin düzenleyicisinde OpenVPN klasöründen vpnconfig. ovpn yapılandırma dosyasını açın.
4. P2S istemci sertifikası bölümünü base64’teki P2S istemci sertifikası genel anahtarı ile doldurun. PEM biçimli bir sertifikada .cer dosyasını açıp base64 anahtarını sertifika üst bilgileri arasına kopyalamanız yeterlidir. Kodlanmış ortak anahtarı almak için bir sertifikanın nasıl dışarı aktarılacağı hakkında bilgi için aşağıdaki makale bağlantılarını kullanın:

   * [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) yönergeler 
   
   * [Sanal WAN](../articles/virtual-wan/certificates-point-to-site.md#cer) yönergeleri
5. Özel anahtar bölümünü, base64’teki P2S istemci sertifikası özel anahtarı ile doldurun. Özel bir anahtarı ayıklama hakkında bilgi için bkz. OpenVPN sitesinde [özel anahtarınızı dışarı aktarma](https://openvpn.net/community-resources/how-to/#pki) .
6. Başka bir alanı değiştirmeyin.
7. İPhone 'inizdeki posta uygulamasında yapılandırılmış olan e-posta hesabınıza profil dosyasını (. ovpn) e-posta ile gönderin. 
8. İPhone 'daki posta uygulamasında e-postayı açın ve ekli dosyaya dokunun

    ![E-postayı aç](./media/vpn-gateway-vwan-config-openvpn-clients/ios2.png)

9. **OpenVPN 'e Kopyala** seçeneğini görmüyorsanız, **daha fazla** öğesine dokunun

    ![Daha fazla](./media/vpn-gateway-vwan-config-openvpn-clients/ios3.png)

10. **OpenVPN 'e Kopyala** seçeneğine dokunun 

    ![OpenVPN 'e Kopyala](./media/vpn-gateway-vwan-config-openvpn-clients/ios4.png)

11. **Profili Içeri aktar** sayfasında **Ekle** ' ye dokunun

    ![Ekle](./media/vpn-gateway-vwan-config-openvpn-clients/ios5.png)

12. **Içeri aktarılan profil** sayfasında **Ekle** ' ye dokunun

    ![Ekle 'ye dokunun](./media/vpn-gateway-vwan-config-openvpn-clients/ios6.png)

13. OpenVPN uygulamasını başlatın ve anahtarı bağlamak için **profil** sayfasına sağ kaydırın

    ![Bağlanma](./media/vpn-gateway-vwan-config-openvpn-clients/ios8.png)


## <a name="linux-clients"></a><a name="linux"></a>Linux istemcileri

1. Yeni bir terminal oturumu açın. Aynı anda ' Ctrl + Alt + t ' tuşlarına basarak yeni bir oturum açabilirsiniz.
2. Gerekli bileşenleri yüklemek için aşağıdaki komutu girin:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Ağ geçidinin VPN profilini indirin. Bu, Azure portal Noktadan siteye Yapılandırma sekmesinden yapılabilir.
4. Oluşturduğunuz ve ağ geçidinde P2S yapılandırmanıza yüklediğiniz P2S istemci sertifikasını dışarı aktarın. Aşağıdaki makale bağlantılarını kullanın:

   * [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) yönergeler 
   
   * [Sanal WAN](../articles/virtual-wan/certificates-point-to-site.md#clientexport) yönergeleri
5. Özel anahtarı ve Base64 parmak izini. pfx dosyasından ayıklayın. Bunu yapmanın birden çok yolu vardır. Bilgisayarınızda OpenSSL kullanmak tek bir yoldur.

    ```
    openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   *profileinfo.txt* dosyası, CA için özel anahtarı ve parmak Izini ve istemci sertifikasını içerir. İstemci sertifikasının parmak izini kullandığınızdan emin olun.

6. *profileinfo.txt* bir metin düzenleyicisinde açın. İstemci (alt) sertifikasının parmak izini almak için, alt sertifika için "-----başlangıç SERTIFIKASı-----" ve "-----son SERTIFIKA-----" gibi metni seçin ve kopyalayın. Konu =/satırına bakarak alt sertifikayı belirleyebilirsiniz.

7. *Vpnconfig. ovpn* dosyasını açın ve aşağıda gösterilen bölümü bulun. Ve "CERT" ve "/CERT" arasındaki her şeyi değiştirin.

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. profileinfo.txt bir metin düzenleyicisinde açın. Özel anahtarı almak için, "-----başlangıç özel anahtarı-----" ve "-----son özel anahtar-----" dahil olmak üzere metni seçin ve kopyalayın.

9. Vpnconfig. ovpn dosyasını bir metin düzenleyicisinde açın ve bu bölümü bulun. Özel anahtarı, ve "Key" ve "/Key" arasındaki her şeyi değiştirerek yapıştırın.

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Başka bir alanı değiştirmeyin. VPN’e bağlanmak için istemci girişinde doldurulmuş yapılandırmayı kullanın.
11. Komut satırını kullanarak bağlanmak için aşağıdaki komutu yazın:
  
    ```
    sudo openvpn --config <name and path of your VPN profile file>&
    ```
12. GUI 'yi kullanarak bağlanmak için sistem ayarları ' na gidin.
13. **+** Yeni BIR VPN bağlantısı eklemek için tıklayın.
14. **VPN Ekle** altında **Dosyadan içeri aktar ' ı seçin...**
15. Profil dosyasına gidin ve çift tıklayın veya **Aç**' ı seçin.
16. **VPN Ekle** penceresinde **Ekle** ' ye tıklayın.
  
    ![Dosyadan içeri aktar](./media/vpn-gateway-vwan-config-openvpn-clients/import.png)
17. **Ağ ayarları** sayfasında veya sistem tepsisindeki ağ SIMGESI altında VPN **'yi** açarak bağlanabilirsiniz.
