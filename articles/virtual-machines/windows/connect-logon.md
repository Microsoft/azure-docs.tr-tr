---
title: Windows Server VM 'ye bağlanma
description: Azure portal ve Kaynak Yöneticisi dağıtım modelini kullanarak bir Windows VM 'ye bağlanmayı ve oturum açmayı öğrenin.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/26/2018
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 007719469eb19809d9e64bccfef9589e1fe491f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104607322"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Windows çalıştıran bir Azure sanal makinesine bağlanma ve oturum açma
Bir Windows masaüstü bilgisayarından Uzak Masaüstü (RDP) oturumu başlatmak için Azure portalında **Bağlan** düğmesini kullanırsınız. Önce sanal makineye bağlanın ve ardından oturum açın.

Mac 'ten bir Windows VM 'sine bağlanmak için, [Microsoft Uzak Masaüstü](https://aka.ms/rdmac)gibi Mac IÇIN bir RDP istemcisi yüklemeniz gerekir.

## <a name="connect-to-the-virtual-machine"></a>Sanal makineye bağlanma
1. Bir VM 'ye bağlanmak için [Azure Portal](https://portal.azure.com/) gidin. **Sanal makineleri** arayın ve seçin.
2. Listeden sanal makineyi seçin.
3. Sanal makine sayfasının başlangıcında **Bağlan**' ı seçin.
4. **Sanal makineye bağlan** sayfasında, **RDP**' yi seçin ve ardından uygun **IP adresini** ve **bağlantı noktası numarasını** seçin. Çoğu durumda, varsayılan IP adresi ve bağlantı noktası kullanılmalıdır. **RDP Dosyasını İndir**’i seçin. VM 'nin tam zamanında bir ilke kümesi varsa, RDP dosyasını indirebilmeniz için **önce erişim iste düğmesini seçmeniz** gerekir. Tam zamanında ilkesi hakkında daha fazla bilgi için, bkz. [tam zamanında ilkesini kullanarak sanal makine erişimini yönetme](../../security-center/security-center-just-in-time.md).
5. İndirilen RDP dosyasını açın ve istendiğinde **Bağlan** ' ı seçin. Dosyanın bilinmeyen bir yayımcıdan olduğunu belirten bir uyarı alacaksınız `.rdp` . Bu beklenen bir durumdur. **Uzak Masaüstü bağlantısı** penceresinde, devam etmek için **Bağlan** ' ı seçin.
   
    ![Bilinmeyen yayımcıya ilişkin uyarı ekran görüntüsü](./media/connect-logon/rdp-warn.png)
3. **Windows Güvenliği** penceresinde **Diğer seçenekler**'i ve ardından **Başka bir hesap kullanın**'ı seçin. Sanal makinedeki bir hesabın kimlik bilgilerini girin ve ardından **Tamam**' ı seçin.
   
     **Yerel hesap**: Bu, genellikle sanal makineyi oluştururken belirttiğiniz yerel hesap Kullanıcı adı ve parolasıdır. Bu durumda, etki alanı sanal makinenin adıdır ve *vmadı*&#92;*kullanıcıadı* olarak girilir.  
   
    **Etki alanına KATıLMıŞ VM**: VM bir etki alanına aitse, Kullanıcı adını *etkialanı* *&#92;Kullanıcı adı biçiminde* girin. Hesabın ayrıca, Yöneticiler grubunda olması ya da VM’ye uzaktan erişim ayrıcalıkları verilmiş olması gerekir.
   
    **Etki alanı denetleyicisi**: VM bir etki alanı denetleyicisiyse, bu etki alanı için bir etki alanı yönetici hesabının kullanıcı adını ve parolasını girin.
4. Sanal makinenin kimliğini doğrulamak için **Evet** ' i seçin ve oturum açmayı sona erdirin.
   
   ![VM kimliğini doğrulamaya ilişkin iletiyi gösteren ekran görüntüsü](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Portaldaki **Bağlan** düğmesi gri Ise ve Azure 'A bir [Express Route](../../expressroute/expressroute-introduction.md) veya [siteden siteye VPN](../../vpn-gateway/tutorial-site-to-site-portal.md) bağlantısı aracılığıyla bağlı değilseniz, RDP KULLANABILMENIZ için VM 'nizi bir genel IP adresi oluşturmanız ve atamanız gerekir. Daha fazla bilgi için bkz. [Azure 'Da genel IP adresleri](../../virtual-network/public-ip-addresses.md).
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>PowerShell kullanarak sanal makineye bağlanma

 

PowerShell kullanıyorsanız ve Azure PowerShell modülü yüklüyse `Get-AzRemoteDesktopFile` , aşağıda gösterildiği gibi cmdlet 'ini kullanarak da bağlanabilirsiniz.

Bu örnek, yukarıdaki benzer istemler üzerinden sizi gerçekleştirerek RDP bağlantısını hemen başlatacaktır.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

Ayrıca, daha sonra kullanmak üzere RDP dosyasını kaydedebilirsiniz.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>Sonraki adımlar
Bağlanmakta güçlük çekiyorsanız, bkz. [uzak masaüstü bağlantılarında sorun giderme](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).