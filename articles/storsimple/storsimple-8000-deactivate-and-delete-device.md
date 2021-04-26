---
title: StorSimple 8000 serisi cihazı devre dışı bırakma ve silme | Microsoft Docs
description: StorSimple Device Manager hizmetine bağlı bir StorSimple cihazını devre dışı bırakıp silmeyi öğrenin.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2018
ms.author: alkohli
ms.openlocfilehash: 07d108306fdca9bfe8f793b61660550e43151d71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017194"
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>StorSimple cihazını devre dışı bırakma ve silme

## <a name="overview"></a>Genel Bakış

Bu makalede, StorSimple Device Manager hizmetine bağlı bir StorSimple cihazını devre dışı bırakma ve silme işlemlerinin nasıl yapılacağı açıklanır. Bu makaledeki kılavuz yalnızca StorSimple bulut gereçlerini içeren StorSimple 8000 serisi cihazlar için geçerlidir. StorSimple Sanal dizisi kullanıyorsanız, [devre dışı bırak ' a gidin ve StorSimple Sanal dizisini silin](storsimple-virtual-array-deactivate-and-delete-device.md).

Cihaz ve ilgili StorSimple Device Manager hizmeti arasındaki bağlantıyı devre dışı bırakma. Bir StorSimple cihazını hizmetten alabilirsiniz (örneğin, cihazınızı değiştiriyorsanız veya yükseltiyorsanız veya artık StorSimple kullanmıyorsanız). Bu durumda, silmeden önce cihazı devre dışı bırakmanız gerekir.

Bir cihazı devre dışı bıraktıktan sonra, cihazda yerel olarak depolanan verilere artık erişilemez. Yalnızca bulutta depolanan cihazla ilişkili veriler kurtarılabilir.

> [!WARNING]
> Devre dışı bırakma kalıcı bir işlemdir ve geri alınamaz. Devre dışı bırakılmış bir cihaz, fabrika varsayılanlarına Sıfırlanmadığı takdirde StorSimple Device Manager hizmetine kaydedilemez.
>
> Fabrika sıfırlama işlemi, cihazınızda yerel olarak depolanan tüm verileri siler. Bu nedenle, bir cihazı devre dışı bırakmadan önce tüm verilerinizin bulut anlık görüntüsünü almalısınız. Bu bulut anlık görüntüsü, daha sonraki bir aşamada tüm verileri kurtarmanızı sağlar.

> [!NOTE]
>
> - StorSimple fiziksel cihazını veya bulut gerecini devre dışı bırakmadan önce, silinen birim kapsayıcısından gelen verilerin cihazdan gerçekten silindiğinden emin olun. Bulut tüketim grafiklerini izleyebilir ve sildiğiniz yedeklemeler nedeniyle bulut kullanımı ' nı gördüğünüzde, cihazı devre dışı bırakmaya devam edebilirsiniz. Bu bırakma yapılmadan önce cihazı devre dışı bırakırsanız, veriler depolama hesabı ve tahakkuk ücretleri ' nde kasalar.
>
> - StorSimple fiziksel cihazını veya bulut gerecini devre dışı bırakmadan önce, söz konusu cihaza bağlı istemcileri ve Konakları durdurun veya silin.
>
> - Depolama hesapları veya birim kapsayıcılarıyla ilişkili depolama hesabındaki kapsayıcılar, verileri cihazdan silmeden önce zaten silinirse, bir hata alırsınız ve verileri silemeymeyebilirsiniz. Depolamadaki depolama hesabı veya kapsayıcılarını silmeden önce Cihazdaki verileri silmenizi öneririz. Ancak, bu durumda, verilerin depolama hesabından zaten kaldırıldığından emin olarak cihaz devre dışı bırakma ve silme işlemine devam etmeniz gerekir.

Bu öğreticiyi okuduktan sonra şunları yapabilirsiniz:

- Bir cihazı devre dışı bırakın ve verileri silin.
- Bir cihazı devre dışı bırakın ve verileri koruyun.

## <a name="deactivate-and-delete-data"></a>Verileri devre dışı bırakma ve silme

Cihazı tamamen silmek ve Cihazdaki verileri sürdürmek istemiyorsanız, aşağıdaki adımları uygulayın ve ardından aşağıdaki adımları izleyin.

### <a name="to-deactivate-the-device-and-delete-the-data"></a>Cihazı devre dışı bırakmak ve verileri silmek için

1. Bir cihazı devre dışı bırakmadan önce, cihazla ilişkili tüm birim kapsayıcılarını (ve birimleri) silmeniz gerekir. Birim kapsayıcılarını yalnızca ilişkili yedeklemeleri sildikten sonra silebilirsiniz. StorSimple fiziksel cihazını veya bulut gerecini devre dışı bırakmadan önce yukarıdaki Genel Bakış bölümündeki nota bakın.

2. Cihazı şu şekilde devre dışı bırakın:

   1. StorSimple Cihaz Yöneticisi hizmetinize gidin ve **Cihazlar**’a tıklayın. **Cihazlar** dikey penceresinde, devre dışı bırakmak istediğiniz cihazı seçin, sağ tıklayın ve ardından **devre dışı bırak**' a tıklayın.

        ![StorSimple cihazını devre dışı bırak](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. **Devre dışı bırak** dikey penceresinde, onaylamak için cihaz adını yazın ve **devre dışı bırak**' a tıklayın. Devre dışı bırakma işlemi başlar ve birkaç dakika sürer.

        ![StorSimple cihazını devre dışı bırak 2](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Devre dışı bırakma işleminden sonra cihazı tamamen silebilirsiniz. Bir cihazın silinmesi, hizmeti hizmetine bağlı cihazların listesinden kaldırır. Hizmet bundan sonra silinen cihazı yönetemez. Cihazı silmek için aşağıdaki adımları kullanın:
   
   1. StorSimple Cihaz Yöneticisi hizmetinize gidin ve **Cihazlar**’a tıklayın. **Cihazlar** dikey penceresinde silmek istediğiniz devre dışı bırakılmış cihazı seçin, sağ tıklayın ve ardından **Sil**' e tıklayın.

        ![StorSimple cihazını devre dışı bırak 3](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. **Sil** dikey penceresinde, onaylamak için cihaz adını yazın ve ardından **Sil**' e tıklayın. Silmenin tamamlanabilmesi birkaç dakika sürer.

        ![StorSimple cihazını devre dışı bırak 4](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Silme işlemi başarıyla tamamlandıktan sonra size bildirilir. Cihaz listesi ayrıca silme işlemini yansıtacak şekilde güncelleştirilir.

## <a name="deactivate-and-retain-data"></a>Verileri devre dışı bırakma ve koruma

Cihazı silmek, ancak verileri sürdürmek istiyorsanız aşağıdaki adımları izleyin:

### <a name="to-deactivate-a-device-and-retain-the-data"></a>Bir cihazı devre dışı bırakmak ve verileri sürdürmek için

1. Cihazı devre dışı bırakın. Tüm birim kapsayıcıları ve cihazın anlık görüntüleri kalır.
   
   1. StorSimple Cihaz Yöneticisi hizmetinize gidin ve **Cihazlar**’a tıklayın. **Cihazlar** dikey penceresinde, devre dışı bırakmak istediğiniz cihazı seçin, sağ tıklayın ve ardından **devre dışı bırak**' a tıklayın.

         ![StorSimple cihazını devre dışı bırakma 5](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. **Devre dışı bırak** dikey penceresinde, onaylamak için cihaz adını yazın ve **devre dışı bırak**' a tıklayın. Devre dışı bırakma işlemi başlar ve birkaç dakika sürer.

         ![StorSimple cihazını devre dışı bırakma 6](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. Artık birim kapsayıcılarının ve ilişkili anlık görüntülerin yükünü devreedebilirsiniz. Yordamlar için, [StorSimple cihazınız Için yük devretme ve olağanüstü durum kurtarma](storsimple-8000-device-failover-disaster-recovery.md)bölümüne gidin.
3. Devre dışı bırakma ve yük devretme işleminden sonra cihazı tamamen silebilirsiniz. Bir cihazın silinmesi, hizmeti hizmetine bağlı cihazların listesinden kaldırır. Hizmet bundan sonra silinen cihazı yönetemez. Cihazı silmek için aşağıdaki adımları izleyin:
   
   1. StorSimple Cihaz Yöneticisi hizmetinize gidin ve **Cihazlar**’a tıklayın. **Cihazlar** dikey penceresinde silmek istediğiniz devre dışı bırakılmış cihazı seçin, sağ tıklayın ve ardından **Sil**' e tıklayın.

       ![StorSimple cihazını devre dışı bırakma 7](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. **Sil** dikey penceresinde, onaylamak için cihaz adını yazın ve ardından **Sil**' e tıklayın. Silmenin tamamlanabilmesi birkaç dakika sürer.

       ![StorSimple cihaz 8 ' i devre dışı bırak](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Silme işlemi başarıyla tamamlandıktan sonra size bildirilir. Cihaz listesi ayrıca silme işlemini yansıtacak şekilde güncelleştirilir.

## <a name="deactivate-and-delete-a-cloud-appliance"></a>Bulut cihazını devre dışı bırakma ve silme

StorSimple Cloud Appliance için portaldan devre dışı bırakma işlemi, sanal makineyi ve sağlandığında oluşturulan kaynakları kaldırır ve siler. Bulut gereci devre dışı bırakıldıktan sonra, önceki durumuna geri yüklenemez.

![StorSimple Cloud Appliance devre dışı bırak](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

Aşağıdaki eylemlerde devre dışı bırakma sonuçları:

* StorSimple Cloud Appliance hizmetten kaldırılır.
* StorSimple Cloud Appliance sanal makinesi silindi.
* StorSimple Cloud Appliance için oluşturulan işletim sistemi diski ve veri diskleri korunur. Bu varlıkları kullanmıyorsanız, bunları el ile silmelisiniz.
* Sağlama sırasında oluşturulan barındırılan hizmet ve sanal ağ tutulur. Bu varlıkları kullanmıyorsanız, bunları el ile silmelisiniz.
* StorSimple Cloud Appliance tarafından oluşturulan bulut anlık görüntüleri korunur.

Bulut gereci devre dışı bırakıldıktan sonra cihaz listesinden silebilirsiniz. Devre dışı bırakılmış cihazı seçin, sağ tıklayın ve ardından **Sil**' e tıklayın. StorSimple, cihaz silindikten ve cihazların listesi güncelleştikten sonra size bildirir.

## <a name="next-steps"></a>Sonraki adımlar

* Devre dışı bırakılan cihazı fabrika ayarlarına geri yüklemek için, [cihazı varsayılan fabrika ayarlarına sıfırlama](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings)bölümüne gidin.
* Teknik yardım için [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md).
* StorSimple Device Manager hizmetini kullanma hakkında daha fazla bilgi edinmek için, StorSimple [cihazınızı yönetmek için storsimple Device Manager hizmetini kullanma](storsimple-8000-manager-service-administration.md)bölümüne gidin.

