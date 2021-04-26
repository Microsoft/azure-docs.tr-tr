---
title: 'ML Studio (klasik): Azure Machine Learning geçir'
description: Modernlanmış bir veri bilimi platformu için Studio 'dan (klasik) Azure Machine Learning 'e geçiş yapın.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: fafb9f7e84263c33cf1b15a52742ec9cd0380a40
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311864"
---
# <a name="migrate-to-azure-machine-learning"></a>Azure Machine Learning’e geçiş

Bu makalede, Studio (klasik) varlıklarını Azure Machine Learning 'a geçirmeyi öğreneceksiniz. Şu anda, kaynakları geçirmek için denemeleri 'nizi el ile yeniden oluşturmanız gerekir.

Azure Machine Learning, kod ve kod ilk yaklaşımlarını birleştiren modernlanmış bir veri bilimi platformu sağlar. Studio (klasik) ve Azure Machine Learning arasındaki farklar hakkında daha fazla bilgi edinmek için bkz. [değerlendirme Azure Machine Learning](#step-1-assess-azure-machine-learning) bölümü.


## <a name="recommended-approach"></a>Önerilen yaklaşım

Azure Machine Learning geçiş yapmak için aşağıdaki yaklaşımı öneririz:

> [!div class="checklist"]
> * 1. Adım: değerlendirme Azure Machine Learning
> * 2. Adım: geçiş planı oluşturma
> * 3. Adım: denemeleri ve Web hizmetlerini yeniden derleme
> * 4. Adım: istemci uygulamalarını tümleştirme
> * 5. Adım: Studio (klasik) varlıklarını Temizleme


## <a name="step-1-assess-azure-machine-learning"></a>1. Adım: değerlendirme Azure Machine Learning
1. [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)hakkında bilgi edinin; avantaj, maliyetler ve mimaridir.

1. Azure Machine Learning ve Studio yeteneklerini karşılaştırın (klasik).

    >[!NOTE]
    > Azure Machine Learning **Tasarımcı** özelliği, Studio 'ya benzer bir sürükle ve bırak deneyimi sağlar (klasik). Ancak Azure Machine Learning Ayrıca, güçlü [kod ilk iş akışlarını](../concept-model-management-and-deployment.md) alternatif olarak sağlar. Bu geçiş serisi, en çok Studio (klasik) deneyimine benzer olduğundan tasarımcı 'ya odaklanır.

    [!INCLUDE [aml-compare-classic](../../../includes/machine-learning-compare-classic-aml.md)]

3. Kritik Studio (klasik) modüllerinizin Azure Machine Learning tasarımcısında desteklendiğinden emin olun. Daha fazla bilgi için aşağıdaki [Studio (klasik) ve tasarımcı modülü eşleme](#studio-classic-and-designer-module-mapping) tablosuna bakın.

4. [Azure Machine Learning çalışma alanı oluşturun](../how-to-manage-workspace.md?tabs=azure-portal).

## <a name="step-2-create-a-migration-plan"></a>2. Adım: geçiş planı oluşturma

1. Geçirmek istediğiniz Studio (klasik) **veri kümelerini**, **modellerini** ve **Web hizmetlerini** tanımlar.

1. Bir geçişin işinizde sahip olacağı etkiyi belirleme.

1. Bir geçiş planı oluşturun.

## <a name="step-3-rebuild-experiments-and-web-services"></a>3. Adım: denemeleri ve Web hizmetlerini yeniden derleme

1. [Veri kümelerini Azure Machine Learning geçirin](migrate-register-dataset.md).
1. [Denemeleri yeniden derlemek](migrate-rebuild-experiment.md)için tasarımcıyı kullanın.
1. [Web hizmetlerini yeniden dağıtmak](migrate-rebuild-web-service.md)için tasarımcıyı kullanın.

    >[!NOTE]
    > Azure Machine Learning Ayrıca, [veri kümeleri](../how-to-create-register-datasets.md), [eğitim](../how-to-set-up-training-targets.md)ve [dağıtım](../how-to-deploy-and-where.md)için kod ilk iş akışlarını destekler.

## <a name="step-4-integrate-client-apps"></a>4. Adım: istemci uygulamalarını tümleştirme

1. Yeni [Azure Machine Learning uç noktalarınızı](migrate-rebuild-integrate-with-client-app.md)kullanmak için Studio (klasik) Web hizmetlerini çağıran istemci uygulamalarını değiştirin.

## <a name="step-5-cleanup-studio-classic-assets"></a>5. Adım: Studio (klasik) varlıklarını Temizleme

1. Ek [ücretlerden kaçınmak Için Studio (klasik) varlıklarını temizleyin](export-delete-personal-data-dsr.md) . Azure Machine Learning iş yüklerini doğrulama yapana kadar varlıkları geri dönüş için tutmanız isteyebilirsiniz.

## <a name="studio-classic-and-designer-module-mapping"></a>Studio (klasik) ve tasarımcı modülü eşleme

Tasarımcıda Studio (klasik) denemeleri yeniden oluşturulurken hangi modüllerin kullanılacağını görmek için aşağıdaki tabloya başvurun.


> [!IMPORTANT]
> Tasarımcı, modülleri Studio (klasik) gibi C# paketleri yerine açık kaynaklı Python paketleri aracılığıyla uygular. Bu fark nedeniyle, tasarımcı modüllerinin çıkışı, Studio (klasik) karşılıklarından biraz farklı olabilir.


|Kategori|Studio (klasik) modülü|Değiştirme Tasarımcısı modülü|
|--------------|----------------|--------------------------------------|
|Veri girişi ve çıkışı|-Verileri el Ile girin </br> -Verileri dışarı aktar </br> -Verileri içeri aktar </br> -Eğitilen model yükle </br> -Sıkıştırılmış veri kümelerini açma|-Verileri el Ile girin </br> -Verileri dışarı aktar </br> -Verileri içeri aktar|
|Veri biçimi dönüştürmeleri|-CSV 'ye Dönüştür </br> -Veri kümesine Dönüştür </br> -ARFF 'e Dönüştür </br> -SVMLight 'ye Dönüştür </br> -TSV 'e Dönüştür|-CSV 'ye Dönüştür </br> -Veri kümesine Dönüştür|
|Veri dönüştürme-düzenleme|-Sütun Ekle</br> -Satır Ekle </br> -SQL dönüşümünü Uygula </br> -Eksik verileri temizleme </br> -Gösterge değerlerine Dönüştür </br> -Meta verileri Düzenle </br> -Verileri birleştirin </br> -Yinelenen satırları kaldır </br> -Veri kümesindeki sütunları seçme </br> -Sütun dönüştürmeyi Seç </br> -SMOTE </br> -Categorik değerlerini gruplandırma|-Sütun Ekle</br> -Satır Ekle </br> -SQL dönüşümünü Uygula </br> -Eksik verileri temizleme </br> -Gösterge değerlerine Dönüştür </br> -Meta verileri Düzenle </br> -Verileri birleştirin </br> -Yinelenen satırları kaldır </br> -Veri kümesindeki sütunları seçme </br> -Sütun dönüştürmeyi Seç </br> -SMOTE|
|Veri dönüştürme – ölçek ve azaltma |-Klip değerleri </br> -Verileri depo gözlerine gruplandırma </br> -Verileri Normalleştir </br>-Sorumlu bileşen analizi |-Klip değerleri </br> -Verileri depo gözlerine gruplandırma </br> -Verileri Normalleştir|
|Veri dönüştürme – örnek ve bölünmüş|-Bölüm ve örnek </br> -Verileri Böl|-Bölüm ve örnek </br> -Verileri Böl|
|Veri dönüştürme – filtre |-Filtre Uygula </br> -KÖKNAR filtresi </br> -IıR filtresi </br> -Ortanca filtresi </br> -Hareketli ortalama filtre </br> -Eşik Filtresi </br> -Kullanıcı tanımlı filtre||
|Veri dönüştürme – sayımlar ile öğrenme |-Derleme sayım dönüşümü </br> -Aktarım sayısı tablosu </br> -Alma sayısı tablosu </br> -Birleştirme sayısı dönüşümü</br>  -Sayım tablosu parametrelerini değiştir||
|Özellik seçimi |-Filtre tabanlı özellik seçimi </br> -Fisher doğrusal ayrılmış Minant Analizi  </br> -Permütasyon özelliği önem derecesi |-Filtre tabanlı özellik seçimi </br>  -Permütasyon özelliği önem derecesi|
| Model-sınıflandırma| -Birden çok Lass karar ormanı </br> -Birden çok Lass karar Jungle  </br> -Birden çok Lass Lojistik gerileme  </br>-Birden çok Lass sinir ağı  </br>-Bire Karşı Hepsi Çoklu Sınıf </br>-Two-Class ortalama Perceptron </br>-Two-Class Bayes noktası makinesi </br>-Two-Class, artırılmış karar ağacı  </br> -Two-Class karar ormanı  </br> -Two-Class karar Jungle  </br> -Two-Class Locally-Deep SVM </br> -Two-Class Lojistik gerileme  </br> -Two-Class sinir ağı </br> -Two-Class destek vektör makinesi  | -Birden çok Lass karar ormanı </br>  -Birden çok Lass Boost karar ağacı  </br> -Birden çok Lass Lojistik gerileme </br> -Birden çok Lass sinir ağı </br> -Bire Karşı Hepsi Çoklu Sınıf  </br> -Two-Class ortalama Perceptron  </br> -Two-Class, artırılmış karar ağacı  </br> -Two-Class karar ormanı </br>-Two-Class Lojistik gerileme </br> -Two-Class sinir ağı </br>-Two-Class destek vektör makinesi  |
| Model kümesi oluşturma| -K-kümelemenin anlamı| -K-kümelemenin anlamı|
| Model-gerileme| -Bayeduyi doğrusal regresyon  </br> -Artırılmış karar ağacı gerileme  </br>-Karar ormanı gerileme  </br> -Hızlı orman Quantile gerileme  </br> -Doğrusal regresyon </br> -Sinir ağ gerileme </br> -Sıralı regresyon Poisson gerilemesi| -Artırılmış karar ağacı gerileme  </br>-Karar ormanı gerileme  </br> -Hızlı orman Quantile gerileme </br> -Doğrusal regresyon  </br> -Sinir ağ gerileme </br> -Poisson regresyon|
| Model – anomali algılama| -One-Class SVM  </br> -PCA-Based anomali algılama | -PCA-Based anomali algılama|
| Machine Learning – değerlendir  | -Çapraz doğrulama modeli  </br>-Modeli değerlendir  </br>-Öneren değerlendir | -Çapraz doğrulama modeli  </br>-Modeli değerlendir </br> -Öneren değerlendir|
| Machine Learning – eğitme| -Tarama Kümelemesi  </br> -Anomali algılama modelini eğitme </br>-Kümeleme modelini eğitme  </br> -Tren Matchbox öneren-</br> Model Eğitme  </br>-Model hiper parametrelerini ayarla| -Anomali algılama modelini eğitme  </br> -Kümeleme modelini eğitme </br> -Model eğitimi-</br> -PyTorch modelini eğitme  </br>-Eğitim SVD öneren  </br>-Geniş ve derin öneren eğitme </br>-Model hiper parametrelerini ayarla|
| Machine Learning – puan| -Dönüşümü Uygula  </br>-Kümelere veri atama  </br>-Score Matchbox öneren </br> -Puan modeli|-Dönüşümü Uygula  </br> -Kümelere veri atama </br> -Puanlama görüntü modeli  </br> -Puan modeli </br>-Skor SVD öneren </br> -Geniş ve derin öneren puanı|
| OpenCV kitaplığı modülleri| -Görüntüleri içeri aktar </br>-Önceden eğitilen basamaklı görüntü sınıflandırması | |
| Python dil modülleri| -Python betiğini yürütme| -Python betiğini yürütme  </br> -Python modeli oluşturma |
| R dil modülleri  | -R betiğini Yürüt  </br> -R modeli oluşturma| -R betiğini Yürüt|
| İstatistiksel İşlevler | -Matematik Işlemini Uygula </br>-Öğesel Istatistikleri hesaplama  </br>-İşlem doğrusal bağıntı  </br>-Olasılık Işlevini değerlendir  </br>-Ayrık değerleri Değiştir  </br>-Verileri özetleme  </br>-T-test kullanarak test varsayım| -Matematik Işlemini Uygula  </br>-Verileri özetleme|
| Metin Analizi| -Dilleri Algıla  </br>-Metinden anahtar tümceleri Ayıkla  </br>-Metinden N-gram özelliklerini Ayıkla  </br>-Özellik karması </br>-Latent Dirichlet ayırması  </br>-Adlandırılmış varlık tanıma </br>-Preprocess metni  </br>-Vowpal Wabbit sürüm 7-10 modelinin puanı  </br>-Vowpal Wabbit sürüm 8 modeli skoru </br>-Tren Vowpal Wabbit sürüm 7-10 modeli  </br>-Tren Vowpal Wabbit sürüm 8 modeli |-Kelimeyi vector öğesine Dönüştür </br> -Metinden N-gram özelliklerini Ayıkla </br>-Özellik karması  </br>-Latent Dirichlet ayırması </br>-Preprocess metni  </br>-Vowpal Wabbit modeli puanı </br> -Vowpal Wabbit modelini eğitme|
| Zaman serisi| -Zaman serisi anomali algılama | |
| Web Hizmeti | -Giriş </br> -Çıkış | -Giriş </br>  - Çıktı|
| Görüntü İşleme| | -Görüntü dönüşümünü Uygula </br> -Görüntü dizinine Dönüştür </br> -Init Image dönüşümü </br> -Görüntü dizinini Böl  </br> -DenseNet resim sınıflandırması   </br>-ResNet görüntü sınıflandırması |

Tasarımcı modüllerinin bireysel olarak nasıl kullanılacağı hakkında daha fazla bilgi için [Tasarımcı modülü başvurusuna](../algorithm-module-reference/module-reference.md)bakın.

### <a name="what-if-a-designer-module-is-missing"></a>Tasarımcı modülü eksikse ne olur?

Azure Machine Learning tasarımcı, Studio 'dan (klasik) en popüler modülleri içerir. Ayrıca, en son makine öğrenimi tekniklerinden faydalanan yeni modüller de içerir. 

Tasarımcıda eksik modüller nedeniyle geçişiniz engellenirse, [bir destek bileti oluşturarak](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)bizimle iletişim kurun.

## <a name="example-migration"></a>Örnek geçiş

Aşağıdaki deneme geçişi, Studio (klasik) ve Azure Machine Learning arasındaki farklılıkları vurgular.

### <a name="datasets"></a>Veri kümeleri

Studio 'da (klasik), **veri kümeleri** çalışma alanınıza kaydedildi ve yalnızca Studio (klasik) tarafından kullanılabilir.

![otomobil-fiyat-klasik-veri kümesi](./media/migrate-overview/studio-classic-dataset.png)

Azure Machine Learning, **veri kümeleri** çalışma alanına kaydedilir ve tüm Azure Machine Learning üzerinde kullanılabilir. Azure Machine Learning veri kümelerinin avantajları hakkında daha fazla bilgi için bkz. [güvenli veri erişimi](../concept-data.md#reference-data-in-storage-with-datasets).

![otomobil-fiyat-AML-veri kümesi](./media/migrate-overview/aml-dataset.png)

### <a name="pipeline"></a>İşlem Hattı

Studio 'da (klasik), **denemeleri** , çalışmanız için işleme mantığını içeriyordu. Sürükle ve bırak modülleriyle denemeleri oluşturdunuz.


![otomobil-fiyat-klasik-deneme](./media/migrate-overview/studio-classic-experiment.png)

Azure Machine Learning, işlem **hatları** , çalışmanız için işleme mantığını içerir. Sürükle ve bırak modülleriyle ya da kod yazarak işlem hatları oluşturabilirsiniz.

![otomobil-fiyat-AML-işlem hattı](./media/migrate-overview/aml-pipeline.png)

### <a name="web-service-endpoint"></a>Web hizmeti uç noktası

Studio 'da (klasik), **istek/yanıt API 'si** gerçek zamanlı tahmin için kullanıldı. Batch **yürütmesi API 'si** toplu tahmin veya yeniden eğitim için kullanıldı.

![otomobil-fiyat-klasik-WebService](./media/migrate-overview/studio-classic-web-service.png)

Azure Machine Learning, gerçek zamanlı tahmin için **gerçek zamanlı uç noktalar** kullanılır. İşlem **hattı uç noktaları** , toplu tahmin veya yeniden eğitim için kullanılır.

![otomobil-fiyat-AML-uç noktası](./media/migrate-overview/aml-endpoint.png)


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Machine Learning geçirmek için üst düzey gereksinimleri öğrendiniz. Ayrıntılı adımlar için bkz. Studio (klasik) geçiş serisinde diğer makaleler:

1. **Geçişe genel bakış**.
1. [Veri kümesini geçirin](migrate-register-dataset.md).
1. [Bir Studio (klasik) eğitim işlem hattını yeniden derleyin](migrate-rebuild-experiment.md).
1. [Bir Studio (klasik) Web hizmetini yeniden derleyin](migrate-rebuild-web-service.md).
1. [İstemci uygulamalarıyla bir Azure Machine Learning Web hizmetini tümleştirin](migrate-rebuild-integrate-with-client-app.md).
1. [Execute R betiğini geçirin](migrate-execute-r-script.md).