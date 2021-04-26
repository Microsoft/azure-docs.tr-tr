---
title: Azure Stack Edge Pro GPU cihazında sanal makine konsoluna bağlanma
description: Azure Stack Edge Pro GPU cihazı üzerinde çalışan bir VM 'de sanal makine konsoluna nasıl bağlanabileceğinizi açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/22/2021
ms.author: alkohli
ms.openlocfilehash: 68cf157a512e9b1f6caee4734869c5bb4b836e2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962743"
---
# <a name="connect-to-a-virtual-machine-console-on-an-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU cihazında bir sanal makine konsoluna bağlanma

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro GPU çözümü, kapsayıcısız iş yüklerini sanal makineler aracılığıyla çalıştırır. Bu makalede, cihazınızda dağıtılan bir sanal makinenin konsoluna nasıl bağlanabildiği açıklanır. 

Sanal makine konsolu, genel olarak kullanılabilir uzak masaüstü araçlarını kullanarak, klavye, fare ve ekran özellikleriyle sanal makinelerinize erişmenizi sağlar. Konsola erişip bir sanal makineyi cihazınızda dağıtmada karşılaşılan sorunları giderebilirsiniz. VM 'niz sağlayamasa bile sanal makine konsoluna bağlanabilirsiniz.


## <a name="workflow"></a>İş akışı

Üst düzey iş akışı aşağıdaki adımlara sahiptir:

1. Cihazınızdaki PowerShell arabirimine bağlanın.
1. Konsol erişimini sanal makineye etkinleştirin.
1. Uzak Masaüstü Protokolü (RDP) kullanarak VM 'ye bağlanın.
1. Konsol erişimini VM 'ye iptal edin.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, aşağıdaki önkoşulları tamamladığınızdan emin olun:

#### <a name="for-your-device"></a>Cihazınız için

Etkinleştirilen bir Azure Stack Edge Pro GPU cihazına erişiminizin olması gerekir. Cihazda bir veya daha fazla sanal makine dağıtılmış olmalıdır. Azure PowerShell, şablonlar aracılığıyla veya Azure portal aracılığıyla VM 'Leri dağıtabilirsiniz.

#### <a name="for-client-accessing-the-device"></a>Cihaza erişen istemci için

Bir istemci sistemine erişiminiz olduğundan emin olun:

- , Cihazın PowerShell arabirimine erişebilir. İstemci [desteklenen bir işletim sistemi](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)çalıştırıyor.
- İstemci PowerShell 7,0 veya üstünü çalıştırıyor. PowerShell 'in bu sürümü Windows, Mac ve Linux istemcileri için geçerlidir. Bkz. [PowerShell 7,0](/powershell/scripting/whats-new/what-s-new-in-powershell-70?view=powershell-7.1&preserve-view=true)' i yüklemek için yönergeler.
- Uzak Masaüstü özelliklerine sahiptir. Windows, macOS veya Linux kullanıyor olmanıza bağlı olarak, bu [Uzak Masaüstü istemcilerinden](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)birini yüklemelisiniz. Bu makalede, [Windows Uzak Masaüstü](/windows-server/remote/remote-desktop-services/clients/windowsdesktop#install-the-client) ve [FreeRDP](https://www.freerdp.com/)ile ilgili yönergeler sağlanmaktadır. <!--Which version of FreeRDP to use?-->


## <a name="connect-to-vm-console"></a>VM konsoluna bağlanma

Cihazınızdaki sanal makine konsoluna bağlanmak için aşağıdaki adımları izleyin.

### <a name="connect-to-the-powershell-interface-on-your-device"></a>Cihazınızda PowerShell arabirimine bağlanma

İlk adım, cihazınızın [PowerShell arabirimine bağlandır](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) . 

### <a name="enable-console-access-to-the-vm"></a>Konsol erişimini VM 'ye etkinleştirme

1.  PowerShell arabiriminde, VM konsoluna erişimi etkinleştirmek için aşağıdaki komutu çalıştırın.

    ```powershell
    Grant-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
    ```
2. Örnek çıktıda, sanal makine KIMLIĞINI bir yere getirin. Daha sonraki bir adımda bunu yapmanız gerekir.

    ```powershell
    [10.100.10.10]: PS>Grant-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1
        
    VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
    VirtualMachineHostName : 3V78B03
    ResourceGroupName      : mywindowsvm1rg
    VirtualMachineName     : mywindowsvm1
    Id                     : 81462e0a-decb-4cd4-96e9-057094040063
    [10.100.10.10]: PS>
    ```

### <a name="connect-to-the-vm"></a>VM’ye bağlanma

Artık sanal makine konsoluna bağlanmak için uzak masaüstü istemcisini kullanabilirsiniz.

#### <a name="use-windows-remote-desktop"></a>Windows Uzak Masaüstü 'Nü kullanma

1. Yeni bir metin dosyası oluşturun ve aşağıdaki metni girin.

    ```
    pcb:s:<VM ID from PowerShell>;EnhancedMode=0
    full address:s:<IP address of the device>   
    server port:i:2179
    username:s:EdgeARMUser
    negotiate security layer:i:0
    ```
1. Dosyayı, istemci sisteminize **. rdp* olarak kaydedin. Bu profili VM 'ye bağlanmak için kullanacaksınız.
1. SANAL makineye bağlanmak için profile çift tıklayın. Aşağıdaki kimlik bilgilerini sağlayın:

    - **Kullanıcı adı**: EdgeARMUser olarak oturum açın.
    - **Parola**: cihazınız için yerel Azure Resource Manager parolasını belirtin. Parolayı unuttuysanız [Azure Portal yoluyla Azure Resource Manager parolayı sıfırlayın](azure-stack-edge-gpu-set-azure-resource-manager-password.md#reset-password-via-the-azure-portal). 

#### <a name="use-freerdp"></a>FreeRDP kullanma

Linux istemciniz üzerinde FreeRDP kullanıyorsanız şu komutu çalıştırın: 

```powershell
./wfreerdp /u:EdgeARMUser /vmconnect:<VM ID from PowerShell> /v:<IP address of the device>
```

## <a name="revoke-vm-console-access"></a>VM konsol erişimini iptal et

VM konsoluna erişimi iptal etmek için cihazınızın PowerShell arabirimine dönün. Şu komutu çalıştırın:

```
Revoke-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
```
Örnek bir çıktı aşağıda verilmiştir:

```powershell
[10.100.10.10]: PS>Revoke-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1

VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
VirtualMachineHostName : 3V78B03
ResourceGroupName      : mywindowsvm1rg
VirtualMachineName     : mywindowsvm1
Id                     : 81462e0a-decb-4cd4-96e9-057094040063

[10.100.10.10]: PS>
```
> [!NOTE] 
> VM konsolunu kullanmayı bitirdikten sonra, oturumdan çıkmak için erişimi iptal etmenizi ya da PowerShell penceresini kapatmayı öneririz. 

## <a name="next-steps"></a>Sonraki adımlar

- Azure portal [Azure Stack Edge Pro](azure-stack-edge-gpu-troubleshoot.md) sorunlarını giderin.