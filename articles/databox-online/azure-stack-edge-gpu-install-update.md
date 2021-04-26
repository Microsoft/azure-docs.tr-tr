---
title: Azure Stack Edge Pro GPU cihazına güncelleştirme yüklemeyi | Microsoft Docs
description: Azure Stack Edge Pro GPU cihazı için Azure portal ve yerel Web Kullanıcı arabirimi ve cihazdaki Kubernetes kümesi kullanılarak güncelleştirmelerin nasıl uygulanacağını açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/25/2021
ms.author: alkohli
ms.openlocfilehash: ac5ed0e5941c6251d632d029fe4c9f80bbcf12df
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612560"
---
# <a name="update-your-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU 'larınızı güncelleştirin 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Bu makalede, yerel Web Kullanıcı arabirimi aracılığıyla ve Azure portal aracılığıyla GPU ile Azure Stack Edge Pro 'ya güncelleştirme yüklemek için gereken adımlar açıklanmaktadır. Azure Stack Edge Pro cihazınızı ve ilişkili Kubernetes kümesini cihazda güncel tutmak için yazılım güncelleştirmelerini veya düzeltmeleri uygularsınız.

Bu makalede açıklanan yordam, yazılımın farklı bir sürümü kullanılarak gerçekleştirilmiştir, ancak işlem geçerli yazılım sürümü için aynı kalır.

> [!IMPORTANT]
> - Güncelleştirme **2103** geçerli güncelleştirmedir ve şu şekilde karşılık gelir:
>   - Cihaz yazılımı sürümü- **2.2.1540.2890**
>   - Kubernetes Server sürümü- **v 1.17.3**
>   - IoT Edge sürüm: **0.1.0-beta13**
>   - GPU sürücü sürümü: **460.32.03**
>   - CUDA sürümü: **11,2**
>    
>    Bu güncelleştirmedeki yenilikler hakkında daha fazla bilgi için, [sürüm notları](azure-stack-edge-gpu-2103-release-notes.md)' na gidin.
> - 2103 güncelleştirmesini uygulamak için cihazınız 2010 çalıştırıyor olmalıdır. Desteklenen en düşük sürümü çalıştırmıyorsanız şu hatayı görürsünüz: *Bağımlılıklar karşılanmadığından güncelleştirme paketi* yüklenemiyor.
> - Bu güncelleştirme, sırayla iki güncelleştirme uygulamanızı gerektiriyor. Önce cihaz yazılım güncelleştirmelerini ve ardından Kubernetes güncelleştirmelerini uygularsınız.
> - Güncelleştirme veya düzeltme yüklendiğinde cihazınızın yeniden başlatıldığını aklınızda bulundurun. Bu güncelleştirme, cihaz yazılım güncelleştirmelerini ve Kubernetes güncelleştirmelerini içerir. Azure Stack Edge Pro tek düğümlü bir cihaz olduğundan, sürmekte olan g/ç miktarı bozulur ve cihazınız güncelleştirme için 1,5 saate kadar bir kesinti yaşar.

Güncelleştirmeleri cihazınıza yüklemek için önce güncelleştirme sunucusunun konumunu yapılandırmanız gerekir. Güncelleştirme sunucusu yapılandırıldıktan sonra, Azure portal Kullanıcı arabirimi veya yerel Web Kullanıcı arabirimi aracılığıyla güncelleştirmeleri uygulayabilirsiniz.

Bu adımların her biri aşağıdaki bölümlerde açıklanmıştır.

## <a name="configure-update-server"></a>Güncelleştirme sunucusunu yapılandır

1. Yerel Web Kullanıcı arabiriminde, **yapılandırma**  >  **güncelleştirme sunucusu**' na gidin.
   
    ![Güncelleştirmeleri Yapılandır 1](./media/azure-stack-edge-gpu-install-update/configure-update-server-1.png)

2. **Güncelleştirme sunucu türünü seç**' de, açılan listeden Microsoft Update sunucusu (varsayılan) veya Windows Server Update Services seçeneklerinden birini belirleyin.  
   
    Windows Server Update Services 'den güncelleştirme yapıyorsanız, sunucu URI 'sini belirtin. Bu URI 'deki sunucu, güncelleştirmeleri bu sunucuya bağlı tüm cihazlara dağıtır.

    ![Güncelleştirmeleri Yapılandır 2](./media/azure-stack-edge-gpu-install-update/configure-update-server-2.png)
    
    WSUS sunucusu, güncelleştirmeleri bir yönetim konsolu aracılığıyla yönetmek ve dağıtmak için kullanılır. WSUS sunucusu, kuruluştaki diğer WSUS sunucuları için güncelleştirme kaynağı da olabilir. Bir güncelleştirme kaynağı görevi gören WSUS sunucusu yukarı akış sunucusu şeklinde adlandırılır. Bir WSUS uygulamasında, ağınızdaki en az bir WSUS sunucusu kullanılabilir güncelleştirme bilgilerini almak için Microsoft Update bağlanabilmelidir. Yönetici olarak, ağ güvenliğine ve yapılandırmaya göre (diğer WSUS sunucularının kaç tane Microsoft Update doğrudan bağlanmadığını belirleyebilirsiniz.
    
    Daha fazla bilgi için [Windows Server Update Services (WSUS)](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) sayfasına gidin

## <a name="use-the-azure-portal"></a>Azure portalını kullanma

Azure portal aracılığıyla güncelleştirmeleri yüklemenizi öneririz. Cihaz güncelleştirmeler için günde bir kez otomatik olarak tarama yapar. Güncelleştirmeler kullanılabilir olduktan sonra portalda bir bildirim görürsünüz. Bundan sonra güncelleştirmeleri indirebilir ve yükleyebilirsiniz.

> [!NOTE]
> Cihazın sağlıklı olduğundan ve durumun **düzgün çalıştığı** şekilde göründüğünden emin olun! güncelleştirmeleri yüklemeye devam etmeden önce.

1. Cihazınız için güncelleştirmeler kullanılabilir olduğunda bir bildirim görürsünüz. Bildirimi veya üstteki komut çubuğundan **cihazı Güncelleştir**' i seçin. Bu, cihaz yazılım güncelleştirmelerini uygulamanıza olanak tanır.

    ![Güncelleştirmeden sonra yazılım sürümü](./media/azure-stack-edge-gpu-install-update/portal-update-1.png)

2. **Cihaz güncelleştirmeleri** dikey penceresinde, sürüm notlarındaki yeni özelliklerle ilişkili lisans koşullarını gözden geçirdiğinizden emin olun.

    Güncelleştirmeleri **indirip yüklemeyi** veya yalnızca güncelleştirmeleri **indirmeyi** seçebilirsiniz. Daha sonra da bu güncelleştirmeleri yükleyebilirsiniz.

    ![Güncelleştirme 2 ' den sonra yazılım sürümü](./media/azure-stack-edge-gpu-install-update/portal-update-2-a.png)    

    Güncelleştirmeleri indirmek ve yüklemek isterseniz, yükleme tamamlandıktan sonra güncelleştirmelerin otomatik olarak yüklenmesi seçeneğini işaretleyin.

    ![Güncelleştirme 3 ' den sonra yazılım sürümü](./media/azure-stack-edge-gpu-install-update/portal-update-2-b.png)

3. Güncelleştirmelerin indirilmesi başlar. İndirmenin devam ettiğini belirten bir bildirim görürsünüz.

    ![Güncelleştirme 4 ' den sonra yazılım sürümü](./media/azure-stack-edge-gpu-install-update/portal-update-3.png)

    Azure portal bir bildirim başlığı da görüntülenir. Bu, indirme ilerleme durumunu gösterir.

    ![Güncelleştirme 5 ' den sonra yazılım sürümü](./media/azure-stack-edge-gpu-install-update/portal-update-4.png)

    Bu bildirimi seçebilir veya güncelleştirmenin ayrıntılı durumunu görmek için **cihazı Güncelleştir** ' i seçebilirsiniz.

    ![Güncelleştirme 6 ' dan sonra yazılım sürümü](./media/azure-stack-edge-gpu-install-update/portal-update-5.png)


4. İndirme işlemi tamamlandıktan sonra, tamamlanma olduğunu göstermek için bildirim başlığı güncellenir. Güncelleştirmeleri indirip yüklemeyi seçerseniz, yükleme otomatik olarak başlatılır.

    Yalnızca güncelleştirmeleri indirmeyi seçerseniz, **cihaz güncelleştirmeleri** dikey penceresini açmak için bildirimi seçin. **Yükle**'yi seçin.
  
5. Yüklemenin devam ettiğini belirten bir bildirim görürsünüz. Portal ayrıca yüklemenin devam ettiğini göstermek için bir bilgilendirme uyarısı görüntüler. Cihaz çevrimdışı ve bakım modunda.
   
    ![Güncelleştirme 10 ' dan sonra yazılım sürümü](./media/azure-stack-edge-gpu-install-update/portal-update-9.png)

6. Bu 1 düğümlü bir cihaz olduğundan, güncelleştirmeler yüklendikten sonra cihaz yeniden başlatılır. Yeniden başlatma sırasında kritik uyarı, cihaz sinyalinin kaybolduğunu gösterir.

    ![Güncelleştirme 11 ' den sonra yazılım sürümü](./media/azure-stack-edge-gpu-install-update/portal-update-10.png)

    İlgili cihaz olayını görmek için uyarıyı seçin.
    
    ![Güncelleştirme 12 ' den sonra yazılım sürümü](./media/azure-stack-edge-gpu-install-update/portal-update-11.png)

7. Yeniden başlatmadan sonra cihaz yazılımının güncelleştirilmesi tamamlanır. Güncelleştirme tamamlandıktan sonra, yerel Web kullanıcı arabiriminden cihaz yazılımının güncelleştirildiğini doğrulayabilirsiniz. Kubernetes yazılım sürümü güncelleştirilmemiş.

    ![Güncelleştirme 13 ' den sonra yazılım sürümü](./media/azure-stack-edge-gpu-install-update/portal-update-12.png)

8. Cihaz güncelleştirmelerinin kullanılabildiğini belirten bir bildirim başlığı görürsünüz. Cihazınızda Kubernetes yazılımını güncelleştirmeye başlamak için bu başlığı seçin. 

    ![Güncelleştirme 13A 'dan sonra yazılım sürümü](./media/azure-stack-edge-gpu-install-update/portal-update-13.png) 


    ![Güncelleştirme 14 ' den sonra yazılım sürümü](./media/azure-stack-edge-gpu-install-update/portal-update-14-a.png) 

    En üstteki komut çubuğundan **cihazı Güncelleştir** ' i seçerseniz, güncelleştirmelerin ilerlemesini görebilirsiniz.  

    ![Güncelleştirme 15 ' den sonra yazılım sürümü](./media/azure-stack-edge-gpu-install-update/portal-update-14-b.png) 


8. Cihazınıza cihaz durumu güncelleştirmeleri, güncelleştirmeler yüklendikten sonra **sorunsuz çalışıyor** . 

    ![Güncelleştirme 16 ' dan sonra yazılım sürümü](./media/azure-stack-edge-gpu-install-update/portal-update-15.png)

    Yerel Web Kullanıcı arabirimine gidin ve ardından **yazılım güncelleştirme** sayfasına gidin. Kubernetes güncelleştirmesinin başarıyla yüklendiğini ve yazılım sürümünün bu durumu gösterdiğinden emin olun.

    ![Güncelleştirme 17 ' den sonra yazılım sürümü](./media/azure-stack-edge-gpu-install-update/portal-update-16.png)


Cihaz yazılımı ve Kubernetes güncelleştirmeleri başarıyla yüklendikten sonra, başlık bildirimi kaybolur. Cihazınız artık cihaz yazılımının en son sürümü ve Kubernetes sürümüdür.


## <a name="use-the-local-web-ui"></a>Yerel Web Kullanıcı arabirimini kullanma

Yerel Web Kullanıcı arabirimi kullanılırken iki adım vardır:

* Güncelleştirmeyi veya düzeltmeyi indirin
* Güncelleştirmeyi veya düzeltmeyi yükler

Bu adımların her biri, aşağıdaki bölümlerde ayrıntılı olarak açıklanmıştır.


### <a name="download-the-update-or-the-hotfix"></a>Güncelleştirmeyi veya düzeltmeyi indirin

Güncelleştirmeyi indirmek için aşağıdaki adımları gerçekleştirin. Güncelleştirmeyi Microsoft tarafından sağlanan konumdan veya Microsoft Update kataloğundan indirebilirsiniz.


Microsoft Update kataloğundan güncelleştirmeyi indirmek için aşağıdaki adımları uygulayın.

1. Tarayıcıyı başlatın ve adresine gidin [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

    ![Katalogda arama](./media/azure-stack-edge-gpu-install-update/download-update-1.png)

2. Microsoft Update kataloğunun arama kutusuna, indirmek istediğiniz güncelleştirme için düzeltme veya koşulların Bilgi Bankası (KB) numarasını girin. Örneğin, **Azure Stack Edge Pro** yazın ve ardından **Ara**' ya tıklayın.
   
    Güncelleştirme listesi **Azure Stack Edge güncelleştirme 2103** olarak görüntülenir.
   
    <!--![Search catalog 2](./media/azure-stack-edge-gpu-install-update/download-update-2-b.png)-->

4. **İndir**'i seçin. İndirilecek iki paket vardır: KB 4613486 ve KB 46134867, sırasıyla cihaz yazılım güncelleştirmelerine (*SoftwareUpdatePackage.exe*) ve Kubernetes güncelleştirmelerine (*Kubernetes_Package.exe*) karşılık gelir. Paketleri yerel sistemdeki bir klasöre indirin. Ayrıca, klasörü cihazdan erişilebilen bir ağ paylaşımında da kopyalayabilirsiniz.

### <a name="install-the-update-or-the-hotfix"></a>Güncelleştirmeyi veya düzeltmeyi yükler

Güncelleştirme veya düzeltme yüklemesinden önce şunları yaptığınızdan emin olun:

 - Güncelleştirme veya düzeltme, ana bilgisayarınızda yerel olarak indirilir veya bir ağ paylaşımından erişilebilir.
 - Yerel Web Kullanıcı arabiriminin **genel bakış** sayfasında gösterildiği gibi cihazınızın durumu sağlıklı olur.

   ![cihaz güncelleştirme](./media/azure-stack-edge-gpu-install-update/local-ui-update-1.png)

Bu yordamın tamamlandığı 20 dakika sürer. Güncelleştirmeyi veya düzeltmeyi yüklemek için aşağıdaki adımları gerçekleştirin.

1. Yerel Web Kullanıcı arabiriminde **bakım**  >  **yazılım güncelleştirmesi**' ne gidin. Çalıştırdığınız yazılım sürümünü bir yere unutmayın. 
   
   ![cihaz güncelleştirme 2](./media/azure-stack-edge-gpu-install-update/local-ui-update-2.png)

2. Güncelleştirme dosyasının yolunu belirtin. Ayrıca, bir ağ paylaşımında yer alıyorsa güncelleştirme yükleme dosyasına da gidebilirsiniz. *SoftwareUpdatePackage.exe* sonekine sahip yazılım güncelleştirme dosyasını seçin.

   ![cihaz güncelleştirme 3](./media/azure-stack-edge-gpu-install-update/local-ui-update-3-a.png)

3. **Uygula**’yı seçin.

   ![güncelleştirme aygıtı 4](./media/azure-stack-edge-gpu-install-update/local-ui-update-4.png)

4. Onaylamanız istendiğinde, devam etmek için **Evet** ' i seçin. Cihaz tek düğümlü bir cihaz olduğundan, güncelleştirme uygulandıktan sonra cihaz yeniden başlatılır ve kapalı kalma süresi vardır. 
   
   ![güncelleştirme aygıtı 5](./media/azure-stack-edge-gpu-install-update/local-ui-update-5.png)

5. Güncelleştirme başlar. Cihaz başarıyla güncelleştirildikten sonra yeniden başlatılır. Yerel Kullanıcı arabirimine bu süre içinde erişilemiyor.
   
6. Yeniden başlatma işlemi tamamlandıktan sonra **oturum açma** sayfasına yönlendirilirsiniz. Cihaz yazılımının güncelleştirildiğini doğrulamak için, yerel Web Kullanıcı arabiriminde **bakım**  >  **yazılım güncelleştirmesi**' ne gidin. Geçerli yayın için, görüntülenmiş yazılım sürümü **Azure Stack Edge 2103** olmalıdır. 

   <!--![update device 6](./media/azure-stack-edge-gpu-install-update/local-ui-update-6.png)-->

7. Şimdi Kubernetes yazılım sürümünü güncelleştecaksınız. Yukarıdaki adımları tekrarlayın. *Kubernetes_Package.exe* sonekine sahip Kubernetes güncelleştirme dosyasına bir yol sağlayın.  

   <!--![update device](./media/azure-stack-edge-gpu-install-update/local-ui-update-7.png)-->

8. **Güncelleştirme Uygula**' yı seçin.

   ![cihaz güncelleştirme 7](./media/azure-stack-edge-gpu-install-update/local-ui-update-8.png)

9. Onaylamanız istendiğinde, devam etmek için **Evet** ' i seçin.

10. Kubernetes güncelleştirmesi başarıyla yüklendikten sonra, **bakım**  >  **yazılımı güncelleştirmesinde**, görüntülenmiş yazılımlar ' da bir değişiklik yoktur.


## <a name="next-steps"></a>Sonraki adımlar

[Azure Stack Edge Pro 'yu yönetme](azure-stack-edge-manage-access-power-connectivity-mode.md)hakkında daha fazla bilgi edinin.