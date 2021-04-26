---
title: Azure portal 'de Azure Stack Edge Pro R cihazının ağ ayarlarını yapılandırma öğreticisi | Microsoft Docs
description: Azure Stack Edge Pro R dağıtma öğreticisi, fiziksel cihazınız için ağ, bilgi işlem ağı ve Web proxy ayarlarını yapılandırmanızı sağlar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: alkohli
ms.openlocfilehash: a1f3966c8794b50f6ec369f1ea86905c4d8aaf3f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059947"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-pro-r"></a>Öğretici: Azure Stack Edge Pro R için ağı yapılandırma

Bu öğreticide, yerel Web Kullanıcı arabirimini kullanarak Azure Stack Edge Pro R cihazınız için ağın nasıl yapılandırılacağı açıklanmaktadır.

Bağlantı işleminin tamamlanması 20 dakika sürebilir.

Bu öğreticide şunları öğrenirsiniz:

> [!div class="checklist"]
>
> * Önkoşullar
> * Ağı yapılandırma
> * İşlem ağını etkinleştir
> * Web proxy yapılandırma


## <a name="prerequisites"></a>Önkoşullar

Azure Stack Edge Pro R cihazınızı yapılandırmadan ve ayarlamadan önce şunları yaptığınızdan emin olun:

* Fiziksel cihazı [yükleme Azure Stack Edge Pro R](azure-stack-edge-gpu-deploy-install.md)' de ayrıntılı olarak yüklediniz.
* [Azure Stack Edge Pro R 'ye bağlanma](azure-stack-edge-gpu-deploy-connect.md) bölümünde açıklandığı gibi cihazın yerel Web Kullanıcı arabirimine bağlandınız


## <a name="configure-network"></a>Ağı yapılandırma

**Başlarken** sayfasında, fiziksel cihazı Azure Stack Edge hizmetine göre yapılandırmak ve kaydetmek için gereken çeşitli ayarlar görüntülenir. 

Cihazınızın ağını yapılandırmak için bu adımları izleyin.

1. Cihazınızın yerel Web Kullanıcı arabiriminde **Başlarken** sayfasına gidin. 

2. **Ağ kutucuğunda** , **ağ** sayfasına gitmek için **Yapılandır** ' ı seçin. 
    
    <!--![Local web UI "Network settings" tile](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)-->

    Fiziksel cihazınızda dört ağ arabirimi vardır. PORT 1 ve PORT 2, 1 Gb/sn'lik ağ arabirimleridir. Bağlantı noktası 3 ve bağlantı noktası 4, 10/25 Gbps ağ arayüzlerdir. BAĞLANTı noktası 1 otomatik olarak yalnızca yönetim bağlantı noktası olarak yapılandırılır ve bağlantı noktası 4 ' e kadar tüm veri bağlantı noktalardır. **Ağ** sayfası aşağıda gösterildiği gibidir.
    
    ![Yerel Web Kullanıcı arabirimi "ağ ayarları" sayfası](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-2.png)

   
3. Ağ ayarlarını değiştirmek için bir bağlantı noktası seçin ve sağ bölmede, IP adresi, alt ağ, ağ geçidi, birincil DNS ve ikincil DNS ' yi değiştirin. 

    - Bağlantı noktası 1 ' i seçerseniz, bunun statik olarak önceden yapılandırılmış olduğunu görebilirsiniz. 

        ![Yerel Web Kullanıcı arabirimi "bağlantı noktası 1 ağ ayarları"](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-3.png)

    - Bağlantı noktası 2, bağlantı noktası 3 veya bağlantı noktası 4 ' ü seçerseniz, bu bağlantı noktalarının tümü varsayılan olarak DHCP olarak yapılandırılır.

        ![Yerel Web Kullanıcı arabirimi "bağlantı noktası 3 ağ ayarları"](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-4.png)

    Ağ ayarlarını yapılandırırken şunları aklınızda bulundurun:

   * Ortamınızda DHCP etkinse, ağ arabirimleri otomatik olarak yapılandırılır. IP adresi, alt ağ, ağ geçidi ve DNS otomatik olarak atanır.
   * DHCP etkinleştirilmemişse, gerekirse statik IP 'Ler atayabilirsiniz.
   * Ağ arabiriminizi IPv4 olarak yapılandırabilirsiniz.
   * Ağ arabirimi kartı (NIC) grubu oluşturma veya bağlantı toplama, Azure Stack Edge ile desteklenmez.
   * Herhangi bir bağlantı noktasının seri numarası, düğüm seri numarasına karşılık gelir.
    <!--* On the 25-Gbps interfaces, you can set the RDMA (Remote Direct Access Memory) mode to iWarp or RoCE (RDMA over Converged Ethernet). Where low latencies are the primary requirement and scalability is not a concern, use RoCE. When latency is a key requirement, but ease-of-use and scalability are also high priorities, iWARP is the best candidate.-->
    Cihaz ağı yapılandırıldıktan sonra, sayfa aşağıda gösterildiği gibi güncellenir.

    ![Yerel Web Kullanıcı arabirimi "ağ ayarları" sayfa 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-2a.png)<!--change-->


     >[!NOTE]
     >
     > * Cihaza bağlanmak için başka bir IP adresiniz yoksa, ağ arabiriminin yerel IP adresini statikten DCHP’ye değiştirmeniz önerilmez. Tek bir ağ arabirimi kullanıyorsanız ve DHCP’ye geçiyorsanız, DHCP adresini belirlemenin hiçbir yolu yoktur. Bir DHCP adresine geçiş yapmak istiyorsanız, cihaz hizmetle etkinleştirilinceye kadar bekleyin ve ardından değiştirin. Daha sonra, hizmetinize yönelik Azure portal **cihaz özelliklerindeki** tüm bağdaştırıcıların IP 'lerini görüntüleyebilirsiniz.


    Ağ ayarlarını yapılandırdıktan ve uyguladıktan sonra, Ileri ' yi seçin. işlem ağını yapılandırmak için **işlem** .

## <a name="enable-compute-network"></a>İşlem ağını etkinleştir

İşlem ağını etkinleştirmek ve yapılandırmak için bu adımları izleyin. 

1. **İşlem** sayfasında, işlem için etkinleştirmek istediğiniz bir ağ arabirimi seçin. 

    ![Yerel Kullanıcı arabirimi 2 ' de işlem sayfası](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-2.png)

1. **Ağ ayarları** Iletişim kutusunda **Etkinleştir**' i seçin. İşlem ' ı etkinleştirdiğinizde, cihazınızda bu ağ arabirimindeki bir sanal anahtar oluşturulur. Sanal anahtar, cihazdaki işlem altyapısı için kullanılır. 
    
1. **Kubernetes düğüm IP 'leri** atayın. Bu statik IP adresleri, işlem sanal makinesi içindir.  

    *N* düğümlü bir cihaz için, başlangıç ve bitiş IP adreslerini kullanan işlem VM 'si için en az *n + 1* IPv4 adresi (veya daha fazla) bulunur. Verilen Azure Stack Edge 1 düğümlü bir cihazdır, en az 2 bitişik IPv4 adresi sağlanır. Bu IP adresleri, işlem ve sanal anahtarın oluşturulduğu ağda olmalıdır.

    > [!IMPORTANT]
    > Azure Stack Edge üzerinde Kubernetes, pod için 172.27.0.0/16 alt ağını ve hizmet için 172.28.0.0/16 alt ağını kullanır. Bunların ağınızda kullanımda olmadığından emin olun. Bu alt ağlar ağınızda zaten kullanılıyorsa, bu alt ağları, `Set-HcsKubeClusterNetworkInfo` cihazın PowerShell arabiriminden cmdlet 'ini çalıştırarak değiştirebilirsiniz. Daha fazla bilgi için bkz. [Kubernetes Pod ve hizmet alt ağlarını değiştirme](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets).


1. **Kubernetes dış hizmet IP 'leri** atayın. Bunlar ayrıca Yük Dengeleme IP adresleridir. Bu bitişik IP adresleri, Kubernetes kümesi dışında kullanıma sunmak istediğiniz hizmetlere yöneliktir ve sunulan hizmet sayısına bağlı olarak statik IP aralığını belirlersiniz. 
    
    > [!IMPORTANT]
    > İşlem modüllerine erişmek için Azure Stack Edge Pro R hub hizmeti için en az 1 IP adresi belirtmenizi önemle öneririz. Daha sonra isteğe bağlı olarak, küme dışından erişilmesi gereken diğer hizmetler/IoT Edge modülleri (hizmet/modül başına 1) için ek IP adresleri belirtebilirsiniz. Hizmet IP adresleri daha sonra güncelleştirilebilen olabilir. 
    
1. **Uygula**’yı seçin.

    ![Yerel Kullanıcı arabirimi 3 ' te işlem sayfası](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. Yapılandırmanın uygulanması birkaç dakika sürer ve tarayıcıyı yenilemeniz gerekebilir. Belirtilen bağlantı noktasının işlem için etkinleştirildiğini görebilirsiniz. 
 
    ![Yerel Kullanıcı arabirimi 4 ' te işlem sayfası](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Ileri ' yi seçin: Web proxy 'yi yapılandırmak için **Web proxy** .  

  
## <a name="configure-web-proxy"></a>Web proxy yapılandırma

Bu, isteğe bağlı bir yapılandırmadır.

> [!IMPORTANT]
> * İşlem ve Azure Stack Edge Pro R cihazınızda IoT Edge modülü kullanıyorsanız, Web proxy kimlik doğrulamasını **hiçbiri** olarak ayarlamanızı öneririz. NTLM desteklenmez.
>* Proxy-otomatik yapılandırma (PAC) dosyaları desteklenmez. PAC dosyası, Web tarayıcılarının ve diğer Kullanıcı aracılarının belirli bir URL 'YI getirmek için uygun proxy sunucusunu (erişim yöntemi) otomatik olarak nasıl seçebileceğini tanımlar. Ara sunucunun sertifikası güvenilir olmadığından, tüm trafiği kesmeye ve okumaya çalışır (sonra her şeyi kendi sertifikalarıyla yeniden imzala) uyumlu değildir. Genellikle saydam proxy 'ler Azure Stack Edge Pro R ile iyi çalışır. saydam olmayan Web proxy 'leri desteklenmez.


1. **Web proxy ayarları** sayfasında, aşağıdaki adımları uygulayın:

    1. **Web proxy URL 'si** kutusuna URL 'yi şu biçimde girin: `http://host-IP address or FQDN:Port number` . HTTPS URL'leri desteklenmez.

    2. **Kimlik Doğrulaması**'nın altında **Yok** veya **NTLM**'yi seçin. İşlem ve Azure Stack Edge Pro R cihazınızda IoT Edge modülü kullanıyorsanız, Web proxy kimlik doğrulamasını **none** olarak ayarlamanızı öneririz. **NTLM** desteklenmiyor.

    3. Kimlik doğrulaması kullanıyorsanız, bir Kullanıcı adı ve parola girin.

    4. Yapılandırılmış Web proxy ayarlarını doğrulamak ve uygulamak için **Uygula**' yı seçin.
    
   ![Yerel Web Kullanıcı arabirimi "Web proxy ayarları" sayfa 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/web-proxy-2.png)

2. Ayarlar uygulandıktan sonra **İleri: cihaz**' ı seçin.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide hakkında bilgi edindiniz:

> [!div class="checklist"]
> * Önkoşullar
> * Ağı yapılandırma
> * İşlem ağını etkinleştir
> * Web proxy yapılandırma


Azure Stack Edge Pro R cihazınızı ayarlamayı öğrenmek için bkz.:

> [!div class="nextstepaction"]
> [Cihaz ayarlarını yapılandırma](./azure-stack-edge-pro-r-deploy-set-up-device-update-time.md)
