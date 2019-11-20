---
title: Redsıs için Azure önbelleğindeki verileri içeri ve dışarı aktarma
description: Redsıs örnekleri için Premium Azure önbelleğiniz ile BLOB depolama alanına ve veri aktarmayı öğrenin
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/31/2017
ms.author: yegu
ms.openlocfilehash: e4265cf3f9c211a19fe05bc18cf47a273165c3c3
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122767"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>Redsıs için Azure önbelleğindeki verileri içeri ve dışarı aktarma
İçeri/dışarı aktarma, redsıs veri yönetimi işlemi için bir Azure önbelleğidir. Bu, redsıs veritabanı (RDB) anlık görüntüsü için bir Azure önbelleğini bir Premium önbellekten bir olarak içeri ve dışarı aktararak bir Azure depolama hesabındaki blob. 

- **Dışarı aktarma** -REDSıS RDB anlık görüntüleri Için Azure önbelleğinizi bir sayfa blobuna dışarı aktarabilirsiniz.
- **Içeri aktarma** -REDSıS RDB anlık görüntüleri Için Azure önbelleğinizi bir sayfa blobundan ya da blok blobundan içeri aktarabilirsiniz.

İçeri/dışarı aktarma, Redsıs örnekleri için farklı Azure önbelleği arasında geçiş yapmanızı veya kullanılmadan önce verileri kullanarak önbelleğin doldurulmasını sağlar.

Bu makalede, Reda için Azure önbelleğiyle verileri içeri ve dışarı aktarmaya yönelik bir kılavuz ve sık sorulan soruların yanıtları sağlanır.

> [!IMPORTANT]
> İçeri/dışarı aktarma önizlemededir ve yalnızca [Premium katman](cache-premium-tier-intro.md) önbellekler için kullanılabilir.
>
>

## <a name="import"></a>İçeri Aktarma
İçeri aktarma, Linux, Windows veya Amazon Web Services ve diğer bulut sağlayıcılarından sonra, herhangi bir bulutta veya ortamda çalışan redin uyumlu RDB dosyalarını getirmek için kullanılabilir. Verilerin içeri aktarılması, önceden doldurulmuş verilerle bir önbellek oluşturmanın kolay bir yoludur. İçeri aktarma işlemi sırasında Redsıs için Azure önbelleği, RDB dosyalarını Azure depolama alanından belleğe yükler ve ardından anahtarları önbelleğe ekler.

> [!NOTE]
> İçeri aktarma işlemine başlamadan önce, redsıs veritabanı (RDB) dosyanızın veya dosyalarınızın, Redsıs örneği için Azure önbelleğiniz ile aynı bölgedeki ve abonelikteki Azure Storage 'daki sayfa veya blok bloblarına yüklendiğinden emin olun. Daha fazla bilgi için bkz. [Azure Blob depolamayı kullanmaya başlama](../storage/blobs/storage-dotnet-how-to-use-blobs.md). [Redsıs dışa aktarma özelliği Için Azure önbelleğini](#export) kullanarak RDB dosyanızı dışarı aktardıysanız, RDB dosyanız bir sayfa blobuna zaten depolanmış ve içeri aktarmaya hazırmış olur.
>
>

1. Dışarı aktarılan bir veya daha fazla önbellek bloblarını içeri aktarmak için Azure portal [önbelleğinize gidin](cache-configure.md#configure-azure-cache-for-redis-settings) ve **kaynak menüsünde** **verileri içeri aktar** ' a tıklayın.

    ![Veri içeri aktarma](./media/cache-how-to-import-export-data/cache-import-data.png)
2. **BLOB Seç** ' e tıklayın ve içeri aktarılacak verileri içeren depolama hesabını seçin.

    ![Depolama hesabı seçin](./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png)
3. İçeri aktarılacak verileri içeren kapsayıcıya tıklayın.

    ![Kapsayıcı seçin](./media/cache-how-to-import-export-data/cache-import-choose-container.png)
4. Blob adının solundaki alana tıklayarak içeri aktarılacak bir veya daha fazla BLOB seçin ve ardından **Seç**' e tıklayın.

    ![Blob seçme](./media/cache-how-to-import-export-data/cache-import-choose-blobs.png)
5. İçeri aktarma işlemine başlamak için **Içeri aktar** ' a tıklayın.

   > [!IMPORTANT]
   > Önbellekte içeri aktarma işlemi sırasında önbellek istemcileri tarafından erişilebilir değildir ve önbellekteki mevcut veriler silinir.
   >
   >

    ![İçeri Aktarma](./media/cache-how-to-import-export-data/cache-import-blobs.png)

    Azure portal bildirimlerini izleyerek veya [Denetim günlüğündeki](../azure-resource-manager/resource-group-audit.md)olayları görüntüleyerek içeri aktarma işleminin ilerlemesini izleyebilirsiniz.

    ![İçeri aktarma ilerleme durumu](./media/cache-how-to-import-export-data/cache-import-data-import-complete.png)

## <a name="export"></a>Dışarı Aktarma
Dışarı aktarma, redin için Azure önbelleğinde depolanan verileri Redsıs uyumlu RDB dosyaları 'na dışarı aktarıp verbırakmanıza olanak tanır. Bu özelliği, Redsıs örneği için bir Azure önbelleğinden diğerine veya başka bir Redsıs sunucusuna veri taşımak için kullanabilirsiniz. Dışarı aktarma işlemi sırasında, Redsıs sunucu örneği için Azure önbelleğini barındıran VM 'de geçici bir dosya oluşturulur ve dosya belirtilen depolama hesabına yüklenir. Dışarı aktarma işlemi başarı veya başarısızlık durumu ile tamamlandığında geçici dosya silinir.

1. Önbelleğin geçerli içeriğini depolama alanına aktarmak için Azure portal [önbelleğinize gidin](cache-configure.md#configure-azure-cache-for-redis-settings) ve **Kaynak menüsünden** **verileri dışarı aktar** ' a tıklayın.

    ![Depolama kapsayıcısını seçin](./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png)
2. **Depolama kapsayıcısını Seç** ' e tıklayın ve istenen depolama hesabını seçin. Depolama hesabının önbelleğiniz ile aynı abonelikte ve bölgede olması gerekir.

   > [!IMPORTANT]
   > Dışa aktarma, hem klasik hem de Kaynak Yöneticisi depolama hesapları tarafından desteklenen, ancak şu anda BLOB depolama hesapları tarafından desteklenmeyen sayfa Blobları ile birlikte kullanılabilir. Daha fazla bilgi için bkz. [Azure depolama hesabına genel bakış](../storage/common/storage-account-overview.md).
   >

    ![Depolama hesabı](./media/cache-how-to-import-export-data/cache-export-data-choose-account.png)
3. İstediğiniz blob kapsayıcısını seçip **Seç**' e tıklayın. Yeni bir kapsayıcı kullanmak için önce **kapsayıcı Ekle** ' ye tıklayın, ardından bunu ekleyin ve listeden seçin.

    ![Depolama kapsayıcısını seçin](./media/cache-how-to-import-export-data/cache-export-data-container.png)
4. Dışarı aktarma işlemini başlatmak için bir **BLOB adı ön eki** yazın ve **dışarı aktar** ' a tıklayın. Blob adı ön eki, bu dışarı aktarma işlemi tarafından oluşturulan dosyaların adlarına önek atamak için kullanılır.

    ![Dışarı Aktarma](./media/cache-how-to-import-export-data/cache-export-data.png)

    Azure portal bildirimleri izleyerek veya [Denetim günlüğündeki](../azure-resource-manager/resource-group-audit.md)olayları görüntüleyerek dışa aktarma işleminin ilerlemesini izleyebilirsiniz.

    ![Verileri dışarı aktarma işlemi Tamam](./media/cache-how-to-import-export-data/cache-export-data-export-complete.png)

    Önbellekler dışa aktarma işlemi sırasında kullanılabilir kalır.

## <a name="importexport-faq"></a>İçeri/dışarı aktarma SSS
Bu bölümde Içeri/dışarı aktarma özelliği hakkında sık sorulan sorular yer almaktadır.

* [Içeri/dışarı aktarma için hangi fiyatlandırma katmanları kullanılabilir?](#what-pricing-tiers-can-use-importexport)
* [Herhangi bir Redsıs sunucusundan veri aktarabilir miyim?](#can-i-import-data-from-any-redis-server)
* [Hangi RDB sürümlerini içeri aktarabilirim?](#what-rdb-versions-can-i-import)
* [Önbelleğim Içeri/dışarı aktarma işlemi sırasında kullanılabilir mi?](#is-my-cache-available-during-an-importexport-operation)
* [Redsıs kümesiyle Içeri/dışarı aktarmayı kullanabilir miyim?](#can-i-use-importexport-with-redis-cluster)
* [Özel veritabanları ayarıyla Içeri/dışarı aktarma nasıl çalışır?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Redsıs kalıcılığı 'nden Içeri/dışarı aktarma farkı nedir?](#how-is-importexport-different-from-redis-persistence)
* [PowerShell, CLı veya diğer yönetim istemcilerini kullanarak Içeri/dışarı aktarmayı otomatik hale getirebilir miyim?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Içeri/dışarı aktarma işlemi sırasında bir zaman aşımı hatası aldım. Ne anlama geliyor?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Verileri Azure Blob depolamaya verirken bir hata aldım. Ne oldu?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Içeri/dışarı aktarma için hangi fiyatlandırma katmanları kullanılabilir?
İçeri/dışarı aktarma yalnızca Premium fiyatlandırma katmanında kullanılabilir.

### <a name="can-i-import-data-from-any-redis-server"></a>Herhangi bir Redsıs sunucusundan veri aktarabilir miyim?
Evet, Redsıs örnekleri için Azure önbelleğinden dışarı aktarılan verileri içeri aktarmanın yanı sıra, RDB dosyalarını Linux, Windows veya Amazon Web Services gibi bulut sağlayıcıları gibi herhangi bir bulutta veya ortamda çalışan herhangi bir Redsıs sunucusundan içeri aktarabilirsiniz. Bunu yapmak için, istenen redo sunucusundan RDB dosyasını bir Azure Storage hesabındaki bir sayfaya veya blok blobuna yükleyin ve ardından bunu Redsıs örneği için Premium Azure önbelleğinize aktarın. Örneğin, verileri üretim önbelleğinizi dışarı aktarmak ve test ya da geçiş için hazırlama ortamının bir parçası olarak kullanılan bir önbelleğe içeri aktarmak isteyebilirsiniz.

> [!IMPORTANT]
> Redsıs sunucularından dışarı aktarılan verileri, bir Sayfa Blobu kullanılırken redo sunuculardan dışarı aktarmak için, Sayfa Blobu boyutu 512 bayt sınırında hizalı olmalıdır. Gerekli bayt doldurmasını gerçekleştirmek için örnek kod için bkz. [örnek Sayfa Blobu karşıya yükleme](https://github.com/JimRoberts-MS/SamplePageBlobUpload).
> 
> 

### <a name="what-rdb-versions-can-i-import"></a>Hangi RDB sürümlerini içeri aktarabilirim?

Redsıs için Azure önbelleği, RDB sürüm 7 aracılığıyla RDB içeri aktarmayı destekler.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>Önbelleğim Içeri/dışarı aktarma işlemi sırasında kullanılabilir mi?
* **Dışarı aktarma** -önbellekler kullanılabilir kalır ve dışarı aktarma işlemi sırasında önbelleğinizi kullanmaya devam edebilirsiniz.
* İçeri aktarma işlemi başladığında **içeri** aktarma önbellekleri kullanılamaz hale gelir ve içeri aktarma işlemi tamamlandığında kullanılabilir hale gelir.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Redsıs kümesiyle Içeri/dışarı aktarmayı kullanabilir miyim?
Evet, kümelenmiş bir önbellek ve kümelenmemiş bir önbellek arasında içeri/dışarı aktarma yapabilirsiniz. Redin kümesi [yalnızca 0 veritabanını desteklediğinden](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), 0 dışındaki veritabanlarındaki tüm veriler içeri aktarılmaz. Kümelenmiş önbellek verileri içeri aktarıldığında, anahtarlar kümenin parçaları arasında yeniden dağıtılır.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Özel veritabanları ayarıyla Içeri/dışarı aktarma nasıl çalışır?
Bazı fiyatlandırma katmanları farklı [veritabanları sınırlarına](cache-configure.md#databases)sahiptir, bu nedenle, önbellek oluşturma sırasında `databases` ayarı için özel bir değer yapılandırdıysanız içeri aktarırken bazı hususlar vardır.

* Bir fiyatlandırma katmanına aktarma sırasında, daha düşük bir `databases` limitini içeri aktardığınız katmanla sınırla:
  * Tüm fiyatlandırma katmanları için 16 olan varsayılan `databases`sayısını kullanıyorsanız, hiçbir veri kaybolmaz.
  * İçeri aktardığınız katman limitlerinin sınırları dahilinde olan özel sayıda `databases` kullanıyorsanız, hiçbir veri kaybolmaz.
  * Verdiğiniz veriler, yeni katmanın sınırlarını aşan bir veritabanında veri içeriyorsa, bu daha yüksek veritabanlarının verileri içeri aktarılmaz.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Redsıs kalıcılığı 'nden Içeri/dışarı aktarma farkı nedir?
Redsıs kalıcılığı için Azure önbelleği, Redsıs 'de depolanan verileri Azure depolama 'ya kalıcı hale bırakmanıza olanak tanır. Kalıcılık yapılandırıldığında, redin için Azure önbelleği, yapılandırılabilir bir yedekleme sıklığına bağlı olarak redsıs binary biçimindeki redin için Azure önbelleğinin anlık görüntüsünü diske sürdürür. Hem birincil hem de çoğaltma önbelleğini devre dışı bırakan çok zararlı bir olay oluşursa, önbellek verileri en son anlık görüntü kullanılarak otomatik olarak geri yüklenir. Daha fazla bilgi için bkz. [Redu Için Premium Azure önbelleği için veri kalıcılığını yapılandırma](cache-how-to-premium-persistence.md).

İçeri/dışarı aktarma, redin için Azure Cache 'e veri almanıza veya dışarı aktarma yapmanıza olanak sağlar. Redsıs kalıcılığı kullanılarak yedeklemeyi ve geri yüklemeyi yapılandırmaz.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>PowerShell, CLı veya diğer yönetim istemcilerini kullanarak Içeri/dışarı aktarmayı otomatik hale getirebilir miyim?
Evet, PowerShell yönergeleri için bkz. [redsıs Için Azure önbelleğini içeri aktarma](cache-how-to-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis) ve [Redsıs Için bir Azure önbelleğini dışarı aktarma](cache-how-to-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Içeri/dışarı aktarma işlemi sırasında bir zaman aşımı hatası aldım. Ne anlama geliyor?
İşlemi başlatmadan önce **verileri Içeri aktar** veya **verileri dışarı aktar** dikey penceresinde 15 dakikadan daha uzun bir süre devam ederseniz, aşağıdaki örneğe benzer bir hata iletisiyle karşılaşırsınız:

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Bu sorunu çözmek için, 15 dakikadan önce içeri aktarma veya dışarı aktarma işlemini başlatın.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Verileri Azure Blob depolamaya verirken bir hata aldım. Ne oldu?
Dışarı aktarma yalnızca sayfa Blobları olarak depolanan RDB dosyaları ile birlikte kullanılabilir. Diğer blob türleri, sık ve seyrek katmanlara sahip BLOB depolama hesapları da dahil olmak üzere şu anda desteklenmemektedir. Daha fazla bilgi için bkz. [Azure depolama hesabına genel bakış](../storage/common/storage-account-overview.md).

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla Premium önbellek özelliği kullanmayı öğrenin.

* [Redsıs Premium katmanı için Azure önbelleğine giriş](cache-premium-tier-intro.md)
