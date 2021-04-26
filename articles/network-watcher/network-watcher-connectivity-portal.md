---
title: Bağlantı sorunlarını giderme-Azure portal
titleSuffix: Azure Network Watcher
description: Azure ağ Izleyicisi 'nin Azure portal kullanarak bağlantı sorunlarını giderme özelliğini kullanmayı öğrenin.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: f33c5f0fdf69737df0d8bd83499ded1e0e0f4f88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97898122"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Azure portal kullanarak Azure ağ Izleyicisi ile bağlantı sorunlarını giderme

> [!div class="op_single_selector"]
> - [Portal](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Azure CLI](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

Bir sanal makineden belirli bir uç noktaya doğrudan TCP bağlantısının kurulabildiğini doğrulamak için bağlantı sorunlarını giderme hakkında bilgi edinin.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede aşağıdaki kaynaklara sahip olduğunuz varsayılır:

* Bir bağlantı sorunlarını gidermek istediğiniz bölgedeki ağ Izleyicisi örneği.
* İle bağlantı sorunlarını gidermek için sanal makineler.

> [!IMPORTANT]
> Bağlantı sorunlarını gidermek için, üzerinde çalıştığınız VM 'nin `AzureNetworkWatcherExtension` VM uzantısının yüklü olması gerekir. Windows VM 'ye uzantı yüklemek için bkz. [Windows Için Azure ağ Izleyicisi Aracısı sanal makine uzantısı](../virtual-machines/extensions/network-watcher-windows.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) ve Linux VM Için [Azure Ağ İzleyicisi Aracısı sanal makine uzantısı](../virtual-machines/extensions/network-watcher-linux.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)' nı ziyaret edin. Uzantı hedef uç noktada gerekli değil.

## <a name="check-connectivity-to-a-virtual-machine"></a>Bir sanal makineye bağlantıyı denetle

Bu örnek, 80 bağlantı noktası üzerinden bir hedef sanal makineye bağlantıyı denetler.

Ağ izleyicisine gidin ve **bağlantı sorunlarını giderme**' ye tıklayın. Bağlantıyı denetlemek için sanal makineyi seçin. **Hedef** bölümünde, **bir sanal makine seç** ' i seçin ve doğru sanal makineyi ve test edilecek bağlantı noktasını seçin.

**Denetle**' ye tıkladığınızda, belirtilen bağlantı noktasındaki sanal makineler arasındaki bağlantı denetlenir. Örnekte, hedef VM ulaşılamaz durumdaysa, atlamalarla bir liste gösterilir.

![Bir sanal makine için bağlantı sonuçlarını denetleme][1]

## <a name="check-remote-endpoint-connectivity"></a>Uzak uç nokta bağlantısını denetleme

Uzak bir uç nokta bağlantısını ve gecikmesini denetlemek için **hedef** bölümünde **el ile radyo belirt** düğmesini seçin, URL 'yi ve bağlantı noktasını girin ve **Denetle**' ye tıklayın.  Bu, Web siteleri ve depolama uç noktaları gibi uzak uç noktalar için kullanılır.

![Web sitesi için bağlantı sonuçlarını denetleme][2]

## <a name="next-steps"></a>Sonraki adımlar

[Bir uyarı tetikledi bildiren paket yakalamayı](network-watcher-alert-triggered-packet-capture.md) görüntüleyerek sanal makine uyarıları ile paket yakalamaları otomatikleştirmeyi öğrenin

IP 'de belirli trafiğe izin verilip verilmeyeceğini [denetle onay IP Flow doğrula](diagnose-vm-network-traffic-filtering-problem.md) ' yı ziyaret ederek bulun

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png