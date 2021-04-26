---
title: Azure Cosmos DB hesabındaki not defterlerini etkinleştir (Önizleme)
description: Azure Cosmos DB yerleşik Not defterleri, portaldaki verilerinizi analiz etmenize ve görselleştirmenize olanak tanır. Bu makalede, Cosmos hesapları için bu özelliğin nasıl etkinleştirileceği açıklanır.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/22/2021
ms.author: dech
ms.custom: references_regions
ms.openlocfilehash: 02e8ad5f2b5326f947ba0bca6456ce9d9d3c27d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101692785"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Azure Cosmos DB hesapları için not defterlerini etkinleştirme (Önizleme)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB için yerleşik Not defterleri Şu anda [29 bölgede](#supported-regions)kullanılabilir. Not defterlerini kullanmak için [Yeni bir Cosmos hesabı oluşturun](#create-a-new-cosmos-account) veya bu bölgelerden birindeki [mevcut bir hesapta not defterlerini etkinleştirin](#enable-notebooks-in-an-existing-cosmos-account) . 

Azure Cosmos DB 'daki yerleşik jupi Not defterleri, Azure portal verilerinizi analiz etmenize ve görselleştirmenize olanak tanır. Bu makale, Azure Cosmos DB hesabınız için bu özelliği etkinleştirmeyi açıklar.

## <a name="create-a-new-cosmos-account"></a>Yeni bir Cosmos hesabı oluşturun
10 Şubat 2021 ' den itibaren, [desteklenen bölgeden](#supported-regions) birinde oluşturulan yeni Azure Cosmos hesaplarının otomatik olarak not defterleri etkin olur. Not defterlerini etkinleştirmek için ek yapılandırma gerekmez. Yeni bir hesap oluşturmak için aşağıdaki yönergeleri kullanın:
1. [Azure portal](https://portal.azure.com/) oturum açın.
1. Azure Cosmos DB **kaynak veritabanları oluştur**' u seçin  >    >  .
1. Hesap için temel ayarları girin.

   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail-2.png" alt-text="Azure Cosmos DB için yeni hesap sayfası":::

1. **Gözden geçir ve oluştur**’u seçin. **Ağ** ve **Etiketler** seçeneğini atlayabilirsiniz. 
1. Hesap ayarlarını gözden geçirip **Oluştur** seçeneğini belirleyin. Hesabın oluşturulması birkaç dakika sürer. Portal sayfasında **Dağıtımınız tamamlandı** iletisinin görüntülenmesini bekleyin.

   :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks-complete.png" alt-text="Azure portaldaki Bildirimler bölmesi":::

1. Azure Cosmos DB hesabı sayfasına gitmek için **Kaynağa git** seçeneğini belirleyin.

   :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png" alt-text="Azure Cosmos DB hesabı sayfası":::

1. **Veri Gezgini** bölmesine gidin. Artık Not defteri çalışma alanınızı görmeniz gerekir.

    :::image type="content" source="media/enable-notebooks/new-notebooks-workspace.png" alt-text="Yeni Azure Cosmos DB Not defteri çalışma alanı":::

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Mevcut Cosmos hesabındaki not defterlerini etkinleştir

Ayrıca, mevcut hesaplarda not defterlerini etkinleştirebilirsiniz. Bu adımın hesap başına yalnızca bir kez yapılması gerekir.

1. Cosmos hesabınızda **Veri Gezgini** bölmesine gidin.
1. **Not defterlerini etkinleştir**' i seçin.

    :::image type="content" source="media/enable-notebooks/enable-notebooks-workspace.png" alt-text="Veri Gezgini yeni bir not defteri çalışma alanı oluştur":::

1. Bu, yeni bir not defteri çalışma alanı oluşturmanızı ister. **Kurulumu Tamam** ' ı seçin.
1. Hesabınız artık not defterlerini kullanacak şekilde etkinleştirilmiştir!

## <a name="create-and-run-your-first-notebook"></a>İlk not defterinizi oluşturma ve çalıştırma

Not defterlerini kullanacağınızı doğrulamak için örnek Not defterleri altındaki not defterlerinden birini seçin. Bu işlem, Not defterinin bir kopyasını çalışma alanınıza kaydeder ve açar.

Bu örnekte, **gettingstarted. ipynb**' yi kullanacağız.

:::image type="content" source="media/enable-notebooks/select-getting-started-notebook.png" alt-text="GettingStarted. ipynb Not defterini görüntüle":::

Not defterini çalıştırmak için:
1. Python kodunu içeren ilk kod hücresini seçin.
1. Hücreyi çalıştırmak için **Çalıştır** ' ı seçin. Hücreyi çalıştırmak için **SHIFT + enter** ' i de kullanabilirsiniz.
1. Oluşturulan veritabanını ve kapsayıcıyı görmek için kaynak bölmesini yenileyin.

    :::image type="content" source="media/enable-notebooks/run-first-notebook-cell.png" alt-text="Başlarken Not defterini çalıştır":::

Yeni bir not defteri oluşturmak için **Yeni Not** defteri ' ni **seçip not defterim menüsünden** **dosya karşıya yükle** ' yi seçerek var olan bir not defteri (. ipynb) dosyasını karşıya yükleyebilirsiniz. 

:::image type="content" source="media/enable-notebooks/create-or-upload-new-notebook.png" alt-text="Yeni bir not defteri oluşturun veya karşıya yükleyin":::

## <a name="supported-regions"></a>Desteklenen bölgeler
Azure Cosmos DB için yerleşik not defterleri şu anda 29 Azure bölgesinde kullanılabilir. Bu bölgelerde oluşturulan yeni Azure Cosmos hesaplarında, Not defterleri otomatik olarak etkinleştirilir. Not defterleri hesabınızla ücretsizdir. 

- Orta Avustralya
- Orta Avustralya 2
- Doğu Avustralya
- Güneydoğu Avustralya
- Güney Brezilya
- Orta Kanada
- Doğu Kanada
- Orta Hindistan
- Central US
- Doğu ABD
- Doğu ABD 2
- Orta Fransa
- Güney Fransa
- Almanya Kuzey
- Almanya Orta Batı
- Batı Japonya
- Güney Kore - Güney
- Orta Kuzey ABD
- Kuzey Avrupa
- Orta Güney ABD
- Güneydoğu Asya
- İsviçre Kuzey
- BAE Orta
- Güney Birleşik Krallık
- Batı Birleşik Krallık
- Orta Batı ABD
- West Europe
- Batı Hindistan
- Batı ABD 2

## <a name="next-steps"></a>Sonraki adımlar

* [Jupyıter Not defterlerinin Azure Cosmos DB](cosmosdb-jupyter-notebooks.md) avantajları hakkında bilgi edinin
* [Not defteri örnekleri galerisini keşfet](https://cosmos.azure.com/gallery.html)
* [Not defterlerini Azure Cosmos DB Not defteri galerisine yayımlama](publish-notebook-gallery.md)
* [Python Not defteri özelliklerini ve komutlarını kullanma](use-python-notebook-features-and-commands.md)
* [C# Not defteri özelliklerini ve komutlarını kullanma](use-csharp-notebook-features-and-commands.md)
* [GitHub deposundan not defterlerini içeri aktarma](import-github-notebooks.md)
