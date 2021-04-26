---
title: Azure DevTest Labs 'de Linux sanal makinelerinize bağlanma
description: Bir laboratuvarda Linux sanal makinenize nasıl bağlanacağınızı öğrenin (Azure DevTest Labs)
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 52fe245f85034a4c6300615ad8fb6040c1168298
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86532186"
---
# <a name="connect-to-a-linux-vm-in-your-lab-azure-devtest-labs"></a>Laboratuvarınızda bir Linux VM 'ye bağlanma (Azure DevTest Labs)
Bu makalede, laboratuvarınızda Linux VM 'ye nasıl bağlanabilmeniz gösterilmektedir. 

## <a name="connect-to-a-linux-vm"></a>Linux VM'ye bağlanma
1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Arama çubuğunda **DevTest Labs**' i arayın ve seçin. 

    :::image type="content" source="./media/connect-linux-virtual-machine/search-select.png" alt-text="DevTest Labs 'i arayın ve seçin":::    
1. Laboratuvarlar listesinden **laboratuvarınızı** seçin.

    :::image type="content" source="./media/connect-linux-virtual-machine/select-lab.png" alt-text="Laboratuvarınızı seçin":::            
1. Laboratuvarınızın giriş sayfasında, **sanal makinelerim** LISTESINDEN Linux VM 'nizi seçin. 

    :::image type="content" source="./media/connect-linux-virtual-machine/select-linux-vm.png" alt-text="Linux VM 'nizi seçin":::        
5. **Genel bakış** sayfasında, sanal makinenin tam etki alanı adını (FQDN) veya IP adresini görebilirsiniz. Bağlantı noktasını aşağıdaki görüntüde gösterildiği gibi da görebilirsiniz.

    :::image type="content" source="./media/connect-linux-virtual-machine/vm-overview.png" alt-text="Sanal makine için tam etki alanı adı":::    

    VM başlatılmış olmasına rağmen **Bağlan** düğmesinin gri olduğuna dikkat edin. Bu, tasarıma göre.
6.  Linux sanal makinenize bağlanmak için SSH kullanın. Aşağıdaki örnek, `mydtl07172452621450000.eastus.cloudapp.azure.com` Kullanıcı adı `vmuser` ve bağlantı noktası ile FQDN ile VM 'ye bağlanır `51637` . SANAL makineye bağlanmak için kullanıcının parolasını girin. 

    ```bash
    ssh vmuser@mydtl07172452621450000.eastus.cloudapp.azure.com -p 51637
    ```

    SANAL makineye bağlanmak için [Putty](https://www.putty.org/) veya başka bir SSH istemcisi gibi araçları kullanabilirsiniz. 

    SSH kullanarak bağlandıktan sonra masaüstü ortamı ([Xfce](https://www.xfce.org)) ve Uzak Masaüstü ([xrdp](http://xrdp.org)) yükleyip yapılandırabilirsiniz.  Ayrıntılı bilgi için bkz. [Azure 'da bir LINUX VM 'sine bağlanmak Için uzak masaüstü 'Nü yükleyip yapılandırma](../virtual-machines/linux/use-remote-desktop.md). 

## <a name="next-steps"></a>Sonraki Adımlar
[Nasıl yapılır: bir Windows VM 'ye bağlanma](connect-windows-virtual-machine.md)
