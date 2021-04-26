---
title: Azure Bloblarını geri yükleme
description: Azure Bloblarını (önizlemede) nasıl geri yükleyeceğinizi öğrenin.
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 4cbd47ea654115f00095e30f7d5114aec0f2c85a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101746494"
---
# <a name="restore-azure-blobs-in-preview"></a>Azure Bloblarını geri yükleme (önizlemede)

İşletimsel yedekleme yapılandırılmış depolama hesaplarında blok Blobları, bekletme aralığı içinde herhangi bir zaman noktasına geri yüklenebilir. Ayrıca, geri yükleme bilgilerinizi depolama hesabındaki tüm blok bloblarına veya Blobların bir alt kümesine ekleyebilirsiniz.

## <a name="before-you-start"></a>Başlamadan önce

- Bloblar aynı depolama hesabına geri yüklenecek. Bu nedenle, geri yüklemekte olduğunuz saatin bu yana değişiklikler yapmış olan Blobların üzerine yazılacak.
- Yalnızca standart bir genel amaçlı v2 depolama hesabında blok Bloblar, geri yükleme işleminin bir parçası olarak geri yüklenebilir. Ekleme Blobları, sayfa Blobları ve Premium blok Blobları geri yüklenmez.
- Bir geri yükleme işi devam ederken, depolama alanındaki Bloblar okunamaz veya üzerine yazılamaz.
- Etkin kiralamaya sahip bir blob geri yüklenemez. Etkin kiralamaya sahip bir blob geri yüklenecek blob aralığına dahil edilmezse geri yükleme işlemi otomatik olarak başarısız olur. Geri yükleme işlemini başlatmadan önce tüm etkin kiraları kesin.
- Anlık görüntüler, geri yükleme işleminin bir parçası olarak oluşturulmaz veya silinmez. Yalnızca temel blob önceki durumuna geri yüklendi.
- **Kapsayıcıyı silme** işlemini çağırarak depolama hesabından bir kapsayıcıyı silerseniz, o kapsayıcı geri yükleme işlemiyle geri yüklenemez. Bir kapsayıcının tamamını silmek yerine, daha sonra geri yüklemek istiyorsanız ayrı Blobları silin. Ayrıca, Microsoft kapsayıcıları yanlışlıkla silinmeye karşı korumak için işlemsel yedeklemeye ek olarak kapsayıcılar için geçici silme olanağı da sağlar.
- Tüm sınırlamalar ve desteklenen senaryolar için [destek matrisine](blob-backup-support-matrix.md) bakın.

## <a name="restore-blobs"></a>Blobları geri yükleme

Yedekleme merkezi aracılığıyla bir geri yükleme işlemi başlatabilirsiniz.

1. Yedekleme merkezi 'nde üst çubukta **geri yükle** ' ye gidin.

    ![Yedekleme merkezi 'nde geri yükleme](./media/blob-restore/backup-center-restore.png)

1. **Geri yüklemeyi Başlat** sekmesinde, veri kaynağı türü olarak **Azure Blob 'Ları (Azure depolama)** seçin ve geri yüklemek istediğiniz **yedekleme örneğini** seçin. Burada yedekleme örneği, geri yüklemek istediğiniz Blobları içeren depolama hesabıdır.

     ![Yedekleme örneği Seç](./media/blob-restore/select-backup-instance.png)

1. **Kurtarma noktası seç** sekmesinde, verilerinizi geri yüklemek istediğiniz tarihi ve saati seçin. Kaydırıcıyı, geri yükleme için zaman içinde seçmek üzere de kullanabilirsiniz. Tarihin yanındaki bilgi balonu, verilerinizi geri yükleyeceğiniz geçerli süreyi gösterir. Blobların sürekli yedeklenmekte olan işlemsel yedeklemesi, verileri kurtarmak için noktalara ayrıntılı bir denetim sağlar.

    >[!NOTE]
    > Burada gösterilen süre, yerel zaman.

    ![Geri yükleme için tarih ve saat](./media/blob-restore/date-and-time.png)

1. **Parametreleri geri yükle** sekmesinde depolama hesabındaki tüm Blobları, belirli kapsayıcıları veya bir blob alt kümesini önek eşleşmesi kullanarak geri yüklemek isteyip istemediğinizi seçin. Önek eşleşmesi kullanılırken, en fazla 10 önek veya dosya yolu belirtebilirsiniz. Ön ek ile eşleşme hakkında daha fazla ayrıntı [burada verilmiştir](#use-prefix-match-for-restoring-blobs).

    ![Parametreleri geri yükle](./media/blob-restore/restore-parameters.png)

    Aşağıdaki seçeneklerden birini belirleyin:

    - **Depolama hesabındaki tüm Blobları geri yükle**: Bu seçeneği kullanmak, depolama hesabındaki tüm blok bloblarını zaman içindeki seçili noktaya geri yükleyerek geri yükler. Büyük miktarlarda veri içeren depolama hesapları veya yüksek bir karmaşıklığa göre Wonu geri yükleme daha uzun zaman alabilir.

    - **Seçili kapsayıcılara gözatıp geri yükleme**: Bu seçeneği kullanmak, geri yüklemek için en fazla 10 kapsayıcıya gözatmanıza ve seçmenize olanak sağlar. Depolama hesabındaki kapsayıcıları görüntülemek için yeterli izinlere sahip olmanız gerekir, aksi takdirde depolama hesabının içeriğini görmeyebilirsiniz.

    - **Önek eşleşmesi kullanarak geri yüklenecek Blobları seçin**: Bu seçenek, bir önek eşleşmesi kullanarak Blobların bir alt kümesini geri yüklemenize olanak sağlar. Tek bir kapsayıcı içinde veya birden çok kapsayıcı içinde, belirli bir zaman noktasında bu blob 'ları önceki durumlarına döndürmek için en fazla 10 lexıgrafik aralığı belirtebilirsiniz. Göz önünde bulundurmanız gereken birkaç nokta aşağıda verilmiştir:

        - Bir eğik çizgi (/) kullanarak blob önekinden kapsayıcı adını tanımlayabilirsiniz
        - Belirtilen aralığın başlangıcı dahil, ancak belirtilen Aralık özel.

    Blob aralıklarını geri yüklemek için ön ekleri kullanma hakkında daha fazla bilgi için [Bu bölüme](#use-prefix-match-for-restoring-blobs)bakın.

1. Geri yüklenecek Blobları belirtmeyi tamamladıktan sonra, **Gözden geçirme** ve geri yükleme sekmesine geçin ve geri yüklemeyi başlatmak için **geri** Yükle ' yi seçin.

1. **Geri yüklemeyi izle**: geri yüklemeler ayrıntılarını ve durumunu Izlemek Için **yedekleme işleri** görünümünü kullanın. Bunu yapmak için **yedekleme merkezi**  >  **yedekleme işleri**' ne gidin. Geri yükleme gerçekleştirilirken durum **devam ediyor olarak** gösterilir.

    ![Yedekleme işleri sekmesi](./media/blob-restore/backup-jobs.png)

    Geri yükleme işlemi başarıyla tamamlandığında, durum **tamamlandı** olarak değişir. Geri yükleme başarıyla tamamlandıktan sonra depolama hesabındaki Blobları okuyup yazabilirsiniz.

## <a name="additional-topics"></a>Ek konu başlıkları

### <a name="use-prefix-match-for-restoring-blobs"></a>Blobları geri yüklemek için önek eşleştirme kullan

Aşağıdaki örneği inceleyin:

![Ön ek eşleşmesi ile geri yükleme](./media/blob-restore/prefix-match.png)

Görüntüde gösterilen geri yükleme işlemi aşağıdaki eylemleri gerçekleştirir:

- *Kapsayıcı1*'in tüm içeriğini geri yükler.
- *Blob1* ' deki *blob5* ile naicografik aralığındaki Blobları geri yükler *.* Bu Aralık, *blob1*, *blob11*, *blob100*, *blob2* vb. gibi adlara sahip Blobları geri yükler. Aralığın sonu dışlamalı olduğu için, adları *blob4* ile başlayan Blobları geri yükler, ancak adları *blob5* ile başlayan Blobları geri almaz.
- *Container3* ve *container4* içindeki tüm Blobları geri yükler. Aralığın sonu dışlamalı olduğundan, bu Aralık *container5* geri döndürmez.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Blobları için işlemsel yedeklemeye genel bakış (önizlemede)](blob-backup-overview.md)
