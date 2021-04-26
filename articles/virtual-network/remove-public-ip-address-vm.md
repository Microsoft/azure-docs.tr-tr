---
title: Bir Azure VM 'den genel IP adresinin ilişkilendirmesini kaldırma
titlesuffix: Azure Virtual Network
description: Bir VM 'den genel IP adresinin ilişkisini kaldırma hakkında bilgi edinin
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: allensu
ms.openlocfilehash: e9bfadd3e2453f0241dc2f7b8bfa5c964333bcf5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776549"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Bir Azure VM 'den genel IP adresinin ilişkilendirmesini kaldırma 

Bu makalede, bir Azure sanal makinesinden (VM) genel IP adresinin ilişkisini kaldırma hakkında bilgi edineceksiniz.

Bir VM 'den ortak IP adresinin ilişkilendirmesini kaldırmak için [Azure Portal](#azure-portal), Azure [komut satırı arabirimini](#azure-cli) (CLI) veya [PowerShell](#powershell) 'i kullanabilirsiniz.

## <a name="azure-portal"></a>Azure portalı

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Genel IP adresinin ilişkisini kaldırmak istediğiniz sanal makineyi bulun veya arayın ve ardından seçin.
3. VM sayfasında **genel bakış**' ı seçin, aşağıdaki resimde gösterildiği gıbı genel IP adresini seçin:

   ![Ortak IP 'yi seçin](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. Genel IP adresi sayfasında **genel bakış**' ı seçin ve ardından aşağıdaki resimde gösterildiği gibi ilişkiyi **kaldırın ' ı seçin:**

    ![Genel IP ilişkisini kaldır](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. **Genel IP adresi** ilişkisini kaldırma bölümünde **Evet**' i seçin.

## <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)'yi veya Azure Cloud Shell kullanın. Azure Cloud Shell doğrudan Azure portalının içinde çalıştırabileceğiniz ücretsiz bir Bash kabuğudur. Azure CLI, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. Aşağıdaki CLı komutlarında **deneyin** düğmesini seçin. **Dene** ' nin seçilmesi, ile Azure hesabınızda oturum açmak için kullanabileceğiniz bir Cloud Shell çağırır.

1. CLı 'yi Bash içinde yerel olarak kullanıyorsanız, ile Azure 'da oturum açın `az login` .
2. Genel IP adresi, bir VM 'ye bağlı bir ağ arabiriminin IP yapılandırmasıyla ilişkilendirilir. Genel IP adresinin bir IP yapılandırmasından ilişkilendirmesini kaldırmak için [az Network Nic-ip-config Update](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update) komutunu kullanın. Aşağıdaki örnek, *Myresourcegroup* adlı kaynak grubunda *MYVM* adlı bir VM 'ye bağlı olan *myvmvmnıc* adlı mevcut bir ağ arabiriminin *ıpconfigmyvm* adlı bir IP yapılandırmasından *myVMPublicIP* adlı genel IP adresini ilişkilendirmesini geri ayırır.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Sanal makinenize bağlı bir ağ arabiriminin adını bilmiyorsanız, görüntülemek için [az VM Nic List](/cli/azure/vm/nic#az_vm_nic_list) komutunu kullanın. Örneğin aşağıdaki komut, *Myresourcegroup* adlı kaynak grubunda *MYVM* adlı bir VM 'ye bağlı ağ arabirimlerinin adlarını listeler:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Çıktı aşağıdaki örneğe benzer bir veya daha fazla satır içerir:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     Önceki örnekte, *Myvmvmnıc* , ağ arabiriminin adıdır.

   - Bir ağ arabirimi için bir IP yapılandırmasının adını bilmiyorsanız, bunları almak için [az Network Nic IP-Config List](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_list) komutunu kullanın. Örneğin, aşağıdaki komut, *Myresourcegroup* adlı bir kaynak grubunda *Myvmvmnıc* adlı bir ağ arabirimi için genel IP yapılandırmalarının adlarını listeler:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Bir ağ arabirimi için genel IP yapılandırmasının adını bilmiyorsanız, [az Network Nic IP-Config Show](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_show) komutunu kullanarak bunları alın. Örneğin, aşağıdaki komut, *Myresourcegroup* adlı bir kaynak grubunda *Myvmvmnıc* adlı bir ağ arabirimi için genel IP yapılandırmalarının adlarını listeler:

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

[PowerShell](/powershell/azure/install-az-ps)'i yükleyip Azure Cloud Shell kullanın. Azure Cloud Shell doğrudan Azure portalının içinde çalıştırabileceğiniz ücretsiz bir kabuktur. PowerShell 'in önceden yüklenmiş ve hesabınızla birlikte kullanılmak üzere yapılandırılmış olması. İzleyen PowerShell komutlarında **deneyin** düğmesini seçin. **Dene** ' nin seçilmesi, ile Azure hesabınızda oturum açmak için kullanabileceğiniz bir Cloud Shell çağırır.

1. PowerShell 'i yerel olarak kullanıyorsanız, ile Azure 'da oturum açın `Connect-AzAccount` .
2. Genel IP adresi, bir VM 'ye bağlı bir ağ arabiriminin IP yapılandırmasıyla ilişkilendirilir. Ağ arabirimi almak için [Get-Aznetworkınterface](/powershell/module/Az.Network/Get-AzNetworkInterface) komutunu kullanın. Genel IP adresi değerini null olarak ayarlayın ve ardından [set-Aznetworkınterface](/powershell/module/Az.Network/Set-AzNetworkInterface) komutunu kullanarak yeni IP yapılandırmasını ağ arabirimine yazın.

   Aşağıdaki örnek, *Myvm* ADLı bir VM 'ye bağlı olan *Myvmvmnıc* adlı bir ağ ARABIRIMINDEN *myVMPublicIP* adlı genel IP adresini ilişkilendirmesini geri ayırır. Tüm kaynaklar *Myresourcegroup* adlı bir kaynak grubunda bulunur.
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Sanal makinenize bağlı bir ağ arabiriminin adını bilmiyorsanız, [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) komutunu kullanarak bunları görüntüleyin. Örneğin aşağıdaki komut, *Myresourcegroup* adlı kaynak grubunda *MYVM* adlı bir VM 'ye bağlı ağ arabirimlerinin adlarını listeler:

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     Çıktı, aşağıdaki örneğe benzer bir veya daha fazla satır içerir. Örnek çıktıda, *Myvmvmnıc* ağ arabiriminin adıdır.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Bir ağ arabirimi için bir IP yapılandırmasının adını bilmiyorsanız, [Get-Aznetworkınterface](/powershell/module/Az.Network/Get-AzNetworkInterface) komutunu kullanarak bunları alın. Örneğin, aşağıdaki komut, *Myresourcegroup* adlı bir kaynak grubunda *Myvmvmnıc* adlı BIR ağ arabirimi için IP yapılandırmalarının adlarını listeler:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     Çıktı, aşağıdaki örneğe benzer bir veya daha fazla satır içerir. Örnek çıktıda, *ıpconfigmyvm* bir IP yapılandırmasının adıdır.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>Sonraki adımlar

- [Genel IP adresini BIR VM ile ilişkilendirmeyi](associate-public-ip-address-vm.md)öğrenin.
