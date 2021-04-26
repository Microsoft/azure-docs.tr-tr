---
title: 'Öğretici: bilişsel hizmetler ile yaklaşım Analizi'
description: Azure SYNAPSE Analytics 'te yaklaşım analizi için bilişsel hizmetler 'i nasıl kullanacağınızı öğrenin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 08d5e53facce172c2287c2e341895f0ee38571f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943709"
---
# <a name="tutorial-sentiment-analysis-with-cognitive-services-preview"></a>Öğretici: bilişsel hizmetler ile yaklaşım Analizi (Önizleme)

Bu öğreticide, Azure bilişsel [Hizmetler](../../cognitive-services/index.yml)Ile Azure SYNAPSE Analytics 'te verilerinizi kolayca zenginleştirme hakkında bilgi edineceksiniz. [Metin analizi](../../cognitive-services/text-analytics/index.yml) yeteneklerini, yaklaşım analizini gerçekleştirmek için kullanacaksınız. 

Azure SYNAPSE ' deki bir Kullanıcı, yalnızca sentiler ile zenginleştirmek için metin sütunu içeren bir tablo seçebilir. Bu yaklaşım pozitif, negatif, karışık veya nötr olabilir. Bir olasılık da döndürülür.

Bu öğreticinin içindekiler:

> [!div class="checklist"]
> - Yaklaşım analizi için metin sütunu içeren Spark tablo veri kümesini alma adımları.
> - Bilişsel hizmetler 'de Metin Analizi kullanarak verileri zenginleştirmek için Azure SYNAPSE 'de sihirbaz deneyimi kullanma.

Azure aboneliğiniz yoksa [başlamadan önce ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

- Varsayılan depolama alanı olarak yapılandırılmış bir Azure Data Lake Storage 2. depolama hesabıyla [Azure SYNAPSE Analytics çalışma alanı](../get-started-create-workspace.md) . Birlikte çalıştığınız Data Lake Storage 2. dosya sisteminin *Depolama Blobu veri katılımcısı* olması gerekir.
- Azure SYNAPSE Analytics çalışma alanınızdaki Spark Havuzu. Ayrıntılar için bkz. [Azure 'Da Spark havuzu oluşturma SYNAPSE](../quickstart-create-sql-pool-studio.md).
- Öğreticide açıklanan ön yapılandırma adımları [Azure SYNAPSE 'de bilişsel hizmetler 'ı yapılandırın](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="create-a-spark-table"></a>Spark tablosu oluşturma

Bu öğretici için bir Spark tablosu gerekir.

1. Metin analizi için bir veri kümesi içeren [FabrikamComments.csv](https://github.com/Kaiqb/KaiqbRepo0731190208/blob/master/CognitiveServices/TextAnalytics/FabrikamComments.csv) dosyasını indirin. 

1. Dosyayı Data Lake Storage 2. Azure SYNAPSE Storage hesabınıza yükleyin.
  
   ![Karşıya veri yükleme seçimlerini gösteren ekran görüntüsü.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00a.png)

1. Dosyaya sağ tıklayıp **Yeni Not defteri**  >  **oluşturma Spark tablosu**' nı seçerek. csv dosyasından bir Spark tablosu oluşturun.

   ![Spark tablosu oluşturma seçimlerini gösteren ekran görüntüsü.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00b.png)

1. Tabloyu kod hücresine adlandırın ve Not defterini Spark havuzunda çalıştırın. Ayarlamayı unutmayın `header=True` .

   ![Bir not defteri çalıştırmayı gösteren ekran görüntüsü.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00c.png)

   ```python
   %%pyspark
   df = spark.read.load('abfss://default@azuresynapsesa.dfs.core.windows.net/data/FabrikamComments.csv', format='csv'
   ## If a header exists, uncomment the line below
   , header=True
   )
   df.write.mode("overwrite").saveAsTable("default.YourTableName")
   ```

## <a name="open-the-cognitive-services-wizard"></a>Bilişsel hizmetler Sihirbazı 'nı açın

1. Önceki yordamda oluşturulan Spark tablosuna sağ tıklayın.   >  Sihirbazı açmak için **mevcut modeliyle Machine Learning zenginleştirme** seçeneğini belirleyin.

   ![Puanlama Sihirbazı 'nı açmak için seçimleri gösteren ekran görüntüsü.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00d.png)

2. Bir yapılandırma paneli görünür ve bilişsel hizmetler modeli seçmeniz istenir. **Metin analizi-yaklaşım Analizi** seçin.

   ![Bilişsel hizmetler modelinin seçimini gösteren ekran görüntüsü.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00e.png)

## <a name="provide-authentication-details"></a>Kimlik doğrulama ayrıntılarını sağlayın

Bilişsel hizmetler 'in kimliğini doğrulamak için anahtar kasanızın gizli anahtarına başvurmanız gerekir. Aşağıdaki girişler, bu noktadan önce tamamlanması gereken [Önkoşul adımlarına](tutorial-configure-cognitive-services-synapse.md) bağımlıdır.

- **Azure aboneliği**: anahtar kasanızın ait olduğu Azure aboneliğini seçin.
- Bilişsel **Hizmetler hesabı**: bağlanacağı metin analizi kaynağını girin.
- **Bağlı hizmet Azure Key Vault**: önkoşul adımlarının bir parçası olarak, metin analizi kaynağınız için bağlı bir hizmet oluşturdunuz. Buradan seçin.
- **Gizli dizi adı**: bilişsel hizmetler kaynağınız için kimlik doğrulaması yapmak üzere anahtarı içeren anahtar kasanızda gizli dizi adını girin.

![Bir Anahtar Kasası için kimlik doğrulama ayrıntılarını gösteren ekran görüntüsü.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00f.png)

## <a name="configure-sentiment-analysis"></a>Yaklaşım analizini yapılandırma

Sonra, yaklaşım analizini yapılandırın. Aşağıdaki ayrıntıları seçin:
- **Dil**: üzerinde yaklaşım analizi gerçekleştirmek istediğiniz metnin dili olarak **İngilizce** ' yi seçin.
- **Metin sütunu**: açıklamayı belirlemek için analiz etmek istediğiniz metin sütunu olarak **Açıklama (dize)** seçeneğini belirleyin.

İşiniz bittiğinde, **Not defteri 'Ni aç**' ı seçin. Bu, Azure bilişsel hizmetler ile yaklaşım analizini gerçekleştiren PySpark kodu ile sizin için bir not defteri oluşturur.

![Yaklaşım analizini yapılandırmaya yönelik seçimleri gösteren ekran görüntüsü.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00g.png)

## <a name="run-the-notebook"></a>Not defterini çalıştırma

Az önce açtığınız Not defteri, bilişsel hizmetlere bağlanmak için [mmlspark kitaplığını](https://github.com/Azure/mmlspark) kullanır. Belirttiğiniz Azure Key Vault ayrıntıları, bu deneyimdeki gizli dizilerinize onları göstermeden güvenli bir şekilde başvurmanızı sağlar.

Artık tüm hücreleri çalıştırarak verilerinizi Sentiments ile zenginleştirebilirsiniz. **Tümünü Çalıştır**' ı seçin. 

Yaklaşımları **pozitif**, **negatif**, **nötr** veya **karma** olarak döndürülür. Ayrıca, yaklaşım başına olasılıklara de ulaşın. Bilişsel [Hizmetler 'de yaklaşım analizi hakkında daha fazla bilgi edinin](../../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md).

![Yaklaşım analizini gösteren ekran görüntüsü.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00h.png)

## <a name="next-steps"></a>Sonraki adımlar
- [Öğretici: Azure bilişsel hizmetler ile anomali algılama](tutorial-cognitive-services-sentiment.md)
- [Öğretici: Azure SYNAPSE adanmış SQL havuzlarında makine öğrenimi modeli Puanlama](tutorial-sql-pool-model-scoring-wizard.md)
- [Azure Synapse Analytics'te Makine Öğrenmesi özellikleri](what-is-machine-learning.md)