---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d41b86b902d9a58b144e251e6922fbd95d459031
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016143"
---
1. Dapl, rdmacm, ibverbs ve mlx4 'ı yükler

   ```bash
   sudo apt-get update

   sudo apt-get install libdapl2 libmlx4-1

   ```

2. /Etc/waagent.exe ' de, aşağıdaki yapılandırma satırlarının yorum yaparak RDMA 'yi etkinleştirin. Bu dosyayı düzenlemek için kök erişiminizin olması gerekir.
  
   ```
   OS.EnableRDMA=y

   OS.UpdateRdmaDriver=y
   ```

3. /Etc/Security/Limits.conf dosyasında KB cinsinden aşağıdaki bellek ayarlarını ekleyin veya değiştirin. Bu dosyayı düzenlemek için kök erişiminizin olması gerekir. Test amacıyla, Memlock ' i sınırsız olarak ayarlayabilirsiniz. Örneğin: `<User or group name>   hard    memlock   unlimited`.

   ```
   <User or group name> hard    memlock <memory required for your application in KB>

   <User or group name> soft    memlock <memory required for your application in KB>
   ```
  
4. Intel MPı kitaplığı 'nı yükler. Kitaplığı [satın alıp indirin](https://software.intel.com/intel-mpi-library/) ya da [ücretsiz değerlendirme sürümünü](https://registrationcenter.intel.com/en/forms/?productid=1740)indirin.

   ```bash
   wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
   ```
 
   Yalnızca Intel MPı 5. x çalışma zamanları desteklenir.
 
   Yükleme adımları için bkz. [Intel MPI kitaplığı yükleme kılavuzu](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).

5. Kök olmayan hata ayıklayıcı olmayan işlemlere yönelik ptrace 'i etkinleştirin (Intel MPı 'nin en son sürümleri için gereklidir).
 
   ```bash
   echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
   ```
