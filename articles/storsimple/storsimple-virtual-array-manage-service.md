---
title: StorSimple Device Manager hizmetini dağıtma | Microsoft Docs
description: Azure portal StorSimple Device Manager hizmeti oluşturmayı ve silmeyi açıklar ve hizmet kayıt anahtarının nasıl yönetileceğini açıklar.
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
ms.openlocfilehash: 92041be4d3d5ef4d980c17fa0a5870b740c1784a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995036"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>StorSimple Sanal dizisi için StorSimple Device Manager hizmetini dağıtma

## <a name="overview"></a>Genel Bakış

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

StorSimple Device Manager hizmeti Microsoft Azure ' de çalışır ve birden çok StorSimple cihaza bağlanır. Hizmeti oluşturduktan sonra, bir tarayıcıda çalışan Microsoft Azure portal cihazları yönetmek için kullanabilirsiniz. Bu, StorSimple Device Manager hizmetine bağlı tüm cihazları tek ve merkezi bir konumdan izlemenize olanak tanır ve böylece yönetim yükünü en aza indirir.

StorSimple Device Manager hizmetiyle ilgili ortak görevler şunlardır:

* Hizmet oluşturma
* Hizmet silme
* Hizmet kayıt anahtarı alma
* Hizmet kayıt anahtarını yeniden üret

Bu öğreticide, önceki görevlerden her birinin nasıl gerçekleştirileceği açıklanmaktadır. Bu makalede yer alan bilgiler yalnızca StorSimple Sanal dizileri için geçerlidir. StorSimple 8000 serisi hakkında daha fazla bilgi için, [StorSimple Yöneticisi hizmetini dağıtma](./storsimple-8000-manage-service.md)konusuna gidin.

## <a name="create-a-service"></a>Hizmet oluşturma

Bir hizmet oluşturmak için şunları yapmanız gerekir:

* Kurumsal Anlaşma bir abonelik
* Etkin bir Microsoft Azure depolama hesabı
* Erişim yönetimi için kullanılan faturalandırma bilgileri

Hizmeti oluştururken bir depolama hesabı oluşturmayı da tercih edebilirsiniz.

Tek bir hizmet birden çok cihazı yönetebilir. Ancak, bir cihaz birden fazla hizmete yayılamaz. Büyük bir kuruluşun farklı abonelikler, kuruluşlar veya hatta dağıtım konumları ile çalışması için birden çok hizmet örneği olabilir.

> [!NOTE]
> StorSimple 8000 serisi cihazlarını ve StorSimple Sanal dizilerini yönetmek için StorSimple Device Manager hizmetinin ayrı örneklerine ihtiyacınız vardır.


Bir hizmet oluşturmak için aşağıdaki adımları gerçekleştirin.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Hizmet silme

Bir hizmeti silmeden önce, bağlı hiçbir cihazın bu uygulamayı kullandığından emin olun. Hizmet kullanımda ise, bağlı cihazları devre dışı bırakın. Devre dışı bırakma işlemi, cihaz ve hizmet arasındaki bağlantıyı bırakır, ancak buluttaki cihaz verilerini korur.

> [!IMPORTANT]
> Bir hizmet silindikten sonra işlem tersine çevrilemez. Hizmeti kullanan tüm cihazların, başka bir hizmetle kullanılabilmesi için önce fabrika sıfırlaması gerekir. Bu senaryoda, cihazdaki yerel verilerin yanı sıra yapılandırma de kaybedilir.
 

Bir hizmeti silmek için aşağıdaki adımları gerçekleştirin.

#### <a name="to-delete-a-service"></a>Bir hizmeti silmek için

1. **Tüm kaynaklar**'a gidin. StorSimple Device Manager hizmetinizi arayın. Silmek istediğiniz hizmeti seçin.
   
    ![Silinecek hizmeti seçin](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Hizmete bağlı bir cihaz olmadığından emin olmak için hizmet panonuza gidin. Bu hizmete kayıtlı cihaz yoksa, efekte bir başlık iletisi de görürsünüz. **Sil**'e tıklayın.
   
    ![Hizmeti Sil](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Onay istendiğinde, onay bildiriminde **Evet** ' e tıklayın. 
   
    ![Hizmeti silmeyi onayla](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. Hizmetin silinmesi birkaç dakika sürebilir. Hizmet başarıyla silindikten sonra bilgilendirilir.
   
    ![Hizmet silme başarılı](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

Hizmet listesi yenilenir.

 ![Güncelleştirilmiş hizmet listesi](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Hizmet kayıt anahtarı alma
Bir hizmeti başarıyla oluşturduktan sonra StorSimple cihazınızı hizmete kaydetmeniz gerekir. İlk StorSimple cihazınızı kaydetmek için hizmet kayıt anahtarına ihtiyacınız olacaktır. Mevcut bir StorSimple hizmetine ek cihazları kaydetmek için, kayıt anahtarı ve hizmet veri şifreleme anahtarı (kayıt sırasında ilk cihazda oluşturulur) gerekir. Hizmet veri şifreleme anahtarı hakkında daha fazla bilgi için bkz. [StorSimple Security](./storsimple-8000-security.md). Hizmetiniz için **anahtarlar** dikey penceresine erişerek kayıt anahtarını alabilirsiniz.

Hizmet kayıt anahtarını almak için aşağıdaki adımları gerçekleştirin.

#### <a name="to-get-the-service-registration-key"></a>Hizmet kayıt anahtarını almak için
1. **StorSimple Device Manager** dikey penceresinde **&gt; Yönetim** **anahtarlar**' a gidin.
   
   ![Anahtarlar dikey penceresi](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. **Anahtarlar** dikey penceresinde bir hizmet kayıt anahtarı görüntülenir. Kopyala simgesini kullanarak kayıt anahtarını kopyalayın. 

Hizmet kayıt anahtarını güvenli bir konumda saklayın. Bu hizmete ek cihaz kaydetmek için bu anahtara ve hizmet veri şifreleme anahtarına ihtiyacınız olacak. Hizmet kayıt anahtarını aldıktan sonra, StorSimple için Windows PowerShell arabirimi aracılığıyla cihazınızı yapılandırmanız gerekecektir.

## <a name="regenerate-the-service-registration-key"></a>Hizmet kayıt anahtarını yeniden üret
Anahtar dönüşü yapmanız gerekiyorsa veya hizmet yöneticileri listesi değiştiyse, bir hizmet kayıt anahtarını yeniden oluşturmanız gerekecektir. Anahtarı yeniden oluşturduğunuzda yeni anahtar yalnızca sonraki cihazları kaydetmek için kullanılır. Zaten kayıtlı olan cihazlar bu işlemden etkilenmez.

Hizmet kayıt anahtarını yeniden oluşturmak için aşağıdaki adımları gerçekleştirin.

#### <a name="to-regenerate-the-service-registration-key"></a>Hizmet kayıt anahtarını yeniden oluşturmak için
1. **StorSimple Device Manager** dikey penceresinde **&gt; Yönetim** **anahtarlar**' a gidin.
   
   ![Anahtarlar dikey penceresi yeniden](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. **Anahtarlar** dikey penceresinde yeniden **Oluştur**' a tıklayın.
   
   ![Yeniden oluştur 'a tıklayın](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. **Hizmet kayıt anahtarını yeniden oluştur** dikey penceresinde, anahtarlar yeniden üretildiğinde gereken eylemi gözden geçirin. Bu hizmete kayıtlı tüm sonraki cihazlar yeni kayıt anahtarını kullanacaktır. Onaylamak için yeniden **Oluştur** ' a tıklayın. Kayıt tamamlandıktan sonra size bildirim alınacaktır.
   
   ![Anahtarı yeniden oluşturmayı Onayla](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Yeni bir hizmet kayıt anahtarı görüntülenir.
   
    ![Ek onaylama yeniden üretme anahtarı](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Bu anahtarı kopyalayın ve yeni cihazları bu hizmete kaydetmek için kaydedin.

## <a name="next-steps"></a>Sonraki adımlar
* StorSimple Sanal dizisi ile [çalışmaya başlama](storsimple-virtual-array-deploy1-portal-prep.md) hakkında bilgi edinin.
* [StorSimple cihazınızı yönetmeyi](storsimple-ova-web-ui-admin.md)öğrenin.