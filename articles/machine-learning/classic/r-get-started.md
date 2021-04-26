---
title: Machine Learning Studio (klasik) ile R 'yi kullanma-Azure
description: Tahmin çözümü oluşturmak için R 'de Azure Machine Learning Studio (klasik) kullanmaya başlamak için bu R programlama öğreticisini kullanın.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: c619b51f9323477bda4f1ec99aeeb1bfa01028fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100517748"
---
# <a name="get-started-with-azure-machine-learning-studio-classic-in-r"></a>R 'de Azure Machine Learning Studio (klasik) kullanmaya başlama

**Uygulama hedefi:** ![ Bu bir onay işaretidir ve bu makalenin Machine Learning Studio (klasik) için geçerli olduğu anlamına gelir. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasik) ![ Bu bir X ' dir ve bu makalenin Azure Machine Learning için geçerli olduğu anlamına gelir.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

<!-- Stephen F Elston, Ph.D. -->
Bu öğreticide, R kodu oluşturmak, test etmek ve yürütmek için Azure Machine Learning Studio (klasik) kullanmayı öğreneceksiniz. Sonunda, tamamlanmış bir tahmin çözümünüz olacaktır.

> [!div class="checklist"]
> * Veri temizleme ve dönüştürme için kod oluşturun.
> * Veri kümizdeki değişkenlerin bazıları arasındaki bağıntıları çözümleyin.
> * MILK üretimi için mevsimsel bir zaman serisi tahmin modeli oluşturun.


Machine Learning Studio (klasik) birçok güçlü makine öğrenimi ve veri işleme modülü içerir. R programlama dili ile bu birleşim, R 'nin esneklik ve derin analizine sahip Machine Learning Studio (klasik) dağıtımı için ölçeklenebilirlik ve kolaylıklar sağlar.

Tahmin, yaygın olarak kullanılan ve kullanışlı analitik bir yöntemdir. Ortak, dönemsel öğelerin satışlarından tahmine dayalı ve makro ekonomik değişkenleri tahmin etmek için en uygun stok düzeylerini belirleme aralığından yararlanır. Tahmin genellikle zaman serisi modelleriyle yapılır. Zaman serisi verileri, değerlerinin zaman dizinine sahip olduğu bir veri. Saat dizini normal olabilir, örneğin, her ay veya her dakika. Saat dizini de düzensiz olabilir. Zaman serisi modeli, zaman serisi verilerine göre belirlenir. R programlama dili, esnek bir çerçeve ve zaman serisi verileri için kapsamlı analiz içerir.

## <a name="get-the-data"></a>Verileri alma

Bu öğreticide, çeşitli süt ürünlerinin üretimi hakkında aylık bilgiler ve bir kıyaslama ücretlendirmesi olan morijinalini FAT fiyatı dahil olmak üzere California süt üretim ve fiyatlandırma verilerini kullanırsınız.

R betikleriyle birlikte bu makalede kullanılan veriler, [Machinelearningsamples-Notebook/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)adresinden indirilebilir. Dosyadaki veriler `cadairydata.csv` Başlangıçta, University of Wıconte [süt piyasa sitesinden](https://dairymarkets.com)erişilebilen bilgilerden sentezdir.

## <a name="interact-with-r-language-in-machine-learning-studio-classic"></a><a id="mlstudio"></a>Machine Learning Studio 'de R diliyle etkileşim kurma (klasik)

Bu bölümde, Machine Learning Studio (klasik) ortamındaki R programlama diliyle etkileşim kurma hakkında bazı temel bilgiler yer alır. R dili, Machine Learning Studio (klasik) ortamda özelleştirilmiş analiz ve veri işleme modülleri oluşturmak için güçlü bir araç sağlar.

Küçük ölçekte R kodu geliştirmek, test etmek ve hatalarını ayıklamak için RStudio kullanacağız. Bu kod daha sonra kesip Machine Learning Studio (klasik) içinde çalıştırılmaya hazırlanmak üzere bir [Execute R betik][execute-r-script] modülüne yapıştırılır.

### <a name="the-execute-r-script-module"></a>R betiği Yürüt modülü

Machine Learning Studio (klasik) içinde R betikleri [Execute r betiği][execute-r-script] modülü içinde çalıştırılır. Machine Learning Studio (klasik) içinde [R betiği yürütme][execute-r-script] modülüne bir örnek burada gösterilmiştir.

 ![R programlama dilini gösteren ekran görüntüsü: Machine Learning Studio (klasik) ' de seçili R Betiği Çalıştır modülü.](./media/r-quickstart/fig1.png)

Önceki görüntüde, [R betiği Yürüt][execute-r-script] modülüyle çalışmaya yönelik Machine Learning Studio (klasik) ortamının bazı önemli kısımları gösterilmektedir:

* Denemekte olan modüller Orta bölmede gösterilir.
* Sağ bölmenin üst kısmında, R betiklerinizi görüntülemek ve düzenlemek için kullanabileceğiniz bir pencere bulunur.
* Sağ bölmenin alt bölümü, [Execute R betiğinin][execute-r-script]bazı özelliklerini gösterir. Bu bölmenin uygun bölgelerini seçerek hata ve çıkış günlüklerini görüntüleyebilirsiniz.

Bu makalenin geri kalanında, [Execute R betiğini][execute-r-script] daha ayrıntılı bir şekilde ele alacağız.

Karmaşık R işlevleriyle çalışırken, RStudio 'da düzenlemenizi, test etmenizi ve hata ayıklamanızı öneririz. Tüm yazılım geliştirmede olduğu gibi, kodunuzu artımlı olarak genişletin ve küçük ve basit test durumlarında test edin. Sonra işlevlerinizi kes ve [Çalıştır r betik][execute-r-script] modülünün r betiği penceresine yapıştırın. Bu yaklaşım, hem RStudio tümleşik geliştirme ortamını (IDE) hem de Machine Learning Studio (klasik) gücünü kullanmanıza olanak sağlar.

#### <a name="execute-r-code"></a>R kodu yürütme

[Yürüt komut dosyası][execute-r-script] modülündeki herhangi bir r kodu, **Çalıştır** düğmesini seçerek denemeyi çalıştırdığınızda yürütülür. Yürütme tamamlandığında, [R betiği Yürüt][execute-r-script] simgesinde bir onay işareti görünür.

#### <a name="defensive-r-coding-for-machine-learning-studio-classic"></a>Machine Learning Studio için savunma kodu kodlama (klasik)

Machine Learning Studio (klasik) kullanarak bir Web hizmeti için R kodu geliştiriyorsanız, kodunuzun beklenmedik bir veri girişi ve özel durumlarla nasıl uğratacağını kesin olarak planlamanız gerekir. Netme sağlamak için, gösterilen kod örneklerinin çoğunda çok fazla denetim veya özel durum işleme yöntemi eklenmemiştir. Devam edebilmemiz için, R 'nin özel durum işleme özelliğini kullanarak işlevlere birkaç örnek vereceğiz.

R özel durum işlemenin daha kapsamlı bir işleme ihtiyacınız varsa, [daha fazla okuma](#appendixb)bölümünde listelenen ve Wickheham 'ın ilgili bölümlerini okuyun.

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Machine Learning Studio hata ayıklama ve test R (klasik)

R kodunuzda, RStudio 'daki küçük ölçekte test edin ve hata ayıklayın. Ayrıca, [r betiği yürütme][execute-r-script] bölümünde r kod sorunlarını izlemeniz gereken durumlar da vardır. Ayrıca, sonuçlarınızı Machine Learning Studio (klasik) olarak denetlemek iyi bir uygulamadır.

R kodunuzun ve Machine Learning Studio (klasik) platformun yürütülmesi birincil olarak çıktı. log içinde bulunur. Bazı ek bilgiler hata. log dosyasında.

R kodunuzu çalıştırırken Machine Learning Studio (klasik) içinde bir hata oluşursa, ilk eylem kurağınız hata. log dosyasına bakmak için olmalıdır. Bu dosya, hatayı anlamanıza ve düzeltmenize yardımcı olacak yararlı hata iletileri içerebilir. Hata. log dosyasını görüntülemek için, hatayı içeren [R betiği yürütme][execute-r-script] için Özellikler bölmesinde **hata günlüğünü görüntüle** ' yi seçin.

Örneğin, bir [Execute r betik][execute-r-script] modülünde, tanımsız değişkeni olan aşağıdaki r kodunu çalıştırdık.

```r
x <- 1.0
z <- x + y
```

Bu kod yürütülemezse, bu da hata koşuluna neden olur. Özellikler bölmesinde **hata günlüğünü görüntüle** ' nin seçilmesi aşağıdaki ekranı oluşturur.

  ![Bir hata iletisi açılır penceresi gösteren ekran görüntüsü.](./media/r-quickstart/fig2.png)

R hata iletisini görmek için output. log dosyasına bakmamız gerekiyor gibi görünüyor. [R betiği Yürüt][execute-r-script] modülünü seçin ve ardından sağ taraftaki Özellikler bölmesinde **çıktıyı görüntüle. log** öğesini seçin. Yeni bir tarayıcı penceresi açılır ve aşağıdaki hata iletisi görüntülenir.

```output
[Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
---------- Start of error message from R ----------
object 'y' not found


object 'y' not found
----------- End of error message from R -----------
```

Bu hata iletisi, hiçbir sürme içermez ve sorunu açık bir şekilde tanımlar.

R 'deki herhangi bir nesnenin değerini incelemek için, bu değerleri output. log dosyasına yazdırabilirsiniz. Nesne değerlerini inceleme kuralları, aslında etkileşimli bir R oturumundaki ile aynıdır. Örneğin, bir satıra bir değişken adı girerseniz, nesnenin değeri output. log dosyasına yazdırılır.

#### <a name="packages-in-machine-learning-studio-classic"></a>Machine Learning Studio paketler (klasik)

Machine Learning Studio (klasik) 350 ' den fazla önceden yüklenmiş R dil paketi ile gelir. Önceden yüklenmiş paketlerin bir listesini almak için [R betiği Yürüt][execute-r-script] modülünde aşağıdaki kodu kullanabilirsiniz.

```r
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Bu kodun son satırını şu anda anlamıyorsanız, okumaya devam edin. Bu makalenin geri kalanında, Machine Learning Studio (klasik) ortamında R kullanarak kapsamlı bir tartışacağız.

### <a name="introduction-to-rstudio"></a>RStudio 'ya giriş

RStudio, R için yaygın olarak kullanılan bir IDE 'dir. Bu kılavuzda kullanılan R kodundan bazılarını düzenlemek, test etmek ve hatalarını ayıklamak için RStudio kullanacağız. R kodu test edildikten ve hazırlanıyor, RStudio düzenleyicisinden bir Machine Learning Studio (klasik) [yürütme R betik][execute-r-script] modülüne kesip yapıştırabilirsiniz.

Masaüstü makinenizde R programlama dili yüklü değilse, bunu şimdi yapın. Açık kaynaklı R dilinin ücretsiz indirmeleri, [kapsamlı r Arşivi ağı 'nda (CRAN)](https://www.r-project.org/)bulunabilir. Windows, macOS ve Linux/UNIX için İndirilenler mevcuttur. Yakın bir yansıtma seçin ve indirme yönergelerini izleyin. Ayrıca, CRAN birçok yararlı analiz ve veri işleme paketi içerir.

RStudio 'ya yeni başladıysanız Masaüstü sürümünü indirip yüklemelisiniz. Rstudio 'da Windows, macOS ve Linux/UNIX için RStudio İndirmeleri [bulabilirsiniz.](http://www.rstudio.com/products/RStudio/) Masaüstü makinenize RStudio 'Yu yüklemek için belirtilen yönergeleri izleyin.

Rstudio [IDE 'Yi kullanarak](https://support.rstudio.com/hc/sections/200107586-Using-RStudio)rstudio 'ya bir öğreticiye giriş sunulmaktadır.

RStudio kullanma hakkında daha fazla bilgi için bkz. [rstudio belgeleri Kılavuzu](#appendixa).

## <a name="get-data-in-and-out-of-the-execute-r-script-module"></a><a id="scriptmodule"></a>R betiği yürütme modülüne ve dışına veri alma

Bu bölümde, [R betik modülünü yürütme][execute-r-script] ve dışına alma hakkında bilgi edineceksiniz. Ayrıca, [R betiği yürütme][execute-r-script] modülünün içine ve dışına nasıl okunan çeşitli veri türlerini nasıl işleyeceğinizi gözden geçireceğiz.

Bu bölümün tam kodu, [Machinelearningsamples-Notebook/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)içinde bulunur.

### <a name="load-and-check-data"></a>Verileri yükleme ve denetleme

#### <a name="load-the-dataset"></a><a id="loading"></a>Veri kümesini yükleme

**csdairydata.csv** dosyasını Machine Learning Studio (klasik) içine yükleyerek başlayacağız.

1. Machine Learning Studio (klasik) ortamınızı başlatın.
1. Ekranın sol alt kısmındaki **+ Yeni** ' yi seçin ve **veri kümesi**' ni seçin.
1. **Yerel dosya ' dan** seçim yapın ve ardından dosyayı seçmek için **Araştır** ' ı seçin.
1. Veri kümesinin türü olarak **üst bilgi (. csv) Ile genel CSV dosyasını** seçtiğinizden emin olun.
1. Onay işaretini seçin.
1. Veri kümesi karşıya yüklendikten sonra, veri **kümeleri** sekmesini seçtiğinizde yeni veri kümesini görmeniz gerekir.

#### <a name="create-an-experiment"></a>Deneme oluşturma

Artık Machine Learning Studio (klasik) ' de bazı verileriniz olduğuna göre, analiz yapmak için bir deneme oluşturuyoruz.  

1. Ekranın sol alt kısmındaki **+ Yeni** ' **yi seçin ve**  >  **boş deneme**' yı seçin.
1. Sayfanın üst kısmındaki başlıkta **oluşturulan** denemeyi seçerek ve değiştirerek denemenizi adlandırın. Örneğin, bunu **CA süt analiziyle** değiştirin.
1. Deneme sayfasının sol tarafında, **kaydedilmiş veri kümeleri**  >  **veri kümelerim**' i seçin. Daha önce karşıya yüklediğiniz **cadairydata.csv** dosyasını görmeniz gerekir.
1. **csdairydata.csv veri kümesini** deneme üzerine sürükleyin.
1. Sol bölmenin üst kısmındaki **deneme öğelerini ara** kutusuna [Yürüt R betiği][execute-r-script]girin. Modül arama listesinde görünür.
1. [Yürüt R betiği][execute-r-script] modülünü paletle sürükleyin.
1. **csdairydata.csv veri kümesinin** çıkışını, [Execute R betiğinin][execute-r-script]en soldaki giriş (**dataSet1**) öğesine bağlayın.
1. **Kaydet**’i seçin.

Bu noktada, denemeniz bu örneğe benzer bir şekilde görünmelidir.

![Veri kümesi ile CA süt çözümlemesini denemeyi gösteren diyagram ve R betik modülünü yürütün.](./media/r-quickstart/fig3.png)


#### <a name="check-on-the-data"></a>Verileri denetleyin

Deneyimize yüklediğimiz verilere göz atalım. Deneyde **cadairydata.csv veri kümesinin** çıkışını seçin ve **Görselleştir**' i seçin. Bu özete benzer bir şey görmeniz gerekir.

![cadairydata.csv veri kümesinin özetini gösteren ekran görüntüsü.](./media/r-quickstart/fig4.png)

Bu görünümde çok sayıda yararlı bilgi gösterilir. Veri kümesinin ilk birkaç satırını görebiliriz. Bir sütun seçmemiz halinde, **istatistik** bölümünde sütun hakkında daha fazla bilgi gösterilir. Örneğin, **özellik türü** satırı, sütuna hangi veri türlerinin Machine Learning Studio (klasik) atandığını gösterir. Önemli bir iş yapmadan önce bu görünümü kontrol edin.

### <a name="first-r-script"></a>İlk R betiği

Machine Learning Studio (klasik) içinde denemek için basit bir ilk R betiği oluşturalım. RStudio 'da aşağıdaki betiği oluşturulup test ettik.

```r
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
str(cadairydata)
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Şimdi bu betiği Machine Learning Studio (klasik) olarak aktardık. Kesip yapıştırabilirsiniz, ancak bu durumda R betiğini bir ZIP dosyası aracılığıyla aktarabilirsiniz.

### <a name="data-input-to-the-execute-r-script-module"></a>R betiği yürütme modülüne veri girişi

Böylece, [R betiği yürütme][execute-r-script] modülüne yönelik girdilere göz atalım. Bu örnekte, California süt verilerini [Execute R betik][execute-r-script] modülüne okuyacağız.

[R betiği Yürüt][execute-r-script] modülünün üç olası girişi vardır. Uygulamanıza bağlı olarak, bu girişlerin herhangi birini veya tümünü kullanabilirsiniz. Hiç giriş içermeyen bir R betiği de kullanabilirsiniz.

Soldan sağa giderek bu girişlerin her birine bakalım. İmlecinizi girişin üzerine yerleştirerek ve araç ipucunu okuyarak girişlerin her birinin adını görebilirsiniz.

#### <a name="script-bundle"></a>Betik paketi

Betik paketi girişi, zip dosyasının içeriğini [yürütme R betik][execute-r-script] modülüne geçirmenize olanak sağlar. ZIP dosyasının içeriğini R kodunuzda okumak için aşağıdaki komutlardan birini kullanabilirsiniz.

```r
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Machine Learning Studio (klasik), ZIP dosyasındaki dosyaları src/dizinde olduklarından olduğu gibi değerlendirir, bu nedenle dosya adlarınızı bu dizin adıyla birlikte uygulamanız gerekir. Örneğin, ZIP dosyası dosyaları `yourfile.R` ve `yourData.rdata` ZIP dosyasının kökünü içeriyorsa, ve kullandığınızda bu dosyaları olarak adresgörürsünüz `src/yourfile.R` `src/yourData.rdata` `source` `load` .

[Veri kümesini](#loading)yüklerken veri kümelerini yükleme konusunda zaten tartışıldık. Önceki bölümde gösterilen R betiğini oluşturup test ettikten sonra, aşağıdaki adımları uygulayın.

1. R betiğini bir dosyasına kaydedin. R dosyası. Simpleçiz betik dosyasını çağırıyoruz **. R**. İşte bu dosya:

   ```r
   ## Only one of the following two lines should be used
   ## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
   ## If in RStudio, use the second line with read.csv()
   cadairydata <- maml.mapInputPort(1)
   # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
   str(cadairydata)
   pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
   ## The following line should be executed only when running in
   ## Azure Machine Learning Studio (classic)
   maml.mapOutputPort('cadairydata')
   ```

1. Bir ZIP dosyası oluşturun ve komut dosyanızı bu zip dosyasına kopyalayın. Windows 'da dosyaya sağ tıklayıp   >  **sıkıştırılmış klasöre** Gönder ' i seçebilirsiniz. Bu eylem, simpleçizimi içeren yeni bir ZIP dosyası oluşturur **. R** dosyası.

1. Dosyanızı Machine Learning Studio (klasik) **veri kümelerine** ekleyin ve türü **zip** olarak belirtin. Artık veri kümelerinizde ZIP dosyasını görmeniz gerekir.

1. ZIP dosyasını **veri** kümelerinden **ml Studio (klasik) tuvaline** sürükleyin.

1. **ZIP verileri** simgesinin çıkışını, [R betiği Yürüt][execute-r-script] modülünün **betik paketi** girişine bağlayın.

1. `source()` [R betik modülünün yürütülmesi][execute-r-script] için kod penceresine ZIP dosya adınızı içeren işlevi girin. Bu durumda, girdik `source("src/simpleplot.R")` .

1. **Kaydet**’i seçin.

Bu adımlar tamamlandıktan sonra, deneme çalıştırıldığında r betiği [Yürüt][execute-r-script] dosyası ZIP dosyasında yürütülür. Bu noktada, denemeniz bu örneğe benzer bir şekilde görünmelidir.

![Daraltılmış bir R betiği kullanarak bir deneme gösteren diyagram.](./media/r-quickstart/fig6.png)

#### <a name="dataset1"></a>DataSet1

DataSet1 girişini kullanarak, dikdörtgen bir veri tablosunu R kodunuza geçirebilirsiniz. Basit betiğimizde işlev, `maml.mapInputPort(1)` 1 numaralı bağlantı noktasından verileri okur. Bu veriler daha sonra kodunuzda bir dataframe değişken adına atanır. Basit betiğimizde ilk kod satırı atamayı gerçekleştirir.

```r
cadairydata <- maml.mapInputPort(1)
```

**Çalıştır** düğmesini seçerek denemenizi yürütün. Yürütme tamamlandığında, [R betiği Yürüt][execute-r-script] modülünü seçin ve ardından Özellikler bölmesinde **Çıkış günlüğünü görüntüle** ' yi seçin. Tarayıcınızda output. log dosyasının içeriğini gösteren yeni bir sayfa görüntülenmelidir. Aşağı kaydırdığınızda aşağıdaki çıktıya benzer bir şey görmeniz gerekir.

```output
[ModuleOutput] InputDataStructure
[ModuleOutput]
[ModuleOutput] {
[ModuleOutput]  "InputName":Dataset1
[ModuleOutput]  "Rows":228
[ModuleOutput]  "Cols":9
[ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
[ModuleOutput] }
```

Sayfanın altında, sütunlar hakkında daha ayrıntılı bilgiler verilmiştir ve bu, aşağıdaki çıktıya benzer bir şekilde görünür.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput]
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput]
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput]
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput]
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput]
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput]
[ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
[ModuleOutput]
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput]
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput]
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput]
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
```

Bu sonuçlar çoğunlukla, veri çerçevesinde 228 gözlemle ve 9 sütundan oluşan beklendiğinde. Sütun adlarını, R veri türünü ve her bir sütunun bir örneğini görebiliriz.

> [!NOTE]
> Bu aynı yazdırılmış çıktı, r [betiği yürütme][execute-r-script] modülünün r cihaz çıktısından kolayca kullanılabilir. Sonraki bölümde [R betiği yürütme][execute-r-script] modülünün çıkışlarını ele alacağız.  

#### <a name="dataset2"></a>Dataset2

DataSet2 girişinin davranışı DataSet1 ile aynıdır. Bu girişi kullanarak, R kodunuza ikinci bir dikdörtgen veri tablosu geçirebilirsiniz. `maml.mapInputPort(2)`Bağımsız değişkeni 2 olan işlevi, bu verileri geçirmek için kullanılır.  

### <a name="execute-r-script-outputs"></a>R betik çıkışlarını yürütme

#### <a name="output-a-dataframe"></a>Veri çerçevesinin çıktısını al

İşlevi kullanarak bir R veri çerçevesinin içeriğini, result DataSet1 bağlantı noktası aracılığıyla dikdörtgen tablo olarak çıktısını alabilirsiniz `maml.mapOutputPort()` . Basit R betiğimizde, bu eylem aşağıdaki satır tarafından gerçekleştirilir.

```r
maml.mapOutputPort('cadairydata')
```

Denemeyi çalıştırdıktan sonra, result DataSet1 çıkış bağlantı noktasını seçin ve ardından **Görselleştir**' i seçin. Bu örneğe benzer bir şey görmeniz gerekir.

![California süt verilerinin çıktısının görselleştirmesini gösteren ekran görüntüsü.](./media/r-quickstart/fig7.png)

Bu çıktı, tam olarak beklendiğimiz gibi girişle aynı şekilde görünür.

### <a name="r-device-output"></a>R cihaz çıkışı

[Yürütme R betiği][execute-r-script] modülünün cihaz çıktısı iletileri ve grafik çıktısını içerir. R 'deki standart çıkış ve standart hata iletileri R cihaz çıkış bağlantı noktasına gönderilir.

R cihaz çıkışını görüntülemek için, bağlantı noktasını seçin ve ardından **Görselleştir**' i seçin. Burada R betiğindeki standart çıkış ve standart hata görüyoruz.

![R cihaz bağlantı noktasındaki Standart çıktıyı ve standart hatayı gösteren ekran görüntüsü.](./media/r-quickstart/fig8.png)

Aşağı kaydırarak, R betiğimizden alınan grafik çıktısını görüyoruz.

![R cihaz bağlantı noktasındaki grafik çıktısını gösteren ekran görüntüsü.](./media/r-quickstart/fig9.png)

## <a name="data-filtering-and-transformation"></a><a id="filtering"></a>Veri filtreleme ve dönüştürme

Bu bölümde, California süt verilerinde bazı temel veri filtreleme ve dönüştürme işlemlerini gerçekleştiririz. Bu bölümün sonuna kadar veri, analitik model oluşturmak için uygun bir biçimde olacaktır.

Daha belirgin bir şekilde, bu bölümde birkaç ortak veri temizleme ve dönüştürme görevi gerçekletireceğiz: tür dönüşümü, veri çerçevelerine filtre uygulama, yeni hesaplanmış sütunlar ekleme ve değer dönüşümleri yaptık. Bu arka plan, gerçek dünyada sorunlarda karşılaşılan birçok çeşitle ilgilenmenize yardımcı olmalıdır.

Bu bölüm için tam R kodu, [Machinelearningsamples-Notebook/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)içinde mevcuttur.

### <a name="type-transformations"></a>Tür dönüşümleri

Şimdi de California süt verilerini [yürütme r betik][execute-r-script] modülündeki r koduna okuyabilmemiz için sütunlardaki verilerin amaçlanan tür ve biçime sahip olduğundan emin olunması gerekir.

R, dinamik olarak yazılmış bir dildir, bu da veri türlerinin gerektiği şekilde bir başkasına zorlanmasıdır. R 'deki atomik veri türleri sayısal, mantıksal ve karakter içerir. Faktör türü, kategorik verileri depolamak sıkı için kullanılır. Veri türleri hakkında daha fazla bilgi için bkz. daha fazla [okumada](#appendixb)başvurular.

Tablo verileri bir dış kaynaktan R 'ye okunmasında, sonuçta elde edilen türlerin sütunları kontrol etmek her zaman iyi bir fikirdir. Karakter türünde bir sütun isteyebilirsiniz, ancak çoğu durumda sütun faktör olarak görünür veya tam tersi olur. Diğer durumlarda, düşündüğünüz bir sütun karakter verileriyle temsil edilmelidir, örneğin, bir kayan noktalı sayı olarak 1,23 yerine "1,23".

Neyse ki, eşleme mümkün olduğu sürece bir türü diğerine dönüştürmek kolaydır. Örneğin, "Nevada" değerini sayısal bir değere dönüştüremezsiniz, ancak bunu bir faktöre dönüştürebilirsiniz (kategorik değişken). Başka bir örnek olarak, sayısal bir 1 ' i "1" karakterine veya bir faktöre dönüştürebilirsiniz.

Bu dönüştürmelerin herhangi biri için sözdizimi basittir: `as.datatype()` . Bu tür dönüştürme işlevleri aşağıdaki işlevleri içerir:

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Önceki bölümde girdiğimiz sütunların veri türlerine baktığınızda, tüm sütunlar sayısal türündedir. Özel durum, karakter türünde olan "month" etiketini taşıyan sütundur. Bu türü bir faktöre dönüştürelim ve sonuçları test edelim.

Dağılım çizim matrisini oluşturan satırı silmiş ve ay sütununu bir faktöre dönüştürecek bir satır ekledi. Bu deneyde, r kodunu [Execute r betik][execute-r-script] modülünün kod penceresine kesip yapıştıracağız. ZIP dosyasını da güncelleştirebilir ve Machine Learning Studio (klasik) karşıya yükleyebilirsiniz, ancak bu seçenek birkaç adım sürer.

```r
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(cadairydata$Month)
str(cadairydata) # Check the result
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Bu kodu yürütüm ve R betiği için çıkış günlüğüne bakalım. Günlükteki ilgili veriler burada gösterilir.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

Aya ait tür artık **faktör w/14 düzeylerine** sahip olmalıdır. Yılda yalnızca 12 ay olduğu için bu tür bir sorundur. Ayrıca, sonuç veri kümesi bağlantı noktasının **görselleştirilecek** türün **kategorik** olup olmadığını kontrol edebilirsiniz.

Bu sorun, ay sütununun sistematik olarak kodlanmamış olması olabilir. Bazı durumlarda, bir aya Nisan adı verilir ve diğer bir deyişle, bir ay olarak kısaltılabilir. Dizeyi üç karaktere kırparak bu sorunu çözebiliriz. Kod satırı artık aşağıdaki örneğe benzer şekilde görünür.

```r
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Denemeyi yeniden çalıştırın ve çıkış günlüğünü görüntüleyin. Beklenen sonuçlar burada gösterilir.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```


Factor değişkenimizin artık istenen 12 düzeyi vardır.

### <a name="basic-dataframe-filtering"></a>Temel veri çerçevesi filtrelemesi

R veri çerçeveleri güçlü filtreleme özelliklerini destekler. Veri kümeleri, satır ya da sütunlarda mantıksal filtreler kullanılarak alt ayarlanabilir. Birçok durumda, karmaşık filtre ölçütü gerekecektir. Veri çerçevelerini filtrelemeye yönelik kapsamlı örnekler için bkz. [daha fazla okumada](#appendixb)başvurular.

Veri kümeniz üzerinde yaptığımız bir filtre vardır. Cadairydata dataframe içindeki sütunlara baktığınızda, iki gereksiz sütun görürsünüz. İlk sütun, çok faydalı olmayan bir satır numarası içerir. İkinci sütun, Year. month, gereksiz bilgiler içerir. Aşağıdaki R kodunu kullanarak bu sütunları kolayca dışarıda bırakabiliriz.

> [!NOTE]
> Şu andan itibaren, bu bölümde, [yürütme R betiği][execute-r-script] modülüne eklediğimiz ek kodu göstereceğiz. İşlevden *önce* her yeni satırı ekleyeceğiz `str()` . Bu işlevi Machine Learning Studio (klasik) sonuçlarını doğrulamak için kullanırız.

R [betik][execute-r-script] modülündeki r koduna aşağıdaki satırı ekleyeceğiz.

```r
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Bu kodu denemenize çalıştırın ve çıkış günlüğünden elde edilen sonucu denetleyin. Bu sonuçlar burada gösterilir.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  7 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

Artık beklenen sonuçları elde ediyoruz.

### <a name="add-a-new-column"></a>Yeni sütun Ekle

Zaman serisi modelleri oluşturmak için, zaman dizisinin başlangıcından bu yana ayları içeren bir sütun olması uygun olacaktır. Yeni month. Count sütununu oluşturacağız.

Kodu düzenlemenize yardımcı olması için ilk basit işlevimizi oluşturacağız `num.month()` . Daha sonra bu işlevi, veri çerçevesinde yeni bir sütun oluşturmak için uygulayacağız. Yeni kod aşağıdaki gibidir:

```r
## Create a new column with the month count
## Function to find the number of months from the first
## month of the time series
num.month <- function(Year, Month) {
  ## Find the starting year
  min.year  <- min(Year)

  ## Compute the number of months from the start of the time series
  12 * (Year - min.year) + Month - 1
}

## Compute the new column for the dataframe
cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)
```

Şimdi güncelleştirilmiş denemeyi çalıştırın ve sonuçları görüntülemek için çıkış günlüğünü kullanın. Bu sonuçlar burada gösterilir.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```


Her şey çalışıyor gibi görünüyor. Veri Çerçevemizdeki beklenen değerleri içeren yeni bir sütun var.

### <a name="value-transformations"></a>Değer dönüşümleri

Bu bölümde, veri çerçevesinin bazı sütunlarındaki değerler üzerinde bazı basit dönüştürmeler gerçekleştiririz. R dili neredeyse rastgele değer dönüşümlerini destekler. Daha fazla örnek için bkz. daha fazla [okumada](#appendixb)başvurular.

Veri çerçevesinin özetlerindeki değerlere baktığınızda, burada tek bir şey görmeniz gerekir. California 'da üretilmiş mılden daha fazla dondurma mı? Hayır, tabii. Bu, birimlerin farklı olduğu bir sorundur. Fiyat ABD Sterlini ' de, MILI 1.000.000 ABD libre cinsindendir, dondurma, 1.000 ABD galon birimlerinde ve cotsat Cheese, 1.000 ABD Sterlini ' de yer alıyor. Her galon 6,5 libre 'e yaklaşık olarak bir değer aldığımızda, bu değerleri, her biri 1.000 libre olan eşit birimlerde olacak şekilde dönüştürmek için kolayca bir çarpma yapabiliriz.

Tahmin modelimiz için, bu verilerin eğilimi ve dönemsel olarak ayarlanması için bir çoğultıcı model kullanıyoruz. Bir günlük dönüştürmesi, bu işlemi kolaylaştıran doğrusal bir model kullanmamızı sağlar. Günlük dönüşümünü, çarpanın uygulandığı aynı işleve uygulayabiliriz.

Aşağıdaki kodda yeni bir işlev tanımlar `log.transform()` ve bunu sayısal değerleri içeren satırlara uygularsınız. R `Map()` işlevi, `log.transform()` işlevi veri çerçevesinin seçili sütunlarına uygulamak için kullanılır. `Map()`İşlev öğesine benzerdir `apply()` , ancak işleve birden fazla bağımsız değişken listesi için izin verir. Çarpanların listesinin, işleve ikinci bağımsız değişkeni sağladığı unutulmamalıdır `log.transform()` . Bu `na.omit()` işlev, veri çerçevesinde eksik veya tanımsız değerlere sahip olmadığınızdan emin olmak için Temizleme işlemi olarak kullanılır.

```r
log.transform <- function(invec, multiplier = 1) {
  ## Function for the transformation, which is the log
  ## of the input value times a multiplier

  warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                       "ERROR: Arguments to function log.transform must be greate than zero",
                       "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                       "ERROR: Invalid time seies value encountered in function log.transform"
                       )

  ## Check the input arguments
  if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
  if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
  if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

  ## Wrap the multiplication in tryCatch
  ## If there is an exception, print the warning message to
  ## standard error and return NA
  tryCatch(log(multiplier * invec),
           error = function(e){warning(warningmessages[4]); NA})
}


## Apply the transformation function to the 4 columns
## of the dataframe with production data
multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

## Get rid of any rows with NA values
cadairydata <- na.omit(cadairydata)  
```

İşlevde oldukça bir bit vardır `log.transform()` . Bu kodun çoğu, bağımsız değişkenlerle ilgili olası sorunları denetlemektir ya da hesaplamalar sırasında ortaya çıkabilecek özel durumlarla devam edebilir. Bu kodun yalnızca birkaç satırı hesaplamalar.

Savunma programlamanın hedefi, işlemin devam etmesini önleyen tek bir işlevin başarısızlığını önlemektir. Uzun süre çalışan bir çözümlemenin ani bir başarısızlığı, kullanıcılar için sinir bozucu olabilir. Bu durumdan kaçınmak için, varsayılan dönüş değerleri seçilmelidir ve bu da aşağı akış işleme hasarı sınırlayacaktır. Ayrıca, bir şeyin yanlış olduğunu bildiren kullanıcılara uyarı vermek için de bir ileti oluşturulur.

R 'de programlama konusunda savunma yapmak için kullanmıyorsanız, tüm bu kod çok fazla olabilir. Önemli adımlarla adım adım ilerlim:

1. Dört iletiden oluşan bir vektör tanımlanmıştır. Bu iletiler, bu kodla oluşabilecek olası hatalar ve özel durumlar hakkındaki bilgileri iletmek için kullanılır.
1. Her durum için bir değeri döndürür. Daha az yan etkisi olabilecek birçok farklı olasılık vardır. Örneğin, sıfır veya orijinal giriş vektörünün bir vektörünü döndürebiliriz.
1. Denetimler, işlevin bağımsız değişkenlerinde çalıştırılır. Her durumda, bir hata algılanırsa, varsayılan bir değer döndürülür ve işlev tarafından bir ileti oluşturulur `warning()` . `warning()`Bunun yerine kullanıyoruz `stop()` çünkü ikincisi yürütmenin sonlanacak ve kaçınılmaya çalışıyoruz. Bu kod bir yordamsal stilde yazılmıştır, çünkü bu durumda karmaşık ve belirsiz bir işlevsel yaklaşım oluşur.
1. Günlük hesaplamaları ' de sarmalanır, `tryCatch()` böylece özel durumlar, ani bir şekilde durmasına neden olmaz. Olmadan `tryCatch()` , R işlevleri tarafından oluşturulan çoğu hata, yalnızca bunu yapan bir durdurma sinyaliyle sonuçlanır.

Bu R kodunu denemenize yürütün ve çıktı. log dosyasında yazdırılan çıktıya göz atın. Artık günlükte bulunan dört sütunun dönüştürülmüş değerlerini burada gösterildiği gibi görürsünüz.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

Değerlerin dönüştürüldüğünü görüyoruz. MILK üretimi artık diğer tüm süt ürün üretimini büyük ölçüde aşıyor ve artık bir günlük ölçeğine bakıyoruz.

Bu noktada verilerimiz temizlenir ve bazı modelleme için hazırız. [Execute R betik][execute-r-script] modülümüzün sonuç veri kümesi çıkışının görselleştirme özetine baktığınızda, month sütununun, tıpkı yaptığımız gibi 12 benzersiz değer Ile kategorik olduğunu görürsünüz.

## <a name="time-series-objects-and-correlation-analysis"></a><a id="timeseries"></a>Zaman serisi nesneleri ve bağıntı Analizi

Bu bölümde, birkaç temel R zaman serisi nesnesini keşfedeceğiz ve bazı değişkenler arasındaki bağıntıları analiz edeceğiz. Bizim amamız, birkaç lags 'de ikili bağıntı bilgilerini içeren bir veri çerçevesinin çıktısının çıkışını sağlamaktır.

Bu bölümün tam R kodu, [Machinelearningsamples-Not defterleri/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)içinde bulunur.

### <a name="time-series-objects-in-r"></a>R 'deki zaman serisi nesneleri

Daha önce belirtildiği gibi, zaman serisi zamana göre dizinlenen bir veri değerleri dizisidir. R zaman serisi nesneleri, zaman dizinini oluşturmak ve yönetmek için kullanılır. Zaman serisi nesnelerini kullanmanın çeşitli avantajları vardır. Zaman serisi nesneleri, nesne içinde kapsüllenmiş zaman serisi dizin değerlerini yönetme ayrıntılarından çok daha fazla ayrıntı için sizi boşaltın. Ayrıca, zaman serisi nesneleri çizme, yazdırma, modelleme gibi birçok zaman serisi yöntemini kullanmanıza imkan sağlar.

POSIXct zaman serisi sınıfı yaygın olarak kullanılır ve nispeten basittir. Bu zaman serisi sınıfı, dönem başından itibaren 1 Ocak 1970 arasındaki süreyi ölçer. Bu örnekte POSIXct zaman serisi nesneleri kullanacağız. Yaygın olarak kullanılan diğer R zaman serisi nesne sınıfları, Zoo ve XTS (Genişletilebilir zaman serisi) içerir.

### <a name="time-series-object-example"></a>Zaman serisi nesnesi örneği

Bizim örneğimizi kullanmaya başlayalım. Yeni bir [R Betiği Çalıştır][execute-r-script] modülünü denemenize sürükleyin. Mevcut [yürütme r betiği][execute-r-script] modülünün Result DataSet1 çıkış bağlantı noktasını yeni [Execute r betik][execute-r-script] modülünün DataSet1 giriş bağlantı noktasına bağlayın.

İlk örneklerde yaptığımız gibi, örnek boyunca ilerliyoruz. Bazı noktalarda, her adımda yalnızca artımlı ek R kodu satırı göstereceğiz.

#### <a name="read-the-dataframe"></a>Veri çerçevesini okuyun

İlk adım olarak, bir veri çerçevesinde okuyup beklenen sonuçları aldığınızdan emin olun. Aşağıdaki kod işi yapmanız gerekir.

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Şimdi, denemeyi çalıştırın. Yeni yürütme R betiği şeklinin günlüğü bu örneğe benzer şekilde görünmelidir.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
```

Bu veriler beklenen tür ve biçimlerdir. Şimdi month sütunu Factor türündedir ve beklenen düzey sayısına sahiptir.

#### <a name="create-a-time-series-object"></a>Zaman serisi nesnesi oluşturma

Veri çerçevemiz için bir zaman serisi nesnesi eklememiz gerekiyor. Geçerli kodu, POSIXct sınıfının yeni bir sütununu ekleyen aşağıdaki kodla değiştirin.

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Şimdi günlüğe bakın. Bu örnekte olduğu gibi görünmelidir.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...
```

Özet 'den, yeni sütunun POSIXct sınıfının aslında olduğunu görebiliriz.

### <a name="explore-and-transform-the-data"></a>Verileri keşfet ve dönüştürme

Bu veri kümesindeki bazı değişkenleri keşfedelim. Dağılım çizimi matrisi, hızlı bir görünüm oluşturmak için iyi bir yoldur. `str()`Önceki R kodundaki işlevi aşağıdaki satırla değiştirecek.

```r
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Bu kodu çalıştırın ve neler olduğunu görün. R cihaz bağlantı noktasında üretilen çizim bu örneğe benzer şekilde görünmelidir.

![Seçili değişkenlerin dağılım çizim matrisini gösteren ekran görüntüsü.](./media/r-quickstart/fig17.png)

Bu değişkenler arasındaki ilişkilerde bazı tek görünümlü yapı mevcuttur. Belki de bu yapı, verilerdeki eğilimleri ve değişkenleri standartlamadığımızda ortaya çıkar.

### <a name="correlation-analysis"></a>Bağıntı analizi

Bağıntı analizini gerçekleştirmek için, değişkenleri hem eğilim hem de standartlaştırmanız gerekir. Yalnızca `scale()` değişkenleri ortalar ve ölçeklendirilen R işlevini kullanabiliriz. Bu işlev iyi şekilde daha hızlı çalıştırılabilir. Ancak, R 'de savunma programlamasını bir örneğe bakalım.

`ts.detrend()`Aşağıdaki işlev bu işlemlerin her ikisini de gerçekleştirir. Aşağıdaki iki kod satırı verileri de eğilim olarak belirleyip değerleri standartlaştırır.

```r
ts.detrend <- function(ts, Time, min.length = 3){
  ## Function to de-trend and standardize a time series

  ## Define some messages if they are NULL  
  messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                'ERROR: ts.detrend requires argument ts to be of type numeric',
                paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                'ERROR: Detrend regression has failed in ts.detrend',
                'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
  )
  # Create a vector of zeros to return as a default in some cases
  zerovec  <- rep(length(ts), 0.0)

  # The input arguments are not of the same length, return ts and quit
  if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

  # If the ts is not numeric, just return a zero vector and quit
  if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

  # If the ts is too short, just return it and quit
  if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

  ## Check that the Time variable is of class POSIXct
  if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

  ## De-trend the time series by using a linear model
  ts.frame  <- data.frame(ts = ts, Time = Time)
  tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
           error = function(e){warning(messages[5]); zerovec})

  tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
             ts <- ts/stdev},
            error = function(e){warning(messages[6]); zerovec})

  ts
}  
## Apply the detrend.ts function to the variables of interest
df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

## Plot the results to look at the relationships
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")
```

İşlevde oldukça bir bit vardır `ts.detrend()` . Bu kodun çoğu, bağımsız değişkenlerle ilgili olası sorunları denetlemektir ya da hesaplamalar sırasında ortaya çıkabilecek özel durumlarla devam edebilir. Bu kodun yalnızca birkaç satırı, hesaplamaları aslında yapılır.

Değer dönüşümlerindeki savunma programlamanın bir örneğini zaten tartıştık. Her iki hesaplama bloğu de sarmalanır `tryCatch()` . Bazı hatalar için, özgün giriş vektörünü döndürmek mantıklı olur. Diğer durumlarda, sıfırlardan oluşan bir vektör döndürüyoruz.

Serbest bırakma için kullanılan doğrusal gerileme bir zaman serisi gerilemesi olduğunu unutmayın. Öngörücüsü değişkeni bir zaman serisi nesnesidir.

`ts.detrend()`Tanımlandıktan sonra, veri Çerçevemizdeki ilgilendiğiniz değişkenlere uyguladık. Tarafından kullanılarak oluşturulan sonuç listesini `lapply()` veri çerçevesindeki veriye vermemiz gerekir `as.data.frame()` . ' In savunma konuları nedeniyle `ts.detrend()` , değişkenlerden birini işleyememesi başkalarının doğru işlemesini engellemez.

Kodun son satırı, ikili dağılım çizimi oluşturur. R kodunu çalıştırdıktan sonra dağılım grafiğinin sonuçları burada gösterilir.

![Aynı zamanda ve standart zaman serisinin ikili dağılım listesini gösteren ekran görüntüsü.](./media/r-quickstart/fig18.png)

Bu sonuçları, önceki örnekte gösterilenler ile karşılaştırabilirsiniz. Eğilim kaldırılır ve değişkenler standartlaştırılmış olduğunda, bu değişkenler arasındaki ilişkilerdeki çok daha az bir yapı görüyoruz.

Eş ilişkilerini R CCF nesneleri olarak hesaplama kodu aşağıdaki gibidir.

```r
## A function to compute pairwise correlations from a
## list of time series value vectors
pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
  ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
}

## A list of the pairwise indices
corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

## Compute the list of ccf objects
cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

cadairycorrelations
```

Bu kodu çalıştırmak burada gösterilen günlüğü oluşturur.

```output
[ModuleOutput] Loading objects:
[ModuleOutput]   port1
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] [[1]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]    -1     0     1 
[ModuleOutput] 0.148 0.358 0.317 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[2]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.395 -0.186 -0.238 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[3]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.059 -0.089 -0.127 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[4]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]    -1     0     1 
[ModuleOutput] 0.140 0.294 0.293 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[5]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.002 -0.074 -0.124 
```

Her gecikme için bir bağıntı değeri vardır. Bu bağıntı değerlerinin hiçbiri önemli olacak kadar büyük değildir. Her bir değişkeni bağımsız olarak modelliyoruz.

### <a name="output-a-dataframe"></a>Veri çerçevesinin çıktısını al
İkili bağıntıları R CCF nesnelerinin listesi olarak hesapladık. Bu, sonuç veri kümesi çıkış bağlantı noktası gerçekten bir veri çerçevesi gerektirdiğinden oluşan bir sorunu gösterir. Diğer bir deyişle, CCF nesnesi bir listesidir ve yalnızca bu listenin ilk öğesindeki değerleri, çeşitli lags içindeki bağıntıları istiyoruz.

Aşağıdaki kod, kendi listeleri olan CCF nesneleri listesinden öteleme değerlerini ayıklar.

```r
df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
r.names  <- c("Corr Cot Cheese - Ice Cream",
              "Corr Cot Cheese - Milk Prod",
              "Corr Cot Cheese - Fat Price",
              "Corr Ice Cream - Mik Prod",
              "Corr Ice Cream - Fat Price",
              "Corr Milk Prod - Fat Price")

## Build a dataframe with the row names column and the
## correlation dataframe and assign the column names
outframe <- cbind(r.names, df.correlations)
colnames(outframe) <- c.names
outframe


## WARNING!
## The following line works only in Azure Machine Learning Studio (classic)
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

İlk kod satırı biraz karmaşık değildir ve bazı açıklamalar bunu anlamanıza yardımcı olabilir. İçinden çalışırken:

1. Bağımsız değişken **1** olan **[[** işleci, CCF nesne listesinin ilk öğesinden gelen lags 'teki bağıntılar vektörünü seçer.
1. `do.call()`İşlevi, `rbind()` işlevi tarafından döndürülen öğelerin üzerine şunu uygular `lapply()` .
1. `data.frame()`İşlevi, tarafından üretilen sonucu `do.call()` bir veri çerçevesine zorlar.

Satır adlarının veri çerçevesinin bir sütununda olduğunu unutmayın. Bunun yapılması, [yürütme R betiğiyle][execute-r-script]çıkış yaparken satır adlarını korur.

Kodu çalıştırmak, sonuç veri kümesi bağlantı noktasında çıktıyı görüntülemek için **görselleştirin** ' i seçtiğimiz zaman burada gösterilen çıktıyı üretir. Satır adları, hedeflenen şekilde ilk sütunlardır.

![Bağıntı analizinden alınan sonuç çıktısını gösteren ekran görüntüsü.](./media/r-quickstart/fig20.png)

## <a name="time-series-example-seasonal-forecasting"></a><a id="seasonalforecasting"></a>Zaman serisi örneği: mevsimsel tahmin

Verilerimiz artık analiz için uygun bir biçimde olduğundan, değişkenler arasında önemli bir eş ilişki olmadığını belirledik. Bir zaman serisi tahmin modeli taşıyıp oluşturalım. Bu modeli kullanarak, 12 ay 2013 için California mılmi üretimini tahmin edeceğiz.

Tahmin modelimizin iki bileşeni, bir eğilim bileşeni ve bir mevsimlik bileşeni olacaktır. Tüm tahmin, bu iki bileşenin ürünüdür. Bu tür bir model, bir çoğultıcı model olarak bilinir. Alternatif bir eklenebilir modeldir. İlgilendiğiniz değişkenlere zaten bir günlük dönüştürmesi uyguladık, bu da bu analiz tractable 'ı oluşturur.

Bu bölümün tam R kodu, [Machinelearningsamples-Not defterleri/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)içinde bulunur.

### <a name="create-the-dataframe-for-analysis"></a>Analiz için veri çerçevesini oluşturma

Denemeniz için yeni bir [R betiği çalıştırma][execute-r-script] modülü ekleyerek başlayın. Mevcut [Execute R betiği][execute-r-script] modülünün sonuç veri kümesi çıkışını yeni modülün **dataSet1** girişine bağlayın. Sonuç, bu örneğe benzer bir şekilde görünmelidir.

![Yeni Execute R betiği modülü eklenen denemeyi gösteren diyagram.](./media/r-quickstart/fig21.png)

Yalnızca tamamladığımız bağıntı analizinde olduğu gibi, POSIXct zaman serisi nesnesine sahip bir sütun eklememiz gerekiyor. Aşağıdaki kod sütununu ekler.

```r
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Bu kodu çalıştırın ve günlüğe bakın. Sonuç, bu örneğe benzer şekilde görünmelidir.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...
```

Bu sonuçla Analize başlamaya hazırız.

### <a name="create-a-training-dataset"></a>Eğitim veri kümesi oluşturma

Veri çerçevesi oluşturulmuş bir eğitim veri kümesi oluşturuyoruz. Bu veriler, test veri kümizin olan 2013 yılının son 12. hariç tüm gözlemleri içerir. Aşağıdaki kod, veri çerçevesini alt kümeler ve süt üretim ve fiyat değişkenlerinin çizim kümelerini oluşturur. Daha sonra dört üretim ve fiyat değişkenlerinin çizenleri oluşturuyoruz. Bir anonim işlev, çizim için bazı genişletmelerin tanımlanması ve ardından ile diğer iki bağımsız değişkenin listesini yinelemek için kullanılır `Map()` . Bir for döngüsünün burada iyi çalıştığını düşünüyorsanız, doğru demektir. Ancak, R bir işlev dili olduğundan işlevsel bir yaklaşıma bakıyoruz.

```r
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

Kodu çalıştırmak, burada gösterilen R cihaz çıktısından zaman serisi çiztiğini üretir. Zaman ekseni, zaman serisi çizim yönteminin iyi bir avantajı olan tarih birimleridir.

![California süt üretimi ve fiyat verilerinin ilk kez seri çizimi.](./media/r-quickstart/unnamed-chunk-161.png)

![California süt üretimi ve fiyat verilerinin ikinci zaman serisi çizimi.](./media/r-quickstart/unnamed-chunk-162.png)

![California süt üretimi ve fiyat verilerinin üçüncü zaman serisi çizimi.](./media/r-quickstart/unnamed-chunk-163.png).

![California süt üretimi ve fiyat verilerinin dördüncü zaman serisi çizimi](./media/r-quickstart/unnamed-chunk-164.png)

### <a name="a-trend-model"></a>Bir eğilim modeli

Artık bir zaman serisi nesnesi oluşturduğumuz ve verilere baktığımız için, California Milk üretim verileri için bir eğilim modeli oluşturmaya başalım. Zaman serisi gerilemesi kullanabiliriz. Eğitim verilerinde gözlemlenen eğilimi doğru şekilde modellebilmemiz için bir eğim ve kesme noktası olması gereken çizim çizeceğiz.

Verilerin küçük ölçekli verileri verildiğinde, RStudio 'da eğilim için model oluşturacağız ve sonra elde edilen modeli kesip Machine Learning Studio (klasik) olarak yapıştıracağız. RStudio Bu etkileşimli analiz türü için etkileşimli bir ortam sağlar.

İlk denemede, en fazla üç üsle bir polinom gerileme deneyeceğiz. Bu tür modeller çok fazla olduğunda gerçek bir tehlike vardır. Yüksek sipariş koşullarına engel olmak en iyisidir. `I()`İşlevi, içeriğin yorumlanmasını engeller (içeriği olduğu gibi Yorumlar) ve regresyon denkleminde bir tam olarak yorumlanan bir işlev yazmanızı sağlar.

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Bu işlev aşağıdaki çıktıyı üretir.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
##     data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.12667 -0.02730  0.00236  0.02943  0.10586
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 ***
## Time              1.63e-09   1.72e-10    9.47   <2e-16 ***
## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0418 on 212 degrees of freedom
## Multiple R-squared:  0.941,    Adjusted R-squared:  0.94
## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16
```

`Pr(>|t|)`Bu çıkışdaki P değerlerinden (), kare içinde dönem değerinin önemli olabileceğini görebiliriz. `update()`Kare içinde dönemi bırakarak bu modeli değiştirmek için işlevini kullanacağız.

```r
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Bu işlev aşağıdaki çıktıyı üretir.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.12597 -0.02659  0.00185  0.02963  0.10696
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
## Time              1.57e-09   4.32e-11    36.3   <2e-16 ***
## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0417 on 213 degrees of freedom
## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16
```

Bu çıktı daha iyi bir şekilde görünür. Tüm koşullar önemlidir. 2e-16 değeri varsayılan bir değerdir ve çok önemli alınmamalıdır.  

Sağlamlık bir test olarak, California süt üretim verilerinin bir zaman serisi çizimi olarak gösterilen eğilim eğrisini oluşturalım. Modeli oluşturmak ve bir çizim yapmak için Machine Learning Studio (klasik) [Execute R betik][execute-r-script] modelinde (rstudio 'ya değil) aşağıdaki kodu ekledik. Sonuç aşağıdaki örnekte gösterilmiştir.

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Eğilim modeliyle birlikte California mıli üretim verileri gösteriliyor.](./media/r-quickstart/unnamed-chunk-18.png)

Eğilim modeli verileri oldukça iyi bir şekilde sığdırır. Ayrıca, model eğrisindeki tek wggles gibi çok fazla sığdırma konusunda bir kanıt gözükmüyor.

### <a name="seasonal-model"></a>Mevsimlik modeli

El ile bir eğilim modeliyle, tek yapmanız gereken ve mevsimsiz etkileri içermelidir. Aylık ay efektini yakalamak için yılın ayı doğrusal modelde bir kukla değişken olarak kullanacağız. Bir modele faktör değişkenleri gönderdiğinizde, kesme noktası hesaplanmamalıdır. Bunu yapmazsanız, formül aşırı belirtilmiştir ve R istenen faktörlerden birini düşürüyor ancak kesme terimini tutacaktır.

Tatmin edici bir eğilim modeliniz olduğundan, `update()` Yeni terimleri mevcut modele eklemek için işlevini kullanabiliriz. Güncelleştirme formülündeki-1, kesme terimini bırakır. RStudio 'da Şu anda devam ediliyor:

```r
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Bu işlev aşağıdaki çıktıyı üretir.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
##     data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.06879 -0.01693  0.00346  0.01543  0.08726
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 ***
## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 ***
## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 ***
## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 ***
## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 ***
## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 ***
## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 ***
## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 ***
## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 ***
## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 ***
## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 ***
## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 ***
## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0263 on 202 degrees of freedom
## Multiple R-squared:     1,    Adjusted R-squared:     1
## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16
```

Modelin artık bir kesme dönemi olmadığını ve 12 önemli ay faktörü olduğunu görüyoruz. Bu sonuç tam olarak görmek istediğimiz şeydir.

Ayrıca, mevsimlik modelinin ne kadar iyi çalıştığını görmek için California süt üretim verilerinin başka bir zaman serisi çizimi oluşturalım. Modeli oluşturmak ve bir çizim yapmak için Machine Learning Studio (klasik) [Execute R betiğine][execute-r-script] aşağıdaki kodu ekledik.

```r
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

Bu kodun Machine Learning Studio (klasik) çalıştırılması burada gösterilen çizimi oluşturur.

![California mılmi, mevsimsel etkiler dahil olmak üzere modeldir.](./media/r-quickstart/unnamed-chunk-20.png)

Bu örnekte gösterilen verilere Sığdır yerine teşvik. Hem eğilim hem de mevsimsel efekt (aylık değişim) makul bir şekilde görünür.

Modelinize başka bir denetim olarak göz atalım. Aşağıdaki kod, iki modelimizin tahmin edilen değerlerini hesaplar, mevsimlik modelinin kalan değerlerini hesaplar ve ardından eğitim verileri için bu fazlalıklar çizer.

```r
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time&quot;, ylab =&quot;Residuals of Seasonal Model")
```

Kalan çizim burada gösterilmiştir.

![Eğitim verileri için mevsimlik modelinin fazlalıklar.](./media/r-quickstart/unnamed-chunk-21.png)

Bu fazlalıklar makul bir şekilde görünür. 2008-2009 yeniden kullanım açısından, modelimizin özellikle de iyi bir şekilde hesaba sahip olmadığı durumlar dışında belirli bir yapı yoktur.

Bu örnekte gösterilen çizim, fazlalıklar içindeki zamana bağlı desenleri algılamak için yararlıdır. Kullandığımız fazlalıklar hakkında açık yaklaşım, en fazlalıklar, çizim üzerinde zaman sırasına girer. Çizdiğimiz bir `milk.lm$residuals` Çizim, zaman sırası içinde değildir.

Ayrıca, `plot.lm()` bir dizi tanılama grafiğini oluşturmak için kullanabilirsiniz.

```r
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Bu kod, aşağıdaki örneklerde gösterilen bir dizi tanılama grafiğini üretir.

![Mevsimlik modeli için ilk tanılama çizimi.](./media/r-quickstart/unnamed-chunk-221.png)

![Mevsimlik modeli için ikinci tanılama çizimi.](./media/r-quickstart/unnamed-chunk-222.png)

![Mevsimlik modeli için üçüncü tanılama çizimi.](./media/r-quickstart/unnamed-chunk-223.png)

![Mevsimlik modeli için dördüncü tanılama çizimi.](./media/r-quickstart/unnamed-chunk-224.png)

Bu çizimler için çok sayıda etkili şekilde tanımlanmış bir işaret vardır, ancak harika bir sorun oluşmasına neden olmaz. Diğer bir deyişle, normal Q-Q çizinden, kalan ve doğrusal modeller için önemli bir varsayım olan kalanlar.

### <a name="forecasting-and-model-evaluation"></a>Tahmin ve model değerlendirmesi

Örneğimizi tamamlamaya yönelik bir şey daha vardır. Tahminleri hesapladık ve hatayı gerçek verilerle ölçmemiz gerekiyor. Tahminimiz 12 ay 2013 için olacaktır. Bu tahmine yönelik bir hata ölçüsünü eğitim veri kümizin kapsamında olmayan gerçek verilere göre hesaplamanız için işlem yapabilirsiniz. Ayrıca, 18 yıllık eğitim verileriyle ilgili performansı, test verilerinin 12 ayı ile karşılaştırabiliriz.

Zaman serisi modellerinin performansını ölçmek için bir dizi ölçüm kullanılır. Bu durumda, kök ortalama kare (RMS) hatasını kullanacağız. Aşağıdaki işlev, RMS hatasını iki seri arasında hesaplar.

```r
RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
  ## Function to compute the RMS error or difference between two
  ## series or vectors

  messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                "ERROR: Input vector to function RMS.error is too short",
                "ERROR: Input vectors to function RMS.error must be of same length",
                "WARNING: Funtion rms.error has received invald input time series.")

  ## Check the arguments
  if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
    warning(messages[1])
    return(NA)}

  if(length(series1) < min.length) {
    warning(messages[2])
    return(NA)}

  if((length(series1) != length(series2))) {
       warning(messages[3])
    return(NA)}

  ## If is.log is TRUE exponentiate the values, else just copy
  if(is.log) {
    tryCatch( {
      temp1 <- exp(series1)
      temp2 <- exp(series2) },
      error = function(e){warning(messages[4]); NA}
    )
  } else {
    temp1 <- series1
    temp2 <- series2
  }

 ## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute the RMS error in a dataframe
  tryCatch( {
    sqrt(sum((temp1 - temp2)^2) / length(temp1))},
    error = function(e){warning(messages[4]); NA})
}
```

`log.transform()`"Değer dönüşümleri" bölümünde tartışılan işlevde olduğu gibi, bu işlevde çok sayıda hata denetimi ve özel durum kurtarma kodu vardır. Kullanılan ilkeler aynıdır. İş, içine Sarmalanan iki yerde yapılır `tryCatch()` . İlk olarak, değerlerin günlükleriyle çalıştık olduğundan, zaman serisi üs olarak dağıtılır. İkinci olarak, gerçek RMS hatası hesaplanır.

RMS hatasını ölçmek için bir işlev ile donatılmış olduğundan, RMS hatalarını içeren bir veri çerçevesini derleyip çıktım. Yalnızca eğilim modelinin koşullarını ve mevsimsel faktörlerle tüm modeli kapsayacağız. Aşağıdaki kod, oluşturduğumuz iki doğrusal modeli kullanarak işi yapar.

```r
## Compute the RMS error in a dataframe
## Include the row names in the first column so they will
## appear in the output of the Execute R Script
RMS.df  <-  data.frame(
rowNames = c("Trend Model", "Seasonal Model"),
  Traing = c(
  RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
  RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
  Forecast = c(
    RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
    RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
)
RMS.df

## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('RMS.df')
```

Bu kodu çalıştırmak, sonuç veri kümesi çıkış bağlantı noktasında burada gösterilen çıktıyı oluşturur.

![Modellerdeki RMS hatalarının karşılaştırmasını gösteren ekran görüntüsü.](./media/r-quickstart/fig26.png)

Bu sonuçlardan, modele mevsimsel faktörleri eklemenin, RMS hatasını önemli ölçüde azalttığını görüyoruz. Çok fazla değil, eğitim verileri için RMS hatası tahmine göre biraz daha küçüktür.

## <a name="guide-to-rstudio-documentation"></a><a id="appendixa"></a>RStudio belgelerine yönelik kılavuz

RStudio iyi şekilde belgelenmiştir. İşte, başlamanızı sağlamak için RStudio belgelerinin önemli bölümlerine bazı bağlantılar verilmiştir.

* **Proje oluşturma**: R kodunuzu, rstudio 'yu kullanarak projeler halinde düzenleyebilir ve yönetebilirsiniz. Daha fazla bilgi için bkz. [projeleri kullanma](https://support.rstudio.com/hc/articles/200526207-Using-Projects). Bu yönergeleri izleyin ve R kodu örnekleri için bu makaledeki bir proje oluşturun.
* **R kodunu düzenleyin ve yürütün**: rstudio, r kodunu düzenlemek ve çalıştırmak için tümleşik bir ortam sağlar. Daha fazla bilgi için bkz. [kodu düzenlemede ve yürütülüyor](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code).
* **Hata ayıklama**: rstudio güçlü hata ayıklama özellikleri içerir. Bu özellikler hakkında daha fazla bilgi için bkz. [RStudio Ile hata ayıklama](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio). Kesme noktası sorun giderme özellikleri hakkında bilgi için bkz. [kesme noktası sorun giderme](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting).

## <a name="further-reading"></a><a id="appendixb"></a>Daha fazla okuma

Bu R programlama öğreticisi, Machine Learning Studio (klasik) ile R dilini kullanmak için ihtiyaç duyduğunuz temel bilgileri içerir. R hakkında bilginiz yoksa, CRAN üzerinde iki giriş vardır:

* [Yeni başlayanlar Için R](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) Ile Emmanuel paradne, başlamak için iyi bir yerdir.
* W ile [R 'ye giriş](https://cran.r-project.org/doc/manuals/R-intro.html) . N. Venables et al. daha derinliğe gider.

R üzerinde başlamanıza yardımcı olabilecek birçok kitap vardır:

* **R programlamanın sanatı:** Norman Matloff tarafından sunulan Istatistiksel yazılım tasarımının turu, R 'de programlamaya harika bir giriş niteliğindedir.
* Paul Teetor tarafından yapılan **r tanımlama kitabı** , r kullanmaya yönelik bir sorun ve çözüm yaklaşımı sağlar.
* Robert Kabacoff tarafından **yapılacak işlem** başka bir kullanışlı tanıtım defteridir. Yardımcı [hızlı R Web sitesi](https://www.statmethods.net/) yararlı bir kaynaktır.
* **Kınns No,** Patrick, r 'de programlayarak karşılaşılabilecek çok sayıda karmaşık ve zor konuyla ilgilenen, büyük bir insanlı insanlar defteridir. Kitap, [R Inno](https://www.burns-stat.com/documents/books/the-r-inferno/)'da ücretsiz olarak kullanılabilir.
* **Advanced r** by Hadley Wickheham, R 'deki gelişmiş konularda derin bir bakış sağlar. Bu kitabın çevrimiçi sürümü, [Gelişmiş R](http://adv-r.had.co.nz/)'de ücretsiz olarak sunulmaktadır.
* R x Paul Cowpertwaıt ve Andrew Metcalfe **Ile giriş zaman serisi** , zaman serisi analizi için r kullanımına giriş sağlar. Daha çok sayıda teorik metin, R örnekleri sağlar.

Harika internet kaynakları aşağıda verilmiştir:

* [Cran görev görünümü: zaman serisi analizinin](https://cran.r-project.org/web/views/TimeSeries.html) bir R zaman serisi paketleri kataloğu vardır. Belirli zaman serisi nesne paketleri hakkında bilgi için, bu pakete yönelik belgelere bakın.
* [R 'ye giriş](https://www.datacamp.com/courses/introduction-to-r) , video dersleri ve kodlama alıştırmaları ile tarayıcınızın rahatlığını gösteren veri Camp 'ten gelen ücretsiz etkileşimli bir kurs niteliğindedir. En son R teknikleri ve paketleri hakkında etkileşimli öğreticiler vardır.
* DataMentor adresinden [tanımlayıcı kılavuz olan R programlama hakkında bilgi edinin](https://www.datamentor.io/r-programming/) .
* [R CODER](https://r-coder.com/) 'ın, yeni başlayanlar Için ayrıntılı r öğreticileri ve ücretsiz r kursu vardır.
* Clarkson University 'den Kelly siyaha göre [R öğreticisi](https://www.cyclismo.org/tutorial/R/) hızlı bir öğreticidir.
* [Veri becerilerinizi geliştirmek Için En Iyi r dil kaynakları](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html) 60 R 'tan fazla kaynağı listeler.

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
