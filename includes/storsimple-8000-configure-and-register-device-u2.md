---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: ee92e145acd981bb8c84b43986b90306b994c262
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95561090"
---
#### <a name="to-configure-and-register-the-device"></a>Cihazı yapılandırmak ve kaydetmek için

1. StorSimple cihazı seri konsolunuzdaki Windows PowerShell arabirimine erişin. Talimatlar için bkz. [Cihaz seri konsoluna bağlanmak için PuTTY kullanma](#use-putty-to-connect-to-the-device-serial-console). **Yordamı hatasız takip ettiğinizden emin olun; aksi taktirde konsola erişemezsiniz.**

2. Açılan oturumda, komut istemi almak için bir kez **ENTER** tuşuna basın.

3. Cihazınızda ayarlamak istediğiniz dili seçmeniz istenecektir. Dili belirtin ve ardından **ENTER** tuşuna basın.

4. Verilen seri konsol menüsünde **Tam erişimle oturum açmak** için 1 seçeneğini belirleyin.
     Cihazınız için en düşük gerekli ağ ayarlarını yapılandırmak için 5-12 arası adımları tamamlayın. **Bu yapılandırma adımları, cihazın etkin denetleyicisinde gerçekleştirilmelidir.** Seri konsol menüsü, bant iletisindeki denetleyici durumunu belirtir. Etkin denetleyiciye bağlı değilseniz, bağlantıyı kesip etkin denetleyiciye bağlayın.

5. Komut istemine parolanızı yazın. Varsayılan cihaz parolası **Password1**’dir.

6. Şu komutu yazın: `Invoke-HcsSetupWizard`.

7. Cihazın ağ ayarlarını yapılandırmanıza yardımcı olacak bir kurulum sihirbazı görüntülenir. Aşağıdaki bilgileri verin:
   
   * DATA 0 ağ arabirimi için IP adresi
   * Alt ağ maskesi
   * Ağ geçidi
   * Birincil DNS sunucusu için IP adresi

   Aşağıda örnek bir çıktı sunulmuştur.

    ```
        ---------------------------------------------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: 8100-SHX0991003G44MT
        Software Version: 6.3.9600.17759
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Active
        ---------------------------------------------------------------

        Your device needs to be registered with the Microsoft Azure StorSimple Manager service. Please run 'Invoke-HcsSetupWizard' to set up your device.

        Controller0>Invoke-HcsSetupWizard

        Which IP address family would you like to configure on interface Data0?
        [4] IPv4 [6] IPv6 [B] Both (Default is "4"): 4

        Data0 IPv4 address:10.111.111.00
        Data0 IPv4 subnet: 255.255.252.0
        Data0 IPv4 gateway: 10.111.111.11

        IPv4 primary DNS server [10.222.118.154]:10.222.222.111
    ```

    <br>
    Yukarıdaki örnek çıktıda, işlemin her adımından sonra sistemin ağ ayarlarını doğruladığını görebilirsiniz.

     > [!NOTE]
     > Uygulanacak alt ağ maskesi ve DNS ayarları için dakika beklemeniz gerekebilir. “Data 0 ağ bağlantısını denetleyin” hata iletisi alırsanız, etkin denetleyicinizin DATA 0 ağ arabirimindeki fiziksel ağ bağlantısını işaretleyin.

8. (İsteğe bağlı), web ara sunucusunu yapılandırın. Web ara sunucusunun yapılandırması isteğe bağlı olsa **bir web ara sunucu kullanıyorsanız, burada yalnızca bunu yapılandırabileceğinizi unutmayın**. Daha fazla bilgi için [Cihazınız için web ara sunucusunu yapılandırma](../articles/storsimple/storsimple-8000-configure-web-proxy.md)’ya gidin.
9. Cihazınız için Birincil NTP sunucusunu yapılandırın. Bulut hizmeti sağlayıcılarıyla doğrulanabileceği şekilde cihazınızı saati eşitlemesi gerektiğinden NTP sunucuları gereklidir. Ağınızın, NTP trafiğini veri merkezinizden İnternete geçirilmesine izin vermesini sağlayın. Bu yapılamıyorsa dahili bir NTP sunucusu belirtin.

    Örnek çıktı aşağıda gösterilmiştir.

    ```
        Would you like to configure a web proxy?
        [Y] Yes [N] No (Default is "N"):N

        Primary NTP server [time.windows.com]:time.windows.com

    ```

10. Güvenlik nedenleriyle, cihaz yönetici parolasının süresi ilk oturumun ardından dolar; artık bunu değiştirmeniz gerekir. İstendiğinde cihaz yöneticisi parolasını verin. Geçerli bir cihaz yöneticisi parolası 8-15 karakter arasında olmalıdır. Parolada aşağıdakilerden üçü olmalıdır: küçük harf, büyük harf, rakam ve özel karakter.

    ```
        The device administrator password must be between 8 and 15 characters. The password must contain a combination of uppercase letters, lowercase letters, numbers and special characters.
        Administrator Password:********
        Confirm Administrator Password:********
    ```
11. Kurulum sihirbazının son adımı cihazınızı StorSimple Cihaz Yöneticisi hizmetine kaydeder. Bunun için, 2. adımda aldığınız hizmet kayıt anahtarı gerekir. Kayıt anahtarını verdikten sonra cihazın kaydolması için 2-3 dakika beklemeniz gerekebilir.
    
    > [!NOTE]
    > Kurulum sihirbazından çıkmak için istediğiniz zaman Ctrl + C tuşlarına basabilirsiniz. Tüm ağ ayarlarını (Data 0 için IP adresi, alt ağ maskesi ve ağ geçidi) girdiyseniz girişleriniz korunur.
    
    Örnek çıktı aşağıda gösterilmiştir.

    ```
        The service registration key is available in the StorSimple Manager service.
        Enter service registration key:**************************************
        Device registration is in progress. Please wait.

    ```

12. Cihaz kaydedildikten sonra Hizmet Verileri Şifreleme anahtarı görüntülenir. Bu anahtarı kopyalayın ve güvenli bir konuma kaydedin. **StorSimple Cihaz Yöneticisi hizmetiyle ek cihazlar kaydetmek için hizmet kayıt anahtarıyla birlikte bu anahtar da gerekecektir.** Bu anahtar hakkında daha fazla bilgi için bkz. [StorSimple güvenliği](../articles/storsimple/storsimple-8000-security.md).
    
    ![StorSimple kayıt cihazı 7](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup1.png)
    
    > [!NOTE]
    > Seri konsol penceresinden metin kopyalamak için metni seçmeniz yeterlidir. Artık bunu panoya veya metin düzenleyicilere yapıştırabilirsiniz. Hizmet verileri şifreleme anahtarını kopyalamak için CTRL + C tuşlarını kullanmayın. Ctrl + C tuşlarının kullanılması kurulum sihirbazından çıkmanıza neden olur. Sonuç olarak, cihaz yöneticisi parolası değiştirilmez ve cihaz varsayılan parolasına döner.
    
13. Seri konsoldan çıkın.
14. Azure portalına dönün ve aşağıdaki adımları tamamlayın:
    
    1. StorSimple Cihaz Yöneticisi hizmetinize gidin.
    2. **Cihazlar**’a tıklayın.
    3. Cihazların tablosal listesinde cihazın durumunu arayarak cihazın hizmete sorunsuz bir şekilde bağlandığını doğrulayın. Cihazın durumu **Kuruluma hazır** olmalıdır.
       
        ![StorSimple Cihazları sayfası](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup2.png)
       
        Cihaz durumunun **Kuruluma hazır** hale gelmesi için birkaç dakika beklemeniz gerekebilir.
       
        Cihaz bu listede görünmüyorsa, güvenlik duvarı ağınızın [StorSimple cihazınız için ağ gereksinimlerinde](../articles/storsimple/storsimple-8000-system-requirements.md) açıklandığı şekilde yapılandırıldığından emin olun. StorSimple Cihaz Yöneticisi hizmeti ile cihazlar arasındaki iletişim için hizmet veri yolu tarafından kullanılan 9354 bağlantı noktasının giden iletişim için açık olduğunu doğrulayın.