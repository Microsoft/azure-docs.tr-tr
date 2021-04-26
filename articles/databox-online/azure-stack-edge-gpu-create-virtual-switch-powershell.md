---
title: PowerShell aracılığıyla Azure Stack Edge 'de yeni bir sanal anahtar oluşturma
description: PowerShell kullanarak bir Azure Stack Edge cihazında sanal anahtar oluşturmayı açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/06/2021
ms.author: alkohli
ms.openlocfilehash: 1ad86695510a8fe93bbeeab27db53f5afbef92fd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556253"
---
# <a name="create-a-new-virtual-switch-in-azure-stack-edge-pro-gpu-via-powershell"></a>PowerShell aracılığıyla Azure Stack Edge Pro GPU içinde yeni bir sanal anahtar oluşturma

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Bu makalede Azure Stack Edge Pro GPU cihazınızda nasıl yeni bir sanal anahtar oluşturacağınız açıklanır. Örneğin, sanal makinelerinizin farklı bir fiziksel ağ bağlantı noktası üzerinden bağlanmasını istiyorsanız yeni bir sanal anahtar oluşturacaksınız.

## <a name="vm-deployment-workflow"></a>VM dağıtımı iş akışı

1. Cihazınızdaki PowerShell arabirimine bağlanın.
2. Kullanılabilir fiziksel ağ arabirimlerini sorgulayın.
3. Sanal anahtar oluşturun.
4. Otomatik olarak oluşturulan sanal ağı ve alt ağı doğrulayın.

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce aşağıdakilerden emin olun:

- Cihazınızın PowerShell arabirimine erişebilen bir istemci makinesine erişirsiniz. Bkz. [PowerShell arabirimine bağlanma](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 

    İstemci makine [desteklenen bir işletim sistemi](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)çalıştırıyor olmalıdır.

- Cihazınızda [işlem ağını etkinleştirme](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) ' deki yönergelere göre cihazınızdaki fiziksel ağ arabirimlerinden birini etkinleştirmek için yerel kullanıcı arabirimini kullanın. 


## <a name="connect-to-the-powershell-interface"></a>PowerShell arabirimine bağlanın

[Cihazınızın PowerShell arabirimine bağlanın](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

## <a name="query-available-network-interfaces"></a>Kullanılabilir ağ arabirimlerini sorgula

1. Üzerinde yeni bir sanal anahtar oluşturabileceğiniz fiziksel ağ arabirimlerinin listesini göstermek için aşağıdaki komutu kullanın. Bu ağ arabirimlerinden birini seçersiniz.

    ```powershell
    Get-NetAdapter -Physical
    ```
    Örnek bir çıktı aşağıda verilmiştir:
    
    ```powershell
        [10.100.10.10]: PS>Get-NetAdapter -Physical
        
        Name                      InterfaceDescription                    ifIndex Status       MacAddress       LinkSpeed
        ----                      --------------------                    ------- ------       ----------        -----
        Port2                     QLogic 2x1GE+2x25GE QL41234HMCU NIC ...      12 Up           34-80-0D-05-26-EA ...ps
        Ethernet                  Remote NDIS Compatible Device                11 Up           F4-02-70-CD-41-39 ...ps
        Port1                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#3       9 Up           34-80-0D-05-26-EB ...ps
        Port5                     Mellanox ConnectX-4 Lx Ethernet Ad...#2       8 Up           0C-42-A1-C0-E3-99 ...ps
        Port3                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#4       7 Up           34-80-0D-05-26-E9 ...ps
        Port6                     Mellanox ConnectX-4 Lx Ethernet Adapter       6 Up           0C-42-A1-C0-E3-98 ...ps
        Port4                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#2       4 Up           34-80-0D-05-26-E8 ...ps
        
        [10.100.10.10]: PS>
    ```
2. Şu şekilde bir ağ arabirimi seçin:

    - **Çalışır** durumda. 
    - Var olan herhangi bir sanal anahtar tarafından kullanılmıyor. Şu anda her ağ arabirimi için yalnızca bir vSwitch yapılandırılabilir. 
    
    Var olan sanal anahtar ve ağ arabirimi ilişkilendirmesini denetlemek için `Get-HcsExternalVirtualSwitch` komutunu çalıştırın.
 
    Örnek bir çıktı aşağıda verilmiştir.

    ```powershell
    [10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

    Name                          : vSwitch1
    InterfaceAlias                : {Port2}
    EnableIov                     : True
    MacAddressPools               :
    IPAddressPools                : {}
    ConfigurationSource           : Dsc
    EnabledForCompute             : True
    SupportsAcceleratedNetworking : False
    DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
    Type                          : External
    
    [10.100.10.10]: PS>
    ```
    Bu örnekte, bağlantı noktası 2 var olan bir sanal anahtarla ilişkilendirilir ve kullanılmamalıdır.

## <a name="create-a-virtual-switch"></a>Sanal anahtar oluştur

Belirtilen ağ arabiriminizdeki yeni bir sanal anahtar oluşturmak için aşağıdaki cmdlet 'i kullanın. Bu işlem tamamlandıktan sonra, işlem örneklerinde yeni sanal ağ kullanılabilir.

```powershell
Add-HcsExternalVirtualSwitch -InterfaceAlias <Network interface name> -WaitForSwitchCreation $true
```

`Get-HcsExternalVirtualSwitch`Yeni oluşturulan anahtarı tanımlamak için komutunu kullanın. Oluşturulan yeni anahtar olarak adlandırılır `vswitch-<InterfaceAlias>` . 

Örnek bir çıktı aşağıda verilmiştir:

```powershell
[10.100.10.10]: P> Add-HcsExternalVirtualSwitch -InterfaceAlias Port5 -WaitForSwitchCreation $true
[10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

Name                          : vSwitch1
InterfaceAlias                : {Port2}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                : {}
ConfigurationSource           : Dsc
EnabledForCompute             : True
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
Type                          : External

Name                          : vswitch-Port5
InterfaceAlias                : {Port5}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                :
ConfigurationSource           : Dsc
EnabledForCompute             : False
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : 9b301c40-3daa-49bf-a20b-9f7889820129
Type                          : External

[10.100.10.10]: PS>
```

## <a name="verify-network-subnet"></a>Ağı, alt ağı doğrula 

Yeni sanal anahtarı oluşturduktan sonra, Azure Stack Edge Pro GPU, otomatik olarak buna karşılık gelen bir sanal ağ ve alt ağ oluşturur. VM 'Ler oluştururken bu sanal ağı kullanabilirsiniz.

<!--To identify the virtual network and subnet associated with the new switch that you created, use the `Get-HcsVirtualNetwork` command. This cmdlet will be released in April some time. -->

## <a name="next-steps"></a>Sonraki adımlar

- [Azure PowerShell aracılığıyla Azure Stack Edge Pro GPU cihazınızda VM 'Leri dağıtma](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)

- [Azure Stack Edge Pro GPU cihazınızda VM 'Leri Azure portal aracılığıyla dağıtın](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
