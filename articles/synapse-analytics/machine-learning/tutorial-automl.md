---
title: 'Öğretici: otomatik makine öğrenimi kullanarak model eğitme'
description: Azure SYNAPSE Analytics 'te kod olmadan makine öğrenimi modelini eğitme hakkında öğretici.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: aaf0aab2ef600b269b9b47182aeb096ca13c7a87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943528"
---
# <a name="tutorial-train-a-machine-learning-model-without-code"></a>Öğretici: kod olmadan makine öğrenimi modelini eğitme

Spark tablolarında verilerinizi, [otomatik makine öğrenimini](../../machine-learning/concept-automated-ml.md)kullanarak eğitediğiniz yeni makine öğrenimi modelleriyle zenginleştirebilirsiniz. Azure SYNAPSE Analytics 'te, makine öğrenimi modelleri oluşturmak için eğitim veri kümesi olarak kullanmak üzere çalışma alanında Spark tablosu seçebilir ve bunu bir kod ücretsiz deneyimde yapabilirsiniz.

Bu öğreticide, SYNAPSE Studio 'da bir kod ücretsiz deneyim kullanarak makine öğrenimi modellerini eğitme hakkında bilgi edineceksiniz. SYNAPSE Studio, Azure SYNAPSE Analytics 'in bir özelliğidir. 

Deneyimi el ile kodlamak yerine Azure Machine Learning otomatik makine öğrenimini kullanacaksınız. Eğeceğiniz modelin türü, çözmeye çalıştığınız soruna bağlıdır.

Azure aboneliğiniz yoksa [başlamadan önce ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

- [Azure SYNAPSE Analytics çalışma alanı](../get-started-create-workspace.md). Varsayılan depolama alanı olarak yapılandırılmış bir Azure Data Lake Storage 2. depolama hesabına sahip olduğundan emin olun. Birlikte çalıştığınız Data Lake Storage 2. dosya sistemi için, *Depolama Blobu veri katılımcısı* olduğunuzdan emin olun.
- Azure SYNAPSE Analytics çalışma alanınızda bir Apache Spark Havuzu. Ayrıntılar için bkz. [hızlı başlangıç: SYNAPSE Studio kullanarak adanmış BIR SQL havuzu oluşturma](../quickstart-create-sql-pool-studio.md).
- Azure SYNAPSE Analytics çalışma alanınızda Azure Machine Learning bağlı bir hizmet. Ayrıntılar için bkz. [hızlı başlangıç: Azure SYNAPSE Analytics 'te yeni Azure Machine Learning bağlantılı hizmet oluşturma](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="create-a-spark-table-for-the-training-dataset"></a>Eğitim veri kümesi için Spark tablosu oluşturma

Bu öğretici için Spark tablosuna ihtiyacınız vardır. Aşağıdaki Not defteri bir tane oluşturur:

1. [Create-Spark-Table-NYCTaxi-Data. ipynb](https://go.microsoft.com/fwlink/?linkid=2149229)Not defterini indirin.

1. Not defterini SYNAPSE Studio 'ya aktarın.
![Içeri aktarma seçeneği vurgulanmış şekilde Azure SYNAPSE Analytics ekran görüntüsü.](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. Kullanmak istediğiniz Spark havuzunu seçin ve ardından **Tümünü Çalıştır**' ı seçin. Bu adım açık veri kümesinden yeni York vergi verileri alır ve verileri varsayılan Spark veritabanınıza kaydeder.
![Tüm ve Spark veritabanını çalıştır vurgulanmış şekilde Azure SYNAPSE Analytics 'in ekran görüntüsü.](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. Not defteri çalıştırıldıktan sonra, varsayılan Spark veritabanı altında yeni bir Spark tablosu görürsünüz. **Verilerden** **nyc_taxi** adlı tabloyu bulun.
![Yeni tablo vurgulanmış şekilde, Azure SYNAPSE Analytics verileri sekmesinin ekran görüntüsü.](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="open-the-automated-machine-learning-wizard"></a>Otomatik makine öğrenimi Sihirbazı 'nı açın

Sihirbazı açmak için:

1. Önceki adımda oluşturduğunuz Spark tablosuna sağ tıklayın. Ardından   >  **yeni modeliyle Machine Learning zenginleştirme** seçeneğini belirleyin.
![Spark tablosunun, Machine Learning ve yeni model vurgulanmış şekilde zenginleştirme ekran görüntüsü.](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

1. Azure Machine Learning içinde otomatik makine öğrenimi denemesi oluşturmak için yapılandırma ayrıntılarını sağlayın. Bu, birden çok modeli çalıştırır. Başarılı bir çalıştırmanın en iyi modeli Azure Machine Learning modeli kayıt defterine kaydedilir.

   ![Machine Learning modelini eğitmek için yapılandırma belirtimlerinin ekran görüntüsü.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

    - **Azure Machine Learning çalışma alanı**: otomatik makine öğrenimi çalıştırması oluşturmak için bir Azure Machine Learning çalışma alanı gerekir. Ayrıca, [bağlantılı bir hizmet](quickstart-integrate-azure-machine-learning.md)kullanarak Azure SYNAPSE Analytics çalışma alanınızı Azure Machine Learning çalışma alanıyla bağlamanız gerekir. Tüm önkoşulları yerine getirdiğinizde, bu otomatik çalıştırma için kullanmak istediğiniz Azure Machine Learning çalışma alanını belirtebilirsiniz.

    - **Deneme adı**: deneme adını belirtin. Bir otomatik makine öğrenimi çalıştırması gönderdiğinizde, bir deneme adı sağlarsınız. Çalıştırma bilgileri, Azure Machine Learning çalışma alanında bu deneme altında depolanır. Bu deneyim, varsayılan olarak yeni bir deneme oluşturur ve önerilen bir ad oluşturur, ancak aynı zamanda var olan bir deneyin adını da sağlayabilirsiniz.

    - **En iyi model adı**: otomatik çalıştırmada en iyi modelin adını belirtin. En iyi modele bu ad verilir ve bu çalışma sonrasında Azure Machine Learning modeli kayıt defterine otomatik olarak kaydedilir. Otomatik makine öğrenimi çalıştırması birçok makine öğrenimi modeli oluşturur. Sonraki adımda seçtiğiniz birincil ölçüme bağlı olarak, bu modeller karşılaştırılabilir ve en iyi model seçilebilir.

    - **Hedef sütun**: modelin tahmin etmek için eğitilme işlemi budur. Tahmin etmek istediğiniz sütunu seçin. (Bu öğreticide, hedef sütun olarak sayısal sütunu seçtik `fareAmount` .)

    - **Spark havuzu**: otomatik deneme çalıştırması için kullanmak istediğiniz Spark havuzunu belirtin. Hesaplamalar belirttiğiniz havuzda çalıştırılır.

    - **Spark yapılandırma ayrıntıları**: Spark havuzuna ek olarak, oturum yapılandırma ayrıntılarını sağlama seçeneğiniz vardır.

1. **Devam**’ı seçin.

## <a name="choose-a-task-type"></a>Bir görev türü seçin

Denemeye çalıştığınız soruya bağlı olarak, deneme için makine öğrenimi model türünü seçin. `fareAmount`Hedef sütun olduğundan ve sayısal bir değer olduğundan, burada **gerileme** ' ı seçmenizi öneririz. Daha sonra **Devam** seçeneğini belirleyin.

![Yeni modelle zenginleştirme ve gerileme vurgulanacak ekran görüntüsü.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>Ek yapılandırmalar

Önceki bölümde model türü olarak **regresyon** veya **Sınıflandırma** ' yı seçtiyseniz aşağıdaki konfigürasyonlar mevcuttur:

- **Birincil ölçüm**: modelin ne kadar iyi yaptığını ölçen ölçümü girin. Otomatikleştirilmiş çalıştırmada oluşturulan farklı modelleri karşılaştırmak ve hangi modelin en iyi şekilde gerçekleştirileceğini öğrenmek için bu ölçümü kullanırsınız.

- **Eğitim işi süresi (saat)**: modelleri çalıştırma ve eğitme denemesi için en uzun süreyi saat cinsinden belirtin. 1 ' den küçük değerler de sağlayabileceğinizi unutmayın (örneğin, **0,5**).

- **Maksimum eşzamanlı yineleme**: paralel olarak çalışan en fazla yineleme sayısını seçin.

- **Onnx model uyumluluğu**: Bu seçeneği etkinleştirirseniz otomatik makine öğrenimi tarafından eğitilen modeller onnx biçimine dönüştürülür. Bu, özellikle Azure SYNAPSE Analytics SQL havuzlarında Puanlama için model kullanmak istiyorsanız geçerlidir.

Bu ayarların hepsi, özelleştirebileceğiniz bir varsayılan değere sahiptir.
![Regresyon modeli yapılandırmaya yönelik ek yapılandırmaların ekran görüntüsü.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

Tüm gerekli yapılandırma yapıldıktan sonra otomatik çalıştırmayı başlatabilirsiniz. Kod olmadan, çalıştırmayı doğrudan başlatan **çalıştırma oluştur** seçeneğini belirleyebilirsiniz. Alternatif olarak, kodu tercih ediyorsanız **Not defterinde aç**' ı seçebilirsiniz. Bu seçenek, çalıştırmayı oluşturan kodu görmenizi ve sonra Not defterini çalıştırmanızı sağlar.

>[!NOTE]
>Önceki bölümde model türü olarak **zaman serisi tahmini** ' ni seçtiyseniz ek konfigürasyonlar yapmanız gerekir. Tahmin Ayrıca ONNX model uyumluluğunu desteklemez.

### <a name="create-a-run-directly"></a>Doğrudan bir çalıştırma oluşturun

Otomatik makine öğrenimini doğrudan çalıştırmak için **çalıştırmayı Başlat**' ı seçin. Çalıştırmanın başladığını belirten bir bildirim görürsünüz. Sonra başarıyı belirten başka bir bildirim görürsünüz. Ayrıca, bildirimde bulunan bağlantıyı seçerek Azure Machine Learning durumu kontrol edebilirsiniz.
![Başarılı bildirimin ekran görüntüsü.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-a-run-with-a-notebook"></a>Not defteri ile çalıştırma oluşturma

Bir not defteri oluşturmak için **Not defterinde aç**' ı seçin. Ardından **Tümünü Çalıştır**' ı seçin. Bu Ayrıca, otomatik makine öğrenimi çalıştırmaya ayarlar ekleme olanağı sunar.

![Tümünü Çalıştır vurgulanmış bir not defterinin ekran görüntüsü.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

Çalıştırmayı başarıyla gönderdikten sonra, Not defteri çıktısındaki Azure Machine Learning çalışma alanındaki denemenin bir bağlantısını görürsünüz. Azure Machine Learning otomatik çalıştırmayı izlemek için bağlantıyı seçin.
![Bir bağlantı vurgulanmış şekilde Azure SYNAPSE Analytics ekran görüntüsü. ](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png) )

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: adanmış SQL havuzları için makine öğrenme modeli Puanlama Sihirbazı (Önizleme)](tutorial-sql-pool-model-scoring-wizard.md)
- [Hızlı başlangıç: Azure SYNAPSE Analytics 'te yeni Azure Machine Learning bağlantılı hizmet oluşturma](quickstart-integrate-azure-machine-learning.md)
- [Azure SYNAPSE Analytics 'te makine öğrenimi özellikleri](what-is-machine-learning.md)