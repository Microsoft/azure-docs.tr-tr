---
title: 'Azure Data Factory kullanarak verileri artımlı olarak kopyalama '
description: Bu öğreticiler verilerin, kaynak veri deposundan hedef veri deposuna nasıl artımlı olarak kopyalanacağını gösterir. İlki bir tablodan veri kopyalar.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: 9cbe9dfea116df30f49b89e2e59886f2f112487c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683369"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Bir kaynak veri deposundan hedef veri deposuna artımlı olarak veri yükleme

İlk veriler yüklendikten sonra verileri artımlı olarak (veya delta) yükleme senaryosu, veri tümleştirme çözümlerinde sıkça kullanılır. Bu bölümdeki öğreticiler, Azure Data Factory kullanarak artımlı veri yüklemenin farklı yollarını gösterir.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Bir filigran kullanarak veritabanından Delta verileri yükleme
Bu durumda, kaynak veritabanında bir filigran tanımlayın. Filigran, en son güncelleştirilen zaman damgası veya artan bir anahtarı olan bir sütundur. Delta yükleme çözümü, eski bir filigran ile yeni bir filigran arasında değiştirilen verileri yükler. Bu yaklaşıma yönelik iş akışı şu diyagramda gösterilmiştir: 

![Filigran kullanmaya yönelik iş akışı](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Adım adım yönergeler için şu öğreticilere bakın: 
- [Azure SQL Veritabanı’ndaki bir tablodan Azure Blob depolama Alanı’na artımlı olarak veri kopyalama](tutorial-incremental-copy-powershell.md)
- [Şirket içi SQL Server’daki birden fazla tablodan Azure SQL Veritabanı’na artımlı olarak veri kopyalama](tutorial-incremental-copy-multiple-tables-powershell.md)

Şablonlar için aşağıdakilere bakın:
- [Denetim tablosu ile Delta kopyası](solution-template-delta-copy-with-control-table.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Değişiklik İzleme teknolojisini kullanarak SQL DB 'den Delta verileri yükleme
Değişiklik İzleme teknolojisi, SQL Server ve Azure SQL Veritabanı’nda bulunan, uygulamalar için verimli bir değişiklik izleme mekanizması sağlayan basit bir çözümdür. Bir uygulamanın eklenen, güncelleştirilen veya silinen verileri kolayca tanımlamasına olanak sağlar. 

Bu yaklaşıma yönelik iş akışı şu diyagramda gösterilmiştir:

![Değişiklik İzleme kullanmaya yönelik iş akışı](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Adım adım yönergeler için şu öğreticilere bakın: <br/>
- [Değişiklik İzleme teknolojisini kullanarak Azure SQL Veritabanı’ndan Azure Blob depolama alanına artımlı olarak veri kopyalama](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>Yalnızca LastModifiedDate kullanarak yeni ve değiştirilmiş dosyaları yükleme
Yalnızca LastModifiedDate kullanarak hedef depoya yeni ve değiştirilmiş dosyaları kopyalayabilirsiniz. ADF, kaynak deposundaki tüm dosyaları tarar, dosya filtresini LastModifiedDate göre uygular ve yalnızca hedef depoya son kez bu yana yeni ve güncelleştirilmiş dosyayı kopyalar.  ADF 'nin çok büyük miktarlarda dosya taramasına izin verseniz, ancak yalnızca birkaç dosyayı hedefe kopyalayabiliyorsanız, dosya taramanın de uzun süre sürmesi nedeniyle uzun süre beklemeniz gerekir.   

Adım adım yönergeler için şu öğreticilere bakın: <br/>
- [Azure Blob depolama 'dan Azure Blob depolama 'ya LastModifiedDate göre yeni ve değiştirilmiş dosyaları artımlı olarak kopyalama](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

Şablonlar için aşağıdakilere bakın:
- [Yeni dosyaları LastModifiedDate göre Kopyala](solution-template-copy-new-files-lastmodifieddate.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>Yeni dosyalar yalnızca bölümlenmiş klasör veya dosya adı kullanılarak yükleniyor.
Dosya veya klasör adının (örneğin,/yyyy/mm/dd/File.exe) bir parçası olarak timeslice bilgileriyle bölümlenen dosya veya klasörleri yalnızca yeni dosyaları kopyalayabilirsiniz. Yeni dosyaları artımlı olarak yüklemek için en iyi performansa yaklaşımlar. 

Adım adım yönergeler için şu öğreticilere bakın: <br/>
- [Azure Blob depolama 'dan Azure Blob depolama alanı 'na bölümlenmiş bir klasöre veya dosya adına göre yeni dosyaları artımlı olarak kopyalama](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>Sonraki adımlar
Şu öğreticiye ilerleyin: 

> [!div class="nextstepaction"]
>[Azure SQL Veritabanı’ndaki bir tablodan Azure Blob depolama Alanı’na artımlı olarak veri kopyalama](tutorial-incremental-copy-powershell.md)
