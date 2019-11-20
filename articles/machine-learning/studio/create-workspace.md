---
title: Çalışma alanı oluşturma
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (klasik) kullanmak için bir Machine Learning Studio (klasik) çalışma alanınız olması gerekir. Bu çalışma alanı, denemeleri oluşturmak, yönetmek ve yayımlamak için ihtiyacınız olan araçları içerir.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 1652febf6c3eb906990b2405590274911a45348e
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839910"
---
# <a name="create-and-share-an-azure-machine-learning-studio-classic-workspace"></a>Azure Machine Learning Studio (klasik) çalışma alanı oluşturma ve paylaşma

Azure Machine Learning Studio (klasik) kullanmak için bir Machine Learning Studio (klasik) çalışma alanınız olması gerekir. Bu çalışma alanı, denemeleri oluşturmak, yönetmek ve yayımlamak için ihtiyacınız olan araçları içerir.

## <a name="create-a-studio-classic-workspace"></a>Studio (klasik) çalışma alanı oluşturma

1. [Azure portalda](https://portal.azure.com/) oturum açma

    > [!NOTE]
    > Oturum açmak ve bir Studio (klasik) çalışma alanı oluşturmak için bir Azure abonelik yöneticisi olmanız gerekir. 
    >
    > 

2. **+ Yeni** seçeneğine tıklayın

3. Arama kutusuna **Machine Learning Studio (klasik) çalışma alanı** yazın ve eşleşen öğeyi seçin. Ardından sayfanın alt kısmındaki **Oluştur** ' a tıklayın ' ı seçin.

4. Çalışma alanı bilgilerinizi girin:

   - *Çalışma alanı adı* en fazla 260 karakter uzunluğunda olabilir ve bir boşluk ile sonlanmaz. Ad şu karakterleri içeremez: `< > * % & : \ ? + /`
   - Seçtiğiniz ilişkili *fiyatlandırma katmanıyla* birlikte seçtiğiniz (veya oluşturduğunuz) *Web hizmeti planı* , Web hizmetlerini bu çalışma alanından dağıtırsanız kullanılır.

     ![Yeni bir Studio (klasik) çalışma alanı oluştur](./media/create-workspace/create-new-workspace.png)

5. **Oluştur**'a tıklayın.

> [!NOTE]
> Machine Learning Studio (klasik), iş akışını yürütürken ara verileri kaydetmek için sağladığınız bir Azure Depolama hesabını kullanır. Çalışma alanı oluşturulduktan sonra, depolama hesabı silinirse veya erişim anahtarları değiştirilirse çalışma alanı çalışmayı durdurur ve bu çalışma alanındaki tüm denemeleri başarısız olur.
Depolama hesabını yanlışlıkla silerseniz, silinen depolama hesabıyla aynı bölgede aynı ada sahip depolama hesabını yeniden oluşturun ve erişim tuşunu yeniden eşitleyin. Depolama hesabının erişim anahtarlarını değiştirdiyseniz, Azure portalını kullanarak çalışma alanındaki erişim anahtarlarını yeniden eşitleyin.

Çalışma alanı dağıtıldıktan sonra, Machine Learning Studio klasik sürümünde açabilirsiniz.

1. [https://studio.azureml.net/](https://studio.azureml.net/)Machine Learning Studio (klasik) konumuna gidin.

2. Sağ üst köşedeki çalışma alanınızı seçin.

    ![Çalışma alanını seçme](./media/create-workspace/open-workspace.png)

3. **Denemeleri**tıklayın.

    ![Açık denemeleri](./media/create-workspace/my-experiments.png)

Studio (klasik) çalışma alanınızı yönetme hakkında daha fazla bilgi için bkz. [Azure Machine Learning Studio (klasik) çalışma alanını yönetme](manage-workspace.md).
Çalışma alanınızı oluştururken bir sorunla karşılaşırsanız bkz. [sorun giderme kılavuzu: oluşturma ve Machine Learning Studio (klasik) çalışma alanı ile bağlanma](troubleshooting-creating-ml-workspace.md).


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Azure Machine Learning Studio (klasik) çalışma alanını paylaşma
Machine Learning Studio (klasik) çalışma alanı oluşturulduktan sonra, çalışma alanınıza ve tüm denemeleri, veri kümelerine, Notebook, vb. erişimi paylaşmak için kullanıcıları çalışma alanınıza davet edebilirsiniz. Kullanıcıları, iki rolden birine ekleyebilirsiniz:

* **Kullanıcı** -bir çalışma alanı kullanıcısı, çalışma alanında denemeleri, veri kümeleri, vb. oluşturabilir, açabilir, değiştirebilir ve silebilir.
* **Sahip** -bir sahip, kullanıcının ne yapabileceklerini ek olarak çalışma alanındaki kullanıcıları davet edebilir ve kaldırabilir.

> [!NOTE]
> Çalışma alanını oluşturan yönetici hesabı çalışma alanına çalışma alanı sahibi olarak otomatik olarak eklenir. Ancak, Bu abonelikteki diğer yöneticilere veya kullanıcılara otomatik olarak çalışma alanına erişim izni verilmez; bunları açıkça davet etmeniz gerekir.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Bir Studio (klasik) çalışma alanını paylaşmak için

1. [https://studio.azureml.net/Home](https://studio.azureml.net/Home) Machine Learning Studio klasik sürümünde oturum açın

2. Sol bölmede, **Ayarlar** ' a tıklayın.

3. **Kullanıcılar** sekmesine tıklayın

4. Sayfanın alt kısmındaki **daha fazla kullanıcı davet et** ' e tıklayın

    ![Studio ayarları](./media/create-workspace/settings.png)

5. Bir veya daha fazla e-posta adresi girin. Kullanıcılar geçerli bir Microsoft hesabı veya bir kuruluş hesabına (Azure Active Directory) sahip olmalıdır.

6. Kullanıcıları sahip veya Kullanıcı olarak eklemek isteyip istemediğinizi seçin.

7. **Tamam** onay işareti düğmesine tıklayın.

Eklediğiniz her bir Kullanıcı, paylaşılan çalışma alanında nasıl oturum açacağınız hakkında yönergeler içeren bir e-posta alır.

> [!NOTE]
> Kullanıcıların bu çalışma alanında Web hizmetlerini dağıtabilmeleri veya yönetebilmesi için Azure aboneliğinde katkıda bulunan veya yönetici olması gerekir. 



