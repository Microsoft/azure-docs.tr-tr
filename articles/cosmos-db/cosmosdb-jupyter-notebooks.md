---
title: Azure Cosmos DB 'de yerleşik jupi Not defteri desteğine giriş (Önizleme)
description: Sorguları etkileşimli olarak çalıştırmak için Azure Cosmos DB yerleşik jupi Not defteri desteğini nasıl kullanabileceğinizi öğrenin.
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/19/2020
author: deborahc
ms.author: dech
ms.openlocfilehash: 4f57d2793a28f4bbb201764c67af82f31d0b9333
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100369807"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Azure Cosmos DB 'de yerleşik jupi Not Defteri desteği (Önizleme)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Jupyter Notebook; canlı kod, denklemler, görselleştirmeler ve anlatı metni içeren belgeleri oluşturmanıza ve paylaşmanıza olanak tanıyan açık kaynak bir web uygulamasıdır. 

Azure Cosmos DB yerleşik jupi Not defterleri, Azure portal ve Azure Cosmos DB hesaplarınız ile doğrudan tümleşiktir ve kolayca kullanılabilir hale getirir. Geliştiriciler, veri uzmanları, mühendisler ve analistler, veri araştırması, veri temizleme, veri dönüştürmeleri, sayısal benzetimler, istatistiksel modelleme, veri görselleştirme ve makine öğrenimi yapmak için tanıdık Jupistter Not defteri deneyimini kullanabilir.

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png" alt-text="Azure Cosmos DB 'de jupi Not defteri görselleştirmeleri":::

Azure Cosmos DB, çekirdek (SQL), Cassandra, Gremlin, tablo ve MongoDB için API dahil tüm API 'Ler için hem C# hem de Python not defterlerini destekler. Not defteri içinde, Azure Cosmos DB kaynakları oluşturmayı, verileri yüklemeyi ve verilerinizi Azure Cosmos DB ve görselleştirmeyi kolaylaştıran yerleşik komutlar ve özelliklerden yararlanabilirsiniz. 

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png" alt-text="Azure Cosmos DB 'de jupi Not Defteri desteği":::

## <a name="benefits-of-jupyter-notebooks"></a>Jupyıter Not defterlerinin avantajları

Jupi Not defterleri, başlangıçta Python ve R 'de yazılmış veri bilimi uygulamaları için geliştirilmiştir. Ancak, aşağıdakiler de dahil olmak üzere farklı türlerdeki projeler için çeşitli şekillerde kullanılabilirler:

**Veri görselleştirme:** Jupyter Not defterleri, bir veri kümesini grafik olarak işleyen paylaşılan bir not defteri biçiminde görselleştirmenize olanak tanır. Görselleştirmeler oluşturabilir, paylaşılan kod ve veri kümesinde etkileşimli değişiklikler yapabilir ve sonuçları paylaşabilirsiniz.

**Kod paylaşımı:** GitHub gibi hizmetler, kod paylaşma yollarını sağlar, ancak büyük ölçüde etkileşimli değildir. Jupyter Notebook, kodu görüntüleyebilir, yürütebilir ve sonuçları doğrudan Azure portal görüntüleyebilirsiniz.

**Kod Ile canlı etkileşimler:** Jupyter Notebook kod dinamiktir; düzenleyebilir ve güncelleştirmeleri gerçek zamanlı olarak aşamalı olarak çalıştırabilirsiniz. Kod, tanıtımlar veya kavram kanıtı (POCs) için giriş kaynağı olarak kullanılan kullanıcı denetimlerini (örneğin, kaydırıcılar veya metin girişi alanları) ekleyebilirsiniz.

**Kod örnekleri ve veri araştırma sonuçlarının belgeleri:** Bir kod parçasına sahipseniz ve bu kodun nasıl çalıştığını açıklamak istiyorsanız, Jupyter Notebook ekleyebilirsiniz. Belgelerle birlikte aynı anda etkileşim ekleyebilirsiniz.

**Azure Cosmos DB Için yerleşik komutlar:** Azure Cosmos DB yerleşik MAGIC komutları hesabınızla etkileşimde bulunmayı kolaylaştırır. Bir kapsayıcıya veri yüklemek ve [SQL API sözdizimini](sql-query-getting-started.md)kullanarak sorgulamak için%% upload ve%% SQL gibi komutları kullanabilirsiniz. Ek özel kod yazmanız gerekmez.

**Tek bir yerinde ortamda:** Jupleter Not defterleri kod, zengin metin, resim, video, animasyon, matematik denklemleri, çizimler, Haritalar, etkileşimli şekiller, pencere öğeleri ve grafik kullanıcı arabirimlerini tek bir belgede birleştirir.

## <a name="components-of-a-jupyter-notebook"></a>Jupyter Notebook bileşenleri

Jupyter Not defterleri, her biri ayrı bloklara veya hücrelere düzenlenmiş birkaç bileşen türü içerebilir:

**Metin ve HTML:** HTML oluşturmak için markaşağı sözdiziminde açıklama eklenmiş düz metin veya metin, herhangi bir noktada belgeye eklenebilir. CSS stili ayrıca satır içi olarak dahil edilebilir veya Not defterini oluşturmak için kullanılan şablona eklenebilir.

**Kod ve çıkış:** Jupyıter Not defterleri Python ve C# kodunu destekler. Yürütülen kodun sonuçları, kod bloklarından hemen sonra görünür ve kod blokları istediğiniz sırada birden çok kez yürütülenebilir.

**Görselleştirmeler:** Matplotlib, Plotly, bokeh ve diğerleri gibi modüller kullanarak koddan grafikler ve grafikler oluşturabilirsiniz. Çıkışa benzer şekilde, bu görselleştirmeler, bunları üreten kodun yanında satır içi görüntülenir. Çıkışa benzer şekilde, bu görselleştirmeler, bunları üreten kodun yanında satır içi görüntülenir.

**Multimedya:** Jupi Not defterleri Web teknolojisi üzerine inşa edildiğinden, bir Web sayfası tarafından desteklenen tüm multimedya türlerini görüntüleyebilir. Bunları bir not defterine HTML öğeleri olarak dahil edebilir veya modülünü kullanarak programlı bir şekilde oluşturabilirsiniz `IPython.display` .

**Veri:** Verileri Azure Cosmos kapsayıcılarından veya sorguların sonuçlarını programlı bir şekilde Jupyter Notebook içeri aktarabilirsiniz. Azure Cosmos DB verileri karşıya yüklemek veya sorgulamak için yerleşik sihirli komutları kullanın. 

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB yerleşik Jupyter not defterlerini kullanmaya başlamak için aşağıdaki makalelere bakın:

* [Azure Cosmos hesabında not defterlerini etkinleştirme](enable-notebooks.md)
* [Not defteri örnekleri galerisini keşfet](https://cosmos.azure.com/gallery.html)
* [Python Not defteri özelliklerini ve komutlarını kullanma](use-python-notebook-features-and-commands.md)
* [C# Not defteri özelliklerini ve komutlarını kullanma](use-csharp-notebook-features-and-commands.md)
* [GitHub deposundan not defterlerini içeri aktarma](import-github-notebooks.md)