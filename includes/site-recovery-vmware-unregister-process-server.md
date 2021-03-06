---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: bff9457220efbb6a6b318550a5b2dd9af4ae230f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019215"
---
Belirli koşullara ilişkin adımları izleyin.

### <a name="unregister-a-connected-process-server"></a>Bağlı bir işlem sunucusunun kaydını sil

1. İşlem sunucusuyla yönetici olarak uzak bağlantı kurun.
2. **Denetim Masası**'nda **Programlar > program Kaldır**' ı açın.
3. **Site Recovery Mobility hizmeti/ana hedef sunucusu Microsoft Azure** program kaldırın.
4. **Yapılandırma/Işlem sunucusu Site Recovery Microsoft Azure** programı kaldırın.
5. 3 ve 4. adımlarda bulunan programlar kaldırıldıktan sonra, **Microsoft Azure Site Recovery yapılandırma/Işlem sunucusu bağımlılıklarını** kaldırın.

### <a name="unregister-a-disconnected-process-server"></a>Bağlantısı kesilen bir işlem sunucusunun kaydını silme

Bu adımları yalnızca, işlem sunucusunun yüklü olduğu makinenin bir yolu yoksa kullanın.

1. Yapılandırma sunucusunda yönetici olarak oturum açın.
2. Bir yönetim komut istemi açın ve konumuna gidin `%ProgramData%\ASR\home\svsystems\bin` .
3. Bir veya daha fazla işlem sunucusunun listesini almak için bu komutu çalıştırın.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. Hayır: işlem sunucusu seri numarası.
    - IP/ad: işlem sunucusunu çalıştıran makinenin IP adresi ve adı.
    - Sinyal: işlem sunucusu makinesinden son sinyal.
    ! [Ekran görüntüsü, işlem sunucularınız ve metin ile ilgili bilgilerin düz metin görüntüsünü gösterir, lütfen kayıt kaldırmayı kaldırmak için yukarıdaki sunuculardan birini seçin. (medya/site-kurtarma-VMware-Unregister-işlem-sunucu/Unregister-cmd.PNG)

4. Kaldırmak istediğiniz işlem sunucusunun seri numarasını belirtin.
5. Bir işlem sunucusunun kaydını silme tüm ayrıntılarını sistemden kaldırın ve şu iletiyi görüntüler: **sunucu adı> başarıyla silindi (sunucu-IP-adresi)**

