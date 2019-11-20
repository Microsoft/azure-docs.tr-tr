---
title: Azure 'da bir Linux sanal makinesini yönetilmeyen disklerden yönetilen disklere dönüştürme-Azure yönetilen diskler
description: Kaynak Yöneticisi dağıtım modelinde Azure CLı kullanarak, bir Linux sanal makinesini yönetilmeyen disklerden yönetilen disklere dönüştürme
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 5464dd2ab2ab7c783945cc068a1347d7ef9ad3ab
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036603"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Bir Linux sanal makinesini yönetilmeyen disklerden yönetilen disklere dönüştürme

Yönetilmeyen diskler kullanan mevcut Linux sanal makineleriniz (VM) varsa, VM 'Leri [Azure yönetilen diskleri](../linux/managed-disks-overview.md)kullanacak şekilde dönüştürebilirsiniz. Bu işlem hem işletim sistemi diskini hem de bağlı veri disklerini dönüştürür.

Bu makalede, Azure CLı kullanarak VM 'Leri nasıl dönüştürebileceğiniz gösterilmektedir. Yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [Azure CLI 'Yı yüklemek](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Başlamadan önce
* [Yönetilen disklere geçiş hakkında SSS 'yi](faq-for-disks.md#migrate-to-managed-disks)gözden geçirin.

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* Özgün VHD’ler ve dönüştürme öncesinde VM tarafından kullanılan depolama hesabı silinmez. Ücretler uygulanmaya devam eder. Bunlar için ücret alınmasını önlemek istiyorsanız, dönüştürmenin tamamlandığını doğruladıktan sonra özgün VHD bloblarını silin. Bunları silmek için eklenmemiş olan diskleri bulmanız gerekiyorsa, [eklenmemiş Azure yönetilen ve yönetilmeyen disklerini bulma ve silme](find-unattached-disks.md)makalemize bakın.

## <a name="convert-single-instance-vms"></a>Tek örnekli VM 'Leri dönüştürme
Bu bölümde, tek örnekli Azure VM 'lerinin yönetilmeyen disklerden yönetilen disklere nasıl dönüştürüleceği ele alınmaktadır. (Sanal makinelerleriniz bir kullanılabilirlik kümesinde ise, sonraki bölüme bakın.) Bu işlemi, Premium (SSD) yönetilmeyen disklerden Premium yönetilen disklere veya standart (HDD) yönetilmeyen disklerden Standart yönetilen disklere sanal makineler dönüştürmek için kullanabilirsiniz.

1. [Az VM serbest bırakma](/cli/azure/vm)kullanarak VM 'yi serbest bırakın. Aşağıdaki örnek, `myResourceGroup`adlı kaynak grubunda `myVM` adlı VM 'yi kaldırır:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. [Az VM Convert](/cli/azure/vm)kullanarak VM 'yi yönetilen disklere dönüştürün. Aşağıdaki işlem, işletim sistemi diski ve veri diskleri dahil olmak üzere `myVM`adlı sanal makineyi dönüştürür:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. [Az VM start](/cli/azure/vm)kullanarak yönetilen disklere dönüştürmeden sonra VM 'yi başlatın. Aşağıdaki örnek, `myResourceGroup`adlı kaynak grubunda `myVM` adlı sanal makineyi başlatır.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Kullanılabilirlik kümesindeki VM 'Leri dönüştürme

Yönetilen disklere dönüştürmek istediğiniz VM 'Ler bir kullanılabilirlik kümesinde ise, önce kullanılabilirlik kümesini yönetilen bir kullanılabilirlik kümesine dönüştürmeniz gerekir.

Kullanılabilirlik kümesini dönüştürmeden önce kullanılabilirlik kümesindeki tüm VM 'Lerin serbest bırakılmalıdır. Kullanılabilirlik kümesinin kendisi yönetilen bir kullanılabilirlik kümesine dönüştürüldükten sonra tüm VM 'Leri yönetilen disklere dönüştürmeyi planlayın. Ardından, tüm VM 'Leri başlatın ve normal şekilde çalışmaya devam edin.

1. Bir kullanılabilirlik kümesindeki tüm VM 'Leri [az VM AVAILABILITY-Set List](/cli/azure/vm/availability-set)kullanarak listeleyin. Aşağıdaki örnek, `myResourceGroup`adlı kaynak grubunda `myAvailabilitySet` adlı kullanılabilirlik kümesindeki tüm VM 'Leri listeler:

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. [Az VM serbest bırakma](/cli/azure/vm)kullanarak tüm VM 'leri serbest bırakın. Aşağıdaki örnek, `myResourceGroup`adlı kaynak grubunda `myVM` adlı VM 'yi kaldırır:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. [Az VM kullanılabilirliği-set Convert](/cli/azure/vm/availability-set)kullanarak kullanılabilirlik kümesini dönüştürün. Aşağıdaki örnek, `myResourceGroup`adlı kaynak grubundaki `myAvailabilitySet` adlı kullanılabilirlik kümesini dönüştürür:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. [Az VM Convert](/cli/azure/vm)kullanarak tüm VM 'leri yönetilen disklere dönüştürün. Aşağıdaki işlem, işletim sistemi diski ve veri diskleri dahil olmak üzere `myVM`adlı sanal makineyi dönüştürür:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. [Az VM start](/cli/azure/vm)kullanarak yönetilen disklere dönüştürmeden sonra tüm VM 'leri başlatın. Aşağıdaki örnek, `myResourceGroup`adlı kaynak grubunda `myVM` adlı sanal makineyi başlatır:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-using-the-azure-portal"></a>Azure portal kullanarak Dönüştür

Azure portal kullanarak, yönetilmeyen diskleri yönetilen disklere de dönüştürebilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Portaldaki VM 'Ler listesinden VM 'yi seçin.
3. VM 'nin dikey penceresinde, menüden **diskler** ' i seçin.
4. **Diskler** dikey penceresinin üst kısmında, **yönetilen disklere geçir**' i seçin.
5. VM 'niz bir kullanılabilirlik kümesi içinde ise, önce kullanılabilirlik kümesini dönüştürmeniz gereken **yönetilen disklere geçiş** dikey penceresinde bir uyarı olacaktır. Uyarı, kullanılabilirlik kümesini dönüştürmek için tıklabileceğiniz bir bağlantıya sahip olmalıdır. Kullanılabilirlik kümesi dönüştürüldükten veya VM 'niz bir kullanılabilirlik kümesinde değilse, disklerinizi yönetilen disklere geçirme işlemini başlatmak için **Taşı** ' ya tıklayın.

Geçiş tamamlandıktan sonra VM durdurulur ve yeniden başlatılır.

## <a name="next-steps"></a>Sonraki adımlar

Depolama seçenekleri hakkında daha fazla bilgi için bkz. [Azure yönetilen disklere genel bakış](../windows/managed-disks-overview.md).
