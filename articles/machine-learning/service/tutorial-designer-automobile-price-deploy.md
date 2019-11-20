---
title: 'Öğretici: tasarımcı ile makine öğrenimi modeli dağıtma'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning tasarımcısında bir tahmine dayalı analiz çözümü oluşturmayı öğrenin (Önizleme). Sürükle ve bırak modüllerini kullanarak makine öğrenimi modelini eğitme, Puanlama ve dağıtma.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 724a38cb516e5689f817e9ddeaa867b17274971b
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73932038"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>Öğretici: tasarımcı ile makine öğrenimi modeli dağıtma (Önizleme)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Diğer kullanıcılara bunu kullanma şansı vermek için [öğreticinin birinci](tutorial-designer-automobile-price-train-score.md) kısmında geliştirilen tahmine dayalı modeli dağıtabilirsiniz. Birinci bölümde, modelinizi eğitilolursunuz. Şimdi, kullanıcı girişine göre yeni tahmin oluşturma zamanı. Öğreticinin bu bölümünde şunları yapmanız gerekir:

> [!div class="checklist"]
> * Gerçek zamanlı bir çıkarım işlem hattı oluşturun.
> * Bir ınıri sınırlama kümesi oluşturun.
> * Gerçek zamanlı bitiş noktasını dağıtın.
> * Gerçek zamanlı uç noktayı test edin.

## <a name="prerequisites"></a>Önkoşullar

Tasarımcı 'da makine öğrenimi modelinin nasıl eğeceğinizi ve puanlandıralınacağını öğrenmek için [öğreticiden birinin bir kısmını](tutorial-designer-automobile-price-train-score.md) doldurun.

## <a name="create-a-real-time-inference-pipeline"></a>Gerçek zamanlı bir çıkarım işlem hattı oluşturma

İşlem hattınızı dağıtmak için öncelikle eğitim işlem hattını gerçek zamanlı bir çıkarım ardışık düzenine dönüştürmeniz gerekir. Bu işlem, eğitim modüllerini kaldırır ve ınırm istekleri için giriş ve çıkış ekler.

### <a name="create-a-real-time-inference-pipeline"></a>Gerçek zamanlı bir çıkarım işlem hattı oluşturma

1. İşlem hattı tuvalinin üzerinde, **gerçek zamanlı çıkarım işlem hattı** > **çıkarım işlem hattı oluştur** ' u seçin.

    İşlem hatlarınız şu şekilde görünmelidir: 

   ![Dağıtım için hazırlandıktan sonra işlem hattının beklenen yapılandırmasını gösteren ekran görüntüsü](./media/ui-tutorial-automobile-price-deploy/real-time-inference-pipeline.png)

    **Çıkarım işlem hattı oluştur**' u seçtiğinizde birkaç şey meydana gelir:
    
    * Eğitilen model, modül paletinde bir **veri kümesi** modülü olarak depolanır. **Veri kümelerim**altında bulabilirsiniz.
    * Model ve **bölünmüş verileri** **eğitme** gibi eğitim modülleri kaldırılır.
    * Kaydedilen eğitilen model ardışık düzene geri eklenir.
    * **Web hizmeti girişi** ve **Web hizmeti çıkış** modülleri eklendi. Bu modüller, Kullanıcı verilerinin modele girdiği ve verilerin döndürüldüğü yeri gösterir.

    > [!NOTE]
    > *Eğitim işlem hattı* , ardışık düzen tuvalinin en üstündeki yeni sekmenin altına kaydedilir. Ayrıca, tasarımcıda yayımlanmış bir işlem hattı olarak bulunabilir.
    >

1. **Çalıştır**' ı seçin ve aynı işlem hedefini kullanın ve birinci bölümde kullandığınız deneyin.

1. **Puan modeli** modülünü seçin.

1. Özellikler bölmesinde, modelin hala çalıştığını doğrulamak için **çıktılar** > **Görselleştir** ' i seçin. Orijinal verilerin tahmini fiyat ("puanlanmış Etiketler") ile birlikte görüntülendiğini görebilirsiniz.

1. **Dağıt**'ı seçin.

## <a name="create-an-inferencing-cluster"></a>Bir ınıri yalıtma kümesi oluşturma

Görüntülenen iletişim kutusunda, modelinizi dağıtmak için mevcut herhangi bir Azure Kubernetes hizmeti (AKS) kümesi arasından seçim yapabilirsiniz. AKS kümeniz yoksa, bir tane oluşturmak için aşağıdaki adımları kullanın.

1. **İşlem** sayfasına git görüntülenen Iletişim kutusunda **işlem** ' i seçin.

1. Gezinti şeridinde, **çıkarım kümeleri** >  **+ Yeni**' yi seçin.

    ![Yeni çıkarım kümesi bölmesine nasıl alınacağını gösteren ekran görüntüsü](./media/ui-tutorial-automobile-price-deploy/new-inference-cluster.png)

1. Çıkarım kümesi bölmesinde yeni bir Kubernetes hizmeti yapılandırın.

1. **İşlem adı**için *aks-COMPUTE* girin.
    
1. **Bölge**için kullanılabilen bir yakın bölge seçin.

1. **Oluştur**'u seçin.

    > [!NOTE]
    > Yeni bir AKS hizmeti oluşturmak yaklaşık 15 dakika sürer. **Çıkarım kümeleri** sayfasında sağlama durumunu kontrol edebilirsiniz.
    >

## <a name="deploy-the-real-time-endpoint"></a>Gerçek zamanlı uç noktayı dağıtma

AKS hizmetiniz sağlamayı tamamladıktan sonra, dağıtımı tamamlamaya yönelik gerçek zamanlı ıntıma ardışık düzenine geri dönün.

1. Tuvalin üzerinde **Dağıt** ' ı seçin.

1. **Yeni gerçek zamanlı uç noktayı dağıt**' ı seçin. 

1. Oluşturduğunuz AKS kümesini seçin.

1. **Dağıt**'ı seçin.

    ![Yeni bir gerçek zamanlı uç noktanın nasıl ayarlanacağını gösteren ekran görüntüsü](./media/ui-tutorial-automobile-price-deploy/setup-endpoint.png)

    Dağıtım bittikten sonra tuvalin üzerindeki başarı bildirimi görüntülenir. İşlem birkaç dakika sürebilir.

## <a name="test-the-real-time-endpoint"></a>Gerçek zamanlı uç noktayı test etme

Dağıtım bittikten sonra, **uç noktalar** sayfasına giderek gerçek zamanlı uç noktanızı test edebilirsiniz.

1. **Uç noktalar** sayfasında, dağıttığınız uç noktayı seçin.

    ![Son oluşturulan bitiş noktası vurgulanmış şekilde gerçek zamanlı uç noktalar sekmesini gösteren ekran görüntüsü](./media/ui-tutorial-automobile-price-deploy/endpoints.png)

1. **Test**' i seçin.

1. Test verilerini el ile belirtebilir veya oto dolgulu örnek verileri kullanabilir ve **Test**' i seçebilirsiniz.

    Portal, uç noktaya bir test isteği gönderir ve sonuçları gösterir. Giriş verileri için bir fiyat değeri oluşturulsa da tahmin değeri oluşturmak için kullanılmaz.

    ![Fiyat vurgulanmış olarak gerçek zamanlı uç noktanın puanlanmış etiketle nasıl test leneceğini gösteren ekran görüntüsü](./media/ui-tutorial-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, tasarımcıda makine öğrenimi modeli oluşturma, dağıtma ve kullanma bölümündeki temel adımları öğrendiniz. Diğer sorun türlerini çözümlemek için tasarımcıyı nasıl kullanabileceğiniz hakkında daha fazla bilgi edinmek için diğer örnek işlem hatlarımıza bakın.

> [!div class="nextstepaction"]
> [Kredi riski sınıflandırma örneği](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
