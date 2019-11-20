---
title: Yaklaşım Analizi kullanım örneği
titleSuffix: ML Studio (classic) - Azure
description: Metin analizi modellerini metin ön işleme, N-gram veya özellik karması için modüller kullanarak Azure Machine Learning Studio (klasik) oluşturma
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2018
ms.openlocfilehash: 2e5cebb05549c2bd3cd810da58930efdae1422e4
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838630"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (klasik) ' de bir yaklaşım analiz modeli oluşturma

Azure Machine Learning Studio (klasik) kullanarak metin analizi modellerini derleyip çalıştırabilirsiniz. Bu modeller, belge sınıflandırması veya yaklaşım Analizi sorunlarını çözmenize yardımcı olabilir.

Bir metin analizi denemesinde, genellikle şunları yapabilirsiniz:

1. Metin veri kümesini Temizleme ve önceden işleme
2. Önceden işlenmiş metinden sayısal Özellik vektörlerini Ayıkla
3. Sınıflandırmayı veya regresyon modelini eğitme
4. Modeli puan edin ve doğrulayın
5. Modeli üretime dağıtma

Bu öğreticide, Amazon Book Incelemeleri veri kümesini kullanarak bir yaklaşım analiz modelinde gezinirken bu adımları öğrenirsiniz (Bu araştırma kağıdına "Biyograflar, Cıvalywood, Boom-kutular ve Blenders: John Blitize göre" yaklaşım sınıflandırması için etki alanı uyarlama " Dredze ve Fernando Pereira; işaretlerini işaretle Hesaplama Linguistics (ACL), 2007.) ilişkilendirmesi Bu veri kümesi, gözden geçirme puanlarını (1-2 veya 4-5) ve serbest biçimli bir metni içerir. Amaç, gözden geçirme Puanını tahmin etmek için gereklidir: düşük (1-2) veya yüksek (4-5).

Bu öğreticide ele alınan denemeleri, Azure Yapay Zeka Galerisi adresinden bulabilirsiniz:

[Kitap Incelemelerini tahmin etme](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[Tahmin Rehberi Incelemeleri-tahmine dayalı deneme](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>1\. Adım: metin veri kümesini Temizleme ve ön işleme
Sorunu iki sınıf sınıflandırması olarak formüle eklemek için İnceleme puanlarını kategorik düşük ve yüksek demetlere bölerek denemeyi deneyiyoruz. [Düzenle meta verileri](https://msdn.microsoft.com/library/azure/dn905986.aspx) ve [Grup kategorik değerleri](https://msdn.microsoft.com/library/azure/dn906014.aspx) modüllerini kullanıyoruz.

![Etiket oluştur](./media/text-analytics-module-tutorial/create-label.png)

Daha sonra, [önceden Işlem metin](https://msdn.microsoft.com/library/azure/mt762915.aspx) modülünü kullanarak metni temizliyoruz. Temizleme, veri kümesindeki paraziti azaltır, en önemli özellikleri bulmanıza ve son modelin doğruluğunu iyileştirmenize yardımcı olur. "A" veya "a" gibi yaygın sözcükleri, özel karakterleri, yinelenen karakterleri, e-posta adreslerini ve URL 'Leri kaldırdık. Ayrıca, metni küçük harfe dönüştürüyoruz, sözcüklerin limon haline gelir ve daha sonra "| | |" simgesiyle belirtilen sembol sınırlarını algılıyoruz.

![Metni Ön İşleme](./media/text-analytics-module-tutorial/preprocess-text.png)

Stopwords için özel bir liste kullanmak istiyorsanız ne yapmanız gerekir? İsteğe bağlı giriş olarak bunu geçirebilirsiniz. Ayrıca, özel C# sözdizimi normal ifadesi kullanarak alt dizeleri değiştirebilir ve konuşmayı bir parça olarak kaldırabilirsiniz: isimler, fiiller veya sıfatlar.

Ön işleme tamamlandıktan sonra, verileri tren ve test kümelerine böyoruz.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>2\. Adım: önceden işlenmiş metinden sayısal Özellik vektörlerini ayıklama
Metin verileri için bir model oluşturmak üzere genellikle serbest biçimli metni sayısal Özellik vektörlerine dönüştürmeniz gerekir. Bu örnekte, metin verilerini bu biçime dönüştürmek için [metin modülünden Ayıkla N-gram özelliklerini](https://msdn.microsoft.com/library/azure/mt762916.aspx) kullanırız. Bu modül, boşluk ile ayrılmış sözcüklerin bir sütununu alır ve veri kümenizde görünen sözcüklerin bir sözlüğünü veya N-gram sözcüklerini hesaplar. Ardından, her bir sözcüğün veya N-gram ' ın her bir kayıtta kaç kez göründüğünü sayar ve bu sayların Özellik vektörlerini oluşturur. Bu öğreticide, N-gram boyutunu 2 olarak ayarlıyoruz, bu nedenle Özellik vektörlerimiz sonraki iki sözcükten oluşan tek kelimeleri ve birleşimleri içermelidir.

![Ayıkla N-gram](./media/text-analytics-module-tutorial/extract-ngrams.png)

N-gram sayılarına ağırlık olarak TF * ıDF (terim sıklığı ters belge sıklığı) uyguladık. Bu yaklaşım, tek bir kayıtta sık görülen, ancak tüm veri kümesinin tamamında nadir olan sözcüklerin kalınlığını ekler. Diğer seçenekler arasında ikili, TF ve Graf ağırlıklı olarak yer alır.

Bu tür metin özellikleri genellikle yüksek boyutalseldir. Örneğin, yapı 'niz 100.000 benzersiz bir kelime içeriyorsa, özellik alanınız 100.000 boyutlara sahip olur veya N-gram kullanılıyorsa daha fazla. Extract N-gram özellikleri modülü, boyutlılık azaltmak için size bir seçenek kümesi sağlar. Kısa veya uzun veya çok seyrek veya çok sık olan kelimeleri önemli tahmine dayalı bir değere sahip olacak şekilde dışarıda bırakmayı seçebilirsiniz. Bu öğreticide, 5 ' ten az kayıt veya kayıt %80 ' den fazla görüntülenen N-gram sayısını dışarıda bırakacağız.

Ayrıca, yalnızca tahmin Hedefinizle en bağıntılı özellikleri seçmek için özellik seçimini de kullanabilirsiniz. 1000 özelliklerini seçmek için kikare-kare özellik seçimini kullanıyoruz. Seçili sözcüklerin veya N-gram 'un sözlüğünü, ayıklama N-gram modülünün sağ çıktısına tıklayarak görüntüleyebilirsiniz.

N-gram özelliklerini Ayıkla özelliğini kullanmanın alternatif bir yaklaşımı olarak, özellik karma modülünü kullanabilirsiniz. Bu [özellik](https://msdn.microsoft.com/library/azure/dn906018.aspx) karması, derleme özelliği seçimi ÖZELLIKLERINE veya TF * IDF ağırlıklı olmasına sahip değildir.

## <a name="step-3-train-classification-or-regression-model"></a>3\. Adım: sınıflandırmayı veya regresyon modelini eğitme
Artık metin, sayısal Özellik sütunlarına dönüştürüldü. Veri kümesi hala önceki aşamalardan dize sütunları içeriyor, bu nedenle onları hariç tutmak için veri kümesinde sütunları seçin.

Hedefimizi tahmin etmek için [Iki sınıf lojistik regresyon](https://msdn.microsoft.com/library/azure/dn905994.aspx) kullanıyoruz: yüksek veya düşük gözden geçirme puanı. Bu noktada, metin analizi sorunu normal bir sınıflandırma sorununa dönüştürüldü. Modeli geliştirmek için Azure Machine Learning Studio klasik sürümünde bulunan araçları kullanabilirsiniz. Örneğin, ne kadar doğru sonuçların olduğunu bulmak için farklı sınıflandırıcılarla denemeler yapabilir veya doğruluğu artırmak için hiper parametre ayarlamayı kullanabilirsiniz.

![Eğitim ve skor](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>4\. Adım: modeli Puanlama ve doğrulama
Eğitilen modeli nasıl doğrularsınız? Test veri kümesine karşı puan veriyoruz ve doğruluğu değerlendirin. Ancak model, N-gram sözlüğünü ve eğitim veri kümesindeki ağırlıklarını öğrendi. Bu nedenle, sözlük ' i oluşturma aksine, bu sözlüğü ve test verilerinden özellikleri ayıkladığınızda bu ağırlıkları kullanırız. Bu nedenle, denemenin Puanlama dalına N-gram Özellik modülünü ayıklama, çıkış sözlüğünü eğitim dalından bağlama ve sözlük modunu salt okunurdur olarak ayarlama yaptık. Ayrıca, en az 1 örnek ve en fazla %100 olarak ayarlayarak ve özellik seçimini devre dışı bırakarak N-gram sayısını sıklık olarak devre dışı bıraktık.

Test verilerinde metin sütunu sayısal Özellik sütunlarına dönüştürüldükten sonra, eğitim dalında olduğu gibi önceki aşamaların dize sütunlarını hariç tutduk. Daha sonra tahminleri yapmak ve doğruluğu değerlendirmek için model modülünü değerlendirmek için puan modeli modülünü kullanırız.

## <a name="step-5-deploy-the-model-to-production"></a>5\. Adım: modeli üretime dağıtma
Model, üretime dağıtılmaya neredeyse hazırlanıyor. Web hizmeti olarak dağıtıldığında, giriş olarak serbest biçimli metin dizesi alır ve tahmini bir "yüksek" veya "düşük" döndürür. Metin özelliklerine dönüştürmek için öğrenilen N-gram sözlüğünü ve bu özelliklerden bir tahmin yapmak için eğitilen lojistik regresyon modelini kullanır. 

Tahmine dayalı denemeyi ayarlamak için ilk olarak N-gram sözlüğünü veri kümesi olarak ve eğitimin eğitim dalından eğitimli lojistik regresyon modelini kaydettik. Daha sonra, tahmine dayalı deney için bir deneme grafiği oluşturmak üzere "farklı kaydet" i kullanarak denemeyi kaydettik. Veri bölme modülünü ve eğitim dalını deneme sürümünden kaldırdık. Daha sonra, N-gram özelliklerini ayıklamak ve sırasıyla model modüllerini öğrenmek için daha önce kaydedilen N-gram sözlüğünü ve modelini bağlayacağız. Ayrıca modeli değerlendir modülünü de kaldırdık.

Etiket sütununu kaldırmak üzere metin modülünden önce Işlem yapmadan önce sütun Seç modülünü, sonra da puan modülündeki "puan sütununu DataSet 'e Ekle" seçeneğini işaretini kaldırdık. Bu şekilde, Web hizmeti tahmin edilmeye çalıştığı etiketi istemez ve giriş özelliklerini yanıt olarak göstermez.

![Tahmine dayalı deneme](./media/text-analytics-module-tutorial/predictive-text.png)

Şimdi Web hizmeti olarak yayımlanılabilecek ve istek-yanıt veya toplu yürütme API 'Leri kullanılarak çağrılan bir deneyimize ihtiyacımız var.

## <a name="next-steps"></a>Sonraki Adımlar
[MSDN belgelerinden](https://msdn.microsoft.com/library/azure/dn905886.aspx)metin analizi modülleri hakkında bilgi edinin.

