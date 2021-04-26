---
title: 'Öğretici: Jupyter Notebook (Python) ile çalışmaya başlama'
titleSuffix: Azure Machine Learning
description: Jupyter Notebook öğreticileri için kurulum. Çalışma alanı oluşturun, not defterlerini çalışma alanına kopyalayın ve not defterlerini çalıştırdığınız bir işlem örneği oluşturun.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: devx-track-python, contperf-fy21q3
adobe-target: true
ms.openlocfilehash: ed7a95ea83cacc0eaccfa23cd87403fb9be615ee
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210678"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-jupyter-notebooks"></a>Öğretici: Jupyter not defterlerinde Azure Machine Learning kullanmaya başlayın

Bu öğreticide, [yönetilen bulut tabanlı bir iş istasyonunda (işlem örneği)](concept-compute-instance.md)Jupyıter not defterlerini kullanarak Azure Machine Learning kullanmaya başlama adımlarını tamamlayabilirsiniz. Bu öğretici, diğer tüm Jupyter Notebook öğreticileri için bir precurör.

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Diğer Jupyter Notebook öğreticilerde kullanmak üzere bir [Azure Machine Learning çalışma alanı](concept-workspace.md) oluşturun.
> * Öğreticiler Not defterini çalışma alanındaki klasörünüze kopyalayın.
> * Size Azure Machine Learning Python SDK 'nın zaten yüklü ve sizin için yapılandırılmış bir ortam sunan bulut tabanlı bir işlem örneği oluşturun.

Bu öğretici, çalışma alanınızdaki bir işlem kaynağında Jupyıter not defterlerini çalıştırmaya hazırlar.  

Azure Machine Learning 'i kullanmaya başlamak için başka harika bir yöntem de toplu işler gönderiliyor.  Bkz. [öğretici: geliştirme ortamınızda Azure Machine Learning kullanmaya başlama](tutorial-1st-experiment-sdk-setup-local.md).

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin.

## <a name="create-a-workspace"></a>Çalışma alanı oluşturma

Azure Machine Learning çalışma alanı, bulutta makine öğrenimi modellerini denemek, eğmek ve dağıtmak için kullandığınız temel bir kaynaktır. Azure aboneliğiniz ve kaynak grubunuz, hizmette kolayca tüketilen bir nesne ile aynı olur.

Zaten bir Azure Machine Learning çalışma alanınız varsa, [bir not defteri klasörünü kopyalamaya](#clone) atlayın.  

[Çalışma alanı oluşturmanın birçok yolu](how-to-manage-workspace.md)vardır.  Bu öğreticide, Azure kaynaklarınızı yönetmek için Web tabanlı bir konsol olan Azure portal bir çalışma alanı oluşturursunuz.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT]
> *Çalışma alanınızı* ve *aboneliğinizi* bir yere göz atın. Denemenizi doğru yerde oluşturduğunuzdan emin olmak için bu bilgilere ihtiyacınız olacaktır.

## <a name="run-a-notebook-in-your-workspace"></a><a name="azure"></a>Çalışma alanınızda bir not defteri çalıştırma

Azure Machine Learning, bir yüklemesi ücretsiz ve önceden yapılandırılmış deneyim için çalışma alanınızda bir bulut Not defteri sunucusu içerir. Ortamınız, paketler ve bağımlılıklarınız üzerinde denetim sahibi olmayı tercih ediyorsanız [kendi ortamınızı](tutorial-1st-experiment-sdk-setup-local.md) kullanın.

 Bu videoyla birlikte izleyin veya eğitim Not defterini kopyalayıp çalışma alanınızdan çalıştırmak için ayrıntılı adımları kullanın.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a><a name="clone"></a> Not defteri klasörünü Kopyala

Aşağıdaki deneme kurulumunu tamamlayıp Azure Machine Learning Studio 'da adımları çalıştırın. Bu birleştirilmiş arabirim, tüm beceri seviyeleri için veri bilimi senaryolarına yönelik veri bilimi senaryoları gerçekleştirmeye yönelik makine öğrenimi araçlarını içerir.

1. [Azure Machine Learning Studio](https://ml.azure.com/)'da oturum açın.

1. Aboneliğinizi ve oluşturduğunuz çalışma alanını seçin.

1. Sol tarafta, **Not defterleri**' ni seçin.

1. En üstteki **örnekler** sekmesini seçin.

1. **Python** klasörünü açın.

1. Klasörü üzerinde bir sürüm numarasıyla açın. Bu sayı, Python SDK 'sının geçerli sürümünü temsil eder.

1. **Öğreticiler** klasörünün sağ tarafındaki **...** düğmesini seçin ve ardından **Kopyala**' yı seçin.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="Klonlama öğreticileri klasörünü gösteren ekran görüntüsü.":::

1. Klasörlerin listesi, çalışma alanına erişen her kullanıcıyı gösterir. **Öğreticiler** klasörünü kopyalamak için klasörünüzü seçin.

### <a name="open-the-cloned-notebook"></a><a name="open"></a>Kopyalanmış Not defterini açın

1. **Kullanıcı dosyaları** bölümüne kapatılan **öğreticiler** klasörünü açın.

    > [!IMPORTANT]
    > Not defterlerini **örnekler** klasöründe görüntüleyebilirsiniz, ancak buradan bir not defteri çalıştıramazsınız. Bir not defteri çalıştırmak için, **Kullanıcı dosyaları** bölümünde Not defterinin kopyalanmış sürümünü çalıştırdığınızdan emin olun.
    
1. **Öğreticiler/Image-Classification-mnist-Data** klasörünüzdeki **img-Classification-part1-eğitim. ipynb** dosyasını seçin.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="Açık öğreticiler klasörünü gösteren ekran görüntüsü.":::

1. Üst kısımdaki çubukta, Not defterini çalıştırmak için kullanılacak bir işlem örneği seçin. Bu sanal makineler (VM 'Ler) [Azure Machine Learning çalıştırmanız gereken her şey](concept-compute-instance.md#contents)ile önceden yapılandırılmıştır.

1. Hiçbir VM bulunamazsa, işlem örneği VM oluşturmak için **+ Ekle** ' yi seçin.

    1. Bir VM oluşturduğunuzda, bu kuralları izleyin:
 
        + Bir ad gerekiyor ve alan boş olamaz.
        + Ad, çalışma alanının veya işlem örneğinin Azure bölgesindeki tüm mevcut işlem örnekleri genelinde benzersiz olmalıdır (büyük/küçük harfe duyarsız bir biçimde). Seçtiğiniz ad benzersiz değilse bir uyarı alırsınız.
        + Geçerli karakterler büyük ve küçük harfler, 0 ile 9 arasındaki sayılar ve Dash karakteri (-).
        + Ad 3 ila 24 karakter uzunluğunda olmalıdır.
        + Ad bir harf ile başlamalı, sayı veya tire karakteriyle başlamalıdır.
        + Kesik çizgi karakteri kullanılırsa, tireden sonra en az bir harf gelmelidir. Örneğin, test-, test-0, test-01 geçersiz, test-a0, test-0a, geçerli örneklerdir.

    1. Kullanılabilir seçeneklerden VM boyutunu seçin. Öğreticiler için, varsayılan VM iyi bir seçimdir.

    1. Ardından **Oluştur**’u seçin. SANAL makinenizin ayarlanması yaklaşık beş dakika sürebilir.

1. VM kullanılabilir olduğunda, üstteki araç çubuğunda görüntülenir. Artık Not defteri 'ni araç çubuğunda **Çalıştır** ' a veya Not defteri 'nin kod hücrelerinde **SHIFT + enter** ' a tıklayarak çalıştırabilirsiniz.

Özel pencere öğeleri varsa veya jupi veya jupi veya jupi 'yi kullanmayı tercih ediyorsanız, en sağdaki **jupi** açılan listesini seçin. Ardından **Jupyıter** veya **jupi veya jupi** Yeni tarayıcı penceresi açılır.

## <a name="next-steps"></a>Sonraki adımlar

Şimdi ayarlanmış bir geliştirme ortamınız olduğuna göre, bir Jupyter Notebook modeli eğitme devam edin.

> [!div class="nextstepaction"]
> [Öğretici: veri ve scikit ile görüntü sınıflandırma modellerini eğitme-öğrenme](tutorial-train-models-with-aml.md)

<a name="stop-compute-instance"></a> Şu anda başka öğreticiler hakkında plan yapmazsanız, maliyeti azaltmak için kullanmadığınız durumlarda bulut Not defteri sunucu VM 'sini durdurun.

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]
