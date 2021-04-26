---
title: 'ML Studio (klasik): Azure Machine Learning geçirme-Web hizmetini yeniden oluşturma'
description: Studio (klasik) Web hizmetlerini Azure Machine Learning işlem hattı uç noktaları olarak yeniden oluşturun
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 35ee5bf22aa88c18bade0ebdcd961b7687d24e7f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311830"
---
# <a name="rebuild-a-studio-classic-web-service-in-azure-machine-learning"></a>Azure Machine Learning ' de bir Studio (klasik) Web hizmetini yeniden oluşturma

Bu makalede, bir Studio (klasik) Web hizmetini Azure Machine Learning bir **uç nokta** olarak yeniden oluşturmayı öğreneceksiniz.

Tahmin yapmak, modelleri yeniden eğitme veya herhangi bir genel işlem hattı çalıştırmak için Azure Machine Learning işlem hattı uç noktaları kullanın. REST uç noktası, herhangi bir platformda işlem hatlarını çalıştırmanızı sağlar. 

Bu makale, geçiş serisine Azure Machine Learning için Studio 'nun (klasik) bir parçasıdır. Azure Machine Learning geçirme hakkında daha fazla bilgi için bkz. [geçişe genel bakış makalesi](migrate-overview.md).

> [!NOTE]
> Bu geçiş serisi, sürükle ve bırak tasarımcısına odaklanır. Modelleri program aracılığıyla dağıtma hakkında daha fazla bilgi için bkz. [Azure 'da makine öğrenimi modellerini dağıtma](../how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure Machine Learning çalışma alanı. [Azure Machine Learning çalışma alanı oluşturun](../how-to-manage-workspace.md#create-a-workspace).
- Azure Machine Learning eğitim işlem hattı. Daha fazla bilgi için bkz. [Azure Machine Learning bir Studio (klasik) denemesini yeniden oluşturma](migrate-rebuild-experiment.md).

## <a name="real-time-endpoint-vs-pipeline-endpoint"></a>Gerçek zamanlı uç nokta vs ardışık düzen uç noktası

Studio (klasik) Web Hizmetleri, Azure Machine Learning **uç** noktalarıyla değiştirilmiştir. Kullanılacak uç nokta türünü seçmek için aşağıdaki tabloyu kullanın:

|Studio (klasik) Web hizmeti| Azure Machine Learning değiştirme
|---|---|
|Web hizmeti iste/Yanıtla (gerçek zamanlı tahmin)|Gerçek zamanlı uç nokta|
|Batch Web hizmeti (Batch tahmini)|Ardışık düzen uç noktası|
|Web hizmetini yeniden eğitme (yeniden eğitim)|Ardışık düzen uç noktası| 


## <a name="deploy-a-real-time-endpoint"></a>Gerçek zamanlı uç nokta dağıtma

Studio 'da (klasik), gerçek zamanlı tahminler için bir model dağıtmak üzere bir **istek/yanıt Web hizmeti** kullandınız. Azure Machine Learning, gerçek zamanlı bir **uç nokta** kullanırsınız.

Azure Machine Learning modeli dağıtmanın birden çok yolu vardır. En basit yöntemlerinden biri, tasarımcı kullanarak dağıtım işlemini otomatikleştirebilir. Bir modeli gerçek zamanlı uç nokta olarak dağıtmak için aşağıdaki adımları kullanın:

1. Tamamlanan eğitim işlem hattınızı en az bir kez çalıştırın.
1. Çalıştırma tamamlandıktan sonra, tuvalin en üstünde, **çıkarım işlem hattı**  >  **gerçek zamanlı çıkarım işlem hattı** oluştur ' u seçin.

    ![gerçek zamanlı çıkarım ardışık düzeni oluşturma](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    Tasarımcı, eğitim işlem hattını gerçek zamanlı bir çıkarım ardışık düzenine dönüştürür. Benzer bir dönüştürme de Studio 'da (klasik) oluşur.

    Tasarımcıda, dönüştürme adımı aynı zamanda [eğitilen modeli Azure Machine Learning çalışma alanınıza kaydeder](../how-to-deploy-and-where.md#registermodel).

1. Gerçek zamanlı çıkarım ardışık düzenini çalıştırmak için **Gönder** ' i seçin ve başarıyla çalıştığını doğrulayın.

1. Çıkarım ardışık düzenini doğruladıktan sonra **Dağıt**' ı seçin.

1. Uç noktanız ve işlem türü için bir ad girin.

    Aşağıdaki tabloda, tasarımcıda dağıtım işlem seçenekleriniz açıklanmaktadır:

    | İşlem hedefi | Kullanıldığı yerler | Açıklama | Oluşturma |
    | ----- |  ----- | ----- | -----  |
    |[Azure Kubernetes Service (AKS)](../how-to-deploy-azure-kubernetes-service.md) |Gerçek zamanlı çıkarım|Büyük ölçekli, üretim dağıtımları. Hızlı yanıt süresi ve hizmet otomatik ölçeklendirme.| Kullanıcı tarafından oluşturulan. Daha fazla bilgi için bkz. [işlem hedefleri oluşturma](../how-to-create-attach-compute-studio.md#inference-clusters). |
    |[Azure Container Instances ](../how-to-deploy-azure-container-instance.md)|Test veya geliştirme | 48 GB 'den az RAM gerektiren küçük ölçekli, CPU tabanlı iş yükleri.| Azure Machine Learning tarafından otomatik olarak oluşturuldu.

### <a name="test-the-real-time-endpoint"></a>Gerçek zamanlı uç noktayı test etme

Dağıtım tamamlandıktan sonra, daha fazla ayrıntı görebilir ve uç noktanıza test edebilirsiniz:

1. **Uç noktalar** sekmesine gidin.
1. Uç nokta seçin.
1. **Test** sekmesini seçin.
    
    ![Test uç noktası düğmesi ile uç noktalar sekmesini gösteren ekran görüntüsü](./media/migrate-rebuild-web-service/test-realtime-endpoint.png)

## <a name="publish-a-pipeline-endpoint-for-batch-prediction-or-retraining"></a>Toplu tahmin veya yeniden eğitim için bir ardışık düzen uç noktası yayımlayın

Eğitim işlem hattınızı gerçek zamanlı bir uç nokta yerine bir **ardışık düzen uç noktası** oluşturmak için de kullanabilirsiniz. Toplu işlem tahmini veya yeniden eğitim gerçekleştirmek için **ardışık düzen uç noktaları** kullanın.

İşlem hattı uç noktaları, Studio (klasik) **toplu yürütme uç noktaları**  ve **yeniden eğitim Web Hizmetleri** yerine değiştirilir.

### <a name="publish-a-pipeline-endpoint-for-batch-prediction"></a>Toplu tahmin için bir işlem hattı uç noktası yayımlama

Toplu bir tahmin uç noktası yayımlandığında gerçek zamanlı uç noktaya benzer.

Toplu tahmin için bir ardışık düzen uç noktası yayımlamak için aşağıdaki adımları kullanın:

1. Tamamlanan eğitim işlem hattınızı en az bir kez çalıştırın.

1. Çalıştırma tamamlandıktan sonra, tuvalin en üstünde, **çıkarım ardışık düzen**  >  **Batch çıkarımı işlem hattı**' nı seçin.

    ![Eğitim ardışık düzeninde çıkarım ardışık düzeni oluştur düğmesini gösteren ekran görüntüsü](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    Tasarımcı, eğitim işlem hattını bir toplu çıkarım ardışık düzenine dönüştürür. Benzer bir dönüştürme de Studio 'da (klasik) oluşur.

    Tasarımcıda Bu adım Ayrıca [eğitilen modeli Azure Machine Learning çalışma alanınıza kaydeder](../how-to-deploy-and-where.md#registermodel).

1. Toplu çıkarım ardışık düzenini çalıştırmak ve başarıyla tamamlandığını doğrulamak için **Gönder** ' i seçin.

1. Çıkarım ardışık düzenini doğruladıktan sonra **Yayımla**' yı seçin.
 
1. Yeni bir ardışık düzen uç noktası oluşturun veya var olan bir işlem hattını seçin.
    
    Yeni bir işlem hattı uç noktası, işlem hattınızda yeni bir REST uç noktası oluşturur. 

    Mevcut bir ardışık düzen uç noktası seçerseniz, mevcut işlem hattının üzerine yazılmaz. Bunun yerine, uç noktadaki her bir ardışık düzenin sürümlerini Azure Machine Learning. REST çağrınızdaki hangi sürümün çalıştırılacağını belirtebilirsiniz. REST çağrısı bir sürüm belirtmezse, varsayılan bir işlem hattı da ayarlamanız gerekir.


 ### <a name="publish-a-pipeline-endpoint-for-retraining"></a>Yeniden eğitim için bir ardışık düzen uç noktası yayımlama

Yeniden eğitim için bir ardışık düzen uç noktası yayımlamak için, bir modeli gösteren bir işlem hattı taslağınızı zaten kullanmalısınız. Eğitim işlem hattı oluşturma hakkında daha fazla bilgi için bkz. [bir Studio (klasik) denemesini yeniden derleme](migrate-rebuild-experiment.md).

Yeniden eğitim için ardışık düzen uç noktanızı yeniden kullanmak için, giriş veri kümeniz için bir **ardışık düzen parametresi** oluşturmanız gerekir. Bu sayede, modelinize yeniden eğitebilmeniz için eğitim veri kümenizi dinamik olarak ayarlamanıza olanak tanır.

Yeniden eğitme ardışık düzen uç noktasını yayımlamak için aşağıdaki adımları kullanın:

1. Eğitim işlem hattınızı en az bir kez çalıştırın.
1. Çalıştırma tamamlandıktan sonra veri kümesi modülünü seçin.
1. Modül ayrıntıları bölmesinde **ardışık düzen parametresi olarak ayarla**' yı seçin.
1. "Inputdataset" gibi açıklayıcı bir ad girin.    

    ![İşlem hattı parametresinin nasıl oluşturulacağını gösteren ekran görüntüsü](./media/migrate-rebuild-web-service/create-pipeline-parameter.png)

    Bu, giriş veri kümeniz için bir ardışık düzen parametresi oluşturur. Eğitim için ardışık düzen uç noktanızı çağırdığınızda, modeli yeniden eğitebilmeniz için yeni bir veri kümesi belirtebilirsiniz.

1. **Yayımla**’yı seçin.

    ![Eğitim ardışık düzeninde Yayımla düğmesini vurgulayan ekran görüntüsü](./media/migrate-rebuild-web-service/create-retraining-pipeline.png)


## <a name="call-your-pipeline-endpoint-from-the-studio"></a>Studio 'dan işlem hattı uç noktanızı çağırma

Yığın çıkarımı veya yeniden eğitim ardışık düzen uç noktasını oluşturduktan sonra, uç noktanızı doğrudan tarayıcınızdan çağırabilirsiniz.

1. İşlem **hatları** sekmesine gidin ve **ardışık düzen uç noktaları**' nı seçin.
1. Çalıştırmak istediğiniz ardışık düzen uç noktasını seçin.
1. **Gönder**’i seçin.

    **Gönder**' i seçtikten sonra herhangi bir işlem hattı parametresini belirtebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Machine Learning ' de bir Studio (klasik) Web hizmetini yeniden oluşturmayı öğrendiniz. Sonraki adım, [Web hizmetinizi istemci uygulamalarıyla tümleştirmelidir](migrate-rebuild-integrate-with-client-app.md).


Studio (klasik) geçiş serisinde diğer makalelere bakın:

1. [Geçişe genel bakış](migrate-overview.md).
1. [Veri kümesini geçirin](migrate-register-dataset.md).
1. [Bir Studio (klasik) eğitim işlem hattını yeniden derleyin](migrate-rebuild-experiment.md).
1. **Bir Studio (klasik) Web hizmetini yeniden derleyin**.
1. [İstemci uygulamalarıyla bir Azure Machine Learning Web hizmetini tümleştirin](migrate-rebuild-integrate-with-client-app.md).
1. [Execute R betiğini geçirin](migrate-execute-r-script.md).
