---
title: Web Hizmetleri için Excel eklentisi
titleSuffix: ML Studio (classic) - Azure
description: Herhangi bir kod yazmadan Azure Machine Learning Web hizmetlerini doğrudan Excel 'de kullanma.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: b27e1c498aab3cc6c14eaa8a5851c9262535e95a
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837794"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>Azure Machine Learning Studio (klasik) Web Hizmetleri için Excel eklentisi
Excel, herhangi bir kod yazmaya gerek kalmadan Web hizmetlerini doğrudan çağırmayı kolaylaştırır.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Çalışma kitabında var olan bir Web hizmetini kullanma adımları

1. Excel eklentisini ve Titanic üzerindeki pastıcılar hakkındaki verileri içeren [örnek Excel dosyasını](https://aka.ms/amlexcel-sample-2)açın. 
 
    > [!NOTE]
    > Dosya ile ilgili Web hizmetlerinin listesini ve "otomatik tahmin" için en altta bir onay kutusunu görürsünüz. Otomatik tahmin ' i etkinleştirirseniz, **Tüm** hizmetlerinizin tahminleri, girişte her değişiklik olduğunda güncelleştirilir. İşaretlenmezse, yenileme için "tümünü tahmin etme" seçeneğine tıklamanız gerekir. Hizmet düzeyinde otomatik ön tahmin sağlamak için 6. adıma gidin.

2. Bu örnekte, "Titanic acil vor Predictor (Excel Eklentisi örneği) [Score]" öğesine tıklayarak Web hizmetini seçin.
   
    ![Web hizmeti seçin](./media/excel-add-in-for-web-services/image1.png)
3. Bu sizi **tahmin** etme bölümüne götürür.  Bu çalışma kitabı zaten örnek veriler içeriyor, ancak boş bir çalışma kitabı için Excel 'de bir hücre seçip **örnek verileri kullan**' a tıklayabilirsiniz.
4. Üst bilgileri olan verileri seçin ve giriş veri aralığı simgesine tıklayın.  "My Data Headers" kutusunun işaretli olduğundan emin olun.
5. **Çıkış**' ın altında çıktının olmasını istediğiniz hücre numarasını girin, örneğin burada "H1" yazın.
6. **Tahmin**' e tıklayın. "Otomatik tahmin" onay kutusunu seçerseniz seçili alanlardaki tüm değişiklikler (giriş olarak belirtilenler), tahmin düğmesine basmanız gerekmeden bir istek ve çıkış hücrelerinin güncelleştirilmesini tetikler.
   
    ![Tahmin bölümü](./media/excel-add-in-for-web-services/image1.png)

Bir Web hizmeti dağıtın veya mevcut bir Web hizmetini kullanın. Web hizmeti dağıtma hakkında daha fazla bilgi için bkz. [öğretici 3: Kredi risk modeli dağıtma](tutorial-part3-credit-risk-deploy.md).

Web hizmetiniz için API anahtarını alın. Bu eylemi gerçekleştirdiğiniz durumlarda, yeni bir Machine Learning Web hizmetinin klasik Machine Learning Web hizmeti yayımladığınıza bağlıdır.

**Klasik Web hizmeti kullanma** 

1. Machine Learning Studio (klasik) bölümünde, sol bölmedeki **Web Hizmetleri** bölümüne tıklayın ve sonra Web hizmeti ' ni seçin.
   
    ![Studio bir Web hizmeti seçin](./media/excel-add-in-for-web-services/image4.png)
2. Web hizmeti için API anahtarını kopyalayın.
   
    ![Studio API anahtarı](./media/excel-add-in-for-web-services/image5.png)
3. Web hizmetinin **Pano** sekmesinde **istek/yanıt** bağlantısına tıklayın.
4. **İstek URI 'si** bölümünü arayın.  URL 'YI kopyalayın ve kaydedin.

> [!NOTE]
> Artık, klasik bir Machine Learning Web hizmeti için API anahtarını almak üzere [Azure Machine Learning Web Hizmetleri](https://services.azureml.net) portalında oturum açmak mümkündür.
> 
> 

**Yeni bir Web hizmeti kullan**

1. [Azure Machine Learning Web Hizmetleri](https://services.azureml.net) portalında **Web Hizmetleri**' ne ve ardından Web hizmetinizi seçin. 
2. **Tüketme**' ye tıklayın.
3. **Temel tüketim bilgileri** bölümüne bakın. **Birincil anahtarı** ve **istek-yanıt** URL 'sini kopyalayıp kaydedin.

## <a name="steps-to-add-a-new-web-service"></a>Yeni bir Web hizmeti ekleme adımları

1. Bir Web hizmeti dağıtın veya mevcut bir Web hizmetini kullanın. Web hizmeti dağıtma hakkında daha fazla bilgi için bkz. [öğretici 3: Kredi risk modeli dağıtma](tutorial-part3-credit-risk-deploy.md).
2. **Tüketme**' ye tıklayın.
3. **Temel tüketim bilgileri** bölümüne bakın. **Birincil anahtarı** ve **istek-yanıt** URL 'sini kopyalayıp kaydedin.
4. Excel 'de **Web Hizmetleri** bölümüne gidin ( **tahmin** bölümünde yer alıyorsa, Web hizmetleri listesine gitmek için geri okuna tıklayın).
   
    ![Web hizmeti seçimine git](./media/excel-add-in-for-web-services/image3.png)
5. **Web hizmeti Ekle**' ye tıklayın.
6. URL 'yi, **URL**etiketli Excel eklenti metin kutusuna yapıştırın.
7. API/birincil anahtarını **API anahtarı**etiketli metin kutusuna yapıştırın.
8. **Ekle**'ye tıklayın.
   
    ![Klasik Web hizmeti için URL ve API anahtarı.](./media/excel-add-in-for-web-services/image6.png)
9. Web hizmetini kullanmak için, "var olan bir Web hizmetini kullanma adımları" bölümündeki yönergeleri izleyin.

## <a name="sharing-your-workbook"></a>Çalışma kitabınızı paylaşma
Çalışma kitabınızı kaydederseniz, eklediğiniz Web hizmetlerinin API/birincil anahtarı da kaydedilir. Bu, çalışma kitabını yalnızca güvendiğiniz kişilerle paylaşmanız gerektiği anlamına gelir.

Aşağıdaki yorum bölümünde veya [forumumuzda](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409)soru sorun.
