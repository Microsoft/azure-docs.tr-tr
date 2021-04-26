---
title: Azure portal 'de Azure Stack Edge Mini R cihazının ağ ayarlarını yapılandırma öğreticisi
description: Azure Stack Edge Mini R dağıtımı öğreticisi, fiziksel cihazınız için ağ, bilgi işlem ağı ve Web proxy ayarlarını yapılandırmanızı sağlar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: alkohli
ms.openlocfilehash: 34a11679626653afd04b0cd17c77188cbc995308
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061732"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-mini-r"></a>Öğretici: Azure Stack Edge Mini R için ağı yapılandırma

Bu öğreticide, yerel Web Kullanıcı arabirimini kullanarak Azure Stack Edge Mini R cihazınız için ağın birlikte bulunan GPU ile nasıl yapılandırılacağı açıklanmaktadır.

Bağlantı işleminin tamamlanması 20 dakika sürebilir.

Bu öğreticide şunları öğrenirsiniz:

> [!div class="checklist"]
>
> * Önkoşullar
> * Ağı yapılandırma
> * İşlem ağını etkinleştir
> * Web proxy yapılandırma


## <a name="prerequisites"></a>Önkoşullar

Azure Stack Edge Mini R cihazınızı yapılandırmadan ve ayarlamadan önce şunları yaptığınızdan emin olun:

* Fiziksel cihazı [yükleme Azure Stack Edge Mini R](azure-stack-edge-gpu-deploy-install.md)' de ayrıntılı olarak yüklediniz.
* [Azure Stack Edge Mini R 'ye bağlanma](azure-stack-edge-mini-r-deploy-connect.md) bölümünde açıklandığı gibi cihazın yerel Web Kullanıcı arabirimine bağlandınız


## <a name="configure-network"></a>Ağı yapılandırma

**Başlarken** sayfasında, fiziksel cihazı Azure Stack Edge hizmetine göre yapılandırmak ve kaydetmek için gereken çeşitli ayarlar görüntülenir. 

Cihazınızın ağını yapılandırmak için bu adımları izleyin.

1. Cihazınızın yerel Web Kullanıcı arabiriminde **Başlarken** sayfasına gidin. 

2. Sıfır gün güncelleştirme gerekiyorsa bunu, kablolu bağlantıyla bir veri bağlantı noktası yapılandırarak yapabilirsiniz. Bu cihaz için kablolu bağlantı ayarlama hakkında daha fazla yönerge için bkz. [cihazınızı kablolu](azure-stack-edge-mini-r-deploy-install.md#cable-the-device)olarak kullanma. Güncelleştirme bittikten sonra kablolu bağlantıyı kaldırabilirsiniz.

3. Wi-Fi ve imzalama zinciri için sertifika oluşturun. Hem imzalama zinciri hem de Wi-Fi sertifikaları bir *. cer* dosya uzantısıyla der biçiminde olmalıdır. Yönergeler için bkz. [sertifika oluşturma](azure-stack-edge-gpu-manage-certificates.md).

4. Yerel Web Kullanıcı arabiriminde, **kullanmaya** başlayın ' a gidin. **Güvenlik** kutucuğunda **Sertifikalar** ' ı seçin ve ardından **Yapılandır**' ı seçin. 

    [![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfası](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/get-started-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/get-started-1.png#lightbox)

    1. **+ Sertifika ekle**' yi seçin. 
    
        [![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-1.png#lightbox)

    2. İmzalama zincirini karşıya yükleyin ve **Uygula**' yı seçin.

        ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-2.png)

    3. Yordamı Wi-Fi sertifikayla tekrarlayın. 

        ![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-4.png)

    4. Yeni sertifikaların **Sertifikalar** sayfasında gösterilmesi gerekir. 
    
        [![Yerel Web Kullanıcı arabirimi "Sertifikalar" sayfa 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-5.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-5.png#lightbox)

    5. **Kullanmaya** başlamak için geri dönün.

3. **Ağ** kutucuğunda **Yapılandır**' ı seçin.  
    
    Fiziksel cihazınızda beş ağ arabirimi vardır. PORT 1 ve PORT 2, 1 Gb/sn'lik ağ arabirimleridir. Bağlantı noktası 3 ve bağlantı noktası 4 tüm 10 Gbps ağ arabirimlerdir. Beşinci bağlantı noktası Wi-Fi bağlantı noktasıdır. 

    [![Yerel Web Kullanıcı arabirimi "ağ ayarları" sayfa 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)    
    
    Wi-Fi bağlantı noktasını seçin ve bağlantı noktası ayarlarını yapılandırın. 
    
    > [!IMPORTANT]
    > Wi-Fi bağlantı noktası için bir statik IP adresi yapılandırmanız önemle önerilir.  

    ![Yerel Web Kullanıcı arabirimi "ağ ayarları" sayfa 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

    Wi-Fi bağlantı noktası ayarlarını uyguladıktan sonra **ağ** sayfası güncellenir.

    ![Yerel Web Kullanıcı arabirimi "ağ ayarları" sayfa 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)
   
4. **Wi-Fi profili Ekle** ' yi seçin ve Wi-Fi profilinizi karşıya yükleyin. 

    ![Yerel Web Kullanıcı arabirimi "bağlantı noktası WiFi ağ ayarları" 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)
    
    Bir kablosuz ağ profili, bir kablosuz ağa bağlanabilmek için SSID (ağ adı), parola anahtarı ve güvenlik bilgilerini içerir. Ortamınızın Wi-Fi profilini ağ yöneticinizden edinebilirsiniz.

    ![Yerel Web Kullanıcı arabirimi "bağlantı noktası WiFi ağ ayarları" 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

    Profil eklendikten sonra, Wi-Fi profillerinin listesi yeni profili yansıtacak şekilde güncelleştirilir. Profilde **bağlantı durumu** bağlantısı **kesik** olarak gösterilmelidir. 

    ![Yerel Web Kullanıcı arabirimi "bağlantı noktası WiFi ağ ayarları" 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)


5. Kablosuz ağ profili başarıyla yüklendikten sonra bu profile bağlanın. **Wi-Fi Profile Bağlan**' ı seçin. 

    ![Yerel Web Kullanıcı arabirimi "bağlantı noktası Wi-Fi ağ ayarları" 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

6. Önceki adımda eklediğiniz Wi-Fi profilini seçin ve **Uygula**' yı seçin. 

    ![Yerel Web Kullanıcı arabirimi "bağlantı noktası Wi-Fi ağ ayarları" 5](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

    **Bağlantı durumu** **bağlı** olarak güncelleştirilecek. Sinyal gücü, sinyalin kalitesini göstermek için güncelleştirilir. 

    ![Yerel Web Kullanıcı arabirimi "bağlantı noktası Wi-Fi ağ ayarları" 6](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

    > [!NOTE]
    > Büyük miktarlardaki verileri aktarmak için kablosuz ağ yerine kablolu bağlantı kullanmanızı öneririz. 

6. Cihazdaki bağlantı noktası 1 ' i dizüstü bilgisayardan sökün. 

7. Ağ ayarlarını yapılandırırken şunları aklınızda bulundurun:

   - Ortamınızda DHCP etkinse, ağ arabirimleri otomatik olarak yapılandırılır. IP adresi, alt ağ, ağ geçidi ve DNS otomatik olarak atanır.
   - DHCP etkinleştirilmemişse, gerekirse statik IP 'Ler atayabilirsiniz.
   - Ağ arabiriminizi IPv4 olarak yapılandırabilirsiniz.
   - Ağ arabirimi kartı (NIC) grubu oluşturma veya bağlantı toplama, Azure Stack Edge ile desteklenmez.
   - Herhangi bir bağlantı noktasının seri numarası, düğüm seri numarasına karşılık gelir. K serisi bir cihaz için yalnızca bir seri numarası görüntülenir.

     >[!NOTE] 
     > Cihaza bağlanmak için başka bir IP adresiniz yoksa, ağ arabiriminin yerel IP adresini statikten DCHP’ye değiştirmeniz önerilmez. Tek bir ağ arabirimi kullanıyorsanız ve DHCP’ye geçiyorsanız, DHCP adresini belirlemenin hiçbir yolu yoktur. DHCP adresini değiştirmek istiyorsanız cihazın hizmete kaydolmasını bekleyin ve ondan sonra değiştirin. Daha sonra, hizmetinize yönelik Azure portal **cihaz özelliklerindeki** tüm bağdaştırıcıların IP 'lerini görüntüleyebilirsiniz.

Ağ ayarlarını yapılandırdıktan ve uyguladıktan sonra, Ileri ' yi seçin. işlem ağını yapılandırmak için **işlem** .

## <a name="enable-compute-network"></a>İşlem ağını etkinleştir

İşlem ağını etkinleştirmek ve yapılandırmak için bu adımları izleyin. 


1. **İşlem** sayfasında, işlem için etkinleştirmek istediğiniz bir ağ arabirimi seçin. 

    ![Yerel Kullanıcı arabirimi 2 ' de işlem sayfası](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-1.png)

1. **Ağ ayarları** Iletişim kutusunda **Etkinleştir**' i seçin. İşlem ' ı etkinleştirdiğinizde, cihazınızda bu ağ arabirimindeki bir sanal anahtar oluşturulur. Sanal anahtar, cihazdaki işlem altyapısı için kullanılır. 
    
1. **Kubernetes düğüm IP 'leri** atayın. Bu statik IP adresleri, işlem sanal makinesi içindir.  

    *N* düğümlü bir cihaz için, başlangıç ve bitiş IP adreslerini kullanan işlem VM 'si için en az *n + 1* IPv4 adresi (veya daha fazla) bulunur. Verilen Azure Stack Edge 1 düğümlü bir cihazdır, en az 2 bitişik IPv4 adresi sağlanır.

    > [!IMPORTANT]
    > Azure Stack Edge üzerinde Kubernetes, pod için 172.27.0.0/16 alt ağını ve hizmet için 172.28.0.0/16 alt ağını kullanır. Bunların ağınızda kullanımda olmadığından emin olun. Bu alt ağlar ağınızda zaten kullanılıyorsa, bu alt ağları, `Set-HcsKubeClusterNetworkInfo` cihazın PowerShell arabiriminden cmdlet 'ini çalıştırarak değiştirebilirsiniz. Daha fazla bilgi için bkz. [Kubernetes Pod ve hizmet alt ağlarını değiştirme](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets).


1. **Kubernetes dış hizmet IP 'leri** atayın. Bunlar ayrıca Yük Dengeleme IP adresleridir. Bu bitişik IP adresleri, Kubernetes kümesi dışında kullanıma sunmak istediğiniz hizmetlere yöneliktir ve sunulan hizmet sayısına bağlı olarak statik IP aralığını belirtebilirsiniz. 
    
    > [!IMPORTANT]
    > İşlem modüllerine erişmek için Azure Stack Edge Mini R hub hizmeti için en az 1 IP adresi belirtmenizi önemle tavsiye ederiz. Daha sonra isteğe bağlı olarak, küme dışından erişilmesi gereken diğer hizmetler/IoT Edge modülleri (hizmet/modül başına 1) için ek IP adresleri belirtebilirsiniz. Hizmet IP adresleri daha sonra güncelleştirilebilen olabilir. 
    
1. **Uygula**’yı seçin.

    ![Yerel Kullanıcı arabirimi 3 ' te işlem sayfası](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. Yapılandırmanın uygulanması birkaç dakika sürer ve tarayıcıyı yenilemeniz gerekebilir. Belirtilen bağlantı noktasının işlem için etkinleştirildiğini görebilirsiniz. 
 
    ![Yerel Kullanıcı arabirimi 4 ' te işlem sayfası](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Ileri ' yi seçin: Web proxy 'yi yapılandırmak için **Web proxy** .  

  
## <a name="configure-web-proxy"></a>Web proxy yapılandırma

Bu, isteğe bağlı bir yapılandırmadır.

> [!IMPORTANT]
> * İşlem ve Azure Stack Edge Mini R cihazınızda IoT Edge modülü kullanıyorsanız, Web proxy kimlik doğrulamasını **hiçbiri** olarak ayarlamanızı öneririz. NTLM desteklenmez.
>* Proxy-otomatik yapılandırma (PAC) dosyaları desteklenmez. PAC dosyası, Web tarayıcılarının ve diğer Kullanıcı aracılarının belirli bir URL 'YI getirmek için uygun proxy sunucusunu (erişim yöntemi) otomatik olarak nasıl seçebileceğini tanımlar. Ara sunucunun sertifikası güvenilir olmadığından, tüm trafiği kesmeye ve okumaya çalışır (sonra her şeyi kendi sertifikalarıyla yeniden imzala) uyumlu değildir. Genellikle saydam proxy 'ler Azure Stack Edge Mini R ile iyi çalışır. saydam olmayan Web proxy 'leri desteklenmez.


1. **Web proxy ayarları** sayfasında, aşağıdaki adımları uygulayın:

    1. **Web proxy URL 'si** kutusuna URL 'yi şu biçimde girin: `http://host-IP address or FQDN:Port number` . HTTPS URL'leri desteklenmez.

    2. **Kimlik Doğrulaması**'nın altında **Yok** veya **NTLM**'yi seçin. İşlem ve Azure Stack Edge Mini R cihazınızda IoT Edge modülü kullanıyorsanız, Web proxy kimlik doğrulamasını **none** olarak ayarlamanızı öneririz. **NTLM** desteklenmiyor.

    3. Kimlik doğrulaması kullanıyorsanız, bir Kullanıcı adı ve parola girin.

    4. Yapılandırılmış Web proxy ayarlarını doğrulamak ve uygulamak için **Uygula**' yı seçin.
    
   ![Yerel Web Kullanıcı arabirimi "Web proxy ayarları" sayfası](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/web-proxy-1.png)

2. Ayarlar uygulandıktan sonra **İleri: cihaz**' ı seçin.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide hakkında bilgi edindiniz:

> [!div class="checklist"]
> * Önkoşullar
> * Ağı yapılandırma
> * İşlem ağını etkinleştir
> * Web proxy yapılandırma


Azure Stack Edge Mini R cihazınızı ayarlamayı öğrenmek için bkz.:

> [!div class="nextstepaction"]
> [Cihaz ayarlarını yapılandırma](./azure-stack-edge-mini-r-deploy-set-up-device-update-time.md)
