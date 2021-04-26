---
title: 'Azure sanal WAN: Kullanıcı VPN istemci profilleri'
description: Bu, istemci profili dosyasıyla çalışmanıza yardımcı olur
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: d83b6ed2ae83db569d3c61e3cf4cd887f875eb25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99980924"
---
# <a name="working-with-user-vpn-client-profile-files"></a>Kullanıcı VPN istemci profili dosyalarıyla çalışma

Profil dosyaları, bir VPN bağlantısını yapılandırmak için gereken bilgileri içerir. Bu makale, bir kullanıcı VPN istemci profili için gereken bilgileri elde etmenize ve anlamanıza yardımcı olur.

## <a name="download-the-profile"></a>Profili İndir

İstemci profili ZIP dosyasını indirmek için [yükleme profilleri](global-hub-profile.md) makalesindeki adımları kullanabilirsiniz.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **OpenVPN klasörü** , anahtarı ve sertifikayı içerecek şekilde değiştirilmesi gereken *ovpn* profilini içerir. Daha fazla bilgi için bkz. [OpenVPN Istemcilerini yapılandırma](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Sonraki adımlar

Sanal WAN Kullanıcı VPN hakkında daha fazla bilgi için bkz. [Kullanıcı VPN bağlantısı oluşturma](virtual-wan-point-to-site-portal.md).
