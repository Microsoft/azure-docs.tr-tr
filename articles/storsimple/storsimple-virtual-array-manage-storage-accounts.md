---
title: StorSimple Sanal dizi depolama hesabı kimlik bilgilerini yönetme | Microsoft Docs
description: StorSimple Sanal dizisi ile ilişkili depolama hesabı kimlik bilgileri için güvenlik anahtarlarını eklemek, düzenlemek, silmek veya döndürmek üzere StorSimple Device Manager Yapılandır sayfasını nasıl kullanabileceğinizi açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 234bf8bb-d5fe-40be-9d25-721d7482bc3b
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: 70b6231829173059ffee675105bacbe1bd1532f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023900"
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>StorSimple Sanal dizisi için depolama hesabı kimlik bilgilerini yönetmek üzere StorSimple Device Manager kullanma

## <a name="overview"></a>Genel Bakış
StorSimple Sanal dizininizdeki StorSimple Device Manager hizmet dikey penceresinin **yapılandırma** bölümü, StorSimple Yöneticisi hizmetinde oluşturulabilen genel hizmet parametrelerini gösterir. Bu parametreler hizmete bağlı tüm cihazlara uygulanabilir ve şunları içerir:

* Depolama hesabı kimlik bilgileri
* Erişim denetimi kayıtları
  
  ![Device Manager hizmet panosu](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

Bu öğreticide, StorSimple Sanal diziniz için depolama hesabı kimlik bilgilerini nasıl ekleyebileceğiniz, düzenleyebileceğiniz veya silebileceğiniz açıklanır. Bu öğreticideki bilgiler yalnızca StorSimple Sanal dizisi için geçerlidir. 8000 serisinde depolama hesaplarını yönetme hakkında daha fazla bilgi için bkz. [depolama hesabınızı yönetmek Için StorSimple Yöneticisi hizmetini kullanma](./storsimple-8000-manage-storage-accounts.md).

Depolama hesabı kimlik bilgileri, cihazın bulut hizmeti sağlayıcınızda depolama hesabınıza erişmek için kullandığı kimlik bilgilerini içerir. Microsoft Azure depolama hesapları için, bunlar hesap adı ve birincil erişim anahtarı gibi kimlik bilgileridir.

**Depolama hesabı kimlik bilgileri** dikey penceresinde, faturalandırma aboneliği için oluşturulan tüm depolama hesabı kimlik bilgileri, aşağıdaki bilgileri içeren bir tablo biçiminde görüntülenir:

* **Name** : hesap oluşturulduğunda hesaba atanan benzersiz ad.
* **SSL etkin** – TLS 'nin etkinleştirilip etkinleştirilmediğini ve cihazdan buluta iletişimin güvenli kanal üzerinden mi olduğunu belirtir.
  
  ![Yapılandırma bölümü](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

Depolama **hesabı kimlik bilgileri** dikey penceresinde gerçekleştirilebilecek depolama hesabı kimlik bilgileri ile ilgili en yaygın görevler şunlardır:

* Depolama hesabı kimlik bilgisi ekleyin
* Depolama hesabı kimlik bilgilerini düzenleme
* Depolama hesabı kimlik bilgilerini silme

## <a name="types-of-storage-account-credentials"></a>Depolama hesabı kimlik bilgileri türleri
StorSimple aygıtınızla kullanılabilecek üç tür depolama hesabı kimlik bilgisi vardır.

* **Otomatik olarak oluşturulan depolama hesabı kimlik bilgileri** – ad ' ın gösterildiği gibi, bu tür bir depolama hesabı kimlik bilgisi, hizmet ilk oluşturulduğunda otomatik olarak oluşturulur. Bu depolama hesabı kimlik bilgisinin nasıl oluşturulduğu hakkında daha fazla bilgi edinmek için bkz. [yeni hizmet oluşturma](storsimple-virtual-array-manage-service.md#create-a-service).
* **hizmet aboneliğindeki depolama hesabı kimlik bilgileri** : Bunlar, hizmet ile aynı abonelikle ilişkili Azure depolama hesabı kimlik bilgileridir. Bu depolama hesabı kimlik bilgilerinin nasıl oluşturulduğu hakkında daha fazla bilgi edinmek için bkz. [Azure depolama hesapları hakkında](../storage/common/storage-account-create.md).
* **depolama hesabı kimlik bilgileri hizmet aboneliği dışında** : Bunlar, hizmetiniz ile ilişkilendirilmemiş ve hizmet oluşturulmadan önce büyük olasılıkla var olan Azure depolama hesabı kimlik bilgileridir.

## <a name="add-a-storage-account-credential"></a>Depolama hesabı kimlik bilgisi ekleyin
Depolama hesabına bağlı benzersiz bir kolay ad ve erişim kimlik bilgileri sağlayarak StorSimple Device Manager hizmet yapılandırmanıza depolama hesabı kimlik bilgileri ekleyebilirsiniz. Ayrıca, cihazınız ve bulut arasında ağ iletişimi için güvenli bir kanal oluşturmak üzere daha önce Güvenli Yuva Katmanı (SSL) modu olarak bilinen Aktarım Katmanı Güvenliği (TLS) modunu etkinleştirme seçeneğiniz de vardır.

Belirli bir bulut hizmeti sağlayıcısı için birden çok hesap oluşturabilirsiniz. Depolama hesabı kimlik bilgileri kaydedilirken hizmet, bulut hizmeti sağlayıcınızla iletişim kurmayı dener. Sağladığınız kimlik bilgileri ve erişim malzemesinin kimliği şu anda doğrulanır. Depolama hesabı kimlik bilgileri yalnızca kimlik doğrulaması başarılı olursa oluşturulur. Kimlik doğrulaması başarısız olursa, uygun bir hata iletisi görüntülenir.

Azure depolama hesabı kimlik bilgilerini eklemek için aşağıdaki yordamları kullanın:

* Device Manager hizmeti ile aynı Azure aboneliğine sahip bir depolama hesabı kimlik bilgisi eklemek için
* Device Manager hizmeti aboneliğinin dışında bir Azure depolama hesabı kimlik bilgisi eklemek için

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Device Manager hizmeti ile aynı Azure aboneliğine sahip bir depolama hesabı kimlik bilgisi eklemek için

1. Device Manager hizmetinize gidin, seçin ve çift tıklayın. Bu, **genel bakış** dikey penceresini açar.
2. **Yapılandırma** bölümünde **depolama hesabı kimlik bilgilerini** seçin.
3. **Ekle**'ye tıklayın.
4. **Depolama hesabı ekle** dikey penceresinde aşağıdakileri yapın:
   
    1. **Abonelik** için **geçerli**' i seçin.
    2. Azure depolama hesabınızın adını belirtin.
    3. StorSimple cihazınız ve bulut arasında ağ iletişimi için güvenli bir kanal oluşturmak üzere **Etkinleştir** ' i seçin. Yalnızca özel bir bulutta çalışıyorsanız **devre dışı bırak** ' ı seçin.
    4. **Ekle**'ye tıklayın. Depolama hesabı başarıyla oluşturulduktan sonra bilgilendirilirsiniz.<br></br>
   
        ![Mevcut bir depolama hesabı kimlik bilgisi ekleyin](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Device Manager hizmeti aboneliğinin dışında bir Azure depolama hesabı kimlik bilgisi eklemek için

1. Device Manager hizmetinize gidin, seçin ve çift tıklayın. Bu, **genel bakış** dikey penceresini açar.
2. **Yapılandırma** bölümünde **depolama hesabı kimlik bilgilerini** seçin. Bu, StorSimple Device Manager hizmetiyle ilişkili mevcut depolama hesabı kimlik bilgilerini listeler.
3. **Ekle**'ye tıklayın.
4. **Depolama hesabı ekle** dikey penceresinde aşağıdakileri yapın:
   
    1. **Abonelik** için **diğer**' i seçin.
   
    2. Azure depolama hesabı kimlik bilgilerinizin adını belirtin.
   
    3. **Depolama hesabı erişim anahtarı** metin kutusunda, Azure depolama hesabı kimlik bilgilerinizin birincil erişim anahtarını sağlayın. Bu anahtarı almak için Azure depolama hizmeti ' ne gidin, depolama hesabı kimlik bilgilerinizi seçin ve **Hesap anahtarlarını Yönet**' e tıklayın. Artık birincil erişim anahtarını kopyalayabilirsiniz.
   
    4. TLS 'i etkinleştirmek için, StorSimple Device Manager hizmetiniz ve bulut arasında ağ iletişimi için güvenli bir kanal oluşturmak üzere **Etkinleştir** düğmesine tıklayın. Yalnızca özel bir bulutta çalışıyorsanız **devre dışı bırak** düğmesine tıklayın.
   
    5. **Ekle**'ye tıklayın. Depolama hesabı kimlik bilgisi başarıyla oluşturulduktan sonra bilgilendirilirsiniz.

5. Yeni oluşturulan depolama hesabı kimlik bilgileri, **depolama hesabı kimlik bilgileri** altındaki StorSimple yapılandırma Device Manager hizmeti dikey penceresinde görüntülenir.
   
    ![Device Manager hizmeti aboneliğinin dışında bir depolama hesabı kimlik bilgisi ekleyin](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Depolama hesabı kimlik bilgilerini düzenleme
Cihazınız tarafından kullanılan bir depolama hesabı kimlik bilgilerini düzenleyebilirsiniz. Şu anda kullanılmakta olan bir depolama hesabı kimlik bilgisini düzenlerseniz, değişiklik için kullanılabilecek alanlar, depolama hesabı kimlik bilgileri için erişim anahtarı ve TLS modudur. Yeni depolama erişim anahtarını belirtebilir veya **SSL modunu etkinleştir** seçimini değiştirebilir ve güncelleştirilmiş ayarları kaydedebilirsiniz.

#### <a name="to-edit-a-storage-account-credential"></a>Depolama hesabı kimlik bilgilerini düzenlemek için
1. Device Manager hizmetinize gidin, seçin ve çift tıklayın. Bu, **genel bakış** dikey penceresini açar.
2. **Yapılandırma** bölümünde **depolama hesabı kimlik bilgilerini** seçin. Bu, StorSimple Device Manager hizmetiyle ilişkili mevcut depolama hesabı kimlik bilgilerini listeler.
3. Depolama hesabı kimlik bilgilerinin tablosal listesinde, değiştirmek istediğiniz hesabı seçin ve çift tıklatın.
4. Depolama hesabı kimlik bilgisi **özellikleri** dikey penceresinde aşağıdakileri yapın:
   
   1. Gerekirse **SSL modunu etkinleştir** seçimini değiştirebilirsiniz.
   2. Depolama hesabı kimlik bilgileri erişim anahtarlarınızı yeniden oluşturmayı tercih edebilirsiniz. Daha fazla bilgi için bkz. [depolama hesabı erişim anahtarlarını yönetme](../storage/common/storage-account-keys-manage.md). Yeni depolama hesabı kimlik bilgileri anahtarını sağlayın. Azure depolama hesabı için bu birincil erişim anahtarıdır.
   3. Ayarları kaydetmek için **Özellikler** dikey penceresinin en üstündeki **Kaydet** ' e tıklayın. Ayarlar **depolama hesabı kimlik bilgileri** dikey penceresinde güncelleştirilir.
      
      ![Depolama hesabı kimlik bilgilerini düzenleme](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Depolama hesabı kimlik bilgilerini silme
> [!IMPORTANT]
> Bir depolama hesabı kimlik bilgisini, yalnızca kullanımda değilse silebilirsiniz. Bir depolama hesabı kimlik bilgileri kullanımda ise size bildirilir.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Depolama hesabı kimlik bilgilerini silmek için
1. Device Manager hizmetinize gidin, seçin ve çift tıklayın. Bu, **genel bakış** dikey penceresini açar.
2. **Yapılandırma** bölümünde **depolama hesabı kimlik bilgilerini** seçin. Bu, StorSimple Device Manager hizmetiyle ilişkili mevcut depolama hesabı kimlik bilgilerini listeler.
3. Depolama hesabı kimlik bilgilerinin tablosal listesinde, silmek istediğiniz hesabı seçin ve çift tıklatın.
4. Depolama hesabı kimlik bilgisi **özellikleri** dikey penceresinde aşağıdakileri yapın:
   
   1. Kimlik bilgilerini silmek için **Sil** ' e tıklayın.
   2. Onay istendiğinde, silme işlemine devam etmek için **Evet** ' e tıklayın. Tablo listesi, değişiklikleri yansıtacak şekilde güncelleştirilir.
      
      ![Depolama hesabı kimlik bilgilerini silme](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Depolama hesabı kimlik bilgisi anahtarlarını eşitleme
Güvenlik nedenleriyle, anahtar döndürme genellikle veri merkezlerinde gereksinimdir. Microsoft Azure Yöneticisi, depolama hesabı kimlik bilgisine (Microsoft Azure Depolama hizmeti aracılığıyla) doğrudan erişerek birincil veya ikincil anahtarı yeniden oluşturabilir veya değiştirebilir. StorSimple Device Manager hizmeti bu değişikliği otomatik olarak görmez.

Bu değişikliği StorSimple Device Manager hizmetine bildirmek için, StorSimple Device Manager hizmetine erişmeniz, depolama hesabı kimlik bilgisine erişmeniz ve ardından birincil veya ikincil anahtarı (hangisinin değiştirildiği seçeneğe bağlı olarak) eşitlemeniz gerekir. Daha sonra hizmet en son anahtarı alır, anahtarları şifreler ve şifreli anahtarı cihaza gönderir.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>Hizmet ile aynı abonelikte bulunan depolama hesabı kimlik bilgileri anahtarlarını eşzamanlı hale getirmek için (yalnızca Azure)
1. Hizmet giriş dikey penceresinde hizmetinizi seçin, hizmet adına çift tıklayın ve ardından **yapılandırma** bölümünde **depolama hesabı kimlik bilgileri**' ne tıklayın.
2. **Depolama hesabı kimlik bilgileri** dikey penceresinde, depolama hesabı kimlik bilgileri listesinde, anahtarlarını eşitlenmesi istediğiniz depolama hesabı kimlik bilgisini seçin.
3. Seçilen depolama hesabı kimlik bilgisinin **Özellikler** dikey penceresinde aşağıdakileri yapın:
   
    1. **Daha fazla**' ya tıklayın ve ardından **erişim anahtarını Eşitle**' ye tıklayın.
   
    2. Onaylamanız istendiğinde eşitleme **anahtarı** ' na tıklayarak eşitlemeyi doldurun.
    
4. StorSimple Device Manager hizmetinde, daha önce Microsoft Azure Depolama hizmetinde değiştirilen anahtarı güncelleştirmeniz gerekir. **Depolama hesabı anahtarını Eşitle** dikey penceresinde, birincil erişim anahtarı değiştirildiyse (yeniden oluşturulursa), birincil ' a ve ardından **eşitleme anahtarı**' na tıklayın. İkincil anahtar değiştirildiyse, **İkincil**' a ve ardından **anahtarı Eşitle**' ye tıklayın.
   
    ![Erişim anahtarını Eşitle](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-access-key.png)

## <a name="next-steps"></a>Sonraki adımlar
* [StorSimple Sanal dizinizi yönetmeyi](storsimple-ova-web-ui-admin.md)öğrenin.