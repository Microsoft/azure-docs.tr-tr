---
title: include dosyası
description: include dosyası
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3bd9489adaf46e604393fc7059d37443bdd5ec3e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488880"
---
1. Oluşturduğunuz sanal WAN 'ı bulun. Sanal WAN sayfasında, **bağlantı** bölümünde, **hub**' ı seçin.
2. Hub 'Lar sayfasında **+ yeni hub** ' ı seçerek **sanal hub oluştur** sayfasını açın.

    ![Temel Bilgiler](./media/virtual-wan-tutorial-hub-include/basics.png "Temel Bilgiler")
3. **Sanal hub** sayfası **temel bilgileri** Oluştur sekmesinde aşağıdaki alanları doldurun:

    **Proje ayrıntıları**

   * Bölge (daha önce konum olarak adlandırılır)
   * Ad
   * Hub özel adres alanı. En küçük adres alanı bir hub oluşturmak için/24 ' tir. Bu,/25 ile/32 arasında herhangi bir aralığın oluşturulması sırasında bir hata üretecektir.
4. **İleri ' yi seçin: siteden siteye**.

    ![Siteden siteye](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Siteden siteye")

5. **Siteden siteye** sekmesinde aşağıdaki alanları doldurun:

   * Siteden siteye VPN oluşturmak için **Evet** ' i seçin.
   * AS numarası alanı şu anda sanal hub 'da düzenlenemez.
   * Açılan menüden **ağ geçidi ölçek birimleri** değerini seçin. Ölçek birimi, siteleri bağlamak için sanal hub 'da oluşturulan VPN ağ geçidinin toplam verimini seçmenizi sağlar. 1 ölçek birimi = 500 Mbps ' i seçerseniz, her biri en fazla 500 Mbps hızında olacak şekilde, artıklık için iki örnek oluşturulacaktır. Örneğin, her biri dalda 10 Mbps olan beş dala sahipseniz baş uçta 50 Mbps bir toplama işlemi yapmanız gerekir. Azure VPN ağ geçidinin Birleşik kapasitesini planlama, hub 'a dal sayısını desteklemek için gereken kapasite değerlendirdikten sonra yapılmalıdır.
6. Doğrulamak için **gözden geçir + oluştur** ' u seçin.
7. Hub 'ı oluşturmak için **Oluştur** ' u seçin. 30 dakika **sonra hub 'ı bir sayfada görüntülemek** için **yenileyin** . Kaynağa gitmek için **Kaynağa Git** ' i seçin.