---
title: 'Öğretici 3: Kredi risk modelini dağıtma'
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio klasik sürümünde kredi riski değerlendirmesi için tahmine dayalı bir analiz çözümü oluşturmayı gösteren ayrıntılı bir öğretici. Bu öğretici, üç bölümden oluşan bir öğretici serisinin üçüncü bölümüdür. Bir modelin Web hizmeti olarak nasıl dağıtılacağını gösterir.
keywords: kredi riski, tahmine dayalı analiz çözümü, risk değerlendirmesi, dağıtma, Web hizmeti
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 4e8e8a114a06029ef9a81d92b60afd575bc9661f
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839497"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio-classic"></a>Öğretici 3: Kredi risk modelini dağıtma-Azure Machine Learning Studio (klasik)

Bu öğreticide, tahmine dayalı bir analiz çözümü geliştirme sürecinde genişletilmiş bir görünüm elde edersiniz. Machine Learning Studio (klasik) içinde basit bir model geliştirirsiniz.  Daha sonra modeli bir Azure Machine Learning Web hizmeti olarak dağıtabilirsiniz.  Bu dağıtılan model yeni verileri kullanarak tahminleri yapabilir. Bu öğretici, **üç bölümden oluşan bir öğretici serisinin üçüncü bölümüdür**.

Bir kişinin kredi başvurusunda verdiği bilgilere dayanarak kredi riskini tahmin etmeniz gerektiğini varsayalım.  

Kredi risk değerlendirmesi karmaşık bir sorundur, ancak bu öğreticide bir bit basitleştirilmesi sağlanır. Bunu, Microsoft Azure Machine Learning Studio (klasik) kullanarak tahmine dayalı analiz çözümü oluşturma hakkında bir örnek olarak kullanacaksınız. Bu çözüm için Azure Machine Learning Studio klasik sürümünü ve bir Machine Learning Web hizmetini kullanacaksınız. 

Bu üç bölümden oluşan öğreticide, genel kullanıma açık kredi riski verileriyle karşılaşırsınız.  Daha sonra tahmine dayalı bir model geliştirip eğirsiniz.  Son olarak, modeli bir Web hizmeti olarak dağıtırsınız.

[Öğreticinin bir parçası](tutorial-part1-credit-risk.md)olarak bir Machine Learning Studio (klasik) çalışma alanı oluşturdunuz, verileri karşıya yükledi ve bir deneme oluşturdunuz.

[Öğreticinin ikinci bölümünde](tutorial-part2-credit-risk-train.md), modellerinizi eğitildiniz ve değerlendirdiniz.

Öğreticinin bu bölümünde şunları yapabilirsiniz:

> [!div class="checklist"]
> * Dağıtıma hazırlanma
> * Web hizmetini dağıtma
> * Web hizmetini test etme
> * Web hizmetini yönetme
> * Web hizmetine erişme

## <a name="prerequisites"></a>Önkoşullar

[Öğreticinin ikinci bölümünü](tutorial-part2-credit-risk-train.md)doldurun.

## <a name="prepare-for-deployment"></a>Dağıtıma hazırlanma
Başkalarına bu öğreticide geliştirdiğiniz tahmine dayalı modeli kullanma şansı vermek için, Azure 'da Web hizmeti olarak dağıtabilirsiniz.

Modelimizi eğitmek için bu noktaya kadar deneme yapmış olursunuz. Ancak dağıtılan hizmet artık eğitimi yapamayacak. modelimize bağlı olarak kullanıcının girişini Puanlama yoluyla yeni tahminler oluşturacağız. Bu nedenle, bu denemeyi bir ***eğitim deneyminden*** tahmine ***dayalı*** bir deneyle dönüştürmek için bazı hazırlıklar yapacağız. 

Dağıtım hazırlığı üç adımlı bir işlemdir:  

1. Modellerden birini kaldırma
1. Oluşturduğunuz Eğitim denemesini tahmine dayalı bir *deneyle* dönüştürün
1. Tahmine dayalı denemeyi Web hizmeti olarak dağıtma

### <a name="remove-one-of-the-models"></a>Modellerden birini kaldırma

İlk olarak, bu denemeyi biraz daha az kırpabilmeniz gerekir. Şu anda denemenin iki farklı modeli vardır, ancak bunu bir Web hizmeti olarak dağıtırken yalnızca bir model kullanmak istersiniz.  

Daha önce, artırılmış ağaç modelinin SVM modelinden daha iyi gerçekleştirildiğinden emin olalım. Yani ilk yapmanız gereken [Iki sınıf destek vektör makinesi][two-class-support-vector-machine] modülünü ve bunu eğitmek için kullanılan modülleri kaldırdık. Deneme tuvalinin en altında **farklı kaydet** ' e tıklayarak önce denemenin bir kopyasını oluşturmak isteyebilirsiniz.

aşağıdaki modülleri silmeniz gerekir:  

* [İki sınıflı destek vektör makinesi][two-class-support-vector-machine]
* Modele bağlı model ve [puan modeli][score-model] modüllerini [eğitme][train-model]
* [Verileri Normalleştir][normalize-data] (her ikisi de)
* [Modeli değerlendir][evaluate-model] (modellerin değerlendirilmesi tamamlandığımızda)

Her modülü seçin ve DELETE tuşuna basın veya modüle sağ tıklayıp **Sil**' i seçin. 

![Destek vektör makinesi modelini kaldırmak için hangi modüllerin silineceğini vurgular](./media/tutorial-part3-credit-risk-deploy/publish3a.png)

Modelimiz şu şekilde bir şekilde görünmelidir:

![Destek vektör makine modeli silindiğinde ortaya çıkan deneme](./media/tutorial-part3-credit-risk-deploy/publish3.png)

Şimdi, bu modeli [Iki sınıf önceden artırılmış karar ağacını][two-class-boosted-decision-tree]kullanarak dağıtmaya hazırız.

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Eğitim denemesini tahmine dayalı bir deneyle Dönüştür

Bu modeli dağıtıma hazırlamak için, bu eğitim denemesini bir tahmine dayalı deneyle dönüştürmeniz gerekir. Bu üç adımdan oluşur:

1. Eğitilen modeli kaydedin ve ardından eğitim modüllerimizi değiştirin
1. Yalnızca eğitim için gereken modülleri kaldırma denemesinin kırpmasını yapın
1. Web hizmetinin girişi kabul edeceğini ve çıktının nerede oluşturduğu yeri tanımlayın

Bunu el ile yapabilirsiniz ancak neyse ki, deneme tuvalinin alt kısmındaki **Web hizmeti ayarla** öğesine tıklanarak (tahmine **dayalı Web hizmeti** seçeneği seçilerek) tüm üç adımı gerçekleştirebilirsiniz.

> [!TIP]
> Bir eğitim denemesini tahmine dayalı bir deneyle dönüştürdüğünüzde ne olacağı hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Machine Learning Studio (klasik) dağıtım için modelinizi hazırlama](convert-training-experiment-to-scoring-experiment.md).

**Web hizmeti ayarla**' ya tıkladığınızda birkaç şey meydana gelir:

* Eğitilen model tek bir **eğitilen model** modülüne dönüştürülür ve deneme tuvalinin solundaki modül paletinde depolanır ( **eğitilen modeller**altında bulabilirsiniz)
* Eğitim için kullanılan modüller kaldırılır; engelle
  * [İki sınıf önceden artırılmış karar ağacı][two-class-boosted-decision-tree]
  * [Modeli eğitme][train-model]
  * [Verileri Böl][split]
  * test verileri için kullanılan ikinci [Execute R betik][execute-r-script] modülü
* Kaydedilen eğitilen model denemeye geri eklendi
* **Web hizmeti girişi** ve **Web hizmeti çıkış** modülleri eklendi (Bunlar, kullanıcının verilerinin modeli nereye girebileceklerini ve Web hizmetine erişildiğinde verilerin döndürüleceğini belirler)

> [!NOTE]
> Deneyinin, deneme tuvalinin en üstünde eklenen sekmelerin altında iki parçaya kaydedildiğini görebilirsiniz. Özgün eğitim denemesi, **eğitim denemenizin**altındayken yeni oluşturulan tahmine dayalı deneme, tahmine **dayalı denemenin**altında. Tahmine dayalı deneme, Web hizmeti olarak dağıtacağınız bir deneyimdir.

Bu deneyle ek bir adım gerçekleştirmeniz gerekir.
verilere bir ağırlık işlevi sağlamak için iki [Execute R betik][execute-r-script] modülü eklediniz. Bu, yalnızca eğitim ve test için gerekli olan bir eliydi ve bu modülleri son modelde alabilirsiniz.
Machine Learning Studio (klasik) [bölme][split] modülünü kaldıran bir [Execute R betik][execute-r-script] modülünü kaldırdı. Artık diğer ve bağlantı [meta verileri düzenleyicisini][metadata-editor] doğrudan [model Puanlama][score-model]' ne gönderebilirsiniz.    

Deneme mız şu şekilde görünmelidir:  

![Eğitilen modeli Puanlama](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> Tahmine dayalı denemenize ait UCı Almanya kredi kartı veri veri kümesini neden solınızdan merak ediyor olabilirsiniz. Hizmet özgün veri kümesine değil kullanıcının verilerini puanlıyor, bu nedenle özgün veri kümesini neden modelde bıraksın?
> 
> Hizmetin orijinal kredi kartı verilerine ihtiyacı yoktur. Ancak, bu verilerin şemasına ihtiyacı vardır. Bu, kaç sütun olduğunu ve hangi sütunların sayısal olduğunu belirten bilgiler içerir. Bu şema bilgileri, kullanıcının verilerini yorumlamak için gereklidir. hizmet çalışırken Puanlama modülünün veri kümesi şemasına sahip olması için bu bileşenleri bağlı bırakmalısınız. Veriler kullanılmaz, yalnızca şema.  
> 
>Dikkat edilmesi gereken önemli nokta, özgün veri kümeniz etiketi içeriyorsa, Web girişinden beklenen şemanın de etiketi olan bir sütun beklediği bir şeydir! Bunu yapmanın bir yolu, etiketi ve eğitim veri kümesindeki diğer tüm verileri kaldırmanız, ancak web girişi ve eğitim veri kümesini ortak bir modüle bağlamadan önce Web girişlerinde olmayacaktır. 
> 

Deneme süresini bir kez çalıştırın ( **Çalıştır**' a tıklayın.) Modelin hala çalıştığını doğrulamak istiyorsanız, [puan modeli][score-model] modülünün çıktısına tıklayın ve **sonuçları görüntüle**' yi seçin. Orijinal verilerin, kredi riski değeri ("puanlanmış Etiketler") ve Puanlama olasılık değeri ("puanlanmış olasılıkların") ile birlikte görüntülendiğini görebilirsiniz. 

## <a name="deploy-the-web-service"></a>Web hizmetini dağıtma
Denemeyi klasik bir Web hizmeti olarak veya Azure Resource Manager dayalı yeni bir Web hizmeti olarak dağıtabilirsiniz.

### <a name="deploy-as-a-classic-web-service"></a>Klasik Web hizmeti olarak dağıtma
Denediğimiz bir klasik Web hizmetini dağıtmak için, tuvalin altında **Web Hizmeti Dağıt** ' a tıklayın ve **Web Hizmeti Dağıt [klasik]** seçeneğini belirleyin. Machine Learning Studio (klasik), denemeyi bir Web hizmeti olarak dağıtır ve sizi o Web hizmeti panosuna götürür. Bu sayfadan denemeye geri dönebilir (**anlık görüntü** veya **en sonuncuyu**görüntüleme) ve Web hizmetinin basit bir testini çalıştırabilirsiniz (bkz. **Test Web hizmeti** ). Burada Web hizmetine erişebilen uygulamalar (Bu öğreticinin sonraki adımında daha fazla) oluşturmak için buradaki bilgiler de vardır.

![Web hizmeti panosu](./media/tutorial-part3-credit-risk-deploy/publish6.png)


**Yapılandırma** sekmesine tıklayarak hizmeti yapılandırabilirsiniz. Burada, hizmet adını değiştirebilirsiniz (varsayılan olarak deneme adı verilir) ve buna bir açıklama verin. Giriş ve çıkış verileri için daha kolay Etiketler de verebilirsiniz.  

![Web hizmetini yapılandırma](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>Yeni bir Web hizmeti olarak dağıtma

> [!NOTE] 
> Yeni bir Web hizmeti dağıtmak için, Web hizmetini dağıttığınız abonelikte yeterli izinlere sahip olmanız gerekir. Daha fazla bilgi için, [Azure Machine Learning Web Hizmetleri portalını kullanarak bir Web hizmetini yönetme](manage-new-webservice.md)makalesine bakın. 

Denediğimiz yeni bir Web hizmetini dağıtmak için:

1. Tuvalin altında **Web Hizmeti Dağıt** ' a tıklayın ve **Web Hizmeti Dağıt [yeni]** seçeneğini belirleyin. Machine Learning Studio (klasik) sizi Azure Machine Learning Web Hizmetleri **dağıtım** denemesi sayfasına aktarır.

1. Web hizmeti için bir ad girin. 

1. **Fiyat planı**için, mevcut bir fiyatlandırma planı seçebilir veya "Yeni oluştur" seçeneğini belirleyip yeni plana bir ad verebilir ve aylık plan seçeneğini belirleyebilirsiniz. Varsayılan bölgeniz ve Web hizmetiniz için plan katmanları varsayılan olarak bu bölgeye dağıtılır.

1. **Dağıt**' a tıklayın.

Birkaç dakika sonra, Web hizmetiniz için **hızlı başlangıç** sayfası açılır.

**Yapılandır** sekmesine tıklayarak hizmeti yapılandırabilirsiniz. Burada hizmet başlığını değiştirebilir ve bir açıklama verebilirsiniz. 

Web hizmetini test etmek için, **Test** sekmesine tıklayın (aşağıdaki **Web hizmetini test** edin). Web hizmetine erişebilen uygulamalar oluşturma hakkında daha fazla bilgi **için, kullanma sekmesine tıklayın** (bu öğreticideki bir sonraki adım daha fazla ayrıntıya gidecektir).

> [!TIP]
> Web hizmetini dağıttıktan sonra güncelleştirebilirsiniz. Örneğin, modelinizi değiştirmek istiyorsanız Eğitim denemesini düzenleyebilir, model parametrelerini ince ayar ve **Web**hizmeti dağıt, **Web Hizmeti Dağıt [klasik]** veya **Web hizmetini dağıtma [yeni]** ' ya tıklayabilirsiniz. Denemeyi yeniden dağıttığınızda, şimdi güncelleştirilmiş modelinizi kullanarak Web hizmetinin yerini alır.  
> 
> 

## <a name="test-the-web-service"></a>Web hizmetini test etme

Web hizmetine erişildiğinde, kullanıcının verileri, [puan modeli modülüne ve puanına][score-model] geçirildiği **Web hizmeti giriş** modülüne girer. Tahmine dayalı denemeyi ayarlama yönteminiz, model, verileri orijinal kredi riski veri kümesiyle aynı biçimde bekler.
Sonuçlar Web hizmetinden **Web hizmeti çıkış** modülü aracılığıyla kullanıcıya döndürülür.

> [!TIP]
> Tahmine dayalı denemenize sahip olduğunuz şekilde, [puan modeli][score-model] modülündeki tüm sonuçlar döndürülür. Bu, tüm giriş verilerini artı kredi riski değerini ve Puanlama olasılığını içerir. Ancak isterseniz farklı bir şey döndürebilirsiniz. Örneğin, yalnızca kredi risk değerini döndürebilirsiniz. Bunu yapmak için, Web hizmetinin döndürmesini istemediğiniz sütunları ortadan kaldırmak için [puan modeli][score-model] ve **Web hizmeti çıkışı** arasına bir [sütun Seç][select-columns] modülü ekleyin. 
> 
> 

Klasik bir Web hizmetini **Machine Learning Studio (klasik)** veya **Azure Machine Learning Web Hizmetleri** portalında test edebilirsiniz.
Yeni bir Web hizmetini yalnızca **Machine Learning Web Hizmetleri** portalında test edebilirsiniz.

> [!TIP]
> Azure Machine Learning Web Hizmetleri portalında test edilirken, portalın Istek-yanıt hizmetini test etmek için kullanabileceğiniz örnek veriler oluşturmasını sağlayabilirsiniz. **Yapılandırma** sayfasında, **örnek veriler etkin**olarak "Evet" i seçin. **Test** sayfasındaki Istek-yanıt sekmesini açtığınızda, Portal orijinal kredi riski veri kümesinden alınan örnek verileri doldurur.

### <a name="test-a-classic-web-service"></a>Klasik Web hizmetini test etme

Klasik bir Web hizmetini Machine Learning Studio (klasik) veya Machine Learning Web Hizmetleri portalında test edebilirsiniz. 

#### <a name="test-in-machine-learning-studio-classic"></a>Machine Learning Studio 'de test (klasik)

1. Web hizmetinin **Pano** sayfasında, **varsayılan uç nokta**' ın altındaki **Test** düğmesine tıklayın. Bir iletişim kutusu açılır ve size hizmet için giriş verileri sorulur. Bunlar, orijinal kredi riski veri kümesinde görünen sütunlardır.  

1. Bir veri kümesi girin ve ardından **Tamam**' a tıklayın. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Machine Learning Web Hizmetleri portalında test etme

1. Web hizmetinin **Pano** sayfasında, **varsayılan uç nokta**altındaki **Test önizlemesi** bağlantısına tıklayın. Web hizmeti uç noktası için Azure Machine Learning Web Hizmetleri portalındaki sınama sayfası açılır ve size hizmet için giriş verileri ister. Bunlar, orijinal kredi riski veri kümesinde görünen sütunlardır.

2. **Test isteği-yanıt**öğesine tıklayın. 

### <a name="test-a-new-web-service"></a>Yeni bir Web hizmetini test etme

Yeni bir Web hizmetini yalnızca Machine Learning Web Hizmetleri portalında test edebilirsiniz.

1. [Azure Machine Learning Web Hizmetleri](https://services.azureml.net/quickstart) portalında, sayfanın üst kısmındaki **Test** ' e tıklayın. **Test** sayfası açılır ve hizmet için veri girişi yapabilirsiniz. Görüntülenen giriş alanları, orijinal kredi riski veri kümesinde görünen sütunlara karşılık gelir. 

1. Bir veri kümesi girin ve ardından **Test isteği-yanıtı**' na tıklayın.

Testin sonuçları, çıkış sütunundaki sayfanın sağ tarafında görüntülenir. 


## <a name="manage-the-web-service"></a>Web hizmetini yönetme

Klasik veya yeni bir Web hizmetinizi dağıttıktan sonra, [Microsoft Azure Machine Learning Web Hizmetleri](https://services.azureml.net/quickstart) portalından yönetebilirsiniz.

Web hizmetinizin performansını izlemek için:

1. [Microsoft Azure Machine Learning Web Hizmetleri](https://services.azureml.net/quickstart) portalında oturum açın
1. **Web Hizmetleri** ' ne tıklayın
1. Web hizmetinize tıklayın
1. **Panoya** tıklayın

## <a name="access-the-web-service"></a>Web hizmetine erişme

Bu öğreticinin önceki adımında, kredi riski tahmin modelini kullanan bir Web hizmeti dağıttınız. Artık kullanıcılar bu verilere veri gönderebiliyor ve sonuç alabilir. 

Web hizmeti, REST API 'Leri kullanarak verileri alabilen ve döndürebili bir Azure Web hizmetidir:  

* **İstek/yanıt** -Kullanıcı http protokolünü kullanarak hizmete bir veya daha fazla kredi verileri satırı gönderir ve hizmet bir veya daha fazla sonuç kümesiyle yanıt verir.
* **Toplu yürütme** -Kullanıcı bir veya daha fazla kredi verileri satırını bir Azure blobuna depolar ve sonra blob konumunu hizmete gönderir. Hizmet, giriş blobundaki tüm veri satırlarını alır, sonuçları başka bir bloba depolar ve bu kapsayıcının URL 'sini döndürür.  

Klasik bir Web hizmetine erişmenin en hızlı ve en kolay yolu [Azure ML isteği-yanıt hizmeti Web uygulaması](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/) veya [Azure ML Batch yürütme hizmeti Web uygulaması şablonunu](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)kullanmaktır.

Bu Web uygulaması şablonları, Web hizmetinizin giriş verilerini ve ne döneceğini bilen özel bir Web uygulaması oluşturabilir. Yapmanız gereken tek şey Web hizmetinize ve verilerinize erişim sağlar ve şablon Rest 'i yapar.

Web uygulaması şablonlarını kullanma hakkında daha fazla bilgi için bkz. [Web uygulaması şablonuyla Azure Machine Learning Web hizmeti](/azure/machine-learning/studio/consume-web-services)kullanma.



## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şu adımları tamamladınız:

> [!div class="checklist"]
> * Dağıtıma hazırlanma
> * Web hizmetini dağıtma
> * Web hizmetini test etme
> * Web hizmetini yönetme
> * Web hizmetine erişme

Ayrıca, R, C#ve Python programlama dillerinde sunulan başlatıcı kodu kullanarak Web hizmetine erişmek için özel bir uygulama da geliştirebilirsiniz.

> [!div class="nextstepaction"]
> [Azure Machine Learning Web hizmeti kullanma](consume-web-services.md)

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
