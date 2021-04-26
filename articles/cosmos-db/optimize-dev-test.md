---
title: Azure Cosmos DB 'de geliştirme ve test için iyileştirme
description: Bu makalede, Azure Cosmos DB hizmetinin ücretsiz olarak geliştirilmesi ve test edilmesi için birden çok seçenek sunan açıklanmaktadır.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 3ddae808fbb2e3dcfe20909c8b3d0c5a20bb04bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98247531"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Azure Cosmos DB'de geliştirme ve test maliyetlerini iyileştirme
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Bu makalede, geliştirme ve test için Azure Cosmos DB kullanmanın yanı sıra geliştirme veya test hesaplarındaki maliyeti iyileştirmek için kullanabileceğiniz farklı seçenekler açıklanmaktadır.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB öykünücüsü (yerel olarak indirilebilir sürüm)

[Azure Cosmos DB öykünücüsü](local-emulator.md) , Azure Cosmos DB bulut hizmetini taklit eden, yerel olarak indirilebilir bir sürümdür. Ağ bağlantınız olmasa ve herhangi bir ücret ödemeden bile Azure Cosmos DB API 'Leri kullanan kodu yazabilir ve test edebilirsiniz. Azure Cosmos DB öykünücü, bulut hizmeti için yüksek uygunluğa sahip geliştirme amacıyla yerel bir ortam sağlar. Azure aboneliği oluşturmadan uygulamanızı yerel olarak geliştirebilir ve test edebilirsiniz. Uygulamanızı buluta dağıtmaya hazırsanız, bağlantı dizesini buluttaki Azure Cosmos DB uç noktasına bağlanacak şekilde güncelleştirin, başka bir değişiklik yapmanız gerekmez. Ayrıca, testleri çalıştırmak için Azure DevOps 'da [Azure Cosmos DB öykünücü derleme göreviyle BIR CI/CD işlem hattı da ayarlayabilirsiniz](tutorial-setup-ci-cd.md) . [Azure Cosmos DB öykünücü](local-emulator.md) makalesini ziyaret ederek çalışmaya başlayın.

## <a name="azure-cosmos-db-free-tier"></a>Azure Cosmos DB ücretsiz katman

Azure Cosmos DB ücretsiz katman sayesinde çalışmaya başlamak, uygulamalarınızı geliştirmek ve test etmek, hatta küçük üretim iş yüklerini ücretsiz olarak çalıştırmak kolaylaşır. Hesapta ücretsiz katman etkinleştirildiğinde, hesapta ücretsiz olarak ilk 400 RU/sn ve 5 GB depolama alanı alırsınız. Ayrıca, ücretsiz katmana (5 paylaşılan üretilen iş veritabanlarını ücretsiz katman hesabında sınırlayın), veritabanı düzeyinde 400 RU/s paylaşan bir paylaşılan verimlilik veritabanı da oluşturabilirsiniz. Ücretsiz katmanı kullanırken, en az 400 RU/sn aktarım hızı ile paylaşılan bir veritabanı sağlarsanız, bu veritabanındaki tüm kapsayıcılar üretilen işi paylaşabilir. Adanmış işleme sahip paylaşılan aktarım hızı veya kapsayıcılar içeren tüm yeni veritabanları, normal fiyatlandırmadan faturalandırılır.

> [!NOTE]
> Ücretsiz katman yalnızca sağlanan aktarım hızı modunda kullanılabilir.

Ücretsiz katman hesabın kullanım ömrü boyunca süresiz olarak sürer ve sınırsız depolama ve aktarım hızı (RU/s), SLA 'Lar, yüksek kullanılabilirlik, tüm Azure bölgelerinde anahtar genel dağıtım ve daha fazlası dahil olmak üzere normal Azure Cosmos DB hesabının tüm [avantajları ve özellikleriyle](introduction.md#key-benefits) birlikte gelir. Azure aboneliği başına en fazla bir ücretsiz katman hesabınız olabilir ve hesabı oluştururken kabul etmeniz gerekir. Başlamak için, [ücretsiz katman etkinken Azure Portal yeni bir hesap oluşturun](create-cosmosdb-resources-portal.md) veya [ARM şablonu](./manage-with-templates.md#free-tier)kullanın. Daha ayrıntılı bilgi edinmek için [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/cosmos-db/) bakın.

## <a name="try-azure-cosmos-db-for-free"></a>Azure Cosmos DB’yi ücretsiz deneyin

[Ücretsiz Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) , bir Azure hesabına kaydolmadan veya kredi kartınızı kullanarak bulutta Azure Cosmos DB denemenizi sağlayan ücretsiz bir ücret deneyimidir. Deneme Azure Cosmos DB hesapları, şu anda 30 gün sınırlı bir süre için kullanılabilir. Bunları dilediğiniz zaman yenileyebilirsiniz. Azure Cosmos DB hesaplarını deneyin, bir uygulamayı derlemeyi ve test yapmayı ya da hızlı başlangıçlarını veya öğreticilerini kullanmayı Azure Cosmos DB kolaylaştırır. Ayrıca, bir demo oluşturabilir, birim testi gerçekleştirebilir veya hatta çok bölgeli bir hesap oluşturabilir ve herhangi bir maliyet ödemeden bir uygulama çalıştırabilirsiniz. Deneme Azure Cosmos DB hesabında, en fazla 25 kapsayıcı ve 20.000 RU/sn veya 5000 RU/sn 'ye kadar bir kapsayıcı içeren bir paylaşılan üretilen iş veritabanına sahip olabilirsiniz. Başlamak için bkz. [ücretsiz sayfa için Azure Cosmos DB deneyin](https://azure.microsoft.com/try/cosmosdb/) .

## <a name="azure-free-account"></a>Ücretsiz Azure hesabı

Azure Cosmos DB, Azure kredileri ve kaynakları belirli bir süre boyunca ücretsiz olarak sunan [Azure Ücretsiz hesabına](https://azure.microsoft.com/free)dahildir. Özellikle Azure Cosmos DB için bu ücretsiz hesap, tüm yıl boyunca 25 GB depolama ve 400 ru üretilen iş hacmi sağlar. Bu deneyim, herhangi bir geliştiricinin Azure Cosmos DB özelliklerini kolayca test etmesini veya diğer Azure hizmetleriyle sıfır maliyetle tümleştirmesini sağlar. Azure Ücretsiz hesabıyla, ilk 30 gün boyunca harcayabileceğiniz bir $200 kredisi alırsınız. Yükseltmeyi seçinceye kadar Hizmetleri kullanmaya başlasanız bile, ücretlendirilmezsiniz. Başlamak için [Azure Ücretsiz hesap](https://azure.microsoft.com/free) sayfasını ziyaret edin.

## <a name="azure-cosmos-db-serverless"></a>Azure Cosmos DB sunucusuz

[Azure Cosmos DB sunucusuz](serverless.md) , Azure Cosmos hesabınızı, yalnızca veritabanı işlemleriniz tarafından tüketilen istek birimleri ve verileriniz tarafından tüketilen depolama alanı için ücretlendirilebilen tüketim tabanlı bir biçimde kullanmanıza olanak sağlar. Azure Cosmos DB sunucusuz modda kullanırken en düşük ücret söz konusu değildir. Sağlanan kapasite kavramını ortadan kaldırdığı için, özellikle veritabanınız çoğu zaman boşta kaldığında geliştirme veya test etkinliklerine en uygun seçenektir.

## <a name="use-shared-throughput-databases"></a>Paylaşılan verimlilik veritabanlarını kullanma

Paylaşılan bir [üretilen iş veritabanında](set-throughput.md#set-throughput-on-a-database), veritabanının içindeki tüm kapsayıcılar, veritabanının sağlanan VERIMINI (ru/s) paylaşır. Örneğin, 400 RU/sn ile bir veritabanı sağlarsanız ve dört kapsayıcı varsa, dört kapsayıcının tamamı 400 RU/s 'yi paylaşır. Her kapsayıcının daha az sıklıkta erişilebilen bir geliştirme veya test ortamında, en az 400 RU/sn 'tan daha düşük bir işlem yapmak için, kapsayıcıları paylaşılan bir üretilen iş veritabanına koymak maliyeti iyileştirmenize yardımcı olabilir.

Örneğin, geliştirme veya test hesabınızda dört kapsayıcı olduğunu varsayalım. Adanmış aktarım hızı (en az 400 RU/sn) ile dört kapsayıcı oluşturursanız toplam RU/sn, 1600 RU/s olacaktır. Buna karşılık, paylaşılan bir üretilen iş veritabanı (en az 400 RU/sn) oluşturup kapsayıtlarınızı orada yerleştirirseniz, toplam RU/sn yalnızca 400 RU/sn olacaktır. Genel olarak, paylaşılan üretilen iş veritabanları her bir kapsayıcıda garantili aktarım hızı gerektirmeyen senaryolar için idealdir.  [Paylaşılan üretilen iş veritabanları](set-throughput.md#set-throughput-on-a-database) hakkında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerde öykünücü veya ücretsiz Azure Cosmos DB hesaplarını kullanmaya başlamanızı sağlayabilirsiniz:

* [Azure Cosmos DB Faturanızı Anlama](understand-your-bill.md) hakkında daha fazla bilgi edinin
* [Azure Cosmos DB sunucusuz](serverless.md) hakkında daha fazla bilgi edinin
* [Verimlilik maliyetini iyileştirme](optimize-cost-throughput.md) hakkında daha fazla bilgi edinin
* [Depolama maliyetini iyileştirme](optimize-cost-storage.md) hakkında daha fazla bilgi edinin
* [Okuma ve yazma maliyetlerini iyileştirme](optimize-cost-reads-writes.md) hakkında daha fazla bilgi edinin
* [Sorguların maliyetini En Iyi duruma getirme](./optimize-cost-reads-writes.md) hakkında daha fazla bilgi edinin
* [Çok bölgeli Azure Cosmos hesaplarının maliyetini En Iyi duruma getirme](optimize-cost-regions.md) hakkında daha fazla bilgi edinin