---
title: StorSimple Sanal dizi paylaşımlarını yönetme | Microsoft Docs
description: StorSimple Device Manager açıklar ve StorSimple Sanal dizinizdeki paylaşımları yönetmek için nasıl kullanılacağını açıklar.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 0a799c83-fde5-4f3f-af0e-67535d1882b6
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 340b910319cabe3379bdb1bad1c09bc71c17f072
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994953"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>StorSimple Sanal Dizisinde paylaşımları yönetmek için StorSimple Device Manager hizmetini kullanma

## <a name="overview"></a>Genel Bakış

Bu öğreticide, StorSimple Sanal dizinizdeki paylaşımları oluşturmak ve yönetmek için StorSimple Device Manager hizmetinin nasıl kullanılacağı açıklanmaktadır.

StorSimple Device Manager hizmeti, StorSimple çözümünüzü tek bir web arabiriminden yönetmenizi sağlayan Azure portal bir uzantıdır. Paylaşımları ve birimleri yönetmeye ek olarak, StorSimple Device Manager hizmetini kullanarak cihazları görüntüleyebilir ve yönetebilir, uyarıları görüntüleyebilir, yedekleme ilkelerini yönetebilir ve yedekleme kataloğunu yönetebilirsiniz.

## <a name="share-types"></a>Paylaşma türleri

StorSimple paylaşımları şu olabilir:

* **Yerel olarak sabitlenmiş**: Bu paylaşımların verileri her zaman dizide kalır ve buluta taşımaz.
* **Katmanlı**: Bu paylaşımlardaki veriler buluta taşımış olabilir. Katmanlı bir paylaşma oluşturduğunuzda, alanın yaklaşık %10 ' u yerel katmanda sağlanmakta ve alanın %90 ' unun bulutta sağlanması gerekir. Örneğin, 1 TB 'lik bir paylaşma sağladıysanız, 100 GB yerel alanda yer alır ve veri katmanları sırasında bulutta 900 GB kullanılır. Bu işlem, cihazdaki tüm yerel alanı kapatdıysanız katmanlı bir paylaşma sağlayabileceğinizden (yerel katmanda gerekli %10 ' un mevcut olmadığı için), katmanlı bir paylaşımın sağlanamadığı anlamına gelir.

### <a name="provisioned-capacity"></a>Sağlanan kapasite

Her bir paylaşma türü için sağlanan maksimum kapasite için aşağıdaki tabloya bakın.

| **Sınır tanımlayıcı** | **Sınırlı** |
| --- | --- |
| Katmanlı paylaşımın en küçük boyutu |500 GB |
| Katmanlı paylaşımın en büyük boyutu |20 TB |
| Yerel olarak sabitlenmiş bir paylaşımın en küçük boyutu |50 GB |
| Yerel olarak sabitlenmiş bir paylaşımın en büyük boyutu |2 TB |

## <a name="the-shares-blade"></a>Paylaşımlar dikey penceresi

StorSimple hizmet Özeti dikey penceresindeki **Paylaşımlar** menüsü, belirli bir StorSimple dizisindeki depolama paylaşımlarının listesini görüntüler ve bunları yönetmenizi sağlar.

![Paylaşımlar dikey penceresi](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

Bir paylaşımdan bir dizi öznitelik oluşur:

* **Paylaşma adı** : benzersiz olması gereken açıklayıcı bir ad ve paylaşımın tanımlanmasına yardımcı olur.
* **Durum** – çevrimiçi veya çevrimdışı olabilir. Bir Share çevrimdışıyken, paylaşımın kullanıcıları erişemez.
* **Tür** : paylaşımın **katmanlı** (varsayılan) veya **yerel olarak sabitlenmiş** olduğunu gösterir.
* **Kapasite** : paylaşımda depolanabilecek toplam veri miktarına kıyasla kullanılan veri miktarını belirtir.
* **Açıklama** : paylaşımın tanımlanmasına yardımcı olan isteğe bağlı ayar.
* **İzinler** -Windows Explorer aracılığıyla yönetilebilen PAYLAŞıMA yönelik NTFS izinleri.
* **Yedekleme** – StorSimple Sanal dizisi söz konusu olduğunda tüm paylaşımlar yedekleme için otomatik olarak etkinleştirilir.

![Paylaşım ayrıntıları](./media/storsimple-virtual-array-manage-shares/share-details.png)

Aşağıdaki görevleri gerçekleştirmek için bu öğreticideki yönergeleri kullanın:

* Paylaşım ekleme
* Bir paylaşıma değiştirme
* Çevrimdışı bir paylaşımdan yararlanın
* Paylaşımı silme

## <a name="add-a-share"></a>Paylaşım ekleme

1. StorSimple hizmeti Özeti dikey penceresinde, komut çubuğundan **+ paylaşma Ekle** ' ye tıklayın. Bu, **paylaşma Ekle** dikey penceresini açar.

    ![Paylaşma Ekle](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. **Paylaşma Ekle** dikey penceresinde aşağıdakileri yapın:
   
   1. **Paylaşma adı** alanına, paylaşımınız için benzersiz bir ad girin. Ad 3 ile 127 arasında karakter içeren bir dize olmalıdır.

   2. Paylaşıma yönelik isteğe bağlı bir **Açıklama** . Açıklama, paylaşma sahiplerini belirlemesine yardımcı olur.

   3. **Tür** açılan listesinde, **katmanlı** veya **yerel olarak sabitlenmiş** bir paylaşımın oluşturulup oluşturulmayacağını belirtin. Yerel garanti, düşük gecikme süreleri ve daha yüksek performans gerektiren iş yükleri için **yerel olarak sabitlenmiş paylaşma**' yı seçin. Diğer tüm veriler için **katmanlı** paylaşma ' yı seçin.

   4. **Kapasite** alanında, paylaşımın boyutunu belirtin. Katmanlı bir paylaşımın 500 GB ile 20 TB arasında olması ve yerel olarak sabitlenmiş bir paylaşımın 50 GB ile 2 TB arasında olması gerekir.

   5. **Varsayılan tam Izinleri ayarla** alanında, kullanıcıya veya bu paylaşıma erişen gruba izinleri atayın. Kullanıcının adını veya kullanıcı grubunu _john@contoso.com_ biçiminde belirtin. Yönetici ayrıcalıklarının bu paylaşımlara erişmesine izin vermek için bir Kullanıcı grubu (tek bir kullanıcı yerine) kullanmanızı öneririz. Burada izinleri atadıktan sonra, Dosya Gezgini'ni kullanarak bu izinlerde değişiklik yapabilirsiniz.
3. Paylaşımınızı yapılandırmayı bitirdiğinizde **Oluştur**' a tıklayın. Belirtilen ayarlarla bir paylaşma oluşturulacak ve bir bildirim görürsünüz. Varsayılan olarak, yedekleme, paylaşıma yönelik olarak etkinleştirilir.
4. Paylaşımın başarıyla oluşturulduğunu doğrulamak için **Paylaşımlar** dikey penceresine gidin. Paylaşımın listelendiğini görmeniz gerekir.
   
    ![Paylaşma oluşturma başarısı](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Bir paylaşıma değiştirme

Paylaşımın açıklamasını değiştirmeniz gerektiğinde bir paylaşıma değişiklik yapın. Paylaşma oluşturulduktan sonra başka bir paylaşma özelliği değiştirilemez.

#### <a name="to-modify-a-share"></a>Bir paylaşımdan değişiklik yapmak için

1. StorSimple hizmeti Özeti dikey penceresindeki **Paylaşımlar** ayarından, değiştirmek istediğiniz paylaşımın bulunduğu sanal diziyi seçin.
2. Geçerli açıklamayı görüntülemek ve değiştirmek için paylaşıma **seçin** .
3. **Kaydet** komut çubuğuna tıklayarak değişikliklerinizi kaydedin. Belirttiğiniz ayarlar uygulanır ve bir bildirim görürsünüz.
   
    ![ Paylaşma Düzenle](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Çevrimdışı bir paylaşımdan yararlanın

Değişiklik yapmak veya silmek için planlama yaparken bir paylaşıma çevrimdışı uygulamanız gerekebilir. Bir paylaşma çevrimdışıyken, okuma/yazma erişimi için kullanılamaz. Aynı zamanda, cihazdaki ve cihazdaki paylaşımdan çevrimdışı olarak sahip olmanız gerekir.

#### <a name="to-take-a-share-offline"></a>Çevrimdışı bir şekilde paylaşma

1. Söz konusu paylaşımın çevrimdışı duruma getirmeden önce kullanımda olmadığından emin olun.
2. Aşağıdaki adımları gerçekleştirerek dizideki paylaşımdan çevrimdışı olarak yararlanın:
   
    1. StorSimple hizmeti Özeti dikey penceresindeki **Paylaşımlar** ayarından, çevrimdışına almak istediğiniz paylaşımın bulunduğu sanal diziyi seçin.

    2. Paylaşma ' yı **seçin** ve **..** . (bu satıra sağ tıklayın) ve bağlam menüsünden **Çevrimdışına Al**' ı seçin.
     
        ![Çevrimdışı paylaşma](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. **Çevrimdışına Al** dikey penceresindeki bilgileri gözden geçirin ve işlemi kabul edip etmediklerini onaylayın. Paylaşımdan çevrimdışı duruma geçmek için **Çevrimdışına Al** ' a tıklayın. Sürmekte olan işlem bildirimini görürsünüz.

    4. Paylaşımın başarıyla çevrimdışı şekilde alındığını doğrulamak için **Paylaşımlar** dikey penceresine gidin. Paylaşımın durumunu çevrimdışı olarak görmeniz gerekir.

## <a name="delete-a-share"></a>Paylaşımı silme

> [!IMPORTANT]
> Bir paylaşımın yalnızca çevrimdışı olması durumunda silebilirsiniz.


Bir paylaşımın silinmesi için aşağıdaki adımları izleyin.

#### <a name="to-delete-a-share"></a>Bir paylaşıma silme

1. StorSimple hizmeti Özeti dikey penceresindeki **Paylaşımlar** ayarından, silmek istediğiniz paylaşımın bulunduğu sanal diziyi seçin.
2. Paylaşma ' yı **seçin** ve **..** . (bu satıra sağ tıklayın) ve bağlam menüsünden **Sil**' i seçin.
   
    ![Paylaşma Sil](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Silmek istediğiniz paylaşımın durumunu kontrol edin. Silmek istediğiniz paylaşma çevrimdışı değilse, önce çevrimdışına alın. [Bir paylaşma çevrimdışına alma](#take-a-share-offline)bölümündeki adımları izleyin.
4. **Silme** dikey penceresinde onay istendiğinde, onayı kabul edip **Sil**' e tıklayın. Paylaşım şimdi silinecek ve **Paylaşımlar** dikey penceresi, sanal dizi içinde güncelleştirilmiş paylaşımların listesini gösterir.

## <a name="next-steps"></a>Sonraki adımlar
[StorSimple paylaşımının nasıl klonlacağını](storsimple-virtual-array-clone.md)öğrenin.

