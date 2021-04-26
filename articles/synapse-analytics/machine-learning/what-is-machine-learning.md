---
title: Azure SYNAPSE Analytics 'te Machine Learning
description: Azure SYNAPSE Analytics 'te makine öğrenimi özelliklerine genel bakış.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 68b113de63cfefde805c1c46e9303829c4eb33a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222148"
---
# <a name="machine-learning-capabilities-in-azure-synapse-analytics"></a>Azure Synapse Analytics'te Makine Öğrenmesi özellikleri

Azure SYNAPSE Analytics çeşitli makine öğrenimi özellikleri sunar. Bu makalede, Azure SYNAPSE bağlamında Machine Learning nasıl uygulayabilmeniz için bir genel bakış sunulmaktadır.

Bu genel bakışta, bir veri bilimi süreç perspektifinden Machine Learning ile ilgili SYNAPSE 'deki farklı yetenekler ele alınmaktadır.

Tipik bir veri bilimi sürecinin nasıl göründüğünü biliyor olabilirsiniz. Birçok makine öğrenimi projesinin izlediği iyi bilinen bir işlemdir.

Yüksek düzeyde, işlem aşağıdaki adımları içerir:
* (İş anlama)
* Veri edinme ve anlama
* Modelleme
* Model dağıtımı ve Puanlama

Bu makalede, bir veri bilimi süreç perspektifinden farklı analiz altyapılarındaki Azure SYNAPSE Machine Learning özellikleri açıklanmaktadır. Veri bilimi işlemindeki her adım için, yardımcı olabilecek Azure SYNAPSE özellikleri özetlenmektedir.

## <a name="azure-synapse-machine-learning-capabilities"></a>Azure SYNAPSE Machine Learning özellikleri

### <a name="data-acquisition-and-understanding"></a>Veri edinme ve anlama

Çoğu makine öğrenimi projesi iyi belirlenen adımları içerir ve bu adımlardan biri verilere erişmek ve bunları anlamak.

#### <a name="data-source-and-pipelines"></a>Veri kaynağı ve işlem hatları

Azure SYNAPSE 'in yerel olarak tümleşik bir parçası olan [Azure Data Factory](../../data-factory/introduction.md)için, veri alma ve veri düzenleme işlem hatları için kullanabileceğiniz güçlü bir araç kümesi vardır. Bu, verilere erişmek ve verileri makine öğrenimi için kullanılabilecek bir biçime dönüştürmek için kolayca veri işlem hatlarını oluşturmanızı sağlar. SYNAPSE ' de [veri işlem hatları hakkında daha fazla bilgi edinin](../../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) . 

#### <a name="data-preparation-and-explorationvisualization"></a>Veri hazırlama ve araştırma/görselleştirme

Machine Learning işleminin önemli bir bölümü, keşif ve görselleştirmeler ile verileri anlamaktır.

Verilerin nerede depolandığına bağlı olarak, SYNAPSE analiz ve makine öğrenimi için keşfetmeye ve hazırlamaya yönelik bir dizi farklı araç sunar. Veri araştırmasını kullanmaya başlamanın en hızlı yolu, doğrudan veri Gölü veriler üzerinde Apache Spark veya sunucusuz SQL havuzları kullanmaktır.

* [Azure SYNAPSE için Apache Spark](../spark/apache-spark-overview.md) , verilerinizi ölçekli olarak dönüştürme, hazırlama ve keşfetmeye yönelik yetenekler sunmaktadır. Bu Spark havuzları, ölçeklendirerek veri işleme için PySpark/Python, Scala ve .NET gibi araçlar sunar. Güçlü görselleştirme kitaplıklarını kullanarak, veri araştırma deneyimi verileri daha iyi anlamanıza yardımcı olacak şekilde geliştirilebilir. [Spark kullanarak SYNAPSE içindeki verileri araştırma ve görselleştirme hakkında daha fazla bilgi edinin](../get-started-analyze-spark.md).

* [Sunucusuz SQL havuzları](../sql/on-demand-workspace-overview.md) , doğrudan veri Gölü üzerinde TSQL kullanarak verileri keşfetmeye yönelik bir yol sunar. Sunucusuz SQL havuzları Ayrıca SYNAPSE Studio 'da bazı yerleşik görselleştirmeler sunar. [Sunucusuz SQL havuzlarıyla verileri araştırma hakkında daha fazla bilgi edinin](../get-started-analyze-sql-on-demand.md).

### <a name="modeling"></a>Modelleme

Azure SYNAPSE 'de Eğitim makinesi öğrenimi modelleri, Apache Spark havuzlarında PySpark/Python, Scala veya .NET gibi araçlarla gerçekleştirilebilir.

#### <a name="train-models-on-spark-pools-with-mllib"></a>MLlib ile Spark havuzlarında modeller eğitme

Makine öğrenimi modelleri çeşitli algoritmalardan ve kitaplıklardan yardım ile eğitilmiş olabilir. [Spark MLlib](http://spark.apache.org/docs/latest/ml-guide.html) , klasik makine öğrenimi sorunlarının çoğunu çözmenize yardımcı olabilecek ölçeklenebilir makine öğrenimi algoritmaları sunar. [Bu öğreticide](../spark/apache-spark-machine-learning-mllib-notebook.md) , SYNAPSE Içinde mllib kullanarak bir modelin nasıl eğiteyapılacağı ele alınmaktadır.

MLlib 'in yanı sıra, [Scikit öğreni](https://scikit-learn.org/stable/) gibi popüler kitaplıklar da modeller geliştirmek için de kullanılabilir. SYNAPSE Spark havuzlarında kitaplıkların nasıl yükleneceğine ilişkin ayrıntılar için bkz. [Azure SYNAPSE Analytics 'te Apache Spark için kitaplıkları yönetme](../spark/apache-spark-azure-portal-add-libraries.md) .

#### <a name="train-models-with-azure-machine-learning-automated-ml"></a>Azure Machine Learning otomatikleştirilmiş ML ile modelleri eğitme

Makine öğrenimi modellerini eğitmek için başka bir yöntem de, Machine Learning ile ilgili çok daha fazla bilgi gerektirmez. otomatik ML 'yi kullanmaktır. [OTOMATIK ml](../../machine-learning/concept-automated-ml.md) , bir makine öğrenimi modelleri kümesini otomatik olarak gösteren ve kullanıcının belirli ölçümleri temel alarak en iyi modeli seçmesini sağlayan bir özelliktir. Azure Machine Learning Azure SYNAPSE not defterlerinden sorunsuz bir tümleştirme sayesinde, kullanıcılar SYNAPSE 'de otomatik ML 'yi geçiş Azure Active Directory kimlik doğrulamasıyla kolayca kullanabilir.  Bu, yalnızca Azure Machine Learning çalışma alanınızı işaret etmeniz ve herhangi bir kimlik bilgisi girmeniz gerekmediği anlamına gelir. SYNAPSE Spark havuzlarında Azure Machine Learning otomatikleştirilmiş ML kullanarak modellerin nasıl eğleneceğini açıklayan [Otomatikleştirilmiş BIR ml öğreticisi](../spark/apache-spark-azure-machine-learning-tutorial.md) aşağıda verilmiştir.

### <a name="model-deployment-and-scoring"></a>Model dağıtımı ve Puanlama

Azure SYNAPSE ya da Azure SYNAPSE dışında eğitilen modeller toplu Puanlama için kolayca kullanılabilir. Şu anda SYNAPSE ' de, toplu Puanlama çalıştırmak için iki yol vardır.

* Verilerinizin nerede yaşadığı tahminlerinizi çalıştırmak için SYNAPSE SQL havuzlarındaki [TSQL tahmin işlevini](../sql-data-warehouse/sql-data-warehouse-predict.md) kullanabilirsiniz. Bu güçlü ve ölçeklenebilir işlevi, veri ambarınızdaki verileri taşımadan verilerinizi zenginleştirmenize olanak tanır. [SYNAPSE Studio 'da yeni bir Kılavuzlu makine öğrenimi modeli deneyimi](./tutorial-sql-pool-model-scoring-wizard.md) , SYNAPSE SQL havuzlarındaki Azure Machine Learning modeli kayıt defterinden bir onnx MODELINI, tahmin kullanan toplu Puanlama için dağıtabilirsiniz.

* Azure SYNAPSE 'deki toplu Puanlama makine öğrenimi modelleri için başka bir seçenek de Azure SYNAPSE için Apache Spark havuzlarından faydalanır. Modelleri eğitebilmeniz için kullanılan kitaplıklara bağlı olarak, Batch puanlamasını çalıştırmak için bir kod deneyimi kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Synapse Analytics ile çalışmaya başlama](../get-started.md)
* [Çalışma alanı oluşturma](../get-started-create-workspace.md)
* [Hızlı başlangıç: SYNAPSE 'de yeni Azure Machine Learning bağlantılı hizmet oluşturma](quickstart-integrate-azure-machine-learning.md)
* [Öğretici: Machine Learning model Puanlama Sihirbazı-adanmış SQL havuzu](tutorial-sql-pool-model-scoring-wizard.md)