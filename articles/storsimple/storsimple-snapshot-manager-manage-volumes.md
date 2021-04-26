---
title: StorSimple Snapshot Manager ve birimleri | Microsoft Docs
description: Birimleri görüntülemek ve yönetmek ve yedeklemeleri yapılandırmak için StorSimple Snapshot Manager MMC ek bileşeninin nasıl kullanılacağını açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: alkohli
ms.openlocfilehash: 309fa85d0a4d877522a89dd8f1e6e71fb2074744
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96022947"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Birimleri görüntülemek ve yönetmek için StorSimple Snapshot Manager kullanma
## <a name="overview"></a>Genel Bakış
Birimleri seçmek ve bunlarla ilgili bilgileri görüntülemek için StorSimple Snapshot Manager **birimleri** düğümünü ( **kapsam** bölmesinde) kullanabilirsiniz. Birimler, ana bilgisayar tarafından bağlanan birimlere karşılık gelen sürücüler olarak sunulur. **Birimler** düğümü, iSCSI ve bir cihaz kullanımı aracılığıyla bulunan birimler dahil olmak üzere StorSimple tarafından desteklenen yerel birimleri ve birim türlerini gösterir. 

Desteklenen birimler hakkında daha fazla bilgi için, [birden çok birim türü Için desteğe](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types)gidin.

![Sonuçlar bölmesindeki birim listesi](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

**Birimler** düğümü, storsimple Snapshot Manager saptadıktan sonra birimleri yeniden taramanıza veya silmenize olanak tanır. 

Bu öğreticide, birimleri bağlama, başlatma ve biçimlendirme işlemlerinin yanı sıra StorSimple Snapshot Manager kullanarak şunları yapabilirsiniz:

* Birimler hakkındaki bilgileri görüntüleme 
* Birimleri silme
* Birimleri yeniden Tara 
* Temel bir birimi yapılandırma ve yedekleme
* Dinamik bir yansıtılmış birimi yapılandırın ve yedekleyin

> [!NOTE]
> Tüm **birim** düğümü eylemleri Ayrıca **Eylemler** bölmesinde de kullanılabilir.
> 
> 

## <a name="mount-volumes"></a>Birimleri bağlama
StorSimple birimlerini bağlamak, başlatmak ve biçimlendirmek için aşağıdaki yordamı kullanın. Bu yordam, sabit diskleri ve ilgili birimleri veya bölümleri yönetmeye yönelik bir sistem yardımcı programı olan disk yönetimi 'ni kullanır. Disk yönetimi hakkında daha fazla bilgi için Microsoft TechNet Web sitesinde [disk yönetimi](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770943(v=ws.11)) ' ne gidin.

#### <a name="to-mount-volumes"></a>Birimleri bağlamak için
1. Ana bilgisayarınızda Microsoft Iscsı Başlatıcısı ' nı başlatın.
2. Arabirim IP adreslerinden birini hedef portal veya bulma IP adresi olarak sağlayın ve cihaza bağlanın. Cihaz bağlandıktan sonra, birimlere Windows sisteminiz tarafından erişilebilecektir. Microsoft Iscsı Başlatıcısı 'nı kullanma hakkında daha fazla bilgi için [Microsoft Iscsı Başlatıcısı 'Nı yükleme ve yapılandırma][1]konusundaki "bir iSCSI hedef cihazına bağlanma" bölümüne gidin.
3. Disk Yönetimi 'ni başlatmak için aşağıdaki seçeneklerden herhangi birini kullanın:
   
   * **Çalıştır** kutusuna diskmgmt. msc yazın.
   * Sunucu Yöneticisi başlatın, **depolama** düğümünü genişletin ve ardından **disk yönetimi**' ni seçin.
   * **Yönetimsel Araçlar**'ı başlatın, **Bilgisayar Yönetimi** düğümünü genişletin ve ardından **disk yönetimi**'ni seçin. 
     
     > [!NOTE]
     > Disk yönetimini çalıştırmak için yönetici ayrıcalıklarını kullanmanız gerekir.
     > 
     > 
4. Birimleri çevrimiçi duruma alın:
   
   1. Disk Yönetimi 'nde **çevrimdışı** olarak işaretlenmiş herhangi bir birime sağ tıklayın.
   2. **Diski yeniden etkinleştir**' e tıklayın. Disk yeniden etkinleştirildikten sonra diskin **çevrimiçi** olarak işaretlenmesi gerekir.
5. Birim (ler) i başlatın:
   
   1. Bulunan birimlere sağ tıklayın.
   2. Menüsünde **Diski Başlat**' ı seçin.
   3. **Diski Başlat** iletişim kutusunda başlatmak istediğiniz diskleri seçin ve ardından **Tamam**' a tıklayın.
6. Basit birimleri biçimlendirme:
   
   1. Biçimlendirmek istediğiniz bir birime sağ tıklayın.
   2. Menüde **Yeni basit birim**' i seçin.
   3. Birimi biçimlendirmek için yeni basit birim Sihirbazı 'nı kullanın:
      
      * Birim boyutunu belirtin.
      * Bir sürücü harfi sağlayın.
      * NTFS dosya sistemi ' ni seçin.
      * 64 KB ayırma birimi boyutu belirtin.
      * Hızlı biçimlendirme gerçekleştirin.
7. Çoklu bölümlü birimleri biçimlendirin. Yönergeler için, [Disk Yönetimi uygulama](/previous-versions/tn-archive/dd163556(v=technet.10))bölümünde "bölümler ve birimler" bölümüne gidin.

## <a name="view-information-about-your-volumes"></a>Birimleriniz hakkındaki bilgileri görüntüleme
Yerel ve Azure StorSimple birimleri hakkındaki bilgileri görüntülemek için aşağıdaki yordamı kullanın.

#### <a name="to-view-volume-information"></a>Birim bilgilerini görüntülemek için
1. StorSimple Snapshot Manager başlatmak için masaüstü simgesine tıklayın. 
2. **Kapsam** bölmesinde **birimler** düğümüne tıklayın. Tüm Azure StorSimple birimleri de dahil olmak üzere, yerel ve bağlı birimlerin listesi **sonuçlar** bölmesinde görünür. **Sonuçlar** bölmesindeki sütunlar yapılandırılabilir. ( **Birimler** düğümüne sağ tıklayın, **Görünüm**' ü seçin ve ardından **sütun Ekle/Kaldır**' ı seçin.)
   
    ![Sütunları yapılandırma](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Sonuç sütunu | Açıklama |
   |:--- |:--- |
   |  Ad |**Ad** sütunu, bulunan her birime atanan sürücü harfini içerir. |
   |  Cihaz |**Cihaz** sütunu, ana bilgisayara bağlı olan cihazın IP adresini içerir. |
   |  Cihaz birimi adı |**Cihaz birimi adı** sütunu, Seçilen birimin ait olduğu cihaz biriminin adını içerir. Bu, söz konusu birimin Azure portal tanımlanan birim adıdır. |
   |  Erişim yolları |**Erişim yolları** sütunu, birimin erişim yolunu görüntüler. Bu, birimin ana bilgisayar üzerinde erişilebilir olduğu sürücü harfi veya bağlama noktasıdır. |

## <a name="delete-a-volume"></a>Bir birimi silme
StorSimple Snapshot Manager bir birimi silmek için aşağıdaki yordamı kullanın.

> [!NOTE]
> Birim grubunun bir parçasıysa bir birimi silemezsiniz. (Silme seçeneği, bir birim grubunun üyesi olan birimlerde kullanılamaz.) Birimi silmek için birim grubunun tamamını silmeniz gerekir.

#### <a name="to-delete-a-volume"></a>Bir birimi silmek için
1. StorSimple Snapshot Manager başlatmak için masaüstü simgesine tıklayın.
2. **Kapsam** bölmesinde **birimler** düğümüne tıklayın. 
3. **Sonuçlar** bölmesinde, silmek istediğiniz birime sağ tıklayın.
4. Menüsünde **Sil**' e tıklayın. 
   
    ![Bir birimi silme](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. **Birimi Sil** iletişim kutusu görüntülenir. Metin kutusuna **onaylayın** yazın ve ardından **Tamam**' a tıklayın.
6. Varsayılan olarak, StorSimple Snapshot Manager silmeden önce bir birimi yedekler. Bu önlem, silme işlemi istenmeden olursa sizi veri kaybına karşı koruyabilir. StorSimple Snapshot Manager, birimi yedeklediğinde **Otomatik anlık görüntü** ilerleme durumu iletisi görüntüler. 
   
    ![Otomatik anlık görüntü iletisi](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Birimleri yeniden Tara
StorSimple Snapshot Manager bağlı birimleri yeniden taramak için aşağıdaki yordamı kullanın.

#### <a name="to-rescan-the-volumes"></a>Birimleri yeniden taramak için
1. StorSimple Snapshot Manager başlatmak için masaüstü simgesine tıklayın.
2. **Kapsam** bölmesinde, **birimler**' e sağ tıklayın ve ardından **birimleri yeniden Tara**' ya tıklayın.
   
    ![Birimleri yeniden Tara](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Bu yordam, birim listesini StorSimple Snapshot Manager eşitler. Yeni birimler veya silinen birimler gibi değişiklikler sonuçlara yansıtılır.

## <a name="configure-and-back-up-a-basic-volume"></a>Temel bir birimi yapılandırma ve yedekleme
Temel bir birimin yedeğini yapılandırmak için aşağıdaki yordamı kullanın ve ardından hemen bir yedekleme başlatın ya da zamanlanmış yedeklemeler için bir ilke oluşturun.

### <a name="prerequisites"></a>Ön koşullar
Başlamadan önce:

* StorSimple cihazının ve ana bilgisayarın doğru yapılandırıldığından emin olun. Daha fazla bilgi için, Şirket [Içi StorSimple cihazınızı dağıtma](./storsimple-8000-deployment-walkthrough-u2.md)konusuna gidin.
* StorSimple Snapshot Manager yükleyip yapılandırın. Daha fazla bilgi için, [StorSimple Snapshot Manager dağıtma](storsimple-snapshot-manager-deployment.md)konusuna gidin.

#### <a name="to-configure-backup-of-a-basic-volume"></a>Temel bir birimin yedeklemesini yapılandırmak için
1. StorSimple cihazında temel bir birim oluşturun.
2. [Bağlama birimleri](#mount-volumes)bölümünde açıklandığı şekilde birimi bağlayın, başlatın ve biçimlendirin. 
3. Masaüstünüzdeki StorSimple Snapshot Manager simgesine tıklayın. StorSimple Snapshot Manager penceresi görüntülenir. 
4. **Kapsam** bölmesinde **birimler** düğümüne sağ tıklayın ve ardından **birimleri yeniden Tara**' yı seçin. Tarama tamamlandığında, **sonuçlar** bölmesinde bir birim listesi görünmelidir. 
5. **Sonuçlar** bölmesinde, birime sağ tıklayın ve ardından **birim grubu oluştur**' u seçin. 
   
    ![Birim grubu oluştur](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. **Birim grubu oluştur** iletişim kutusunda, birim grubu için bir ad yazın, birime birim atayın ve ardından **Tamam**' a tıklayın.
7. **Kapsam** bölmesinde, **Birim grupları** düğümünü genişletin. Yeni birim grubu **Birim grupları** düğümünün altında görünmelidir. 
8. Birim grubu adına sağ tıklayın.
   
   * Etkileşimli (isteğe bağlı) yedekleme işini başlatmak için, **yedeklemeyi al**' ı tıklatın. 
   * Otomatik yedekleme zamanlamak için **yedekleme Ilkesi oluştur**' a tıklayın. **Genel** sayfasında, listeden bir birim grubu seçin. **Zamanlama** sayfasında, zamanlama ayrıntılarını girin. İşiniz bittiğinde **Tamam**'a tıklayın. 
9. Yedekleme işinin başlatıldığını onaylamak için, **kapsam** bölmesinde **işler** düğümünü genişletin ve ardından **çalışan** düğümüne tıklayın. Çalışmakta olan işlerin listesi **sonuçlar** bölmesinde görüntülenir. 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Dinamik yansıtılmış birimi yapılandırma ve yedekleme
Dinamik bir yansıtılmış birimin yedeklemesini yapılandırmak için aşağıdaki adımları izleyin:

* 1. Adım: dinamik bir yansıtılmış birim oluşturmak için disk yönetimi 'Ni kullanın. 
* 2. Adım: yedek yapılandırmak için StorSimple Snapshot Manager kullanın.

### <a name="prerequisites"></a>Ön koşullar
Başlamadan önce:

* StorSimple cihazının ve ana bilgisayarın doğru yapılandırıldığından emin olun. Daha fazla bilgi için, Şirket [Içi StorSimple cihazınızı dağıtma](storsimple-8000-deployment-walkthrough-u2.md)konusuna gidin.
* StorSimple Snapshot Manager yükleyip yapılandırın. Daha fazla bilgi için, [StorSimple Snapshot Manager dağıtma](storsimple-snapshot-manager-deployment.md)konusuna gidin.
* StorSimple cihazında iki birim yapılandırın. (Örneklerde, kullanılabilir birimler **disk 1** ve **Disk 2**' dir.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>1. Adım: Dinamik yansıtılmış birim oluşturmak için disk yönetimi 'Ni kullanma
Disk Yönetimi, sabit diskleri ve içerdikleri birimleri veya bölümleri yönetmeye yönelik bir sistem yardımcı programıdır. Disk yönetimi hakkında daha fazla bilgi için Microsoft TechNet Web sitesinde [disk yönetimi](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770943(v=ws.11)) ' ne gidin.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Dinamik bir yansıtılmış birim oluşturmak için
1. Disk Yönetimi 'ni başlatmak için aşağıdaki seçeneklerden herhangi birini kullanın: 
   
   * **Çalıştır** kutusunu açın, **diskmgmt. msc** yazın ve ENTER tuşuna basın.
   * Sunucu Yöneticisi başlatın, **depolama** düğümünü genişletin ve ardından **disk yönetimi**' ni seçin. 
   * **Yönetimsel Araçlar**'ı başlatın, **Bilgisayar Yönetimi** düğümünü genişletin ve ardından **disk yönetimi**'ni seçin. 
2. StorSimple cihazında iki biriminiz bulunduğundan emin olun. (Örnekte, kullanılabilir birimler **disk 1** ve **Disk 2**' dir.) 
3. Disk Yönetimi penceresinde, alt bölmenin sağ sütununda, **disk 1** ' e sağ tıklayıp **yeni yansıtılmış birim**' i seçin. 
   
    ![Yeni yansıtılmış birim](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. **Yeni yansıtılmış birim** Sihirbazı sayfasında, **İleri**' ye tıklayın.
5. **Diskleri seçin** sayfasında, **Seçili** bölmedeki **Disk 2** ' yi seçin, **Ekle**' ye ve ardından **İleri**' ye tıklayın. 
6. **Sürücü harfi veya yolu ata** sayfasında, Varsayılanları kabul edin ve ardından **İleri**' ye tıklayın. 
7. **Birim Biçimlendir** sayfasında, **ayırma birimi boyutu** kutusunda, **64K**' yı seçin. **Hızlı biçimlendirme gerçekleştir** onay kutusunu seçin ve ardından **İleri**' ye tıklayın. 
8. **Yeni yansıtılmış birimi tamamlama** sayfasında, ayarlarınızı gözden geçirin ve ardından **son**' a tıklayın. 
9. Temel diskin dinamik diske dönüştürüleceğini belirten bir ileti görüntülenir. **Evet**'e tıklayın.
   
    ![Dinamik disk dönüştürme iletisi](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. Disk Yönetimi 'nde disk 1 ve disk 2 ' nin dinamik yansıtılmış birimler olarak gösterildiğini doğrulayın. (**Dinamik** durum sütununda görünmelidir ve kapasite çubuğu rengi, yansıtılmış bir birimi belirten kırmızı olarak değiştirilmelidir.) 
    
    ![Disk Yönetimi yansıtılmış dinamik diskler](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>2. Adım: bir yedeklemeyi yapılandırmak için StorSimple Snapshot Manager kullanma
Dinamik bir yansıtılmış birimi yapılandırmak için aşağıdaki yordamı kullanın ve ardından hemen bir yedekleme başlatın veya zamanlanmış yedeklemeler için bir ilke oluşturun.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Dinamik bir yansıtılmış birimin yedeklemesini yapılandırmak için
1. Masaüstünüzdeki StorSimple Snapshot Manager simgesine tıklayın. StorSimple Snapshot Manager penceresi görüntülenir. 
2. **Kapsam** bölmesinde, **birimler** düğümüne sağ tıklayın ve **birimleri yeniden Tara**' yı seçin. Tarama tamamlandığında, **sonuçlar** bölmesinde bir birim listesi görünmelidir. Dinamik yansıtılmış birim tek bir birim olarak listelenir. 
3. **Sonuçlar** bölmesinde, dinamik yansıtılmış birime sağ tıklayın ve ardından **birim grubu oluştur**' a tıklayın. 
4. **Birim grubu oluştur** iletişim kutusunda, birim grubu için bir ad yazın, dinamik yansıtılmış birimi bu gruba atayın ve ardından **Tamam**' a tıklayın. 
5. **Kapsam** bölmesinde, **Birim grupları** düğümünü genişletin. Yeni birim grubu  **Birim grupları** düğümünün altında görünmelidir. 
6. Birim grubu adına sağ tıklayın. 
   
   * Etkileşimli (isteğe bağlı) yedekleme işini başlatmak için, **yedeklemeyi al**' ı tıklatın. 
   * Otomatik yedekleme zamanlamak için **yedekleme Ilkesi oluştur**' a tıklayın. **Genel** sayfasında, listeden birim grubunu seçin. **Zamanlama** sayfasında, zamanlama ayrıntılarını girin. İşiniz bittiğinde **Tamam**'a tıklayın. 
7. Yedekleme işini çalışırken izleyebilirsiniz. **Kapsam** bölmesinde, **işler** düğümünü genişletin ve ardından **çalışıyor**' a tıklayın, **sonuçlar** bölmesinde iş ayrıntıları görüntülenir. Yedekleme işi tamamlandığında, Ayrıntılar **son 24** saatlik iş listesine aktarılır. 

## <a name="next-steps"></a>Sonraki adımlar
* StorSimple [çözümünüzü yönetmek Için storsimple Snapshot Manager](storsimple-snapshot-manager-admin.md)nasıl kullanacağınızı öğrenin.
* [StorSimple Snapshot Manager kullanarak birim grupları oluşturma ve yönetme](storsimple-snapshot-manager-manage-volume-groups.md)hakkında bilgi edinin.

<!--Reference links-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee338480(v=ws.10)