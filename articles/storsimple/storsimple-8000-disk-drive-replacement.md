---
title: StorSimple 8000 serisi cihazındaki bir disk sürücüsünü değiştirme | Microsoft Docs
description: Bir StorSimple birincil kasasında veya bir EBOD muhafazasında disk sürücüsünün nasıl değiştirileceğini açıklar.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 8/25/2017
ms.author: alkohli
ms.openlocfilehash: a5a7a31c174271f963bb005f10b8376a59133d36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014882"
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>StorSimple 8000 serisi cihazınızın disk sürücüsünü değiştirme

## <a name="overview"></a>Genel Bakış
Bu öğreticide, bir Microsoft Azure StorSimple cihazında hatalı çalışan veya başarısız bir sabit disk sürücüsünü nasıl kaldırabileceğiniz ve değiştireceğiniz açıklanmaktadır. Bir disk sürücüsünü değiştirmek için şunları yapmanız gerekir:

* Antideme kilidine katıl
* Disk sürücüsünü kaldırma
* Değiştirilen disk sürücüsünü yükler

> [!IMPORTANT]
> Bir disk sürücüsünü kaldırmadan ve değiştirmeden önce, [StorSimple donanım bileşeni değiştirme](storsimple-8000-hardware-component-replacement.md)içindeki güvenlik bilgilerini gözden geçirin.
 

## <a name="disengage-the-antitamper-lock"></a>Antideme kilidine katıl
Bu yordamda, bir disk sürücüsünü değiştirdiğiniz zaman StorSimple cihazındaki kolay kullanılabilir kilit açma işlemlerinin nasıl yapılacağı ve üzerinde boşta olduğu açıklanmaktadır. Yeniden kullanılabilir kilitler, sürücü taşıyıcı tanıtıcılarının içine alınır ve tanıtıcının mandal bölümünde küçük bir açıklık üzerinden erişilir. Diskler kilitli konuma ayarlanmış olan kilitler ile sağlanır.

#### <a name="to-unlock-the-antitamper-lock"></a>Antibir kilit kilidini açmak için
1. Kilit anahtarını (Microsoft tarafından sunulan bir "wıerkanıtı" T10 screwdriver) tanıtıcıdaki açıklık ve yuvasına dikkatle ekleyin. 
   
   Antietkin kilit etkinleştirilirse, kırmızı gösterge açıklık içinde görünür.
  
    ![Kilitli disk sürücüsü](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **Şekil 1** Yetkisiz kilit, meşgul
   
   | Etiketle | Açıklama |
   |:--- |:--- |
   | 1 |Gösterge açıklık |
   | 2 |Antibir kilit |
2. Kırmızı gösterge anahtarın üzerindeki açıklık içinde görünene kadar anahtarı saat yönünde bir yönde döndürün.
3. Anahtarı kaldırın.
   
    ![Kilidi açılmış disk sürücüsü](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **Şekil 2** Kilidi açılmış disk sürücüsü
4. Disk sürücüsü artık kaldırılabilir.

Kilidi ilerlemek için ters adımları izleyin.

## <a name="remove-the-disk-drive"></a>Disk sürücüsünü kaldırma
StorSimple cihazınız, RAID 10 benzeri bir depolama alanları yapılandırmasını destekler. Bu, bir hatalı disk, katı hal sürücüsü (SSD) veya sabit disk sürücüsü (HDD) ile normal şekilde çalışılabilmesini gerektirir.

> [!IMPORTANT]
> * Sisteminizde birden fazla başarısız disk varsa, her zaman bir noktada sistemden birden fazla SSD veya HDD kaldırmayın. Bunun yapılması veri kaybına neden olabilir.
> * Daha önce bir SSD içeren bir yuvaya bir değiştirme SSD yerleştirdiğinizden emin olun. Benzer şekilde, bir yedek HDD 'yi daha önce HDD içeren bir yuvaya yerleştirin.
> * Azure portal, yuvalar 0 – 11 ' den numaralandırılır. Bu nedenle, Portal Yuva 2 ' deki bir diskin başarısız olduğunu gösteriyorsa, cihazda sol üstteki üçüncü yuvada başarısız olan diski arayın.
> 
> 

Sistem çalışırken sürücüler kaldırılabilir ve değiştirilebilir.

#### <a name="to-remove-a-drive"></a>Bir sürücüyü kaldırmak için
1. Başarısız diski belirlemek için Azure portal, **donanım sağlığı > cihaz ayarlarınıza** gidin. Bir disk birincil kasada ve/veya bir EBOD muhafazasında (8600 modeli kullanıyorsanız) başarısız olabileceğinden, **paylaşılan bileşenler** altındaki disklerin durumuna ve **ebod paylaşılan bileşenleri**' ne bakın. İki kasada de başarısız olan bir disk, kırmızı bir durumla gösterilir.
2. Birincil kutunun önünde veya EBOD Kasası ' nın önündeki sürücüleri bulun. 
3. Diskin kilidi açıksa, sonraki adıma geçin. Disk kilitliyse, daha fazla kaldırma [kilidi](#disengage-the-antitamper-lock)ile olan yordamı izleyerek bu yordamın kilidini açın.
4. Sürücü taşıyıcısı modülünde Siyah mandal ' a basın ve sürücü taşıyıcısı tutamağını kasanın önüne ve dışına çekin.
   
    ![Disk sürücüsü tanıtıcısı serbest bırakılıyor](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **Şekil 3** Sürücü tanıtıcısı serbest bırakılıyor
5. Sürücü taşıyıcı tutamacı tamamen genişletildiğinde, sürücü taşıyıcısı kasadan dışarı kaydırın. 
   
    ![Disk sürücüsünde kayan disk kalmadı](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **Şekil 4** Disk sürücüsünü taşıyıcının dışına kaydırma

## <a name="install-the-replacement-disk-drive"></a>Değiştirilen disk sürücüsünü yükler
StorSimple cihazınızda bir sürücü başarısız olduktan sonra dosyayı kaldırdıktan sonra, yeni bir sürücüyle değiştirmek için bu yordamı izleyin.

#### <a name="to-insert-a-drive"></a>Sürücü eklemek için
1. Aşağıdaki görüntüde gösterildiği gibi, sürücü taşıyıcısı tanıtıcısının tam olarak genişletildiğinden emin olun.
   
    ![Tanıtıcı genişletilmiş disk sürücüsü](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **Şekil 5** Tanıtıcı genişletilmiş sürücü
2. Sürücü taşıyıcısı ' i kasaya kaydır.
   
    ![Diski disk sürücüsü taşıyıcısı içine kaydırma](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **Şekil 6**  Sürücü taşıyıcısı kasaya kaydırma
3. Sürücü taşıyıcısı takıldığında sürücü taşıyıcısı, sürücü taşıyıcı tanıtıcısı kilitli bir konuma yaslanana kadar, sürücü taşıyıcısı gövdeye göndermeye devam ederken sürücü taşıyıcısı tanıtıcısını kapatın.
4. Kilitleme vidalı 'yı saatin tersi yönde kapatarak, taşıyıcı tutamacının yerine getirmek için Microsoft tarafından sunulan kilit anahtarını kullanın.
5. Değiştirme işleminin başarılı olduğunu ve sürücünün çalışır durumda olduğunu doğrulayın. Azure Portal erişin ve **cihaz ayarları**  >  **donanım sistem durumu**' na gidin. **Paylaşılan bileşenler** veya **ebod paylaşılan bileşenleri** altında sürücü durumu yeşil olmalıdır ve iyi durumda olduğunu gösterir.

   
   > [!NOTE]
   > Değiştirme sonrasında disk durumunun yeşil olması birkaç saat sürebilir.
  
## <a name="next-steps"></a>Sonraki adımlar
[StorSimple donanım bileşeni değiştirme](storsimple-8000-hardware-component-replacement.md)hakkında daha fazla bilgi edinin.

