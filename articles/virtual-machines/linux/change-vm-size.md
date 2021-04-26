---
title: Azure CLı ile Linux VM 'yi yeniden boyutlandırma
description: VM boyutunu değiştirerek bir Linux sanal makinesini büyütme veya azaltma.
author: DavidCBerry13
ms.service: virtual-machines
ms.topic: how-to
ms.date: 02/10/2017
ms.author: daberry
ms.collection: linux
ms.openlocfilehash: 290c42cadd840e5a292201247b1555059b5b4381
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556647"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Azure CLI kullanarak Linux Sanal Makinesini yeniden boyutlandırma 

Bir sanal makine (VM) sağlamadıktan sonra [VM boyutunu][vm-sizes]değiştirerek VM 'yi yukarı veya aşağı ölçeklendirdirebilirsiniz. Bazı durumlarda, önce VM 'yi serbest bırakın. İstenen boyut, VM 'yi barındıran donanım kümesinde yoksa VM 'yi serbest getirmeniz gerekir. Bu makalede, Azure CLı ile Linux VM 'nin nasıl yeniden boyutlandırılması ayrıntılı olarak açıklanır. 

## <a name="resize-a-vm"></a>VM’yi yeniden boyutlandırma
Bir VM 'yi yeniden boyutlandırmak için, [az Login](/cli/azure/reference-index)kullanarak en son [Azure CLI](/cli/azure/install-az-cli2) 'Nın yüklü ve bir Azure hesabında oturum açmış olmanız gerekir.

1. VM 'nin [az VM List-VM-Resize-Options](/cli/azure/vm)ile barındırıldığı donanım KÜMESINDEKI kullanılabilir VM boyutlarının listesini görüntüleyin. Aşağıdaki örnek, kaynak grubu bölgesinde adlı sanal makinenin VM boyutlarını listeler `myVM` `myResourceGroup` :
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. İstenen VM boyutu listeleniyorsa, [az VM Resize](/cli/azure/vm)ile VM 'yi yeniden boyutlandırın. Aşağıdaki örnek, adlı sanal makineyi boyutuna göre yeniden boyutlandırır `myVM` `Standard_DS3_v2` :
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    Bu işlem sırasında VM yeniden başlatılır. Yeniden başlatıldıktan sonra, var olan işletim sistemi ve veri diskleriniz yeniden eşlenir. Geçici disk üzerinde herhangi bir şey kaybedilir.

3. İstenen VM boyutu listelenmiyorsa, öncelikle VM 'yi [serbest bırakma](/cli/azure/vm)' yı kullanarak VM 'yi serbest bırakın. Bu işlem, sanal makinenin daha sonra bölge tarafından desteklenen ve başlatılan kullanılabilir boyuta yeniden boyutlandırılabilmesini sağlar. Aşağıdaki adımlar, adlı kaynak grubunda adlı VM 'yi serbest bırakın, yeniden boyutlandırın ve sonra başlatın `myVM` `myResourceGroup` :
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > VM 'nin serbest çıkarılması VM 'ye atanan dinamik IP adreslerini de serbest bırakır. İşletim sistemi ve veri diskleri etkilenmez.

## <a name="next-steps"></a>Sonraki adımlar
Ek ölçeklenebilirlik için birden fazla sanal makine örneği çalıştırın ve ölçeği ölçeklendirin. Daha fazla bilgi için bkz. [sanal makine ölçek kümesindeki Linux makinelerini otomatik olarak ölçeklendirme][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/tutorial-autoscale-cli.md 
[vm-sizes]:sizes.md
