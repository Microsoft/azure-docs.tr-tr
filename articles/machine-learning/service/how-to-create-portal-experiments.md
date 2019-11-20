---
title: Otomatikleştirilmiş ML modellerini derleme & dağıtma
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Studio 'da otomatik makine öğrenimi denemeleri oluşturun, yönetin ve dağıtın.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.openlocfilehash: 25e775cf7bfd415768144b28ab2ca6989f360edd
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818499"
---
# <a name="create-explore-and-deploy-automated-machine-learning-experiments-with-azure-machine-learning-studio"></a>Azure Machine Learning Studio ile otomatik makine öğrenimi denemeleri oluşturma, araştırma ve dağıtma
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

 Bu makalede, Azure Machine Learning Studio 'da tek bir kod satırı olmadan otomatik makine öğrenimi denemeleri oluşturma, araştırma ve dağıtmayı öğreneceksiniz. Otomatik makine öğrenimi, belirli verileriniz için kullanılacak en iyi algoritmayı seçme sürecini otomatikleştirir, böylece bir makine öğrenimi modelini hızlıca oluşturabilirsiniz. [Otomatik makine öğrenimi hakkında daha fazla bilgi edinin](concept-automated-ml.md).

 Daha fazla kod tabanlı bir deneyim tercih ediyorsanız, [otomatik makine öğrenimi denemeleri](how-to-configure-auto-train.md) [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ile Python 'da da yapılandırabilirsiniz.

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin.

* **Enterprise Edition**türünde bir Azure Machine Learning çalışma alanı. Bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md).  Mevcut bir çalışma alanını Enterprise Edition 'a yükseltmek için bkz. [Enterprise Edition 'A yükseltme](how-to-manage-workspace.md#upgrade).

## <a name="get-started"></a>Başlarken

1. [Azure Machine Learning Studio](https://ml.azure.com)'da oturum açın. 

1. Aboneliğinizi ve çalışma alanınızı seçin. 

1. Sol bölmeye gidin. **Yazar** bölümü altında **Otomatik ml** ' yi seçin.

[![Azure Machine Learning Studio gezinti bölmesi](media/how-to-create-portal-experiments/nav-pane.png)](media/how-to-create-portal-experiments/nav-pane-expanded.png)

 Denemeleri ilk kez varsa, boş bir liste ve belge bağlantıları görürsünüz. 

Aksi takdirde, SDK ile oluşturulanlar da dahil olmak üzere, son otomatik makine öğrenimi denemeleri bir listesini görürsünüz. 

## <a name="create-and-run-experiment"></a>Deneme oluşturma ve çalıştırma

1. **+ Deneme oluştur** ' u seçin ve formu doldurun.

1. Depolama kapsayıcıınızdan bir veri kümesi seçin veya yeni bir veri kümesi oluşturun. Veri kümeleri yerel dosyalardan, Web URL 'lerinden, veri depolarından veya Azure açık veri kümelerinden oluşturulabilir. 

    >[!Important]
    > Eğitim verileri için gereksinimler:
    >* Verilerin tablolu biçimde olması gerekir.
    >* Tahmin etmek istediğiniz değer (hedef sütun) verilerde bulunmalıdır.

    1. Yerel bilgisayarınızdaki bir dosyadan yeni bir veri kümesi oluşturmak için, **Araştır** ' ı seçin ve ardından dosyayı seçin. 

    1. Veri kümenize benzersiz bir ad verin ve isteğe bağlı bir açıklama sağlayın. 

    1. **İleri**' yi seçerek, çalışma alanınız ile otomatik olarak oluşturulan varsayılan depolama kapsayıcısına yükleyin veya deneme için kullanmak istediğiniz bir depolama kapsayıcısını seçin. 

    1. Doğruluk için **ayarları ve önizleme** formunu gözden geçirin. Form, dosya türüne göre akıllıca doldurulur. 

        Alan| Açıklama
        ----|----
        Dosya biçimi| Bir dosyada depolanan verilerin yerleşimini ve türünü tanımlar.
        Ayırıcı| Düz metin veya diğer veri akışlarında ayrı, bağımsız bölgeler arasındaki sınırı belirtmek için bir veya daha fazla karakter.
        Encoding| Veri kümenizi okumak için kullanılacak bit karakter şeması tablosunu belirler.
        Sütun başlıkları| Veri kümesinin üst bilgilerinin (varsa) nasıl değerlendirileceğini gösterir.
        Satırları atla | Veri kümesinde kaç tane, ne varsa satırların atlandığını gösterir.
    
        **İleri**’yi seçin.

    1. **Şema** formu, **Ayarlar ve önizleme** formundaki seçimlere göre akıllıca doldurulur. Burada her bir sütun için veri türünü yapılandırın, sütun adlarını gözden geçirin ve denemenize **dahil olmayan** sütunları seçin. 
            
        **İleri ' yi seçin.**

    1. **Ayrıntıları Onayla** formu, daha önce **temel bilgi** ve **Ayarlar ve önizleme** formlarında doldurulmuş bilgilerin bir özetidir. Ayrıca, profil oluşturma etkinleştirilmiş bir işlem kullanarak veri kümenizi profil oluşturma seçeneğiniz de vardır. [Veri profili oluşturma](#profile)hakkında daha fazla bilgi edinin.

        **İleri**’yi seçin.
1. Yeni oluşturduğunuz veri kümenizi göründüğünde seçin. Ayrıca, veri kümesinin ve örnek istatistiklerin önizlemesini de görüntüleyebilirsiniz. 

1. **Çalıştırma yapılandırması** formunda, benzersiz bir deneme adı girin.

1. Hedef sütun seçin; Bu, tahminlerde yapmak istediğiniz sütundur.

1. Veri profili oluşturma ve eğitim işi için bir işlem seçin. Mevcut hesaplamalarınızın listesi açılan menüde bulunabilir. Yeni bir işlem oluşturmak için adım 7 ' deki yönergeleri izleyin.

1. Bu deneme için işlem bağlamını yapılandırmak üzere **Yeni bir Işlem oluştur** ' u seçin.

    Alan|Açıklama
    ---|---
    İşlem adı| İşlem bağlamını tanımlayan benzersiz bir ad girin.
    Sanal makine boyutu| İşlem için sanal makine boyutunu seçin.
    En az/en fazla düğüm (Gelişmiş ayarlarda)| Veri profili için, 1 veya daha fazla düğüm belirtmeniz gerekir. İşlem için en fazla düğüm sayısını girin. Varsayılan değer bir AML Işlem için 6 düğümünüz.
    
    **Oluştur**'u seçin. Yeni bir işlem oluşturmak birkaç dakika sürebilir.

    >[!NOTE]
    > İşlem adınız, seçtiğiniz işlem *profil oluşturma*/oluşturma işleminin etkin olup olmadığını gösterir. (Daha fazla ayrıntı için bkz. [veri profili oluşturma](#profile) bölümü).

    **İleri**’yi seçin.

1. **Görev türü ve ayarlar** formunda, görev türünü seçin: sınıflandırma, regresyon veya tahmin. Daha fazla bilgi için bkz. [görev türlerini tanımlama](how-to-define-task-type.md) .

    1. Sınıflandırma için, metin özelliklerini de kullanan derin öğrenmeyi de etkinleştirebilirsiniz.

    1. Tahmin için:
        1. Zaman sütununu seçin: Bu sütun kullanılacak saat verilerini içerir.

        1. Tahmin ufku seçin: modelin kaç zaman birimi (dakika/saat/gün/hafta/ay/yıl) gelecek şekilde tahmin edemeyeceğini belirtin. Daha sonra modelin daha iyi tahmin edilmesi gerektiğinde, daha az doğru olacaktır. [Tahmin ve tahmin ufku hakkında daha fazla bilgi edinin](how-to-auto-train-forecast.md).

1. Seçim Ek yapılandırma: eğitim işini daha iyi denetleyebilmeniz için kullanabileceğiniz ek ayarlar. Aksi takdirde, denemeler seçimine ve verilerine göre varsayılan ayarlar uygulanır. 

    Ek yapılandırmalar|Açıklama
    ------|------
    Birincil ölçüm| Modelinize Puanlama için kullanılan ana ölçüm. [Model ölçümleri hakkında daha fazla bilgi edinin](how-to-configure-auto-train.md#explore-model-metrics).
    Otomatik olarak korleştirme| Otomatik makine öğrenimi tarafından gerçekleştirilen ön işleme özelliğini etkinleştirmek veya devre dışı bırakmak için seçin. Ön işleme, yapay özellikler oluşturmak için otomatik veri temizleme, hazırlama ve dönüştürme içerir. [Ön işleme hakkında daha fazla bilgi edinin](#preprocess).
    Engellenen algoritma| Eğitim işinden dışlamak istediğiniz algoritmaları seçin.
    Çıkış ölçütü| Bu ölçütlerden herhangi biri karşılandığında eğitim işi durdurulur. <br> *Eğitim işi süresi (saat)* : eğitim işinin ne kadar süreyle çalışmasına izin verme. <br> *Ölçüm puan eşiği*: tüm işlem hatları için en düşük ölçüm puanı. Bu, ulaşmak istediğiniz tanımlı bir hedef ölçüsünün olması durumunda eğitim işinde gerekli olandan daha fazla zaman harcamamanızı sağlar.
    Doğrulama| Eğitim işinde kullanmak için çapraz doğrulama seçeneklerinden birini seçin. [Çapraz doğrulama hakkında daha fazla bilgi edinin](how-to-configure-auto-train.md).
    Eşzamanlılık| *Maksimum eşzamanlı yineleme*: eğitim işinde sınanacak maksimum işlem hattı sayısı (yineleme). İş, belirtilen sayıda yinelemeden daha fazla çalıştırmayacak. <br> *Yineleme başına en fazla çekirdek*: çok çekirdekli işlem kullanırken kullanmak istediğiniz çok çekirdekli limitleri seçin.

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Veri profili oluşturma & Özet istatistikleri

Veri ayarlanmış olup olmadığını doğrulamak için veri kümesi genelinde çok sayıda Özet istatistiği alabilir. Sayısal olmayan sütunlarda yalnızca min, Max ve Error Count gibi temel istatistikler bulunur. Sayısal sütunlarda, istatistiksel ve tahmini quantiles değerlerini de gözden geçirebilirsiniz. Veri profiliniz özellikle şunları içerir:

>[!NOTE]
> İlgisiz türler içeren özellikler için boş girdiler görüntülenir.

İstatistik|Açıklama
------|------
Özellik| Özetlenen sütunun adı.
Profil| Çıkarılan türe göre satır içi görselleştirme. Örneğin, dizeler, Boole değerleri ve tarihler değer sayılarına sahip olacaktır, ancak Ondalıklar (Numerics) de yaklaşık histogramlar olur. Bu, verilerin dağıtımını hızlı bir şekilde anlayabilmeniz için size izin verir.
Tür dağılımı| Bir sütun içindeki türlerin satır içi değer sayısı. Null değerler kendi türlerdir, bu nedenle bu görselleştirme tek veya eksik değerleri algılamak için yararlıdır.
Tür|Sütunun Çıkarsanan türü. Olası değerler şunlardır: dizeler, Boole değerleri, tarihler ve ondalıklar.
Min| Sütunun minimum değeri. Boş girdiler, türü bir devralınan sıralamaya (örn. Boolean) sahip olmayan özellikler için görünür.
Maks| Sütunun en büyük değeri. 
Sayı| Sütundaki eksik ve eksik olmayan girdilerin toplam sayısı.
Eksik sayı yok| Sütundaki eksik girdi sayısı. Boş dizeler ve hatalar değer olarak değerlendirilir ve bu nedenle "eksik sayısı" öğesine katkıda bulunmazlar.
Quantiles| Verilerin dağıtılması hakkında bir fikir sağlamak için her bir satışla yaklaşık değerler.
Ortası| Sütunun aritmetik ortalaması veya ortalaması.
Standart sapma| Bu sütun verisinin dağılım veya değişim miktarının ölçümü.
Denetlemesini| Bu sütunun verilerinin ne kadar yayıldığı, ortalama değerinden oluşan ölçüdür. 
Eğrilikleri| Bu sütun verilerinin normal bir dağılıma göre ne kadar farklı olduğunu gösteren ölçü.
Sıklık| Bu sütun verilerinin ne kadar süden bir normal dağıtımla karşılaştırıldığı ölçüdür.

<a name="preprocess"></a>

## <a name="advanced-preprocessing-options"></a>Gelişmiş ön işleme seçenekleri

Denemeleri 'nizi yapılandırırken `Preprocess`gelişmiş ayarı etkinleştirebilirsiniz. Bunun yapılması, aşağıdaki veri ön işleme ve korleştirme adımlarının otomatik olarak gerçekleştirildiği anlamına gelir.

|&nbsp;adımları ön işleme| Açıklama |
| ------------- | ------------- |
|Yüksek önem düzeyi bırakma veya fark özelliği yok|Tüm değerleri eksik olan özellikler de dahil olmak üzere eğitim ve doğrulama kümelerinden bunları bırakın, tüm satırlarda veya çok yüksek kardinalite (örneğin, karma, kimlik veya GUID) ile aynı değeri kullanın.|
|Impute eksik değerler|Sayısal özellikler için sütunundaki değerleri ortalama olan ımpute.<br/><br/>Kategorik özellikler için en sık değer içeren ımpute.|
|Ek özellikler oluştur|Tarih saat özellikleri için: yıl, ay, gün, haftanın günü, yılın günü, üç aylık dönem, yılın haftası, saat, dakika, saniye.<br/><br/>Metin özellikleri için: tekli gram, Çift gram ve üçlü karakter-gram temelinde Dönem sıklığı.|
|Dönüştür ve kodla |Birkaç benzersiz değer içeren sayısal özellikler kategorik özelliklere dönüştürülür.<br/><br/>Düşük kardinalite kategorik için tek bir etkin kodlama gerçekleştirilir; yüksek kardinalite, tek bir sıcak karma kodlama için.|
|Sözcük katıştırlamaları|Metin belirteçlerinin vektörlerini, önceden eğitilen bir model kullanarak tümce vektörlerine dönüştüren metin korleştirici. Belge özellik vektörü oluşturmak için bir belgedeki her bir sözcüğün katıştırma vektörü birlikte toplanır.|
|Hedef kodlamalar|Kategorik özellikler için, her bir kategoriyi gerileme sorunları için Ortalama hedef değeriyle ve sınıflandırma sorunları için her bir sınıfın sınıf olasılığa eşler. Sıklık tabanlı ağırlığa ve k katlamalı çapraz doğrulama, seyrek veri kategorilerinin neden olduğu eşlemenin ve gürültü üzerine gitmek için geçerlidir.|
|Metin hedefi kodlaması|Metin girişi için, her bir sınıfın olasılığını oluşturmak için kelimeleri olan bir yığılmış Doğrusal model kullanılır.|
|Kanıt ağırlığı (WoE)|, Kategorik sütunların bağıntısı olarak hedef sütuna bir ölçü olarak, WoE hesaplar. Sınıf içi ve sınıf dışı olasılıkların oranının günlüğü olarak hesaplanır. Bu adım, her sınıf için bir sayısal özellik sütunu verir ve eksik değerler ve aykırı değer işleme gereksinimini ortadan kaldırır.|
|Küme uzaklığı|K. a, tüm sayısal sütunlarda kümeleme modeli anlamına gelir.  Her bir örneğin her bir kümenin centroıd değerine her bir örnek arasındaki mesafeyi içeren, her küme için yeni bir sayısal özellik olan yeni özellik çıkışları.|

## <a name="run-experiment-and-view-results"></a>Deneme çalıştırma ve sonuçları görüntüleme

Denemenizi çalıştırmak için **Başlat** ' ı seçin. Deneme hazırlama işlemi 10 dakikaya kadar sürebilir. Eğitim işleri, her bir işlem hattının çalışmayı tamamlaması için ek 2-3 dakika daha fazla sürebilir.

### <a name="view-experiment-details"></a>Deneme ayrıntılarını görüntüle

>[!NOTE]
> Çalıştırmanın durumunu görüntülemek için düzenli aralıklarla **Yenile** ' yi seçin. 

**Ayrıntı Çalıştır** ekranı **Ayrıntılar** sekmesinde açılır. Bu ekranda **çalışma durumu**dahil olmak üzere deneme çalıştırmasının bir özeti gösterilir. 

**Modeller** sekmesi, ölçüm puanına göre sıralanmış oluşturulan modellerin bir listesini içerir. Varsayılan olarak, seçili ölçüm temelinde en yüksek düzeyde puan veren model listenin en üstünde yer alır. Eğitim işi daha fazla model denediğinde, bunlar listeye eklenir. Şimdiye kadar üretilen modellerin ölçümlerinin hızlı bir şekilde karşılaştırılmasını sağlamak için bunu kullanın.

[![çalıştırma ayrıntıları panosu](media/how-to-create-portal-experiments/run-details.png)](media/how-to-create-portal-experiments/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Eğitim çalışma ayrıntılarını görüntüle

**Görselleştirme** sekmesindeki **model ayrıntıları** sekmesinde veya Performans grafiklerinde ölçümleri Çalıştır gibi eğitim çalıştırma ayrıntılarını görmek için tamamlanan modellerin herhangi birinde detaya gidin. [grafikler hakkında daha fazla bilgi edinin](how-to-understand-automated-ml.md).

[![yineleme ayrıntıları](media/how-to-create-portal-experiments/iteration-details.png)](media/how-to-create-portal-experiments/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Modelinizi dağıtın

En iyi modeli elde etmeniz durumunda, yeni verileri tahmin etmek için bunu bir Web hizmeti olarak dağıtmanız zaman alır.

Otomatikleştirilmiş ML, kodu yazmadan modeli dağıtmanıza yardımcı olur:

1. Dağıtım için birkaç seçeneğiniz vardır. 

    + Seçenek 1: en iyi modeli dağıtmak Için (tanımladığınız ölçüm ölçütlerine göre), Ayrıntılar sekmesinden En Iyi modeli Dağıt ' ı seçin.

    + 2\. seçenek: bu deneyime ait belirli bir model yinelemesini dağıtmak Için modelin detayına gidin ve model ayrıntıları sekmesini açın ve modeli Dağıt ' ı seçin.

1. **Modeli dağıt** bölmesini doldurun.

    Alan| Değer
    ----|----
    Ad| Dağıtımınız için benzersiz bir ad girin.
    Açıklama| Bu dağıtımın ne için olduğunu daha iyi tanımlamak için bir açıklama girin.
    İşlem türü| Dağıtmak istediğiniz uç nokta türünü seçin: *Azure Kubernetes hizmeti (AKS)* veya *Azure Container Instance (acı)* .
    Ad| *Yalnızca AKS Için geçerlidir:* Dağıtmak istediğiniz AKS kümesinin adını seçin.
    Kimlik doğrulamayı etkinleştirme | Belirteç tabanlı veya anahtar tabanlı kimlik doğrulamasına izin vermek için seçin.
    Özel dağıtım varlıklarını kullanma| Kendi Puanlama betiğinizi ve ortam dosyanızı karşıya yüklemek istiyorsanız bu özelliği etkinleştirin. [Puanlama betikleri hakkında daha fazla bilgi edinin](how-to-deploy-and-where.md#script).

    >[!Important]
    > Dosya adları 32 karakterden oluşmalıdır ve alfasayısal karakterlerle başlamalı ve bitmelidir. Arasına tire, alt çizgi, nokta ve alfasayısal karakterler içerebilir. Boşluklara izin verilmez.

    *Gelişmiş* menü, [veri toplama](how-to-enable-app-insights.md) ve kaynak kullanımı ayarları gibi varsayılan dağıtım özelliklerini sunar. Bu Varsayılanları geçersiz kılmak istiyorsanız bu menüdeki bu ayarları yapın.

1. **Dağıt**'ı seçin. Dağıtımın tamamlanması yaklaşık 20 dakika sürebilir.

Artık tahmin oluşturmak için işlemsel bir Web hizmetiniz vardır! [Power BI yerleşik Azure Machine Learning desteği '](how-to-consume-web-service.md#consume-the-service-from-power-bi)nden hizmeti sorgulayarak tahminleri test edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Machine Learning ilk OTOMATIK ml denemenizin oluşturulması için uçtan uca öğreticiyi](tutorial-first-experiment-automated-ml.md)deneyin. 
* [Otomatik makine öğrenimi ve Azure Machine Learning hakkında daha fazla bilgi edinin](concept-automated-ml.md) .
* [Otomatik makine öğrenimi sonuçlarını anlayın](how-to-understand-automated-ml.md).
* [Bir Web hizmetini kullanmayı öğrenin](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service).
