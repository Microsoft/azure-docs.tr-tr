---
title: include dosyası
description: include dosyası
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/21/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 083ab61d5a20bfb8e38747ae0694b1176c0a0fd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521551"
---
1. [Azure portalını](https://portal.azure.com) açın. Bağlanmak istediğiniz sanal makineye gidin ve sonra **Bağlan**' ı seçin. Açılan **listeden savunma** ' yı seçin.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="Savunma seçin":::

1. Açılan listeden atlama ' yi seçtikten sonra, üç sekmeye sahip olan bir yan çubuk görüntülenir: RDP, SSH ve savunma. Sanal ağ için savunma sağlanmakta olduğundan, savunma sekmesi varsayılan olarak etkindir. **Kullanım kullanımı**' nı seçin.

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="Kullanımı kullan seçeneğini belirleyin":::

1. Azure savunma **kullanarak bağlan** sayfasında, sanal makinenizin Kullanıcı adını ve parolasını girin ve ardından **Bağlan**' ı seçin.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm-host.png" alt-text="Bağlan":::

1. Bu sanal makineyle savunma aracılığıyla RDP bağlantısı, bağlantı noktası 443 ve savunma hizmeti kullanılarak doğrudan Azure portal (HTML5 üzerinden) açılır.

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="Bağlantı noktası 443 kullanarak bağlan":::
