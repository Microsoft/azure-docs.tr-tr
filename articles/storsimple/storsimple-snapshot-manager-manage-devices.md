---
title: StorSimple Snapshot Manager cihazları yönetme | Microsoft Docs
description: StorSimple cihazlarını bağlamak ve yönetmek için StorSimple Snapshot Manager MMC ek bileşeninin nasıl kullanılacağını açıklar.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: dbb7e835d110bcb6cb81fd5425a2aafbb578e692
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96022981"
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>StorSimple cihazlarını bağlamak ve yönetmek için StorSimple Snapshot Manager kullanma
## <a name="overview"></a>Genel Bakış
İçe aktarılmış StorSimple cihaz verilerini doğrulamak ve bağlı depolama cihazlarını yenilemek için StorSimple Snapshot Manager **kapsam** bölmesinde düğümleri kullanabilirsiniz. Ayrıca, **cihazlar** düğümüne tıkladığınızda, **sonuçlar** bölmesinde bağlı cihazların ve ilgili durum bilgilerinin bir listesini görebilirsiniz.

![Bağlı cihazlar](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Şekil 1: StorSimple Snapshot Manager bağlı cihaz** 

**Görünüm** seçimlerinize bağlı olarak, **sonuçlar** bölmesi her bir cihazla ilgili olarak aşağıdaki bilgileri gösterir. (Görünüm yapılandırma hakkında daha fazla bilgi için [Görünüm menüsüne](storsimple-use-snapshot-manager.md#view-menu)gidin.

| Sonuç sütunu | Açıklama |
|:--- |:--- |
| Ad |Klasik Azure portalında yapılandırılan cihazın adı |
| Modelleme |Cihazın model numarası |
| Sürüm |Cihazda yüklü yazılımın sürümü |
| Durum |Cihazın kullanılabilir olup olmadığı |
| Son eşitleme |Cihazın son eşitlenme tarihi ve saati |
| Seri No. |Cihazın seri numarası |

**Kapsam** bölmesinde **cihazlar** düğümüne sağ tıklarsanız aşağıdaki eylemlerden seçim yapabilirsiniz:

* Bir cihaz ekleme veya değiştirme
* Bir cihazı bağlama ve içeri aktarmaları doğrulama
* Bağlı cihazları Yenile

**Cihazlar** düğümüne tıklayın ve ardından **sonuçlar** bölmesinde bir cihaz adına sağ tıkladığınızda, aşağıdaki eylemlerden seçim yapabilirsiniz:

* Bir cihazın kimliğini doğrulama
* Cihaz ayrıntılarını görüntüleme
* Bir cihazı yenileme
* Cihaz yapılandırmasını silme
* Bir cihaz parolasını değiştirme

> [!NOTE]
> Bu eylemlerin tümü, **Eylemler** bölmesinde de mevcuttur.


Bu öğreticide, StorSimple Snapshot Manager kullanarak cihazları bağlama ve yönetme ve aşağıdaki görevleri gerçekleştirme açıklanmaktadır:

* Bir cihaz ekleme veya değiştirme
* Bir cihazı bağlama ve içeri aktarmaları doğrulama
* Bağlı cihazları Yenile
* Bir cihazın kimliğini doğrulama
* Cihaz ayrıntılarını görüntüleme
* Tek bir cihazı yenileme
* Cihaz yapılandırmasını silme
* Zaman aşımına uğradı bir cihaz parolasını değiştirme
* Hatalı bir cihazı değiştirme

> [!NOTE]
> StorSimple Snapshot Manager arabirimini kullanma hakkında genel bilgi için, [storsimple Snapshot Manager Kullanıcı arabirimine](storsimple-use-snapshot-manager.md)gidin.


## <a name="add-or-replace-a-device"></a>Bir cihaz ekleme veya değiştirme
StorSimple cihazını eklemek veya değiştirmek için aşağıdaki yordamı kullanın.

#### <a name="to-add-or-replace-a-device"></a>Bir cihaz eklemek veya değiştirmek için
1. StorSimple Snapshot Manager başlatmak için masaüstü simgesine tıklayın.
2. **Kapsam** bölmesinde, **cihazlar** düğümüne sağ tıklayın ve ardından **cihaz yapılandırma**' ya tıklayın. **Cihaz Yapılandır** iletişim kutusu görüntülenir.
   
    ![StorSimple cihazını yapılandırma](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. **Cihaz** açılan kutusunda, cihazın veya sanal cihazın IP adresini seçin. 
4. **Parola** metin kutusuna, klasik Azure portalında cihaz Için oluşturduğunuz storsimple Snapshot Manager parolasını yazın. **Tamam**'a tıklayın. StorSimple Snapshot Manager, tanımladığınız cihazı arar. 
   
   * Cihaz kullanılabiliyorsa, StorSimple Snapshot Manager bir bağlantı ekler.
   * Cihaz herhangi bir nedenle kullanılamıyorsa, StorSimple Snapshot Manager bir hata mesajı döndürür. **Tamam** ' a tıklayarak hata iletisini kapatın ve ardından **iptal** ' e tıklayarak **bir cihaz Yapılandır** iletişim kutusunu kapatın.

## <a name="connect-a-device-and-verify-imports"></a>Bir cihazı bağlama ve içeri aktarmaları doğrulama
StorSimple cihazını bağlamak ve ilişkili yedekleri olan mevcut birim gruplarının içeri aktarıldığını doğrulamak için aşağıdaki yordamı kullanın.

#### <a name="to-connect-a-device-and-verify-imports"></a>Bir cihazı bağlamak ve içeri aktarmaları doğrulamak için
1. Bir cihazı StorSimple Snapshot Manager bağlamak için, bir cihaz ekleme veya değiştirme içindeki yönergeleri izleyin. Bir cihaza bağlandığı zaman StorSimple Snapshot Manager aşağıdaki gibi yanıt verir:
   
   * Cihaz herhangi bir nedenle kullanılamıyorsa, StorSimple Snapshot Manager bir hata mesajı döndürür. 
   
   * Cihaz kullanılabiliyorsa, StorSimple Snapshot Manager bir bağlantı ekler. Cihazı seçtiğinizde, **sonuçlar** bölmesinde görünür ve durum alanı cihazın **kullanılabilir** olduğunu gösterir. StorSimple Snapshot Manager, birim gruplarının ilişkili yedeklemeleri olması şartıyla, cihaz için yapılandırılmış tüm birim gruplarını içeri aktarır. Yedekleme ilkeleri içeri aktarılmaz. İlişkili yedeklemeleri olmayan birim grupları içeri aktarılmaz.
2. StorSimple Snapshot Manager başlatmak için masaüstü simgesine tıklayın.
3. **Kapsam** bölmesinde en üst düğüme sağ tıklayın ve ardından **Içeri aktarmalar görüntüsünü aç**' a tıklayın.
   
    ![Içeri aktarmalar görünümünü değiştirmeyi Seç](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. İçeri **aktarmalar görüntüsünü değiştirme** iletişim kutusu görünür ve içeri aktarılan birim gruplarının ve yedeklemelerin durumunu gösterir. **Tamam**'a tıklayın.

Birim grupları ve yedeklemeler başarıyla alındıktan sonra, bunları yönetmek için StorSimple Snapshot Manager kullanabilirsiniz, böylece, oluşturduğunuz ve StorSimple Snapshot Manager ile yapılandırdığınız yedeklemeleri yönetebilirsiniz. 

## <a name="refresh-connected-devices"></a>Bağlı cihazları Yenile
Bağlı StorSimple cihazlarını StorSimple Snapshot Manager ile eşleştirmek için aşağıdaki yordamı kullanın.

#### <a name="to-refresh-connected-devices"></a>Bağlı cihazları yenilemek için
1. StorSimple Snapshot Manager başlatmak için masaüstü simgesine tıklayın.
2. **Kapsam** bölmesinde, **cihazlar**' a sağ tıklayın ve ardından **cihazları Yenile**' ye tıklayın. Bu, bağlı cihazları StorSimple Snapshot Manager ile eşitler, böylece son ekler de dahil olmak üzere birim gruplarını ve yedeklemeleri görüntüleyebilmenizi sağlayabilir. 
   
    ![StorSimple cihazlarını yenileme](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

**Cihazları Yenile** eylemi, bağlı cihazlardan tüm yeni birim gruplarını ve ilişkili yedekleri alır. **Birimler** düğümü için kullanılabilir **birimleri yeniden Tara** eyleminin aksine, **cihazları Yenile** , yedekleme kayıt defterini geri yüklemez.

## <a name="authenticate-a-device"></a>Bir cihazın kimliğini doğrulama
StorSimple Snapshot Manager ile StorSimple cihazının kimliğini doğrulamak için aşağıdaki yordamı kullanın.

#### <a name="to-authenticate-a-device"></a>Bir cihazın kimliğini doğrulamak için
1. StorSimple Snapshot Manager başlatmak için masaüstü simgesine tıklayın.
2. **Kapsam** bölmesinde, **cihazlar**' a tıklayın.
3. **Sonuçlar** bölmesinde, cihazın adına sağ tıklayın ve ardından **kimlik doğrula**' ya tıklayın.
4. **Kimlik doğrula** iletişim kutusu görüntülenir. Cihaz parolasını yazın ve ardından **Tamam**' a tıklayın.
   
    ![Kimlik doğrulaması iletişim kutusu](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Cihaz ayrıntılarını görüntüleme
StorSimple cihazının ayrıntılarını görüntülemek için aşağıdaki yordamı kullanın ve gerekirse, cihazı StorSimple Snapshot Manager yeniden eşitleyin.

#### <a name="to-view-and-resynchronize-device-details"></a>Cihaz ayrıntılarını görüntülemek ve yeniden eşitlemek için
1. StorSimple Snapshot Manager başlatmak için masaüstü simgesine tıklayın.
2. **Kapsam** bölmesinde, **cihazlar**' a tıklayın.
3. **Sonuçlar** bölmesinde, cihazın adına sağ tıklayın ve ardından **Ayrıntılar**' a tıklayın.

4. **cihaz ayrıntıları** iletişim kutusu görüntülenir. Bu kutu ad, model, sürüm, seri numarası, durum, hedef Iscsı nitelenmiş adı (ıQN) ve son eşitleme tarih ve saatini gösterir.

* Cihazı **eşitleme için yeniden Eşitle '** ye tıklayın.
* **Tamam** ' a tıklayın veya iletişim kutusunu kapatmak için **iptal** ' e tıklayın.
  
  ![Cihaz ayrıntıları](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Tek bir cihazı yenileme
Tek bir StorSimple cihazını StorSimple Snapshot Manager yeniden eşitlemek için aşağıdaki yordamı kullanın.

#### <a name="to-refresh-a-device"></a>Bir cihazı yenilemek için
1. StorSimple Snapshot Manager başlatmak için masaüstü simgesine tıklayın. 
2. **Kapsam** bölmesinde, **cihazlar**' a tıklayın. 
3. **Sonuçlar** bölmesinde, cihazın adına sağ tıklayın ve ardından **cihazı Yenile**' ye tıklayın. Bu, cihazı StorSimple Snapshot Manager eşitler.

## <a name="delete-a-device-configuration"></a>Cihaz yapılandırmasını silme
StorSimple Snapshot Manager bireysel bir StorSimple cihaz yapılandırmasını silmek için aşağıdaki yordamı kullanın.

#### <a name="to-delete-a-device-configuration"></a>Bir cihaz yapılandırmasını silmek için
1. StorSimple Snapshot Manager başlatmak için masaüstü simgesine tıklayın.
2. **Kapsam** bölmesinde, **cihazlar**' a tıklayın. 
3. **Sonuçlar** bölmesinde, cihazın adına sağ tıklayın ve ardından **Sil**' e tıklayın. 
4. Aşağıdaki ileti görünür. Yapılandırmayı silmek için **Evet** ' e, silme işlemini iptal etmek için **Hayır** ' a tıklayın.
   
    ![Cihaz yapılandırmasını silme](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Zaman aşımına uğradı bir cihaz parolasını değiştirme
StorSimple Snapshot Manager bir StorSimple cihazının kimliğini doğrulamak için bir parola girmeniz gerekir. Bu parolayı, cihazı kurmak için Windows PowerShell arabirimini kullandığınızda yapılandırırsınız. Ancak parolanın kullanım süreleri dolacak. Bu durumda, parolayı değiştirmek için klasik Azure portalını kullanabilirsiniz. Daha sonra, cihazın StorSimple Snapshot Manager, parolanın geçerliliği dolmadan önce yapılandırıldığından, StorSimple Snapshot Manager cihaz kimliğini yeniden doğrulamanız gerekir.

#### <a name="to-change-the-expired-password"></a>Süre biten parolayı değiştirmek için
1. Klasik Azure portalında, StorSimple Yöneticisi hizmetini başlatın.
2. Cihaz için **cihazlar**  >  **yapılandırması** ' na tıklayın.
3. StorSimple Snapshot Manager bölümüne gidin. 14-15 karakter olan bir parola girin. Parolanın büyük harf, küçük harf, sayısal ve özel karakterlerin bir karışımını içerdiğinden emin olun.
4. Parolayı onaylamak için yeniden girin.
5. Sayfanın alt kısmındaki **Kaydet**’e tıklayın.

#### <a name="to-re-authenticate-the-device"></a>Cihazın yeniden doğrulanması için
1. StorSimple Snapshot Manager başlatın.
2. **Kapsam** bölmesinde, **cihazlar**' a tıklayın. Yapılandırılmış cihazların bir listesi **sonuçlar** bölmesinde görüntülenir.
3. Cihazı seçin, sağ tıklayın ve ardından **kimlik doğrula**' ya tıklayın.
4. **Kimlik doğrula** penceresinde yeni parolayı girin.
5. Cihazı seçin, sağ tıklayın ve **cihazı Yenile**' yi seçin. Bu, cihazı StorSimple Snapshot Manager eşitler.

## <a name="replace-a-failed-device"></a>Hatalı bir cihazı değiştirme
Bir StorSimple cihazı başarısız olursa ve bekleme (yük devretme) cihazından değiştirilirse, yeni cihaza bağlanmak ve ilişkili yedeklemeleri görüntülemek için aşağıdaki adımları kullanın.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Yük devretmeden sonra yeni bir cihaza bağlanmak için
1. Iscsı bağlantısını yeni cihazla yeniden yapılandırın. Yönergeler için, [Şirket Içi StorSimple cihazınızı dağıtma](storsimple-8000-deployment-walkthrough-u2.md)bölümünde "adım 7: bir birimi bağlama, başlatma ve biçimlendirme" bölümüne gidin.

> [!NOTE]
> Yeni StorSimple cihazının eskisiyle aynı IP adresi varsa, eski yapılandırmayı bağlayabilirsiniz.


1. Microsoft StorSimple Yönetim hizmetini durdurun:
   
   1. Sunucu Yöneticisi'ni başlatın.
   2. Sunucu Yöneticisi panosunda, **Araçlar** menüsünde **Hizmetler**' i seçin.
   3. **Hizmetler** penceresinde **Microsoft StorSimple yönetim hizmeti**' ni seçin.
   4. Sağ bölmedeki **Microsoft StorSimple yönetim hizmeti** altında **hizmeti Durdur**' a tıklayın.
2. Eski cihazla ilgili yapılandırma bilgilerini Kaldır:
   
   1. Dosya Gezgini 'nde C:\programdata\microsoft\storsimple\bacatalogdizinine göz atın.
   2. BACatalog klasöründeki dosyaları silin.
3. Microsoft StorSimple Yönetim hizmetini yeniden başlatın:
   
   1. Sunucu Yöneticisi panosunda, **Araçlar** menüsünde **Hizmetler**' i seçin.
   2. **Hizmetler** penceresinde **Microsoft StorSimple yönetim hizmeti**' ni seçin.
   3. Sağ bölmedeki **Microsoft StorSimple yönetim hizmeti** altında **hizmeti yeniden Başlat**' a tıklayın.
4. StorSimple Snapshot Manager başlatın.
5. Yeni StorSimple cihazını yapılandırmak için adım 2: StorSimple cihazını [dağıtım storsimple Snapshot Manager](storsimple-snapshot-manager-deployment.md)içindeki adımları izleyin.
6. **Kapsam** bölmesinde en üst düzey düğüme sağ tıklayın (örnekteki storsimple Snapshot Manager) ve ardından **Içeri aktarmalar görünümünü aç**' a tıklayın. 
7. İçeri aktarılan birim grupları ve yedeklemeler StorSimple Snapshot Manager görünür olduğunda bir ileti görüntülenir. **Tamam**'a tıklayın.

## <a name="next-steps"></a>Sonraki adımlar
* StorSimple [çözümünüzü yönetmek Için storsimple Snapshot Manager](storsimple-snapshot-manager-admin.md)nasıl kullanacağınızı öğrenin.
* [Birimleri görüntülemek ve yönetmek Için StorSimple Snapshot Manager kullanmayı](storsimple-snapshot-manager-manage-volumes.md)öğrenin.

