---
title: 'ML Studio (klasik): çalışma alanı oluşturma-Azure'
description: Azure Machine Learning Studio (klasik) kullanmak için bir Machine Learning Studio (klasik) çalışma alanınız olması gerekir. Bu çalışma alanı, denemeleri oluşturmak, yönetmek ve yayımlamak için ihtiyacınız olan araçları içerir.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 9caac94c90aa18f8661ab46a0ae72d49500f39cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100520553"
---
# <a name="create-and-share-an-machine-learning-studio-classic-workspace"></a>Machine Learning Studio (klasik) çalışma alanı oluşturma ve paylaşma

**Uygulama hedefi:** ![ Bu bir onay işaretidir ve bu makalenin Machine Learning Studio (klasik) için geçerli olduğu anlamına gelir. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasik) ![ Bu bir X ' dir ve bu makalenin Azure Machine Learning için geçerli olduğu anlamına gelir.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Azure Machine Learning Studio (klasik) kullanmak için bir Machine Learning Studio (klasik) çalışma alanınız olması gerekir. Bu çalışma alanı, denemeleri oluşturmak, yönetmek ve yayımlamak için ihtiyacınız olan araçları içerir.

## <a name="create-a-studio-classic-workspace"></a>Studio (klasik) çalışma alanı oluşturma

Machine Learning Studio (klasik) ' de bir çalışma alanı açmak için, çalışma alanını oluşturmak için kullandığınız Microsoft hesabında oturum açmanız veya çalışma alanına katılması için sahibinden bir davetiye almanız gerekir. Azure portal, erişimi yapılandırma özelliğini içeren çalışma alanını yönetebilirsiniz.

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

5. **Oluştur**’a tıklayın.

   Machine Learning, şu anda sınırlı sayıda bölgede kullanılabilir. Aboneliğiniz bu bölgelerden birini içermiyorsa, "izin verilen bölgelerde aboneliğiniz yok" hata iletisini görebilirsiniz.  Aboneliğinize bir bölgenin eklenmesini istemek için, Azure portal yeni bir Microsoft destek isteği oluşturun, sorun türü olarak **faturalandırma** ' i seçin ve isteğinizi göndermek için istemleri izleyin.


> [!NOTE]
> Machine Learning Studio (klasik), iş akışını yürütürken ara verileri kaydetmek için sağladığınız bir Azure Depolama hesabını kullanır. Çalışma alanı oluşturulduktan sonra, depolama hesabı silinirse veya erişim anahtarları değiştirilirse çalışma alanı çalışmayı durdurur ve bu çalışma alanındaki tüm denemeleri başarısız olur.
Depolama hesabını yanlışlıkla silerseniz, silinen depolama hesabıyla aynı bölgede aynı ada sahip depolama hesabını yeniden oluşturun ve erişim tuşunu yeniden eşitleyin. Depolama hesabının erişim anahtarlarını değiştirdiyseniz, Azure portalını kullanarak çalışma alanındaki erişim anahtarlarını yeniden eşitleyin.

Çalışma alanı dağıtıldıktan sonra, Machine Learning Studio (klasik) içinde açabilirsiniz.

1. Machine Learning Studio (klasik) konumuna gidin [https://studio.azureml.net/](https://studio.azureml.net/) .

2. Sağ üst köşeden çalışma alanınızı seçin.

    ![Çalışma alanını seçme](./media/create-workspace/open-workspace.png)

3. **Denemeleri** tıklayın.

    ![Açık denemeleri](./media/create-workspace/my-experiments.png)

Studio (klasik) çalışma alanınızı yönetme hakkında daha fazla bilgi için bkz. [Azure Machine Learning Studio (klasik) çalışma alanını yönetme](manage-workspace.md).
Çalışma alanınızı oluştururken bir sorunla karşılaşırsanız bkz. [sorun giderme kılavuzu: oluşturma ve Machine Learning Studio (klasik) çalışma alanı ile bağlanma](index.yml).


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Azure Machine Learning Studio (klasik) çalışma alanını paylaşma
Machine Learning Studio (klasik) çalışma alanı oluşturulduktan sonra, çalışma alanınıza ve tüm denemeleri, veri kümelerine ve vb. erişimi paylaşmak için kullanıcıları çalışma alanınıza davet edebilirsiniz. Kullanıcıları, iki rolden birine ekleyebilirsiniz:

* **Kullanıcı** -bir çalışma alanı kullanıcısı, çalışma alanında denemeleri, veri kümeleri, vb. oluşturabilir, açabilir, değiştirebilir ve silebilir.
* **Sahip** -bir sahip, kullanıcının ne yapabileceklerini ek olarak çalışma alanındaki kullanıcıları davet edebilir ve kaldırabilir.

> [!NOTE]
> Çalışma alanını oluşturan yönetici hesabı çalışma alanına çalışma alanı sahibi olarak otomatik olarak eklenir. Ancak, Bu abonelikteki diğer yöneticilere veya kullanıcılara otomatik olarak çalışma alanına erişim izni verilmez; bunları açıkça davet etmeniz gerekir.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Bir Studio (klasik) çalışma alanını paylaşmak için

1. Machine Learning Studio 'de (klasik) oturum açın [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

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

## <a name="troubleshoot-storage-accounts"></a>Depolama hesaplarında sorun giderme


Machine Learning hizmeti, verileri depolamak için bir depolama hesabı gerektirir. Mevcut bir depolama hesabını kullanabilir veya yeni Machine Learning Studio (klasik) çalışma alanını oluştururken yeni bir depolama hesabı oluşturabilirsiniz (yeni bir depolama hesabı oluşturmak için kotayı kullanıyorsanız).

Yeni Machine Learning Studio (klasik) çalışma alanı oluşturulduktan sonra, çalışma alanını oluşturmak için kullandığınız Microsoft hesabı kullanarak Machine Learning Studio (klasik) oturum açabilirsiniz. "Çalışma alanı bulunamadı" hata iletisiyle karşılaşırsanız (aşağıdaki ekran görüntüsüne benzer şekilde), lütfen tarayıcı tanımlama bilgilerinizi silmek için aşağıdaki adımları kullanın.

![Çalışma alanı bulunamadı](media/troubleshooting-creating-ml-workspace/screen3.png)

**Tarayıcı tanımlama bilgilerini silmek için**

1. Internet Explorer kullanıyorsanız, sağ üst köşedeki **Araçlar** düğmesine tıklayın ve **Internet seçenekleri**' ni seçin.  

   ![İnternet seçenekleri](media/troubleshooting-creating-ml-workspace/screen4.png)

2. **Genel** sekmesinde Sil ' e tıklayın **...**

   ![Genel sekmesi](media/troubleshooting-creating-ml-workspace/screen5.png)

3. **Gözatma Geçmişini Sil** iletişim kutusunda, **tanımlama bilgileri ve Web sitesi verilerinin** seçili olduğundan emin olun ve **Sil**' e tıklayın.

   ![Tanımlama bilgilerini silme](media/troubleshooting-creating-ml-workspace/screen6.png)

Tanımlama bilgileri silindikten sonra, tarayıcıyı yeniden başlatın ve ardından [Microsoft Azure Machine Learning Studio (klasik)](https://studio.azureml.net) sayfasına gidin. Bir Kullanıcı adı ve parola istendiğinde, çalışma alanını oluşturmak için kullandığınız Microsoft hesabı aynısını girin.


## <a name="next-steps"></a>Sonraki adımlar

Çalışma alanını yönetme hakkında daha fazla bilgi için bkz. [Azure Machine Learning Studio (klasik) çalışma alanını yönetme](manage-workspace.md).