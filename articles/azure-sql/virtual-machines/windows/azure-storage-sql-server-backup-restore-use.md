---
title: SQL Server Yedekleme ve geri yükleme için Azure Storage 'ı kullanma | Microsoft Docs
description: SQL Server Azure depolama 'ya nasıl yedekleyeceğinizi öğrenin. SQL veritabanlarını Azure depolama 'ya yedeklemenin avantajlarını açıklar.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.openlocfilehash: 35fff49a53f5a0a9532fd0dff841356c5deaf3ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97724791"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>SQL Server Yedekleme ve geri yükleme için Azure Storage 'ı kullanma
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

SQL Server 2012 SP1 CU2 UYGULAMAZSANıZ 'den başlayarak, artık SQL Server veritabanlarını doğrudan Azure Blob depolama alanına yazabilirsiniz. Azure Blob depolama alanından yedeklemek ve geri yüklemek için bu işlevselliği kullanın. Buluta yedekleme, kullanılabilirlik, sınırsız coğrafi olarak çoğaltılan, site dışı depolamanın ve buluta veri geçişi kolaylığının avantajları sunar. `BACKUP` `RESTORE` Transact-SQL veya smo kullanarak sorun veya deyimler verebilirsiniz.

## <a name="overview"></a>Genel Bakış
SQL Server 2016 yeni özellikleri tanıtır; [dosya anlık görüntüsü yedeklemesini](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) , neredeyse anlık yedeklemeler ve inanılmaz hızlı geri yüklemeleri gerçekleştirmek için kullanabilirsiniz.

Bu konuda, Azure Storage 'ı SQL Server yedeklemeleri için kullanmayı tercih edebileceğinizi ve ilgili bileşenleri nasıl belirleyeceğiniz açıklanmaktadır. Bu hizmeti SQL Server yedeklemelerinizle kullanmaya başlamak için adım adım kılavuzlarına ve ek bilgilere erişmek üzere makalenin sonunda sunulan kaynakları kullanabilirsiniz.

## <a name="benefits-of-using-azure-blob-storage-for-sql-server-backups"></a>SQL Server yedeklemeleri için Azure Blob depolamayı kullanmanın avantajları
SQL Server yedeklenirken birçok zorluk söz konusu olabilir. Bu güçlükler depolama yönetimi, depolama hatası riski, site dışı depolamaya erişim ve donanım yapılandırması içerir. Bu güçlüklerin birçoğu SQL Server yedeklemeleri için Azure Blob depolama kullanılarak ele alınır. Aşağıdaki avantajları göz önünde bulundurun:

* **Kullanım kolaylığı**: yedeklemelerinizi Azure Bloblarında depolamak kullanışlı, esnek ve site dışı bir seçeneğe sahip olabilir. SQL Server yedeklemeleriniz için site dışı depolama oluşturmak, var olan betiklerinizi/işlerinizi, **URL 'ye yedekleme** söz dizimini kullanacak şekilde değiştirmek kadar kolay olabilir. Site dışı depolamanın, genellikle site dışı ve üretim veritabanı konumlarını etkileyebilecek tek bir olağanüstü durum oluşmasını engellemek için üretim veritabanı konumundan yeterince fazla olması gerekir. [Azure bloblarınızı coğrafi olarak çoğaltmayı](../../../storage/common/storage-redundancy.md)seçerek, tüm bölgeyi etkileyebilecek bir olağanüstü durum durumunda ek bir koruma katmanınız vardır.
* **Yedekleme Arşivi**: Azure Blob depolama, yedeklemeleri arşivlemek için sık kullanılan bant seçeneğine daha iyi bir alternatif sunar. Bant depolama, medyayı korumak için bir site dışı tesis ve ölçülere fiziksel taşıma gerektirebilir. Yedeklemelerinizi Azure Blob Storage 'da depolamak, anlık, yüksek oranda kullanılabilir ve dayanıklı bir arşivleme seçeneği sağlar.
* **Yönetilen donanım**: Azure hizmetleriyle donanım yönetiminin ek yükü yoktur. Azure hizmetleri donanımı yönetebilir ve donanım arızalarına karşı artıklık ve koruma için coğrafi çoğaltma sağlar.
* **Sınırsız depolama**: Azure Blob 'larına doğrudan yedekleme etkinleştirerek, neredeyse sınırsız depolamaya erişebilirsiniz. Alternatif olarak, bir Azure sanal makine diskine yedekleme, makine boyutuna bağlı sınırlara sahiptir. Yedeklemeler için bir Azure sanal makinesine iliştirebilmeniz için disk sayısıyla ilgili bir sınır vardır. Bu sınır, çok büyük bir örnek için 16 disk ve daha küçük örnekler için daha azdır.
* **Yedekleme kullanılabilirliği**: Azure Blob 'larında depolanan yedeklemeler her yerden ve dilediğiniz zaman KULLANILABILIR ve VHD 'yi ekleme/ayırma ya da indirme ve iliştirme gerekmeden SQL Server örneğine geri yükleme için kolayca erişilebilir.
* **Maliyet**: yalnızca kullanılan hizmet için ödeme yapın. , Site dışı ve yedekleme Arşivi seçeneği olarak uygun maliyetli olabilir. Daha fazla bilgi için bkz. [Azure Fiyatlandırma Hesaplayıcı](https://go.microsoft.com/fwlink/?LinkId=277060 "Fiyatlandırma Hesaplayıcı")ve [Azure fiyatlandırma makalesi](https://go.microsoft.com/fwlink/?LinkId=277059 "Fiyatlandırma makalesi") .
* **Depolama anlık görüntüleri**: veritabanı dosyaları bir Azure blobuna depolandığında ve SQL Server 2016 kullanıyorsanız, neredeyse anlık yedeklemeler ve inanılmaz hızlı geri yüklemeleri gerçekleştirmek için [dosya anlık görüntüsü yedeklemesini](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) kullanabilirsiniz.

Daha ayrıntılı bilgi için bkz. [Azure Blob depolama Ile yedekleme ve geri yükleme SQL Server](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service).

Aşağıdaki iki bölümde, gerekli SQL Server bileşenleri dahil olmak üzere Azure Blob depolama alanı tanıtılmaktadır. Azure Blob depolama alanından yedekleme ve geri yükleme özelliklerini başarılı bir şekilde kullanmaya yönelik bileşenleri ve bunların etkileşimini anlamak önemlidir.

## <a name="azure-blob-storage-components"></a>Azure Blob depolama bileşenleri
Aşağıdaki Azure bileşenleri Azure Blob depolama alanına yedeklenirken kullanılır.

| Bileşen | Açıklama |
| --- | --- |
| **Depolama hesabı** |Depolama hesabı, tüm depolama hizmetleri için başlangıç noktasıdır. Azure Blob depolama alanına erişmek için önce bir Azure depolama hesabı oluşturun. Azure Blob depolama hakkında daha fazla bilgi için bkz. [Azure Blob depolamayı kullanma](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/). |
| **Kapsayıcı** |Bir kapsayıcı bir blob kümesi gruplandırması sağlar ve sınırsız sayıda blob saklayabilir. Azure Blob depolamaya SQL Server bir yedekleme yazmak için, en azından kök kapsayıcının oluşturulması gerekir. |
| **Blob** |Herhangi bir tür ve boyutun dosyası. Bloblar Şu URL biçimi kullanılarak adreslenebilir: `https://<storageaccount>.blob.core.windows.net/<container>/<blob>` . Sayfa Blobları hakkında daha fazla bilgi için bkz. [blok ve sayfa Bloblarını anlama](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) |

## <a name="sql-server-components"></a>SQL Server bileşenleri
Aşağıdaki SQL Server bileşenleri Azure Blob depolama alanına yedeklenirken kullanılır.

| Bileşen | Açıklama |
| --- | --- |
| **URL** |URL, benzersiz bir yedekleme dosyasına bir Tekdüzen Kaynak tanımlayıcısı (URI) belirtir. URL, SQL Server yedekleme dosyasının konumunu ve adını sağlar. URL yalnızca bir kapsayıcıyı değil, gerçek bir bloba işaret etmelidir. Blob yoksa, Azure tarafından oluşturulur. Mevcut bir blob belirtilmişse, seçenek belirtilmediği takdirde yedekleme komutu başarısız olur `WITH FORMAT` . Aşağıda, yedekleme komutunda belirlediğiniz URL 'nin bir örneği verilmiştir: `https://<storageaccount>.blob.core.windows.net/<container>/<FILENAME.bak>` .<br><br> HTTPS önerilir, ancak gerekli değildir. |
| **Kimlik Bilgisi** |Azure Blob depolamaya bağlanmak ve kimliğini doğrulamak için gereken bilgiler bir kimlik bilgisi olarak depolanır. SQL Server bir Azure Blobuna yedekleme veya geri yükleme için bir SQL Server kimlik bilgisinin oluşturulması gerekir. Daha fazla bilgi için [SQL Server kimlik bilgileri](/sql/t-sql/statements/create-credential-transact-sql)bölümüne bakın. |

> [!NOTE]
> SQL Server 2016, blok bloblarını destekleyecek şekilde güncelleştirildi. Daha fazla ayrıntı için lütfen bkz. [öğretici: SQL Server 2016 veritabanlarıyla Microsoft Azure Blob depolama kullanma](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016) .
> 

## <a name="next-steps"></a>Sonraki adımlar

1. Henüz yoksa bir Azure hesabı oluşturun. Azure 'u değerlendiriyorsanız, [ücretsiz denemeyi](https://azure.microsoft.com/free/)göz önünde bulundurun.
2. Ardından, depolama hesabı oluşturma ve geri yükleme gerçekleştirme konusunda size kılavuzluk eden aşağıdaki öğreticilerden birini inceleyin.
   
   * **SQL Server 2014**: [öğretici: SQL Server 2014 yedekleme ve Microsoft Azure Blob depolamaya geri yükleme](/previous-versions/sql/2014/relational-databases/backup-restore/sql-server-backup-to-url).
   * **SQL Server 2016**: [öğretici: SQL Server 2016 veritabanları ile Microsoft Azure Blob depolamayı kullanma](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016)
3. [SQL Server Yedekleme ve Microsoft Azure Blob depolama Ile geri yükleme](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)ile başlayarak ek belgeleri gözden geçirin.

Herhangi bir sorununuz varsa, [URL 'Yi En Iyi uygulamalar ve sorun giderme SQL Server](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting)konusunu gözden geçirin.

Yedekleme ve geri yükleme seçeneklerinin diğer SQL Server için bkz. [Azure sanal makinelerinde SQL Server Için Yedekleme ve geri yükleme](backup-restore.md).