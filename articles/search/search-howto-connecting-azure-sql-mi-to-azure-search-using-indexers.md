---
title: Arama dizini oluşturma için Azure SQL yönetilen örnek bağlantısı
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'deki bir dizin oluşturucudan SQL yönetilen örneklere bağlantılara izin vermek için ortak uç noktayı etkinleştirin.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 16daf4a79252134703715ccd88f0b10dda7f4fa6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792164"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Azure Bilişsel Arama Dizin oluşturucudan SQL yönetilen örneği 'ne bağlantı yapılandırma

[Dizin oluşturucular kullanarak Azure SQL veritabanı 'Nı azure bilişsel arama bağlama](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)bölümünde belirtildiği gibi, **SQL yönetilen örneklere** karşı Dizin oluşturucular oluşturmak, Azure bilişsel arama tarafından genel uç nokta üzerinden desteklenir.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Ortak uç nokta ile Azure SQL yönetilen örneği oluşturma
**Ortak uç noktayı etkinleştir** seçeneği belirlenmiş olarak bir SQL yönetilen örneği oluşturun.

   ![Ortak uç noktayı etkinleştir](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Ortak uç noktayı etkinleştir")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Azure SQL yönetilen örneği genel uç noktasını etkinleştir
Ayrıca, genel uç noktasını **güvenlik** > **sanal ağ** altında bulunan mevcut bir SQL yönetilen örneği üzerinde de **etkinleştirebilirsiniz > **  >  **.**

   ![Ortak uç noktayı etkinleştir](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Ortak uç noktayı etkinleştir")

## <a name="verify-nsg-rules"></a>NSG kurallarını doğrulama
Ağ güvenlik grubunun Azure hizmetlerinden gelen bağlantılara izin veren doğru **gelen güvenlik kurallarına** sahip olup olmadığını denetleyin.

   ![NSG gelen güvenlik kuralı](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG gelen güvenlik kuralı")

## <a name="get-public-endpoint-connection-string"></a>Genel uç nokta bağlantı dizesi al
**Genel uç nokta** için bağlantı dizesini kullandığınızdan emin olun (bağlantı noktası 1433 değil bağlantı noktası 3342).

   ![Genel uç nokta bağlantı dizesi](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Genel uç nokta bağlantı dizesi")

## <a name="next-steps"></a>Sonraki adımlar
Yapılandırma sayesinde, artık Portal veya REST API kullanarak bir Azure Bilişsel Arama Dizin Oluşturucu için veri kaynağı olarak bir SQL yönetilen örneği belirtebilirsiniz. Daha fazla bilgi için bkz. [Dizin oluşturucular kullanarak Azure SQL veritabanı 'Nı azure bilişsel arama bağlama](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) .
