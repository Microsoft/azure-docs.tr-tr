---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 48f33514510618abadf329a11a9ab71a020be0bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92479585"
---
1. Sanal ağ geçidinizin sayfasını açın. Ağ geçidine, **sanal ağınızın adına gidip > genel bakış ' ı > bağlı cihazlar-> adı**' na giderek çok daha fazla farklı yol olsa da gidebilirsiniz.
1. Ağ geçidinin sayfasında **Bağlantılar**' ı seçin. Bağlantılar sayfasının en üstünde **+ Ekle** ' yi seçerek **bağlantı ekle** sayfasını açın.

   :::image type="content" source="./media/vpn-gateway-add-site-to-site-connection-portal-include/connection.png" alt-text="Siteden siteye bağlantı":::
1. **Bağlantı ekle** sayfasında, bağlantınız için değerleri yapılandırın.

   * **Ad:** Bağlantınızı adlandırın.
   * **Bağlantı türü:** **Siteden siteye (IPSec)** seçeneğini belirleyin.
   * **Sanal ağ geçidi:** Bu ağ geçidinden bağlandığınız için değer sabittir.
   * **Yerel ağ geçidi:** **Bir yerel ağ geçidi seçin** ' i seçin ve kullanmak istediğiniz yerel ağ geçidini seçin.
   * **Paylaşılan Anahtar:** Buradaki değer, yerel şirket için VPN cihazınız için kullandığınız değerle eşleşmelidir. Örnekte 'abc123' değeri kullanılmıştır, ancak siz daha karmaşık bir değer kullanabilirsiniz (ve kullanmalısınız). Önemli olan, burada belirttiğiniz değerin VPN cihazınızı yapılandırırken belirttiğiniz değerle aynı olmasıdır.
   * **Azure özel IP adresini kullan** ' ın işaretini kaldırın.
   * **BGP etkinleştirme** işaretini kaldırın.
   * **Ikev2** öğesini seçin.
   * **Abonelik**, **Kaynak Grubu** ve **Konum** için kalan değerler sabittir.

1. Bağlantınızı oluşturmak için **Tamam ' ı** seçin. Ekranda yanıp sönen *Bağlantısı Oluşturuluyor* yazısını göreceksiniz.
1. Bağlantıyı sanal ağ geçidinin **Bağlantılar** sayfasında görüntüleyebilirsiniz. *Bilinmiyor* Durumu *Bağlanıyor* olarak ve ardından *Başarılı* olarak değişir.
