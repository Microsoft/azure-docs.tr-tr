---
title: Microsoft Azure StorSimple Sanal diziyi devre dışı bırakma ve silme | Microsoft Docs
description: İlk olarak devre dışı bırakarak StorSimple cihazını hizmetten kaldırmayı ve sonra silmeyi açıklar.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: a929f5bc-03e2-4b01-b925-973db236f19f
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: 84fd1a2d3d0dc4ce6960469ea3212b8ca1d4d07c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023032"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>StorSimple Sanal Dizisini devre dışı bırakma ve silme

## <a name="overview"></a>Genel Bakış

StorSimple Sanal dizisini devre dışı bıraktıktan sonra, cihazla ilgili StorSimple Device Manager hizmeti arasındaki bağlantıyı kesebilirsiniz. Bu öğreticide, aşağıdaki işlemlerin nasıl yapılacağı açıklanmaktadır:

* Cihazı devre dışı bırakma 
* Devre dışı bırakılmış bir cihazı silme

Bu makaledeki bilgiler yalnızca StorSimple Sanal dizileri için geçerlidir. 8000 serisi hakkında daha fazla bilgi için, [bir cihazı devre dışı bırakma veya silme](./storsimple-8000-deactivate-and-delete-device.md)bölümüne gidin.

## <a name="when-to-deactivate"></a>Ne zaman devre dışı bırakılacak?

Devre dışı bırakma kalıcı bir işlemdir ve geri alınamaz. Devre dışı bırakılmış bir cihazı StorSimple Device Manager hizmetine yeniden kaydedemez. Aşağıdaki senaryolarda bir StorSimple Sanal dizisinin etkinliğini kaldırmanız ve silmeniz gerekebilir:

* **Planlı Yük devretme** : cihazınız çevrimiçi ve cihazınızın yükünü devretmek için planlama yapın. Daha büyük bir cihaza yükseltmeyi planlıyorsanız cihazınızın yükünü devretmek gerekebilir. Veri sahipliği aktarıldıktan ve yük devretme işlemi tamamlandıktan sonra, kaynak cihaz otomatik olarak silinir.
* **Planlanmamış yük devretme** : cihazınız çevrimdışı ve cihazın yükünü devretmek gerekir. Bu senaryo, veri merkezinde bir kesinti olduğunda ve birincil cihazınız kapalıysa bir olağanüstü durum sırasında gerçekleşebilir. Cihazın yükünü ikincil bir cihaza devretmek için plan yapın. Veri sahipliği aktarıldıktan ve yük devretme işlemi tamamlandıktan sonra, kaynak cihaz otomatik olarak silinir.
* **Yetkisini** alma: cihazın yetkisini almak istiyorsunuz. Bu, önce cihazı devre dışı bırakmanız ve ardından silmeniz gerekir. Bir cihazı devre dışı bırakırsanız, yerel olarak depolanan verilere artık erişemezsiniz. Yalnızca bulutta depolanan verilere erişebilir ve bunları kurtarabilirsiniz. Devre dışı bıraktıktan sonra cihaz verilerini saklamayı planlıyorsanız, bir cihazı devre dışı bırakmadan önce tüm verilerinizin bulut anlık görüntüsünü almalısınız. Bu bulut anlık görüntüsü, daha sonraki bir aşamada tüm verileri kurtarmanızı sağlar.

## <a name="deactivate-a-device"></a>Cihazı devre dışı bırakma

Cihazınızı devre dışı bırakmak için aşağıdaki adımları gerçekleştirin.

#### <a name="to-deactivate-the-device"></a>Cihazı devre dışı bırakmak için

1. Hizmetinizde **yönetim > cihazlar**' a gidin. **Cihazlar** dikey penceresinde, devre dışı bırakmak istediğiniz cihazı tıklatın ve seçin.
   
    ![Devre dışı bırakılacak cihazı seçin](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. **Cihaz panosu** dikey penceresinde... öğesine tıklayın **. Daha fazla** ve listeden **devre dışı bırak**' ı seçin.
   
    ![Devre dışı bırak 'a tıklayın](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. **Devre dışı bırak** dikey penceresinde, cihaz adını yazın ve **devre dışı bırak**' a tıklayın. 
   
    ![Devre dışı bırakın Onayla](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    Devre dışı bırakma işlemi başlar ve birkaç dakika sürer.
   
    ![Devre dışı bırakma devam ediyor](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Devre dışı bırakma işleminden sonra cihaz listesi yenilenir.
   
    ![Devre dışı bırakma tamam](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    Artık bu cihazı silebilirsiniz.

## <a name="delete-the-device"></a>Cihazı silme

Bir cihazın silinmesi için önce devre dışı bırakılması gerekir. Bir cihazın silinmesi, hizmeti hizmetine bağlı cihazların listesinden kaldırır. Hizmet bundan sonra silinen cihazı yönetemez. Cihazla ilişkili veriler, ancak bulutta kalır. Bu veriler daha sonra ücretlendiriler.

Cihazı silmek için aşağıdaki adımları gerçekleştirin.

#### <a name="to-delete-the-device"></a>Cihazı silmek için

1. StorSimple Device Manager **yönetim > cihazlar**' a gidin. **Cihazlar** dikey penceresinde silmek istediğiniz devre dışı bırakılmış bir cihazı seçin.
2. **Cihaz panosu** dikey penceresinde... öğesine tıklayın **. Daha fazla bilgi** ve sonra **Sil**' e tıklayın.
   
   ![Silinecek cihazı seçin](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. **Sil** dikey penceresinde, silme işlemini onaylamak için cihazınızın adını yazın ve ardından **Sil**' e tıklayın. Cihazın silinmesi cihazla ilişkili bulut verilerini silmez. 
   
   ![Silmeyi onayla](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. Silme işlemi başlar ve birkaç dakika sürer.
   
   ![Silme devam ediyor](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    Cihaz silindikten sonra, güncelleştirilmiş cihaz listesini görebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* Yük devretme hakkında daha fazla bilgi için, [StorSimple Sanal diziniz Için yük devretme ve olağanüstü durum kurtarma](storsimple-virtual-array-failover-dr.md)bölümüne gidin.

* StorSimple Device Manager hizmetini kullanma hakkında daha fazla bilgi edinmek için StorSimple [Device Manager hizmetini kullanarak StorSimple Sanal dizinizi yönetin](storsimple-virtual-array-manager-service-administration.md).