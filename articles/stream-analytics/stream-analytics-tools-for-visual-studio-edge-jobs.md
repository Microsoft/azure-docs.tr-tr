---
title: Visual Studio 'da Azure Stream Analytics Edge işleri
description: Bu makalede, Visual Studio Stream Analytics araçlarını kullanarak IoT Edge işlerinde Stream Analytics yazma, hata ayıklama ve oluşturma işlemleri açıklanmaktadır.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: ec4a4041378ce94ae70ba7a88b3fef80f7dcd193
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72925018"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Visual Studio araçlarını kullanarak Stream Analytics Edge işleri geliştirme

Bu öğreticide, Visual Studio için Stream Analytics araçlarını kullanmayı öğreneceksiniz. Stream Analytics Edge işlerinizi yazma, hata ayıklama ve oluşturma hakkında bilgi edineceksiniz. İşi oluşturup test ettikten sonra, cihazlarınıza dağıtmak için Azure portal gidebilirsiniz. 

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki önkoşullara sahip olmanız gerekir:

* [Visual studio 2019](https://visualstudio.microsoft.com/downloads/), [Visual studio 2015](https://www.visualstudio.com/vs/older-downloads/)veya [Visual Studio 2013 güncelleştirme 4](https://www.microsoft.com/download/details.aspx?id=45326)'i yükler. Enterprise (Ultimate/Premium), Professional ve Community sürümleri desteklenir. Express sürümü desteklenmiyor.  

* Visual Studio için Stream Analytics araçları 'nı yüklemek üzere [yükleme yönergelerini](stream-analytics-tools-for-visual-studio-edge-jobs.md) izleyin.
 
## <a name="create-a-stream-analytics-edge-project"></a>Stream Analytics Edge projesi oluşturma 

Visual Studio 'dan **dosya** > **Yeni** > **Proje**' yi seçin. Sol taraftaki **Şablonlar** listesine gidin > **Azure Stream Analytics** > **Stream Analytics Edge** > **Azure Stream Analytics Edge uygulaması**' nı genişletin. Projeniz için bir ad, konum ve çözüm adı girip **Tamam**' ı seçin.

![Visual Studio 'da yeni Stream Analytics Edge projesi](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

Proje oluşturulduktan sonra, klasör hiyerarşisini görüntülemek için **Çözüm Gezgini** gidin.

![Stream Analytics Edge işinin Çözüm Gezgini görünümü](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Doğru aboneliği seçin

1. Visual Studio **Görünüm** menüsünden **Sunucu Gezgini**' yi seçin.  

2. **Azure** 'a sağ tıklayın > **Microsoft Azure abonelik > Bağlan** ' ı seçin ve ardından Azure hesabınızla oturum açın.

## <a name="define-inputs"></a>Girişleri tanımlama

1. **Çözüm Gezgini**, **giriş düğümünü genişletin,** **edgeınput. JSON**adlı bir girdi görmeniz gerekir. Ayarlarını görüntülemek için çift tıklayın.  

2. Kaynak türünü **veri akışı**olarak ayarlayın. Ardından, kaynağı **Edge hub**, olay serileştirme biçimi olarak ayarlayın ve **UTF8**olarak **kodlama yapın.** İsteğe bağlı olarak, **giriş diğer adını**yeniden adlandırabilir, bu örnekte olduğu gibi bırakım. Giriş diğer adını yeniden adlandırmanız durumunda, sorguyu tanımlarken belirttiğiniz adı kullanın. Ayarları kaydetmek için **Kaydet**’i seçin.  
   ![Stream Analytics iş girişi yapılandırmasını](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Çıktıları tanımlama

1. **Çözüm Gezgini**, **çıktılar** düğümünü genişletin, **edgeoutput. JSON**adlı bir çıktı görmeniz gerekir. Ayarlarını görüntülemek için çift tıklayın.  

2. Havuz 'u **Edge hub**'ı seçmek, olay serileştirme biçimini **JSON**olarak ayarlamak, kodlamayı **UTF8**olarak ayarlamak ve biçim **dizisi**ayarlamak için ayarladığınızdan emin olun. İsteğe bağlı olarak, **çıktı diğer adını**yeniden adlandırabilir, bu örnekte olduğu gibi bırakım. Çıktı diğer adını yeniden adlandırmanız durumunda, sorguyu tanımlarken belirttiğiniz adı kullanın. Ayarları kaydetmek için **Kaydet**’i seçin. 
   ![Stream Analytics iş çıkışı yapılandırması](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Dönüşüm sorgusunu tanımlama

Stream Analytics IoT Edge ortamlarında dağıtılan Stream Analytics işleri [Stream Analytics sorgu dili başvurusunun](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396)çoğunu destekler. Ancak, aşağıdaki işlemler Stream Analytics Edge işleri için henüz desteklenmemektedir: 


|**Kategori**  | **Komut**  |
|---------|---------|
|Diğer işleçler | <ul><li>BÖLÜM ÖLÇÜTÜ</li><li>ÜZERINDEN ZAMAN DAMGASı</li><li>JavaScript UDF</li><li>Kullanıcı tanımlı toplamalar (UDA)</li><li>GetMetadataPropertyValue</li><li>Tek adımda 14 ' ten fazla toplamalar kullanma</li></ul>   |

Portalda bir Stream Analytics Edge işi oluşturduğunuzda, desteklenen bir işleç kullanmıyorsanız derleyici sizi otomatik olarak uyarır.

Visual Studio 'dan aşağıdaki dönüştürme sorgusunu sorgu Düzenleyicisi 'nde (**Script. aşama QL dosyası**) tanımlayın

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>İşi yerel olarak test etme

Sorguyu yerel olarak test etmek için örnek verileri karşıya yüklemeniz gerekir. [GitHub deposundan](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) kayıt verilerini indirerek örnek verileri alabilir ve yerel bilgisayarınıza kaydedebilirsiniz. 

1. Örnek verileri karşıya yüklemek için **Edgeınput. JSON** dosyasına sağ tıklayın ve **yerel giriş Ekle** ' yi seçin.  

2. Açılır pencerede > yerel yolunuzda örnek verilere **gözatıp** > **Kaydet**' i seçin.
   Visual Studio 'da yerel giriş yapılandırması ![](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. **Local_EdgeInput. JSON** adlı bir dosya, giriş klasörünüze otomatik olarak eklenir.  
4. Yerel olarak çalıştırabilir veya Azure 'a gönderebilirsiniz. Sorguyu test etmek için **yerel olarak çalıştır**' ı seçin.  
   ![Visual Studio 'da Stream Analytics iş çalıştırma seçenekleri](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. Komut istemi penceresinde işin durumu gösterilir. İş başarıyla çalıştırıldığında, "Visual Studio 2015 \ Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42" Proje klasörü yolunda "2018-02-23-11-31-42" gibi görünen bir klasör oluşturur. Yerel klasördeki sonuçları görüntülemek için klasör yoluna gidin:

   Ayrıca Azure portal oturum açabilir ve işin oluşturulduğunu doğrulayabilirsiniz. 

   ![Stream Analytics iş sonucu klasörü](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>İşi Azure 'a gönderme

1. İşi Azure 'a göndermeden önce Azure aboneliğinize bağlanmanız gerekir. **Sunucu Gezgini** açın > **Azure** 'a sağ tıklayın > **Microsoft Azure aboneliğe bağlanın** > Azure aboneliğinizde oturum açın.  

2. İşi Azure 'a göndermek için sorgu Düzenleyicisi ' ne gidin > **Azure 'A gönder**' i seçin.  

3. Açılır pencere açılır. Mevcut bir Stream Analytics Edge işini güncelleştirmeyi veya yenisini oluşturmayı seçin. Var olan bir işi güncelleştirdiğinizde, bu senaryoda tüm iş yapılandırması değiştirilir, yeni bir iş yayımlayacaksınız. **Yeni Azure Stream Analytics Işi oluştur** ' u seçin > **MyASAEdgeJob** gibi bir ad girin > gerekli **aboneliği**, **kaynak grubunu**ve **konumu** seçin > **Gönder**' i seçin.

   ![Visual Studio 'dan Azure 'a Stream Analytics işi gönderme](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   Artık Stream Analytics Edge işiniz oluşturulmuştur. Cihazları cihazlarınıza dağıtmayı öğrenmek için [IoT Edge öğreticideki Işleri Çalıştır '](stream-analytics-edge.md) a bakabilirsiniz. 

## <a name="manage-the-job"></a>İşi yönetme 

İşin durumunu ve iş diyagramını Sunucu Gezgini görüntüleyebilirsiniz. Sunucu Gezgini **Stream Analytics** ,Stream Analytics Edge işini dağıttığınız aboneliği ve kaynak grubunu genişletin. **Oluşturulan**durum ile MyASAEdgejob görüntüleyebilirsiniz. İş düğümünü genişletin ve iş görünümünü açmak için çift tıklayın.

![Sunucu Gezgini iş yönetimi seçenekleri](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
İş görünümü penceresi, işi yenileme, işi silme ve işi Azure portal açma gibi işlemler sağlar.

![Visual Studio 'da iş diyagramı ve diğer seçenekler](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Sonraki adımlar

* [Azure IoT Edge hakkında daha fazla bilgi](../iot-edge/about-iot-edge.md)
* [IoT Edge öğreticide ASA](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Bu anketi kullanarak ekibe geri bildirim gönderin](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
