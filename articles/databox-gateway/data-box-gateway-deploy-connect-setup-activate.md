---
title: Azure portal Azure Data Box Gateway bağlanma, yapılandırma ve etkinleştirme
description: Data Box Gateway dağıtmaya yönelik üçüncü öğretici, sanal cihazınızı bağlanmanızı, ayarlamanıza ve etkinleştirmenizi sağlar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: cb6ef6ac25c4afa72160ba437e0ea3b5492cfd93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96583690"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Öğretici: Azure Data Box Gateway'i bağlama, kurma ve etkinleştirme

## <a name="introduction"></a>Giriş

Bu öğretici, yerel Web Kullanıcı arabirimini kullanarak Data Box Gateway cihazınızı nasıl bağlanılacağını, ayarlanacağını ve etkinleştireceğinize açıklamaktadır. 

Kurulum ve etkinleştirme işleminin tamamlanması yaklaşık 10 dakika sürebilir. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Sanal cihaza Bağlan
> * Sanal cihazı ayarlama ve etkinleştirme

## <a name="prerequisites"></a>Önkoşullar

Data Box Gateway yapılandırmadan ve ayarlamadan önce, aşağıdakileri yaptığınızdan emin olun:

* Bir sanal cihaz sağladınız ve [Hyper-V ' d e bir Data Box Gateway sağlama](data-box-gateway-deploy-provision-hyperv.md) veya [VMware 'de bir Data Box Gateway sağlama](data-box-gateway-deploy-provision-vmware.md)bölümünde açıklandığı şekılde buna bağlı bir URL edindiniz.
* Data Box Gateway cihazlarını yönetmek için oluşturduğunuz Data Box Gateway hizmetinden etkinleştirme anahtarınız vardır. Daha fazla bilgi için, [Azure Data Box Gateway dağıtmaya hazırlanma](data-box-gateway-deploy-prep.md)bölümüne gidin.


## <a name="connect-to-the-local-web-ui-setup"></a>Yerel Web Kullanıcı arabirimi kurulumuna bağlanma 

1. Bir tarayıcı penceresi açın ve cihazın yerel Web Kullanıcı arabirimine şu adresten erişin:
   
   https: \/ /ip-Address-of-Network-Interface
   
   Önceki öğreticide belirtilen bağlantı URL 'sini kullanın. Web sitesinin güvenlik sertifikasında sorun olduğunu belirten bir hata veya uyarı görürsünüz.

2. **Bu Web sayfasına devam et**' i seçin. Bu adımlar kullandığınız tarayıcıya bağlı olarak değişebilir.
   
    ![Web sitesi güvenlik sertifikası hata iletisi](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Sanal cihazınızın web kullanıcı arabiriminde oturum açın. Varsayılan parola *Parola1*' dir. 
   
    ![Yerel Web Kullanıcı arabiriminde oturum açın](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. İstemde, cihaz parolasını değiştirin. Yeni parola 8 ile 16 arasında karakter içermelidir. Aşağıdakilerden 3 ' ü içermesi gerekir: büyük harf, küçük harf, sayısal ve özel karakterler.

    ![Cihaz parolasını değiştirme](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Artık cihazınızın **panosundan** hazırsınız.

## <a name="set-up-and-activate-the-virtual-device"></a>Sanal cihazı ayarlama ve etkinleştirme
 
Panonuz, sanal cihazı Data Box Gateway hizmeti ile yapılandırmak ve kaydetmek için gereken çeşitli ayarları görüntüler. **Cihaz adı**, **ağ ayarları**, **Web proxy ayarları** ve **zaman ayarları** isteğe bağlıdır. Yalnızca **bulut ayarları** gereklidir.
   
![Yerel Web Kullanıcı arabirimi "Pano" sayfası](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. Sol bölmede **Cihaz adı**' nı seçin ve ardından cihazınız için kolay bir ad girin. Kolay ad, 1 ile 15 karakter uzunluğunda olmalı ve harf, rakam ve kısa çizgi içermelidir. 

    ![Yerel Web Kullanıcı arabirimi "Cihaz adı" sayfası](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. Seçim Sol bölmede **ağ ayarları** ' nı seçin ve ardından ayarları yapılandırın. Sanal cihazınızda, en az bir ağ arabirimi ve temel alınan sanal makinede ne kadar yapılandırdığınıza bağlı olarak daha fazla bilgi edineceksiniz. Bir ağ arabirimi etkin bir sanal cihaz için **ağ ayarları** sayfası aşağıda gösterildiği gibidir.
    
    ![Yerel Web Kullanıcı arabirimi "ağ ayarları" sayfası](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Ağ ayarlarını yapılandırırken şunları göz önünde bulundurun:

    - Ortamınızda DHCP etkinse, ağ arabirimleri otomatik olarak yapılandırılır. IP adresi, alt ağ, ağ geçidi ve DNS otomatik olarak atanır.
    - DHCP etkinleştirilmemişse, gerekirse statik IP 'Ler atayabilirsiniz.
    - Ağ arabiriminizi IPv4 olarak yapılandırabilirsiniz.

     >[!NOTE] 
     > Cihaza bağlanmak için başka bir IP adresiniz yoksa, ağ arabiriminin yerel IP adresini statik 'ten DHCP 'ye geçmenizi öneririz. Tek bir ağ arabirimi kullanıyorsanız ve DHCP’ye geçiyorsanız, DHCP adresini belirlemenin hiçbir yolu yoktur. DHCP adresini değiştirmek istiyorsanız cihazın hizmete kaydolmasını bekleyin ve ondan sonra değiştirin. Daha sonra, hizmetinize yönelik Azure portal **cihaz özelliklerindeki** tüm bağdaştırıcıların IP 'lerini görüntüleyebilirsiniz.

3. (İsteğe bağlı), web ara sunucusunu yapılandırın. Web proxy yapılandırması isteğe bağlı olsa da, bir Web Proxy kullanıyorsanız, bunu yalnızca bu sayfada yapılandırabilirsiniz.
   
   ![Yerel Web Kullanıcı arabirimi "Web proxy ayarları" sayfası](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   **Web proxy** sayfasında, şunları yapın:
   
   1. **Web proxy URL 'si** kutusuna URL 'yi şu biçimde girin: `http://&lt;host-IP address or FQDN&gt;:Port number` . HTTPS URL'leri desteklenmez.
   2. **Kimlik Doğrulaması**'nın altında **Yok** veya **NTLM**'yi seçin.
   3. Kimlik doğrulaması kullanıyorsanız, bir **Kullanıcı adı** ve **parola** girin.
   4. Yapılandırılmış Web proxy ayarlarını doğrulamak ve uygulamak için **Uygula**' yı seçin.

   > [!NOTE]
   > Proxy-otomatik yapılandırma (PAC) dosyaları desteklenmez. PAC dosyası, Web tarayıcılarının ve diğer Kullanıcı aracılarının belirli bir URL 'YI getirmek için uygun proxy sunucusunu (erişim yöntemi) otomatik olarak nasıl seçebileceğini tanımlar.
   > Tüm trafiği kesmeye ve okumaya (sonra da kendi sertifikasıyla yeniden imzalamaya) çalışan ara sunucular uyumlu değildir çünkü ara sunucunun sertifikası güvenilir değildir.
   > Genellikle saydam proxy 'ler Azure Data Box Gateway iyi çalışır.

4. Seçim Sol bölmede **zaman ayarları**' nı seçin ve ardından cihazınız için saat dilimini ve birincil ve ikincil NTP sunucularını yapılandırın. 

    Cihazınızın bulut hizmeti sağlayıcılarınız ile kimlik doğrulaması yapabilmesi için zaman eşitlemesini gerektiğinden NTP sunucuları gereklidir.
    
    ![Yerel Web Kullanıcı arabirimi "zaman ayarları" sayfası](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    **Zaman ayarları** sayfasında, aşağıdakileri yapın:
    
    1. **Saat dilimi** açılan listesinde, cihazın dağıtıldığı coğrafi konuma karşılık gelen saat dilimini seçin.
        Cihazınızın varsayılan saat dilimi PST ' dir. Cihazınız zamanlanan tüm işlemler için bu saat dilimini kullanır.

    2. Cihazınız için bir **BIRINCIL NTP sunucusu** belirtin veya varsayılan değerini kabul edin `time.windows.com` .   
        Ağınızın, NTP trafiğini veri merkezinizden İnternete geçirilmesine izin vermesini sağlayın.

    3. İsteğe bağlı olarak, **IKINCIL NTP sunucusu** kutusuna cihazınız için bir ikincil sunucu girin.

    4. Yapılandırılan saat ayarlarını doğrulamak ve uygulamak için **Uygula**' yı seçin.

6. Sol bölmede, **bulut ayarları**' nı seçin ve ardından cihazınızı Azure Portal Data Box Gateway hizmetiyle etkinleştirin.
    
    1. **Etkinleştirme anahtarı** kutusunda, Data Box Gateway için [etkinleştirme anahtarını al](data-box-gateway-deploy-prep.md#get-the-activation-key) bölümünde aldığınız **etkinleştirme anahtarını** girin.

    2. **Etkinleştir**' i seçin.
       
         ![Yerel Web Kullanıcı arabirimi "bulut ayarları" sayfası](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. Cihaz etkinleştirilir ve varsa kritik güncelleştirmeler otomatik olarak uygulanır. Bu etkiye yönelik bir bildirim görürsünüz. Azure portal aracılığıyla güncelleştirme ilerlemesini izleyin.

        ![Yerel Web Kullanıcı arabirimi "bulut ayarları" sayfa 2](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **İletişim kutusunda ayrıca güvenli bir konuma kopyalamanız ve kaydetmeniz gereken bir kurtarma anahtarı bulunur. Bu anahtar, cihazın önyüklenebildiği olaydaki verilerinizi kurtarmak için kullanılır.**


    4. Güncelleştirmenin başarıyla tamamlanabilmesi için birkaç dakika beklemeniz gerekebilir. Güncelleştirme tamamlandıktan sonra cihazda oturum açın. **Bulut ayarları** sayfası, cihazın başarıyla etkinleştirildiğini belirtecek şekilde güncelleştirilir.

        ![Yerel Web Kullanıcı arabirimi "bulut ayarları" sayfası güncelleştirildi](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

Cihaz kurulumu tamamlanmıştır. Artık cihazınıza paylaşımlar ekleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Sanal cihaza Bağlan
> * Sanal cihazı ayarlama ve etkinleştirme

Data Box Gateway verileri nasıl aktaracağınızı öğrenmek için bkz.:

> [!div class="nextstepaction"]
> [Data Box Gateway verileri aktarın](./data-box-gateway-deploy-add-shares.md).
