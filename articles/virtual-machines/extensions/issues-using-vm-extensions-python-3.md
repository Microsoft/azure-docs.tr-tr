---
title: Python 3 etkin Linux Azure sanal makineleri sistemlerinde VM uzantıları kullanma sorunları
description: Python 3 özellikli Linux sistemlerinde VM uzantılarını kullanma hakkında bilgi edinin
services: virtual-machines
documentationcenter: ''
author: v-miegge
ms.author: jparrel
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/25/2020
ms.assetid: 3cd520fd-eaf7-4ef9-b4d3-4827057e5028
ms.openlocfilehash: d38b41bc47c01b01fcbb1b05238396dca6d8eb4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558653"
---
# <a name="issues-using-vm-extensions-in-python-3-enabled-linux-azure-virtual-machines-systems"></a>Python 3 etkin Linux Azure sanal makineleri sistemlerinde VM uzantıları kullanma sorunları

> [!NOTE]
> İş yükünüz **Python 2. x** desteği gerektirmediği takdirde, Microsoft kullanıcıların sistemlerinde **Python 3. x** 'i benimsemesini öneriyor. Bu gereksinime örnek olarak eski yönetim betikleri veya **Azure disk şifrelemesi** ve **Azure izleyici** gibi uzantılar bulunabilir.
>
> **Python 2. x** ' i yüklemeden önce, Python 2. x ' in uzun süreli destek sorusunu, özellikle de güvenlik güncelleştirmelerini alma yeteneğini göz önünde bulundurun. Ürün olarak, bahsedilen uzantının bazıları dahil, **python 3,8** desteğiyle güncelleştirme yaparken Python 2. x kullanımını sona erdirmelisiniz.

Bazı Linux dağıtımları Python 3,8 ' ye geçti ve `/usr/bin/python` Python için eski giriş noktasını tamamen kaldırdı. Bu geçiş, belirli sanal makine (VM) uzantılarının bu iki koşuldan birini kullanıma hazır, otomatik olarak dağıtımını etkiler:

- Hala Python 3. x desteğine geçiş yapan uzantılar
- Eski giriş noktasını kullanan uzantılar `/usr/bin/python`

**Python 3. x** ' e geçiş yapan Linux dağıtım kullanıcıları, `/usr/bin/python` Bu uzantıları VM 'lerine dağıtmayı denemeden önce eski giriş noktasının mevcut olduğundan emin olmalıdır. Aksi takdirde, uzantı dağıtımı başarısız olabilir. 

- Etkilenen Linux dağıtımları **Ubuntu Server 20,04 LTS** ve **Ubuntu Pro 20,04 LTS**'yi içerir.

- Etkilenen VM uzantıları arasında **Azure disk şifrelemesi**, **Log Analytics**, **VM erişimi** (parola sıfırlama için kullanılır) ve **Konuk tanılama** (ek performans sayaçları için kullanılır) bulunur.

**Ubuntu 18,04 LTS** 'Den **ubuntu 20,04 LTS**'e yükseltme gibi yerinde yükseltmeler, symlink 'i tutmalı `/usr/bin/python` ve etkilenmeden kalır.

## <a name="resolution"></a>Çözüm

Daha önce özette açıklanan bilinen etkilenen senaryolara uzantılar dağıtılmadan önce bu genel önerileri göz önünde bulundurun:

1. Uzantıyı dağıtılmadan önce, `/usr/bin/python` Linux dağıtım satıcısı tarafından sunulan yöntemi kullanarak oluşturmaksızın 'i yeniden devreye sokun.

   - Örneğin, **Python 2,7** için şunu kullanın: `sudo apt update && sudo apt install python-is-python2`

1. Bu öneri Azure müşterilerine yöneliktir ve Azure Stack desteklenmez:

   - Bu sorunu gösteren bir örnek zaten dağıttıysanız, yukarıda bahsedilen komutları çalıştırmak için VM dikey penceresinde Çalıştır komut işlevini kullanın. Run komutu uzantısının kendisi Python 3,8 ' e geçiş işleminden etkilenmez.

1. Yeni bir örnek dağıtıyorsanız ve sağlama zamanında bir uzantı ayarlamanız gerekiyorsa, yukarıda bahsedilen paketleri yüklemek için **Cloud-init** Kullanıcı verilerini kullanın.

   Örneğin, Python 2,7 için:

   ```python
   # create cloud-init config
   cat > cloudinitConfig.json <<EOF
   #cloud-config
   package_update: true
    
   runcmd:
   - sudo apt update
   - sudo apt install python-is-python2 
   EOF

   # create VM
   az vm create \
       --resource-group <resourceGroupName> \
       --name <vmName> \
       --image <Ubuntu 20.04 Image URN> \
       --admin-username azadmin \
       --ssh-key-value "<sshPubKey>" \
       --custom-data ./cloudinitConfig.json
   ```

1. Kuruluşunuzun ilke yöneticileri, uzantıların sanal makinelere dağıtılması gerektiğini tespit ederseniz, sağlama sırasında uzantı desteğini devre dışı bırakabilirsiniz:

   - REST API

     Bu özellikle bir VM dağıtabilmeniz durumunda uzantıları devre dışı bırakmak ve etkinleştirmek için:

     ```python
       "osProfile": {
         "allowExtensionOperations": false
       },
     ```

## <a name="next-steps"></a>Sonraki adımlar

Lütfen daha fazla bilgi için bkz. [18,04 LTS-Python 3 ' ün varsayılan olarak diğer temel sistem değişiklikleri](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes#Python3_by_default) .
