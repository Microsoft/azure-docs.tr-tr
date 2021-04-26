---
title: include dosyası
titleSuffix: Azure
description: include dosyası
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: c3212acd5015edbb639b8904b885c718d654e5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96356270"
---
Bu bölümde, doğrudan eşleme için aşağıdaki değişiklik işlemlerinin nasıl gerçekleştirileceği açıklanmaktadır.

### <a name="add-direct-peering-connections"></a>Doğrudan eşleme bağlantıları Ekle
1. **+ Bağlantı ekle** düğmesini seçin ve yeni bir eşleme bağlantısı yapılandırın.
    > [!div class="mx-imgBorder"]
    > ![AshburnPeering-Connections sayfası bağlantıları listeler. + Bağlantı Ekle düğmesi vurgulanır.](../media/setup-direct-modify-addconnection.png)

1. **Doğrudan eşleme bağlantısı** formunu doldurun ve **Kaydet**' i seçin. Eşleme bağlantısı yapılandırmaya yönelik yardım için "doğrudan eşleme oluşturma ve sağlama" bölümündeki adımları gözden geçirin.
    > [!div class="mx-imgBorder"]
    > ![Doğrudan eşleme bağlantı formu](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Doğrudan eşleme bağlantılarını kaldır

Bir bağlantının kaldırılması Azure portal Şu anda desteklenmiyor. Daha fazla bilgi için [Microsoft eşlemesi](mailto:peeringexperience@microsoft.com)'ne başvurun.

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Etkin bağlantılarda bant genişliğini yükseltme veya düşürme
1. Değiştirmek istediğiniz eşleme bağlantısını seçin ve ardından **..**  >  . seçeneğini belirleyin. **Bağlantıyı Düzenle**.
    > [!div class="mx-imgBorder"]
    > ![Bağlantıyı Düzenle](../media/setup-direct-modify-editconnection.png)

1. Kaydırıcıyı taşıyarak bant genişliğini değiştirin ve ardından **Kaydet**' i seçin.
    > [!div class="mx-imgBorder"]
    > ![Bant genişliğini değiştirme](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>Etkin bağlantılara IPv4 veya IPv6 oturumu bilgilerini ekleme
1. Değiştirmek istediğiniz eşleme bağlantısını seçin ve ardından **..**  >  . seçeneğini belirleyin. **Bağlantıyı** adım 1 ' de gösterildiği gibi düzenleyin.
1. **Oturum IPv4 öneki** veya **oturum IPv6 ön eki** bilgilerini girin ve **Kaydet**' i seçin.

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>Etkin bağlantılardaki IPv4 veya IPv6 oturumu bilgilerini Kaldır
**Oturum IPv4 öneki** veya **oturum IPv6 öneki** bilgileri kaldırılırken portalda Şu anda desteklenmiyor. Daha fazla bilgi için [Microsoft eşlemesi](mailto:peeringexperience@microsoft.com)'ne başvurun.
