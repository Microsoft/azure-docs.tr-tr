---
title: Azure Veri Gezgini tarama
description: Bu kılavuzda, Azure Veri Gezgini tarama ayrıntıları açıklanmaktadır.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 7adc7f568fb82692f2c96f610575076e397bd99c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896171"
---
# <a name="register-and-scan-azure-data-explorer"></a>Azure Veri Gezgini kaydetme ve tarama

Bu makalede Azure 'da bir Azure Veri Gezgini hesabının nasıl kaydedileceği ve taramanın nasıl ayarlanacağı özetlenmektedir.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Azure Veri Gezgini meta verileri ve şemayı yakalamak için tam ve artımlı taramaları destekler. Taramalar Ayrıca verileri sistem ve özel sınıflandırma kurallarına göre otomatik olarak sınıflandırır.

## <a name="prerequisites"></a>Önkoşullar

- Veri kaynaklarını kaydetmeden önce bir Azure purview hesabı oluşturun. Bir purview hesabı oluşturma hakkında daha fazla bilgi için bkz. [hızlı başlangıç: Azure purview hesabı oluşturma](create-catalog-portal.md).
- Azure purview veri kaynağı Yöneticisi olmanız gerekir

## <a name="setting-up-authentication-for-a-scan"></a>Tarama için kimlik doğrulamasını ayarlama

Azure Veri Gezgini için kimlik doğrulaması ayarlamak için yalnızca bir yol vardır:

- Hizmet Sorumlusu

### <a name="service-principal"></a>Hizmet sorumlusu

Taramalar için hizmet sorumlusu kimlik doğrulamasını kullanmak için mevcut bir tane kullanabilir veya yeni bir tane oluşturabilirsiniz. 

> [!Note]
> Yeni bir hizmet sorumlusu oluşturmanız gerekiyorsa lütfen şu adımları izleyin:
> 1. [Azure Portal](https://portal.azure.com)gidin.
> 1. Sol taraftaki menüden **Azure Active Directory** ' yi seçin.
> 1. **Uygulama kayıtları**’nı seçin.
> 1. **+ Yeni uygulama kaydı**' nı seçin.
> 1. **Uygulama** için bir ad girin (hizmet asıl adı).
> 1. **Yalnızca bu kuruluş dizininde hesaplar '** ı seçin.
> 1. Yeniden yönlendirme URI 'SI için **Web** ' i seçin ve istediğiniz URL 'yi girin; gerçek veya iş olması gerekmez.
> 1. Ardından **Kaydet**’i seçin.

Hizmet sorumlusunun uygulama KIMLIĞI ve gizli anahtarı almak için gereklidir:

1. [Azure Portal](https://portal.azure.com) hizmet sorumlusuna gidin
1. **Uygulama (istemci) kimliğinin** değerlerini, **Sertifikalar & Gizliliklerden** **genel bakış** ve **istemci gizliliğine** kopyalayın.
1. Anahtar kasanıza gidin
1. **Gizli dizileri > ayarları** seçin
1. **+ Oluştur/Içeri aktar** ' ı seçin ve seçtiğiniz **adı** ve **değeri** hizmet sorumlınızdan **istemci gizli anahtarı** olarak girin
1. Tamamlanacak **Oluştur** ' u seçin
1. Anahtar Kasanızda purview 'a bağlı değilse, [Yeni bir Anahtar Kasası bağlantısı oluşturmanız](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) gerekir
1. Son olarak, taramanızı ayarlamak için hizmet sorumlusunu kullanarak [Yeni bir kimlik bilgisi oluşturun](manage-credentials.md#create-a-new-credential)

#### <a name="granting-the-service-principal-access-to-your-azure-data-explorer-instance"></a>Azure Data Explorer örneğinize hizmet sorumlusu erişimi verme

1. Azure portalına gidin. Ardından Azure Veri Gezgini örneğinize gidin.

1. Aşağıdaki ekran görüntüsünde gösterildiği gibi, **izin** sekmesindeki **AllDatabasesViewer** rolüne hizmet sorumlusunu ekleyin.

    :::image type="content" source="./media/register-scan-azure-data-explorer/permissions-auth.png" alt-text="İzinlerde hizmet sorumlusu eklemek için ekran görüntüsü" border="true":::

## <a name="register-an-azure-data-explorer-account"></a>Azure Veri Gezgini hesabını kaydetme

Yeni bir Azure Veri Gezgini (kusto) hesabını veri kataloğunuza kaydetmek için aşağıdakileri yapın:

1. Purview hesabınıza gidin
1. Sol gezinti bölmesinde **kaynakları** seçin
1. **Kaydol** ' u seçin
1. **Kayıt kaynakları** üzerinde **Azure Veri Gezgini** seçin
1. **Devam**'ı seçin

:::image type="content" source="media/register-scan-azure-data-explorer/register-new-data-source.png" alt-text="Yeni veri kaynağını Kaydet" border="true":::

**Kaynakları Kaydet (Azure Veri Gezgini (kusto))** ekranında şunları yapın:

1. Veri kaynağının katalogda listelenecek bir **ad** girin.
1. İstediğiniz depolama hesabınıza nasıl işaret etmek istediğinizi seçin:
   1. **Azure aboneliği ' nden** seçim yapın, **Azure aboneliği** açılan kutusundan ilgili aboneliği ve **küme** açılan kutusundan ilgili kümeyi seçin.
   1. İsterseniz **El Ile gir** ' i seçebilir ve bir hizmet uç noktası (URL) girebilirsiniz.
1. Veri kaynağını kaydetmek için **son** ' a gidin.

:::image type="content" source="media/register-scan-azure-data-explorer/register-sources.png" alt-text="kaynakları kaydetme seçenekleri" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans-azure-data-explorer.md)]

## <a name="next-steps"></a>Sonraki adımlar

- [Azure purview veri kataloğuna gözatın](how-to-browse-catalog.md)
- [Azure purview Veri Kataloğu 'Nda arama](how-to-search-catalog.md)
