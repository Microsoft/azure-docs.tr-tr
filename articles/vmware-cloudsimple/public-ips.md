---
title: CloudSimple ile Azure VMware çözümü genel IP adreslerini ayır
description: Özel Bulut ortamındaki sanal makineler için genel IP adreslerinin nasıl ayrılacağını açıklar
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 565c9ad0fbd37b026a1ba555d83b9032d2efcba4
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773796"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>Özel bulut ortamı için genel IP adresleri ayır

Özel bulut ortamınızdaki sanal makineler için genel IP adresleri ayırmak üzere ağ sayfasında ortak IP 'Ler sekmesini açın.

1. [CloudSimple portalına erişin](access-cloudsimple-portal.md) ve yan menüdeki **ağ** ' ı seçin.
2. **Genel IP 'leri**seçin.
3. **Yeni genel IP**' ye tıklayın.

    ![Genel IP 'Ler sayfası](media/public-ips-page.png)

4. IP adresi girişini tanımlamak için bir ad girin.
5. Varsayılan konumu koruyun.
6. Gerekirse, boşta kalma zaman aşımını değiştirmek için kaydırıcıyı kullanın.
7. Genel IP adresi atamak istediğiniz yerel IP adresini girin.
8. İlişkili bir DNS adı girin.
9. **Gönder**'e tıklayın.

![Genel IP 'Leri ayır](media/network-public-ip-allocate.png)

Genel IP adresini ayırma görevi başlar. Görevin durumunu **etkinlik > görevler** sayfasında kontrol edebilirsiniz. Ayırma tamamlandığında, yeni giriş genel IP 'Ler sayfasında gösterilir.
