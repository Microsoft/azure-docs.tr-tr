---
title: Windows Server VM 'ye bağlanma
description: Azure portal ve Kaynak Yöneticisi dağıtım modelini kullanarak bir Windows VM 'ye bağlanmayı ve oturum açmayı öğrenin.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: 43b6d541cfb3d2e5bd583223d9d93921982c65e0
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039451"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Windows çalıştıran bir Azure sanal makinesine bağlanma ve oturum açma
Bir Windows masaüstü bilgisayarından Uzak Masaüstü (RDP) oturumu başlatmak için Azure portalında **Bağlan** düğmesini kullanırsınız. Önce sanal makineye bağlanın ve ardından oturum açın.

Mac 'ten bir Windows VM 'sine bağlanmak için, [Microsoft Uzak Masaüstü](https://aka.ms/rdmac)gibi Mac IÇIN bir RDP istemcisi yüklemeniz gerekir.

## <a name="connect-to-the-virtual-machine"></a>Sanal makineye bağlanma
1. Önceden yapmadıysanız, [Azure portal](https://portal.azure.com/)da oturum açın
2. Sol taraftaki menüden **sanal makineler**' i seçin.
3. Listeden sanal makineyi seçin.
4. Sanal makine sayfasının üst kısmında **Bağlan**' ı seçin.
2. **Sanal makineye bağlan** sayfasında, uygun IP adresini ve bağlantı noktasını seçin. Çoğu durumda, varsayılan IP adresi ve bağlantı noktası kullanılmalıdır. **RDP dosyasını indir**'i seçin. VM 'nin tam zamanında bir ilke kümesi varsa, RDP dosyasını indirebilmeniz için **önce erişim iste düğmesini seçmeniz** gerekir. Tam zamanında ilkesi hakkında daha fazla bilgi için, bkz. [tam zamanında ilkesini kullanarak sanal makine erişimini yönetme](../../security-center/security-center-just-in-time.md).
2. İndirilen RDP dosyasını açın ve istendiğinde **Bağlan** ' ı seçin. 
2. `.rdp` dosyasının bilinmeyen bir yayımcıdan olduğunu belirten bir uyarı alacaksınız. Bu beklenen bir durumdur. **Uzak Masaüstü bağlantısı** penceresinde, devam etmek için **Bağlan** ' ı seçin.
   
    ![Bilinmeyen yayımcıya ilişkin uyarı ekran görüntüsü](./media/connect-logon/rdp-warn.png)
3. **Windows Güvenliği** penceresinde **Diğer seçenekler**'i ve ardından **Başka bir hesap kullanın**'ı seçin. Sanal makinedeki bir hesabın kimlik bilgilerini girin ve ardından **Tamam**' ı seçin.
   
     **Yerel hesap**: Bu, genellikle sanal makineyi oluştururken belirttiğiniz yerel hesap Kullanıcı adı ve parolasıdır. Bu durumda, etki alanı sanal makinenin adıdır ve *vmadı*&#92;*kullanıcıadı* olarak girilir.  
   
    **Etki alanına katılmış VM**: VM bir etki alanına aitse, Kullanıcı adını *etkialanı*&#92;*kullanıcıadı*biçiminde girin. Hesabın ayrıca, Yöneticiler grubunda olması ya da VM’ye uzaktan erişim ayrıcalıkları verilmiş olması gerekir.
   
    **Etki alanı denetleyicisi**: VM bir etki alanı denetleyicisiyse, bu etki alanı için bir etki alanı yönetici hesabının kullanıcı adını ve parolasını girin.
4. Sanal makinenin kimliğini doğrulamak için **Evet** ' i seçin ve oturum açmayı sona erdirin.
   
   ![VM kimliğini doğrulamaya ilişkin mesajı gösteren ekran görüntüsü](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Portaldaki **Bağlan** düğmesi gri Ise ve Azure 'A bir [Express Route](../../expressroute/expressroute-introduction.md) veya [siteden siteye VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) bağlantısı aracılığıyla bağlı değilseniz, RDP KULLANABILMENIZ için VM 'nizi bir genel IP adresi oluşturmanız ve atamanız gerekir. Daha fazla bilgi için bkz. [Azure 'Da genel IP adresleri](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>PowerShell kullanarak sanal makineye bağlanma

 

PowerShell kullanıyorsanız ve Azure PowerShell modülü yüklüyse, aşağıda gösterildiği gibi `Get-AzRemoteDesktopFile` cmdlet 'ini kullanarak da bağlanabilirsiniz.

Bu örnek, yukarıdaki benzer istemler üzerinden sizi gerçekleştirerek RDP bağlantısını hemen başlatacaktır.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

Ayrıca, daha sonra kullanmak üzere RDP dosyasını kaydedebilirsiniz.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>Sonraki adımlar
Bağlanmakta güçlük çekiyorsanız, bkz. [uzak masaüstü bağlantılarında sorun giderme](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

