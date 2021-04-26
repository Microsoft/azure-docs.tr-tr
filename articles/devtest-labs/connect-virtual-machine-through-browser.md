---
title: Sanal makinelerinize bir tarayıcıdan bağlanın-Azure | Microsoft Docs
description: Sanal makinelerinize bir tarayıcıdan nasıl bağlanacağınızı öğrenin.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 352fc5fd3ff53a00d9f62966ecf21417ad898706
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87288060"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Sanal makinelerinize bir tarayıcı üzerinden bağlanma 

DevTest Labs, sanal makinelerinize bir tarayıcıdan bağlanmanızı sağlayan [Azure](../bastion/index.yml)savunma ile tümleşir. DevTest Labs 'de bu özelliği etkinleştirme hakkında daha fazla bilgi için bkz. [Laboratuvar sanal makinelerinde tarayıcı bağlantısını etkinleştirme](enable-browser-connection-lab-virtual-machines.md).

*Tarayıcı bağlantısı* etkinleştirildikten sonra, laboratuar kullanıcıları sanal makinelere bir tarayıcıdan erişebilir.  

## <a name="create-a-lab-virtual-machine"></a>Laboratuvar sanal makinesi oluşturma

İlk olarak, üzerinde yapılandırılmış bir sanal ağ içinde laboratuvar sanal makinesini oluşturmanız gerekir. AzureBastionSubnet değil, oluşturduğunuz ikinci **alt ağı** seçin. **Gelişmiş ayarlar** sekmesine giderek, sanal makine oluşturma sırasında bir sanal ağ seçebilirsiniz.

![Sanal makine oluşturma](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Sanal makineyi bir tarayıcıda Başlat

Sanal makine oluşturulduktan sonra *tarayıcı Bağlan* düğmesine tıklayıp makine için Kullanıcı adınızı ve parolanızı girerek bir tarayıcıda başlatabilirsiniz.  

![Tarayıcıda Başlat](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Sonraki Adımlar

[Azure DevTest Labs 'de laboratuvara VM ekleme](devtest-lab-add-vm.md)
