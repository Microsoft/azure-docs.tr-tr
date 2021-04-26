---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/08/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a5ae81cde28efbf57965beda2f82915854579e43
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106073206"
---
## <a name="extract-the-zip-file"></a>ZIP dosyasını Ayıkla

Zip dosyasını ayıklayın. Dosya aşağıdaki klasörleri içerir:

* AzureVPN
* Genel
* OpenVPN ( **Azure sertifikası** Ile OpenVPN 'i veya ağ geçidinde **RADIUS kimlik doğrulama** ayarlarını etkinleştirdiyseniz). Kiracı oluşturmak için yapılandırmanıza karşılık gelen ilgili makaleyi seçin.

  * [VPN Gateway-kiracı oluşturun](../articles/vpn-gateway/openvpn-azure-ad-tenant.md).
  * [Sanal WAN-kiracı oluşturun](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="retrieve-information"></a>Bilgi alma

**AzureVPN** klasöründe **_azurevpnconfig.xml_** dosyasına gidin ve Not defteri ile açın. Aşağıdaki Etiketler arasındaki metni bir yere unutmayın.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Profil ayrıntıları

Bir bağlantı eklediğinizde, profil ayrıntıları sayfası için önceki adımda topladığınız bilgileri kullanın. Alanlar aşağıdaki bilgilere karşılık gelir:

* **Hedef kitle:** Belirtecin hedeflenen alıcı kaynağını tanımlar.
* **Veren:** Belirteci oluşturan güvenlik belirteci hizmetini (STS) ve Azure AD kiracısını tanımlar.
* **Kiracı:** Belirteci veren dizin kiracının sabit, benzersiz bir tanımlayıcısını içerir.
* **FQDN:** Azure VPN ağ geçidinde tam etki alanı adı (FQDN).
* **Sunucusecret:** VPN Gateway önceden paylaşılan anahtarı.

## <a name="folder-contents"></a>Klasör içeriği

* **Genel klasör** , ortak sunucu sertifikasını ve VpnSettings.xml dosyasını içerir. VpnSettings.xml dosyası bir genel istemciyi yapılandırmak için gereken bilgileri içerir.

* İndirilen ZIP dosyası, **WindowsAmd64** ve **WindowsX86** klasörlerini de içerebilir. Bu klasörler, Windows istemcileri için SSTP ve Ikev2 için yükleyiciyi içerir. İstemci üzerinde yönetici haklarına sahip olmanız gerekir.
