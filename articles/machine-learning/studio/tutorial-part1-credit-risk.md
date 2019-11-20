---
title: 'Öğretici 1: kredi riskini tahmin etme'
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio klasik sürümünde kredi riski değerlendirmesi için tahmine dayalı bir analiz çözümü oluşturmayı gösteren ayrıntılı bir öğretici. Bu öğretici, üç bölümden oluşan bir öğretici serisinin bir parçasıdır.  Çalışma alanı oluşturma, verileri yükleme ve deneme oluşturma işlemlerinin nasıl yapılacağını gösterir.
keywords: kredi riski, tahmine dayalı analiz çözümü, risk değerlendirmesi
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: e8c1eea097fc3ea2704acfae198eb493943b6ccf
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838863"
---
# <a name="tutorial-1-predict-credit-risk---azure-machine-learning-studio-classic"></a>Öğretici 1: kredi riskini tahmin etme-Azure Machine Learning Studio (klasik)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Bu öğreticide, tahmine dayalı bir analiz çözümü geliştirme sürecinde genişletilmiş bir görünüm elde edersiniz. Machine Learning Studio (klasik) içinde basit bir model geliştirirsiniz.  Daha sonra modeli bir Azure Machine Learning Web hizmeti olarak dağıtabilirsiniz.  Bu dağıtılan model yeni verileri kullanarak tahminleri yapabilir. Bu öğretici, **üç bölümden oluşan bir öğretici serisinin bir parçasıdır**.

Bir kişinin kredi başvurusunda verdiği bilgilere dayanarak kredi riskini tahmin etmeniz gerektiğini varsayalım.  

Kredi risk değerlendirmesi karmaşık bir sorundur, ancak bu öğreticide bir bit basitleştirilmesi sağlanır. Bunu, Microsoft Azure Machine Learning Studio (klasik) kullanarak tahmine dayalı analiz çözümü oluşturma hakkında bir örnek olarak kullanacaksınız. Bu çözüm için Azure Machine Learning Studio klasik sürümünü ve bir Machine Learning Web hizmetini kullanacaksınız.  

Bu üç bölümden oluşan öğreticide, genel kullanıma açık kredi riski verileriyle karşılaşırsınız.  Daha sonra tahmine dayalı bir model geliştirip eğirsiniz.  Son olarak, modeli bir Web hizmeti olarak dağıtırsınız.

Öğreticinin bu bölümünde şunları yapabilirsiniz: 
 
> [!div class="checklist"]
> * Machine Learning Studio (klasik) çalışma alanı oluşturma
> * Var olan verileri yükleme
> * Deneme oluşturma

Daha sonra bu denemeyi, 2. [bölüm içindeki modelleri eğmek](tutorial-part2-credit-risk-train.md) ve sonra [3. bölüm içinde dağıtmak](tutorial-part3-credit-risk-deploy.md)için kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide, Machine Learning Studio (klasik) en az bir kez daha önce kullandığınızı ve makine öğrenimi kavramlarını kavradığınızı varsaymaktadır. Bununla birlikte, bir uzman olduğunuz da varsayılmaz.

Daha önce **Azure Machine Learning Studio (klasik)** daha önce kullanmadıysanız, hızlı başlangıç ile başlamak isteyebilirsiniz, [ilk veri bilimi denemenizi Azure Machine Learning Studio (klasik) olarak oluşturun](create-experiment.md). Hızlı başlangıç size Machine Learning Studio (klasik) ' i ilk kez götürür. Öğreticide modülleri sürükleyip denemenize bırakma, birbirine bağlama, denemeyi çalıştırma ve sonuçları görme konularında temel bilgiler verilir.


> [!TIP] 
> Bu öğreticide geliştirdiğimiz denemenin çalışan bir kopyasını [Azure yapay zeka Galerisi](https://gallery.azure.ai)bulabilirsiniz. **[Öğreticiye gidin-kredi riskini tahmin](https://gallery.azure.ai/Experiment/Walkthrough-Credit-risk-prediction-1)** edin ve deneyinin bir kopyasını Machine Learning Studio (klasik) çalışma alanınıza Indirmek için **Studio 'da aç** ' a tıklayın.
> 


## <a name="create-a-machine-learning-studio-classic-workspace"></a>Machine Learning Studio (klasik) çalışma alanı oluşturma

Machine Learning Studio (klasik) kullanmak için bir Microsoft Azure Machine Learning Studio (klasik) çalışma alanınız olması gerekir. Bu çalışma alanı, denemeleri oluşturmak, yönetmek ve yayımlamak için ihtiyacınız olan araçları içerir.  

Bir çalışma alanı oluşturmak için, bkz. [Azure Machine Learning Studio (klasik) çalışma alanı oluşturma ve paylaşma](create-workspace.md).

Çalışma alanınız oluşturulduktan sonra Machine Learning Studio açın (klasik) ([https://studio.azureml.net/Home](https://studio.azureml.net/Home)). Birden fazla çalışma alanınız varsa, pencerenin sağ üst köşesindeki araç çubuğundan çalışma alanını seçebilirsiniz.

![Studio 'da çalışma alanı seçme (klasik)](./media/tutorial-part1-credit-risk/open-workspace.png)

> [!TIP]
> Çalışma alanının sahibiyseniz, diğer kişileri çalışma alanına davet ederek üzerinde çalışmakta olduğunuz denemeleri paylaşabilirsiniz. Bunu, **Ayarlar** sayfasında Machine Learning Studio (klasik) olarak yapabilirsiniz. Her Kullanıcı için yalnızca Microsoft hesabı veya kuruluş hesabına ihtiyacınız vardır.
> 
> **Ayarlar** sayfasında, **Kullanıcılar**' a ve ardından pencerenin alt kısmındaki **daha fazla kullanıcı davet et** ' e tıklayın.
> 

## <a name="upload"></a>Mevcut verileri karşıya yükle

Kredi riski için tahmine dayalı bir model geliştirmek için, modeli eğtirecek ve test etmeye yönelik olarak kullanabileceğiniz verilerin olması gerekir. Bu öğreticide, UC Irvine Machine Learning deposundan "UCı Statgünlüğü (Almanya kredi verileri) veri kümesi" ni kullanacaksınız. Buradan bulabilirsiniz:  
<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

**Almanya. Data**adlı dosyayı kullanacaksınız. Bu dosyayı yerel sabit sürücünüze indirin.  

**Almanya. Data** veri kümesi, kredi için 1000 son başvura için 20 değişken satırları içerir. Bu 20 değişken, her kredi başvuranı için tanımlayıcı özellikler sağlayan veri kümesinin özellik kümesini ( *özellik vektörü*) temsil eder. Her satırdaki ek bir sütun, başvuranın, düşük kredi riski ve 300 yüksek risk olarak tanımlanan 700 başvuranlarla hesaplanan kredi riskini temsil eder.

UCI Web sitesi, bu veriler için özellik vektörünün özniteliklerinin açıklamasını sağlar. Bu veriler, mali bilgiler, kredi geçmişi, istihdam durumu ve kişisel bilgiler içerir. Her başvuran için, düşük veya yüksek kredi riski olup olmadığını belirten bir ikili derecelendirme verilmiştir. 

Bu verileri, tahmine dayalı bir analiz modelini eğitebilmeniz için kullanacaksınız. İşiniz bittiğinde, modelinizin yeni bir kişiye ait bir özellik vektörünü kabul edebilmelidir ve düşük veya yüksek kredi riski olup olmadığını tahmin edebilir.  

İşte ilginç bir twist.

Bir kişinin kredi riskini yanlış sınıflandırdıysanız, UCI Web sitesindeki veri kümesinin açıklaması BT maliyetlerine değinmektedir.
Model, aslında düşük kredi riski olan birisi için yüksek kredi riski getirse, model hatalı sınıflandırma yaptı.

Ancak ters sınıflandırma, mali kurum için beş kat daha maliyetlidir: model, aslında yüksek kredi riski olan birisi için düşük bir kredi riski öngörür.

Bu nedenle, bu ikinci tür hatalı sınıflandırmanın maliyeti diğer bir şekilde yanlış sınıflandırmadan beş kat daha yüksek olacak şekilde modelinize eğitme yapmak istiyorsunuz.

Deneme ortamınızdaki modeli eğitmek, yüksek kredi riski olan birini temsil eden girişleri çoğaltarak (beş kez) bunu yapmanın basit bir yoludur. 

Daha sonra, model aslında yüksek riskli olmaları durumunda birini düşük kredi riski olarak sınıflandırsa, model her yineleme için bir kez olmak üzere beş kez aynı yanlış sınıflandırmayla yapılır. Bu işlem, eğitim sonuçlarında bu hatanın maliyetini artırır.


### <a name="convert-the-dataset-format"></a>Veri kümesi biçimini Dönüştür

Özgün veri kümesi boş olarak ayrılmış bir biçim kullanır. Machine Learning Studio klasik sürümü, virgülle ayrılmış değer (CSV) dosyası ile daha iyi çalışarak, boşlukları virgülle değiştirerek veri kümesini dönüştürürsünüz.  

Bu verileri dönüştürmenin birçok yolu vardır. Tek yönlü, aşağıdaki Windows PowerShell komutunu kullanmaktır:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Diğer bir yöntem de UNIX SED komutunu kullanmaktır:  

    sed 's/ /,/g' german.data > german.csv  

Her iki durumda da, denemenize kullanabileceğiniz **Almanca. csv** adlı bir dosyada verilerin virgülle ayrılmış bir sürümünü oluşturdunuz.

### <a name="upload-the-dataset-to-machine-learning-studio-classic"></a>Veri kümesini Machine Learning Studio karşıya yükleme (klasik)

Veriler CSV biçimine dönüştürüldükten sonra, Machine Learning Studio klasik sürümüne yüklemeniz gerekir. 

1. Machine Learning Studio (klasik) giriş sayfasını açın ([https://studio.azureml.net](https://studio.azureml.net)). 

2. Pencerenin sol üst köşesindeki menü ![menü](./media/tutorial-part1-credit-risk/menu.png) tıklayın, **Azure Machine Learning**' ye tıklayın, **Studio**' yı seçin ve oturum açın.

3. Pencerenin alt kısmındaki **+ Yeni** seçeneğine tıklayın.

4. **Veri kümesi**' ni seçin.

5. **Yerel dosyadan**seçim yapın.

    ![Yerel dosyadan veri kümesi ekleme](./media/tutorial-part1-credit-risk/add-dataset.png)

6. **Yeni bir veri kümesini karşıya yükle** iletişim kutusunda, Araştır ' a tıklayın ve oluşturduğunuz **Almanya. csv** dosyasını bulun.

7. Veri kümesi için bir ad girin. Bu öğreticide, "UCI Almanya kredi kartı verileri" ni çağırın.

8. Veri türü için, **üst bilgisi olmayan (. NH. csv) genel CSV dosyası**' nı seçin.

9. İsterseniz bir açıklama ekleyin.

10. **Tamam** onay işaretine tıklayın.  

    ![Veri kümesini karşıya yükle](./media/tutorial-part1-credit-risk/upload-dataset.png)

Bu, verileri bir deneme içinde kullanabileceğiniz bir veri kümesi modülüne yükler.

Studio (klasik) penceresinin solundaki **veri kümeleri** sekmesine tıklayarak, Studio 'ya (klasik) yüklediğiniz veri kümelerini yönetebilirsiniz.

![Veri Kümelerini Yönet](./media/tutorial-part1-credit-risk/dataset-list.png)

Diğer veri türlerini bir denemeye aktarma hakkında daha fazla bilgi için bkz. [eğitim verilerinizi Azure Machine Learning Studio Içeri aktarma (klasik)](import-data.md).

## <a name="create-an-experiment"></a>Deneme oluşturma

Bu öğreticideki bir sonraki adım, yüklediğiniz veri kümesini kullanan Machine Learning Studio klasik sürümünde bir deneme oluşturmaktır.  

1. Studio 'da (klasik) pencerenin alt kısmındaki **+ Yeni** ' ye tıklayın.
1. **Denemeler**' i seçin ve ardından "boş deneme" ' ı seçin. 

    ![Yeni bir deneme oluşturma](./media/tutorial-part1-credit-risk/create-new-experiment.png)


1. Tuvalin en üstünde varsayılan deneme adını seçin ve anlamlı bir şekilde yeniden adlandırın.

    ![Yeniden adlandırma denemesi](./media/tutorial-part1-credit-risk/rename-experiment.png)

   > [!TIP]
   > **Özellikler** bölmesinde deneme için **Özet** ve **Açıklama** alanlarını doldurmanız iyi bir uygulamadır. Bu özellikler, daha sonra baktığı herkesin hedeflerinizi ve metodolojisini anlayabilmesi için denemeyi belgeleme şansı sağlar.
   > 
   > ![Deneme özellikleri](./media/tutorial-part1-credit-risk/experiment-properties.png)
   > 

1. Deneme tuvalinin solundaki modül paletinde, **kaydedilmiş veri kümeleri**' ni genişletin.
1. **Veri kümelerim** bölümünde oluşturduğunuz veri kümesini bulun ve tuvale sürükleyin. Ayrıca, paletin üzerindeki **arama** kutusuna adı girerek veri kümesini bulabilirsiniz.  

    ![Veri kümesini denemeye ekleme](./media/tutorial-part1-credit-risk/add-dataset-to-experiment.png)


### <a name="prepare-the-data"></a>Verileri hazırlama

Verilerin ilk 100 satırını ve tüm veri kümesi için bazı istatistiksel bilgileri görüntüleyebilirsiniz: veri kümesinin çıkış bağlantı noktasına (alt kısımdaki küçük daire) tıklayın ve **Görselleştir**' i seçin.  

Veri dosyası sütun başlıkları ile gelmediğinden, Studio 'nun klasik sürümü genel başlıklar (Sütun1, Col2, *vb.* ) sağlamıştır. İyi başlıklar bir model oluşturmak için gerekli değildir, ancak deneme sürümündeki verilerle çalışmayı kolaylaştırır. Ayrıca, bu modeli bir Web hizmetinde sonunda yayımladığınızda, başlıklar hizmetin kullanıcısına ait sütunları belirlemesine yardımcı olur.  

[Meta veri düzenleme][edit-metadata] modülünü kullanarak sütun başlıkları ekleyebilirsiniz.

Bir veri kümesiyle ilişkili meta verileri değiştirmek için [meta verileri Düzenle][edit-metadata] modülünü kullanın. Bu durumda, sütun başlıkları için daha kolay adlar sağlamak üzere kullanın. 

[Meta verileri Düzenle][edit-metadata]' yi kullanmak için, ilk olarak değiştirilecek sütunları (Bu durumda, hepsi) belirlersiniz. Ardından, bu sütunlarda gerçekleştirilecek eylemi belirtirsiniz (Bu durumda, sütun başlıklarını değiştirebilirsiniz.)

1. Modül paletinde, **arama** kutusuna "metadata" yazın. [Düzenleme meta verileri][edit-metadata] modül listesinde görünür.

1. [Meta verileri Düzenle][edit-metadata] modülünü tuvale sürükleyin ve daha önce eklediğiniz veri kümesinin altına bırakın.

1. Veri kümesini [düzenleme meta verilerine][edit-metadata]bağlama: veri kümesinin çıkış bağlantı noktasına (veri kümesinin altındaki küçük daire) tıklayın, [verileri Düzenle][edit-metadata] (modülün en üstündeki küçük daire) giriş bağlantı noktasına sürükleyin, sonra fare düğmesini bırakın. Tuvalde etrafında hareket etseniz bile veri kümesi ve modül bağlı kalır.
 
    Deneme şimdi şuna benzer görünmelidir:  

    ![Düzenleme meta verileri ekleme](./media/tutorial-part1-credit-risk/experiment-with-edit-metadata-module.png)

    Kırmızı ünlem işareti, Bu modülün özelliklerini henüz belirlemediğiniz anlamına gelir. Bunu bir sonraki adımda yapacaksınız.

    > [!TIP]
    > Modüle çift tıklayıp metin girerek bir modüle yorum ekleyebilirsiniz. Bu, modülün denemenizde ne işe yaradığını bir bakışta görmenize yardımcı olabilir. Bu durumda, [meta verileri Düzenle][edit-metadata] modülüne çift tıklayın ve "sütun başlıkları ekle" açıklamasını yazın. Metin kutusunu kapatmak için tuvalde başka bir yere tıklayın. Yorumu göstermek için modüldeki aşağı oka tıklayın.
    > 
    > ![Açıklama eklenmiş meta veri modülünü Düzenle](./media/tutorial-part1-credit-risk/edit-metadata-with-comment.png)
    > 

1. [Meta verileri Düzenle][edit-metadata]' yi seçin ve tuvalin sağındaki **Özellikler** bölmesinde, **sütun seçiciyi Başlat**' a tıklayın.

1. **Sütunları seçin** iletişim kutusunda, **kullanılabilir sütunlardaki** tüm satırları seçin ve > ' a tıklayarak **Seçili sütunlara**taşıyın.
   İletişim kutusu şöyle görünmelidir:

   ![Tüm sütunları seçili olan sütun seçici](./media/tutorial-part1-credit-risk/select-columns.png)


1. **Tamam** onay işaretine tıklayın.

1. **Özellikler** bölmesinde, **Yeni sütun adları** parametresini bulun. Bu alana, veri kümesindeki 21 sütun için, virgülle ve sütun düzeninde ayrılmış adların bir listesini girin. Veri kümesi belgelerindeki sütun adlarını UCI Web sitesinde edinebilir veya kolaylık sağlaması için aşağıdaki listeyi kopyalayabilir ve yapıştırabilirsiniz:  

   ```   
   Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   ```

   Özellikler bölmesi şöyle görünür:

   ![Meta verileri düzenleme özellikleri](./media/tutorial-part1-credit-risk/edit-metadata-properties.png)

   > [!TIP]
   > Sütun başlıklarını doğrulamak istiyorsanız, denemeyi çalıştırın (deneme tuvalinin altında **Çalıştır** ' a tıklayın). Çalışmayı bitirdiğinde ( [meta verileri Düzenle][edit-metadata]' de yeşil bir onay işareti görünür), [meta veri düzenleme][edit-metadata] modülünün çıkış bağlantı noktasına tıklayın ve **Görselleştir**' i seçin. Denemeler aracılığıyla verilerin ilerlemesini görüntülemek için herhangi bir modülün çıktısını aynı şekilde görüntüleyebilirsiniz.
   > 
   > 

### <a name="create-training-and-test-datasets"></a>Eğitim ve test veri kümeleri oluşturma

Modeli eğitebilmeniz için bazı veriler gerekir ve bu da bazı verileri test etmek için gereklidir.
Bu nedenle, deneyinin bir sonraki adımında, veri kümesini iki ayrı veri kümesine bölecektir: bir tane, modelimizi ve test etmeyi deneyin.

Bunu yapmak için, [verileri Böl][split] modülünü kullanırsınız.  

1. [Bölünmüş veri][split] modülünü bulun, tuvale sürükleyin ve [meta verileri düzenleme][edit-metadata] modülüne bağlayın.

1. Varsayılan olarak, bölme oranı 0,5, **rastgele bölünen** parametresi ayarlanır. Bu, verilerin rastgele bir yarısında [bölünen veri][split] modülünün bir bağlantı noktası üzerinden çıkış olduğu ve diğerinin yarısını oluşturan anlamına gelir. Eğitim ve test verileri arasındaki bölmeyi değiştirmek için, bu parametrelerin yanı sıra **rastgele çekirdek** parametresini de ayarlayabilirsiniz. Bu örnekte, bunları olduğu gibi bırakın.
   
   > [!TIP]
   > **İlk çıkış veri kümesindeki satırların Özellik kesri** , verilerin *sol* çıkış bağlantı noktasından ne kadar çıkış olduğunu belirler. Örneğin, oranını 0,7 olarak ayarlarsanız, verilerin %70 ' u sol bağlantı noktasından ve %30 ' a doğru bağlantı noktası üzerinden çıktı.  
   > 
   > 

1. [Bölünmüş veri][split] modülüne çift tıklayın ve "eğitim/test verileri bölünmüş 50%" açıklamasını girin. 

Aynı şekilde [bölünmüş veri][split] modülünün çıkışlarını kullanabilirsiniz, ancak, sınama verileri olarak sol çıktıyı eğitim verileri ve doğru çıkış olarak kullanmayı seçelim.  

[Önceki adımda](tutorial-part1-credit-risk.md#upload)belirtildiği gibi, yüksek kredi riskini düşük olarak sınıflandırmakta olan maliyet, düşük kredi riskini yüksek bir şekilde yanlış sınıflandırma maliyetinden beş kat daha yüksektir. Bunu hesaba eklemek için, bu maliyet işlevini yansıtan yeni bir veri kümesi oluşturursunuz. Yeni veri kümesinde her bir yüksek riskli örnek beş kez çoğaltılır, ancak her düşük riskli örnek çoğaltılmaz.   

Bu çoğaltmayı R Code kullanarak yapabilirsiniz:  

1. [Execute R betiği][execute-r-script] modülünü bulun ve deneme tuvaline sürükleyin. 

1. [Bölünmüş veri][split] modülünün sol çıkış bağlantı noktasını, [R betiği yürütme][execute-r-script] modülünün ilk giriş bağlantı noktasına ("DataSet1") bağlayın.

1. [R betik modülünü Yürüt ' e][execute-r-script] çift tıklayın ve "maliyet ayarlamayı ayarla" açıklamasını girin.

1. **Özellikler** bölmesinde, **R betiği** parametresindeki varsayılan metni silin ve şu betiği girin:
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![R betiği Yürüt modülünde r betiği](./media/tutorial-part1-credit-risk/execute-r-script.png)

Eğitim ve test verilerinin aynı maliyet ayarlamasına sahip olması için, [bölünmüş veri][split] modülünün her çıkışı için aynı çoğaltma işlemini yapmanız gerekir. Bunu yapmanın en kolay yolu, yeni oluşturduğunuz ve bunu [bölünmüş veri][split] modülünün diğer çıkış bağlantı noktasına bağlayan [R betik][execute-r-script] modülünü çoğaltarak kullanmaktır.

1. [R betik modülünü Yürüt ' e][execute-r-script] sağ tıklayın ve **Kopyala**' yı seçin.

1. Deneme tuvaline sağ tıklayıp **Yapıştır**' ı seçin.

1. Yeni modülü konuma sürükleyin ve ardından [bölünmüş veri][split] modülünün sağ çıkış bağlantı noktasını bu yeni [Execute R betik][execute-r-script] modülünün ilk giriş bağlantı noktasına bağlayın. 

1. Tuvalin alt kısmındaki **Çalıştır**' a tıklayın. 

> [!TIP]
> Execute R betiği modülünün kopyası, özgün modülle aynı komut dosyasını içerir. Tuvalde bir modül kopyalayıp yapıştırdığınızda, kopya orijinalin tüm özelliklerini korur.  
> 
>

Deneme mız şu şekilde görünür:

![Bölünmüş modül ve R betikleri ekleme](./media/tutorial-part1-credit-risk/experiment.png)

Denemeleri 'de R betikleri kullanma hakkında daha fazla bilgi için, bkz. [denemenizi r Ile genişletme](extend-your-experiment-with-r.md).


## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şu adımları tamamladınız: 
 
> [!div class="checklist"]
> * Machine Learning Studio (klasik) çalışma alanı oluşturma
> * Varolan verileri çalışma alanına yükle
> * Deneme oluşturma

Artık bu verilerin modellerini eğitmek ve değerlendirmek için hazırsınız.

> [!div class="nextstepaction"]
> [Öğretici 2-modelleri eğitme ve değerlendirme](tutorial-part2-credit-risk-train.md)

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
