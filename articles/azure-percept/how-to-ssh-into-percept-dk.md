---
title: SSH üzerinden Azure Percept DK 'ye bağlanma
description: PuTTY ile Azure Percept DK 'de nasıl SSH kullanacağınızı öğrenin
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 39ee1c1cc5b52dc62e3199536234c1f7d9381436
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104721486"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>SSH üzerinden Azure Percept DK 'ye bağlanma

OpenSSH veya [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)aracılığıyla Azure Percept dk 'NIZE bir SSH bağlantısı kurmak için aşağıdaki adımları izleyin.

## <a name="prerequisites"></a>Önkoşullar

- Wi-Fi özelliğine sahip bir Windows, Linux veya OS X tabanlı ana bilgisayar
- Bir SSH istemcisi (Yükleme Kılavuzu için sonraki bölüme bakın)
- Azure Percept DK (Dev Kit)
- [Azure PERCEPT dk kurulum deneyimi](./quickstart-percept-dk-set-up.md) SıRASıNDA oluşturulan SSH oturum açma bilgileri

## <a name="install-your-preferred-ssh-client"></a>Tercih ettiğiniz SSH istemcinizi yüklemesi

Ana bilgisayarınız Linux veya OS X çalıştırıyorsa, SSH Hizmetleri bu işletim sistemlerine dahil edilir ve ayrı bir istemci uygulaması olmadan çalıştırılabilir. SSH hizmetlerinin nasıl çalıştırılacağı hakkında daha fazla bilgi için işletim sistemi ürün belgelerinize bakın.

Ana bilgisayarınız Windows çalıştırıyorsa, aralarından seçim yapabileceğiniz iki SSH istemci seçeneğiniz olabilir: OpenSSH ve PuTTY.

### <a name="openssh"></a>OpenSSH

Windows 10, OpenSSH adlı yerleşik bir SSH istemcisi içerir ve komut istemi içinde basit bir komutla çalıştırılabilir. Kullanılabilir ise, Azure Percept ile OpenSSH kullanılması önerilir. Windows bilgisayarınızda OpenSSH yüklü olup olmadığını denetlemek için şu adımları izleyin:

1. **Başlangıç**  ->  **ayarları**' na gidin.

1. **Uygulamalar**' ı seçin.

1. **Uygulamalar & Özellikler** altında, **isteğe bağlı özellikler**' i seçin.

1. **Yüklü Özellikler** arama çubuğuna **OpenSSH istemcisini** yazın. OpenSSH görüntülenirse, istemci zaten yüklüdür ve sonraki bölüme geçebilirsiniz. OpenSSH görmüyorsanız, **Özellik Ekle**' ye tıklayın.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-install.png" alt-text="OpenSSH yükleme durumunu gösteren ayarların ekran görüntüsü.":::

1. **OpenSSH Client** ' ı seçin ve ardından **Install**( Şimdi bir sonraki bölüme geçebilirsiniz. Bilgisayarınızda yüklemesi için OpenSSH yoksa, üçüncü taraf bir SSH istemcisi olan PuTTY 'yi yüklemek için aşağıdaki adımları izleyin.

### <a name="putty"></a>PuTTY

Windows Bilgisayarınız OpenSSH içermiyorsa, [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)kullanmanızı öneririz. PuTTY indirmek ve yüklemek için aşağıdaki adımları izleyin:

1. [Putty indirme sayfasına](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)gidin.

1. **Paket dosyaları** altında, yükleyiciyi indirmek için 32-bit veya 64-bit. msi dosyasına tıklayın. Hangi sürümden seçeceğiniz konusunda emin değilseniz, [SSS](https://www.chiark.greenend.org.uk/~sgtatham/putty/faq.html#faq-32bit-64bit)'ye göz atın.

1. Yükleme işlemini başlatmak için yükleyiciye tıklayın. Gerektiğinde istemleri izleyin.

1. Tebrikler! PuTTY SSH istemcisini başarıyla yüklediniz.

## <a name="initiate-the-ssh-connection"></a>SSH bağlantısını başlatma

1. Azure Percept DK üzerinde güç.

1. Geliştirme seti, Ethernet veya Wi-Fi üzerinden bir ağa zaten bağlıysa, sonraki adıma atlayın. Aksi takdirde, ana bilgisayarınızı doğrudan Dev Kit 'in Wi-Fi erişim noktasına bağlayın. Diğer bir Wi-Fi ağına bağlanma gibi, bilgisayarınızda ağ ve İnternet ayarlarını açın, aşağıdaki ağa tıklayın ve istendiğinde ağ parolasını girin:

    - **Ağ adı**: Dev Kit 'in işletim sistemi sürümüne bağlı olarak, Wi-Fi erişim noktasının adı **SCZ-xxxx** veya **APD-xxxx** olur (burada "xxxx", Dev Kit 'in Mac adresinin son dört rakamı)
    - **Parola**: Dev Kit Ile gelen hoş geldiniz kartında bulunabilir

    > [!WARNING]
    > Azure Percept DK Wi-Fi erişim noktasına bağlıyken, ana bilgisayarınız Internet bağlantısını geçici olarak kaybedecektir. Etkin görüntülü konferans çağrıları, web akışı veya diğer ağ tabanlı deneyimler kesintiye uğracaktır.

1. SSH istemcisine göre SSH bağlantı işlemini doldurun.

### <a name="using-openssh"></a>OpenSSH kullanma

1. Bir komut istemi açın (  ->  **komut istemi**' ni başlatın).

1. Komut istemine aşağıdaki komutu girin:

    ```console
    ssh [your ssh user name]@[IP address]
    ```

    Bilgisayarınız Dev Kit 'in Wi-Fi erişim noktasına bağlıysa, IP adresi 10.1.1.1 olur. Geliştirme seti, Ethernet üzerinden bağlandıysa, cihazın Ethernet yönlendiricisinden veya hub 'ından alabileceğiniz yerel IP adresini kullanın. Geliştirme seti, Wi-Fi üzerinden bağlanırsa, [Azure PERCEPT dk kurulum deneyimi](./quickstart-percept-dk-set-up.md)sırasında geliştirme setinize atanan IP adresini kullanmanız gerekir.

    > [!TIP]
    > Geliştirme seti bir Wi-Fi ağa bağlıysa ancak IP adresini görmüyorsanız Azure Percept Studio ' ya gidin ve [cihazınızın video akışını açın](./how-to-view-video-stream.md). Video akışı tarayıcısı sekmesindeki Adres çubuğu, cihazınızın IP adresini gösterir.

1. İstendiğinde SSH parolanızı girin.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-prompt.png" alt-text="Açık SSH komut istemi oturum açma ekran görüntüsü.":::

1. Bu, OpenSSH aracılığıyla geliştirme setinizi ilk kez bağlıyorsanız, konağın anahtarını kabul etmeniz de istenebilir. Anahtarı kabul etmek için **Evet** girin.

1. Tebrikler! Geliştirme setinizi SSH üzerinden başarıyla bağladınız.

### <a name="using-putty"></a>PuTTY kullanma

1. PuTTY’yi açın. **Putty yapılandırma** penceresine aşağıdakini girin ve geliştirme SETINIZE SSH için **Aç** ' a tıklayın:

    1. Ana bilgisayar adı: [IP adresi]
    1. Bağlantı noktası: 22
    1. Bağlantı türü: SSH

    **Ana bilgisayar adı** dev kıt 'in IP adresidir. Bilgisayarınız Dev Kit 'in Wi-Fi erişim noktasına bağlıysa, IP adresi 10.1.1.1 olur. Geliştirme seti, Ethernet üzerinden bağlandıysa, cihazın Ethernet yönlendiricisinden veya hub 'ından alabileceğiniz yerel IP adresini kullanın. Geliştirme seti, Wi-Fi üzerinden bağlanırsa, [Azure PERCEPT dk kurulum deneyimi](./quickstart-percept-dk-set-up.md)sırasında geliştirme setinize atanan IP adresini kullanmanız gerekir.

    > [!TIP]
    > Geliştirme seti bir Wi-Fi ağa bağlıysa ancak IP adresini görmüyorsanız Azure Percept Studio ' ya gidin ve [cihazınızın video akışını açın](./how-to-view-video-stream.md). Video akışı tarayıcısı sekmesindeki Adres çubuğu, cihazınızın IP adresini gösterir.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="PuTTY yapılandırma penceresinin ekran görüntüsü.":::

1. PuTTY terminali açılır. İstendiğinde, terminale SSH kullanıcı adınızı ve parolanızı girin.

1. Tebrikler! Geliştirme setinizi SSH üzerinden başarıyla bağladınız.

## <a name="next-steps"></a>Sonraki adımlar

SSH üzerinden Azure Percept DK 'ye bağlandıktan sonra, [cihaz sorun gidermesi](./troubleshoot-dev-kit.md) ve [USB güncelleştirmeleri](./how-to-update-via-usb.md)dahil olmak üzere çeşitli görevler gerçekleştirebilirsiniz.