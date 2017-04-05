---
title: "Windows Server VM’ye bağlanma | Microsoft Belgeleri"
description: "Azure portal ve Resource Manager dağıtım yöneticisini kullanarak bir Windows VM’ye nasıl bağlanacağınızı ve oturum açacağınızı öğrenin."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/01/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 88431377a36d5bc36220c630f0c8d4a46ab4a434
ms.lasthandoff: 03/31/2017


---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Windows çalıştıran bir Azure Virtual Machine’e bağlanma ve oturum açma
Bir Windows masaüstü bilgisayarından Uzak Masaüstü (RDP) oturumu başlatmak için Azure portalında **Bağlan** düğmesini kullanırsınız. Önce sanal makineye bağlandıktan sonra oturum açın.

Bir Mac bilgisayardan bir Windows VM’e bağlanmaya çalışıyorsanız, Mac bilgisayar için [Microsoft Uzak Masaüstü](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417) gibi bir RDP istemcisi yüklemeniz gerekir.

## <a name="connect-to-the-virtual-machine"></a>Sanal makineye bağlanma
1. Önceden yapmadıysanız, [Azure portal](https://portal.azure.com/)da oturum açın
2. Hub menüsünde, **Virtual Machines**’e tıklayın.
3. Listeden sanal makineyi seçin.
4. Sanal makine için dikey pencere üzerinde **Bağlan**’a tıklayın.
   
    ![VM'nize nasıl bağlanacağınızı gösteren Azure portal ekran görüntüsü.](./media/connect-logon/connect.png)
   
   > [!TIP]
   > Portal'daki **Bağlan** düğmesi gri ise ve Azure’a bir [Express Route](../../expressroute/expressroute-introduction.md) veya [Siteden Siteye VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) aracılığıyla bağlı değilseniz RDP kullanmadan önce IP adresi oluşturmalı ve VM’nize atamalısınız. [Azure’da genel IP adresleri](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) hakkında daha fazlasını okuyabilirsiniz.
   > 
   > 

## <a name="log-on-to-the-virtual-machine"></a>Sanal makinede oturum açma
[!INCLUDE [virtual-machines-log-on-win-server](../../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Sonraki adımlar
Bağlanmayı denediğinizde sorun yaşıyorsanız, bkz. [Uzak Masaüstü bağlantı sorunlarını giderme](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Bu makale ortak sorunları tanılama ve çözme boyunca size yol gösterecektir.


