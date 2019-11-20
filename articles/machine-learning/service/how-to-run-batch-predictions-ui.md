---
title: Azure Machine Learning Designer (Önizleme) kullanarak toplu tahmine dayalı tahminleri çalıştırma
titleSuffix: Azure Machine Learning
description: Tasarımcıyı kullanarak bir modeli eğitme ve Batch tahmini işlem hattı ayarlama hakkında bilgi edinin. İşlem hattını, herhangi bir HTTP kitaplığından tetiklenebilecek parametreli bir Web hizmeti olarak dağıtın.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye
ms.author: trbye
author: trevorbye
ms.date: 11/04/2019
ms.custom: Ignite2019
ms.openlocfilehash: c496e57ea5f4b5b7fcda4f9c43c511488ef3c246
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501494"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer"></a>Azure Machine Learning tasarımcısını kullanarak toplu tahminleri Çalıştır
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu nasıl yapılır, bir modeli eğitme ve Batch tahmin işlem hattı ve Web hizmeti ayarlama hakkında tasarımcı 'yı nasıl kullanacağınızı öğrenirsiniz. Toplu tahmin, isteğe bağlı olarak, herhangi bir HTTP kitaplığından tetiklenebilecek bir Web hizmeti olarak yapılandırılmış, büyük veri kümelerinde sürekli ve isteğe bağlı eğitilen modellerin kullanımını sağlar. 

SDK 'Yı kullanarak Batch Puanlama hizmetlerini ayarlamak için, bkz. [nasıl yapılır](how-to-run-batch-predictions.md).

Bu nasıl yapılır bölümünde aşağıdaki görevleri öğrenirsiniz:

> [!div class="checklist"]
> * İşlem hattında temel ML denemesi oluşturma
> * Parametreli yığın çıkarım ardışık düzeni oluşturma
> * İşlem hatlarını el ile veya bir REST uç noktasından yönetin ve çalıştırın

## <a name="prerequisites"></a>Önkoşullar

1. Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning hizmetinin ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree)deneyin.

1. [Çalışma alanı](tutorial-1st-experiment-sdk-setup.md)oluşturun.

1. [Azure Machine Learning Studio](https://ml.azure.com/)'da oturum açın.

Bu nasıl yapılır, tasarımcıda basit bir işlem hattı oluşturma hakkında temel bilgileri varsayar. Tasarımcıya yönelik kılavuzlu bir giriş için [öğreticiyi](tutorial-designer-automobile-price-train-score.md)doldurun. 

## <a name="create-a-pipeline"></a>İşlem hattı oluşturma

Bir toplu çıkarım işlem hattı oluşturmak için önce bir makine öğrenimi denemesinin olması gerekir. Bir tane oluşturmak için, çalışma alanınızdaki **Tasarımcı** sekmesine gidin ve kullanımı **kolay önceden oluşturulmuş modüller** seçeneğini belirleyerek yeni bir işlem hattı oluşturun.

![Tasarımcı ana sayfası](media/how-to-run-batch-predictions-ui/ui-batch-scoring-1.png)

Aşağıda, tanıtım amacıyla basit bir makine öğrenimi modeli verilmiştir. Veriler, Azure açık veri kümeleri diabetes verilerinden oluşturulan kayıtlı bir veri kümesidir. Azure açık veri kümelerinde veri kümelerini kaydetme [hakkında nasıl yapılır bölümüne](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets) bakın. Veriler eğitim ve doğrulama kümelerine bölünür ve artırılmış bir karar ağacı eğitilmiş ve puanlanır. Bir ının bir işlem hattı oluşturmak için işlem hattının en az bir kez çalıştırılması gerekir. İşlem hattını çalıştırmak için **Çalıştır** düğmesine tıklayın.

![Basit deneme oluşturma](media/how-to-run-batch-predictions-ui/ui-batch-scoring-2.png)

## <a name="create-a-batch-inference-pipeline"></a>Toplu çıkarım ardışık düzeni oluşturma

İşlem hattı çalıştırıldığına göre, **Çalıştır** ve **Yayımla** adlı **çıkarım ardışık düzeni oluşturma**' nın yanında yeni bir seçenek mevcuttur. Açılan listeye tıklayın ve **Batch çıkarım işlem hattı**' nı seçin.

![Toplu çıkarım ardışık düzeni oluşturma](media/how-to-run-batch-predictions-ui/ui-batch-scoring-5.png)

Sonuç, varsayılan bir Batch çıkarım ardışık düzeni olur. Bu, özgün işlem hattı denemenize yönelik bir düğüm, Puanlama için ham veriler için bir düğüm ve ham verilerin özgün işlem hattınızda elde edilecek bir düğüm içerir.

![Varsayılan toplu çıkarım ardışık düzeni](media/how-to-run-batch-predictions-ui/ui-batch-scoring-6.png)

Toplu iş örneği işleme işleminin davranışını değiştirmek için başka düğümler ekleyebilirsiniz. Bu örnekte, Puanlama öncesinde giriş verilerinden rastgele örnekleme için bir düğüm eklersiniz. Bir **bölüm ve örnek** düğüm oluşturun ve ham veriler ile Puanlama düğümleri arasına yerleştirin. Ardından, ayarlar ve parametrelere erişim kazanmak için **bölüm ve örnek** düğümüne tıklayın.

![Yeni düğüm](media/how-to-run-batch-predictions-ui/ui-batch-scoring-7.png)

*Örnekleme parametresi oranı* , özgün veri kümesinin ne kadarlık bir rastgele örnek almak için ayarlandığını denetler. Bu, sıklıkla ayarlamak için yararlı olacak bir parametredir, bu nedenle onu bir işlem hattı parametresi olarak etkinleştirmeniz gerekir. İşlem hattı parametreleri çalışma zamanında değiştirilebilir ve bir REST uç noktasından işlem hattı yeniden çalıştırıldığında bir yük nesnesi içinde belirlenebilir. 

Bu alanı bir ardışık düzen parametresi olarak etkinleştirmek için alanın üzerindeki üç noktaya tıklayın ve ardından **ardışık düzen parametresine Ekle**' ye tıklayın. 

![Örnek ayarlar](media/how-to-run-batch-predictions-ui/ui-batch-scoring-8.png)

Sonra, parametreye bir ad ve varsayılan değer verin. Ad, parametreyi tanımlamak ve bir REST çağrısında belirtmek için kullanılır.

![İşlem hattı parametresi](media/how-to-run-batch-predictions-ui/ui-batch-scoring-9.png)

## <a name="deploy-batch-inferencing-pipeline"></a>Toplu iş ve bölge zinciri dağıtma

Artık işlem hattını dağıtmaya hazırsınız. Bir uç nokta ayarlamak için arabirimi açan **Dağıt** düğmesine tıklayın. Açılan listeye tıklayın ve **Yeni PipelineEndpoint**' i seçin.

![İşlem hattı dağıtımı](media/how-to-run-batch-predictions-ui/ui-batch-scoring-10.png)

Uç noktaya bir ad ve isteğe bağlı bir açıklama sağlayın. En alttaki 0,8 varsayılan değeriyle yapılandırdığınız `sample-rate` parametresini görürsünüz. Hazırlanıyor, **Dağıt**' a tıklayın.

![Kurulum uç noktası](media/how-to-run-batch-predictions-ui/ui-batch-scoring-11.png)

## <a name="manage-endpoints"></a>Uç noktaları yönetme 

Dağıtım tamamlandıktan sonra, **uç noktalar** sekmesine gidin ve yeni oluşturduğunuz uç noktanın adına tıklayın.

![Uç nokta bağlantısı](media/how-to-run-batch-predictions-ui/ui-batch-scoring-12.png)

Bu ekran, belirli bir uç nokta altındaki tüm yayınlanan işlem hatlarını gösterir. Inizme işlem hattınızı tıklatın.

![Çıkarım işlem hattı](media/how-to-run-batch-predictions-ui/ui-batch-scoring-13.png)

İşlem hattı ayrıntıları sayfasında, işlem hattınızla ilgili ayrıntılı çalıştırma geçmişi ve bağlantı dizesi bilgileri görüntülenir. İşlem hattının el ile çalıştırılmasını oluşturmak için **Çalıştır** düğmesine tıklayın.

![Ardışık düzen ayrıntıları](media/how-to-run-batch-predictions-ui/ui-batch-scoring-14.png)

Çalıştır kurulumunda, çalıştırma için bir açıklama sağlayabilir ve herhangi bir işlem hattı parametresi için değeri değiştirebilirsiniz. Bu kez, örnek işleme işlem hattını 0,9 örnek oranıyla yeniden çalıştırın. İşlem hattını çalıştırmak için **Çalıştır** ' a tıklayın.

![İşlem hattı çalıştırması](media/how-to-run-batch-predictions-ui/ui-batch-scoring-15.png)

**Tüketme** sekmesi, işlem hattınızı yeniden ÇALıŞTıRMAYA yönelik REST uç noktasını içerir. Rest çağrısı yapmak için bir OAuth 2,0 taşıyıcı türü kimlik doğrulama üst bilgisi gerekir. Çalışma alanınıza yönelik kimlik doğrulamasını ayarlama ve parametreli bir REST çağrısı yapma hakkında daha fazla ayrıntı için aşağıdaki [öğretici bölümüne](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bir regresyon modeli eğitme ve dağıtma için tasarımcı [öğreticisini](tutorial-designer-automobile-price-train-score.md) izleyin.