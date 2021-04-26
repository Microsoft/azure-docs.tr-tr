---
title: Tasarımcıda modelleri yeniden eğitme işlem hattı parametrelerini kullanma
titleSuffix: Azure Machine Learning
description: Azure Machine Learning tasarımcısında yayımlanmış işlem hatları ve işlem hattı parametreleriyle modelleri yeniden eğitme.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 03/06/2021
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: ed5f59fc1d751f92a7c016aa3586497659f778fd
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962960"
---
# <a name="use-pipeline-parameters-to-retrain-models-in-the-designer"></a>Tasarımcıda modelleri yeniden eğitme işlem hattı parametrelerini kullanma


Bu nasıl yapılır makalesinde, işlem hattı parametrelerini kullanarak makine öğrenimi modelini yeniden eğitme Azure Machine Learning tasarımcısını nasıl kullanacağınızı öğreneceksiniz. İş akışınızı otomatikleştirmek için yayımlanmış işlem hatlarını kullanacaksınız ve modelinizi yeni verilerde eğtirecek parametreleri ayarlayacaksınız. İşlem hattı parametreleri, farklı işler için mevcut işlem hatlarını yeniden kullanmanıza olanak sağlar.  

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Makine öğrenimi modelini eğitme.
> * İşlem hattı parametresi oluşturun.
> * Eğitim işlem hattınızı yayımlayın.
> * Modelinize yeni parametrelerle yeniden eğitme.

## <a name="prerequisites"></a>Önkoşullar

* Azure Machine Learning çalışma alanı
* Bu nasıl yapılır serisinin 1. kısmını, [tasarımcıda veri dönüştürme](how-to-designer-transform-data.md)

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

Bu makalede ayrıca tasarımcı 'da işlem hattı oluşturma hakkında bilgi sahibi olduğunuz varsayılır. Kılavuzlu bir giriş için [öğreticiyi](tutorial-designer-automobile-price-train-score.md)izleyin. 

### <a name="sample-pipeline"></a>Örnek işlem hattı

Bu makalede kullanılan işlem hattı, tasarımcı giriş hattının örnek bir ardışık düzen [geliri tahmini](samples-designer.md#classification) sürümü olarak değiştirilmiştir. İşlem hattı, kendi verilerinizi kullanarak modellerinizi nasıl eğitekullanacağınızı göstermek için örnek veri kümesi yerine [veri alma](algorithm-module-reference/import-data.md) modülünü kullanır.

![Değiştirilen örnek işlem hattını, verileri Içeri aktarma modülünü vurgulama kutusuyla gösteren ekran görüntüsü](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>İşlem hattı parametresi oluşturma

İşlem hattı parametreleri, daha sonra değişen parametre değerleriyle yeniden gönderilebilir, çok yönlü işlem hatları oluşturmak için kullanılır. Bazı yaygın senaryolar, yeniden eğitim için veri kümelerini veya bazı Hyper-parametrelerini güncelleştiriyor. Çalışma zamanında değişkenleri dinamik olarak ayarlamak için işlem hattı parametreleri oluşturun. 

İşlem hattında veri kaynağına veya modül parametrelerine işlem hattı parametreleri eklenebilir. İşlem hattı yeniden gönderdiğinizde, bu parametrelerin değerleri belirtilebilir.

Bu örnekte, modelinizi farklı verilerde yeniden eğitebilmeniz için sabit bir değerden bir parametreye eğitim verileri yolunu değiştirirsiniz. Ayrıca, diğer modül parametrelerini kullanım çalışmanıza göre işlem hattı parametreleri olarak ekleyebilirsiniz.

1. **Veri Içeri aktarma** modülünü seçin.

    > [!NOTE]
    > Bu örnek, kayıtlı bir veri deposundaki verilere erişmek için veri alma modülünü kullanır. Ancak, alternatif veri erişim desenleri kullanıyorsanız benzer adımları izleyebilirsiniz.

1. Sağ taraftaki modül ayrıntısı bölmesinde, veri kaynağınızı seçin.

1. Verilerinizin yolunu girin. Ayrıca dosya ağacınızı taramak için, **yolu araştır** ' ı seçebilirsiniz. 

1. **Yol** alanı üzerinde Mouseve görüntülenen **yol** alanının üzerindeki üç noktayı seçin.

1. **Ardışık düzen parametresine Ekle**' yi seçin.

1. Bir parametre adı ve varsayılan değer sağlayın.

   ![İşlem hattı parametresinin nasıl oluşturulacağını gösteren ekran görüntüsü](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. **Kaydet**’i seçin.

   > [!NOTE]
   > Ayrıca, işlem hattı parametreleri eklemeye benzer şekilde modül ayrıntısı bölmesindeki işlem hattı parametresinden bir modül parametresini ayırabilirsiniz.
   >
   > İşlem hattı taslağınız başlığının yanındaki **Ayarlar** dişli simgesini seçerek işlem hattı parametrelerinizi inceleyebilir ve düzenleyebilirsiniz. 
   >    - Ayrıldıktan sonra, işlem hattı parametresini, **setler** bölmesinde silebilirsiniz.
   >    - Ayrıca, **Ayarlar** bölmesine bir işlem hattı parametresi ekleyebilir ve bu parametreyi bazı modül parametresine uygulayabilirsiniz.

1. İşlem hattı çalıştırmasını gönder.

## <a name="publish-a-training-pipeline"></a>Eğitim işlem hattı yayımlama

İşlem hatlarınızı ileride kolayca yeniden kullanmak için bir ardışık düzen uç noktasına bir işlem hattı yayımlayın. Ardışık düzen uç noktası, gelecekteki işlem hattını çağırmak için bir REST uç noktası oluşturur. Bu örnekte, işlem hattı uç noktanız, farklı verilerde bir modeli yeniden eğitebilmeniz için işlem hattınızı yeniden kullanmanıza olanak tanır.

1. Tasarımcı tuvalinin üzerinde **Yayımla** ' yı seçin.
1. Ardışık düzen uç noktası seçin veya oluşturun.

   > [!NOTE]
   > Tek bir uç noktada birden çok işlem hattı yayımlayabilirsiniz. Belirli bir uç noktadaki her işlem hattına, ardışık düzen uç noktasını çağırdığınızda belirtebileceğiniz bir sürüm numarası verilir.

1. **Yayımla**’yı seçin.

## <a name="retrain-your-model"></a>Modelinizi yeniden eğitme

Artık yayımlanmış bir eğitim işlem hattına sahip olduğunuza göre, bunu kullanarak modelinize yeni verileri yeniden eğitebilirsiniz. Çalışan bir ardışık düzen uç noktasından veya programlama yoluyla çalışma gönderebilirsiniz.

### <a name="submit-runs-by-using-the-studio-portal"></a>Çalışmaları, Studio portalını kullanarak gönderme

Studio portalından parametreli bir işlem hattı çalıştırması göndermek için aşağıdaki adımları kullanın:

1. Studio çalışma alanınızdaki **uç noktalar** sayfasına gidin.
1. **Ardışık düzen uç noktaları** sekmesini seçin. Ardından, işlem hattı uç noktanızı seçin.
1. Yayınlanan işlem **hatları** sekmesini seçin. Sonra, çalıştırmak istediğiniz işlem hattı sürümünü seçin.
1. **Gönder**’i seçin.
1. Kurulum iletişim kutusunda, çalıştırma için parametre değerlerini belirtebilirsiniz. Bu örnekte, modelinizi ABD olmayan bir veri kümesi kullanarak eğitebilmeniz için veri yolunu güncelleştirin.

![Tasarımcıda parametreli bir işlem hattı çalıştırmasının nasıl ayarlanacağını gösteren ekran görüntüsü](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Kodu kullanarak çalıştırmaları gönder

Yayımlanan bir işlem hattının REST uç noktasını genel bakış panelinde bulabilirsiniz. Uç noktayı çağırarak, yayımlanan işlem hattını yeniden eğitebilirsiniz.

REST çağrısı yapmak için bir OAuth 2,0 taşıyıcı türü kimlik doğrulama üst bilgisi gerekir. Çalışma alanınıza kimlik doğrulama ayarlama ve parametreli REST çağrısı yapma hakkında bilgi için bkz. [Batch Puanlama için Azure Machine Learning işlem hattı oluşturma](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Tasarımcıyı kullanarak parametreli bir eğitim işlem hattı uç noktası oluşturmayı öğrendiniz.

Tahmine dayalı hale getirmek üzere bir modeli nasıl dağıtacağınızı gösteren kapsamlı bir kılavuz için bkz. bir regresyon modeli eğitme ve dağıtmaya yönelik [Tasarımcı öğreticisi](tutorial-designer-automobile-price-train-score.md) .

SDK kullanarak bir çalıştırma ardışık düzen uç noktasına yayımlama ve gönderme için, [Bu makaleye](how-to-deploy-pipelines.md)bakın.
