---
title: BLOB depolama verilerini SSIS bağlayıcılarıyla taşıma-Team Data Science Process
description: Azure için SQL Server Integration Services Feature Pack kullanarak Azure Blob depolama 'ya veya buradan veri taşımayı öğrenin.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8fb802905070ab1ddc8af93e501e8e11e262cf4a
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053942"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>SSIS bağlayıcıları kullanarak verileri Azure Blob depolama alanına veya sunucudan taşıma
[Azure için SQL Server Integration Services Feature Pack](https://msdn.microsoft.com/library/mt146770.aspx) , Azure 'a bağlanmak, Azure ile şirket içi veri kaynakları arasında veri aktarımı yapmak ve Azure 'da depolanan verileri işlemek için bileşenler sağlar.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Müşteriler şirket içi verileri buluta taşıdıktan sonra Azure teknolojileri paketinin tam gücünden yararlanmak için herhangi bir Azure hizmetinden erişebilirler. Örneğin, Azure Machine Learning veya bir HDInsight kümesinde kullanılabilir.

Bu, genellikle [SQL](sql-walkthrough.md) ve [HDInsight](hive-walkthrough.md) izlenecek yolları için ilk adımdır.

Karma veri tümleştirme senaryolarında sık kullanılan iş ihtiyaçlarını başarmak için SSIS kullanan kurallı senaryolar hakkında daha fazla bilgi için bkz. [Azure blogu için SQL Server Integration Services Feature Pack ile daha fazla](https://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) çalışma.

> [!NOTE]
> Azure Blob depolamaya yönelik kapsamlı bir giriş için [Azure Blob temelleri](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) ve [Azure Blob hizmeti](https://msdn.microsoft.com/library/azure/dd179376.aspx)' ne bakın.
> 
> 

## <a name="prerequisites"></a>Önkoşullar
Bu makalede açıklanan görevleri gerçekleştirmek için bir Azure aboneliğiniz ve ayarlanmış bir Azure depolama hesabınızın olması gerekir. Verileri karşıya yüklemek veya indirmek için Azure depolama hesabı adınızı ve hesap anahtarınızı bilmeniz gerekir.

* Bir **Azure aboneliği**ayarlamak için bkz. [ücretsiz bir aylık deneme sürümü](https://azure.microsoft.com/pricing/free-trial/).
* **Depolama hesabı** oluşturma ve hesap ve anahtar bilgilerini alma hakkında yönergeler için bkz. [Azure Storage hesapları hakkında](../../storage/common/storage-create-storage-account.md).

**SSIS bağlayıcılarını**kullanmak için şunu indirmeniz gerekir:

* **SQL Server 2014 veya 2016 Standard (veya üzeri)** : ınstall, SQL Server Integration Services içerir.
* **Azure için Microsoft SQL Server 2014 veya 2016 Integration Services özellik paketi**: Bunlar, sırasıyla [SQL Server 2014 tümleştirme hizmetleri](https://www.microsoft.com/download/details.aspx?id=47366) ve [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) sayfalarından indirilebilir.

> [!NOTE]
> SSIS SQL Server ile yüklenir, ancak Express sürümüne dahil değildir. SQL Server çeşitli sürümlerinde hangi uygulamaların dahil olduğu hakkında bilgi için, bkz. [SQL Server sürümleri](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

SSIS 'deki eğitim malzemeleri için bkz. [SSIS Için uygulamalı eğitim](https://www.microsoft.com/sql-server/training-certification)

Basit ayıklama, dönüştürme ve yükleme (ETL) paketleri oluşturmak için SISS kullanarak nasıl çalışmaya başlacağınız hakkında bilgi için bkz. [SSIS öğreticisi: Basit BIR etl paketi oluşturma](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>NYC TAXI veri kümesini indir
Burada açıklanan örnek, genel kullanıma açık bir veri kümesi ( [NYC TAXI gidiş](https://www.andresmh.com/nyctaxitrips/) veri kümesi) kullanır. Veri kümesi, 2013 yılında NYC 'de yaklaşık 173.000.000 TAXI bayıldığı 'ten oluşur. İki tür veri vardır: seyahat ayrıntıları verileri ve tarifeli havayolu verileri. Her ay bir dosya olduğu için, her biri yaklaşık 2 GB boyutunda sıkıştırılmamış olan 24 dosya vardır.

## <a name="upload-data-to-azure-blob-storage"></a>Azure Blob depolama alanına veri yükleme
SSIS özellik paketini Şirket içinden Azure Blob depolama 'ya kullanarak verileri taşımak için, burada gösterildiği gibi [**Azure Blob karşıya yükleme görevinin**](https://msdn.microsoft.com/library/mt146776.aspx)bir örneğini kullanırız:

![Configure-Data-Science-VM](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

Görevin kullandığı parametreler burada açıklanmıştır:

| Alan | Açıklama |
| --- | --- |
| **Azurestorampaconnection** |Mevcut bir Azure depolama bağlantı yöneticisini belirtir veya blob dosyalarının barındırıldığı yeri işaret eden bir Azure depolama hesabına başvuran yeni bir tane oluşturur. |
| **BlobContainer** |Karşıya yüklenen dosyaları blob olarak tutan blob kapsayıcısının adını belirtir. |
| **BlobDirectory** |Karşıya yüklenen dosyanın bir Blok Blobu olarak depolandığı blob dizinini belirtir. Blob dizini bir sanal hiyerarşik yapısıdır. Blob zaten mevcutsa, o şekilde değişir. |
| **Yerel Dizin** |Karşıya yüklenecek dosyaları içeren yerel dizini belirtir. |
| **Kısaltın** |Belirtilen ad düzenine sahip dosyaları seçmek için bir ad filtresi belirtir. Örneğin, MySheet\*. xls\* MySheet001. xls ve MySheetABC. xlsx gibi dosyaları içerir |
| **TimeRangeFrom/TimeRangeTo** |Bir zaman aralığı filtresi belirtir. *Timerangefrom* ve Before *timerangeto* 'dan sonra değiştirilen dosyalar. |

> [!NOTE]
> **Azurestorampaconnection** kimlik bilgilerinin doğru olması gerekir ve aktarım denendiğinde **blobcontainer** değeri bulunmalıdır.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Azure Blob depolamadan veri yükleme
SSIS ile şirket içi depolamaya Azure Blob depolamadan veri indirmek için [Azure Blob Indirme görevinin](https://msdn.microsoft.com/library/mt146779.aspx)bir örneğini kullanın.

## <a name="more-advanced-ssis-azure-scenarios"></a>Daha gelişmiş SSIS-Azure senaryoları
SSIS özellik paketi, paketleme görevleri ile birlikte daha karmaşık akışların işlenmesine olanak tanır. Örneğin, blob verileri, çıkışı bir blob 'a ve ardından Şirket içi depolamaya geri yüklenebilen bir HDInsight kümesine doğrudan akış yapılabilir. SSIS, ek SSIS bağlayıcıları kullanarak bir HDInsight kümesinde Hive ve Pig işleri çalıştırabilir:

* SSIS ile bir Azure HDInsight kümesinde Hive betiğini çalıştırmak için [Azure HDInsight Hive görevini](https://msdn.microsoft.com/library/mt146771.aspx)kullanın.
* SSIS ile bir Azure HDInsight kümesinde Pig betiği çalıştırmak için [Azure HDInsight Pig görevini](https://msdn.microsoft.com/library/mt146781.aspx)kullanın.

