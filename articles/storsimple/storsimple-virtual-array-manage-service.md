---
title: StorSimple Aygıt Yöneticisi hizmetini dağıtma | Microsoft Docs
description: Azure portal StorSimple Aygıt Yöneticisi hizmeti oluşturmayı ve silmeyi açıklar ve hizmet kayıt anahtarının nasıl yönetileceğini açıklar.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 28499494-8c4d-4a7f-9d44-94b2b8a93c93
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 2ff987448e3900c2a533789b2f38fd3ced71ce72
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516676"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>StorSimple Sanal dizisi için StorSimple Aygıt Yöneticisi hizmetini dağıtma

## <a name="overview"></a>Genel Bakış

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

StorSimple Aygıt Yöneticisi hizmeti Microsoft Azure ' de çalışır ve birden çok StorSimple cihaza bağlanır. Hizmeti oluşturduktan sonra, bir tarayıcıda çalışan Microsoft Azure portal cihazları yönetmek için kullanabilirsiniz. Bu, StorSimple Aygıt Yöneticisi hizmetine bağlı tüm cihazları tek ve merkezi bir konumdan izlemenize olanak tanır ve böylece yönetim yükünü en aza indirir.

StorSimple Aygıt Yöneticisi hizmetiyle ilgili ortak görevler şunlardır:

* Bir hizmet oluşturma
* Hizmet silme
* Hizmet kayıt anahtarı alma
* Hizmet kayıt anahtarını yeniden üret

Bu öğreticide, önceki görevlerden her birinin nasıl gerçekleştirileceği açıklanmaktadır. Bu makalede yer alan bilgiler yalnızca StorSimple Sanal dizileri için geçerlidir. StorSimple 8000 serisi hakkında daha fazla bilgi için, [StorSimple Yöneticisi hizmetini dağıtma](storsimple-manage-service.md)konusuna gidin.

## <a name="create-a-service"></a>Bir hizmet oluşturma

Bir hizmet oluşturmak için şunları yapmanız gerekir:

* Kurumsal Anlaşma bir abonelik
* Etkin bir Microsoft Azure depolama hesabı
* Erişim yönetimi için kullanılan faturalandırma bilgileri

Hizmeti oluştururken bir depolama hesabı oluşturmayı da tercih edebilirsiniz.

Tek bir hizmet birden çok cihazı yönetebilir. Ancak, bir cihaz birden fazla hizmete yayılamaz. Büyük bir kuruluşun farklı abonelikler, kuruluşlar veya hatta dağıtım konumları ile çalışması için birden çok hizmet örneği olabilir.

> [!NOTE]
> StorSimple 8000 serisi cihazlarını ve StorSimple Sanal dizilerini yönetmek için StorSimple Aygıt Yöneticisi hizmetinin ayrı örneklerine ihtiyacınız vardır.


Bir hizmet oluşturmak için aşağıdaki adımları gerçekleştirin.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Hizmet silme

Bir hizmeti silmeden önce, bağlı hiçbir cihazın bu uygulamayı kullandığından emin olun. Hizmet kullanımda ise, bağlı cihazları devre dışı bırakın. Devre dışı bırakma işlemi, cihaz ve hizmet arasındaki bağlantıyı bırakır, ancak buluttaki cihaz verilerini korur.

> [!IMPORTANT]
> Bir hizmet silindikten sonra işlem tersine çevrilemez. Hizmeti kullanan tüm cihazların, başka bir hizmetle kullanılabilmesi için önce fabrika sıfırlaması gerekir. Bu senaryoda, cihazdaki yerel verilerin yanı sıra yapılandırma de kaybedilir.
 

Bir hizmeti silmek için aşağıdaki adımları gerçekleştirin.

#### <a name="to-delete-a-service"></a>Bir hizmeti silmek için

1. **Tüm kaynaklar**'a gidin. StorSimple Aygıt Yöneticisi hizmetinizi arayın. Silmek istediğiniz hizmeti seçin.
   
    ![Silinecek hizmeti seçin](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Hizmete bağlı bir cihaz olmadığından emin olmak için hizmet panonuza gidin. Bu hizmete kayıtlı cihaz yoksa, efekte bir başlık iletisi de görürsünüz. Tıklayın **Sil**.
   
    ![Hizmeti sil](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Onay istendiğinde, onay bildiriminde **Evet** ' e tıklayın. 
   
    ![Hizmeti silmeyi onayla](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. Hizmetin silinmesi birkaç dakika sürebilir. Hizmet başarıyla silindikten sonra bilgilendirilir.
   
    ![Hizmet silme başarılı](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

Hizmet listesi yenilenir.

 ![Güncelleştirilmiş hizmet listesi](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Hizmet kayıt anahtarı alma
Bir hizmeti başarıyla oluşturduktan sonra StorSimple cihazınızı hizmete kaydetmeniz gerekir. İlk StorSimple cihazınızı kaydetmek için hizmet kayıt anahtarına ihtiyacınız olacaktır. Mevcut bir StorSimple hizmetine ek cihazları kaydetmek için, kayıt anahtarı ve hizmet veri şifreleme anahtarı (kayıt sırasında ilk cihazda oluşturulur) gerekir. Hizmet veri şifreleme anahtarı hakkında daha fazla bilgi için bkz. [StorSimple Security](storsimple-security.md). Hizmetiniz için **anahtarlar** dikey penceresine erişerek kayıt anahtarını alabilirsiniz.

Hizmet kayıt anahtarını almak için aşağıdaki adımları gerçekleştirin.

#### <a name="to-get-the-service-registration-key"></a>Hizmet kayıt anahtarını almak için
1. **StorSimple aygıt yöneticisi** dikey penceresinde  **&gt; yönetim** **anahtarlar**' a gidin.
   
   ![Anahtarlar dikey penceresi](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. **Anahtarlar** dikey penceresinde bir hizmet kayıt anahtarı görüntülenir. Kopyala simgesini kullanarak kayıt anahtarını kopyalayın. 

Hizmet kayıt anahtarını güvenli bir konumda saklayın. Bu hizmete ek cihaz kaydetmek için bu anahtara ve hizmet veri şifreleme anahtarına ihtiyacınız olacak. Hizmet kayıt anahtarını aldıktan sonra, StorSimple için Windows PowerShell arabirimi aracılığıyla cihazınızı yapılandırmanız gerekecektir.

## <a name="regenerate-the-service-registration-key"></a>Hizmet kayıt anahtarını yeniden üret
Anahtar dönüşü yapmanız gerekiyorsa veya hizmet yöneticileri listesi değiştiyse, bir hizmet kayıt anahtarını yeniden oluşturmanız gerekecektir. Anahtarı yeniden oluşturduğunuzda yeni anahtar yalnızca sonraki cihazları kaydetmek için kullanılır. Zaten kayıtlı olan cihazlar bu işlemden etkilenmez.

Hizmet kayıt anahtarını yeniden oluşturmak için aşağıdaki adımları gerçekleştirin.

#### <a name="to-regenerate-the-service-registration-key"></a>Hizmet kayıt anahtarını yeniden oluşturmak için
1. **StorSimple aygıt yöneticisi** dikey penceresinde  **&gt; yönetim** **anahtarlar**' a gidin.
   
   ![Anahtarlar dikey penceresi](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. **Anahtarlar** dikey penceresinde yeniden **Oluştur**' a tıklayın.
   
   ![Yeniden oluştur 'a tıklayın](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. **Hizmet kayıt anahtarını yeniden oluştur** dikey penceresinde, anahtarlar yeniden üretildiğinde gereken eylemi gözden geçirin. Bu hizmete kayıtlı tüm sonraki cihazlar yeni kayıt anahtarını kullanacaktır. Onaylamak için yeniden **Oluştur** ' a tıklayın. Kayıt tamamlandıktan sonra size bildirim alınacaktır.
   
   ![Anahtarı yeniden oluşturmayı Onayla](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Yeni bir hizmet kayıt anahtarı görüntülenir.
   
    ![Anahtarı yeniden oluşturmayı Onayla](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Bu anahtarı kopyalayın ve yeni cihazları bu hizmete kaydetmek için kaydedin.

## <a name="next-steps"></a>Sonraki adımlar
* StorSimple Sanal dizisi ile [çalışmaya başlama](storsimple-virtual-array-deploy1-portal-prep.md) hakkında bilgi edinin.
* [StorSimple cihazınızı yönetmeyi](storsimple-ova-web-ui-admin.md)öğrenin.

