---
title: Azure Data Factory veri denetimi
description: Azure Data Factory veri Wrangling 'e genel bakış
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: f922e7a2755a6e26a0d9f93f2668753e2f4dad5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98738178"
---
# <a name="what-is-data-wrangling"></a>Veri wrangling nedir?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Kuruluşların, her gün büyümeye devam eden karmaşık verilerin doğru analizini sağlamak için veri hazırlama ve denetimi için önemli iş verilerini araştırmasına ihtiyacı vardır. Kuruluşların verileri çeşitli iş süreçlerinde kullanabilmesi ve değerin süresini azaltabilmesi için veri hazırlama gereklidir.

Data Factory, Power Query kullanarak bulut ölçeğinde kod içermeyen veri hazırlığı yapmayı güçler. Data Factory, [Power Query çevrimiçi](/power-query/) ile tümleşir ve bir işlem hattı etkinliği olarak Power Query d işlevlerinin kullanılabilmesini sağlar.

Data Factory, Power Query çevrimiçi karma düzenleyici tarafından oluşturulan d 'yi Azure Data Factory veri akışına çevirerek, bulut ölçek yürütmesi için Spark koduna dönüştürür. Power Query ve veri akışları ile wrangling verileri, özellikle veri mühendisleri veya ' vatandaşlık veri tümleştiricileri ' için faydalıdır.

> [!NOTE]
> Azure Data Factory Power Query etkinliği şu anda genel önizlemede kullanılabilir

## <a name="use-cases"></a>Uygulama alanları

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFkW]

### <a name="fast-interactive-data-exploration-and-preparation"></a>Hızlı etkileşimli veri araştırması ve hazırlığı

Birden çok veri mühendisi ve vatandaşlık veri tümleştiricileri, bulut ölçeğinde veri kümelerini etkileşimli bir şekilde araştırabilir ve hazırlayabilir. Verilerin yükseminde, veri kümelerindeki verilerin çeşitliliğini, çeşitli ve hızına sahip kullanıcılar, veri kümelerini keşfetmeye ve hazırlamaya yönelik etkili bir yönteme ihtiyaç duyar. Örneğin, ' 2017 ' den beri yeni müşteriler için tüm müşteri demoonları bilgilerine sahip bir veri kümesi oluşturmanız gerekebilir. Bilinen bir hedefle eşlememeniz yok. Gölü üzerinde yayımlamadan önce bir gereksinimi karşılamak için araştırırken, wrangling ve veri kümeleri önceden alınıyor. Wrangling, genellikle daha az resmi analiz senaryosu için kullanılır. Önceden yazılmış veri kümeleri, dönüşümler ve makine öğrenimi işlemleri için aşağı akış yapmak üzere kullanılabilir.

### <a name="code-free-agile-data-preparation"></a>Kod ücretsiz çevik veri hazırlama

Vatandaşlık veri tümleştiricileri, verileri arayan ve hazırlama zamanının %60 ' inden fazlasını harcıyordu. İşlem üretkenliğini artırmak için bunu bir kod ücretsiz olarak yapmak arıyorlar. Vatandaşlık veri tümleştiricilerinin, ölçeklenebilir bir şekilde Power Query çevrimiçi gibi bilinen araçları kullanarak verileri zenginleştirmesine ve yayımlamasına olanak tanımak, büyük ölçüde üretkenliğini artırır. Azure Data Factory 'de wrangling, vatandaşlık veri tümleştiricilerinin hataları hızla düzeltmesine, verileri standartlaştırılmasına ve iş kararlarını desteklemek için yüksek kaliteli veriler üretmesine olanak tanımak için tanıdık Power Query çevrimiçi karma düzenleyici 'yi sağlar.

### <a name="data-validation-and-exploration"></a>Veri doğrulama ve araştırma

Herhangi bir şeyi kaldırmak ve hızlı analizler için bir şekle uymak üzere verilerinizi kod ücretsiz olarak görsel olarak tarayın.

## <a name="supported-sources"></a>Desteklenen kaynaklar

| Bağlayıcı | Veri biçimi | Kimlik doğrulaması türü |
| -- | -- | --|
| [Azure Blob Depolama](connector-azure-blob-storage.md) | CSV, Parquet | Hesap anahtarı |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | Hizmet Sorumlusu |
| [Azure Data Lake Storage 2. Nesil](connector-azure-data-lake-storage.md) | CSV, Parquet | Hesap anahtarı, hizmet sorumlusu |
| [Azure SQL Veritabanı](connector-azure-sql-database.md) | - | SQL kimlik doğrulaması |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | SQL kimlik doğrulaması |

## <a name="the-mashup-editor"></a>Karma düzenleyici

Power Query etkinliği oluşturduğunuzda tüm kaynak veri kümeleri veri kümesi sorguları haline gelir ve **Adfresource** klasörüne yerleştirilir. Varsayılan olarak, UserQuery ilk veri kümesi sorgusuna işaret eder. Veri kümesi sorgularının değişiklikleri desteklenmediğinden veya kalıcı hale getirilmesi nedeniyle, tüm dönüşümler UserQuery üzerinde yapılmalıdır. Sorguları yeniden adlandırma, ekleme ve silme işlemi şu anda desteklenmiyor.

![Denetimi](media/wrangling-data-flow/editor.png)

Şu anda tüm Power Query M işlevleri, yazma sırasında kullanılabilir olmasına rağmen veri denetimi için desteklenmez. Power Query etkinliklerinizi oluştururken, bir işlev desteklenmiyorsa aşağıdaki hata iletisiyle karşılaşırsınız:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Desteklenen Dönüşümler hakkında daha fazla bilgi için bkz. [veri denetimi işlevleri](wrangling-functions.md).

## <a name="next-steps"></a>Sonraki adımlar

[Mash Power Query veri bir denetimi oluşturma](wrangling-tutorial.md)hakkında bilgi edinin.
