---
title: 'Öğretici 2: Kredi risk modellerini eğitme'
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio klasik sürümünde kredi riski değerlendirmesi için tahmine dayalı bir analiz çözümü oluşturmayı gösteren ayrıntılı bir öğretici. Bu öğretici, üç bölümden oluşan bir öğretici serisinin ikinci bölümüdür. Modellerin nasıl eğiteleceğini ve değerlendirileceğini gösterir.
keywords: kredi riski, tahmine dayalı analiz çözümü, risk değerlendirmesi
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: b3f0ebe2cad68759e7feeb7a7cc3dadbbd03ee7e
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838650"
---
# <a name="tutorial-2-train-credit-risk-models---azure-machine-learning-studio-classic"></a>Öğretici 2: Kredi risk modellerini eğitme-Azure Machine Learning Studio (klasik)

Bu öğreticide, tahmine dayalı bir analiz çözümü geliştirme sürecinde genişletilmiş bir görünüm elde edersiniz. Machine Learning Studio klasik sürümünde basit bir model geliştirirsiniz.  Daha sonra modeli bir Azure Machine Learning Web hizmeti olarak dağıtabilirsiniz.  Bu dağıtılan model yeni verileri kullanarak tahminleri yapabilir. Bu öğretici, **üç bölümden oluşan bir öğretici serisinin ikinci bölümüdür**.

Bir kişinin kredi başvurusunda verdiği bilgilere dayanarak kredi riskini tahmin etmeniz gerektiğini varsayalım.  

Kredi risk değerlendirmesi karmaşık bir sorundur, ancak bu öğreticide bir bit basitleştirilmesi sağlanır. Bunu, Microsoft Azure Machine Learning Studio (klasik) kullanarak tahmine dayalı analiz çözümü oluşturma hakkında bir örnek olarak kullanacaksınız. Bu çözüm için Azure Machine Learning Studio klasik sürümünü ve bir Machine Learning Web hizmetini kullanacaksınız.  

Bu üç bölümden oluşan öğreticide, genel kullanıma açık kredi riski verileriyle karşılaşırsınız.  Daha sonra tahmine dayalı bir model geliştirip eğirsiniz.  Son olarak, modeli bir Web hizmeti olarak dağıtırsınız.

[Öğreticinin bir parçası](tutorial-part1-credit-risk.md)olarak bir Machine Learning Studio (klasik) çalışma alanı oluşturdunuz, verileri karşıya yükledi ve bir deneme oluşturdunuz.

Öğreticinin bu bölümünde şunları yapabilirsiniz:
 
> [!div class="checklist"]
> * Birden çok modeli eğitme
> * Modelleri Puanlama ve değerlendirme


[Öğreticinin üçüncü kısmında](tutorial-part3-credit-risk-deploy.md), modeli bir Web hizmeti olarak dağıtacaksınız.

## <a name="prerequisites"></a>Önkoşullar

[Öğreticiden birinin bir kısmını](tutorial-part1-credit-risk.md)doldurun.

## <a name="train"></a>Birden çok modeli eğitme

Makine öğrenimi modelleri oluşturmak için Azure Machine Learning Studio klasik sürümünü kullanmanın avantajlarından biri, tek bir deneyde aynı anda birden fazla model türü deneyebilme ve sonuçları karşılaştırmaktır. Bu tür bir deneme, sorununuz için en iyi çözümü bulmanıza yardımcı olur.

Bu öğreticide geliştirdiğimiz deneymde, iki farklı model türü oluşturacak ve sonra Puanlama sonuçlarını karşılaştırarak son denememiz için hangi algoritmayı kullanmak istediğinize karar verirsiniz.  

Aralarından seçim yapabileceğiniz çeşitli modeller vardır. Kullanılabilir modelleri görmek için, modül paletindeki **Machine Learning** düğümünü genişletin ve ardından modeli ve altındaki düğümleri **Başlat** ' ı genişletin. Bu denemenin amaçları doğrultusunda, [Iki sınıf destek vektör makinesi][two-class-support-vector-machine] (SVM) ve [Iki sınıf önceden artırılmış karar ağacı][two-class-boosted-decision-tree] modüllerini seçersiniz.

> [!TIP]
> Çözmeye çalıştığınız belirli bir sorunu en iyi şekilde karşılayacak Machine Learning algoritmasının karar vermeyle ilgili yardım almak için bkz. [Microsoft Azure Machine Learning Studio algoritmaları seçme (klasik)](algorithm-choice.md).
> 
> 

Bu denemenize hem [Iki sınıf önceden desteklenen karar ağacı][two-class-boosted-decision-tree] modülünü hem de [Iki sınıf desteği vektör makinesi][two-class-support-vector-machine] modülünü ekleyeceksiniz.

### <a name="two-class-boosted-decision-tree"></a>İki sınıf önceden artırılmış karar ağacı

İlk olarak, artırılmış karar ağacı modelini ayarlayın.

1. Modül paletinde [Iki sınıf önceden artırılmış karar ağacı][two-class-boosted-decision-tree] modülünü bulun ve tuvale sürükleyin.

1. [Model eğitme][train-model] modülünü bulun, tuvale sürükleyin ve sonra [Iki sınıf yeniden maliyetli karar ağacı][two-class-boosted-decision-tree] modülünün çıkışını [eğitme modeli][train-model] modülünün sol giriş bağlantı noktasına bağlayın.
   
   [Iki sınıf önceden artırılmış karar ağacı][two-class-boosted-decision-tree] modülü genel modeli başlatır ve [modeli eğitme][train-model] modeli eğitimi için eğitim verilerini kullanır. 

1. Sol tarafta bulunan [R betiği][execute-r-script] modülünün sol çıkışını, [model eğitimi][train-model] modülünün sağ giriş bağlantı noktasına bağlayın (Bu öğreticide, eğitim için veri ayırma modülünün [sol tarafından gelen verileri kullandınız](#train) ).
   
   > [!TIP]
   > Bu deneme için iki girişin ve [yürütme R betiği][execute-r-script] modülünün çıktılarından birine ihtiyacınız yoktur, bu nedenle eklenmeleri geri alabilirsiniz. 
   > 
   > 

Denemenin bu bölümü artık şuna benzer:  

![Model eğitimi](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

Şimdi, modelin kredi riski değerini tahmin etmek istediğiniz modeli [eğitme][train-model] modülüne bildirmeniz gerekir.

1. [Model eğitme][train-model] modülünü seçin. **Özellikler** bölmesinde, **sütun seçiciyi Başlat**' ı tıklatın.

1. **Tek bir sütun seçin** iletişim kutusunda, **kullanılabilir sütunlar**altındaki ara alanına "kredi riski" yazın, aşağıdaki "kredi riski" nı seçin ve sağ ok düğmesine ( **>** ) tıklayarak "kredi riski" nu **Seçili sütunlara**taşıyın. 

    ![Model eğitme modülü için kredi risk sütununu seçin](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. **Tamam** onay işaretine tıklayın.

### <a name="two-class-support-vector-machine"></a>İki Sınıflı Destek Vektör Makinesi

Ardından, SVM modelini ayarlarsınız.  

Birincisi, SVM hakkında kısa bir açıklama. Daha fazla sayıda karar ağaçları, herhangi bir türdeki özelliklerle iyi çalışır. Ancak, SVM modülü doğrusal bir sınıflandırıcı oluşturduğundan, oluşturduğu modelin tüm sayısal özellikler aynı ölçekte olduğunda en iyi test hatası vardır. Tüm sayısal özellikleri aynı ölçeğe dönüştürmek için bir "tanh" dönüşümü ( [Normalleştir veri][normalize-data] modülüyle birlikte) kullanırsınız. Bu, numaralarımızı [0, 1] aralığına dönüştürür. SVM modülü, dize özelliklerini kategorik özelliklerine ve sonra ikili 0/1 özelliklerine dönüştürür, bu nedenle dize özelliklerini el ile dönüştürmeniz gerekmez. Ayrıca, kredi risk sütununu (sütun 21) dönüştürmek istemezsiniz; bu sayı sayısal olur, ancak modeli tahmin etmek için eğitidiğimiz değer bu şekilde tek tek ayrılmaları gerekir.  

SVM modelini ayarlamak için şunları yapın:

1. Modül paletinde [Iki sınıf destek vektör makinesi][two-class-support-vector-machine] modülünü bulun ve tuvale sürükleyin.

1. [Modeli eğitme][train-model] modülüne sağ tıklayın, **Kopyala**' yı seçin ve ardından tuvale sağ tıklayıp **Yapıştır**' ı seçin. [Tren modeli][train-model] modülünün kopyası, orijinalle aynı sütun seçimine sahiptir.

1. [Iki sınıf destek vektör makinesi][two-class-support-vector-machine] modülünün çıkışını Ikinci [eğitim modeli][train-model] modülünün sol giriş bağlantı noktasına bağlayın.

1. [Verileri Normalleştir][normalize-data] modülünü bulun ve tuvale sürükleyin.

1. Sol [Execute R betik][execute-r-script] modülünün sol çıkışını Bu modülün girdisine bağlayın (bir modülün çıkış bağlantı noktasının birden fazla modüle bağlı olabileceğini unutmayın).

1. [Normalleştirme verileri][normalize-data] modülünün sol çıkış bağlantı noktasını Ikinci [eğitim modeli][train-model] modülünün doğru giriş bağlantı noktasına bağlayın.

Denemenizin bu bölümü şu şekilde görünmelidir:  

![İkinci modeli eğitme](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

Şimdi [normalize veri][normalize-data] modülünü yapılandırın:

1. [Verileri Normalleştir][normalize-data] modülünü seçmek için tıklayın. **Özellikler** bölmesinde, **dönüşüm yöntemi** parametresi için **tanh** ' yi seçin.

1. **Sütun seçiciyi Başlat**' a tıklayın, **Başlangıç**için "sütun yok" u seçin, ilk açılan menüde **Ekle** ' yi seçin, ikinci açılan menüde **sütun türü** ' nü seçin ve üçüncü açılan menüde **sayısal** ' i seçin. Bu, tüm sayısal sütunların (ve yalnızca sayısal) dönüştürüldüğünü belirtir.

1. Bu satırın sağ tarafındaki artı işaretine (+) tıklayın. Bu, açılan bir satır oluşturur. İlk açılan menüde **hariç tut** ' u seçin, ikinci açılan menüde **sütun adları** ' nı seçin ve metin alanına "kredi riski" yazın. Bu, kredi riski sütununun yoksayılıp sayılmayacağını belirtir (Bu sütun sayısal olduğu için bunu yapmanız gerekir, aksi takdirde onu dışmadıysanız dönüştürüleirdi).

1. **Tamam** onay işaretine tıklayın.  

    ![Normalize veri modülü için sütunları seçin](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


[Verileri Normalleştir][normalize-data] modülü, kredi riski sütunu hariç tüm sayısal sütunlarda bir tanh dönüştürmesi gerçekleştirecek şekilde ayarlanmıştır.  

## <a name="score-and-evaluate-the-models"></a>Modelleri Puanlama ve değerlendirme

eğitimli modellerimizi öğrenmek için [bölünmüş veri][split] modülü tarafından ayrılmış test verilerini kullanın. daha sonra hangi oluşturulan daha fazla sonuç olduğunu görmek için iki modelin sonucunu karşılaştırabilirsiniz.  

### <a name="add-the-score-model-modules"></a>Puan modeli modüllerini ekleme

1. [Puan modeli][score-model] modülünü bulun ve tuvale sürükleyin.

1. [Iki sınıflı bir karar ağacı][two-class-boosted-decision-tree] modülüne bağlanan [eğitim modeli][train-model] modülünü, [puan modeli][score-model] modülünün sol giriş bağlantı noktasına bağlayın.

1. Sağ [Execute R betik][execute-r-script] modülünü (test verileri), [puan modeli][score-model] modülünün sağ giriş bağlantı noktasına bağlayın.

    ![Puan modeli modülü bağlandı](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   [Puan modeli][score-model] modülü artık test verilerinden kredi bilgilerini alabilir, bunu model aracılığıyla çalıştırabilir ve modelin test verilerinde gerçek kredi riski sütunuyla oluşturduğu tahminleri karşılaştırabilir.

1. İkinci bir kopya oluşturmak için [puan modeli][score-model] modülünü kopyalayıp yapıştırın.

1. SVM modelinin çıkışını (yani, [Iki sınıf destek vektör makinesi][two-class-support-vector-machine] modülüne bağlı olan [tren modeli][train-model] modülünün çıkış bağlantı noktası) ikinci [puan modeli][score-model] modülünün giriş bağlantı noktasına bağlayın.

1. SVM modeli için, eğitim verilerinde yaptığınız gibi test verilerinde aynı dönüşümü yapmanız gerekir. Bu nedenle, ikinci bir kopya oluşturmak ve sağ [Execute R betik][execute-r-script] modülüne bağlamak Için [normalize veri][normalize-data] modülünü kopyalayıp yapıştırın.

1. İkinci [normalize veri][normalize-data] modülünün sol çıkışını Ikinci [puan modeli][score-model] modülünün sağ giriş bağlantı noktasına bağlayın.

    ![Her iki puan modeli modülü bağlı](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>Modeli değerlendir modülünü ekleme

İki Puanlama sonucunu değerlendirmek ve karşılaştırmak için [model değerlendir][evaluate-model] modülünü kullanırsınız.  

1. [Modeli değerlendir][evaluate-model] modülünü bulun ve tuvale sürükleyin.

1. Artırılmış karar ağacı modeliyle ilişkili [puan modeli][score-model] modülünün çıkış bağlantı noktasını, [modeli değerlendir][evaluate-model] modülünün sol giriş bağlantı noktasına bağlayın.

1. Diğer [puan modeli][score-model] modülünü doğru giriş bağlantı noktasına bağlayın.  

    ![Bağlı model modülünü değerlendir](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>Denemeyi çalıştırın ve sonuçları denetleyin

Denemeyi çalıştırmak için, tuvalin altındaki **Çalıştır** düğmesine tıklayın. Bu işlem birkaç dakika sürebilir. Her modülde dönen bir gösterge çalıştığını gösterir ve ardından, modülün ne zaman bittiğini gösteren yeşil bir onay işareti görünür. Tüm modüllerin onay işareti varsa, deneme çalışmayı bitirdi.

Deneme şimdi şuna benzer görünmelidir:  

![Her iki modeli değerlendirme](./media/tutorial-part2-credit-risk-train/final-experiment.png)


Sonuçları denetlemek için [modeli değerlendir][evaluate-model] modülünün çıkış bağlantı noktasına tıklayın ve **Görselleştir**' i seçin.  

[Modeli değerlendir][evaluate-model] modülü, iki puanlanmış modelin sonuçlarını karşılaştırmanıza imkan tanıyan bir çift eğri ve ölçüm oluşturur. Sonuçları alıcı Işleci özelliği (ROC) eğrileri, duyarlık/geri çağırma eğrileri veya eğrileri Yükselt olarak görüntüleyebilirsiniz. Görünen ek veriler, bir karışıklık matrisi, eğri (AUC) ve diğer ölçümler altında bulunan alanın birikmeli değerlerini içerir. Kaydırıcıyı sola veya sağa taşıyarak eşik değerini değiştirebilir ve ölçüm kümesini nasıl etkileyeceğini görebilirsiniz.  

Grafiğin sağında, ilişkili eğriyi vurgulamak ve aşağıdaki ilişkili ölçümleri göstermek üzere karşılaştırmak için **puanlanmış veri kümesi** veya **Puansal veri kümesi** ' ne tıklayın. Eğrilerin göstergesinde, "puanlanmış veri kümesi" [modeli değerlendir][evaluate-model] modülünün sol giriş bağlantı noktasına karşılık gelir. Bu durumda, bu, önceden maliyetli karar ağacı modelidir. "Karşılaştırılacak puanlanmış veri kümesi", bizim örneğimizde bulunan SVM modeli doğru giriş bağlantı noktasına karşılık gelir. Bu etiketlerden birine tıkladığınızda, bu modelin eğrisi vurgulanır ve aşağıdaki grafikte gösterildiği gibi ilgili ölçümler görüntülenir.  

![Modeller için ROC eğrileri](./media/tutorial-part2-credit-risk-train/roc-curves.png)

Bu değerleri inceleyerek, aradığınız sonuçları vermek için hangi modelin en yakın olduğuna karar verebilirsiniz. Farklı modellerdeki parametre değerlerini değiştirerek denemeniz için geri dönüp yineleyebilirsiniz. 

Bu sonuçları yorumlarken ve model performansını ayarlamaya yönelik bilim ve sanat Bu öğreticinin kapsamı dışındadır. Ek Yardım için aşağıdaki makaleleri okuyabilirsiniz:
- [Azure Machine Learning Studio 'de model performansını değerlendirme (klasik)](evaluate-model-performance.md)
- [Azure Machine Learning Studio algoritmalarınızı iyileştirmek için parametreler seçin (klasik)](algorithm-parameters-optimize.md)
- [Azure Machine Learning Studio model sonuçlarını yorumlama (klasik)](interpret-model-results.md)

> [!TIP]
> Denemeyi her çalıştırdığınızda, bu yinelemenin bir kaydı çalıştırma geçmişinde tutulur. Bu yinelemeleri görüntüleyebilir ve tuvalin altındaki **çalıştırma geçmişini görüntüle** ' ye tıklayarak bunlardan birine dönebilirsiniz. Ayrıca, açtığınız bir yinelemeden hemen önce yinelemeye geri dönmek için **Özellikler** bölmesinde **önceki çalıştırmaya** de tıklayabilirsiniz.
> 
> Herhangi bir yinelemenizin kopyasını, tuvalin altında olacak **şekilde kaydet** ' i tıklatarak yapabilirsiniz. 
> Deneme yinelemeleriniz içinde çalıştıklerinizin kaydını tutmak için deneme 'nin **Özet** ve **Açıklama** özelliklerini kullanın.
> 
> Daha fazla bilgi için bkz. [Azure Machine Learning Studio (klasik) deneme yinelemelerini yönetme](manage-experiment-iterations.md).  
> 
> 

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şu adımları tamamladınız: 
 
> [!div class="checklist"]
> * Deneme oluşturma
> * Birden çok modeli eğitme
> * Modelleri Puanlama ve değerlendirme

Artık bu veriler için modeller dağıtmaya hazırsınız demektir.

> [!div class="nextstepaction"]
> [Öğretici 3 - Modelleri dağıtma](tutorial-part3-credit-risk-deploy.md)


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/