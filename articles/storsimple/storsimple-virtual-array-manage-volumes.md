---
title: StorSimple Sanal dizisinde birimleri yönetme | Microsoft Docs
description: StorSimple Device Manager açıklar ve StorSimple Sanal dizinizdeki birimleri yönetmek için nasıl kullanılacağını açıklar.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: caa6a26b-b7ba-4a05-b092-1a79450225cf
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 79e3ce8c1605e5d68ff44901f53854d2f5f10abc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014389"
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>StorSimple Sanal Dizisinde birimleri yönetmek için StorSimple Device Manager hizmetini kullanma

## <a name="overview"></a>Genel Bakış

Bu öğreticide, StorSimple Sanal dizinizdeki birimleri oluşturmak ve yönetmek için StorSimple Device Manager hizmetinin nasıl kullanılacağı açıklanmaktadır.

StorSimple Device Manager hizmeti, StorSimple çözümünüzü tek bir web arabiriminden yönetmenizi sağlayan Azure portal bir uzantıdır. Paylaşımları ve birimleri yönetmeye ek olarak, StorSimple Device Manager hizmetini kullanarak cihazları görüntüleyebilir ve yönetebilir, uyarıları görüntüleyebilir, yedekleme ilkelerini ve yedekleme kataloğunu görüntüleyebilir ve yönetebilirsiniz.

## <a name="volume-types"></a>Birim türleri

StorSimple birimleri şu olabilir:

* **Yerel olarak sabitlenmiş**: Bu birimlerdeki veriler dizi üzerinde her zaman kalır ve buluta taşımaz.
* **Katmanlı**: Bu birimlerdeki veriler buluta taşıbilirler. Katmanlı bir birim oluşturduğunuzda, alanın yaklaşık %10 ' u yerel katmanda sağlanır ve alanın %90 ' unun bulutta sağlanması gerekir. Örneğin, 1 TB 'lik bir birim sağladıysanız, 100 GB yerel alanda yer alır ve veri katmanları sırasında bulutta 900 GB kullanılır. Bu işlem, cihazdaki tüm yerel alanı kapatdıysanız katmanlı bir birim sağlayabileceğinizden (yerel katmanda gerekli %10 ' un mevcut olmadığından) bu şekilde görünür.

### <a name="provisioned-capacity"></a>Sağlanan kapasite
Her birim türü için sağlanan maksimum kapasite için aşağıdaki tabloya bakın.

| **Sınır tanımlayıcı**                                       | **Sınırlı**     |
|------------------------------------------------------------|---------------|
| Katmanlı birimin en küçük boyutu                            | 500 GB        |
| Katmanlı birimin en büyük boyutu                            | 5 TB          |
| Yerel olarak sabitlenmiş bir birimin en küçük boyutu                    | 50 GB         |
| Yerel olarak sabitlenmiş bir birimin en büyük boyutu                    | 200 GB        |

## <a name="the-volumes-blade"></a>Birimler dikey penceresi
StorSimple hizmet Özeti dikey penceresindeki **birimler** menüsü, belirli bir StorSimple dizisindeki depolama birimlerinin listesini görüntüler ve bunları yönetmenizi sağlar.

![Birimler dikey penceresi](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

Bir birim bir dizi öznitelik içerir:

* **Birim adı** : benzersiz olması gereken açıklayıcı bir ad ve birimin tanımlanmasına yardımcı olur.
* **Durum** – çevrimiçi veya çevrimdışı olabilir. Bir birim çevrimdışıysa, birimi kullanmak için erişim izni verilen başlatıcılara (sunucular) görünmez.
* **Tür** – birimin **katmanlı** (varsayılan) veya **yerel olarak sabitlenmiş** olduğunu gösterir.
* **Kapasite** : başlatıcı tarafından depolanabilecek toplam veri miktarına kıyasla kullanılan veri miktarını belirtir (sunucu).
* **Yedekleme** – StorSimple Sanal dizisi söz konusu olduğunda, tüm birimler yedekleme için otomatik olarak etkinleştirilir.
* **Bağlı konaklar** – bu birime erişim izni verilen başlatıcıları (sunucular) belirtir.

![Birim ayrıntıları](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Aşağıdaki görevleri gerçekleştirmek için bu öğreticideki yönergeleri kullanın:

* Birim ekleme
* Bir birimi değiştirme
* Bir birimi çevrimdışı duruma getirme
* Bir birimi silme

## <a name="add-a-volume"></a>Birim ekleme

1. StorSimple hizmeti Özeti dikey penceresinde, komut çubuğundan **+ Birim Ekle** ' ye tıklayın. Bu, **Birim Ekle** dikey penceresini açar.
   
    ![Ekran görüntüsü, birim Ekle düğmesini ve birim Ekle bölmesini gösterir.](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. **Birim Ekle** dikey penceresinde aşağıdakileri yapın:
   
   * **Birim adı** alanına biriminiz için benzersiz bir ad girin. Ad 3 ile 127 arasında karakter içeren bir dize olmalıdır.
   * **Tür** açılan listesinde, **katmanlı** veya **yerel olarak sabitlenmiş** bir birim oluşturulup oluşturulmayacağını belirtin. Yerel garanti, düşük gecikme süreleri ve daha yüksek performans gerektiren iş yükleri için **yerel olarak sabitlenmiş birim**' i seçin. Diğer tüm veriler için **katmanlı** birim ' i seçin.
   * **Kapasite** alanında birimin boyutunu belirtin. Katmanlı bir birim 500 GB ile 5 TB arasında olmalıdır ve yerel olarak sabitlenmiş bir birim 50 GB ile 500 GB arasında olmalıdır.
   * * **Bağlı konaklar**' a tıklayın, bu birime bağlamak istediğiniz iSCSI başlatıcısına karşılık gelen bir erişim denetimi kaydı (ACR) seçin ve ardından **Seç**' e tıklayın.
3. Yeni bir bağlı konak eklemek için **Yeni Ekle**' ye tıklayın, ana bilgisayar ve Iscsı tam adı (IQN) için bir ad girin ve ardından **Ekle**' ye tıklayın.
   
    ![Ekran görüntüsünde, yeni ekleyebileceğiniz bağlı konaklar bölmesi görüntülenir.](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Biriminiz yapılandırmayı bitirdiğinizde **Oluştur**' a tıklayın. Belirtilen ayarlarla bir birim oluşturulur ve başarılı bir şekilde oluşturma hakkında bir bildirim görürsünüz. Varsayılan olarak, birim için yedekleme etkinleştirilir.
5. Birimin başarıyla oluşturulduğunu doğrulamak için **birimler** dikey penceresine gidin. Listelenen birimi görmeniz gerekir.
   
    ![Toplu oluşturma başarısı](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Bir birimi değiştirme

Birime erişen Konakları değiştirmeniz gerektiğinde bir birimi değiştirin. Birim oluşturulduktan sonra bir birimin diğer öznitelikleri değiştirilemez.

#### <a name="to-modify-a-volume"></a>Bir birimi değiştirmek için

1. StorSimple hizmet Özeti dikey penceresindeki **birimler** ayarından, değiştirmek istediğiniz birimin bulunduğu sanal diziyi seçin.
2. Birimi **seçin** ve **bağlı konaklar** ' a tıklayarak bağlı olan Konağı görüntüleyin ve farklı bir sunucuda değiştirin.
   
    ![Birimi Düzenle](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. **Kaydet** komut çubuğuna tıklayarak değişikliklerinizi kaydedin. Belirttiğiniz ayarlar uygulanır ve bir bildirim görürsünüz.

## <a name="take-a-volume-offline"></a>Bir birimi çevrimdışı duruma getirme

Bunu değiştirmeyi veya silmeyi planlarken bir birimi çevrimdışına almanız gerekebilir. Bir birim çevrimdışıyken, okuma/yazma erişimi için kullanılamaz. Birimi konakta ve cihazda çevrimdışı duruma getirmeniz gerekecektir.

#### <a name="to-take-a-volume-offline"></a>Bir birimi çevrimdışı duruma getirme

1. Söz konusu birimin çevrimdışı duruma getirmeden önce kullanımda olmadığından emin olun.
2. Birimi önce konakta çevrimdışına alın. Bu, birimdeki veri bozulması olasılığını ortadan kaldırır. Belirli adımlar için, ana bilgisayar işletim sisteminizle ilgili yönergelere bakın.
3. Konaktaki birim çevrimdışı olduktan sonra aşağıdaki adımları gerçekleştirerek dizideki birimi çevrimdışına alın:
   
   * StorSimple hizmet Özeti dikey penceresindeki **birimler** ayarından, çevrimdışına almak istediğiniz birimin bulunduğu sanal diziyi seçin.
   * Birimi **seçin** ve **..** . ' ye tıklayın (bu satıra sağ tıklayın) ve bağlam menüsünden **Çevrimdışına Al**' ı seçin.
     
        ![Çevrimdışı birim](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * **Çevrimdışına Al** dikey penceresindeki bilgileri gözden geçirin ve işlemi kabul edip etmediklerini onaylayın. Birimi çevrimdışına almak için **Çevrimdışına Al** ' a tıklayın. Sürmekte olan işlem bildirimini görürsünüz.
   * Birimin başarıyla çevrimdışına alındığını doğrulamak için **birimler** dikey penceresine gidin. Birimin durumunu çevrimdışı olarak görmeniz gerekir.
     
       ![Çevrimdışı birim onayı](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Bir birimi silme

> [!IMPORTANT]
> Bir birimi yalnızca çevrimdışı ise silebilirsiniz.
> 
> 

Bir birimi silmek için aşağıdaki adımları izleyin.

#### <a name="to-delete-a-volume"></a>Bir birimi silmek için

1. StorSimple hizmet Özeti dikey penceresindeki **birimler** ayarından, silmek istediğiniz birimin bulunduğu sanal diziyi seçin.
2. Birimi **seçin** ve **..** . ' ye tıklayın (bu satıra sağ tıklayın) ve bağlam menüsünde **Sil**' i seçin.
   
    ![Birimi Sil](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Silmek istediğiniz birimin durumunu denetleyin. Silmek istediğiniz birim çevrimdışı değilse, [bir birimi çevrimdışı duruma getirme](#take-a-volume-offline)bölümündeki adımları izleyerek önce çevrimdışına alın.
4. **Silme** dikey penceresinde onay istendiğinde, onayı kabul edip **Sil**' e tıklayın. Birim şimdi silinecek ve **birimler** dikey penceresi, sanal dizi içindeki güncelleştirilmiş birim listesini gösterir.

## <a name="next-steps"></a>Sonraki adımlar

[StorSimple birimini klonlamak](storsimple-virtual-array-clone.md)hakkında bilgi edinin.

