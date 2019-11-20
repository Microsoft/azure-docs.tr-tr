---
title: 'Azure VPN Gateway: P2S VPN istemci profilleri hakkında'
description: Bu, istemci profili dosyasıyla çalışmanıza yardımcı olur
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 5386cace7191be60534f0d2fbf4a85b592d1ecdd
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151630"
---
# <a name="about-p2s-vpn-client-profiles"></a>P2S VPN istemci profilleri hakkında

İndirilen profil dosyası, bir VPN bağlantısını yapılandırmak için gereken bilgileri içerir. Bu makale, bir VPN istemci profili için gereken bilgileri elde etmenize ve anlamanıza yardımcı olur.

## <a name="1-download-the-file"></a>1. dosyayı indirin

Aşağıdaki komutları çalıştırın. Profil ZIP dosyasını indirmek için sonuç URL 'sini tarayıcınıza kopyalayın.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. zip dosyasını Ayıkla

Zip dosyasını ayıklayın. Dosya aşağıdaki klasörleri içerir:

* AzureVPN
* Genel
* OpenVPN (ağ geçidinde OpenVPN ve Azure AD kimlik doğrulaması ayarlarını etkinleştirdiyseniz). Bkz. [kiracı oluşturma](openvpn-azure-ad-tenant.md).)

## <a name="3-retrieve-information"></a>3. bilgileri alma

**AzureVPN** klasöründe, ***azurevpnconfig. xml*** dosyasına gidin ve Not defteri ile açın. Aşağıdaki Etiketler arasındaki metni bir yere unutmayın.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Profil ayrıntıları

Bir bağlantı eklediğinizde, profil ayrıntıları sayfası için önceki adımda topladığınız bilgileri kullanın. Alanlar aşağıdaki bilgilere karşılık gelir:

   * **Hedef kitle:** Belirtecin hedeflenen alıcı kaynağını tanımlar
   * **Veren:** Belirteci oluşturan güvenlik belirteci hizmetini (STS) ve Azure AD kiracısını tanımlar
   * **Kiracı:** Belirteci veren dizin kiracının sabit, benzersiz bir tanımlayıcısını içerir
   * **FQDN:** Azure VPN Gateway 'de tam etki alanı adı (FQDN)
   * **Sunucusecret:** VPN Gateway önceden paylaşılan anahtarı

## <a name="folder-contents"></a>Klasör içeriği

* **OpenVPN klasörü** , anahtarı ve sertifikayı içerecek şekilde değiştirilmesi gereken *ovpn* profilini içerir. Daha fazla bilgi için bkz. [Azure Için OpenVPN Istemcilerini yapılandırma VPN Gateway](vpn-gateway-howto-openvpn-clients.md#windows).

* **Genel klasör** , ortak sunucu sertifikasını ve vpnsettings. xml dosyasını içerir. VpnSettings. xml dosyası, genel bir istemciyi yapılandırmak için gereken bilgileri içerir.

* İndirilen ZIP dosyası, **WindowsAmd64** ve **WindowsX86** klasörlerini de içerebilir. Bu klasörler, Windows istemcileri için SSTP ve Ikev2 için yükleyiciyi içerir. İstemci üzerinde yönetici haklarına sahip olmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Noktadan siteye hakkında daha fazla bilgi için bkz. [Noktadan siteye hakkında](point-to-site-about.md).
