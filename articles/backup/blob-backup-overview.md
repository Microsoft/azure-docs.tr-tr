---
title: Azure Blob 'Ları için işlemsel yedeklemeye genel bakış
description: Azure Blobları (önizlemede) için işlemsel yedekleme hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 1f6bc98b92a2af4b05cae766a2186f2970b7133c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558763"
---
# <a name="overview-of-operational-backup-for-azure-blobs-in-preview"></a>Azure Blobları için işlemsel yedeklemeye genel bakış (önizlemede)

Blob 'lar için işlemsel yedekleme, blok bloblarınızı bozulmalar, blob silme işlemleri ve yanlışlıkla depolama hesabı silme gibi çeşitli veri kaybı senaryolarından korumanıza imkan tanıyan yönetilen, yerel bir veri koruma çözümüdür. Veriler yerel olarak kaynak depolama hesabının içinde depolanır ve gerektiğinde seçilen bir zaman noktasına kurtarılabilir. Bu nedenle, bloblarınızı korumak için basit, güvenli ve uygun maliyetli bir yol sağlar.

Blob 'Lar için işletimsel yedekleme, [yedekleme merkezi](backup-center-overview.md)ile tümleşir, diğer yedekleme yönetim özellikleri arasında, yedeklemeleri ölçekteki yönetmenize, izlemenize, çalıştırmanıza ve çözümlemenize yardımcı olabilecek tek bir cam bölmesi sağlamak Için yedekleme merkezi ile tümleştirilir.

## <a name="how-operational-backup-works"></a>İşletimsel yedekleme nasıl çalışır?

Blobların işletimsel yedeklemesi, **yerel bir yedekleme** çözümüdür. Bu nedenle yedekleme verileri yedekleme kasasına aktarılmaz, ancak kaynak depolama hesabının kendisinde saklanır. Ancak, Yedekleme Kasası hala yedeklemeleri yönetme birimi görevi görür. Ayrıca, bu sürekli bir **yedekleme** çözümüdür. Bu, herhangi bir yedekleme zamanlamanız gerekmediği anlamına gelir ve seçilen zaman noktasındaki tüm değişiklikler korunur ve durumdan geri yüklenebilir.

İşletimsel yedekleme, verilerinizi korumak ve gerektiğinde kurtarmaya izin vermek için blob platformu yeteneklerini kullanır:

- **Zaman içinde geri yükleme**: blob 'un [zaman içindeki geri yükleme,](../storage/blobs/point-in-time-restore-overview.md) blob verilerinin önceki bir duruma geri yüklenmesine izin verir. Bu, sırasıyla, belirtilen süre boyunca verileri sürdürmek için **geçici silme**, **akışı değiştirme** ve **BLOB sürümü oluşturma** kullanır. İşletimsel yedekleme, belirli bir süre boyunca verilerin korunduğundan emin olmak için belirli bir noktaya geri yükleme ve temel özellikleri etkinleştirme konusunda da sürer.

- **Kilidi Sil**: kilit silme, depolama hesabının yanlışlıkla veya yetkisiz kullanıcılar tarafından silinmesini engeller. İşlem yedekleme, aynı zamanda, depolama hesabı silinirken veri kaybı olasılıklarını azaltmak için otomatik olarak bir silme kilidi uygular.

Geçerli çözümün sınırlamaları hakkında bilgi edinmek için [destek matrisine](blob-backup-support-matrix.md) bakın.

### <a name="protection"></a>Koruma

İşletimsel yedekleme, **depolama hesabı** düzeyinde yapılandırılır ve yönetilir ve depolama hesabı içindeki tüm blok Blobları için geçerlidir. İşletimsel yedekleme, yedekleme verilerinin (eski sürümler ve silinen blob 'lar dahil) saklanacağı süreyi yönetmek için bir **yedekleme ilkesi** kullanır. bu şekilde, verilerinizi geri yükleyeceğiniz süreyi tanımlar. Yedekleme ilkesi 360 günlük bir en fazla bekletme elde edebilir veya eşit sayıda (51) ya da aya (11) eşittir.

Bir depolama hesabı için yedeklemeyi yapılandırdığınızda ve ' n ' gün bekletme içeren bir yedekleme ilkesi atadığınızda, temeldeki özellikler aşağıda açıklandığı gibi ayarlanır. Bu özellikleri, Depolama hesabınızdaki blob hizmeti 'nin **veri koruma** sekmesinde görebilirsiniz.

- **Zaman içinde geri yükleme**: yedekleme ilkesinde tanımlandığı şekilde ' n ' gün olarak ayarlanır. Depolama hesabı zaten bir bekletme ile etkin bir zamana sahip ise, yedeklemeyi yapılandırmadan önce ' x ' gün daha büyük bir süre geri yükleme süresi iki değerden daha büyük (n, x) olacak şekilde ayarlanır. Zaten belirli bir noktaya geri yükleme etkinleştirdiyseniz ve saklama, yedekleme ilkesinde bundan daha büyük olacak şekilde belirtilmişse, değişmeden kalır.

- **Geçici silme**: ' n + 5 ' gün olarak, diğer bir deyişle, yedekleme ilkesinde belirtilen süreye ek olarak beş gün olarak ayarlanır. İşlemsel yedekleme için yapılandırılmış depolama hesabı zaten bir bekletme ile etkin olan geçici silme işlemi içeriyorsa, ' y ' gün deyin, geçici silme bekletme iki değerin en yüksek değerine ayarlanır, yani en fazla (n + 5, y). Geçici silme özelliği zaten etkinse ve saklama, yedekleme ilkesine göre bundan daha büyük olacak şekilde belirtilmişse, değişmeden kalır.

- **BLOB 'lar ve BLOB değişiklik akışı Için sürüm oluşturma**: işlemsel yedekleme için yapılandırılmış depolama hesapları için sürüm oluşturma ve değişiklik akışı etkindir.

- **Kilidi Sil**: bir depolama hesabında işletimsel yedeklemeyi yapılandırmak, depolama hesabında silme kilidi de uygular. Yedekleme tarafından uygulanan silme kilidi, depolama hesabının **kilitler** sekmesi altında görüntülenebilir.

Yedeklemenin depolama hesaplarında bu özelliklerin korunmasını sağlamak için, yedekleme kasasına ilgili depolama hesaplarında **depolama hesabı yedeklemesi katılımcısı** rolü verilmelidir.

>[!NOTE]
>İşletimsel yedekleme yalnızca blok Blobları üzerindeki işlemleri destekler ve kapsayıcılardaki işlemler geri yüklenemez. **Kapsayıcıyı silme** işlemini çağırarak depolama hesabından bir kapsayıcıyı silerseniz, o kapsayıcı geri yükleme işlemi ile geri yüklenemez. Veri koruma ve kurtarmayı iyileştirmek için geçici silme özelliğini etkinleştirmeniz önerilir.

### <a name="management"></a>Yönetim

Bir depolama hesabında yedeklemeyi etkinleştirdikten sonra, yedekleme kasasındaki depolama hesabına karşılık gelen bir yedekleme örneği oluşturulur. Karşılık gelen yedekleme örneği aracılığıyla geri yükleme, izleme, korumayı durdurma gibi bir depolama hesabı için yedeklemeyle ilgili tüm işlemleri gerçekleştirebilirsiniz.

İşletimsel yedekleme Ayrıca, tüm depolama hesaplarınızın korunmasını merkezi olarak yönetmenize yardımcı olmak için yedekleme merkezi ile doğrudan tümleşir, diğer tüm yedekleme desteklenen iş yükleriyle birlikte. Yedekleme merkezi, tüm yedekleme gereksinimleriniz için izleme işleri ve yedeklemelerin durumunu ve geri yüklemeyi, uyumluluk ve idare etmeyi, yedekleme kullanımını çözümlemeyi ve verilerin yedeklenmesi ve geri yüklenmesi ile ilgili işlemler gerçekleştirmeyi sağlayan tek bölmesidir.

### <a name="restore"></a>Geri Yükleme

Bir kurtarma noktasının bulunduğu zaman içindeki herhangi bir noktadan verileri geri yükleyebilirsiniz. Bir depolama hesabı korumalı durumundayken bir kurtarma noktası oluşturulur ve yedekleme ilkesi tarafından tanımlanan bekletme döneminde (ve dolayısıyla depolama hesabındaki blob hizmeti 'nin zaman içinde geri yükleme özelliği) olduğu sürece verileri geri yüklemek için kullanılabilir. İşletimsel yedekleme, verileri bir kurtarma noktasından geri yüklemek için blob zaman içinde geri yükleme kullanır.

İşletimsel yedekleme, depolama hesabındaki tüm blok bloblarını geri yükleme, belirli kapsayıcıları gözden geçirme ve geri yükleme veya Blobların bir alt kümesini geri yüklemek için önek eşleşmeleri kullanma seçeneği sunar. Tüm geri yüklemeler yalnızca kaynak depolama hesabına uygulanabilir.

## <a name="pricing"></a>Fiyatlandırma

Blob 'lar için işlemsel yedeklemeyi kullanırken herhangi bir yönetim ücreti veya örnek ücreti ödemeniz gerekmez. Ancak, aşağıdaki ücretleri ödeyirsiniz:

- Geri yüklemeler, işlenen veri miktarına bağlı olarak blob zaman içinde geri yükleme ve atma ücretleri kullanılarak yapılır. Daha fazla bilgi için bkz. [Noktadan noktaya geri yükleme fiyatlandırması](../storage/blobs/point-in-time-restore-overview.md#pricing-and-billing).

- [BLOB 'lar Için geçici silme](../storage/blobs/soft-delete-blob-overview.md), [Azure Blob depolamada akış desteğini değiştirme](../storage/blobs/storage-blob-change-feed.md)ve [BLOB sürümü oluşturma](../storage/blobs/versioning-overview.md)nedeniyle verilerin saklanması.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Blobları yedeklemesini yapılandırma ve yönetme](blob-backup-configure-manage.md)