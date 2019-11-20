---
title: 'Öğretici: tasarımcı ile otomobil fiyatlarını tahmin etme'
titleSuffix: Azure Machine Learning
description: Bir sürükle ve bırak arabirimi kullanarak makine öğrenimi modelini eğitme, Puanlama ve dağıtma hakkında bilgi edinin. Bu öğretici, doğrusal regresyon kullanarak otomobil fiyatlarını tahmin etmeye yönelik iki bölümlü bir serinin bir parçasıdır.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 0ffe85b6e005d2dc8fe077a5a08d8b0f11c73589
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929637"
---
# <a name="tutorial-predict-automobile-price-with-the-designer-preview"></a>Öğretici: tasarımcı ile otomobil fiyatını tahmin etme (Önizleme)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Bu iki bölümden oluşan öğreticide, her bir otomobil fiyatını tahmin eden tahmine dayalı bir analiz çözümü geliştirmek ve dağıtmak için Azure Machine Learning tasarımcısını nasıl kullanacağınızı öğreneceksiniz. 

Birinci bölümde, ortamınızı ayarlayın, modülleri etkileşimli bir tuvale sürükleyin ve bir Azure Machine Learning işlem hattı oluşturmak için bunları birbirine bağlayın.

Öğreticinin birinci bölümünde şunları yapmayı öğreneceksiniz:

> [!div class="checklist"]
> * Yeni bir işlem hattı oluşturun.
> * Verileri içeri aktarın.
> * Verileri hazırlayın.
> * Makine öğrenimi modelini eğitme.
> * Machine Learning modelini değerlendirin.

Öğreticinin [ikinci bölümünde](tutorial-designer-automobile-price-deploy.md) , size gönderilen teknik belirtimlere göre herhangi bir arabasının fiyatını tahmin etmek için tahmine dayalı modelinizi gerçek zamanlı bir zaman aşımı noktası olarak dağıtmayı öğreneceksiniz. 

> [!NOTE]
>Bu öğreticinin tamamlanmış sürümü örnek bir işlem hattı olarak sunulmaktadır.
>
>Bulmak için çalışma alanınızdaki tasarımcıya gidin. Yeni işlem **hattı** bölümünde **Örnek 1-gerileme: otomobil fiyat tahmini (temel)** öğesini seçin.

## <a name="create-a-new-pipeline"></a>Yeni işlem hattı oluşturma

Azure Machine Learning işlem hatları, birden çok, bağımlı makine öğrenimi ve veri işleme adımlarını tek bir kaynakta düzenler. İşlem hatları, projeler ve kullanıcılar arasında karmaşık makine öğrenimi iş akışlarını düzenlemenize, yönetmenize ve yeniden kullanmanıza yardımcı olur. Azure Machine Learning bir işlem hattı oluşturmak için bir Azure Machine Learning çalışma alanına ihtiyacınız vardır. Bu bölümde, bu kaynakların her ikisini de oluşturmayı öğreneceksiniz.

### <a name="create-a-new-workspace"></a>Yeni çalışma alanı oluştur

Enterprise sürümü olan bir Azure Machine Learning çalışma alanınız varsa, [sonraki bölüme atlayın](#create-the-pipeline).

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal-enterprise.md)]

### <a name="create-the-pipeline"></a>İşlem hattını oluşturma

1. [Ml.Azure.com](https://ml.azure.com)'de oturum açın ve birlikte çalışmak istediğiniz çalışma alanını seçin.

1. **Tasarımcı**' yı seçin.

    ![Tasarımcıya nasıl erişediğinin gösterildiği görsel çalışma alanının ekran görüntüsü](./media/ui-tutorial-automobile-price-train-score/launch-visual-interface.png)

1. Kullanımı **kolay önceden oluşturulmuş modüller**' i seçin.

1. Tuvalin en üstünde **oluşturulan** varsayılan işlem hattı adı işlem hattını seçin. Bunu anlamlı bir şekilde yeniden adlandırın. Bir örnek, *otomobil fiyat tahminiyle*bir örnektir. Adın benzersiz olması gerekmez.

## <a name="import-data"></a>Veri içeri aktarma

Üzerinde denemeler yapmanız için tasarımcıya dahil edilen birkaç örnek veri kümesi vardır. Bu öğretici için, **otomobil fiyat verileri (ham)** kullanın. 

1. Ardışık düzen tuvalinin sol tarafında bir veri kümesi ve modül paleti bulunur. **Veri kümelerini**seçin ve ardından kullanılabilir örnek veri kümelerini görüntülemek için **örnekler** bölümünü görüntüleyin.

1. Veri kümesi **otomobil fiyat verileri (ham)** öğesini seçin ve tuvale sürükleyin.

   ![Verileri tuvale sürükleyin](./media/ui-tutorial-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Verileri görselleştirme

Kullanacağınız veri kümesini anlamak için verileri görselleştirebilirsiniz.

1. **Otomobil fiyat verileri (ham)** modülünü seçin.

1. Tuvalin sağındaki Özellikler bölmesinde, **çıktılar**' i seçin.

1. Verileri görselleştirmek için grafik simgesini seçin.

    ![Verileri görselleştirme](./media/ui-tutorial-automobile-price-train-score/visualize-data.png)

1. Her biri hakkındaki bilgileri görüntülemek için veri penceresinde farklı sütunları seçin.

    Her satır bir otomobil ve her bir otomobil ile ilişkili değişkenler sütun olarak görünür. Bu veri kümesinde 205 satır ve 26 sütun vardır.

## <a name="prepare-data"></a>Verileri hazırlama

Veri kümeleri, analiz etmeden önce genellikle bazı ön işleme gerektirir. Veri kümesini denetlediğinizde bazı eksik değerleri fark etmiş olabilirsiniz. Bu eksik değerler, modelin verileri doğru çözümleyebilmesi için temizlenmelidir.

### <a name="remove-a-column"></a>Sütun kaldırma

Bir modeli eğitedığınızda, eksik olan veriler hakkında bir şey yapmanız gerekir. Bu veri kümesinde, **normalleştirilmiş kayıplar** sütununda çok sayıda değer yok, bu nedenle söz konusu sütunu modelden hariç tutabilmeniz gerekir.

1. **Veri kümesindeki sütunları seç** modülünde bulmak için paletin en üstündeki arama kutusuna **seçin** yazın.

1. **Veri kümesindeki sütunları seçme** modülüne tuval üzerine sürükleyin. Modülün veri kümesi modülünün altına bırakın.

1. **Otomobil fiyat verileri (ham)** veri kümesini **veri kümesindeki sütunları seçme** modülüne bağlayın. Veri kümesinin çıkış bağlantı noktasından, tuvaldeki veri kümesinin en altında bulunan küçük daire, modülün en üstünde bulunan küçük bir daire olan **veri kümesindeki sütun seçme**giriş bağlantı noktasına sürükleyin.

    > [!TIP]
    > Bir modülün çıkış bağlantı noktasını başka bir giriş bağlantı noktasına bağladığınızda işlem hattınızla veri akışı oluşturursunuz.
    >

    ![Modülleri bağlama](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. **Veri kümesi modülünde sütunları seç '** i seçin.

1. Tuvalin sağ tarafındaki Özellikler bölmesinde **parametreler** > **sütunu Düzenle**' yi seçin.

1. Yeni bir kural eklemek için **+** seçin.

1. Açılan menüden **Dışla** ve **sütun adlarını**seçin.
    
1. Metin kutusuna *normalleştirilmiş zararlar* girin.

1. Sütun seçiciyi kapatmak için sağ alt köşedeki **Kaydet** ' i seçin.

    ![Sütun dışlama](./media/ui-tutorial-automobile-price-train-score/exclude-column.png)
        
    Özellikler bölmesi, **normalleştirilmiş kayıplar** sütununun dışlanacağını gösterir.

1. **Veri kümesi modülünde sütunları seç '** i seçin. 

1. Özellikler bölmesinde **parametreler** > **Açıklama** ' yı seçin ve *normalleştirilmiş zararları hariç tut*' u girin.

### <a name="clean-missing-data"></a>Eksik verileri temizle

**Normalleştirilmiş zararlar** sütununu kaldırdıktan sonra Veri kümeniz hala eksik değerler içeriyor. **Eksik verileri temizle** modülünü kullanarak kalan eksik verileri kaldırabilirsiniz.

> [!TIP]
> Giriş verilerinden eksik değerleri Temizleme, tasarımcıda birçok modülün kullanılmasına yönelik bir önkoşuldur.

1. **Eksik verileri temizle** modülünü bulmak için arama kutusuna **temiz** yazın.

1. **Eksik verileri temizle** modülünü ardışık düzen tuvaline sürükleyin. **Veri kümesi modülündeki sütunları seçme** modülüne bağlayın. 

1. Özellikler bölmesinde, **temizleme modu**' nun altındaki **tüm satırı Kaldır** ' ı seçin.

1. Özellikler bölmesi **Açıklama** kutusunda *eksik değer satırlarını kaldır*' ı girin. 

    İşlem hatlarınız şuna benzer şekilde görünmelidir:
    
    ![Select-Column](./media/ui-tutorial-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Makine öğrenimi modelini eğitme

Artık veriler işlense de, tahmine dayalı bir model eğitebilirsiniz.

### <a name="select-an-algorithm"></a>Algoritmayı seçme

*Sınıflandırma* ve *regresyon*, denetimli makine öğrenimi algoritmasının iki türüdür. Sınıflandırma, kırmızı, mavi veya yeşil gibi bir renk gibi tanımlı kategori kümesinden bir yanıtı tahmin eder. Bir sayıyı tahmin etmek için regresyon kullanılır.

Bir sayı olan fiyatı tahmin etmek istediğiniz için, regresyon algoritmasını kullanabilirsiniz. Bu örnekte, doğrusal regresyon modeli kullanırsınız.

### <a name="split-the-data"></a>Verileri bölme

Modeli eğitmek ve test etmek için verilerinizi iki ayrı veri kümesine ayırın.

1. **Bölünmüş veri** modülünü bulmak için arama kutusuna **bölünmüş verileri** girin. **Temizleme eksik veri** modülünün sol bağlantı noktasına bağlayın.

1. **Bölünmüş veri** modülünü seçin.

1. Özellikler bölmesinde, **ilk çıktı veri kümesindeki satır kesirini** 0,7 olarak ayarlayın.

    Bu seçenek, modeli eğitmek için verilerin yüzde 70 ' unu ve test için yüzde 30 ' unu böler.

1. Özellikler bölmesi **Açıklama** kutusunda, *veri kümesini eğitim kümesi (0,7) ve test kümesi (0,3) olarak Böl*yazın.

### <a name="train-the-model"></a>Modeli eğitme

Fiyatı içeren bir veri kümesi vererek modeli eğitme. Model, verileri tarar ve bir araba oluşturmak için bir otomobil özellikleri ile fiyatı arasında bağıntıları arar.

1. Öğrenme algoritmasını seçmek için modül paleti arama kutusunu temizleyin.

1. **Machine Learning algoritmaları**genişletin.
    
    Bu seçenek, öğrenme algoritmalarını başlatmak için kullanabileceğiniz birkaç modül kategorisini görüntüler.

1. **Doğrusal regresyon** > **gerileme** ' yı seçin ve bunu işlem hattı tuvaline sürükleyin.

1. **Eğitim modeli** modülünü bulup ardışık düzen tuvaline sürükleyin. 

1. **Doğrusal regresyon** modülünün çıkışını **eğitme modeli** modülünün sol girişine bağlayın.

1. **Veri ayırma** modülünün eğitim verileri çıkışını (sol bağlantı noktası), **eğitme modeli** modülünün sağ girişine bağlayın.

    ![Eğitim modeli modülünün doğru yapılandırmasını gösteren ekran görüntüsü. Doğrusal regresyon modülü, model eğitimi modülünün sol bağlantı noktasına bağlanır ve bölünmüş veri modülü, tren modelinin sağ bağlantı noktasına bağlanır](./media/ui-tutorial-automobile-price-train-score/pipeline-train-model.png)

1. **Model eğitme** modülünü seçin.

1. Özellikler bölmesinde, **sütun seçiciyi Düzenle** ' yi seçin.

1. **Etiket sütunu** iletişim kutusunda, açılan menüyü genişletin ve **sütun adları**' nı seçin. 

1. Metin kutusuna *Price*yazın. Fiyat, modelinizin tahmin edilecek değerdir.

    İşlem hatlarınız şöyle görünmelidir:

    ![Eğitim modeli modülü eklendikten sonra işlem hattının doğru yapılandırmasını gösteren ekran görüntüsü.](./media/ui-tutorial-automobile-price-train-score/pipeline-train-graph.png)

## <a name="evaluate-a-machine-learning-model"></a>Machine Learning modelini değerlendirme

Modelinize verilerin yüzde 70 'ini kullanarak eğdikten sonra, modelinizin ne kadar iyi olduğunu görmek için bu değeri, diğer 30 ' a puan almak üzere kullanabilirsiniz.

1. **Puan modeli** modülünü bulmak için arama kutusuna *puan modeli* girin. Modülü işlem hattı tuvaline sürükleyin. 

1. **Eğitim modeli** modülünün çıkışını, **puan modelinin**sol giriş bağlantı noktasına bağlayın. **Veri ayırma** modülünün test verileri çıkışını (sağ bağlantı noktası), **puan modelinin**sağ giriş bağlantı noktasına bağlayın.

1. **Modeli değerlendir** modülünü bulmak için arama kutusuna *değerlendir* yazın. Modülü işlem hattı tuvaline sürükleyin. 

1. **Puan modeli** modülünün çıkışını **modeli değerlendir**modülünün sol girişine bağlayın. 

    Son işlem hattı şuna benzer görünmelidir:

    ![İşlem hattının doğru yapılandırmasını gösteren ekran görüntüsü.](./media/ui-tutorial-automobile-price-train-score/pipeline-final-graph.png)

### <a name="run-the-pipeline"></a>İşlem hattını çalıştırma

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

### <a name="view-results"></a>Sonuçları görüntüleme

Çalıştırma tamamlandıktan sonra, işlem hattı çalıştırmasının sonuçlarını görüntüleyebilirsiniz. 

1. Çıktısını görüntülemek için **puan modeli** modülünü seçin.

1. Özellikler bölmesinde, **çıktılar** > **görselleştirin**' ı seçin.

    Burada, tahmin edilen fiyatları ve test verilerinin gerçek fiyatlarını görebilirsiniz.

    ![Puanlanmış etiket sütununu vurgulayan çıkış görselleştirmesinin ekran görüntüsü](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Çıktısını görüntülemek için **modeli değerlendir** modülünü seçin.

1. Özellikler bölmesinde **çıkış** > **Görselleştir**' i seçin.

Modeliniz için aşağıdaki istatistikler gösterilmektedir:

* Ortalama **mutlak hata (MAE)** : mutlak hataların ortalaması. Bir hata, tahmin edilen değer ve gerçek değer arasındaki farktır.
* **Kök ortalama kare hatası (rmo)** : test veri kümesinde yapılan tahmine dayalı ortalama kare şeklindeki hata sayısının kare kökü.
* **Relative Absolute Error (Göreli Mutlak Hata)** : Gerçek değerler ve tüm gerçek değerlerin ortalaması arasındaki mutlak hataların mutlak farka göreli ortalaması.
* **Relative Squared Error (Göreli Karesi Alınmış Hata)** : Gerçek değerler ve tüm gerçek değerlerin ortalaması arasındaki karesi alınmış hataların karesi alınmış farka göreli ortalaması.
* **Belirleme katsayısı**: R kare değeri olarak da bilinen bu istatistiksel ölçüm, modelin verilere ne kadar iyi uyduğunu gösterir.

Her bir hata istatistiği ne kadar küçük olursa o kadar iyidir. Daha küçük bir değer, tahminlerinin gerçek değerlere yakın olduğunu gösterir. Belirleme katsayısı için, değeri bir (1,0), daha iyi tahmine dayalı olur.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticinin bir parçası olarak, aşağıdaki görevleri tamamladınız:

* İşlem hattı oluşturma
* Verileri hazırlama
* Modeli eğitme
* Modeli Puanlama ve değerlendirme

İkinci bölümde, modelinizi gerçek zamanlı bir uç nokta olarak dağıtmayı öğreneceksiniz.

> [!div class="nextstepaction"]
> [Modelleri dağıtmaya devam et](tutorial-designer-automobile-price-deploy.md)
