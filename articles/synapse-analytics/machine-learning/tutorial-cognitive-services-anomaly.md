---
title: 'Öğretici: bilişsel hizmetler ile anomali algılama'
description: Azure SYNAPSE Analytics 'te anomali algılama için bilişsel hizmetler 'i nasıl kullanacağınızı öğrenin.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: c54300bf37f6f4526c525b1502d902e5f4336ed7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943498"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services-preview"></a>Öğretici: bilişsel hizmetler ile anomali algılama (Önizleme)

Bu öğreticide, Azure bilişsel [Hizmetler](../../cognitive-services/index.yml)Ile Azure SYNAPSE Analytics 'te verilerinizi kolayca zenginleştirme hakkında bilgi edineceksiniz. [Anomali algılayıcısının](../../cognitive-services/anomaly-detector/index.yml) öğrenilerini bulmak için kullanacaksınız. Azure SYNAPSE ' deki bir Kullanıcı, anomali algılama için zenginleştirmek üzere bir tablo seçebilir.

Bu öğreticinin içindekiler:

> [!div class="checklist"]
> - Zaman serisi verilerini içeren Spark tablo veri kümesini alma adımları.
> - Bilişsel hizmetler 'de anomali algılayıcısı kullanarak verileri zenginleştirmek için Azure SYNAPSE 'de sihirbaz deneyimi kullanımı.

Azure aboneliğiniz yoksa [başlamadan önce ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

- Varsayılan depolama alanı olarak yapılandırılmış bir Azure Data Lake Storage 2. depolama hesabıyla [Azure SYNAPSE Analytics çalışma alanı](../get-started-create-workspace.md) . Birlikte çalıştığınız Data Lake Storage 2. dosya sisteminin *Depolama Blobu veri katılımcısı* olması gerekir.
- Azure SYNAPSE Analytics çalışma alanınızdaki Spark Havuzu. Ayrıntılar için bkz. [Azure 'Da Spark havuzu oluşturma SYNAPSE](../quickstart-create-sql-pool-studio.md).
- Azure 'da bilişsel [Hizmetler 'ı yapılandırma SYNAPSE](tutorial-configure-cognitive-services-synapse.md) öğreticisindeki yapılandırma öncesi adımların tamamlanması.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="create-a-spark-table"></a>Spark tablosu oluşturma

Bu öğretici için bir Spark tablosu gerekir.

1. Spark tablosu oluşturmak için kod içeren aşağıdaki Not defteri dosyasını indirin: [prepare_anomaly_detector_data. ipynb](https://go.microsoft.com/fwlink/?linkid=2149577).

1. Dosyayı Azure SYNAPSE çalışma alanınıza yükleyin.

   ![Not defteri karşıya yükleme seçimlerini gösteren ekran görüntüsü.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. Not defteri dosyasını açın ve tüm hücreleri çalıştırmak için **Tümünü Çalıştır** ' ı seçin.

   ![Spark tablosu oluşturma seçimlerini gösteren ekran görüntüsü.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. **Anomaly_detector_testing_data** adlı Spark tablosu artık varsayılan Spark veritabanında görünmelidir.

## <a name="open-the-cognitive-services-wizard"></a>Bilişsel hizmetler Sihirbazı 'nı açın

1. Önceki adımda oluşturulan Spark tablosuna sağ tıklayın.   >  Sihirbazı açmak için **mevcut modeliyle Machine Learning zenginleştirme** seçeneğini belirleyin.

   ![Puanlama Sihirbazı 'nı açmak için seçimleri gösteren ekran görüntüsü.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. Bir yapılandırma paneli görünür ve bilişsel hizmetler modeli seçmeniz istenir. **Anomali algılayıcısı**' nı seçin.

   ![Model olarak anomali algılayıcısı seçimini gösteren ekran görüntüsü.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="provide-authentication-details"></a>Kimlik doğrulama ayrıntılarını sağlayın

Bilişsel hizmetler 'in kimliğini doğrulamak için Anahtar Kasanızda gizli dizi başvurusunda bulunmak gerekir. Aşağıdaki girişler, bu noktadan önce tamamlanması gereken [Önkoşul adımlarına](tutorial-configure-cognitive-services-synapse.md) bağımlıdır.

- **Azure aboneliği**: anahtar kasanızın ait olduğu Azure aboneliğini seçin.
- Bilişsel **Hizmetler hesabı**: bağlanacağı metin analizi kaynağını girin.
- **Bağlı hizmet Azure Key Vault**: önkoşul adımlarının bir parçası olarak, metin analizi kaynağınız için bağlı bir hizmet oluşturdunuz. Buradan seçin.
- **Gizli dizi adı**: bilişsel hizmetler kaynağınız için kimlik doğrulaması yapmak üzere anahtarı içeren anahtar kasanızda gizli dizi adını girin.

![Bir Anahtar Kasası için kimlik doğrulama ayrıntılarını gösteren ekran görüntüsü.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00d.png)

## <a name="configure-anomaly-detector"></a>Anomali algılayıcısı 'nı yapılandırma

Anomali algılayıcısı 'nı yapılandırmak için aşağıdaki ayrıntıları sağlayın:

- **Ayrıntı düzeyi**: verilerinizin örneklendiği hız. **Aylık** seçeneğini belirleyin. 

- **Zaman damgası sütunu**: serinin saatini temsil eden sütun. **Zaman damgası (dize)** seçin.

- **Timeseries değer sütunu**: zaman damgası sütunu tarafından belirtilen zamanda serinin değerini temsil eden sütun. **Değer (Double)** seçeneğini belirleyin.

- **Gruplandırma sütunu**: seriyi gruplandıran sütun. Diğer bir deyişle, bu sütunda aynı değere sahip tüm satırlar bir zaman serisi oluşturmalıdır. **Grup (dize)** öğesini seçin.

İşiniz bittiğinde, **Not defteri 'Ni aç**' ı seçin. Bu işlem, Azure bilişsel hizmetler 'i kullanarak, anormallikleri tespit etmek için bir not defteri oluşturur.

![Anomali algılayıcısı için yapılandırma ayrıntılarını gösteren ekran görüntüsü.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00e.png)

## <a name="run-the-notebook"></a>Not defterini çalıştırma

Az önce açtığınız Not defteri, bilişsel hizmetlere bağlanmak için [mmlspark kitaplığını](https://github.com/Azure/mmlspark) kullanır. Belirttiğiniz Azure Key Vault ayrıntıları, bu deneyimdeki gizli dizilerinize onları göstermeden güvenli bir şekilde başvurmanızı sağlar.

Şimdi anomali algılama işlemini gerçekleştirmek için tüm hücreleri çalıştırabilirsiniz. **Tümünü Çalıştır**' ı seçin. Bilişsel [Hizmetler 'de anomali algılayıcısı hakkında daha fazla bilgi edinin](../../cognitive-services/anomaly-detector/index.yml).

![Anomali algılamayı gösteren ekran görüntüsü.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00f.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: Azure bilişsel hizmetler ile yaklaşım Analizi](tutorial-cognitive-services-sentiment.md)
- [Öğretici: Azure SYNAPSE adanmış SQL havuzlarında makine öğrenimi modeli Puanlama](tutorial-sql-pool-model-scoring-wizard.md)
- [Azure Synapse Analytics'te Makine Öğrenmesi özellikleri](what-is-machine-learning.md)
