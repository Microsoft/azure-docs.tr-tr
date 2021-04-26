---
title: Azure Lab Services bir Linux VM 'ye bağlanma | Microsoft Docs
description: Azure Lab Services 'deki bir laboratuvarda Linux sanal makineleri için Uzak Masaüstü 'nü nasıl kullanacağınızı öğrenin.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d13868477ff2e3378d87d7785789a7498ed17e59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85443426"
---
# <a name="connect-to-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Azure Lab Services sınıf laboratuvarında Linux sanal makinelerine bağlanma
Bu makalede, öğrencilerin kullanarak laboratuvardaki bir Linux sanal makinesine (VM) nasıl bağlanabildiği gösterilmektedir:
- SSH (Secure Shell Protocol) terminali
- GUI (grafik kullanıcı arabirimi) uzak masaüstü

> [!IMPORTANT] 
> SSH, her iki öğrenci ve eğitmenin ek bir kurulum olmadan Linux sanal makinelerine SSH olarak kullanılabilmesi için otomatik olarak yapılandırılır. Ancak, öğrenciler bir GUI Uzak Masaüstü kullanarak bağlantı kurmak gerekiyorsa, eğitmenin ek kurulum yapması gerekebilir.  Ayrıntılar için bkz. [Linux sanal makineleri için uzak masaüstünü etkinleştirme](how-to-enable-remote-desktop-linux.md).

## <a name="connect-to-the-student-vm-using-ssh"></a>SSH kullanarak öğrenci VM 'sine bağlanma

1. Bir öğrenci, Labs portalında doğrudan ( `https://labs.azure.com` ) veya bir kayıt bağlantısı () kullanarak oturum açtığında `https://labs.azure.com/register/<registrationCode>` , öğrencinin erişimi olan her laboratuvar için bir kutucuk görüntülenir. 
   
1. Kutucukta, durdurulmuş durumdaysa sanal makineyi başlatmak için düğmeyi değiştirin. 

2. **Bağlan**’ı seçin. SANAL makineye bağlanmak için iki seçenek görürsünüz: **SSH** ve **RDP**.

    ![Öğrenci VM-bağlantı seçenekleri](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

3. **SSH** seçeneğini belirleyin ve **Sanal makinenize Bağlan** iletişim kutusunu görürsünüz:  

    ![SSH bağlantı dizesi](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. SSH bağlantı bilgilerini panoya kopyalamak için metin kutusunun yanındaki **Kopyala** düğmesine tıklayın. 

5. Bu bağlantı bilgilerini bir sonraki adımda kullanabilmeniz için, metin paneli gibi SSH bağlantı bilgilerini kaydedin.

6. Bir SSH terminalinde ( [Putty](https://www.putty.org/)gibi), sanal makinenize bağlanın.

## <a name="connect-to-the-student-vm-using-gui-remote-desktop"></a>GUI uzak masaüstü 'nü kullanarak öğrenci VM 'sine bağlanma
Eğitmen, sanal makineleri bir GUI Uzak Masaüstü kullanarak da bağlayabilmeleri için VM 'Leri yapılandırmayı tercih edebilir.  Bu durumda, öğrenciler, **Microsoft Uzak Masaüstü (RDP)** veya **X2Go** istemci uygulamasını kullanarak VM 'lerine bağlanıp bağlanamayacağını öğrenmek zorunda kalmaz.  Bu uygulamaların her ikisi de bir öğrencinin sanal makinesine uzaktan bağlanmasına ve Linux grafik masaüstünü yerel bilgisayarlarında görüntülemesine olanak tanır.

### <a name="connect-to-the-student-vm-using-microsoft-remote-desktop-rdp"></a>Microsoft Uzak Masaüstü (RDP) kullanarak öğrenci VM 'sine bağlanma
Öğrenciler, bir Linux grafik masaüstü ortamı (örneğin, MATE, XFCE vb.) için kendi Linux VM 'lerine, kendi Linux sanal makinelerinde laboratuvar ve GUI paketleri ile bağlantı kurmak için Microsoft Uzak Masaüstü (RDP) kullanabilir. Bağlanma adımları aşağıda verilmiştir: 

1. SANAL makinenizin kutucuğunda VM 'nin çalıştığından emin olun ve **Bağlan**' a tıklayın. SANAL makineye bağlanmak için iki seçenek görürsünüz: **SSH** ve **RDP**.

    ![Öğrenci VM-bağlantı seçenekleri](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)
2. **RDP** seçeneğini belirleyin.  RDP dosyası makinenize indirildikten sonra sanal makinenize kaydedin.

3. Bir Windows bilgisayarından bağlanıyorsanız, genellikle Microsoft Uzak Masaüstü (RDP) istemcisi zaten yüklenir ve yapılandırılır.  Sonuç olarak, tek yapmanız gereken, RDP dosyasına tıklayarak açmak ve uzak oturumu başlatmak için gereklidir.

    Bunun yerine, bir Mac ya da Kmebook 'tan bağlanıyorsanız aşağıdaki adımlara bakın:
   - [Mac ÜZERINDE RDP kullanarak BIR VM 'ye bağlanın](connect-virtual-machine-mac-remote-desktop.md).
   - Bir [Kmebook 'TA RDP kullanarak BIR VM 'ye bağlanın](connect-virtual-machine-chromebook-remote-desktop.md).  

### <a name="connect-to-the-student-vm-using-x2go"></a>X2Go kullanarak öğrenci VM 'sine bağlanma
Öğrenciler, X2Go kullanabilir ve bir Linux grafik masaüstü ortamı (MATE, XFCE vb.) için GUI paketleriyle kendi Linux sanal makinelerine bağlanabilir.

Öğrenciler, eğitmenin yüklediği Linux grafik masaüstü ortamına ait olan bir eğitmeni öğrenmiştir.  Bu bilgiler, X2Go istemcisini kullanarak bağlanmak için sonraki adımlarda gereklidir.

1. [X2Go istemcisini](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) yerel bilgisayarınıza yükler.

1. VM 'niz için SSH bağlantı bilgilerini kopyalamak üzere [ilk bölümdeki](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-ssh) yönergeleri izleyin.  X2Go istemcisini kullanarak bağlanmak için bu bilgilere ihtiyacınız vardır.

1. SSH bağlantı bilgilerine sahip olduktan sonra X2Go istemcisini açın ve **oturum**  >  **yeni oturum**' yi seçin.
   ![X2Go yeni oturum oluştur](./media/how-to-use-classroom-lab/x2go-new-session.png)

1. SSH bağlantı bilgilerinizi temel alarak **oturum tercihleri** bölmesindeki değerleri girin.  Örneğin, bağlantı bilgileriniz şuna benzer olacaktır:

    ```bash
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
    ```

    Bu örneği kullanarak, aşağıdaki değerler girilir:

   - **Oturum adı** -sanal makinenizin adı gibi bir ad belirtin.
   - **Ana bilgisayar** -sanal makinenizin kimliği; Örneğin, **`ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`** .
   - **Oturum açma** -sanal makinenizin Kullanıcı adı; Örneğin, **öğrenci**.
   - **SSH bağlantı noktası** -sanal makinenize atanan benzersiz bağlantı noktası; Örneğin, **12345**.
   - **Oturum türü** -eğitmenin sanal makinenize yapılandırdığı Linux grafik masaüstü ortamını seçin.  Bu bilgileri Eğitmeninizden almanız gerekir.

    Son olarak, oturumu oluşturmak için **Tamam** ' ı tıklatın.

    ![X2Go oturum tercihleri](./media/how-to-use-classroom-lab/x2go-session-preferences.png)

1.  Sağ taraftaki bölmede oturumunuz ' na tıklayın.

    ![X2Go yeni oturum Başlat](./media/how-to-use-classroom-lab/x2go-start-session.png)

    > [!NOTE] 
    > Buna benzer bir ileti istenirse, parolanızı girmeye devam etmek için **Evet** ' i seçin: **' [ `00000000-0000-0000-0000-000000000000.eastus2.cloudapp.eastus.cloudapp.azure.com` ]: 12345 ' konağının gerçekliği oluşturulamıyor.  ECDSA anahtar parmak izi SHA256:00000000000000000000000000000000000000000000. bağlanmaya devam etmek istediğinizden emin misiniz (Evet/Hayır)?**

2. İstendiğinde parolanızı girip **Tamam**' a tıklayın.  Artık, sanal makinenizin GUI masaüstü ortamına uzaktan bağlanırsınız.

## <a name="next-steps"></a>Sonraki adımlar
Bir sınıf laboratuvarında Linux VM 'Leri için Uzak Masaüstü bağlantısı özelliğini etkinleştirme hakkında bilgi edinmek için bkz. [Linux sanal makineleri için uzak masaüstünü etkinleştirme](how-to-enable-remote-desktop-linux.md). 

