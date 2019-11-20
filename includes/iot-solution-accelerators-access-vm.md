---
title: include dosyası
description: include dosyası
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: a58e408feadd10e6dbc9d6878b82a4d045918ea6
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781504"
---
## <a name="access-the-virtual-machine"></a>Sanal makineye erişin

Aşağıdaki adımlarda Azure Cloud Shell ' de Azure CLı kullanılır. Tercih ederseniz, [Azure CLI](/cli/azure/install-azure-cli) 'yi geliştirme makinenize yükleyebilir ve komutları yerel olarak çalıştırabilirsiniz.

Aşağıdaki adımlarda, Azure sanal makinesinin **SSH** erişimine izin verecek şekilde nasıl yapılandırılacağı gösterilmektedir. Gösterilen adımlarda, Çözüm Hızlandırıcısı için seçtiğiniz adın **contoso-simülasyon** olduğunu varsaymaktadır. bu değeri dağıtımınızın adıyla değiştirin:

1. Çözüm Hızlandırıcısı kaynaklarını içeren kaynak grubunun içeriğini listeleyin:

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Sanal makinenin, genel IP adresinin ve ağ güvenlik grubunun adını, daha sonra bu değerlere ihtiyacınız olduğunu unutmayın.

1. Ağ güvenlik grubunu SSH erişimine izin verecek şekilde güncelleştirin. Aşağıdaki komut, ağ güvenlik grubunun adının **contoso-simülasyon-NSG** olduğunu varsayar; bu değeri ağ güvenlik grubunuzun adıyla değiştirin:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Yalnızca test ve geliştirme sırasında SSH erişimini etkinleştirin. SSH 'yi etkinleştirirseniz, [mümkün olan en kısa sürede yeniden devre dışı bırakmanız gerekir](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices#disable-rdpssh-access-to-virtual-machines).

1. Sanal makinede **azureuser** hesabının parolasını bildiğiniz bir parolayla güncelleştirin. Aşağıdaki komutu çalıştırdığınızda kendi parolanızı seçin:

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Sanal makinenizin genel IP adresini bulun. Aşağıdaki komut, sanal makinenin adının **VM-vikxv** olduğunu varsayar; bu değeri, daha önce bir örneği yaptığınız sanal makinenin adıyla değiştirin:

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Sanal makinenizin genel IP adresini bir yere getirin.
