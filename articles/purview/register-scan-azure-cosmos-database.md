---
title: Azure Cosmos veritabanını tarama (SQL API)
description: Bu kılavuzda, Azure Cosmos veritabanını (SQL API) tarama ayrıntıları açıklanmaktadır.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 1aaeed1973ebd15af312b722ab61938aa4271947
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97696244"
---
# <a name="register-and-scan-azure-cosmos-database-sql-api"></a>Azure Cosmos veritabanını kaydetme ve tarama (SQL API)

Bu makalede, Azure 'da bir Azure Cosmos veritabanı (SQL API) hesabının nasıl kaydedileceği ve taramanın nasıl ayarlanacağı özetlenmektedir.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Azure Cosmos veritabanı (SQL API), meta verileri ve şemayı yakalamak için tam ve artımlı taramaları destekler. Taramalar Ayrıca verileri sistem ve özel sınıflandırma kurallarına göre otomatik olarak sınıflandırır.

## <a name="prerequisites"></a>Önkoşullar

- Veri kaynaklarını kaydetmeden önce bir Azure purview hesabı oluşturun. Bir purview hesabı oluşturma hakkında daha fazla bilgi için bkz. [hızlı başlangıç: Azure purview hesabı oluşturma](create-catalog-portal.md).
- Azure purview veri kaynağı Yöneticisi olmanız gerekir

## <a name="setting-up-authentication-for-a-scan"></a>Tarama için kimlik doğrulamasını ayarlama

Azure Cosmos veritabanı (SQL API) için kimlik doğrulaması ayarlamak için yalnızca bir yol vardır:

- Hesap anahtarı
 
### <a name="account-key"></a>Hesap anahtarı

Kimlik doğrulama yöntemi seçili olduğunda **, anahtar** kasasında erişim anahtarınızı almanız ve depolamanız gerekir:

1. Azure portal Cosmos DB hesabınıza gidin 
1. **Ayar**  >  **anahtarlarını** seçin 
1. *Anahtarınızı* kopyalayın ve sonraki adımlar için bir yere kaydedin
1. Anahtar kasanıza gidin
1. **Gizli dizileri > ayarları** seçin
1. **+ Oluştur/Içeri aktar** ' ı seçin ve depolama hesabınızdan *anahtar* olarak **ad** ve **değer** girin
1. Tamamlanacak **Oluştur** ' u seçin
1. Anahtar Kasanızda purview 'a bağlı değilse, [Yeni bir Anahtar Kasası bağlantısı oluşturmanız](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) gerekir
1. Son olarak, taramayı kurmak için anahtarı kullanarak [Yeni bir kimlik bilgisi oluşturun](manage-credentials.md#create-a-new-credential)

## <a name="register-an-azure-cosmos-database-sql-api-account"></a>Azure Cosmos veritabanı (SQL API) hesabı kaydetme

Veri kataloğunuza yeni bir Azure Cosmos veritabanı (SQL API) hesabı kaydetmek için aşağıdakileri yapın:

1. Purview hesabınıza gidin
1. Sol gezinti bölmesinde **kaynakları** seçin
1. **Kaydol** ' u seçin
1. **Kayıt kaynakları** üzerinde **Azure Cosmos DB (SQL API)** seçeneğini belirleyin.
1. **Devam**'ı seçin

:::image type="content" source="media/register-scan-azure-cosmos-database/register-new-data-source.png" alt-text="Yeni veri kaynağını Kaydet" border="true":::

**Kaynakları Kaydet (Azure Cosmos DB (SQL API))** ekranında, şunları yapın:

1. Veri kaynağının katalogda listelenecek bir **ad** girin.
1. İstediğiniz depolama hesabınıza nasıl işaret etmek istediğinizi seçin:
   1. **Azure aboneliği ' nden** seçim yapın, **Azure aboneliği** açılan kutusundan ilgili aboneliği ve **Cosmos DB hesap adı** açılan kutusundan uygun cosmosdb hesabını seçin.
   1. İsterseniz **El Ile gir** ' i seçebilir ve bir hizmet uç noktası (URL) girebilirsiniz.
1. Veri kaynağını kaydetmek için **son** ' a gidin.

:::image type="content" source="media/register-scan-azure-cosmos-database/register-sources.png" alt-text="kaynakları kaydetme seçenekleri" border="true":::


[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Sonraki adımlar

- [Azure purview veri kataloğuna gözatın](how-to-browse-catalog.md)
- [Azure purview Veri Kataloğu 'Nda arama](how-to-search-catalog.md)
