---
ms.openlocfilehash: dfb887004cd29b5bd9f1d9886b7dfa5f43c83dbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99532058"
---
MP4 dosyaları, VIDEO_OUTPUT_FOLDER_ON_DEVICE anahtarını kullanarak *. env* dosyasında yapılandırdığınız Edge cihazında bir dizine yazılır. Varsayılan değeri kullandıysanız, sonuçlar */var/Media/* klasöründe olmalıdır.

MP4 klibini oynatmak için:

1. Kaynak grubunuza gidin, VM 'yi bulun ve ardından Azure savunma kullanarak bağlanın.

    ![Kaynak grubu](../../../media/quickstarts/resource-group.png)
    
    ![VM](../../../media/quickstarts/virtual-machine.png)
1. [Azure kaynaklarınızı ayarlarken](../../../detect-motion-emit-events-quickstart.md#set-up-azure-resources)oluşturulan kimlik bilgilerini kullanarak oturum açın. 
1. Komut isteminde ilgili dizine gidin. Varsayılan konum */var/Media*' dir. MP4 dosyalarını dizinde görmeniz gerekir.

    ![Çıktı](../../../media/quickstarts/samples-output.png) 

1. Dosyaları yerel makinenize kopyalamak için [Güvenli kopya (SCP)](../../../../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md) kullanın. 
1. [VLC medya oynatıcı](https://www.videolan.org/vlc/) veya başka bir MP4 oynatıcı kullanarak dosyaları yürütün.
