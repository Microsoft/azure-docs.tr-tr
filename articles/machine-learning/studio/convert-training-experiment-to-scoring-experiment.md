---
title: Modeli dağıtım için hazırla
titleSuffix: ML Studio (classic) - Azure
description: Machine Learning Studio (klasik) eğitim denemenizi tahmine dayalı bir deneyle dönüştürerek, eğitilen modelinizi Web hizmeti olarak dağıtıma hazırlama.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 03/28/2017
ms.openlocfilehash: 4b07150ac9f35085763786c505d3d746428a542b
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839670"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (klasik) dağıtım için modelinizi hazırlama

Azure Machine Learning Studio (klasik), tahmine dayalı bir analiz modeli geliştirmek için ihtiyacınız olan araçları sağlar ve Azure Web hizmeti olarak dağıtarak onu çalıştırabilirsiniz.

Bunu yapmak için Studio 'nun klasik sürümünü kullanarak modelinizi eğitebileceğiniz, skor ve düzenlediğiniz *eğitim denemenize* yönelik bir deneme adı verilir. Memnun olduktan sonra, eğitim denemenizi, Kullanıcı verilerini Puanlama için yapılandırılmış bir tahmine *dayalı deneye* dönüştürerek modelinizi dağıtıma hazırlıyoruz.

Bu işleme örnek olarak [öğretici 1: kredi riskini tahmin](tutorial-part1-credit-risk.md)edin ' de bakabilirsiniz.

Bu makalede, bir eğitim denemesinin tahmine dayalı bir deneyime nasıl dönüştürüldüğü ve bu tahmine dayalı denemenin nasıl dağıtıldığı ayrıntılarıyla ilgili ayrıntılı bilgiler yer alır. Bu ayrıntıları inceleyerek, dağıtılan modelinizin daha etkili olması için nasıl yapılandırılacağını öğrenebilirsiniz.



## <a name="overview"></a>Genel Bakış 

Bir öngörülü deneye Eğitim denemesini dönüştürme işlemi üç adımdan oluşur:

1. Machine Learning algoritması modüllerini eğitilen modellerinizle değiştirin.
2. Denemeyi yalnızca Puanlama için gereken modüllerle kırpın. Eğitim denemesi, eğitim için gerekli olan, ancak model eğitilen bir kez gerekli olmayan birkaç modül içerir.
3. Modelinizin Web hizmeti kullanıcıdan verileri kabul edip etmeyeceğini ve hangi verilerin döndürüleceğini tanımlayın.

> [!TIP]
> Eğitim denemenizin deneme sürümünde kendi verilerinizi kullanarak modelinize yönelik eğitim ve Puanlama ile ilgilendiniz. Ancak dağıtıldıktan sonra kullanıcılar modelinize yeni veriler gönderir ve tahmin sonuçları döndürür. Bu nedenle, eğitim denemenizi dağıtıma hazırlamak için bir tahmine dayalı deneyle dönüştürerek, modelin başkaları tarafından nasıl kullanılacağını aklınızda bulundurun.
> 
> 

## <a name="set-up-web-service-button"></a>Web hizmeti ayarlama düğmesi
Denemenizi çalıştırdıktan sonra (deneme tuvalinin alt kısmındaki **Çalıştır** ' a tıklayın), **Web hizmeti ayarla** düğmesine tıklayın (tahmine **dayalı Web hizmeti** seçeneğini belirleyin). **Web hizmeti** 'ni, eğitim denemenizi bir tahmine dayalı deneyle dönüştürmenin üç adımı sizin için gerçekleştirir:

1. Eğitilen modelinizi modül paleti (deneme tuvalinin sol tarafında) **eğitilen modeller** bölümüne kaydeder. Ardından makine öğrenimi algoritmasını değiştirir ve model modüllerini kaydedilen eğitilen modelle [eğitme][train-model] .
2. Denemenizin analiz edildiği ve yalnızca eğitim için açıkça kullanılan ve artık gerekli olmayan modülleri kaldıran.
3. _Web hizmeti giriş_ ve _Çıkış_ modüllerini denemenizin varsayılan konumlarına ekler (Bu modüller Kullanıcı verilerini kabul eder ve döndürür).

Örneğin, aşağıdaki deneme, örnek görselleştirmenizdeki verilerini kullanarak iki sınıf bir önceden maliyetli karar ağacı modeli ister:

![Eğitim denemesi](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

Bu deneydeki modüller temelde dört farklı işlevi gerçekleştirir:

![Modül işlevleri](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Bu eğitim denemesini tahmine dayalı bir deneyle dönüştürdüğünüzde, bu modüllerden bazıları artık gerekli değildir veya artık farklı bir amaç sunar:

* **Veri** -Bu örnek veri kümesindeki veriler Puanlama sırasında kullanılmaz; Web hizmeti kullanıcısı puanlanmayacak verileri sağlar. Ancak, veri türleri gibi bu veri kümesindeki meta veriler eğitilen model tarafından kullanılır. Bu nedenle, bu meta verileri sağlayabilmesi için veri kümesini tahmine dayalı deneyde tutmanız gerekir.

* **Prep** -Puanlama için gönderilecek kullanıcı verilerine bağlı olarak, bu modüller gelen verileri işlemek için gerekli olmayabilir veya olmayabilir. **Web hizmeti ayarlama** düğmesine dokunmaz. bunları nasıl işlemek istediğinize karar vermeniz gerekir.
  
    Örneğin, bu örnekte örnek veri kümesinde eksik değerler olabilir, bu nedenle bunlarla başa çıkmak için bir [Temizleme eksik veri][clean-missing-data] modülü eklenmiştir. Ayrıca, örnek veri kümesi, modeli eğitmek için gerekli olmayan sütunları içerir. Bu nedenle, veri akışından bu ek sütunları hariç tutmak için DataSet modülünde bir [Select sütunları][select-columns] eklenmiştir. Puanlama için Web hizmeti üzerinden gönderilecek verilerin eksik değerlere sahip olmadığını biliyorsanız, [eksik veri modülünü Temizleme][clean-missing-data] seçeneğini kaldırabilirsiniz. Ancak, [veri kümesi modülündeki sütunları seç][select-columns] , eğitilen modelin beklediği veri sütunlarını tanımlamaya yardımcı olduğundan, Bu modülün kalması gerekir.

* **Eğitme** -bu modüller modeli eğiteiçin kullanılır. **Web hizmeti ayarla**' ya tıkladığınızda, bu modüller, eğitilen modeli içeren tek bir modülle değiştirilmiştir. Bu yeni modül, modül paleti 'nin **eğitilen modeller** bölümüne kaydedilir.

* **Puan** -Bu örnekte, veri akışını test verilerine ve eğitim verilerine bölmek Için [bölünmüş veri][split] modülü kullanılır. Tahmine dayalı deneyde artık eğitim duymuyoruz, bu nedenle [bölünmüş veriler][split] kaldırılabilir. Benzer şekilde, ikinci [puan modeli][score-model] modülü ve [modeli değerlendir][evaluate-model] modülü, sonuçları test verileriyle karşılaştırmak için kullanılır; bu nedenle, bu modüller tahmine dayalı deneyde gerekli değildir. Ancak, kalan [puan modeli][score-model] modülü, Web hizmeti aracılığıyla bir puan sonucu döndürmek için gereklidir.

Aşağıda, **Web hizmeti ayarla**' yı tıkladıktan sonra örneğimiz şöyle görünür:

![Dönüştürülmüş tahmine dayalı deneme](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

**Web hizmeti ayarlama** tarafından gerçekleştirilen iş, denemenizi bir Web hizmeti olarak dağıtılacak şekilde hazırlamak yeterli olabilir. Ancak, denemenize özgü bazı ek işler yapmak isteyebilirsiniz.

### <a name="adjust-input-and-output-modules"></a>Giriş ve çıkış modüllerini ayarlama
Eğitim denemenizin deneme sürümünde bir dizi eğitim verisi kullandınız ve daha sonra Machine Learning algoritmasının gerek duyduğu bir formdaki verileri almak için bazı işlemler gerçekleştirirsiniz. Web hizmeti üzerinden almak istediğiniz verilerin bu işleme ihtiyacı yoksa, bu işlemi atlayabilirsiniz: **Web Hizmeti Giriş modülünün** çıkışını denemenizin farklı bir modülüne bağlama. Kullanıcının verileri şimdi bu konumdaki modele ulaşacaktır.

Örneğin, varsayılan olarak, Web **hizmeti kurulumu** , yukarıdaki şekilde gösterildiği gibi, **Web hizmeti giriş** modülünü veri akışınızı en üst kısmına koyar. Ancak, **Web hizmeti girişini** veri işleme modüllerinden geçmiş olarak el ile konumlandırabiliriz:

![Web hizmeti girişini taşıma](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

Web hizmeti üzerinden sunulan giriş verileri artık ön işleme olmadan doğrudan puan modeli modülüne geçirilecek.

Benzer şekilde, varsayılan olarak, Web **hizmeti kurulumu** , Web hizmeti çıkış modülünü veri akışınız altına koyar. Bu örnekte, Web hizmeti, tüm giriş verisi vektörünü ve Puanlama sonuçlarını içeren, kullanıcıya [puan modeli][score-model] modülünün çıktısını döndürür.
Ancak, farklı bir şey döndürmeyi tercih ediyorsanız, **Web hizmeti çıkış** modülünden önce ek modüller ekleyebilirsiniz. 

Örneğin, tüm giriş verileri vektörünü değil yalnızca Puanlama sonuçlarını döndürmek için, Puanlama sonuçları hariç tüm sütunları hariç tutmak için DataSet modülüne bir [Select sütunları][select-columns] ekleyin. Daha sonra **Web hizmeti çıkış** modülünü [veri kümesi modülündeki sütunları seçme][select-columns] modülüne taşıyın. Deneme şöyle görünür:

![Web hizmeti çıkışını taşıma](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>Ek veri işleme modülleri ekleme veya kaldırma
Denemenizin Puanlama sırasında gerekli olacağını bildiğiniz daha fazla modül varsa, bunlar kaldırılabilir. Örneğin, **Web hizmeti giriş** modülünü veri işleme modüllerinden sonra bir noktaya taşıdığımızda, [eksik veri][clean-missing-data] modülünü tahmine dayalı deneyden kaldırabiliriz.

Tahmine dayalı deneyimiz şu şekilde görünür:

![Ek modül kaldırılıyor](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>İsteğe bağlı Web hizmeti parametreleri Ekle
Bazı durumlarda, hizmet erişildiğinde Web hizmetinizin kullanıcısına modüllerin davranışını değiştirmesine izin vermek isteyebilirsiniz. *Web hizmeti parametreleri* bunu yapmanıza olanak sağlar.

Ortak bir örnek, bir [Içeri aktarma verileri][import-data] modülü, dağıtılmış Web hizmeti kullanıcısının Web hizmetine erişildiğinde farklı bir veri kaynağı belirtmesini sağlayacak şekilde ayarlıyor. Veya [dışarı aktarma veri][export-data] modülünü farklı bir hedef belirtime için yapılandırma.

Web hizmeti parametreleri tanımlayabilir ve bunları bir veya daha fazla modül parametresiyle ilişkilendirebilirsiniz ve bunların gerekli veya isteğe bağlı olup olmadığını belirtebilirsiniz. Web hizmetinin kullanıcısı, hizmete erişildiğinde bu parametrelerin değerlerini sağlar ve modül eylemleri buna göre değiştirilir.

Web hizmeti parametrelerinin ne olduğu ve nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [Azure Machine Learning Web hizmeti parametrelerini kullanma][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Tahmine dayalı denemeyi Web hizmeti olarak dağıtma
Tahmine dayalı deneme yeterli şekilde hazırlandığına göre, bunu Azure Web hizmeti olarak dağıtabilirsiniz. Kullanıcılar, Web hizmetini kullanarak modelinize veri gönderebilir ve model tahmine dayalı olarak döndürülür.

Dağıtım işlemi hakkında daha fazla bilgi için bkz. [Azure Machine Learning Web hizmeti dağıtma][deploy]

[deploy]: deploy-a-machine-learning-web-service.md

<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
