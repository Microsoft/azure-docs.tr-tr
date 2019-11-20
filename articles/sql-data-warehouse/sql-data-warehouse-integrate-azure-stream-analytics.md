---
title: Azure Stream Analytics kullan
description: Çözümleri geliştirmek için Azure SQL veri ambarı ile Azure Stream Analytics kullanmaya yönelik ipuçları.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 03/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 63803f3ac477e48d8d1c14a72e2ee9b9d4860047
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685726"
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>SQL veri ambarı ile Azure Stream Analytics kullanma
Azure Stream Analytics, bulutta akış verileri üzerinde düşük gecikmeli, yüksek oranda kullanılabilir ve ölçeklenebilir karmaşık olay işleme sağlayan, tam olarak yönetilen bir hizmettir. [Azure Stream Analytics tanıtım][Introduction to Azure Stream Analytics]bilgilerini okuyarak temelleri öğrenebilirsiniz. Daha sonra, Azure Stream Analytics öğreticisini [kullanmaya başlarken][Get started using Azure Stream Analytics] ' i izleyerek Stream Analytics ile uçtan uca bir çözüm oluşturmayı öğrenebilirsiniz.

Bu makalede, Azure SQL veri ambarı veritabanınızı Stream Analytics işleriniz için çıkış havuzu olarak kullanmayı öğreneceksiniz.

## <a name="prerequisites"></a>Ön koşullar
İlk olarak, Azure Stream Analytics öğreticisini [kullanmaya başlarken][Get started using Azure Stream Analytics] ' de aşağıdaki adımları gerçekleştirin.  

1. Olay Hub 'ı girişi oluşturma
2. Olay Oluşturucu uygulamasını yapılandırma ve başlatma
3. Stream Analytics işi sağlama
4. İş girişi ve sorgu belirtme

Ardından, bir Azure SQL veri ambarı veritabanı oluşturun

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>İş çıkışını belirtin: Azure SQL veri ambarı veritabanı
### <a name="step-1"></a>1\. Adım
Stream Analytics işiniz sayfanın üst kısmından **Çıkış** ' a tıklayın ve ardından **Ekle**' ye tıklayın.

### <a name="step-2"></a>2\. Adım
SQL veritabanı ' nı seçin.

### <a name="step-3"></a>Adım 3
Sonraki sayfada aşağıdaki değerleri girin:

* *Çıkış diğer adı*: Bu iş çıktısı için kolay bir ad girin.
* *Abonelik*:
  * SQL veri ambarı veritabanınız Stream Analytics işle aynı abonelikte yer alıyorsa, geçerli abonelikteki SQL veritabanı kullan ' ı seçin.
  * Veritabanınız farklı bir abonelikte yer alıyorsa, başka bir abonelikteki SQL veritabanını kullan ' ı seçin.
* *Veritabanı*: hedef veritabanının adını belirtin.
* *Sunucu adı*: az önce belirttiğiniz veritabanının sunucu adını belirtin. Bunu bulmak için Azure portal kullanabilirsiniz.

![][server-name]

* *Kullanıcı adı*: veritabanı için yazma izinlerine sahip olan bir hesabın kullanıcı adını belirtin.
* *Parola*: belirtilen kullanıcı hesabı için parola belirtin.
* *Tablo*: veritabanındaki hedef tablonun adını belirtin.

![][add-database]

### <a name="step-4"></a>4\. Adım
Bu iş çıktısını eklemek ve Stream Analytics veritabanına başarıyla bağlanabildiğini doğrulamak için onay düğmesine tıklayın.

Veritabanı bağlantısı başarılı olduğunda portalda bir bildirim görürsünüz. Veritabanına bağlantıyı test etmek için test ' e tıklayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Tümleştirmeye genel bakış için bkz. [SQL veri ambarı tümleştirmesine genel bakış][SQL Data Warehouse integration overview].

Geliştirme ile ilgili daha fazla ipucu için bkz. [SQL Data Warehouse geliştirmeye genel bakış][SQL Data Warehouse development overview].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduction to Azure Stream Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Get started using Azure Stream Analytics]: ../stream-analytics/stream-analytics-real-time-fraud-detection.md
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: https://azure.microsoft.com/documentation/services/stream-analytics/
