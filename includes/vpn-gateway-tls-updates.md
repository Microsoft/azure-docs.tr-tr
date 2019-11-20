---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e4d20cd39d2a843ee1ab57a412ac668b3495fdb1
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "67188195"
---
>[!NOTE]
>1 Temmuz 2018 tarihinden itibaren TLS 1.0 ve 1.1 desteği Azure VPN Gateway'den kaldırılıyor. VPN Gateway, yalnızca TLS 1.2’yi destekleyecektir. Destek sağlamak için, [TLS 1.2 desteğini etkinleştirmek üzere güncelleştirmeler](#tls1)bölümüne bakın.

Ayrıca, aşağıdaki eski algoritmalar 1 Temmuz 2018 tarihinde TLS için de kullanım dışı olacaktır:

* RC4 (Rivest Şifrelemesi 4)
* DES (Veri Şifreleme Algoritması)
* 3DES (Üçlü Veri Şifreleme Algoritması)
* MD5 (İleti Özeti 5)

### <a name="tls1"></a>Windows 7 ve Windows 8.1 Nasıl yaparım? TLS 1,2 desteğini etkinleştirmek istiyor musunuz?

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
