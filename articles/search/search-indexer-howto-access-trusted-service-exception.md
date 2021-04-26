---
title: Güvenilen hizmet özel durumu kullanılarak Azure depolama 'ya Dizin Oluşturucu erişimi
titleSuffix: Azure Cognitive Search
description: Azure depolama 'da güvenli şekilde depolanan veriler için Azure Bilişsel Arama Dizin Oluşturucu tarafından veri erişimini etkinleştirin.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: e139c15ef6de00376a4e1a88000d263c3486994b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92101384"
---
# <a name="indexer-access-to-azure-storage-using-the-trusted-service-exception-azure-cognitive-search"></a>Güvenilen hizmet özel durumu kullanılarak Azure depolama 'ya Dizin Oluşturucu erişimi (Azure Bilişsel Arama)

Azure depolama hesaplarındaki verilere erişen bir Azure Bilişsel Arama hizmetindeki Dizin oluşturucular, verilere güvenli bir şekilde erişmek için [Güvenilen hizmet özel durum](../storage/common/storage-network-security.md#exceptions) özelliğinden daha fazla kullanabilir. Bu mekanizma, [IP güvenlik duvarı kuralları kullanarak Dizin Oluşturucu erişimi](search-indexer-howto-access-ip-restricted.md) veren müşterileri, depolama hesaplarındaki verilere erişmek için basit, güvenli ve ücretsiz bir alternatif sağlar.

> [!NOTE]
> Güvenilen bir hizmet özel durumu aracılığıyla depolama hesaplarındaki verilere erişim desteği, Azure Blob depolama ve Azure Data Lake Gen2 depolama ile sınırlıdır. Azure Tablo Depolaması desteklenmiyor.

## <a name="step-1-configure-a-connection-using-a-managed-identity"></a>1. Adım: yönetilen kimlik kullanarak bağlantı yapılandırma

[Yönetilen kimlik kullanarak bir Azure depolama hesabına bağlantı ayarlama](search-howto-managed-identities-storage.md)bölümündeki yönergeleri izleyin. İşiniz bittiğinde, arama hizmetinizi güvenilir bir hizmet olarak Azure Active Directory kaydettiniz ve Azure Storage 'da verilere veya bilgilere erişmek için arama kimliğine özel haklar veren izinler vermiş olursunuz.

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>2. Adım: güvenilen Microsoft hizmetlerinin depolama hesabına erişmesine Izin verin

Azure portal, depolama hesabının **güvenlik duvarları ve sanal ağlar** sekmesine gidin. **Güvenilen Microsoft hizmetlerinin bu depolama hesabına erişmesine Izin ver** seçeneğinin işaretli olduğundan emin olun. Bu seçenek, belirli bir arama hizmeti örneğine, IP güvenlik duvarı kuralları tarafından güvenli hale getirilse bile depolama hesabındaki verilere erişmek için depolama hesabına uygun rol tabanlı erişime (güçlü kimlik doğrulaması) izin verir.

![Güvenilen hizmet özel durumu](media\search-indexer-howto-secure-access\exception.png "Güvenilen hizmet özel durumu")

Hesap, IP güvenlik duvarı kuralları aracılığıyla güvenli hale getirilse bile, Dizin oluşturucular artık depolama hesabındaki verilere erişebilecektir.

## <a name="next-steps"></a>Sonraki adımlar

Azure depolama Dizin oluşturucular hakkında daha fazla bilgi edinin:

- [Azure Blob Dizin Oluşturucu](search-howto-indexing-azure-blob-storage.md)
- [Azure Data Lake Storage 2. Dizin Oluşturucu](search-howto-index-azure-data-lake-storage.md)
- [Azure Tablo Dizin Oluşturucu](search-howto-indexing-azure-tables.md)