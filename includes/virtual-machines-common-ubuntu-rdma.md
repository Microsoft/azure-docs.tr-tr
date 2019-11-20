---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d41b86b902d9a58b144e251e6922fbd95d459031
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671187"
---
1. Dapl, rdmacm ibverbs ve mlx4 yükleyin

   ```bash
   sudo apt-get update

   sudo apt-get install libdapl2 libmlx4-1

   ```

2. /Etc/waagent.conf içinde aşağıdaki yapılandırma satırları uncommenting tarafından rdma'yı etkinleştirin. Bu dosyayı düzenlemek için kök erişmeniz gerekir.
  
   ```
   OS.EnableRDMA=y

   OS.UpdateRdmaDriver=y
   ```

3. Ekleyebilir veya aşağıdaki /etc/security/limits.conf dosyasında KB bellek ayarlarını değiştirin. Bu dosyayı düzenlemek için kök erişmeniz gerekir. Test amacıyla memlock sınırsız olarak ayarlayabilirsiniz. Örneğin: `<User or group name>   hard    memlock   unlimited`

   ```
   <User or group name> hard    memlock <memory required for your application in KB>

   <User or group name> soft    memlock <memory required for your application in KB>
   ```
  
4. Intel MPI Kitaplığı'nı yükleyin. Her iki [satın alın ve indirme](https://software.intel.com/intel-mpi-library/) Intel ya da indirme kitaplıktan [ücretsiz deneme sürümü](https://registrationcenter.intel.com/en/forms/?productid=1740).

   ```bash
   wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
   ```
 
   Yalnızca Intel MPI 5.x çalışma zamanları desteklenir.
 
   Yükleme adımları için bkz: [Intel MPI kitaplık Yükleme Kılavuzu](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).

5. Ptrace (Intel MPI en son sürümleri için gereklidir) kök olmayan hata ayıklayıcı olmayan işlemler için etkinleştirin.
 
   ```bash
   echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
   ```
