---
title: 'Öğretici: görüntü sınıflandırması için etiketleme projesi oluşturma'
titleSuffix: Azure Machine Learning
description: Resimleri etiketleme sürecini, çok sınıflı görüntü sınıflandırma modellerinde kullanılabilecek şekilde nasıl yöneteceğinizi öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 04/09/2020
ms.custom: data4ml
ms.openlocfilehash: 41e93584937ca10740e9ee0be3353d1edf5efb3e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587689"
---
# <a name="tutorial-create-a-labeling-project-for-multi-class-image-classification"></a>Öğretici: çok sınıflı görüntü sınıflandırması için etiketleme projesi oluşturma 


Bu öğreticide, makine öğrenimi modelleri oluşturmak için veri olarak kullanılacak etiketleme işleminin (etiketleme olarak da anılır) nasıl yönetileceği gösterilmektedir. Azure Machine Learning içindeki veri etiketleme genel önizlemede.

Görüntüleri sınıflandırmak için bir makine öğrenimi modeli eğitmek istiyorsanız, doğru şekilde etiketlendirilmiş yüzlerce veya hatta binlerce görüntü gerekir.  Azure Machine Learning, verilerinizi etiketlemede etki alanı uzmanlarınızın özel takımınızı yönetmenize yardımcı olur.
 
Bu öğreticide, kediler ve köpekler resimlerini kullanacaksınız.  Her resim bir kedi veya bir köpek olduğundan, bu bir *çok sınıflı* etiketleme projem. Şunları öğrenirsiniz:

> [!div class="checklist"]
>
> * Bir Azure depolama hesabı oluşturun ve görüntüleri hesaba yükleyin.
> * Azure Machine Learning çalışma alanı oluşturun.
> * Çok sınıflı bir görüntü etiketleme projesi oluşturun.
> * Verilerinizi etiketleyin.  Siz veya etiketleyiclarınız bu görevi gerçekleştirebilir.
> * Verileri gözden geçirerek ve dışarı aktararak projeyi doldurun.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://aka.ms/AMLFree) oluşturun.

## <a name="create-a-workspace"></a>Çalışma alanı oluşturma

Azure Machine Learning çalışma alanı, bulutta makine öğrenimi modellerini denemek, eğmek ve dağıtmak için kullandığınız temel bir kaynaktır. Azure aboneliğiniz ve kaynak grubunuz, hizmette kolayca tüketilen bir nesne ile aynı olur.

[Çalışma alanı oluşturmanın birçok yolu](how-to-manage-workspace.md)vardır. Bu öğreticide, Azure kaynaklarınızı yönetmek için Web tabanlı bir konsol olan Azure portal bir çalışma alanı oluşturursunuz.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>Etiketleme projesi başlatma

Daha sonra, tüm beceri seviyelerinin veri bilimi senaryolarına yönelik veri bilimi senaryoları gerçekleştirmek için Machine Learning araçlarını içeren birleştirilmiş bir arabirim olan Azure Machine Learning Studio 'da veri etiketleme projesini yönetirsiniz. Studio, Internet Explorer tarayıcılarında desteklenmez.

1. [Azure Machine Learning Studio](https://ml.azure.com)'da oturum açın.

1. Aboneliğinizi ve oluşturduğunuz çalışma alanını seçin.

### <a name="create-a-datastore"></a><a name="create-datastore"></a>Veri deposu oluşturma

Azure Machine Learning veri depoları, abonelik KIMLIĞINIZ ve belirteç yetkilendirmesi gibi bağlantı bilgilerini depolamak için kullanılır. Burada, Bu öğreticinin görüntülerini içeren depolama hesabına bağlanmak için bir veri deposu kullanırsınız.

1. Çalışma alanınızın sol tarafında **veri depoları**' nı seçin.

1. **+ Yeni veri deposu** seçin.

1. Formu şu ayarlarla doldurun:

    Alan|Açıklama 
    ---|---
    Veri deposu adı | Veri deposuna bir ad verin.  Burada **labeling_tutorial** kullanırız.
    Veri deposu türü | Depolama türünü seçin.  Burada, görüntüler için tercih edilen depolama alanı olan **Azure Blob depolama**'yı kullanırız.
    Hesap seçim yöntemi | **El Ile gir**' i seçin.
    URL | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    Kimlik doğrulaması türü | **SAS belirtecini** seçin.
    Hesap anahtarı | `?sv=2019-02-02&ss=bfqt&srt=sco&sp=rl&se=2025-03-25T04:51:17Z&st=2020-03-24T20:51:17Z&spr=https&sig=7D7SdkQidGT6pURQ9R4SUzWGxZ%2BHlNPCstoSRRVg8OY%3D`

1. Veri deposunu oluşturmak için **Oluştur** ' u seçin.

### <a name="create-a-labeling-project"></a>Etiketleme projesi oluşturma

Etiketlenmesini istediğiniz verilere erişiminiz olduğuna göre etiketleme projenizi oluşturun.

1. Sayfanın üst kısmında **Projeler**' i seçin.

1. **+ Proje Ekle**' yi seçin.

    :::image type="content" source="media/tutorial-labeling/create-project.png" alt-text="Proje oluşturma":::

### <a name="project-details"></a>Proje ayrıntıları

1. **Proje ayrıntıları** formu için aşağıdaki girişi kullanın:

    Alan|Açıklama 
    ---|---
    Proje adı | Projenize bir ad verin.  Burada **öğretici-kediler-n-Dog**'leri kullanacağız.
    Etiketleme görev türü | **Görüntü sınıflandırması çoklu sınıf**' ı seçin.  
    
    Projeyi oluşturmaya devam etmek için **İleri ' yi** seçin.

### <a name="select-or-create-a-dataset"></a>Veri kümesi seçme veya oluşturma

1.   **Veri kümesi Seç veya oluştur** formunda ikinci seçimi seçin, **bir veri kümesi oluşturun** ve ardından **veri deposundan** bağlantıyı seçin.

1. Veri **deposundan Create DataSet** form için aşağıdaki girişi kullanın:

    1. **Temel bilgi** formunda bir ad ekleyin, burada **öğreticiyi görüntüler**.  İsterseniz bir açıklama ekleyin.  Sonra **İleri**’yi seçin.
    1. **Veri deposu seçim** formunda, **önceden oluşturulmuş veri deposu**' nu seçin, ardından veri deposu adına tıklayın ve **veri deposunu Seç**' i seçin.
    1. Sonraki sayfada, seçili olan veri deposunun doğru olduğunu doğrulayın. Aksi takdirde, **önceden oluşturulmuş veri deposu** ' nu seçin ve önceki adımı tekrarlayın.
    1. Bundan sonra, **veri deposu seçim** formunda, git ' i seçin ve **birden çok Lass-DogsCats**' **yi seçin.**  Yol olarak **/birden çok Lass-DogsCats** kullanmak için **Kaydet** ' i seçin.
    1. Ayrıntıları onaylamak için **İleri** ' yi, sonra da **Oluştur** ' u seçerek veri kümesini oluşturun.
    1. Listedeki veri kümesi adının yanındaki daireyi seçin (örneğin, **eğitim için görüntüler**).

1. Projeyi oluşturmaya devam etmek için **İleri ' yi** seçin.

### <a name="incremental-refresh"></a>Artımlı yenileme

Veri kümenize yeni görüntüler eklemeyi planlıyorsanız, artımlı yenileme, bu yeni görüntüleri bulacak ve projenize ekleyecek.  Bu özelliği etkinleştirdiğinizde, proje yeni görüntüleri düzenli olarak denetler.  Bu öğretici için veri deposuna yeni görüntüler eklememezsiniz, bu yüzden bu özelliği işaretlenmemiş olarak bırakın.

Devam etmek için **İleri** seçeneğini belirleyin.

### <a name="label-classes"></a>Etiket sınıfları

1. **Etiket sınıfları** formunda, bir etiket adı yazın ve ardından sonraki etiketi yazmak için **+ etiket ekle** ' yi seçin.  Bu proje için Etiketler **Cat**, **köpek** ve **belirsiz**' dir.

1. Tüm etiketleri ekledikten **sonra ileri ' yi** seçin.

### <a name="labeling-instructions"></a>Etiketleme yönergeleri

1. **Etiketleme yönergeleri** formunda, etiketleyiclarınız için ayrıntılı yönergeler sağlayan bir Web sitesinin bağlantısını sağlayabilirsiniz.  Bu öğretici için boş bırakacağız.

1. Ayrıca, doğrudan forma görevin kısa bir açıklamasını da ekleyebilirsiniz.  Tür **etiketleme öğreticisi-kediler & köpekler.**

1. **İleri**’yi seçin.

1. **Ml yardımlı etiketleme** bölümünde onay kutusunu işaretlenmemiş olarak bırakın. ML yardımlı etiketleme, bu öğreticide kullandığınızdan daha fazla veri gerektirir.

1. **Create project** (Proje oluştur) öğesini seçin.

Bu sayfa otomatik olarak yenilenmez. Bir duraklama sonrasında, projenin durumu **oluşturuldu** olana kadar sayfayı el ile yenileyin.

## <a name="start-labeling"></a>Etiketlemeyi Başlat

Azure kaynaklarınızı ayarlamış ve bir veri etiketleme projesi yapılandırmış oldunuz. Verilerinize etiketler ekleme zamanı.

### <a name="tag-the-images"></a>Görüntüleri etiketleme

Öğreticinin bu bölümünde, *Proje yöneticisinden* rolleri bir *etiketleyici*'ye değiştireceksiniz.  Çalışma alanınıza katılımcı erişimi olan herkes bir etiketleyici olabilir.

1. Projeyi bulmak için [Machine Learning Studio](https://ml.azure.com)'da sol taraftaki **veri etiketleme** ' yi seçin.  

1. Proje için **etiket bağlantısı** ' nı seçin.

1. Yönergeleri okuyun ve **Görevler**' i seçin.

1. Bir go içinde etiketlemek istediğiniz görüntü sayısını göstermek için sağ tarafta bir küçük resim seçin. Üzerinde geçiş yapabilmeniz için önce tüm bu görüntülerin etiketlenmesi gerekir. Yalnızca etiketli verilerin yeni bir sayfasına sahip olduğunuzda mizanpajları değiştirin. Mizanpajları değiştirmek sayfanın devam eden etiketleme işini temizler.

1. Bir veya daha fazla görüntü seçin, sonra seçime uygulanacak bir etiket seçin. Etiket görüntünün altında görünür.  Sayfadaki tüm görüntüleri seçip etiketleyerek devam edin.  Görüntülenecek tüm görüntüleri eşzamanlı olarak seçmek için **Tümünü Seç**' i seçin. Etiket uygulamak için en az bir görüntü seçin.


    > [!TIP]
    > Klavyenizdeki sayı tuşlarını kullanarak ilk dokuz etiketi seçebilirsiniz.

1. Sayfadaki tüm görüntüler etiketledikten sonra bu etiketleri göndermek için **Gönder** ' i seçin.

    ![Resimleri etiketleme](media/tutorial-labeling/catsndogs.gif)

1. Bir yandan veriler için Etiketler gönderdikten sonra, Azure sayfayı iş kuyruğundan yeni bir görüntü kümesiyle yeniler.

## <a name="complete-the-project"></a>Projeyi doldurun

Şimdi etiketleme projesi için rolleri *proje yöneticisine* geri geçirebilirsiniz.

Yönetici olarak, etiketleyicinin çalışmasını gözden geçirmek isteyebilirsiniz.  

### <a name="review-labeled-data"></a>Etiketli verileri gözden geçirme

1. Projeyi bulmak için [Machine Learning Studio](https://ml.azure.com)'da sol taraftaki **veri etiketleme** ' yi seçin.  

1. Proje adı bağlantısını seçin.

1. Panoda projenizin ilerleme durumu gösterilir.

1. Sayfanın üst kısmında, **veriler**' i seçin.

1. Etiketli görüntülerinizi görmek için sol tarafta **etiketli veriler** ' i seçin.  

1. Bir etiketle kabul ettiğinizde, görüntüyü seçin ve ardından sayfanın alt kısmındaki **Reddet** ' i seçin.  Etiketler kaldırılacak ve resim etiketli görüntülerin kuyruğuna geri yerleştirilecek.

### <a name="export-labeled-data"></a>Etiketlenmiş verileri dışarı aktar

Machine Learning deneme için etiket verilerini dilediğiniz zaman dışarı aktarabilirsiniz. Kullanıcılar genellikle birden çok kez dışa aktarır ve tüm görüntülerin etiketlenmesi için beklemek yerine farklı modeller eğitin.

Resim etiketleri [Coco formatında](http://cocodataset.org/#format-data) veya Azure Machine Learning veri kümesi olarak aktarılabilir. Veri kümesi biçimi Azure Machine Learning eğitim için kullanmayı kolaylaştırır.  

1. Projeyi bulmak için [Machine Learning Studio](https://ml.azure.com)'da sol taraftaki **veri etiketleme** ' yi seçin.  

1. Proje adı bağlantısını seçin.

1. **Dışarı aktar** ' ı seçin ve **Azure ML veri kümesi olarak dışarı aktar** seçeneğini 

    Dışarı aktarmanın durumu **dışa aktarma** düğmesinin hemen altında görünür. 

1. Etiketler başarıyla verildikten sonra, sonuçları görüntülemek için sol taraftaki **veri kümeleri** ' ni seçin.

## <a name="clean-up-resources"></a>Kaynakları temizleme


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Machine Learning görüntü tanıma modelini eğitme](/azure/machine-learning/how-to-use-labeled-dataset).

