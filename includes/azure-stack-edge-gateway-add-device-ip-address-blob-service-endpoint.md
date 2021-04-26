---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 11/21/2019
ms.author: alkohli
ms.openlocfilehash: 0c6845f081ccbe42e70964eaa939d58597e3b69b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185552"
---
1. Cihazınızın yerel Web Kullanıcı arabirimine gidin ve cihazınızda oturum açın. Cihazın kilidinin açık olduğundan emin olun.

2. **Ağ ayarları** sayfasına gidin. İstemciye bağlanmak için kullanılan ağ arabiriminin cihaz IP adresini bir yere unutmayın.

3. Uzak bir Windows istemcisiyle çalışıyorsanız, bir yönetici olarak **Not defteri** ' ni başlatın ve ardından konumunda bulunan Hosts dosyasını açın `C:\Windows\System32\Drivers\etc` .

4. Hosts dosyanıza aşağıdaki girişi ekleyin: `<Device IP address> <Blob service endpoint>`

    Azure portal oluşturulan Edge depolama hesabından blob hizmeti uç noktası aldınız. Yalnızca blob hizmeti uç noktasının sonekini kullanacaksınız.

    Başvuru için aşağıdaki görüntüyü kullanın. `hosts` dosyasını kaydedin.

    ![Windows istemcisinde ana bilgisayar dosyasını Değiştir](media/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint/hosts-file-1.png)