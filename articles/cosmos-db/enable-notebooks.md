---
title: Azure Cosmos DB hesabında not defterlerini etkinleştirin
description: Azure Cosmos DB yerleşik Not defterleri, portaldaki verilerinizi analiz etmenize ve görselleştirmenize olanak tanır. Bu makalede, Cosmos hesapları için bu özelliğin nasıl etkinleştirileceği açıklanır.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: 22bad3b31b8cbe54900b96bd4b327f18916e81a3
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677159"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts"></a>Azure Cosmos DB hesapları için not defterlerini etkinleştir

> [!IMPORTANT]
> Azure Cosmos DB için yerleşik Not defterleri Şu anda şu Azure bölgelerinde sunulmaktadır: Avustralya Doğu, Doğu ABD, Doğu ABD 2, Kuzey Avrupa, Orta Güney ABD, Güneydoğu Asya, UK Güney, Batı Avrupa ve Batı ABD 2. Not defterlerini kullanmak için, bu bölgelerden birindeki mevcut bir hesapta not defterleri [ile yeni bir hesap oluşturun](#enable-notebooks-in-a-new-cosmos-account) veya Not [defterlerini etkinleştirin](#enable-notebooks-in-an-existing-cosmos-account) .

Azure Cosmos DB 'daki yerleşik jupi Not defterleri, Azure portal verilerinizi analiz etmenize ve görselleştirmenize olanak tanır. Bu makalede, Azure Cosmos DB hesabınız için bu özelliğin nasıl etkinleştirileceği açıklanır.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Yeni bir Cosmos hesabındaki not defterlerini etkinleştir
1. [Azure portal](https://portal.azure.com/) oturum açın.
1. **Kaynak oluştur** > **Veritabanları** > **Azure Cosmos DB** seçeneğini belirleyin.
1. **Azure Cosmos DB hesabı oluştur** sayfasında, **Not defterleri**' ni seçin. 
 
    ![Azure Cosmos DB dikey pencere Oluştur bölümünde Not defterleri seçeneğini belirleyin](media/enable-notebooks/create-new-account-with-notebooks.png)
1. **İncele ve oluştur**’u seçin. **Ağ** ve **Etiketler** seçeneğini atlayabilirsiniz. 
1. Hesap ayarlarını gözden geçirin ve ardından **Oluştur**' u seçin. Hesabın oluşturulması birkaç dakika sürer. Portalın, **dağıtımınızın**tamamlanmasını bekleyin. 

    ![Azure portalındaki Bildirimler bölmesi](media/enable-notebooks/create-new-account-with-notebooks-complete.png)
1. Azure Cosmos DB hesap sayfasına gitmek için **Kaynağa Git** ' i seçin. 

    ![Azure Cosmos DB hesabı sayfası](../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png)

1. **Veri Gezgini** bölmesine gidin. Artık Not defteri çalışma alanınızı görmeniz gerekir.

    ![Yeni Azure Cosmos DB Not defteri çalışma alanı](media/enable-notebooks/new-notebooks-workspace.png)

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Mevcut Cosmos hesabındaki not defterlerini etkinleştir
Ayrıca, mevcut hesaplarda not defterlerini etkinleştirebilirsiniz. Bu adımın hesap başına yalnızca bir kez yapılması gerekir.

1. Cosmos hesabınızda **Veri Gezgini** bölmesine gidin.
1. **Not defterlerini etkinleştir**' i seçin.

    ![Veri Gezgini yeni bir not defteri çalışma alanı oluştur](media/enable-notebooks/enable-notebooks-workspace.png)
1. Bu, yeni bir not defteri çalışma alanı oluşturmanızı ister. **Kurulumu Tamam** ' ı seçin.
1. Hesabınız artık not defterlerini kullanacak şekilde etkinleştirilmiştir!

## <a name="create-and-run-your-first-notebook"></a>İlk not defterinizi oluşturma ve çalıştırma

Not defterlerini kullanacağınızı doğrulamak için örnek Not defterleri altındaki not defterlerinden birini seçin. Bu işlem, Not defterinin bir kopyasını çalışma alanınıza kaydeder ve açar.

Bu örnekte, **gettingstarted. ipynb**' yi kullanacağız. 

![GettingStarted. ipynb Not defterini görüntüle](media/enable-notebooks/select-getting-started-notebook.png)

Not defterini çalıştırmak için:
1. Python kodunu içeren ilk kod hücresini seçin. 
1. Hücreyi çalıştırmak için **Çalıştır** ' ı seçin. Hücreyi çalıştırmak için **SHIFT + enter** ' i de kullanabilirsiniz.
1. Oluşturulan veritabanını ve kapsayıcıyı görmek için kaynak bölmesini yenileyin.

    ![Başlarken Not defterini çalıştır](media/enable-notebooks/run-first-notebook-cell.png)

Yeni bir not defteri oluşturmak için **Yeni Not** defteri ' ni **seçip not defterim menüsünden** **dosya karşıya yükle** ' yi seçerek var olan bir not defteri (. ipynb) dosyasını karşıya yükleyebilirsiniz. 

![Yeni bir not defteri oluşturun veya karşıya yükleyin](media/enable-notebooks/create-or-upload-new-notebook.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Jupyıter Not defterlerinin Azure Cosmos DB](cosmosdb-jupyter-notebooks.md) avantajları hakkında bilgi edinin
