---
title: StorSimple Sanal dizisi yedeklemesini kopyalama | Microsoft Docs
description: Bir yedeklemeyi klonlamak ve StorSimple Sanal dizinizdeki bir dosyayı kurtarmak hakkında bilgi edinin.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: af6e979c-55e3-477c-b53e-a76a697f80c9
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: b6cc89cb082cd0ed32abd88e3a6683c60a27ba90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023049"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>StorSimple Sanal dizininizin bir yedeklemesinden kopyalayın

## <a name="overview"></a>Genel Bakış

Bu makalede, Microsoft Azure StorSimple Sanal dizininizdeki paylaşımlarınızın veya birimlerinizin yedek kümesini kopyalama adım adım açıklanmaktadır. Kopyalanmış yedekleme, silinen veya kayıp bir dosyayı kurtarmak için kullanılır. Bu makalede, bir dosya sunucusu olarak yapılandırılmış StorSimple Sanal diziniz üzerinde öğe düzeyinde kurtarma gerçekleştirmeye yönelik ayrıntılı adımlar da yer almaktadır.

## <a name="clone-shares-from-a-backup-set"></a>Yedekleme kümesinde kopya paylaşımları

**Paylaşımları kopyalamaya çalışmadan önce, bu işlemi gerçekleştirmek için cihazda yeterli alana sahip olduğunuzdan emin olun.** Bir yedekten kopyalamak için, [Azure Portal](https://portal.azure.com/), aşağıdaki adımları gerçekleştirin.

#### <a name="to-clone-a-share"></a>Bir paylaşımın klonlamak için

1. **Cihazlar** dikey penceresine gidin. Cihazınızı seçin ve tıklayın ve ardından **Paylaşımlar**' a tıklayın. Kopyalamak istediğiniz paylaşmayı seçin, bağlam menüsünü çağırmak için paylaşıma sağ tıklayın. **Kopyala**'yı seçin.
   
   ![Bir yedeklemeyi Kopyala](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. **Kopyala** dikey penceresinde **Yedekle > öğesini seçin** ve ardından aşağıdakileri yapın: 
   
   a.    Bu cihazdaki bir yedeklemeyi zaman aralığına göre filtreleyin. Son **7 gün**, **son 30 gün** ve **geçmiş yıl** arasından seçim yapabilirsiniz.
   
   b.    Filtrelenmiş yedeklemeler listesinde, kopyalamak için bir yedekleme seçin.
   
   c.    **Tamam**'a tıklayın.
   
   ![Yedek kopyalama 2](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. **Kopyala** dikey penceresinde **hedef ayarlar** ' a tıklayın ve ardından aşağıdakileri yapın:
   
   a.    Bir paylaşma adı belirtin. Paylaşma adı 3-127 karakter içermelidir.
   
   b.    Kopyalanmış paylaşma için isteğe bağlı olarak bir açıklama sağlayın.
   
   c.    Geri yüklemekte olduğunuz paylaşımın türünü değiştiremezsiniz. Katmanlı bir paylaşımda, yerel olarak sabitlenmiş bir katmanlı ve yerel olarak sabitlenmiş bir paylaşımın kopyası oluşturulur.
   
   d.    Kapasite, kopyalama yaptığınız paylaşımın boyutuna eşit olarak ayarlanır.
   
   e.    Bu paylaşıma yönelik yöneticileri atayın. Kopyalama işlemi tamamlandıktan sonra, dosya Gezgini ile paylaşma özelliklerini değiştirebileceksiniz.
   
   f.    **Tamam**'a tıklayın.
   
   ![Bir yedeklemeyi Kopyala 3](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Bir kopyalama işi başlatmak için **Kopyala** ' ya tıklayın. İş tamamlandıktan sonra kopyalama işlemi başlar ve size bildirilir. Kopyanın ilerlemesini izlemek için **işler** dikey penceresine gidip iş ayrıntılarını görüntülemek için işe tıklayın.
5. Kopyalama başarıyla oluşturulduktan sonra, cihazınızdaki **Paylaşımlar** dikey penceresine geri gidin.
6. Şimdi, yeni kopyalanmış paylaşımı cihazınızdaki paylaşımlar listesinde görüntüleyebilirsiniz. Katmanlı bir paylaşımın katmanlı olarak klonlanmış ve yerel olarak sabitlenmiş bir paylaşıma yerel olarak sabitlenmiş bir Share.
   
   ![Yedek 4 Kopyala](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Yedekleme kümesinden kopya birimleri

Bir yedekten kopyalamak için, Azure portal, bir paylaşımın kopyalanırken olduğu gibi adımları gerçekleştirmeniz gerekir. Kopyalama işlemi, yedeği aynı sanal cihazdaki yeni bir birime klonlar; farklı bir cihaza klonlamamanız gerekir.

#### <a name="to-clone-a-volume"></a>Bir birimi kopyalamak için

1. **Cihazlar** dikey penceresine gidin. Cihazınızı seçip tıklatın ve ardından **birimler**' e tıklayın. Kopyalamak istediğiniz birimi seçin, bağlam menüsünü çağırmak için birime sağ tıklayın. **Kopyala**'yı seçin.
   
   ![Bir birimi kopyalama](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. **Kopyala** dikey penceresinde **Yedekle** ' ye tıklayın ve ardından aşağıdakileri yapın: 
   
   a.    Bu cihazdaki bir yedeklemeyi zaman aralığına göre filtreleyin. Son **7 gün**, **son 30 gün** ve **geçmiş yıl** arasından seçim yapabilirsiniz. 
   
   b.    Filtrelenmiş yedeklemeler listesinde, kopyalamak için bir yedekleme seçin.
   
   c.    **Tamam**'a tıklayın.
   
   ![Birim kopyalama 2](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. **Kopyala** dikey penceresinde, **hedef birim ayarları** ' na tıklayın ve ardından aşağıdakileri yapın::
   
   a. Cihaz adı otomatik olarak doldurulur.
   
   b. **Kopyalanan birim** için bir birim adı girin. Birim adı 3 ile 127 karakter içermelidir.
   
   c. Birim türü otomatik olarak özgün birime ayarlanır. Katmanlı bir birim katmanlı olarak klonlanır ve yerel olarak sabitlenmiş bir birim yerel olarak sabitlenmiştir.
   
   d. **Bağlı konaklar** için **Seç**' e tıklayın.
   
   ![Birim kopyalama 3](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. **Bağlı konaklar** dikey penceresinde, var olan bir ACR arasından seçim yapın veya yeni bir ACR ekleyin. Yeni bir ACR eklemek için bir ACR adı ve ana bilgisayar ıQN 'si sağlamanız gerekir. **Seç**’e tıklayın.
   
   ![Birim kopyalama 4](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Bir kopyalama işi başlatmak için **Kopyala** ' ya tıklayın.
   
   ![Birim kopyalama 5](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. Kopyalama işi oluşturulduktan sonra kopyalama işlemi başlar. Kopya oluşturulduktan sonra, cihazınızdaki birimler dikey penceresinde görüntülenir. Katmanlı bir birimin katmanlı olarak kopyalandığına ve yerel olarak sabitlenmiş bir birimin yerel olarak sabitlenmiş bir birim olarak kopyalandığından emin olmanız gerektiğini unutmayın.
   
   ![Birim kopyalama 6](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. Birim listesinde birim çevrimiçi göründüğünde, birim kullanılabilir. Iscsı Başlatıcısı ana bilgisayarında, Iscsı Başlatıcısı Özellikler penceresinde hedeflerin listesini yenileyin. Kopyalanan birim adını içeren yeni bir hedef, durum sütunu altında ' etkin değil ' olarak görünmelidir.
8. Hedefi seçin ve **Bağlan**' a tıklayın. Başlatıcı hedefe bağlandıktan sonra, durumun **bağlı** olarak değiştirilmesi gerekir.
9. **Disk yönetimi** penceresinde, takılan birimler aşağıdaki çizimde gösterildiği gibi görünür. Bulunan birime sağ tıklayın (disk adına tıklayın) ve ardından **Çevrimiçi**’ne tıklayın.

> [!IMPORTANT]
> Yedekleme kümesinden bir birimi veya bir kopyayı kopyalamaya çalışırken, kopyalama işi başarısız olursa, portalda bir hedef birim veya paylaşma yine de oluşturulabilir. Bu öğeden doğan gelecekteki sorunları en aza indirmek için bu hedef birimi veya paylaşımın portalda silinmesi önemlidir.
> 
> 

## <a name="item-level-recovery-ilr"></a>Öğe düzeyinde kurtarma (ILR)

Bu sürüm, bir dosya sunucusu olarak yapılandırılmış bir StorSimple Sanal dizisindeki öğe düzeyinde kurtarmayı (ıLR) tanıtır. Özelliği, StorSimple cihazındaki tüm paylaşımların bir bulut yedeğinden dosya ve klasörlerin ayrıntılı kurtarılmasına olanak tanır. Bir self servis modeli kullanarak, son yedeklerden silinen dosyaları alabilirsiniz.

Her paylaşımda en son yedeklemeleri içeren bir *. Backups* klasörü bulunur. İstediğiniz yedeklemeye gidebilir, ilgili dosya ve klasörleri yedekten kopyalayabilir ve geri yükleyebilirsiniz. Bu özellik, yedeklerden dosya geri yüklemek için yöneticilere yapılan çağrıları ortadan kaldırır.

1. ILR 'yi gerçekleştirirken, yedeklemeleri dosya Gezgini ile görüntüleyebilirsiniz. Yedeklemeye bakmak istediğiniz belirli bir paylaşıma tıklayın. Tüm yedeklemeleri depolayan paylaşımda oluşturulmuş bir *. Backups* klasörü görürsünüz. Yedeklemeleri görüntülemek için *. Backups* klasörünü genişletin. Klasör, tüm yedekleme hiyerarşisinin ayrılmış görünümünü gösterir. Bu görünüm talep üzerine oluşturulmuştur ve genellikle yalnızca birkaç saniye sürer.
   
   Son beş yedek bu şekilde görüntülenir ve öğe düzeyinde kurtarma gerçekleştirmek için kullanılabilir. Son kullanılan beş yedek, hem varsayılan zamanlanmış hem de el ile yedeklemeler içerir.
   
   * Cihaz adı olarak adlandırılan **Zamanlanmış yedeklemeler** - &lt; &gt; YYYYMMDD-SSMMSS-UTC.
   * Ad-hoc-YYYYMMDD-HHMMSS-UTC olarak adlandırılan **el ile yedeklemeler** .
     
     ![. Backups klasörünü gösteren dosya Gezgini 'nin ekran görüntüsü. Bu klasörün içinde el ile yedeklemeler klasörü seçilidir.](./media/storsimple-virtual-array-clone/image14.png)

2. Silinen dosyanın en son sürümünü içeren yedeği belirler. Klasör adı, önceki durumların her birinde UTC zaman damgası içerse de, klasörün oluşturulduğu zaman, yedeklemenin başlatıldığı gerçek cihaz zamanı olur. Yedeklemeleri bulmak ve tanımlamak için klasör zaman damgasını kullanın.

3. Önceki adımda belirlediğiniz yedeklemeye geri yüklemek istediğiniz klasörü veya dosyayı bulun. Unutmayın yalnızca izinleriniz olan dosyaları veya klasörleri görüntüleyebilirsiniz. Belirli dosyalara veya klasörlere erişemiyorsanız, bir paylaşma yöneticisiyle iletişim kurun. Yönetici, dosya Gezgini 'ni kullanarak, paylaşma izinlerini düzenleyebilir ve belirli dosya veya klasöre erişmenizi sağlayabilir. Bu, paylaşılan yöneticinin tek bir kullanıcı yerine bir Kullanıcı grubu olması önerilen en iyi uygulamadır.

4. Dosyayı veya klasörü StorSimple dosya sunucunuzdaki uygun paylaşıma kopyalayın.

## <a name="next-steps"></a>Sonraki adımlar

[Yerel Web Kullanıcı arabirimini kullanarak StorSimple Sanal dizinizi yönetme](storsimple-ova-web-ui-admin.md)hakkında daha fazla bilgi edinin.

