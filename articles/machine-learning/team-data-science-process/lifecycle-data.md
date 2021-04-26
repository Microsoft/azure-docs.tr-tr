---
title: Team Data Science Işleminin veri alımı ve anlama
description: Veri bilimi projelerinizin veri alımı ve anlama aşamasına yönelik hedefler, görevler ve teslim edilebilirler
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: fffb52e333bea1b2be11b127a9eab6656dc1d1f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94740337"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Team Data Science Işleminin veri alımı ve anlama aşaması

Bu makalede, Team Data Science Işleminin (TDSP) veri alımı ve öğrenimi ile ilişkili hedefler, görevler ve teslim edilebilirler özetlenmektedir. Bu işlem, veri bilimi projelerinizi yapılandırmak için kullanabileceğiniz önerilen bir yaşam döngüsü sağlar. Yaşam döngüsü, projelerin tipik olarak yürütülen ana aşamaları özetler, genellikle yinelemeli olarak:

   1. **Kurumsal yaklaşım**
   2. **Veri edinme ve anlama**
   3. **Modelleme**
   4. **Dağıtım**
   5. **Müşteri kabulü**

TDSP yaşam döngüsünün görsel bir gösterimi aşağıda verilmiştir: 

![TDSP yaşam döngüsü](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Hedefler
* Hedef değişkenlerle ilişkilerini anlamış olan temiz ve yüksek kaliteli bir veri kümesi üretin. Modele hazırsanız, uygun analiz ortamında veri kümesini bulun.
* Verileri düzenli olarak yenileyen ve puanlarını karşılayan veri işlem hattının çözüm mimarisini geliştirin.

## <a name="how-to-do-it"></a>Nasıl yapılır?
Bu aşamada listelenen üç ana görev vardır:

   * **Verileri** hedef analitik ortama alma.
   * Veri kalitesinin soruyu yanıtlamak için yeterli olup olmadığını öğrenmek için **verileri araştırın** . 
   * Yeni veya düzenli olarak yenilenen verileri öğrenmek için **bir veri işlem hattı ayarlayın** .

### <a name="ingest-the-data"></a>Verileri alma
Verileri kaynak konumlarından eğitim ve tahmin gibi analiz işlemlerini çalıştırdığınız hedef konumlara taşımak için işlemi ayarlayın. Verilerin çeşitli Azure veri hizmetleriyle nasıl taşınacağı hakkında teknik ayrıntılar ve seçenekler için bkz. [analiz için depolama ortamlarına veri yükleme](ingest-data.md). 

### <a name="explore-the-data"></a>Verileri keşfetme
Modellerinizi eğitmeden önce, verileri bir ses olarak öğrenmeniz gerekir. Gerçek dünyada veri kümeleri genellikle gürültülü, eksik değerlerdir veya diğer tutarsızlıklardan oluşan bir konağa sahiptir. Veri özetlemesini ve görselleştirmeyi kullanarak verilerinizin kalitesini denetleyebilir ve verileri modellemeye hazırlanmadan önce işlemek için gereken bilgileri sağlayabilirsiniz. Bu işlem genellikle yinelemelidir. Verileri temizleme hakkında yönergeler için bkz. [Gelişmiş makine öğrenimi için veri hazırlama görevleri](prepare-data.md).  

Temizleme verilerinin kalitesinden memnun kaldıktan sonra, bir sonraki adım veride bulunan desenleri daha iyi anlamak için kullanılır. Bu veri analizi, hedef için uygun bir tahmine dayalı model seçmenize ve geliştirmenize yardımcı olur. Verilerin hedefe ne kadar iyi bağlandığını bulun. Ardından, sonraki modelleme adımlarıyla ileri doğru hareket etmek için yeterli veri olup olmadığını saptayın. Bu işlem, genellikle yinelemeli bir işlemdir. Başlangıçta önceki aşamada tanımlanan veri kümesini genişletmek için daha doğru veya daha fazla ilgili veriye sahip yeni veri kaynakları bulmanız gerekebilir. 

### <a name="set-up-a-data-pipeline"></a>Veri işlem hattı ayarlama
Verilerin ilk alımı ve temizlenmesinin yanı sıra, genellikle yeni verileri skor veya devam eden bir öğrenme sürecinin bir parçası olarak verileri düzenli olarak yenilemek için bir işlem ayarlamanız gerekir. Puanlama, bir veri işlem hattı veya iş akışı ile tamamlanabilir. [SQL Server örneğinden Azure SQL veritabanı 'na veri taşıma Azure Data Factory makale ile](move-sql-azure-adf.md) [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)ile işlem hattının nasıl ayarlanacağı hakkında bir örnek sağlar. 

Bu aşamada, veri işlem hattının çözüm mimarisini geliştirirsiniz. Veri bilimi projesinin bir sonraki aşamasına sahip işlem hattını paralel olarak geliştirirsiniz. İş gereksinimlerinize ve bu çözümün tümleştirildiği mevcut sistemlerinizin kısıtlamalarına bağlı olarak, işlem hattı aşağıdaki seçeneklerden biri olabilir: 

   * Toplu iş tabanlı
   * Akış veya gerçek zamanlı 
   * Karma 

## <a name="artifacts"></a>Artifacts
Bu aşamada teslim edilebilirler aşağıda verilmiştir:

   * [Veri kalitesi raporu](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md): Bu rapor, veri özetlerini, her öznitelik ve hedef, değişken derecelendirmesi ve daha fazlasını içeren ilişkileri içerir. 
   * **Çözüm mimarisi**: çözüm mimarisi, bir model oluşturduktan sonra yeni verilerde Puanlama veya tahmin çalıştırmak için kullandığınız veri işlem hattının bir diyagramı veya açıklaması olabilir. Ayrıca, yeni verilere göre modelinizi yeniden eğitme işlem hattını de içerir. TDSP dizin yapısı şablonunu kullandığınızda belgeyi [Proje](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) dizininde depolayın.
   * **Kontrol noktası kararı**: tam özellik Mühendisliği ve model oluşturmaya başlamadan önce, beklenen değerin, söz konusu değeri uygulamaya devam etmek için yeterli olup olmadığını tespit etmek üzere projeyi yeniden değerlendirmeye başlayabilirsiniz. Örneğin, işleme hazırsanız, daha fazla veri toplamanız veya bu soruyu yanıtlamak için veriler mevcut olmadığından projeyi iptal etmeye de devam edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

TDSP yaşam döngüsüyle her adımın bağlantıları aşağıda verilmiştir:

   1. [Kurumsal yaklaşım](lifecycle-business-understanding.md)
   2. [Veri edinme ve anlama](lifecycle-data.md)
   3. [Modelleme](lifecycle-modeling.md)
   4. [Dağıtım](lifecycle-deployment.md)
   5. [Müşteri kabulü](lifecycle-acceptance.md)

Belirli senaryolar için işlemdeki tüm adımları gösteren tam izlenecek yollar sunuyoruz. [Örnek yönergeler](walkthroughs.md) makalesi, bağlantılar ve küçük resim açıklamaları olan senaryoların bir listesini sağlar. İzlenecek yollar, akıllı bir uygulama oluşturmak için bulut, şirket içi araçların ve hizmetlerin bir iş akışı veya işlem hattına nasıl birleştirileceğini gösterir. 

Azure Machine Learning Studio kullanan TDSPs 'de adımların nasıl yürütüleceği hakkında örnekler için, bkz. [Azure Machine Learning Ile TDSP kullanma]().
