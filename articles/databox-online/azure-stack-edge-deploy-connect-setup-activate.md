---
title: Azure portal 'de Azure Stack Edge Pro cihazını bağlama, yapılandırma, etkinleştirme öğreticisi | Microsoft Docs
description: Azure Stack Edge Pro 'Yu dağıtmaya yönelik öğretici, fiziksel cihazınızı bağlanmanızı, ayarlamanıza ve etkinleştirmenizi sağlar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: alkohli
ms.openlocfilehash: af0c305cc13b94666a87d937d1eac10586135a4d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067701"
---
# <a name="tutorial-connect-set-up-and-activate-azure-stack-edge-pro"></a>Öğretici: Azure Stack Edge Pro 'Yu bağlama, ayarlama ve etkinleştirme 

Bu öğreticide, yerel Web Kullanıcı arabirimini kullanarak Azure Stack Edge Pro cihazınızı nasıl bağlayabileceğinizi, ayarlayabileceğinizi ve etkinleştirebileceğiniz açıklanmaktadır.

Kurulum ve etkinleştirme işleminin tamamlanması 20 dakika sürebilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Fiziksel bir cihaza bağlanma
> * Fiziksel cihazı ayarlama ve etkinleştirme

## <a name="prerequisites"></a>Önkoşullar

Azure Stack Edge Pro cihazınızı yapılandırmadan ve ayarlamadan önce şunları yaptığınızdan emin olun:

* Fiziksel cihazı [yükleme Azure Stack Edge Pro](azure-stack-edge-deploy-install.md)' da ayrıntılı olarak yüklediniz.
* Azure Stack Edge Pro cihazını yönetmek için oluşturduğunuz Azure Stack Edge hizmetinden etkinleştirme anahtarınız vardır. Daha fazla bilgi için, [Azure Stack Edge Pro 'yu dağıtmaya hazırlanma](azure-stack-edge-deploy-prep.md)bölümüne gidin.

## <a name="connect-to-the-local-web-ui-setup"></a>Yerel Web Kullanıcı arabirimi kurulumuna bağlanma

1. Bilgisayarınızda Ethernet bağdaştırıcısını, 192.168.100.5 ve alt ağ 255.255.255.0 statik IP adresiyle Azure Stack Edge Pro cihazına bağlanacak şekilde yapılandırın.

2. Bilgisayarınızı cihazınızdaki PORT 1'e bağlayın. Cihazınızda PORT 1'i belirlemek için aşağıdaki çizimi kullanın.

    ![Kabloları takılmış bir cihazın arka yüzü](./media/azure-stack-edge-deploy-install/backplane-cabled.png)

3. Tarayıcı penceresini açın ve `https://192.168.100.10` adresinden cihazın yerel web kullanıcı arabirimine erişin.  
    Siz cihazı açtıktan sonra bu eylem birkaç dakika sürebilir.

    Web sitesinin güvenlik sertifikasında sorun olduğunu belirten bir hata veya uyarı görürsünüz.
   
    ![Web sitesi güvenlik sertifikası hata iletisi](./media/azure-stack-edge-deploy-connect-setup-activate/image2.png)

4. **Bu Web sayfasına devam et**' i seçin.  
    Bu adımlar kullandığınız tarayıcıya bağlı olarak değişebilir.

5. Cihazınızın web kullanıcı arabiriminde oturum açın. Varsayılan parola *Parola1*' dir. 
   
    ![Azure Stack Edge Pro cihaz oturum açma sayfası](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. İstem sırasında cihaz yönetici parolasını değiştirin.  
    Yeni parola 8 ile 16 arasında karakter içermelidir. Parola şu karakterlerden üçünü içermelidir: küçük harf, büyük harf, rakam ve özel karakterler.

Artık cihazınızın panosundan hazırsınız.

## <a name="set-up-and-activate-the-physical-device"></a>Fiziksel cihazı ayarlama ve etkinleştirme
 
Panonuz fiziksel cihazı Azure Stack Edge hizmeti ile yapılandırmak ve kaydetmek için gereken çeşitli ayarları görüntüler. **Cihaz adı**, **ağ ayarları**, **Web proxy ayarları** ve **zaman ayarları** isteğe bağlıdır. Yalnızca **bulut ayarları** gereklidir.
   
![Yerel Web Kullanıcı arabirimi "Pano" sayfası](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. Sol bölmede **Cihaz adı**' nı seçin ve ardından cihazınız için kolay bir ad girin.  
    Kolay ad, 1 ile 15 arasında karakter içermeli ve harf, rakam ve kısa çizgi içermelidir.

    ![Yerel Web Kullanıcı arabirimi "Cihaz adı" sayfası](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-2.png)

2. Seçim Sol bölmede **ağ ayarları** ' nı seçin ve ardından ayarları yapılandırın.  
    Fiziksel cihazınızda altı ağ arabirimi vardır. PORT 1 ve PORT 2, 1 Gb/sn'lik ağ arabirimleridir. Bağlantı noktası 3, bağlantı noktası 4, bağlantı noktası 5 ve bağlantı noktası 6, 10 Gbps ağ arabirimi olarak da kullanılabilen tüm 25 Gbps ağ arabirimlerdir. BAĞLANTı noktası 1 otomatik olarak yalnızca yönetim bağlantı noktası olarak yapılandırılır ve bağlantı noktası 6 ' dan bağlantı noktası 6 ' dan tüm veri bağlantı noktaları bulunur. **Ağ ayarları** sayfası aşağıda gösterildiği gibidir.
    
    ![Yerel Web Kullanıcı arabirimi "ağ ayarları" sayfası](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Ağ ayarlarını yapılandırırken şunları aklınızda bulundurun:

   - Ortamınızda DHCP etkinse, ağ arabirimleri otomatik olarak yapılandırılır. IP adresi, alt ağ, ağ geçidi ve DNS otomatik olarak atanır.
   - DHCP etkinleştirilmemişse, gerekirse statik IP 'Ler atayabilirsiniz.
   - Ağ arabiriminizi IPv4 olarak yapılandırabilirsiniz.

     >[!NOTE] 
     > Cihaza bağlanmak için başka bir IP adresiniz yoksa, ağ arabiriminin yerel IP adresini statikten DCHP’ye değiştirmeniz önerilmez. Tek bir ağ arabirimi kullanıyorsanız ve DHCP’ye geçiyorsanız, DHCP adresini belirlemenin hiçbir yolu yoktur. DHCP adresini değiştirmek istiyorsanız cihazın hizmete kaydolmasını bekleyin ve ondan sonra değiştirin. Daha sonra, hizmetinize yönelik Azure portal **cihaz özelliklerindeki** tüm bağdaştırıcıların IP 'lerini görüntüleyebilirsiniz.

3. Seçim Sol bölmede **Web proxy ayarları**' nı seçin ve ardından Web Proxy sunucunuzu yapılandırın. Web proxy yapılandırması isteğe bağlı olsa da, bir Web Proxy kullanıyorsanız, bunu yalnızca bu sayfada yapılandırabilirsiniz.
   
   ![Yerel Web Kullanıcı arabirimi "Web proxy ayarları" sayfası](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   **Web proxy ayarları** sayfasında, aşağıdakileri yapın:
   
   a. **Web proxy URL 'si** kutusuna URL 'yi şu biçimde girin: `http://host-IP address or FQDN:Port number` . HTTPS URL'leri desteklenmez.

   b. **Kimlik Doğrulaması**'nın altında **Yok** veya **NTLM**'yi seçin. İşlem ve Azure Stack Edge Pro cihazınızda IoT Edge modülü kullanıyorsanız, Web proxy kimlik doğrulamasını **none** olarak ayarlamanızı öneririz. **NTLM** desteklenmiyor.

   c. Kimlik doğrulaması kullanıyorsanız, bir Kullanıcı adı ve parola girin.

   d. Yapılandırılmış Web proxy ayarlarını doğrulamak ve uygulamak için **Ayarları Uygula**' yı seçin.

   > [!NOTE]
   > Proxy-otomatik yapılandırma (PAC) dosyaları desteklenmez. PAC dosyası, Web tarayıcılarının ve diğer Kullanıcı aracılarının belirli bir URL 'YI getirmek için uygun proxy sunucusunu (erişim yöntemi) otomatik olarak nasıl seçebileceğini tanımlar.
   > Tüm trafiği kesmeye ve okumaya (sonra da kendi sertifikasıyla yeniden imzalamaya) çalışan ara sunucular uyumlu değildir çünkü ara sunucunun sertifikası güvenilir değildir.
   > Genellikle saydam proxy 'ler Azure Stack Edge Pro ile iyi çalışır.

4. Seçim Sol bölmede **zaman ayarları**' nı seçin ve ardından cihazınız için saat dilimini ve birincil ve ikincil NTP sunucularını yapılandırın.  
    Cihazınızın bulut hizmeti sağlayıcılarınız ile kimlik doğrulaması yapabilmesi için zaman eşitlemesini gerektiğinden NTP sunucuları gereklidir.
       
    **Zaman ayarları** sayfasında, aşağıdakileri yapın:
    
    1. **Saat dilimi** açılan listesinde, cihazın dağıtıldığı coğrafi konuma karşılık gelen saat dilimini seçin.
        Cihazınızın varsayılan saat dilimi PST ' dir. Cihazınız zamanlanan tüm işlemler için bu saat dilimini kullanır.

    2. **BIRINCIL NTP sunucusu** kutusunda, cihazınızın birincil sunucusunu girin veya Time.Windows.com varsayılan değerini kabul edin.  
        Ağınızda NTP trafiğinin veri merkezinizden internet 'e geçmesine izin verdiğinden emin olun.

    3. İsteğe bağlı olarak, **IKINCIL NTP sunucusu** kutusuna cihazınız için bir ikincil sunucu girin.

    4. Yapılandırılan saat ayarlarını doğrulamak ve uygulamak için **Ayarları Uygula**' yı seçin.

        ![Yerel Web Kullanıcı arabirimi "zaman ayarları" sayfası](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-5.png)

5. Seçim Cihazınızda depolama dayanıklılığı yapılandırmak için sol bölmede **depolama ayarları** ' nı seçin. Bu özellik şu anda önizleme sürümündedir. Varsayılan olarak, cihazdaki depolama alanı dayanıklı değildir ve cihazda bir veri diski başarısız olursa veri kaybı olur. Dayanıklı seçeneğini etkinleştirdiğinizde, cihazdaki depolama alanı yeniden yapılandırılır ve cihaz veri kaybı olmadan bir veri diskinin hatasını değiştirebilir. Depolamayı dayanıklı olarak yapılandırmak, cihazınızın kullanılabilir kapasitesini azaltır.

    > [!IMPORTANT] 
    > Dayanıklılık yalnızca cihazı etkinleştirmeden önce yapılandırılabilir. 

    ![Yerel Web Kullanıcı arabirimi "depolama ayarları" sayfası](./media/azure-stack-edge-deploy-connect-setup-activate/storage-settings.png)

6. Sol bölmede **bulut ayarları**' nı seçin ve ardından Azure Portal Azure Stack Edge hizmeti ile cihazınızı etkinleştirin.
    
    1. **Etkinleştirme anahtarı** kutusuna Azure Stack Edge Pro için [etkinleştirme anahtarını al](azure-stack-edge-deploy-prep.md#get-the-activation-key) ' da aldığınız etkinleştirme anahtarını girin.
    2. **Uygula**’yı seçin.
       
        ![Yerel Web Kullanıcı arabirimi "bulut ayarları" sayfası](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-6.png)

    3. İlk olarak cihaz etkinleştirilir. Daha sonra bu cihaz kritik güncelleştirmeler için taranır ve varsa güncelleştirmeler otomatik olarak uygulanır. Bu etkiye yönelik bir bildirim görürsünüz.

        İletişim kutusunda Ayrıca, kopyalayıp güvenli bir konuma kaydetmeniz gereken bir kurtarma anahtarı bulunur. Bu anahtar, cihazın önyüklenebildiği olaydaki verilerinizi kurtarmak için kullanılır.

        ![Yerel Web Kullanıcı arabirimi "bulut ayarları" sayfası güncelleştirildi](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-7.png)

    4. Güncelleştirme başarıyla tamamlandıktan sonra birkaç dakika beklemeniz gerekebilir. Sayfa, cihazın başarıyla etkinleştirildiğini belirtecek şekilde güncelleştirilir.

        ![Yerel Web Kullanıcı arabirimi "bulut ayarları" sayfası güncelleştirildi 2](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-8.png)

Cihaz kurulumu tamamlanmıştır. Artık cihazınıza paylaşımlar ekleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Fiziksel bir cihaza bağlanma
> * Fiziksel cihazı ayarlama ve etkinleştirme

Azure Stack Edge Pro cihazınızla veri aktarmayı öğrenmek için bkz.:

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro ile veri aktarma](./azure-stack-edge-deploy-add-shares.md).
