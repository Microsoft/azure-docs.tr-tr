---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: db4e27c0972a93d870d0a6565f1bd3212146df62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582737"
---
DHCP olmayan bir ortamda önyükleme yaparsanız, Data Box Gateway sanal makineyi dağıtmak için aşağıdaki adımları izleyin.

1. [Cihazın Windows PowerShell arabirimine bağlanın](#connect-to-the-powershell-interface).
2. `Get-HcsIpAddress`Sanal cihazınızda etkin olan ağ arabirimlerini listelemek için cmdlet 'ini kullanın. Cihazınızda tek bir ağ arabirimi varsa `Ethernet` varsayılan adı atanır.

    Aşağıdaki örnek, bu cmdlet 'in kullanımını gösterir:

    ```
    [10.100.10.10]: PS>Get-HcsIpAddress

    OperationalStatus : Up
    Name              : Ethernet
    UseDhcp           : True
    IpAddress         : 10.100.10.10
    Gateway           : 10.100.10.1
    ```

3. Ağı yapılandırmak için `Set-HcsIpAddress` cmdlet'ini kullanın. Aşağıdaki örneğe bakın:

    ```
    Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1
    ```

