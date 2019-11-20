---
title: Azure Cosmos DB 'de geliştirme ve test için iyileştirme
description: Bu makalede, Azure Cosmos DB hizmetinin ücretsiz olarak geliştirilmesi ve test edilmesi için birden çok seçenek sunan açıklanmaktadır.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 992d165d323aab79bb7b5475aa396d4432691530
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754892"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Azure Cosmos DB geliştirme ve test maliyetini iyileştirin

Bu makalede, ücretsiz olarak geliştirme ve test için Azure Cosmos DB kullanmanın farklı seçenekleri açıklanmaktadır.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB öykünücüsü (yerel olarak indirilebilir sürüm)

[Azure Cosmos DB öykünücüsü](local-emulator.md) , Azure Cosmos DB bulut hizmetini taklit eden, yerel olarak indirilebilir bir sürümdür. Ağ bağlantınız olmasa ve herhangi bir ücret ödemeden bile Azure Cosmos DB API 'Leri kullanan kodu yazabilir ve test edebilirsiniz. Azure Cosmos DB öykünücü, bulut hizmeti için yüksek uygunluğa sahip geliştirme amacıyla yerel bir ortam sağlar. Azure aboneliği oluşturmadan uygulamanızı yerel olarak geliştirebilir ve test edebilirsiniz. Uygulamanızı buluta dağıtmaya hazırsanız, bağlantı dizesini buluttaki Azure Cosmos DB uç noktasına bağlanacak şekilde güncelleştirin, başka bir değişiklik yapmanız gerekmez. Ayrıca, testleri çalıştırmak için Azure DevOps 'da [Azure Cosmos DB öykünücü derleme göreviyle BIR CI/CD işlem hattı da ayarlayabilirsiniz](tutorial-setup-ci-cd.md) . [Azure Cosmos DB öykünücü](local-emulator.md) makalesini ziyaret ederek çalışmaya başlayın.

## <a name="try-azure-cosmos-db-for-free"></a>Azure Cosmos DB’yi ücretsiz deneyin

[Ücretsiz Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) , veritabanı ve koleksiyonlar oluşturmanıza ve Bulutta Azure Cosmos DB denemeye olanak sağlayan ücretsiz bir ücret deneyimidir. Azure 'a kaydolmanız veya herhangi bir maliyet ödemeniz gerekmez. Deneme Azure Cosmos DB hesapları, şu anda 30 gün sınırlı bir süre için kullanılabilir. Bunları dilediğiniz zaman yenileyebilirsiniz. Azure Cosmos DB hesapları, hızlı başlangıçlara veya öğreticilerle bir uygulamayı değerlendirmeyi Azure Cosmos DB, derlemeyi ve test yapmayı kolaylaştırır. Herhangi bir ücret ödemeden bir demo oluşturabilir veya birim testi yapabilirsiniz. Ücretsiz hesaplar için TRY Azure Cosmos DB kullanarak, anahtar genel dağıtım, SLA 'Lar ve tutarlılık modelleri de dahil olmak üzere Azure Cosmos DB Premium yeteneklerini ücretsiz olarak değerlendirebilirsiniz. En fazla 25 Azure Cosmos kapsayıcıyla ve 10.000 RU/sn işleme sahip bir veritabanı oluşturabilirsiniz. Örnek uygulamayı bir Azure hesabına abone olmadan veya kredi kartınızı kullanarak çalıştırabilirsiniz. Deneme Azure Cosmos DB ücretsiz olarak, çok bölgeli bir Azure Cosmos hesabı oluşturabilir ve bir uygulamayı yalnızca birkaç dakikada çalıştırabilirsiniz. Başlamak için bkz. [ücretsiz sayfa için Azure Cosmos DB deneyin](https://azure.microsoft.com/try/cosmosdb/) .

## <a name="azure-free-account"></a>Azure Ücretsiz Hesabı

Azure Cosmos DB, Azure kredileri ve kaynakları belirli bir süre boyunca ücretsiz olarak sunan [Azure Ücretsiz hesabına](https://azure.microsoft.com/free)dahildir. Özellikle Azure Cosmos DB için bu ücretsiz hesap, tüm yıl boyunca 5 GB depolama ve 400 ru iş hacmi sunmaktadır. Bu deneyim, herhangi bir geliştiricinin Azure Cosmos DB özelliklerini kolayca test etmesini veya diğer Azure hizmetleriyle sıfır maliyetle tümleştirmesini sağlar. Azure Ücretsiz hesabıyla, ilk 30 gün boyunca harcayabileceğiniz bir $200 kredisi alırsınız. Yükseltmeyi seçinceye kadar Hizmetleri kullanmaya başlasanız bile, ücretlendirilmezsiniz. Başlamak için [Azure Ücretsiz hesap](https://azure.microsoft.com/free) sayfasını ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerde öykünücü veya ücretsiz Azure Cosmos DB hesaplarını kullanmaya başlamanızı sağlayabilirsiniz:

* [Geliştirme ve test Için iyileştirme](optimize-dev-test.md) hakkında daha fazla bilgi edinin
* [Azure Cosmos DB Faturanızı Anlama](understand-your-bill.md) hakkında daha fazla bilgi edinin
* [Verimlilik maliyetini iyileştirme](optimize-cost-throughput.md) hakkında daha fazla bilgi edinin
* [Depolama maliyetini iyileştirme](optimize-cost-storage.md) hakkında daha fazla bilgi edinin
* [Okuma ve yazma maliyetlerini iyileştirme](optimize-cost-reads-writes.md) hakkında daha fazla bilgi edinin
* [Sorguların maliyetini En Iyi duruma getirme](optimize-cost-queries.md) hakkında daha fazla bilgi edinin
* [Çok bölgeli Azure Cosmos hesaplarının maliyetini En Iyi duruma getirme](optimize-cost-regions.md) hakkında daha fazla bilgi edinin

