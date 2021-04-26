---
title: Depolama hesabı kimlik bilgilerini yönetme, StorSimple 8000 serisi cihaz
description: Bir depolama hesabı için güvenlik anahtarlarını eklemek, düzenlemek, silmek veya döndürmek için StorSimple Device Manager Yapılandır sayfasını nasıl kullanabileceğinizi açıklar.
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
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 0df0c12d5942179e2492bfdc932fa25d126a2426
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016888"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>Depolama hesabı kimlik bilgilerinizi yönetmek için StorSimple Device Manager hizmetini kullanın

## <a name="overview"></a>Genel Bakış

StorSimple Device Manager hizmeti dikey penceresindeki **yapılandırma** bölümü, storsimple Device Manager hizmetinde oluşturulabilen tüm genel hizmet parametrelerini gösterir. Bu parametreler hizmete bağlı tüm cihazlara uygulanabilir ve şunları içerir:

* Depolama hesabı kimlik bilgileri
* Bant genişliği şablonları 
* Erişim denetimi kayıtları 

Bu öğreticide depolama hesabı kimlik bilgilerini ekleme, düzenleme veya silme ya da bir depolama hesabı için güvenlik anahtarlarını döndürme işlemleri açıklanmaktadır.

 ![Depolama hesabı kimlik bilgilerinin listesi 1](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

Depolama hesapları, StorSimple cihazının bulut hizmeti sağlayıcınızda depolama hesabınıza erişmek için kullandığı kimlik bilgilerini içerir. Microsoft Azure depolama hesapları için, bunlar hesap adı ve birincil erişim anahtarı gibi kimlik bilgileridir. 

**Depolama hesabı kimlik bilgileri** dikey penceresinde, faturalandırma aboneliği için oluşturulan tüm depolama hesapları aşağıdaki bilgileri içeren bir tablo biçiminde görüntülenir:

* **Name** : hesap oluşturulduğunda hesaba atanan benzersiz ad.
* **SSL etkin** – TLS 'nin etkinleştirilip etkinleştirilmediğini ve cihazdan buluta iletişimin güvenli kanal üzerinden mi olduğunu belirtir.
* **Kullanım** alanı: depolama hesabını kullanan birim sayısı.

Gerçekleştirilebileceği depolama hesaplarıyla ilgili en yaygın görevler şunlardır:

* Depolama hesabı ekleme 
* Depolama hesabını düzenleme 
* Bir depolama hesabını silme 
* Depolama hesaplarının anahtar dönüşü 

## <a name="types-of-storage-accounts"></a>Depolama hesabı türleri

StorSimple cihazlarınızla kullanılabilecek üç tür depolama hesabı vardır.

* **Otomatik olarak oluşturulan depolama hesapları** – ad ' yi önerdiğinde, bu depolama hesabı türü, hizmet ilk oluşturulduğunda otomatik olarak oluşturulur. Bu depolama hesabının nasıl oluşturulduğu hakkında daha fazla bilgi edinmek için bkz. 1. Adım: Şirket [Içi StorSimple cihazınızı dağıtma](storsimple-8000-deployment-walkthrough-u2.md)bölümünde [Yeni bir hizmet oluşturma](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) . 
* **Hizmet aboneliğindeki depolama hesapları** : Bunlar, hizmet ile aynı abonelikle ilişkili Azure depolama hesaplarıdır. Bu depolama hesaplarının nasıl oluşturulduğu hakkında daha fazla bilgi edinmek için bkz. [Azure depolama hesapları hakkında](../storage/common/storage-account-create.md). 
* **Hizmet aboneliği dışındaki depolama hesapları** : Bunlar, hizmetiniz ile ilişkilendirilmemiş ve hizmet oluşturulmadan önce büyük olasılıkla var olan Azure depolama hesaplarıdır.

## <a name="add-a-storage-account"></a>Depolama hesabı ekleme

Depolama hesabına bağlı benzersiz bir kolay ad ve erişim kimlik bilgileri sağlayarak (belirtilen bulut hizmeti sağlayıcısıyla) bir depolama hesabı ekleyebilirsiniz. Ayrıca, cihazınız ve bulut arasında ağ iletişimi için güvenli bir kanal oluşturmak üzere daha önce Güvenli Yuva Katmanı (SSL) modu olarak bilinen Aktarım Katmanı Güvenliği (TLS) modunu etkinleştirme seçeneğiniz de vardır.

Belirli bir bulut hizmeti sağlayıcısı için birden çok hesap oluşturabilirsiniz. Ancak, bir depolama hesabı oluşturulduktan sonra bulut hizmeti sağlayıcısını değiştiremezsiniz.

Depolama hesabı kaydedilirken hizmet, bulut hizmeti sağlayıcınızla iletişim kurmayı dener. Sağladığınız kimlik bilgileri ve erişim malzemesinin kimliği şu anda doğrulanır. Bir depolama hesabı yalnızca kimlik doğrulaması başarılı olursa oluşturulur. Kimlik doğrulaması başarısız olursa, uygun bir hata iletisi görüntülenir.

Azure depolama hesabı kimlik bilgilerini eklemek için aşağıdaki yordamları kullanın:

* Device Manager hizmeti ile aynı Azure aboneliğine sahip bir depolama hesabı kimlik bilgisi eklemek için
* Device Manager hizmeti aboneliğinin dışında bir Azure depolama hesabı kimlik bilgisi eklemek için

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>StorSimple Device Manager hizmeti aboneliğinin dışında bir Azure depolama hesabı kimlik bilgisi eklemek için

1. StorSimple Device Manager hizmetinize gidin, seçin ve çift tıklayın. Bu, **genel bakış** dikey penceresini açar.
2. **Yapılandırma** bölümünde **depolama hesabı kimlik bilgilerini** seçin. Bu, StorSimple Device Manager hizmetiyle ilişkili mevcut depolama hesabı kimlik bilgilerini listeler.
3. **Ekle**'ye tıklayın.
4. **Depolama hesabı kimlik bilgisi ekle** dikey penceresinde aşağıdakileri yapın:
   
    1. **Abonelik** için **diğer**' i seçin.
   
    2. Azure depolama hesabı kimlik bilgilerinizin adını belirtin.
   
    3. **Depolama hesabı erişim anahtarı** metin kutusunda, Azure depolama hesabı kimlik bilgilerinizin birincil erişim anahtarını sağlayın. Bu anahtarı almak için Azure depolama hizmeti ' ne gidin, depolama hesabı kimlik bilgilerinizi seçin ve **Hesap anahtarlarını Yönet**' e tıklayın. Artık birincil erişim anahtarını kopyalayabilirsiniz.
   
    4. TLS 'i etkinleştirmek için, StorSimple Device Manager hizmetiniz ve bulut arasında ağ iletişimi için güvenli bir kanal oluşturmak üzere **Etkinleştir** düğmesine tıklayın. Yalnızca özel bir bulutta çalışıyorsanız **devre dışı bırak** düğmesine tıklayın.
   
    5. **Ekle**'ye tıklayın. Depolama hesabı kimlik bilgisi başarıyla oluşturulduktan sonra bilgilendirilirsiniz.

5. Yeni oluşturulan depolama hesabı kimlik bilgileri, **depolama hesabı kimlik bilgileri** altındaki StorSimple yapılandırma Device Manager hizmeti dikey penceresinde görüntülenir.
   


## <a name="edit-a-storage-account"></a>Depolama hesabını düzenleme

Birim kapsayıcısı tarafından kullanılan bir depolama hesabını düzenleyebilirsiniz. Şu anda kullanımda olan bir depolama hesabını düzenlerseniz, yalnızca değişiklik için kullanılabilen tek alan, depolama hesabının erişim anahtarıdır. Yeni depolama erişim anahtarını belirtebilir ve güncelleştirilmiş ayarları kaydedebilirsiniz.

#### <a name="to-edit-a-storage-account"></a>Bir depolama hesabını düzenlemek için

1. StorSimple Cihaz Yöneticisi hizmetinize gidin. **Yapılandırma** bölümünde **Depolama hesabı kimlik bilgileri**’ne tıklayın.

    ![Depolama hesabı kimlik bilgileri 2](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. **Depolama hesabı kimlik bilgileri** dikey penceresinde, depolama hesabı kimlik bilgileri listesinden, düzenlemek istediğiniz bir tane seçin ve tıklayın. 

3. **SSL 'Yi etkinleştir** seçimini değiştirebilirsiniz. Ayrıca, **daha fazla...** ' a tıklayabilir ve ardından, depolama hesabı erişim anahtarlarınızı **döndürmek için eşitleme erişim anahtarı** ' nı seçebilirsiniz. Anahtar dönüşü gerçekleştirme hakkında daha fazla bilgi için [depolama hesaplarının anahtar dönüşüyle](#key-rotation-of-storage-accounts) gidin. Ayarları değiştirdikten sonra **Kaydet**' e tıklayın. 

    ![Düzenlenmiş depolama hesabı kimlik bilgilerini kaydet](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Onayınız istendiğinde **Evet**’e tıklayın. 

    ![Değişiklikleri Onayla](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

Ayarlar, depolama hesabınız için güncelleştirilir ve kaydedilir. 

## <a name="delete-a-storage-account"></a>Bir depolama hesabını silme

> [!IMPORTANT]
> Bir depolama hesabını, yalnızca bir birim kapsayıcısı tarafından kullanılmıyorsa silebilirsiniz. Bir depolama hesabı bir birim kapsayıcısı tarafından kullanılıyorsa, önce birim kapsayıcısını silin ve ardından ilişkili depolama hesabını silin.

#### <a name="to-delete-a-storage-account"></a>Bir depolama hesabını silmek için

1. StorSimple Cihaz Yöneticisi hizmetinize gidin. **Yapılandırma** bölümünde **Depolama hesabı kimlik bilgileri**’ne tıklayın.

2. Depolama hesapları tablosal listesinde, silmek istediğiniz hesabın üzerine gelin. Bağlam menüsünü çağırmak için sağ tıklayın ve **Sil**' e tıklayın.

    ![Depolama hesabı kimlik bilgilerini silme](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. Onay istendiğinde, silme işlemine devam etmek için **Evet** ' e tıklayın. Tablo listesi, değişiklikleri yansıtacak şekilde güncelleştirilir.

    ![Silmeyi onayla](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Depolama hesaplarının anahtar dönüşü

Güvenlik nedenleriyle, anahtar döndürme genellikle veri merkezlerinde gereksinimdir. Her Microsoft Azure aboneliğinin bir veya daha fazla ilişkili depolama hesabı olabilir. Bu hesaplara erişim, her depolama hesabı için abonelik ve erişim anahtarları tarafından denetlenir. 

Bir depolama hesabı oluşturduğunuzda, Microsoft Azure depolama hesabına erişildiğinde kimlik doğrulama için kullanılan 2 512 bitlik depolama erişim tuşları üretir. İki depolama erişim anahtarı olması, depolama hizmetinize herhangi bir kesinti olmadan veya bu hizmete erişebilmeniz için anahtarları yeniden oluşturmanız sağlar. Şu anda kullanılmakta olan anahtar *birincil* anahtardır ve yedekleme anahtarı *İkincil* anahtar olarak adlandırılır. Microsoft Azure StorSimple cihazınız bulut depolama hizmeti sağlayıcınıza eriştiğinde, bu iki anahtardan biri sağlanmalıdır.

## <a name="what-is-key-rotation"></a>Anahtar döndürme nedir?

Genellikle, uygulamalar verilerinize erişmek için anahtarlardan yalnızca birini kullanır. Belirli bir süre sonra, uygulamalarınızın ikinci anahtarı kullanmak için üzerine geçiş yapabilirsiniz. Uygulamalarınızı ikincil anahtara geçirdikten sonra, ilk anahtarı devre dışı bırakabilir ve sonra yeni bir anahtar oluşturabilirsiniz. Bu şekilde iki anahtar kullanılması, uygulamalarınızın kapalı kalma süresi olmadan verilere erişmesine olanak sağlar.

Depolama hesabı anahtarları, her zaman hizmetinde şifrelenmiş bir biçimde depolanır. Ancak bunlar StorSimple Device Manager hizmeti aracılığıyla sıfırlanabilir. Hizmet, depolama hizmetinde oluşturulan hesaplar ve StorSimple Device Manager hizmeti ilk oluşturulduğunda oluşturulan varsayılan depolama hesapları da dahil olmak üzere, aynı abonelikteki tüm depolama hesapları için birincil anahtarı ve ikincil anahtarı alabilir. StorSimple Device Manager hizmeti, bu anahtarları Azure klasik portalından alacak ve sonra şifrelenmiş bir biçimde depolacaktır.

## <a name="rotation-workflow"></a>Döndürme iş akışı

Microsoft Azure Yöneticisi, depolama hesabına (Microsoft Azure Depolama hizmeti aracılığıyla) doğrudan erişerek birincil veya ikincil anahtarı yeniden oluşturabilir veya değiştirebilir. StorSimple Device Manager hizmeti bu değişikliği otomatik olarak görmez.

Bu değişikliğin StorSimple Device Manager hizmetini bilgilendirmek için, StorSimple Device Manager hizmetine erişmeniz, depolama hesabına erişmeniz ve ardından birincil veya ikincil anahtarı eşitlemeniz gerekir (hangisinin değiştirildiği bağlı olarak). Daha sonra hizmet en son anahtarı alır, anahtarları şifreler ve şifreli anahtarı cihaza gönderir.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>Hizmet ile aynı abonelikte bulunan depolama hesaplarının anahtarlarını eşleştirmek için 
1. StorSimple Cihaz Yöneticisi hizmetinize gidin. **Yapılandırma** bölümünde **Depolama hesabı kimlik bilgileri**’ne tıklayın.
2. Depolama hesaplarının tablolu listesinden, değiştirmek istediğiniz öğesine tıklayın. 

    ![anahtarları Synchronize](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Tıklayın **... Daha fazla** ve ardından, **döndürmek için eşitleme erişim anahtarını** seçin.   

    ![anahtarları Synchronize 2](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. StorSimple Device Manager hizmetinde, daha önce Microsoft Azure Depolama hizmetinde değiştirilen anahtarı güncelleştirmeniz gerekir. Birincil erişim anahtarı değiştirildiyse (yeniden oluşturulursa), **birincil** anahtar ' ı seçin. İkincil anahtar değiştirildiyse, **İkincil** anahtar ' ı seçin. **Eşitleme anahtarı**' na tıklayın.
      
      ![anahtarları Synchronize 3](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

Anahtar başarıyla eşitlendikten sonra size bildirimde bulunulacaktır.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Hizmet aboneliği dışındaki depolama hesaplarına ait anahtarları eşitlemeye yönelik
1. **Hizmetler** sayfasında, **Yapılandır** sekmesine tıklayın.
2. **Depolama hesapları Ekle/Düzenle**' ye tıklayın.
3. İletişim kutusunda, şunları yapın:
   
   1. Güncelleştirmek istediğiniz erişim anahtarına sahip depolama hesabını seçin.
   2. StorSimple Device Manager hizmetindeki depolama erişim anahtarını güncelleştirmeniz gerekir. Bu durumda, depolama erişim anahtarını görebilirsiniz. Yeni anahtarı **depolama hesabı erişim anahtarı** kutusuna girin. 
   3. Yaptığınız değişiklikleri kaydedin. Depolama hesabı erişim anahtarınız şimdi güncelleştiriliyor.

## <a name="next-steps"></a>Sonraki adımlar
* [StorSimple güvenliği](storsimple-8000-security.md)hakkında daha fazla bilgi edinin.
* StorSimple [cihazınızı yönetmek Için storsimple Device Manager hizmetini kullanma](storsimple-8000-manager-service-administration.md)hakkında daha fazla bilgi edinin.