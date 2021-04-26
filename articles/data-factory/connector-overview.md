---
title: Azure Data Factory bağlayıcıya genel bakış
description: Data Factory içinde desteklenen bağlayıcıları öğrenin.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: jingwang
ms.openlocfilehash: cfd3376174ec0f7789389988245f7377b9896a00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015942"
---
# <a name="azure-data-factory-connector-overview"></a>Azure Data Factory bağlayıcıya genel bakış

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory, kopyalama, veri akışı, arama, meta veri al ve silme etkinliklerini kullanarak aşağıdaki veri depolarını ve biçimlerini destekler. Desteklenen özellikleri ve Ayrıntılar ' daki ilgili konfigürasyonları öğrenmek için her bir veri deposuna tıklayın.

## <a name="supported-data-stores"></a>Desteklenen veri depoları

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="integrate-with-more-data-stores"></a>Daha fazla veri deposu ile tümleştirin

Azure Data Factory, yukarıda belirtilen listeden daha geniş veri depoları kümesine ulaşabilir. Azure Data Factory yerleşik bağlayıcı listesinde olmayan bir veri deposuna/kaynağından veri taşımanız gerekiyorsa, bazı Genişletilebilir seçenekler aşağıda verilmiştir:
- Veritabanı ve veri ambarı için genellikle [Genel ODBC bağlayıcısını](connector-odbc.md)kullanabileceğiniz karşılık gelen bir ODBC sürücüsünü bulabilirsiniz.
- SaaS uygulamaları için:
    - Yeniden kullanılabilir API 'Ler sağlıyorsa, [genel Rest bağlayıcısını](connector-rest.md)kullanabilirsiniz.
    - OData akışı varsa, [genel OData bağlayıcısını](connector-odata.md)kullanabilirsiniz.
    - SOAP API 'Leri sağlıyorsa, [Genel http bağlayıcısını](connector-http.md)kullanabilirsiniz.
    - ODBC sürücüsü varsa, [Genel ODBC Bağlayıcısı](connector-odbc.md)' nı kullanabilirsiniz.
- Diğer bir deyişle, ADF tarafından desteklenen herhangi bir veri deposu (örn. Azure blob/dosya/FTP/SFTP/vb.) için veri yükleyip yükleyemediğinizden emin olun ve daha sonra ADF 'yi buradan çekmeye izin verin. [Azure işlevi](control-flow-azure-function-activity.md), [özel etkinlik](transform-data-using-dotnet-custom-activity.md), [databricks](transform-data-databricks-notebook.md) / [HDInsight](transform-data-using-hadoop-hive.md), [Web etkinliği](control-flow-web-activity.md)vb. aracılığıyla özel veri yükleme mekanizmalarının çağrılmasını sağlayabilirsiniz.

## <a name="supported-file-formats"></a>Desteklenen dosya biçimleri

Azure Data Factory aşağıdaki dosya biçimlerini destekler. Biçim tabanlı ayarlar için her makaleye başvurun.

- [Avro biçimi](format-avro.md)
- [İkili biçim](format-binary.md)
- [Common Data Model biçimi](format-common-data-model.md)
- [Sınırlandırılmış metin biçimi](format-delimited-text.md)
- [Delta biçimi](format-delta.md)
- [Excel biçimi](format-excel.md)
- [JSON biçimi](format-json.md)
- [ORC biçimi](format-orc.md)
- [Parquet biçimi](format-parquet.md)
- [XML biçimi](format-xml.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Kopyalama etkinliği](copy-activity-overview.md)
- [Eşleme Veri Akışı](concepts-data-flow-overview.md)
- [Arama Etkinliği](control-flow-lookup-activity.md)
- [Meta Veri Alma Etkinliği](control-flow-get-metadata-activity.md)
- [Etkinliği sil](delete-activity.md)
