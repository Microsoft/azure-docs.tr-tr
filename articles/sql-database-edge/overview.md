---
title: Azure SQL veritabanı Edge nedir? | Microsoft Belgeleri
description: Azure SQL veritabanı Edge hakkında bilgi edinin
keywords: SQL veritabanı kenarına giriş, SQL veritabanı Edge nedir, SQL veritabanı kenarına genel bakış
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9cbfc17e7412b4d30f082354996721ee7b5d6d5b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514010"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>Azure SQL veritabanı Edge önizlemesi nedir?

Azure SQL veritabanı Edge önizlemesi, IoT ve IoT Edge dağıtımları için iyileştirilmiş bir ilişkisel veritabanı altyapısıdır. IoT uygulamaları ve çözümleri için yüksek performanslı veri depolama ve işleme katmanı oluşturma olanakları sağlar. Azure SQL veritabanı Edge; JSON, grafik ve zaman serisi verileri gibi ilişkisel ve ilişkisel olmayan, çeşitli modern IoT uygulamaları için doğru seçim yapan, bunları akışa almak, işlemek ve analiz etmek için yetenekler sağlar.

Azure SQL veritabanı Edge, sektör lideri performans, güvenlik ve sorgu işleme olanakları sağlayan [Microsoft SQL Server veritabanı altyapısının](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json)en son sürümlerinde oluşturulmuştur. Azure SQL veritabanı Edge, SQL Server ve Azure SQL veritabanı ile aynı altyapıda oluşturulduğundan, uygulama ve çözümlerin geliştirilmesini daha kolay ve hızlı hale getiren T-SQL programlama yüzeyi alanını sağlar ve aynı zamanda uygulamanın IoT Edge cihazları, veri merkezleri ve buluttan doğrudan ileri sarma arasında taşınabilirlik.

## <a name="deployment-models"></a>Dağıtım modelleri

Azure SQL veritabanı Edge, Azure Marketi 'nde kullanılabilir ve [Azure IoT Edge](../iot-edge/about-iot-edge.md)için bir modül olarak dağıtılabilir. Daha fazla bilgi için bkz. [Azure SQL veritabanı Edge 'ı dağıtma](deploy-portal.md).<br>

![SQL veritabanı kenarına genel bakış Diyagramı](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>SQL veritabanı kenarının sürümleri

SQL veritabanı Edge üç farklı sürüm veya yazılım planlarıyla kullanılabilir. Bu sürümler aynı özellik kümelerine sahiptir ve yalnızca kullanım hakları ve destekledikleri CPU/bellek miktarı bakımından farklılık gösterir.

   |**Planlama**  |**Açıklama**  |
   |---------|---------|
   |Geliştirici  |  Aşağıda bahsedilen standart SKU ile belirlenen kısıtlama limitleriyle yalnızca geliştirme için bir SKU vardır |
   |Standart   |  Standart plan, en fazla 4 CPU ve SQL veritabanı kenar kapsayıcısı için 32 GB bellek destekler. |
   |Premium    |  Premium SKU, en fazla 8 çekirdek ve SQL veritabanı kenar kapsayıcısı için 64 GB bellek destekler. |

## <a name="pricing-and-availability"></a>Fiyatlandırma ve kullanılabilirlik

Azure SQL veritabanı şu anda önizleme aşamasındadır. Fiyatlandırma ve kullanılabilirlik hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı Edge](https://azure.microsoft.com/services/sql-database-edge/).

> [!IMPORTANT]
> Azure SQL veritabanı Edge ve SQL Server arasındaki özellik farklarını ve farklı Azure SQL veritabanı uç seçenekleri arasındaki farkları anlamak için bkz. [SQL veritabanı Edge veritabanı özellikleri](https://azure.microsoft.com/services/sql-database-edge/).

## <a name="streaming-capabilities"></a>Akış özellikleri  

Azure SQL veritabanı Edge, gerçek zamanlı analiz ve karmaşık olay işleme için yerleşik akış özellikleri sağlar. Akış özelliği, [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) ile aynı yapılar kullanılarak oluşturulmuştur ve [IoT Edge üzerinde Azure Stream Analytics](../stream-analytics/stream-analytics-edge.md)benzer özellikler sağlar.

Azure SQL veritabanı Edge için akış altyapısı, düşük gecikmeli, dayanıklılık, bant genişliği ve uyumluluk açısından verimli bir şekilde kullanılmak üzere tasarlanmıştır.

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Machine Learning ve yapay zeka özellikleri

Azure SQL veritabanı Edge, farklı çerçeveler arasında derin öğrenme ve sinir ağ modellerinin değişimine izin veren ONNX (Open sinir Network Exchange) çalışma zamanının açık biçimini tümleştirerek yerleşik makine öğrenimi ve analiz özellikleri sağlar. ONNX hakkında daha fazla bilgi için [buraya](https://onnx.ai/)bakın. ONNX çalışma zamanı, seçtiğiniz bir dilde veya bir araç içinde modeller geliştirme esnekliği sağlar ve daha sonra SQL veritabanı Edge içinde yürütülmek üzere ONNX biçimine dönüştürülebilirler. Daha fazla bilgi için bkz. [SQL veritabanı Edge 'de ONNX ile Machine Learning ve yapay zeka](onnx-overview.md).

## <a name="working-with-azure-sql-database-edge"></a>Azure SQL veritabanı kenarıyla çalışma

Azure SQL veritabanı Edge, uygulamaları geliştirmeyi ve uygulamayı daha kolay ve üretken hale getirir. Kullanıcılar IoT Edge gereksinimlerine yönelik harika uygulamalar ve çözümler oluşturmak için tüm tanıdık araç ve becerileri kullanabilir. Kullanıcı, aşağıdaki gibi araçları kullanarak SQL veritabanı Edge 'de geliştirme yapabilir:

- [Azure Portal](https://portal.azure.com/) -tüm Azure hizmetlerini yönetmek için Web tabanlı bir uygulama.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) -SQL Server SQL veritabanı 'na HERHANGI bir SQL altyapısını yönetmeye yönelik ücretsiz, indirilebilir bir istemci uygulaması.
- [Visual Studio 'da SQL Server veri araçları](/sql/ssdt/download-sql-server-data-tools-ssdt/) -SQL Server ilişkisel VERITABANLARı, SQL veritabanları, Tümleştirme Hizmetleri paketleri, Analysis Services veri modelleri ve Raporlama Hizmetleri raporları geliştirmeye yönelik ücretsiz, indirilebilir bir istemci uygulaması.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) -Windows, MacOS ve Linux 'ta Microsoft 'un Şirket içi ve bulut veri platformlarını kullanarak veri uzmanlarına yönelik ücretsiz, indirilebilir ve platformlar arası veritabanı aracı.
- [Visual Studio Code](https://code.visualstudio.com/docs) -Windows, MacOS ve Linux için ücretsiz, indirilebilir, açık kaynaklı kod Düzenleyicisi. Microsoft SQL Server, Azure SQL veritabanı ve Azure SQL veri ambarı 'nı sorgulamak için [MSSQL uzantısı](https://aka.ms/mssql-marketplace) dahil olmak üzere uzantıları destekler.


## <a name="next-steps"></a>Sonraki adımlar

- Fiyatlandırma ve kullanılabilirliğe ilişkin ayrıntılar için bkz. [Azure SQL veritabanı Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Aboneliğiniz için Azure SQL veritabanı Edge 'i etkinleştirme isteği.
- Başlamak için aşağıdakilere bakın:
  - [Azure portal aracılığıyla SQL veritabanı ucunu dağıtma](deploy-portal.md)
  - [SQL veritabanı kenarıyla Machine Learning ve yapay zeka](onnx-overview.md)
