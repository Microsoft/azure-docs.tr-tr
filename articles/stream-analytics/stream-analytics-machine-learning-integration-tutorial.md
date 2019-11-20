---
title: Azure Machine Learning ile tümleştirme Azure Stream Analytics
description: Bu makalede, Kullanıcı tanımlı bir işlevi kullanarak Azure Machine Learning tümleştiren basit bir Azure Stream Analytics işinin nasıl hızlı bir şekilde ayarlanacağı açıklanır.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.custom: seodec18
ms.openlocfilehash: b078c92d02c55080cb84c386b7bbdabf3e1f85bf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467888"
---
# <a name="perform-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic-preview"></a>Azure Stream Analytics ve Azure Machine Learning Studio (klasik) ile yaklaşım analizini gerçekleştirme (Önizleme)
Bu makalede, Azure Machine Learning Studio (klasik) tümleştiren basit bir Azure Stream Analytics işinin hızlı bir şekilde nasıl ayarlanacağı açıklanır. Akış metin verilerini analiz etmek ve yaklaşım Puanını gerçek zamanlı olarak belirleyebilmek için Cortana Intelligence Gallery Machine Learning bir yaklaşım Analizi modeli kullanırsınız. Cortana Intelligence Suite kullanmak, bu görevi, bir yaklaşım analiz modeli oluşturmanın karmaşık olduğu konusunda endişelenmenize gerek kalmadan gerçekleştirmenize olanak tanır.

Bu makaleden öğrendiklerinizi, bunlar gibi senaryolar için uygulayabilirsiniz:

* Veri akışı Twitter verilerinde gerçek zamanlı yaklaşım çözümleniyor.
* Destek personeli ile müşteri sohbetleri kayıtlarını analiz etme.
* Forumlar, blogların ve videoların açıklamalarını değerlendirme. 
* Diğer birçok gerçek zamanlı, tahmine dayalı puan senaryosu.

Gerçek dünyada bir senaryoda, verileri doğrudan Twitter veri akışından alırsınız. Öğreticiyi basitleştirmek için, Akış Analizi işinin Azure Blob depolama alanındaki bir CSV dosyasından yer almasını sağlamak üzere yazılmıştır. Kendi CSV dosyanızı oluşturabilir veya aşağıdaki görüntüde gösterildiği gibi örnek bir CSV dosyası kullanabilirsiniz:

![CSV dosyasında gösterilen örnek doldurulabilir](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

Oluşturduğunuz Akış Analizi işi, blob deposundan örnek metin verileri üzerinde Kullanıcı tanımlı bir işlev (UDF) olarak, yaklaşım Analizi modelini uygular. Çıktı (yaklaşım analizinin sonucu), farklı bir CSV dosyasındaki aynı blob deposuna yazılır. 

Aşağıdaki şekilde bu yapılandırma gösterilmektedir. Belirtildiği gibi, daha gerçekçi bir senaryo için blob depolamayı Azure Event Hubs girişinden akış Twitter verileriyle değiştirebilirsiniz. Ayrıca, toplam yaklaşım için [Microsoft Power BI](https://powerbi.microsoft.com/) gerçek zamanlı görselleştirme oluşturabilirsiniz.    

![Stream Analytics Machine Learning tümleştirmeye genel bakış](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Önkoşullar
Başlamadan önce şunlara sahip olduğunuzdan emin olun:

* Etkin bir Azure aboneliği.
* İçindeki verileri içeren bir CSV dosyası. Daha önce [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv)'dan gösterilen dosyayı indirebilir veya kendi dosyanızı oluşturabilirsiniz. Bu makalede, dosyayı GitHub 'dan kullandığınız varsayılır.

Yüksek düzeyde, bu makalede gösterilen görevleri tamamlayabilmeniz için aşağıdakileri yapmanız gerekir:

1. Bir Azure depolama hesabı ve bir BLOB depolama kapsayıcısı oluşturun ve kapsayıcıya CSV biçimli bir giriş dosyası yükleyin.
3. Cortana Intelligence Gallery Azure Machine Learning Studio (klasik) çalışma alanınıza bir yaklaşım Analizi modeli ekleyin ve bu modeli Machine Learning çalışma alanında bir Web hizmeti olarak dağıtın.
5. Metin girişinin yaklaşımını öğrenmek için bu Web hizmetini bir işlev olarak çağıran bir Stream Analytics iş oluşturun.
6. Stream Analytics işini başlatın ve çıktıyı denetleyin.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Bir depolama kapsayıcısı oluşturun ve CSV giriş dosyasını karşıya yükleyin
Bu adım için, GitHub 'dan kullanılabilir olan gibi herhangi bir CSV dosyasını kullanabilirsiniz.

1. Azure portal **kaynak oluştur** > **depolama** > **depolama hesabı**' na tıklayın.

2. Bir ad sağlayın (örnekte`samldemo`). Ad yalnızca küçük harf ve rakam kullanabilir ve Azure genelinde benzersiz olmalıdır. 

3. Mevcut bir kaynak grubu belirtin ve bir konum belirtin. Konum için, bu öğreticide oluşturulan tüm kaynakların aynı konumu kullanması önerilir.

    ![depolama hesabı ayrıntılarını sağlayın](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

4. Azure portal depolama hesabını seçin. Depolama hesabı dikey penceresinde **kapsayıcılar** ' a tıklayın ve ardından BLOB depolama oluşturmak için **+&nbsp;kapsayıcı** ' ya tıklayın.

    ![Giriş için BLOB depolama kapsayıcısı oluştur](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Kapsayıcı için bir ad sağlayın (örnekteki`azuresamldemoblob`) ve **erişim türünün** **BLOB**olarak ayarlandığını doğrulayın. İşiniz bittiğinde, **Tamam**’a tıklayın.

    ![blob kapsayıcısı ayrıntılarını belirtin](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. **Kapsayıcılar** dikey penceresinde, o kapsayıcının dikey penceresini açan yeni kapsayıcıyı seçin.

7. **Karşıya Yükle**'ye tıklayın.

    ![Bir kapsayıcı için ' karşıya yükle ' düğmesi](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. **Blobu karşıya yükle** dikey penceresinde, daha önce indirdiğiniz **sampleınput. csv** dosyasını karşıya yükleyin. **BLOB türü**için **Blok Blobu** seçin ve blok boyutunu 4 MB olarak ayarlayın, bu öğretici için yeterlidir.

9. Dikey pencerenin alt kısmındaki **karşıya yükle** düğmesine tıklayın.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Cortana Intelligence Gallery yaklaşım Analytics modelini ekleme

Örnek veriler bir blob 'da olduğuna göre, Cortana Intelligence Gallery ' de yaklaşım Analizi modelini etkinleştirebilirsiniz.

1. Cortana Intelligence Gallery, tahmine [dayalı yaklaşım Analizi modeli](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) sayfasına gidin.  

2. **Studio 'Da aç**' a tıklayın.  
   
   ![Stream Analytics Machine Learning, Machine Learning Studio açın](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Çalışma alanına gitmek için oturum açın. Bir konum seçin.

4. Sayfanın alt kısmındaki **Çalıştır** ' a tıklayın. İşlem çalışır ve yaklaşık bir dakika sürer.

   ![deneme Machine Learning Studio Çalıştır](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. İşlem başarıyla çalıştırıldıktan sonra sayfanın alt kısmındaki **Web Hizmeti Dağıt** ' ı seçin.

   ![deneme Machine Learning Studio Web hizmeti olarak dağıtma](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Yaklaşım Analizi modelinin kullanıma hazırlandığını doğrulamak için, **Test** düğmesine tıklayın. "Microsoft seviyorum" gibi metin girişi sağlayın. 

   ![Machine Learning Studio test denemesi](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Test çalışırsa, aşağıdaki örneğe benzer bir sonuç görürsünüz:

   ![Machine Learning Studio test sonuçları](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Excel çalışma kitabını indirmek için **uygulamalar** sütununda **Excel 2010 veya önceki bir çalışma kitabı** bağlantısına tıklayın. Çalışma kitabı, daha sonra Stream Analytics işini ayarlamak için gerekli olan API anahtarını ve URL 'YI içerir.

    ![Stream Analytics Machine Learning, Hızlı Bakış](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Machine Learning modelini kullanan bir Stream Analytics işi oluşturma

Artık, BLOB depolama alanındaki CSV dosyasından örnek çoklu alanı içeren bir Stream Analytics iş oluşturabilirsiniz. 

### <a name="create-the-job"></a>İşi oluşturma

1. [Azure Portal](https://portal.azure.com) gidin.  

2.  > **Stream Analytics işi** **nesnelerin interneti** > **kaynak oluştur ' a** tıklayın. 

3. İşi `azure-sa-ml-demo`adlandırın, bir abonelik belirtin, var olan bir kaynak grubu belirtin veya yeni bir tane oluşturun ve işin konumunu seçin.

   ![Yeni Stream Analytics işi için ayarları belirtin](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-job-1.png)
   

### <a name="configure-the-job-input"></a>İş girişini yapılandırma
İş, daha önce karşıya yüklediğiniz CSV dosyasındaki girişini BLOB depolama alanına alır.

1. İş oluşturulduktan sonra, iş dikey penceresindeki iş **topolojisi** altında **girişler** seçeneğine tıklayın.    

2. **Giriş** dikey penceresinde, **BLOB depolama** >**akış girişi Ekle** ' ye tıklayın.

3. **BLOB depolama** dikey penceresini şu değerlerle doldurun:

   
   |Alan  |Değer  |
   |---------|---------|
   |**Giriş diğer adı** | `datainput` adı ' nı kullanın ve **aboneliğinizden blob depolamayı Seç** ' i seçin       |
   |**Depolama hesabı**  |  Daha önce oluşturduğunuz depolama hesabını seçin.  |
   |**Kapsayıcı**  | Daha önce oluşturduğunuz kapsayıcıyı seçin (`azuresamldemoblob`)        |
   |**Olay serileştirme biçimi**  |  **CSV** 'yi seçin       |

   ![Yeni Stream Analytics işi girişi ayarları](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

1. **Kaydet** düğmesine tıklayın.

### <a name="configure-the-job-output"></a>İş çıktısını yapılandırma
İş, sonuçları girdi aldığı BLOB depolama alanına gönderir. 

1. İş dikey penceresindeki iş **topolojisi** altında **çıktılar** seçeneğine tıklayın.  

2. **Çıktılar** dikey penceresinde >**BLOB depolama alanı** **Ekle** ' ye tıklayın ve ardından diğer adla `datamloutput`bir çıkış ekleyin. 

3. **BLOB depolama** dikey penceresini şu değerlerle doldurun:

   |Alan  |Değer  |
   |---------|---------|
   |**Çıkış diğer adı** | `datamloutput` adı ' nı kullanın ve **aboneliğinizden blob depolamayı Seç** ' i seçin       |
   |**Depolama hesabı**  |  Daha önce oluşturduğunuz depolama hesabını seçin.  |
   |**Kapsayıcı**  | Daha önce oluşturduğunuz kapsayıcıyı seçin (`azuresamldemoblob`)        |
   |**Olay serileştirme biçimi**  |  **CSV** 'yi seçin       |

   ![Yeni Stream Analytics iş çıktısı için ayarlar](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-output.png) 

4. **Kaydet** düğmesine tıklayın.   


### <a name="add-the-machine-learning-function"></a>Machine Learning işlevini ekleme 
Daha önce bir Web hizmetine Machine Learning modeli yayımladınız. Bu senaryoda, Akış Analizi işi çalıştırıldığında, her örnek tweet, yaklaşım analizi için girişten Web hizmetine gönderilir. Machine Learning Web hizmeti, bir yaklaşım (`positive`, `neutral`veya `negative`) ve Tweet 'in pozitif olma olasılığını döndürür. 

Öğreticinin bu bölümünde, Stream çözümleme işinde bir işlev tanımlarsınız. İşlevi, Web hizmetine bir Tweet göndermek ve yanıtı geri almak için çağrılabilir. 

1. Excel çalışma kitabında daha önce indirdiğiniz Web hizmeti URL 'SI ve API anahtarına sahip olduğunuzdan emin olun.

2. İş dikey penceresine gidin > **işlevler** > + **AzureML** > **ekleyin**

3. **Azure Machine Learning işlevi** dikey penceresini şu değerlerle doldurun:

   |Alan  |Değer  |
   |---------|---------|
   | **İşlev diğer adı** | `sentiment` adını kullanın ve size URL ve anahtar girme seçeneği sunan **Azure Machine Learning işlev ayarlarını el Ile sağla** ' yı seçin.      |
   | **URL**| Web hizmeti URL 'sini yapıştırın.|
   |**Anahtar** | API anahtarını yapıştırın. |
  
   ![Stream Analytics işine Machine Learning işlev ekleme ayarları](./media/stream-analytics-machine-learning-integration-tutorial/add-machine-learning-function.png)  
    
4. **Kaydet** düğmesine tıklayın.

### <a name="create-a-query-to-transform-the-data"></a>Verileri dönüştürmek için bir sorgu oluşturma

Stream Analytics girişi incelemek ve işlemek için bildirime dayalı SQL tabanlı bir sorgu kullanır. Bu bölümde, girişten her Tweet okuyan ve sonra yaklaşım analizini gerçekleştirmek için Machine Learning işlevini çağıran bir sorgu oluşturacaksınız. Sorgu daha sonra sonucu tanımladığınız çıktıya (BLOB depolama) gönderir.

1. İşe genel bakış dikey penceresine dönün.

2.  **Iş topolojisi**altında **sorgu** kutusuna tıklayın.

3. Aşağıdaki sorguyu girin:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM datainput  
    )  

    SELECT text, result.[Score]  
    INTO datamloutput
    FROM sentiment  
    ```    

    Sorgu, girişte bulunan her bir Tweet için yaklaşım analizini gerçekleştirmek üzere daha önce oluşturduğunuz (`sentiment`) işlevini çağırır. 

4. Sorguyu kaydetmek için **Kaydet** ' e tıklayın.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Stream Analytics işini başlatıp çıktıyı denetleyin

Artık Stream Analytics işini başlatabilirsiniz.

### <a name="start-the-job"></a>İşi başlatma
1. İşe genel bakış dikey penceresine dönün.

2. Dikey pencerenin en üstünde bulunan **Başlat** ' a tıklayın.

3. **Başlat Işinde** **özel**' i seçin ve ardından CSV dosyasını blob depolamaya yüklemeden önce bir gün önce bir gün seçin. İşiniz bittiğinde **Başlat**' a tıklayın.  


### <a name="check-the-output"></a>Çıktıyı denetleyin
1. **İzleme** kutusunda etkinlik görene kadar birkaç dakika boyunca işe çalışmasına izin verin. 

2. Blob depolamanın içeriğini incelemek için normalde kullandığınız bir aracınız varsa, `azuresamldemoblob` kapsayıcısını incelemek için bu aracı kullanın. Alternatif olarak, Azure portal aşağıdaki adımları uygulayın:

    1. Portalda `samldemo` depolama hesabını bulun ve hesap içinde `azuresamldemoblob` kapsayıcısını bulun. Kapsayıcıda iki dosya görürsünüz: örnek arası ve Stream Analytics işi tarafından oluşturulan bir CSV dosyası içeren dosya.
    2. Oluşturulan dosyaya sağ tıklayın ve ardından **İndir**' i seçin. 

   ![Blob depolamadan CSV iş çıkışını indirin](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Oluşturulan CSV dosyasını açın. Aşağıdaki örneğe benzer bir şey görürsünüz:  
   
   ![Stream Analytics Machine Learning, CSV görünümü](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Ölçümleri görüntüle
Ayrıca, işlevle ilgili ölçümleri de görüntüleyebilirsiniz Azure Machine Learning. İşle ilgili aşağıdaki ölçümler, iş dikey penceresindeki **izleme** kutusunda görüntülenir:

* **Işlev istekleri** bir Machine Learning Web hizmetine gönderilen istek sayısını gösterir.  
* **Işlev olayları** istekteki olay sayısını gösterir. Varsayılan olarak, bir Machine Learning Web hizmetine yapılan her istek en fazla 1.000 olay içerir.  


## <a name="next-steps"></a>Sonraki adımlar

* [Azure Stream Analytics giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API ve Machine Learning tümleştirin](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](https://msdn.microsoft.com/library/azure/dn835031.aspx)



