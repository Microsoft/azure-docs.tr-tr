---
title: StorSimple Sanal dizisine güncelleştirmeleri yükler | Microsoft Docs
description: Azure portal ve sık düzeltme yöntemi kullanılarak 0,4 güncelleştirmesini uygulamak için StorSimple Sanal dizisi Web Kullanıcı arabirimi 'nin nasıl kullanılacağını açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/07/2017
ms.author: alkohli
ms.openlocfilehash: cc458f91a9b67ddd54e706575d29b8fc8618a2f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94534305"
---
# <a name="install-update-04-on-your-storsimple-virtual-array"></a>StorSimple Sanal diziniz üzerinde 0,4 güncelleştirmesini yükler

## <a name="overview"></a>Genel Bakış

Bu makalede, yerel Web Kullanıcı arabirimi aracılığıyla ve Azure portal aracılığıyla StorSimple Sanal dizinize güncelleştirme 0,4 ' ü yüklemek için gereken adımlar açıklanmaktadır. StorSimple Sanal dizinizi güncel tutmak için yazılım güncelleştirmelerini veya düzeltmeleri uygulamanız gerekir. 

Güncelleştirme veya düzeltme yüklendiğinde cihazınızın yeniden başlatıldığını aklınızda bulundurun. StorSimple Sanal dizisi tek düğümlü bir cihaz olduğu için, devam eden g/ç işlemi bozulur ve cihazınız kesinti yaşar. 

Bir güncelleştirmeyi uygulamadan önce, önce konakta ve sonra da cihazda bulunan birimleri veya paylaşımları çevrimdışına almanız önerilir. Bu, veri bozulması olasılığını en aza indirir.

> [!IMPORTANT]
> Güncelleştirme 0,1 veya GA yazılım sürümlerini çalıştırıyorsanız, güncelleştirme 0,3 ' i yüklemek için yerel Web Kullanıcı arabirimi aracılığıyla düzeltme yöntemini kullanmanız gerekir. Güncelleştirme 0,2 veya sonraki bir sürümü çalıştırıyorsanız, Azure portal aracılığıyla güncelleştirmeleri yüklemenizi öneririz.
 

## <a name="use-the-local-web-ui"></a>Yerel Web Kullanıcı arabirimini kullanma

Yerel Web Kullanıcı arabirimi kullanılırken iki adım vardır:

* Güncelleştirmeyi veya düzeltmeyi indirin
* Güncelleştirmeyi veya düzeltmeyi yükler

### <a name="download-the-update-or-the-hotfix"></a>Güncelleştirmeyi veya düzeltmeyi indirin

Microsoft Update Kataloğu'ndan yazılım güncelleştirmesi indirmek için aşağıdaki adımları uygulayın.

#### <a name="to-download-the-update-or-the-hotfix"></a>Güncelleştirmeyi veya düzeltmeyi indirmek için

1. Internet Explorer 'ı başlatın ve adresine gidin [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

2. Microsoft Update Kataloğu’nu bu bilgisayarda ilk kez kullanıyorsanız, sorulduğunda **Yükle**’ye tıklayarak Microsoft Update Kataloğu eklentisini yükleyin.

3. Microsoft Update kataloğunun arama kutusuna, indirmek istediğiniz düzeltmenin Bilgi Bankası (KB) numarasını girin. Güncelleştirme 0,4 için **3216577** girin ve ardından **Ara**' ya tıklayın.
   
    Düzeltme listesi (örneğin, **StorSimple Sanal dizisi güncelleştirme 0,4**) görüntülenir.
   
    ![Katalogda arama](./media/storsimple-virtual-array-install-update-04/download1.png)

4. **Ekle**'ye tıklayın. Güncelleştirme sepete eklenir.

5. **Sepeti Görüntüle**’ye tıklayın.

6. **İndir**’e tıklayın. İndirilen öğelerin görünmesini istediğiniz yerel konumu belirtin veya **Gözat** seçeneğiyle konumu bulun. Güncelleştirmeler belirtilen konuma indirilir ve güncelleştirme ile aynı adı taşıyan alt klasöre yerleştirilir. Klasör, cihazdan erişilebilen bir ağ paylaşımına da kopyalanabilir.

7. Kopyalanmış klasörü açın, tek başına bir Microsoft Update paket dosyası görmeniz gerekir `WindowsTH-KB3011067-x64` . Bu dosya, güncelleştirmeyi veya düzeltmeyi yüklemek için kullanılır.

### <a name="install-the-update-or-the-hotfix"></a>Güncelleştirmeyi veya düzeltmeyi yükler

Güncelleştirme veya düzeltme yüklemesinden önce, güncelleştirmenin veya düzeltmenin, konakta yerel olarak indirildiğinden veya bir ağ paylaşımından erişilebilir olduğundan emin olun. 

Bu yöntemi, GA çalıştıran bir cihaza güncelleştirme yüklemek veya 0,1 yazılım sürümünü güncelleştirmek için kullanın. Bu yordamın tamamlanmasını 2 dakikadan kısa sürer. Güncelleştirmeyi veya düzeltmeyi yüklemek için aşağıdaki adımları gerçekleştirin.

#### <a name="to-install-the-update-or-the-hotfix"></a>Güncelleştirmeyi veya düzeltmeyi yüklemek için

1. Yerel Web Kullanıcı arabiriminde **bakım**  >  **yazılım güncelleştirmesi**' ne gidin.
   
    ![Ekran görüntüsü, bakım menüsündeki yazılım güncelleştirmesinin seçili olduğunu gösterir.](./media/storsimple-virtual-array-install-update/update1m.png)

2. **Güncelleştirme dosyası yolu**' nda, güncelleştirme veya düzeltme için dosya adını girin. Bir ağ paylaşımında yer alıyorsa güncelleştirme veya düzeltme yükleme dosyasına da gidebilirsiniz. **Uygula**’ya tıklayın.
   
    ![Ekran görüntüsü, yazılım güncelleştirme sayfasındaki dosya yolunu Güncelleştir metin kutusunu gösterir.](./media/storsimple-virtual-array-install-update/update2m.png)

3. Bir uyarı görüntülenir. Bu tek düğümlü bir cihaz olduğundan, güncelleştirme uygulandıktan sonra cihaz yeniden başlatılır ve kapalı kalma süresi vardır. Onay simgesine tıklayın.
   
   ![Ekran görüntüsü, kapalı kalma süresinin iletişim kutusu uyarısını gösterir.](./media/storsimple-virtual-array-install-update/update3m.png)

4. Güncelleştirme başlar. Cihaz başarıyla güncelleştirildikten sonra yeniden başlatılır. Yerel Kullanıcı arabirimine bu süre içinde erişilemiyor.
   
    ![Ekran görüntüsü güncelleştirme için bir başarı iletisi gösterir.](./media/storsimple-virtual-array-install-update/update5m.png)

5. Yeniden başlatma işlemi tamamlandıktan sonra **oturum açma** sayfasına yönlendirilirsiniz. Cihaz yazılımının güncelleştirildiğini doğrulamak için, yerel Web Kullanıcı arabiriminde **bakım**  >  **yazılım güncelleştirmesi**' ne gidin. Güncelleştirme 0,4 için, görüntülenmiş yazılım sürümü **10.0.0.0.0.10289.0** olmalıdır.
   
   > [!NOTE]
   > Yazılım sürümlerini yerel Web Kullanıcı arabiriminde ve Azure portal biraz farklı bir şekilde raporlarız. Örneğin, yerel Web Kullanıcı arabirimi raporları **10.0.0.0.0.10289** ve aynı sürüm için **10.0.10289.0** raporlar Azure Portal.
   
    ![Ekran görüntüsü, geçerli yazılım sürümüne sahip yazılım güncelleştirme sayfasını gösterir.](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Azure portalını kullanma

Güncelleştirme 0,2 ve sonraki bir sürümü çalıştırıyorsanız, Azure portal aracılığıyla güncelleştirmeleri yüklemenizi öneririz. Portal yordamı, kullanıcının güncelleştirmeleri taramasını, indirmesini ve yüklemesini gerektirir. Bu yordamın tamamlanmasıyla yaklaşık 7 dakika sürer. Güncelleştirmeyi veya düzeltmeyi yüklemek için aşağıdaki adımları gerçekleştirin.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Yükleme tamamlandıktan sonra (iş durumu %100 ' de belirtildiği gibi) StorSimple Device Manager hizmetinize gidin. **Cihazlar** ' ı seçin ve ardından bu hizmete bağlı cihazların listesinden güncelleştirmek istediğiniz cihazı seçin ve tıklatın. **Ayarlar** dikey penceresinde **Yönet** bölümüne gidin ve **cihaz güncelleştirmeleri**' ni seçin. Görüntülenmiş yazılım sürümü **10.0.10289.0** olmalıdır.


## <a name="next-steps"></a>Sonraki adımlar

[StorSimple Sanal dizinizi yönetme](storsimple-ova-web-ui-admin.md)hakkında daha fazla bilgi edinin.

