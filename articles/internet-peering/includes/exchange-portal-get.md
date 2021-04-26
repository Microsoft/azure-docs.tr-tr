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
ms.openlocfilehash: e139954e6550e33edb75d01ab9dbec0bba543ea6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92548262"
---
**Eşleme** kaynağı başarıyla dağıtıldıktan sonra, bu adımları izleyerek görüntüleyebilirsiniz.

1. **Kaynak grupları**' na gidin ve **eşleme** kaynağını oluştururken seçtiğiniz kaynak grubunu seçin. Çok fazla kaynak grubunuz varsa **filtre** kutusunu kullanın.

    > [!div class="mx-imgBorder"]
    > ![Kaynak grupları](../media/setup-direct-get-resourcegroup.png)

1. Oluşturduğunuz **eşleme** kaynağını seçin.

    > [!div class="mx-imgBorder"]
    > ![Oluşturduğunuz eşleme kaynağını gösteren ekran görüntüsü.](../media/setup-direct-get-open.png)

1. **Genel bakış** sayfası, burada gösterildiği gibi üst düzey bilgileri gösterir.

    > [!div class="mx-imgBorder"]
    > ![Eşleme kaynağına genel bakış bölmesi](../media/setup-exchange-get-overview.png)

1. Sol tarafta, PeerAsn oluştururken gönderilen bilgileri görüntülemek için **ASN bilgilerini** seçin.

    > [!div class="mx-imgBorder"]
    > ![Eşleme kaynağı ASN bilgileri](../media/setup-direct-get-asninfo.png)

1. Sol tarafta **Bağlantılar**' ı seçin. Ekranın üst kısmında, bağlantı hattı kapsamındaki farklı tesislerde ASN ile Microsoft arasındaki eşleme bağlantılarının bir özetini görürsünüz. Ayrıca, gösterilen şekilde orta bölmedeki **Bağlantılar** ' ı seçerek **genel bakış** sayfasından bağlantı özetine erişebilirsiniz.

    > [!div class="mx-imgBorder"]
    > ![Eşleme kaynağı bağlantıları](../media/setup-exchange-get-connectionssummary.png)

    * **Bağlantı durumu** , eşleme bağlantısı kurulumunun durumuna karşılık gelir. Bu alanda gösterilen durumlar, [Exchange eşleme gözden geçirmede](../walkthrough-exchange-all.md)gösterilen durum diyagramını izler.
    * **IPv4 oturum durumu** ve **IPv6 oturum durumu** SıRASıYLA IPv4 ve IPv6 BGP oturum durumlarına karşılık gelir.  
    * Ekranın üst kısmında bir satır seçtiğinizde, alt taraftaki **bağlantı** bölümünde her bağlantı için ayrıntılar gösterilir. **Yapılandırma**, **IPv4 adresi** ve **IPv6 adresini** genişletmek için okları seçin.

    > [!div class="mx-imgBorder"]
    > ![Bir bölümü genişleten bir oku vurgulayan ekran görüntüsü.](../media/setup-exchange-get-connectionsipv4.png)
