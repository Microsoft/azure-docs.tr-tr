---
title: StorSimple Sanal dizisine güncelleştirme 1,0 ' ü yükler | Microsoft Docs
description: Azure portal ve sık düzeltme yöntemi kullanılarak 1,0 güncelleştirmesini uygulamak için StorSimple Sanal dizisi Web Kullanıcı arabirimi 'nin nasıl kullanılacağını açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: d05395f97aded0b57780f898e9cba6ddca491f95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005835"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>StorSimple Sanal diziniz üzerinde 1,0 güncelleştirmesini yükler

## <a name="overview"></a>Genel Bakış

Bu makalede, yerel Web Kullanıcı arabirimi aracılığıyla ve Azure portal aracılığıyla StorSimple Sanal dizinize güncelleştirme 1,0 ' ü yüklemek için gereken adımlar açıklanmaktadır.

StorSimple Sanal dizinizi güncel tutmak için yazılım güncelleştirmelerini veya düzeltmeleri uygularsınız. Bir güncelleştirmeyi uygulamadan önce, önce konakta ve sonra da cihazda bulunan birimleri veya paylaşımları çevrimdışına almanız önerilir. Bu, veri bozulması olasılığını en aza indirir. Birimler veya paylaşımlar çevrimdışı olduktan sonra, cihazın el ile yedeklemesini de yapmanız gerekir.

> [!IMPORTANT]
>
> - Güncelleştirme 1,0, cihazınızdaki **10.0.10296.0** yazılım sürümüne karşılık gelir. Bu güncelleştirmedeki yenilikler hakkında daha fazla bilgi için [güncelleştirme 1,0 sürüm notları](storsimple-virtual-array-update-1-release-notes.md)' na gidin.
>
> - Güncelleştirme veya düzeltme yüklendiğinde cihazınızın yeniden başlatıldığını aklınızda bulundurun. StorSimple Sanal dizisi tek düğümlü bir cihaz olduğu için, devam eden g/ç işlemi bozulur ve cihazınız kesinti yaşar.
>
> - Güncelleştirme 1, yalnızca sanal dizi güncelleştirme 0,6 çalıştırıyorsa Azure portal kullanılabilir. Güncelleştirme öncesi 0,6 sürümlerini çalıştıran sanal diziler için, önce 0,6 güncelleştirme 'yi yüklemeli ve ardından güncelleştirme 1 ' i yüklemeniz gerekir.

## <a name="use-the-azure-portal"></a>Azure portalını kullanma

Güncelleştirme 0,2 ve sonraki bir sürümü çalıştırıyorsanız, Azure portal aracılığıyla güncelleştirmeleri yüklemenizi öneririz. Portal yordamı, kullanıcının güncelleştirmeleri taramasını, indirmesini ve yüklemesini gerektirir. Sanal diziniz çalıştığı yazılım sürümüne bağlı olarak, Azure portal aracılığıyla güncelleştirme uygulamak farklıdır.

 - Sanal diziniz güncelleştirme 0,6 çalıştırıyorsa, Azure portal doğrudan cihazınıza güncelleştirme 1 ' i (10.0.10296.0) yüklüyor. Bu yordamın tamamlanmasıyla yaklaşık 7 dakika sürer.
 - Sanal diziniz güncelleştirme 0,6 ' den önceki bir sürümü çalıştırıyorsa, güncelleştirme iki aşamada yapılır. Azure portal önce cihazınıza güncelleştirme 0,6 (10.0.10293.0) yüklenir. Sanal dizi yeniden başlatılır ve Portal, bilgisayarınıza güncelleştirme 1 ' i (10.0.10296.0) yüklüyor. Bu yordamın tamamlandığı 15 dakika sürer.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

Yükleme tamamlandıktan sonra StorSimple Device Manager hizmetinize gidin. **Cihazlar** ' ı seçin ve ardından yeni güncelleştirdiğiniz cihazı seçin ve tıklayın. **> cihaz güncelleştirmelerini yönetmek > ayarlar**' a gidin. Görüntülenmiş yazılım sürümü **10.0.10296.0** olmalıdır.

![Güncelleştirmeden sonra yazılım sürümü](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>Yerel Web Kullanıcı arabirimini kullanma

Yerel Web Kullanıcı arabirimi kullanılırken iki adım vardır:

* Güncelleştirmeyi veya düzeltmeyi indirin
* Güncelleştirmeyi veya düzeltmeyi yükler

> [!IMPORTANT] 
> **Bu güncelleştirmeye yalnızca Güncelleştirme 0,6 (10.0.10293.0) çalıştırıyorsanız devam edin. Önceki bir sürümü çalıştırıyorsanız, önce cihazınıza [güncelleştirme 0,6](storsimple-virtual-array-install-update-06.md) ' ü yükledikten sonra güncelleştirme 1 ' i uygulayın.**

### <a name="download-the-update-or-the-hotfix"></a>Güncelleştirmeyi veya düzeltmeyi indirin

Sanal diziniz güncelleştirme 0,6 çalıştırıyorsa, Microsoft Update kataloğundan güncelleştirme 1 ' i indirmek için aşağıdaki adımları gerçekleştirin.

#### <a name="to-download-the-update-or-the-hotfix"></a>Güncelleştirmeyi veya düzeltmeyi indirmek için

1. Internet Explorer 'ı başlatın ve adresine gidin [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

2. Bu bilgisayarda ilk kez Microsoft Update kataloğunu kullanıyorsanız, Microsoft Update Katalog eklentisini yüklemek isteyip istemediğiniz sorulduğunda **yüklensin** ' e tıklayın.

3. Microsoft Update kataloğunun arama kutusuna, indirmek istediğiniz düzeltmenin Bilgi Bankası (KB) numarasını girin. Güncelleştirme 1,0 için **4047203** girin ve ardından **Ara**' ya tıklayın.
   
    Düzeltme listesi (örneğin, **StorSimple Sanal dizisi güncelleştirme 1,0**) görüntülenir.
   
    ![Katalogda arama](./media/storsimple-virtual-array-install-update-1/download1.png)

4. **İndir**’e tıklayın.

5. İki dosyayı bir klasöre indirin. Ayrıca, klasörü cihazdan erişilebilen bir ağ paylaşımında da kopyalayabilirsiniz.

6. Dosyaların bulunduğu klasörü açın.

    ![Paketteki dosyalar](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    İki dosya görürsünüz:
    -  Tek başına Microsoft Update paket dosyası `WindowsTH-KB3011067-x64` . Bu dosya, cihaz yazılımını güncelleştirmek için kullanılır.
    - Ağustos için toplu güncelleştirmeleri içeren bir dosya `windows8.1-kb4034681-x64` . Bu toplamada nelerin dahil olduğu hakkında daha fazla bilgi için [aylık güvenlik toplamasına](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810)gidin.

### <a name="install-the-update-or-the-hotfix"></a>Güncelleştirmeyi veya düzeltmeyi yükler

Güncelleştirme veya düzeltme yüklemesinden önce şunları yaptığınızdan emin olun:

 - Güncelleştirme veya düzeltme, ana bilgisayarınızda yerel olarak indirilir veya bir ağ paylaşımından erişilebilir.
 - Sanal diziniz güncelleştirme 0,6 ' i çalıştırıyor (10.0.10293.0). Güncelleştirme 0,6 ' den önceki bir sürümü çalıştırıyorsanız, önce [güncelleştirme 0,6](storsimple-virtual-array-install-update-06.md) ' yi yükleyip Güncelleştirme 1 ' i yüklemeniz gerekir.

Bu yordamın tamamlandığı 4 dakika sürer. Güncelleştirmeyi veya düzeltmeyi yüklemek için aşağıdaki adımları gerçekleştirin.

#### <a name="to-install-the-update-or-the-hotfix"></a>Güncelleştirmeyi veya düzeltmeyi yüklemek için

1. Yerel Web Kullanıcı arabiriminde **bakım**  >  **yazılım güncelleştirmesi**' ne gidin. Çalıştırdığınız yazılım sürümünü bir yere unutmayın. **Bu güncelleştirmeye yalnızca Güncelleştirme 0,6 (10.0.10293.0) çalıştırıyorsanız devam edin. Önceki bir sürümü çalıştırıyorsanız, önce cihazınıza [güncelleştirme 0,6](storsimple-virtual-array-install-update-06.md) ' ü yükledikten sonra güncelleştirme 1 ' i uygulayın.**
   
    ![Ekran görüntüsü, bakım menüsündeki yazılım güncelleştirmesinin seçili olduğunu gösterir.](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. **Güncelleştirme dosyası yolu**' nda, güncelleştirme veya düzeltme için dosya adını girin. Bir ağ paylaşımında yer alıyorsa güncelleştirme veya düzeltme yükleme dosyasına da gidebilirsiniz. **Uygula**’ya tıklayın.
   
    ![Ekran görüntüsü, yazılım güncelleştirme sayfasındaki geçerli yazılım sürümünü ve dosya yolunu Güncelleştir metin kutusunu gösterir.](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. Bir uyarı görüntülenir. Sanal dizi tek düğümlü bir cihaz olduğundan, güncelleştirme uygulandıktan sonra cihaz yeniden başlatılır ve kapalı kalma süresi vardır. Onay simgesine tıklayın.
   
   ![Ekran görüntüsü, kapalı kalma süresinin iletişim kutusu uyarısını gösterir.](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. Güncelleştirme başlar. Cihaz başarıyla güncelleştirildikten sonra yeniden başlatılır. Yerel Kullanıcı arabirimine bu süre içinde erişilemiyor.
   
    ![Ekran görüntüsü güncelleştirme için bir başarı iletisi gösterir.](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. Yeniden başlatma işlemi tamamlandıktan sonra **oturum açma** sayfasına yönlendirilirsiniz. Cihaz yazılımının güncelleştirildiğini doğrulamak için, yerel Web Kullanıcı arabiriminde **bakım**  >  **yazılım güncelleştirmesi**' ne gidin. Güncelleştirme 1,0 için, görüntülenmiş yazılım sürümü **10.0.0.0.0.10296** olmalıdır.
   
   > [!NOTE]
   > Yazılım sürümlerini yerel Web Kullanıcı arabiriminde ve Azure portal biraz farklı bir şekilde raporlarız. Örneğin, yerel Web Kullanıcı arabirimi raporları **10.0.0.0.0.10296** ve aynı sürüm için **10.0.10296.0** raporlar Azure Portal.
   
    ![Ekran görüntüsü, geçerli yazılım sürümüne sahip yazılım güncelleştirme sayfasını gösterir.](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Dosya kullanarak Windows Güvenlik düzeltmesini yüklemek için 2-4 adımlarını yineleyin `windows8.1-kb4012213-x64` . Sanal dizi, yüklemeden sonra yeniden başlatılır ve yerel Web Kullanıcı arabiriminde oturum açmanız gerekir.

> [!NOTE]
> Güncelleştirme 0,6 ' dan önce sürüm çalıştıran bir cihaza doğrudan güncelleştirme 1 ' i uyguladıysanız, bazı güncelleştirmeler eksik demektir. Sonraki adımlar için lütfen Microsoft Desteği başvurun.

## <a name="next-steps"></a>Sonraki adımlar

[StorSimple Sanal dizinizi yönetme](storsimple-ova-web-ui-admin.md)hakkında daha fazla bilgi edinin.
