---
title: Azure Data Factory etkinlikte kopyalama etkinliğine göre desteklenen dosya biçimleri
description: Bu konu, Azure Data Factory ' de kopyalama etkinliği tarafından desteklenen dosya biçimlerini ve sıkıştırma kodlarını açıklamaktadır.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jingwang
ms.openlocfilehash: bb773d01124b99b4837f393b610e00ecbfa510fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100364622"
---
# <a name="supported-file-formats-and-compression-codecs-by-copy-activity-in-azure-data-factory"></a>Azure Data Factory etkinlikte kopyalama etkinliğine göre desteklenen dosya biçimleri ve sıkıştırma codec bileşenleri
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Bu makale aşağıdaki bağlayıcılar için geçerlidir: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage 1.](connector-azure-data-lake-store.md), [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md), [Azure dosya depolama](connector-azure-file-storage.md), [dosya sistemi](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md),, [](connector-hdfs.md), [http](connector-http.md)ve [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

[Kopyalama etkinliğini](copy-activity-overview.md) , dosyaları iki dosya tabanlı veri deposu arasında olduğu gibi kopyalamak için kullanabilirsiniz. Bu durumda, veriler serileştirme veya seri durumundan çıkarma yapılmadan verilerin verimli bir şekilde kopyalanabilmesi. 

Ayrıca, belirli bir biçimin dosyalarını ayrıştırır veya oluşturabilirsiniz. Örneğin, şunları yapabilirsiniz:

* SQL Server veritabanından verileri kopyalayın ve Parquet biçiminde Azure Data Lake Storage 2. yazın.
* Metin (CSV) biçimindeki dosyaları şirket içi bir dosya sisteminden kopyalayın ve avro biçiminde Azure Blob depolama alanına yazın.
* ZIP dosyalarını şirket içi bir dosya sisteminden kopyalayın, açık olarak açıp Azure Data Lake Storage 2. ve ayıklanan dosyaları yazın.
* Verileri Azure Blob depolama alanından gzip sıkıştırılmış metin (CSV) biçiminde kopyalayın ve Azure SQL veritabanı 'na yazın.
* Serileştirme/seri durumdan çıkarma veya sıkıştırma/sıkıştırmayı gerektiren çok sayıda etkinlik.

## <a name="next-steps"></a>Sonraki adımlar

Diğer kopyalama etkinliği makalelerine bakın:

- [Kopyalama etkinliğine genel bakış](copy-activity-overview.md)
- [Kopyalama etkinliği performansı](copy-activity-performance.md)
