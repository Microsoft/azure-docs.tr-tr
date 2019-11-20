---
title: Azure Data Box ve diğer yöntemleri kullanarak Azure Dosya Eşitleme verileri geçirme
description: Toplu verileri Azure Dosya Eşitleme ile uyumlu bir şekilde geçirin.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9264aa6d24256b991abefe35b41045caa2e76d67
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997778"
---
# <a name="migrate-bulk-data-to-azure-file-sync"></a>Toplu verileri Azure Dosya Eşitleme geçirme
Toplu verileri Azure Dosya Eşitleme iki şekilde taşıyabilirsiniz:

* **Azure Dosya Eşitleme kullanarak dosyalarınızı karşıya yükleyin.** Bu en basit yöntemdir. Dosyalarınızı yerel olarak Windows Server 2012 R2 veya sonraki bir sürüme taşıyın ve Azure Dosya Eşitleme aracısını yüklersiniz. Eşitlemeyi ayarladıktan sonra dosyalarınız sunucudan karşıya yüklenir. (Müşterilerimiz Şu anda iki günde bir yaklaşık 1 TiB karşıya yükleme hızıyla karşılaşmakta.) Sunucunuzun veri merkeziniz için çok fazla bant genişliği kullandığından emin olmak için bir [bant genişliği azaltma zamanlaması](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)ayarlamak isteyebilirsiniz.
* **Dosyalarınızı çevrimdışına aktarın.** Yeterli bant genişliğine sahip değilseniz, dosyaları Azure 'a makul bir süre içinde karşıya yükleyemeyebilirsiniz. Sınama tüm dosya kümesinin ilk eşitledir. Bu zorluğu aşmak için [Azure Data Box ailesi](https://azure.microsoft.com/services/storage/databox)gibi çevrimdışı toplu geçiş araçlarını kullanın. 

Bu makalede, dosyaları Azure Dosya Eşitleme ile uyumlu olacak şekilde çevrimdışı geçirme açıklanmaktadır. Dosya çakışmalarını önlemek ve eşitlemeyi etkinleştirdikten sonra dosya ve klasör erişim denetim listelerini (ACL 'Ler) ve zaman damgalarını korumak için bu yönergeleri izleyin.

## <a name="migration-tools"></a>Geçiş araçları
Bu makalede anladığımız işlem yalnızca Data Box için değil, diğer çevrimdışı geçiş araçları için de geçerlidir. Ayrıca, AzCopy, Robocopy veya iş ortağı araçları ve internet üzerinden doğrudan çalışan hizmetler gibi araçlar için de çalışır. Ancak, ilk karşıya yükleme sınamasını aşmak için, bu araçları Azure Dosya Eşitleme ile uyumlu bir şekilde kullanmak için bu makaledeki adımları izleyin.

Bazı durumlarda, Azure Dosya Eşitleme benimsemadan önce bir Windows Server 'dan başka bir Windows Server 'a taşımanız gerekir. [Depolama geçiş hizmeti](https://aka.ms/storagemigrationservice) (SMS), bu konuda yardımcı olabilir. Azure Dosya Eşitleme (Windows Server 2012R2 ve üzerinde) tarafından desteklenen bir sunucu işletim sistemi sürümüne geçiş yapmanız gerekip gerekmediğini veya Azure Dosya Eşitleme için yeni bir sistem satın alırken yalnızca geçiş yapmanız gerekiyorsa, SMS 'nin migr 'nizin sağlanmasına yardımcı olacak çok sayıda özelliği ve avantajı vardır işlem sorunsuz şekilde yapılır.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Çevrimdışı veri aktarmak için araç kullanmanın avantajları
Çevrimdışı geçiş için Data Box gibi bir aktarım aracı kullanmanın başlıca avantajları aşağıda verilmiştir:

- Tüm dosyalarınızı ağ üzerinden yüklemeniz gerekmez. Büyük ad alanları için, bu araç önemli miktarda ağ bant genişliği ve saati kaydedebilir.
- Azure Dosya Eşitleme kullandığınızda, kullandığınız aktarım aracı (Data Box, Azure Içeri/dışarı aktarma hizmeti vb.) kullanıldığında, canlı sunucunuz yalnızca verileri Azure 'a taşıdıktan sonra değişen dosyaları karşıya yükler.
- Azure Dosya Eşitleme, çevrimdışı toplu geçiş aracı ACL 'Leri taşımasa bile dosya ve klasörünüzün ACL 'Lerini eşitler.
- Data Box ve Azure Dosya Eşitleme kapalı kalma süresi gerektirmez. Verileri Azure 'a aktarmak için Data Box kullandığınızda, ağ bant genişliğini verimli bir şekilde kullanırsınız ve dosya kalitesini koruyabilirsiniz. Ayrıca, verileri Azure 'a taşıdıktan sonra yalnızca değişen dosyaları karşıya yükleyerek, ad alanınızı güncel tutmanız gerekir.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Çevrimdışı veri aktarımı önkoşulları
Çevrimdışı veri Aktarımınız tamamlanmadan önce geçirdiğiniz sunucuda eşitlemeyi etkinleştirmemelisiniz. Başlamadan önce göz önünde bulundurmanız gereken diğer şeyler şunlardır:

- Toplu geçişiniz için Data Box kullanmayı planlıyorsanız: [Data Box için dağıtım önkoşullarını](../../databox/data-box-deploy-ordered.md#prerequisites)gözden geçirin.
- Son Azure Dosya Eşitleme topolojinizi planlayın: [Azure Dosya Eşitleme dağıtımı için plan yapın](storage-sync-files-planning.md)
- Eşitlemek istediğiniz dosya paylaşımlarını barındıracak Azure depolama hesaplarını seçin. Toplu geçişinizin aynı depolama hesabındaki geçici hazırlama paylaşımlarına yapıldığından emin olun. Toplu geçiş yalnızca, aynı depolama hesabında bulunan bir son ve bir hazırlama paylaşımının kullanılmasıyla etkinleştirilebilir.
- Toplu geçiş yalnızca bir sunucu konumuyla yeni bir eşitleme ilişkisi oluşturduğunuzda kullanılabilir. Mevcut bir eşitleme ilişkisiyle toplu geçiş etkinleştiremezsiniz.


## <a name="process-for-offline-data-transfer"></a>Çevrimdışı veri aktarımı için işlem
Azure Dosya Eşitleme Azure Data Box gibi toplu geçiş araçlarıyla uyumlu olacak şekilde nasıl ayarlanacağı aşağıda verilmiştir:

![Azure Dosya Eşitleme ayarlamayı gösteren diyagram](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Adım | Ayrıntı |
|---|---------------------------------------------------------------------------------------|
| ![1\. Adım](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Data Box sıralayın](../../databox/data-box-deploy-ordered.md). Data Box ailesi, gereksinimlerinizi karşılayacak [çeşitli ürünler](https://azure.microsoft.com/services/storage/databox/data) sunmaktadır. Data Box aldığınızda, Data Box Şu UNC yoluna [verilerinizi kopyalamak için belgelerini](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) izleyin:  *\\< deviceıpaddres StorageAccountName_AzFile\> \>\<\< PaylaşımAdı\>* . Burada, *PaylaşımAdı* hazırlama paylaşımının adıdır. Data Box Azure 'a geri gönderin. |
| ![2\. Adım](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Dosyalarınız, geçici hazırlama paylaşımları olarak seçtiğiniz Azure dosya paylaşımlarında gösterilene kadar bekleyin. *Bu paylaşımlara eşitlemeyi etkinleştirmeyin.* |
| ![3\. Adım](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Sizin için Data Box oluşturulan her dosya paylaşımında yeni bir boş paylaşma oluşturun. Bu yeni paylaşımın Data Box paylaşımıyla aynı depolama hesabında olması gerekir. [Yeni bir Azure dosya paylaşımının oluşturulması](storage-how-to-create-file-share.md). |
| ![4\. Adım](media/storage-sync-files-offline-data-transfer/bullet_4.png) | Depolama eşitleme hizmetinde [bir eşitleme grubu oluşturun](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) . Boş paylaşıma bulut uç noktası olarak başvur. Her Data Box dosya paylaşımında bu adımı yineleyin. [Azure dosya eşitleme ayarlayın](storage-sync-files-deployment-guide.md). |
| ![5\. Adım](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Canlı sunucu dizininizi sunucu uç noktası olarak ekleyin](storage-sync-files-deployment-guide.md#create-a-server-endpoint). İşleminde, dosyaları Azure 'a taşımış ve hazırlama paylaşımlarına başvurulacağını belirtin. Gerektiğinde bulut katmanlamayı etkinleştirebilir veya devre dışı bırakabilirsiniz. Canlı sunucunuzda bir sunucu uç noktası oluştururken, hazırlama paylaşımında başvuru yapın. **Sunucu uç noktası Ekle** dikey penceresinde, **çevrimdışı veri aktarımı**altında, **etkin**' i seçin ve ardından bulut uç noktasıyla aynı depolama hesabında olması gereken hazırlama payını seçin. Burada, kullanılabilir paylaşımların listesi depolama hesabı ve henüz eşitlenmeyen paylaşımlar tarafından filtrelenmiştir. |

![Yeni bir sunucu uç noktası oluşturulurken çevrimdışı veri aktarımının nasıl etkinleştirileceğini gösteren Azure portal Kullanıcı arabiriminin ekran görüntüsü](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Paylaşma eşitleniyor
Sunucu uç noktanızı oluşturduktan sonra eşitleme başlar. Eşitleme işlemi, sunucu üzerindeki her bir dosyanın, Data Box dosyaları yatırın hazırlama paylaşımında de mevcut olup olmadığını belirler. Dosya orada varsa, eşitleme işlemi dosyayı sunucudan karşıya yüklemek yerine hazırlama paylaşımından kopyalar. Dosya hazırlama paylaşımında yoksa veya yerel sunucuda daha yeni bir sürüm varsa, eşitleme işlemi dosyayı yerel sunucudan yükler.

> [!IMPORTANT]
> Toplu geçiş modunu yalnızca bir sunucu uç noktası oluştururken etkinleştirebilirsiniz. Sunucu uç noktası oluşturduktan sonra, önceden eşitlenen bir sunucudan toplu geçirilen verileri ad alanına tümleştiremezsiniz.

## <a name="acls-and-timestamps-on-files-and-folders"></a>Dosya ve klasörlerdeki ACL 'Ler ve zaman damgaları
Azure Dosya Eşitleme, kullandığınız toplu geçiş aracı başlangıçta ACL 'Leri taşımasa bile, dosya ve klasör ACL 'Lerinin canlı sunucudan eşitlenmesini sağlar. Bu nedenle, hazırlama paylaşımının dosya ve klasörler için ACL 'Leri içermesi gerekmez. Yeni bir sunucu uç noktası oluştururken çevrimdışı veri geçiş özelliğini etkinleştirdiğinizde, tüm dosya ACL 'Leri sunucuda eşitlenir. Yeni oluşturulan ve değiştirilen zaman damgaları de eşitlenir.

## <a name="shape-of-the-namespace"></a>Ad alanının şekli
Eşitlemeyi etkinleştirdiğinizde, sunucunun içeriği ad alanının şeklini saptayabilir. Data Box anlık görüntü ve geçiş bittikten sonra dosyalar yerel sunucudan silinirse, bu dosyalar canlı, eşitleniyor ad alanına taşınamaz. Bunlar, hazırlama paylaşımında kalır, ancak kopyalanmaz. Eşitleme, ad alanını canlı sunucuya göre sakladığı için bu gereklidir. Data Box *anlık görüntüsü* , verimli dosya kopyalama için yalnızca bir aşamadır. Canlı ad alanı şekli için yetkili değildir.

## <a name="cleaning-up-after-bulk-migration"></a>Toplu geçişten sonra Temizleme 
Sunucu, ad alanının ilk eşitlemesini tamamladıkça, Data Box toplu geçiş dosyaları hazırlama dosya payını kullanır. Azure portal içindeki **sunucu uç noktası özellikleri** dikey penceresinde, **çevrimdışı veri aktarımı** bölümünde durum ' dan **devam** ' dan **tamamlandı**' ya değişir. 

![Çevrimdışı veri aktarımı için durum ve devre dışı bırakma denetimlerinin bulunduğu sunucu uç noktası özellikleri dikey penceresinin ekran görüntüsü](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Artık, maliyetleri kaydetmek için hazırlama paylaşımının temizleyebilmeniz gerekir:

1. **Sunucu uç noktası özellikleri** dikey penceresinde, durum **tamamlandığında** **çevrimdışı veri aktarımını devre dışı bırak**' ı seçin.
2. Maliyet tasarrufu sağlamak için hazırlama payını silmeyi göz önünde bulundurun. Hazırlama paylaşımında büyük olasılıkla dosya ve klasör ACL 'Leri yoktur, bu nedenle çok yararlı değildir. Yedekleme noktası açısından, [eşitleme Azure dosya paylaşımının gerçek bir anlık görüntüsünü](storage-snapshots-files.md)oluşturun. Bir zamanlamaya göre [anlık görüntü almak için Azure Backup ayarlayabilirsiniz]( ../../backup/backup-azure-files.md) .

Çevrimdışı veri aktarımı modunu yalnızca durum **tamamlandığında** veya bir yanlış yapılandırma nedeniyle iptal etmek istediğinizde devre dışı bırakın. Dağıtım sırasında modu devre dışı bırakırsanız, hazırlama paylaşımınız hala kullanılabilir olsa bile dosyalar sunucudan karşıya yüklenmeye başlayacaktır.

> [!IMPORTANT]
> Çevrimdışı veri aktarım modunu devre dışı bıraktıktan sonra, toplu geçişten hazırlama payı hala kullanılabilir olsa bile, yeniden etkinleştirilemez.

## <a name="next-steps"></a>Sonraki adımlar
- [Azure Dosya Eşitleme dağıtımı için plan yapın](storage-sync-files-planning.md)
- [Azure Dosya Eşitleme’yi dağıtma](storage-sync-files-deployment-guide.md)
