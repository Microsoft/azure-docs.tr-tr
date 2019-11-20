---
title: 'VPN Gateway: VPN istemcisi-Azure AD kimlik doğrulaması sorunlarını giderme'
description: Azure AD kimlik doğrulama istemcilerinin P2S VPN Gateway sorunlarını giderme
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 8871e92f0911c4d3cbcc1772bef1daeb5c70b5d7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151964"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Azure AD kimlik doğrulaması VPN istemcisinde sorun giderme

Bu makale, Noktadan siteye VPN ve Azure Active Directory kimlik doğrulaması kullanarak bir sanal ağa bağlanmak için bir VPN istemcisinin sorunlarını gidermenize yardımcı olur.

## <a name="status"></a>Durum günlüğünü görüntüle

Hata iletileri için durum günlüğünü görüntüleyin.

![logs](./media/troubleshoot-ad-vpn-client/1.png)

1. **Durum günlüklerini**göstermek için istemci penceresinin sağ alt köşesindeki oklar simgesine tıklayın.
2. Sorunu gösterebilen hatalar için günlüklere bakın.
3. Hata iletileri kırmızı renkte görüntülenir.

## <a name="clear"></a>Oturum açma bilgilerini temizle

Oturum açma bilgilerini temizleyin.

![oturum aç](./media/troubleshoot-ad-vpn-client/2.png)

1. Şunu seçin... sorun gidermek istediğiniz profilin yanında. **Yapılandır-> kayıtlı hesabı temizle**' yi seçin.
2. **Kaydet**’i seçin.
3. Bağlanmayı deneyin.
4. Bağlantı hala başarısız olursa, sonraki bölüme geçin.

## <a name="diagnostics"></a>Tanılamayı Çalıştır

VPN istemcisinde tanılamayı çalıştırın.

![tanılama](./media/troubleshoot-ad-vpn-client/3.png)

1. **..** . Öğesine tıklayın. Tanılamayı çalıştırmak istediğiniz profilin yanında. **Tanıla-> çalıştırma tanılaması**' nı seçin.
2. İstemci bir dizi testi çalıştırır ve testin sonucunu görüntüler

   * İnternet erişimi – istemcinin Internet bağlantısı olup olmadığını denetler
   * İstemci kimlik bilgileri – Azure Active Directory kimlik doğrulama uç noktasının erişilebilir olup olmadığını denetleyin
   * Sunucu çözülebilir – yapılandırılmış VPN sunucusunun IP adresini çözümlemek için DNS sunucusuyla Iletişim kurar
   * Erişilebilir sunucu – VPN sunucusunun yanıt verip vermediğini denetler
3. Testlerin herhangi biri başarısız olursa, sorunu gidermek için ağ yöneticinize başvurun.
4. Sonraki bölümde, gerekirse günlüklerin nasıl toplanacağı gösterilmektedir.

## <a name="logfiles"></a>İstemci günlük dosyalarını topla

VPN istemci günlük dosyalarını toplayın. Günlük dosyaları, seçtiğiniz bir yöntem aracılığıyla destek/yöneticiye gönderilebilir. Örneğin, e-posta.

1. "..." Düğmesine tıklayın. Tanılamayı çalıştırmak istediğiniz profilin yanında. **Tanıla-> Günlükler dizinini göster**' i seçin.

   ![günlükleri göster](./media/troubleshoot-ad-vpn-client/4.png)
2. Windows Gezgini, günlük dosyalarını içeren klasörü açar.

   ![dosyayı görüntüle](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [Azure AD kimlik doğrulaması kullanan P2S Open VPN bağlantıları için Azure Active Directory kiracı oluşturma](openvpn-azure-ad-tenant.md).