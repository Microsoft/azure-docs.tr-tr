---
title: R ile çalışmaya başlama
titleSuffix: ML Studio (classic) - Azure
description: Tahmin çözümü oluşturmak için Azure Machine Learning Studio (klasik) ile R dilini kullanmaya başlamak için bu R programlama öğreticisini kullanın.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: f55b6e743ff82d4192cbdd91ba54c92efef432bc
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838736"
---
# <a name="getting-started-with-the-r-programming-language-in-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (klasik) R programlama diliyle çalışmaya başlama

<!-- Stephen F Elston, Ph.D. -->

## <a name="introduction"></a>Giriş

Bu öğretici, R programlama dilini kullanarak Azure Machine Learning Studio (klasik) genişletmeye başlamanıza yardımcı olur. Studio (klasik) içinde R kodu oluşturmak, test etmek ve yürütmek için bu R programlama öğreticisini izleyin. Öğretici aracılığıyla çalışırken, Studio 'nun klasik sürümünde R dilini kullanarak komple bir tahmin çözümü oluşturacaksınız.  

Azure Machine Learning Studio klasik sürümü birçok güçlü makine öğrenimi ve veri işleme modülü içerir. Güçlü R dili, analiz Lingua frani CA 'sı olarak açıklanmaktadır. .Net Studio 'nun klasik sürümündeki analiz ve veri işleme, R kullanılarak genişletilebilir. Bu birleşim, R 'nin esneklik ve derin analizine sahip olan, klasik .NET sürümünün ölçeklenebilirliğini ve kolayca dağıtılmasını sağlar.

### <a name="forecasting-and-the-dataset"></a>Tahmin ve veri kümesi

Tahmin, yaygın olarak kullanılan ve oldukça kullanışlı bir analitik yöntemdir. Ortak, dönemsel öğelerin satışlarından tahmine dayalı, makro ekonomik değişkenleri tahmin etmek için en iyi envanter düzeylerini belirleyen Aralık kullanır. Tahmin genellikle zaman serisi modelleriyle yapılır.

Zaman serisi verileri, değerlerinin zaman dizinine sahip olduğu bir veri. Saat dizini normal, örneğin her ay veya her dakika ya da düzensiz olabilir. Zaman serisi modeli, zaman serisi verilerine göre belirlenir. R programlama dili, esnek bir çerçeve ve zaman serisi verileri için kapsamlı analiz içerir.

Bu kılavuzda, California süt üretimi ve fiyatlandırma verileriyle birlikte çalışıyoruz. Bu veriler, çeşitli süt ürünlerinin üretimi ile ilgili aylık bilgileri ve bir kıyaslama emtia, MILK FAT fiyatını içerir.

R betikleriyle birlikte bu makalede kullanılan veriler, [Machinelearningsamples-Notebook/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)adresinden indirilebilir. `cadairydata.csv` dosyadaki veriler, başlangıçta [https://dairymarkets.com](https://dairymarkets.com)Üniversitesi 'nin University of Wıconconsin tarafından sunulan bilgilerden sentezlenmiş.

### <a name="organization"></a>Kuruluş

Azure Machine Learning Studio ortamının klasik sürümünde analiz ve veri işleme R kodu oluşturma, test etme ve yürütme hakkında bilgi edinmek için çeşitli adımlarda ilerliyoruz.  

* İlk olarak, Azure Machine Learning Studio ortamının klasik sürümünde R dilinin kullanımıyla ilgili temel bilgileri keşfedeceğiz.
* Daha sonra, Azure Machine Learning Studio ortamının klasik sürümündeki veriler, R kodu ve grafikler için g/ç 'nin çeşitli yönlerini tartışmak için ilerliyoruz.
* Daha sonra, veri temizleme ve dönüştürme için kod oluşturarak tahmin çözümümüzün ilk bölümünü oluşturacağız.
* Verilerimizin hazırlandığı veri kümenizdeki değişkenlerin bazıları arasında bağıntılar analizini gerçekleştiririz.
* Son olarak, MILI üretimi için mevsimsel bir zaman serisi tahmin modeli oluşturacağız.

## <a id="mlstudio"></a>Machine Learning Studio 'de R diliyle etkileşim kurma (klasik)

Bu bölümde, Machine Learning Studio (klasik) ortamındaki R programlama diliyle etkileşim kurma hakkında bazı temel bilgiler yer alır. R dili, Azure Machine Learning Studio ortamının klasik sürümü içinde özelleştirilmiş analiz ve veri işleme modülleri oluşturmak için güçlü bir araç sağlar.

R kodunu küçük bir ölçekte geliştirmek, test etmek ve hatalarını ayıklamak için RStudio 'yu kullanacaksınız. Bu kod daha sonra kesip Machine Learning Studio klasik sürümünde çalıştırılmaya HAZIRAN bir [Execute R betik][execute-r-script] modülüne yapıştırın.  

### <a name="the-execute-r-script-module"></a>R betiği Yürüt modülü

Machine Learning Studio klasik sürümü içinde, R betikleri [Execute r betiği][execute-r-script] modülü içinde çalıştırılır. Şekil 1 ' de klasik Machine Learning Studio [R betiği yürütme][execute-r-script] modülüne bir örnek gösterilmiştir.

 ![R programlama dili: Machine Learning Studio (klasik) ' de seçili R Betiği Çalıştır modülü](./media/r-quickstart/fig1.png)

*Şekil 1. R betiğini Yürüt modülünün seçili olduğunu gösteren Machine Learning Studio (klasik) ortam.*

Şekil 1 ' e başvurarak Machine Learning Studio (klasik) ortamının [yürütme, R betik][execute-r-script] modülüyle çalışmaya yönelik bazı önemli bölümlerine bakalim.

* Denemekte olan modüller Orta bölmede gösterilir.
* Sağ bölmenin üst bölümü R betiklerinizi görüntülemek ve düzenlemek için bir pencere içerir.  
* Sağ bölmenin alt bölümü, [yürütme R betiğinin][execute-r-script]bazı özelliklerini gösterir. Bu bölmenin uygun noktaları seçerek hata ve çıkış günlüklerini görüntüleyebilirsiniz.

Tabii ki, bu makalenin geri kalanında [R betiğini Yürüt][execute-r-script] bölümünde daha ayrıntılı bir şekilde ele alınacaktır.

Karmaşık R işlevleriyle çalışırken, RStudio 'da düzenlemenizi, test etmenizi ve hata ayıklamanızı öneririz. Tüm yazılım geliştirmede olduğu gibi, kodunuzu artımlı olarak genişletin ve küçük basit test durumlarında test edin. Sonra işlevlerinizi kes ve [Çalıştır r betik][execute-r-script] modülünün r betiği penceresine yapıştırın. Bu yaklaşım, hem RStudio tümleşik geliştirme ortamını (IDE) hem de klasik Azure Machine Learning Studio 'ın gücünü kullanmanıza olanak sağlar.  

#### <a name="execute-r-code"></a>R kodu yürütme

[Yürüt komut dosyası][execute-r-script] modülündeki herhangi bir r kodu, **Çalıştır** düğmesini seçerek denemeyi çalıştırdığınızda yürütülür. Yürütme tamamlandığında, [R betiği Yürüt][execute-r-script] simgesinde bir onay işareti görünür.

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>Azure Machine Learning için savunma kodu kodlama

Azure Machine Learning Studio klasik sürümünü kullanarak bir Web hizmeti için R kodu geliştiriyorsanız, kodunuzun beklenmedik bir veri girişi ve özel durumları ile nasıl ele alınacağını kesinlikle planlamanız gerekir. Netme sağlamak için, gösterilen kod örneklerinin çoğunda çok fazla denetim veya özel durum işleme yöntemi dahil değildir. Bununla birlikte, devam ettiğimiz için R 'nin özel durum işleme özelliğini kullanarak işlevlere birkaç örnek vereceğiz.  

R özel durum işlemesi için daha fazla işlem yapmanız gerekiyorsa, kitabın ilgili bölümlerini [daha fazla okuma](#appendixb)bölümünde listelenen Wickheham ile okumanızı öneririz.

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Machine Learning Studio hata ayıklama ve test R (klasik)

Yeniden yinelemek için, R kodunuzu RStudio 'daki küçük bir ölçekte test etmenizi ve hata ayıklamanızı öneririz. Bununla birlikte, r [betiği yürütme][execute-r-script] bölümünde r kod sorunlarını izlemeniz gereken durumlar vardır. Ayrıca, Machine Learning Studio klasik sürümünde sonuçlarınızı denetlemek iyi bir uygulamadır.

R kodunuzun yürütmeden ve Azure Machine Learning Studio platformunun klasik sürümünde oluşan çıkış, birincil olarak output. log ' da bulunur. Bazı ek bilgiler hata. log dosyasında görünür.  

R kodunuzu çalıştırırken Machine Learning Studio klasik sürümünde bir hata oluşursa, ilk işlem kurağınız hata. log dosyasına bakmak için olmalıdır. Bu dosya, hatayı anlamanıza ve düzeltmenize yardımcı olacak yararlı hata iletileri içerebilir. Hata. log dosyasını görüntülemek için, hata içeren bir [R betiği yürütme][execute-r-script] için **Özellikler bölmesinde** **hata günlüğünü görüntüle** ' yi seçin.

Örneğin, bir [yürütme r betik][execute-r-script] modülünde bir tanımsız değişken Ile aşağıdaki r kodunu çalıştırdım:

```R
x <- 1.0
z <- x + y
```

Bu kod yürütülemezse bir hata koşuluna neden olur. **Özellikler bölmesinde** **hata günlüğünü görüntüle** ' nin seçilmesi Şekil 2 ' de gösterilen ekranı oluşturur.

  ![Hata iletisi açılan kutusu](./media/r-quickstart/fig2.png)

*Şekil 2. Hata iletisi açılır penceresi.*

R hata iletisini görmek için output. log dosyasına bakmamız gerekiyor gibi görünüyor. [R betiğini Yürüt][execute-r-script] ' ü seçin ve ardından sağdaki **Özellikler bölmesinde** **çıktıyı görüntüle. log** öğesini seçin. Yeni bir tarayıcı penceresi açılır ve aşağıdakiler görüntülenir.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Bu hata iletisi, hiçbir sürme içermez ve sorunu açık bir şekilde tanımlar.

R 'deki herhangi bir nesnenin değerini incelemek için, bu değerleri output. log dosyasına yazdırabilirsiniz. Nesne değerlerini inceleme kuralları, aslında etkileşimli bir R oturumundaki ile aynıdır. Örneğin, bir satıra bir değişken adı yazarsanız, nesnenin değeri output. log dosyasına yazdırılır.  

#### <a name="packages-in-machine-learning-studio-classic"></a>Machine Learning Studio paketler (klasik)

Studio 350 üzeri önceden yüklenmiş R dil paketi ile gelir. Önceden yüklenmiş paketlerin bir listesini almak için [R betiği Yürüt][execute-r-script] modülünde aşağıdaki kodu kullanabilirsiniz.

```R
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Bu kodun son satırını şu anda anlamıyorsanız, okumaya devam edin. Bu makalenin geri kalanında, Studio (klasik) ortamında R kullanarak kapsamlı bir tartışacağız.

### <a name="introduction-to-rstudio"></a>RStudio 'ya giriş

RStudio, R için yaygın olarak kullanılan bir IDE 'dir. RStudio 'Yu, bu kılavuzda kullanılan R kodundan bazılarını düzenlemekte, test etmeye ve hata ayıklamaya yönelik olarak kullanacaksınız. R kodu test edildikten ve hazırlanıyor, yalnızca RStudio düzenleyicisinden bir Machine Learning Studio (klasik) [yürütme R betik][execute-r-script] modülüne kesip yapıştırabilirsiniz.  

Masaüstü makinenizde R programlama dili yüklü değilse, bunu şimdi yapmanızı öneririz. Açık kaynaklı R dilinin ücretsiz indirmeleri [https://www.r-project.org/](https://www.r-project.org/)adresindeki kapsamlı r arşiv ağı 'NDA (Cran) kullanılabilir. Windows, Mac OS ve Linux/UNIX için indirilebilir dosyalar var. Yakındaki bir yansıtma seçin ve indirme yönergelerini izleyin. Ayrıca, CRAN, çok sayıda kullanışlı analiz ve veri işleme paketi içerir.

RStudio 'yu yeni kullanıyorsanız Masaüstü sürümünü indirip yüklemelisiniz. Windows, Mac OS ve Linux/UNIX için RStudio İndirmeleri http://www.rstudio.com/products/RStudio/adresinde bulabilirsiniz. Masaüstü makinenize RStudio 'Yu yüklemek için belirtilen yönergeleri izleyin.  

Rstudio [IDE 'Yi kullanarak](https://support.rstudio.com/hc/sections/200107586-Using-RStudio)rstudio 'ya bir öğreticiye giriş sunulmaktadır.

RStudio ile ilgili ek bilgiler [için aşağıdaki rstudio belgelerine bakın](#appendixa) .  

## <a id="scriptmodule"></a>R betiği yürütme modülüne ve dışına veri alma

Bu bölümde, [R betik modülünü yürütme][execute-r-script] ve dışarı aktarma hakkında bilgi edineceğiz. [R betik modülünü yürütme][execute-r-script] ve dışına okuma çeşitli veri türlerini nasıl işleyeceğinizi inceleyeceğiz.

Bu bölümün tam kodu, [Machinelearningsamples-Notebook/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)içinde bulunur.

### <a name="load-and-check-data-in-machine-learning-studio-classic"></a>Machine Learning Studio verileri yükleme ve denetleme (klasik)

#### <a id="loading"></a>Veri kümesini yükleme

**Csdairydata. csv** dosyasını Azure Machine Learning Studio klasik sürümüne yükleyerek başlayacağız.

1. Azure Machine Learning Studio ortamı klasik sürümünüzü başlatın.
1. Ekranınızın sol alt kısmındaki **+ Yeni** ' yi seçin ve **veri kümesi**' ni seçin.
1. **Yerel dosya ' dan**seçim yapın ve ardından dosyayı seçmek için ' a **gidin** .
1. Veri kümesinin türü olarak **üst bilgi (. csv) Ile genel CSV dosyası** seçtiğinizden emin olun.
1. Onay işaretini seçin.
1. Veri kümesi karşıya yüklendikten sonra, veri **kümeleri** sekmesini seçerek yeni veri kümesini görmeniz gerekir.  

#### <a name="create-an-experiment"></a>Deneme oluşturma

Machine Learning Studio klasik sürümünde bazı verileriniz olduğuna göre, analiz yapmak için bir deneme oluşturmanız gerekir.  

1. Sol alt köşedeki **+ Yeni** ' yi seçin ve **denemeler**' i ve **boş deneme**' yı seçin.
1. Sayfanın üst kısmındaki... başlığı altında **oluşturulan** denemeyi seçerek ve değiştirerek denemenizin adını verebilirsiniz. Örneğin, bunu **CA süt analizine**değiştirme.
1. Deneme sayfasının sol tarafında, **kaydedilen veri kümeleri**' ni ve ardından **veri kümelerim**' i genişletin. Daha önce karşıya yüklediğiniz **cadairydata. csv** ' i görmeniz gerekir.
1. **Csdairydata. csv veri kümesini** , denemenize sürükleyin ve bırakın.
1. Sol bölmenin üst kısmındaki **deneme öğelerini ara** kutusunda, [R betiği Yürüt][execute-r-script]yazın. Modülün arama listesinde göründüğünü görürsünüz.
1. [Execute R betiği][execute-r-script] modülünü palete sürükleyin ve bırakın.  
1. **Csdairydata. csv veri kümesinin** çıkışını, [Execute R betiğinin][execute-r-script]en soldaki giriş (**dataSet1**) öğesine bağlayın.
1. **' Kaydet ' seçeneğini belirtmeyi unutmayın!**  

Bu noktada, denemenizin şekil 3 gibi görünmesi gerekir.

![Veri kümesi ile CA süt çözümleme denemesi ve R betik modülünü yürütme](./media/r-quickstart/fig3.png)

*Şekil 3. Veri kümesi ile CA süt Analizi denemesi yapın ve R betik modülünü yürütün.*

#### <a name="check-on-the-data"></a>Verileri denetleyin

Deneyimize yüklediğimiz verilere göz atalım. Denemenin içinde **cadairydata. csv veri kümesinin** çıkışını seçin ve **Görselleştir**' i seçin. Şekil 4 gibi bir şey görmeniz gerekir.  

![Cadairydata. csv veri kümesinin Özeti](./media/r-quickstart/fig4.png)

*Şekil 4. Cadairydata. csv veri kümesinin Özeti.*

Bu görünümde çok sayıda faydalı bilgi görüyoruz. Bu veri kümesinin ilk birkaç satırını görebiliriz. Bir sütun seçmemiz halinde, Istatistik bölümünde sütun hakkında daha fazla bilgi gösterilir. Örneğin, özellik türü satırı, hangi veri türlerinin Azure Machine Learning Studio hangi klasik sürümünün sütuna atandığını gösterir. Önemli bir iş yapmadan önce bu, iyi bir göz atalım.

### <a name="first-r-script"></a>İlk R betiği

Azure Machine Learning Studio klasik sürümünde denemek için basit bir ilk R betiği oluşturalım. RStudio 'da aşağıdaki betiği oluşturup test ediyorum.  

```R
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

Şimdi bu betiği Azure Machine Learning Studio klasik sürümüne aktarmalıyım. Yalnızca kesip yapıştırabilir. Ancak, bu durumda R betiğimi bir ZIP dosyası aracılığıyla aktaracağım.

### <a name="data-input-to-the-execute-r-script-module"></a>R betiği yürütme modülüne veri girişi

Böylece, [R betiği yürütme][execute-r-script] modülüne yönelik girdilere göz atalım. Bu örnekte, California süt verilerini [Execute R betik][execute-r-script] modülüne okuyacağız.  

[R betiği Yürüt][execute-r-script] modülünün üç olası girişi vardır. Uygulamanıza bağlı olarak, bu girişlerin herhangi birini veya tümünü kullanabilirsiniz. Ayrıca, hiç giriş içermeyen bir R betiği kullanmak da mükemmel bir şekilde mantıklı.  

Soldan sağa giderek bu girişlerin her birine bakalım. İmlecinizi girişin üzerine yerleştirerek ve araç ipucunu okuyarak girişlerin her birinin adını görebilirsiniz.  

#### <a name="script-bundle"></a>Betik paketi

Betik paketi girişi, bir ZIP dosyasının içeriğini [R betik][execute-r-script] modülüne geçirmenize olanak sağlar. ZIP dosyasının içeriğini R kodunuzda okumak için aşağıdaki komutlardan birini kullanabilirsiniz.

```R
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Azure Machine Learning Studio klasik sürümü, zip içindeki dosyaları src/dizinde gibi davranır, bu nedenle dosya adlarınızı bu dizin adı ile önekle uygulamanız gerekir. Örneğin, ZIP dosyaları içeriyorsa `yourfile.R` ve ZIP köküne `yourData.rdata`, `source` ve `load`kullanırken bunları `src/yourfile.R` ve `src/yourData.rdata` olarak ele almanız gerekir.

[Veri kümesini](#loading)yüklerken veri kümelerini yükleme konusunda zaten tartışıldık. Önceki bölümde gösterilen R betiğini oluşturup sınadıktan sonra şunları yapın:

1. R betiğini bir dosyasına kaydedin. R dosyası. Komut dosyası dosyamı "simpleçiz" olarak çağırdım. R ". İçeriği burada bulabilirsiniz.

   ```R
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

1. Bir ZIP dosyası oluşturun ve komut dosyanızı bu zip dosyasına kopyalayın. Windows 'da dosyaya sağ tıklayıp **Gönder**' i ve sonra **Sıkıştırılmış klasör**' i seçin. Bu, "simpleçiz" öğesini içeren yeni bir ZIP dosyası oluşturur. R "dosyası.

1. Dosyanızı **zip**olarak belirterek Machine Learning Studio klasik sürümündeki **veri kümelerine** ekleyin. Artık veri kümelerinizde ZIP dosyasını görmeniz gerekir.

1. ZIP dosyasını **veri** kümelerinden **ml Studio (klasik) tuvaline**sürükleyin ve bırakın.

1. **ZIP verileri** simgesinin çıkışını, [R betiği Yürüt][execute-r-script] modülünün **betik paketi** girişine bağlayın.

1. [R betik modülünün yürütülmesi][execute-r-script] için kod penceresine ZIP dosya adınızla birlikte `source()` işlevini yazın. Bu durumda `source("src/simpleplot.R")`yazdım.  

1. **Kaydet**' i seçtiğinizden emin olun.

Bu adımlar tamamlandıktan sonra, deneme çalıştırıldığında [r betiği Yürüt][execute-r-script] , ZIP dosyasındaki r betiğini yürütür. Bu noktada, denemenizin Şekil 5 ' i gibi görünmesi gerekir.

![Daraltılmış R betiği kullanarak deneme](./media/r-quickstart/fig6.png)

*Şekil 5. Daraltılmış R betiği kullanmayı deneyin.*

#### <a name="dataset1"></a>DataSet1

DataSet1 girişini kullanarak, dikdörtgen bir veri tablosunu R kodunuza geçirebilirsiniz. Basit betiğimizde `maml.mapInputPort(1)` işlevi, bağlantı noktası 1 ' den verileri okur. Bu veriler daha sonra kodunuzda bir dataframe değişken adına atanır. Basit betiğimizde ilk kod satırı atamayı gerçekleştirir.

```R
cadairydata <- maml.mapInputPort(1)
```

**Çalıştır** düğmesini seçerek denemenizi yürütün. Yürütme tamamlandığında, [R betiği Yürüt][execute-r-script] modülünü seçin ve ardından Özellikler bölmesinde **Çıkış günlüğünü görüntüle** ' yi seçin. Tarayıcınızda output. log dosyasının içeriğini gösteren yeni bir sayfa görüntülenmelidir. Aşağı kaydırdığınızda aşağıdakine benzer bir şey görmeniz gerekir.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Sayfanın altında, sütunlar hakkında daha ayrıntılı bilgiler verilmiştir ve bu, aşağıdakine benzer şekilde görünür.

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

Bu sonuçlar çoğunlukla, veri çerçevesinde 228 gözlemle ve 9 sütundan oluşan beklendiğinde. Sütun adlarını, R veri türünü ve her bir sütunun örneğini görebiliriz.

> [!NOTE]
> Bu aynı yazdırılmış çıktı, r [betiği yürütme][execute-r-script] modülünün r cihaz çıktısından kolayca kullanılabilir. Sonraki bölümde [R betiği yürütme][execute-r-script] modülünün çıkışları tartışılacağız.  

#### <a name="dataset2"></a>Dataset2

DataSet2 girişinin davranışı DataSet1 ile aynıdır. Bu girişi kullanarak, ikinci bir dikdörtgen veri tablosunu R kodunuza geçirebilirsiniz. Bağımsız değişkeni 2 olan `maml.mapInputPort(2)`işlevi, bu verileri geçirmek için kullanılır.  

### <a name="execute-r-script-outputs"></a>R betik çıkışlarını yürütme

#### <a name="output-a-dataframe"></a>Veri çerçevesinin çıktısını al

`maml.mapOutputPort()` işlevini kullanarak bir R veri çerçevesinin içeriğini, result DataSet1 bağlantı noktası aracılığıyla dikdörtgen tablo olarak çıktısını alabilirsiniz. Basit R betiğimizde bu, aşağıdaki satır tarafından gerçekleştirilir.

```
maml.mapOutputPort('cadairydata')
```

Denemeyi çalıştırdıktan sonra, result DataSet1 çıkış bağlantı noktasını seçin ve ardından **Görselleştir**' i seçin. Şekil 6 ' ya benzer bir şey görmeniz gerekir.

![California süt verilerinin çıktısının görselleştirmesi](./media/r-quickstart/fig7.png)

*Şekil 6. California süt verilerinin çıktısının görselleştirmesi.*

Bu çıktı, tam olarak beklendiğimiz gibi girişle aynı şekilde görünür.  

### <a name="r-device-output"></a>R cihaz çıkışı

[Yürütme R betiği][execute-r-script] modülünün cihaz çıktısı iletileri ve grafik çıktısını içerir. R 'deki standart çıkış ve standart hata iletileri R cihaz çıkış bağlantı noktasına gönderilir.  

R cihaz çıkışını görüntülemek için, bağlantı noktasını seçin ve ardından **Görselleştir**' i seçin. Şekil 7 ' de R betiğindeki standart çıkış ve standart hata görüyoruz.

![R cihaz bağlantı noktasından standart çıkış ve standart hata](./media/r-quickstart/fig8.png)

*Şekil 7. R cihaz bağlantı noktasından standart çıkış ve standart hata.*

Aşağı kaydırarak R betiğimizden alınan grafik çıktısını Şekil 8 ' de görüyorsunuz.  

![R cihaz bağlantı noktasından grafik çıkışı](./media/r-quickstart/fig9.png)

*Şekil 8. R cihaz bağlantı noktasından alınan grafik çıktısı.*  

## <a id="filtering"></a>Veri filtreleme ve dönüştürme

Bu bölümde, California süt verilerinde bazı temel veri filtreleme ve dönüştürme işlemleri gerçekleştirilecektir. Bu bölümün sonuna kadar veri, analitik model oluşturmak için uygun bir biçimde olacaktır.  

Daha belirgin bir şekilde, bu bölümde birkaç ortak veri temizleme ve dönüştürme görevi gerçekleştirmeleri gerekir: tür dönüşümü, veri çerçevelerine filtre uygulama, yeni hesaplanmış sütunlar ve değer dönüşümleri. Bu arka plan, gerçek dünyada sorunlarda karşılaşılan birçok çeşitle ilgilenmenize yardımcı olmalıdır.

Bu bölüm için tam R kodu, [Machinelearningsamples-Notebook/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)içinde mevcuttur.

### <a name="type-transformations"></a>Tür dönüşümleri

Şimdi de California süt verilerini [yürütme r betik][execute-r-script] modülündeki r koduna okuyabilmemiz için sütunlardaki verilerin amaçlanan tür ve biçime sahip olduğundan emin olunması gerekir.  

R, dinamik olarak yazılmış bir dildir, bu da veri türlerinin gerektiği şekilde bir başkasına zorlanmasıdır. R 'deki atomik veri türleri sayısal, mantıksal ve karakter içerir. Faktör türü, kategorik verileri depolamak sıkı için kullanılır. [Daha fazla](#appendixb) bilgi için başvurularda veri türleriyle ilgili daha fazla bilgi bulabilirsiniz.

Tablo verileri bir dış kaynaktan R 'ye okundüğünde, sütunlardaki sonuç türlerini denetlemek her zaman iyi bir fikirdir. Karakter türünde bir sütun isteyebilirsiniz, ancak çoğu durumda bu, faktör olarak gösterilir veya tam tersi olur. Diğer durumlarda, düşündüğünüz bir sütun, bir kayan noktalı sayı olarak 1,23 yerine ' 1,23 ' gibi karakter verileriyle temsil edilmelidir.  

Neyse ki, eşleme mümkün olduğu sürece bir türü diğerine dönüştürmek kolaydır. Örneğin, ' Nevada ' değerini sayısal bir değere dönüştüremezsiniz, ancak bunu bir faktöre dönüştürebilirsiniz (kategorik değişken). Başka bir örnek olarak, sayısal bir 1 ' i ' 1 ' karakterine veya bir faktöre dönüştürebilirsiniz.  

Bu dönüşümlerden herhangi biri için sözdizimi basittir: `as.datatype()`. Bu tür dönüştürme işlevleri aşağıdakileri içerir.

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Önceki bölümde girdiğimiz sütunların veri türlerine bakıyor: tüm sütunlar, karakter türünde olan ' month ' etiketli sütun dışında, sayısal türündedir. Şimdi bunu bir etmene dönüştürüp sonuçları test edin.  

Dağınık terçiz matrisini oluşturan satırı sildim ve ' month ' sütununu bir faktöre dönüştüren bir satır ekledi. Denemem sırasında r kodunu [Execute r betik][execute-r-script] modülünün Code penceresine kesip yapıştırırım. ZIP dosyasını da güncelleştirebilir ve Azure Machine Learning Studio klasik sürümüne yükleyebilirsiniz, ancak bu birkaç adım sürer.  

```R
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

Bu kodu yürütüm ve R betiği için çıkış günlüğüne bakalım. Günlükteki ilgili veriler Şekil 9 ' da gösterilmektedir.

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

*Şekil 9. Bir faktör değişkeni olan veri çerçevesinin Özeti.*

Month için tür artık '**Factor w/14 seviye**' deyin. Bu, yılda yalnızca 12 ay olduğundan bir sorundur. Ayrıca, sonuç veri kümesi bağlantı noktasının **görselleştirilecek** türün '**kategorik**' olup olmadığını kontrol edebilirsiniz.

Sorun, ' month ' sütununun sistematik olarak kodlanmamalıdır. Bazı durumlarda, bir ay Nisan olarak adlandırılır ve başkaları da bir şekilde kısaltılabilir. Dizeyi 3 karaktere kırparak bu sorunu çözebiliriz. Kod satırı artık aşağıdakine benzer şekilde görünür:

```R
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Denemeyi yeniden çalıştırın ve çıkış günlüğünü görüntüleyin. Beklenen sonuçlar Şekil 10 ' da gösterilmiştir.  

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

*Şekil 10. Doğru sayıda faktör düzeyi olan veri çerçevesinin Özeti.*

Factor değişkenimizin artık istenen 12 düzeyi vardır.

### <a name="basic-data-frame-filtering"></a>Temel veri çerçevesi filtrelemesi

R veri çerçeveleri güçlü filtreleme özelliklerini destekler. Veri kümeleri, satır ya da sütunlarda mantıksal filtreler kullanılarak alt ayarlanabilir. Birçok durumda, karmaşık filtre ölçütü gerekecektir. Aşağıda [daha fazla okumada](#appendixb) bulunan başvurular, veri çerçevelerini filtrelemeye yönelik kapsamlı örnekler içerir.  

Veri kümeniz üzerinde yaptığımız bir filtre vardır. Cadairydata dataframe içindeki sütunlara baktığınızda, iki gereksiz sütun görürsünüz. İlk sütun, çok faydalı olmayan bir satır numarasını tutar. İkinci sütun, Year. month, gereksiz bilgiler içerir. Aşağıdaki R kodunu kullanarak bu sütunları kolayca dışarıda bırakabiliriz.

> [!NOTE]
> Bu bölümde, şimdi [R betiği Yürüt][execute-r-script] modülüne eklemem gereken ek kodu göstereceğiz. `str()` işlevinden **önce** her yeni satırı ekleyebilirim. Azure Machine Learning Studio klasik sürümünde sonuçlarımı doğrulamak için bu işlevi kullanıyorum.

[R komut dosyasını yürüt][execute-r-script] modülünde r kodum 'a aşağıdaki satırı ekle.

```R
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Bu kodu denemenize çalıştırın ve çıkış günlüğünden elde edilen sonucu denetleyin. Bu sonuçlar Şekil 11 ' de gösterilmiştir.

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

*Şekil 11. İki sütunlu veri çerçevesinin Özeti kaldırıldı.*

İyi haber! Beklenen sonuçlar elde ediyoruz.

### <a name="add-a-new-column"></a>Yeni sütun Ekle

Zaman serisi modelleri oluşturmak için, zaman dizisinin başlangıcından bu yana ayları içeren bir sütun olması uygun olacaktır. Yeni bir ' month. Count ' sütunu oluşturacağız.

Kodu düzenlemeye yardımcı olmak için ilk basit işlevimizi `num.month()`. Daha sonra bu işlevi, veri çerçevesinde yeni bir sütun oluşturmak için uygulayacağız. Yeni kod aşağıdaki gibidir.

```R
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

Şimdi güncelleştirilmiş denemeyi çalıştırın ve sonuçları görüntülemek için çıkış günlüğünü kullanın. Bu sonuçlar Şekil 12 ' de gösterilmiştir.

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

*Şekil 12. Ek sütunuyla veri çerçevesinin Özeti.*

Her şey çalışıyor gibi görünüyor. Veri Çerçevemizdeki beklenen değerleri içeren yeni bir sütun var.

### <a name="value-transformations"></a>Değer dönüşümleri

Bu bölümde, veri Çerçevemizin sütunlarındaki bazı sütunlarımızda bazı basit dönüştürmeler gerçekleştireceğiz. R dili neredeyse rastgele değer dönüşümlerini destekler. Aşağıda [daha fazla okumada](#appendixb) başvurular kapsamlı örnekler içerir.

Veri Çerçevemizin özetlerindeki değerlere baktığınızda, burada tek bir şey görmeniz gerekir. California 'da üretilmiş mılden daha fazla dondurma mı? Hayır, bu durum hiçbir şey olmadığı için değildir, bu olgu gibi üzgün bir ABD Birimler farklı. Fiyat ABD Sterlini ' de yer alıyor, Milk, 1 M ABD libre cinsindendir, dondurma 1.000 ABD galon birimlerinde ve cotsat Cheese, 1.000 ABD libre cinsindendir. Her galon 6,5 Sterlini yaklaşık olarak% libre ağırldığında, bu değerleri, hepsi de eşit birimlerde olan 1.000 libre olacak şekilde dönüştürmek için çarpmaları kolayca yapabiliriz.

Tahmin modelimiz için, bu verilerin eğilimi ve dönemsel olarak ayarlanması için bir çarpma modeli kullanıyoruz. Bir günlük dönüştürmesi, bu işlemi basitleştirecek doğrusal bir model kullanmamızı sağlar. Günlük dönüşümünü, çarpanın uygulandığı aynı işleve uygulayabiliriz.

Aşağıdaki kodda, yeni bir işlev tanımladım, `log.transform()`ve sayısal değerleri içeren satırlara uygularsınız. R `Map()` işlevi, `log.transform()` işlevini veri çerçevesinin seçili sütunlarına uygulamak için kullanılır. `Map()`, `apply()` benzerdir, ancak işleve birden fazla bağımsız değişken listesi sağlar. Çarpanların listesinin `log.transform()` işlevine ikinci bağımsız değişkeni sağladığı unutulmamalıdır. `na.omit()` işlevi, dataframe 'de eksik veya tanımsız değerlere sahip olmadığından emin olmak için temizlik biti olarak kullanılır.

```R
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
  ## If there is an exception, print the warningmessage to
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

`log.transform()` işlevinde oldukça bir bit vardır. Bu kodun çoğu, bağımsız değişkenlerle ilgili olası sorunları denetlemektir ya da hesaplamalar sırasında ortaya çıkabilecek özel durumlarla devam edebilir. Bu kodun yalnızca birkaç satırı, hesaplamaları aslında yapılır.

Savunma programlamanın hedefi, işlemin devam etmesini önleyen tek bir işlevin başarısızlığını önlemektir. Uzun süre çalışan bir çözümlemenin ani bir başarısızlığı, kullanıcılar için oldukça sinir bozucu olabilir. Bu durumdan kaçınmak için, varsayılan dönüş değerleri seçilmelidir ve bu da aşağı akış işleme hasarı sınırlayacaktır. Ayrıca, bir şeyin yanlış olduğunu bildiren kullanıcılara uyarı vermek için de bir ileti oluşturulur.

R 'de programlama konusunda savunma yapmak için kullanmıyorsanız, bu kodun hepsi biraz daha fazla görünebilir. Ana adımlarda size yol gösterecek:

1. Dört iletiden oluşan bir vektör tanımlanmıştır. Bu iletiler, bu kodla oluşabilecek olası hatalar ve özel durumlar hakkındaki bilgileri iletmek için kullanılır.
2. Her durum için bir değeri döndürür. Daha az yan etkisi olabilecek birçok farklı olasılık vardır. Örneğin, sıfır veya orijinal giriş vektörünün bir vektörünü döndürebiliyorum.
3. Denetimler, işlevin bağımsız değişkenlerinde çalıştırılır. Her durumda, bir hata algılanırsa, varsayılan bir değer döndürülür ve `warning()` işlevi tarafından bir ileti oluşturulur. İkincisi yürütmeyi sonlandıracak ve tamamen kaçınmaya çalıştığım için `stop()` yerine `warning()` kullanıyorum. Bu kodu yordamsal bir biçimde yazdım, bu durumda karmaşık ve belirsiz bir işlevsel yaklaşım olabilir.
4. Günlük hesaplamaları `tryCatch()` sarmalanır, böylece özel durumlar, işlemin işlenmesine neden olmaz. R işlevleri tarafından oluşturulan çoğu hata `tryCatch()`, yalnızca bunu yapan bir durdurma sinyaliyle sonuçlanır.

Bu R kodunu denemenize yürütün ve çıktı. log dosyasında yazdırılan çıktıya göz atın. Şimdi şekil 13 ' te gösterildiği gibi, günlükteki dört sütunun dönüştürülmüş değerlerini görürsünüz.

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

*Şekil 13. Veri çerçevesindeki dönüştürülmüş değerlerin Özeti.*

Değerlerin dönüştürüldüğünü görüyoruz. MILK üretimi artık diğer tüm süt ürün üretimini büyük ölçüde aşıyor ve artık bir günlük ölçeğine baktık.

Bu noktada verilerimiz temizlenir ve bazı modelleme için hazırız. [Execute R betik][execute-r-script] modülümüzün sonuç veri kümesi çıkışının görselleştirme özetine bakarak, tıpkı istediğiniz gibi, ' month ' sütununun, 12 benzersiz değer Içeren ' kategorik ' olduğunu görürsünüz.

## <a id="timeseries"></a>Zaman serisi nesneleri ve bağıntı Analizi

Bu bölümde, birkaç temel R zaman serisi nesnesini keşfedecektir ve bazı değişkenler arasındaki bağıntıları analiz edeceğiz. Bizim amamız, birkaç lags 'de ikili bağıntı bilgilerini içeren bir veri çerçevesinin çıktısının çıkışını sağlamaktır.

Bu bölümün tam R kodu, [Machinelearningsamples-Not defterleri/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)içinde bulunur.

### <a name="time-series-objects-in-r"></a>R 'deki zaman serisi nesneleri

Daha önce belirtildiği gibi, zaman serisi zamana göre dizinlenen bir veri değerleri dizisidir. R zaman serisi nesneleri, zaman dizinini oluşturmak ve yönetmek için kullanılır. Zaman serisi nesnelerini kullanmanın çeşitli avantajları vardır. Zaman serisi nesneleri, nesne içinde kapsüllenmiş zaman serisi dizin değerlerini yönetme ayrıntılarından çok daha fazla ayrıntı için sizi boşaltın. Ayrıca, zaman serisi nesneleri çizim, yazdırma, modelleme vb. için çok sayıda zaman serisi yöntemi kullanmanıza imkan tanır.

POSIXct zaman serisi sınıfı yaygın olarak kullanılır ve nispeten basittir. Bu zaman serisi sınıfı, dönem başından itibaren 1 Ocak 1970 arasındaki süreyi ölçer. Bu örnekte, POSIXct zaman serisi nesneleri kullanacağız. Yaygın olarak kullanılan diğer R zaman serisi nesne sınıfları, Zoo ve XTS, Genişletilebilir zaman serisini içerir.

### <a name="time-series-object-example"></a>Zaman serisi nesnesi örneği

Bizim örneğimizi kullanmaya başlayalım. **Yeni** bir [R Betiği Çalıştır][execute-r-script] modülünü sürükleyip denemenize bırakın. Mevcut [yürütme r betiği][execute-r-script] modülünün Result DataSet1 çıkış bağlantı noktasını yeni [Execute r betik][execute-r-script] modülünün DataSet1 giriş bağlantı noktasına bağlayın.

İlk örneklerde yaptığımız gibi, örneğin, bazı noktalarda her bir adımda yalnızca artımlı ek R kodu satırı gösterilecektir.  

#### <a name="reading-the-dataframe"></a>Veri çerçevesini okuma

İlk adım olarak, bir veri çerçevesinde okuyup beklenen sonuçları aldığınızdan emin olun. Aşağıdaki kod işi yapmanız gerekir.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Şimdi, denemeyi çalıştırın. Yeni yürütme R betiği şeklinin günlüğü Şekil 14 gibi görünmelidir.

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

*Şekil 14. R betiği yürütme modülündeki veri çerçevesinin Özeti.*

Bu veriler beklenen tür ve biçimlerdir. ' Month ' sütununun faktör tür olduğunu ve beklenen düzey sayısına sahip olduğunu unutmayın.

#### <a name="creating-a-time-series-object"></a>Zaman serisi nesnesi oluşturma

Veri çerçevemiz için bir zaman serisi nesnesi eklememiz gerekiyor. Geçerli kodu, POSIXct sınıfının yeni bir sütununu ekleyen aşağıdaki ile değiştirin.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Şimdi günlüğe bakın. Şekil 15 gibi görünmelidir.

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

*Şekil 15. Bir zaman serisi nesnesi ile veri çerçevesinin Özeti.*

Özet 'den, yeni sütunun POSIXct sınıfının aslında olduğunu görebiliriz.

### <a name="exploring-and-transforming-the-data"></a>Verileri keşfetme ve dönüştürme

Bu veri kümesindeki bazı değişkenleri keşfedelim. Bir dağınık terçiz matrisi hızlı bir görünüm oluşturmak için iyi bir yoldur. Önceki R kodundaki `str()` işlevini aşağıdaki satırla değiştirdim.

```R
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Bu kodu çalıştırın ve neler olduğunu görün. R cihaz bağlantı noktasında üretilen çizim, Şekil 16 gibi görünmelidir.

![Seçili değişkenlerin dağınık terçiz matrisi](./media/r-quickstart/fig17.png)

*Şekil 16. Seçili değişkenlerin dağınık terçiz matrisi.*

Bu değişkenler arasındaki ilişkilerde bazı tek görünümlü yapı mevcuttur. Belki de bu, verilerdeki eğilimler ve değişkenleri standart olmayan olgudan ortaya çıkar.

### <a name="correlation-analysis"></a>Bağıntı Analizi

Bağıntı analizini gerçekleştirmek için, değişkenleri aynı şekilde ve standartlaştırmanız gerekir. Yalnızca değişkenleri ortalar ve ölçeklendirilen R `scale()` işlevini kullanabiliriz. Bu işlev iyi şekilde daha hızlı çalıştırılabilir. Bununla birlikte, R 'de savunma programı 'nın bir örneğini göstermek istiyorum.

Aşağıda gösterilen `ts.detrend()` işlevi bu işlemlerin her ikisini de gerçekleştirir. Aşağıdaki iki kod satırı verileri de eğilim olarak belirleyip değerleri standartlaştırır.

```R
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

`ts.detrend()` işlevinde oldukça bir bit vardır. Bu kodun çoğu, bağımsız değişkenlerle ilgili olası sorunları denetlemektir ya da hesaplamalar sırasında ortaya çıkabilecek özel durumlarla devam edebilir. Bu kodun yalnızca birkaç satırı, hesaplamaları aslında yapılır.

Değer dönüşümlerindeki savunma programlamanın bir örneğini zaten tartıştık. Her iki hesaplama bloğu da `tryCatch()`sarmalanır. Bazı hatalar için, özgün giriş vektörünü döndürmek mantıklı olur ve diğer durumlarda sıfırlardan oluşan bir vektör döndürür.  

Serbest bırakma için kullanılan doğrusal gerileme bir zaman serisi gerilemesi olduğunu unutmayın. Öngörücüsü değişkeni bir zaman serisi nesnesidir.  

`ts.detrend()` tanımlandıktan sonra, veri Çerçevemizdeki ilgilendiğiniz değişkenlere uygularsınız. `as.data.frame()`kullanılarak veri verisi çerçevesine `lapply()` tarafından oluşturulan sonuç listesini zorunlu etmemiz gerekir. `ts.detrend()`savunma konuları nedeniyle, değişkenlerden birini işleyememesi diğerlerinin doğru işlemesini engellemez.  

Kodun son satırı, ikili bir dağınık terçiz oluşturur. R kodunu çalıştırdıktan sonra, dağınık terçizimin sonuçları şekil 17 ' de gösterilir.

![De iç içe ve standart zaman serisinin ikili dağılmış terçizimi](./media/r-quickstart/fig18.png)

*Şekil 17. De iç içe ve standart zaman serisinin ikili dağılmış terçizimi.*

Bu sonuçları Şekil 16 ' da gösterilenler ile karşılaştırabilirsiniz. Eğilim kaldırılır ve değişkenler standartlaştırılmış olduğunda, bu değişkenler arasındaki ilişkilerdeki çok daha az bir yapı görüyoruz.

Eş ilişkilerini R CCF nesneleri olarak hesaplama kodu aşağıdaki gibidir.

```R
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

Bu kodun çalıştırılması Şekil 18 ' de gösterilen günlüğü oluşturur.

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

*Şekil 18. İkili bağıntı analizinden CCF nesnelerinin listesi.*

Her gecikme için bir bağıntı değeri vardır. Bu bağıntı değerlerinin hiçbiri önemli olacak kadar büyük değildir. Bu nedenle, her bir değişkeni bağımsız olarak modelliyoruz.

### <a name="output-a-dataframe"></a>Veri çerçevesinin çıktısını al
İkili bağıntıları R CCF nesnelerinin listesi olarak hesapladık. Bu, sonuç veri kümesi çıkış bağlantı noktası gerçekten bir veri çerçevesi gerektirdiğinden oluşan bir sorunu gösterir. Ayrıca, CCF nesnesi bir listesidir ve yalnızca bu listenin ilk öğesindeki değerleri, çeşitli lags içindeki bağıntıları tercih ediyoruz.

Aşağıdaki kod, kendi listeleri olan CCF nesneleri listesinden öteleme değerlerini ayıklar.

```R
df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
r.names  <- c("Corr Cot Cheese - Ice Cream",
              "Corr Cot Cheese - Milk Prod",
              "Corr Cot Cheese - Fat Price",
              "Corr Ice Cream - Mik Prod",
              "Corr Ice Cream - Fat Price",
              "Corr Milk Prod - Fat Price")

## Build a dataframe with the row names column and the
## correlation data frame and assign the column names
outframe <- cbind(r.names, df.correlations)
colnames(outframe) <- c.names
outframe


## WARNING!
## The following line works only in Azure Machine Learning Studio (classic)
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

İlk kod satırı biraz karmaşık değildir ve bazı açıklamalar bunu anlamanıza yardımcı olabilir. İçinden çalışırken şunlar vardır:

1. '**1**' bağımsız değişkenine sahip ' **[[** ' işleci, CCF nesne listesinin ilk öğesinden gelen lags 'teki bağıntılar vektörünü seçer.
2. `do.call()` işlevi, `rbind()` işlevini, liste `lapply()`tarafından döndürülen öğelerin üzerine uygular.
3. `data.frame()` işlevi, `do.call()` tarafından oluşturulan sonucu bir veri çerçevesine zorlar.

Satır adlarının veri çerçevesinin bir sütununda olduğunu unutmayın. Bunun yapılması, [yürütme R betiğindeki][execute-r-script]çıktılar olduğunda satır adlarını korur.

Kodu çalıştırmak, sonuç veri kümesi bağlantı noktasında çıktıyı **Görselleştirdiğimde** Şekil 19 ' da gösterilen çıktıyı üretir. Satır adları, hedeflenen şekilde ilk sütunlardır.

![Bağıntı analizinden alınan sonuç çıktısı](./media/r-quickstart/fig20.png)

*Şekil 19. Bağıntı analizinden alınan sonuç çıktısı.*

## <a id="seasonalforecasting"></a>Zaman serisi örneği: mevsimsel tahmin

Verilerimiz artık analiz için uygun bir biçimde olduğundan, değişkenler arasında önemli bir ilişki bulunmadığını belirledik. Bir zaman serisi tahmin modeli taşıyıp oluşturalım. Bu modeli kullanarak, California mılmi üretimini 2013 aylık 12 ay boyunca tahmin edeceğiz.

Tahmin modelimizin iki bileşeni, bir eğilim bileşeni ve bir mevsimlik bileşeni olacaktır. Tüm tahmin, bu iki bileşenin ürünüdür. Bu tür bir model, bir çoğultıcı model olarak bilinir. Alternatif bir eklenebilir modeldir. İlgilendiğiniz değişkenlere zaten bir günlük dönüştürmesi uyguladık, bu da bu analiz tractable 'ı oluşturur.

Bu bölümün tam R kodu, [Machinelearningsamples-Not defterleri/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)içinde bulunur.

### <a name="creating-the-dataframe-for-analysis"></a>Analiz için veri çerçevesini oluşturma

Denemeniz için yeni bir [R betiği çalıştırma][execute-r-script] modülü ekleyerek başlayın. Mevcut [Execute R betiği][execute-r-script] modülünün **sonuç veri kümesi** çıkışını yeni modülün **dataSet1** girişine bağlayın. Sonuç şekil 20 ' ye benzer bir şekilde görünmelidir.

![Yeni Execute R betiği modülü eklendi](./media/r-quickstart/fig21.png)

*Şekil 20. Yeni Execute R betiği modülü eklendi.*

Yalnızca tamamladığımız bağıntı analizinde olduğu gibi, POSIXct zaman serisi nesnesine sahip bir sütun eklememiz gerekiyor. Aşağıdaki kod yalnızca bunu kullanacaktır.

```R
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Bu kodu çalıştırın ve günlüğe bakın. Sonuç şekil 21 gibi görünmelidir.

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

*Şekil 21. Veri çerçevesinin Özeti.*

Bu sonuçla Analize başlamaya hazırız.

### <a name="create-a-training-dataset"></a>Eğitim veri kümesi oluşturma

Oluşturulan veri çerçevesi ile bir eğitim veri kümesi oluşturmanız gerekir. Bu veriler, test veri kümizin olan 2013 yılının son 12. hariç tüm gözlemleri içerir. Aşağıdaki kod, veri çerçevesini alt kümeler ve süt üretim ve fiyat değişkenlerinin çizim kümelerini oluşturur. Ardından dört üretim ve fiyat değişkenlerinin çizimlerini oluşturdum. Bir anonim işlev, çizim için bazı genişletmelerin tanımlanması ve ardından `Map()`diğer iki bağımsız değişkenin listesini yinelemek için kullanılır. Bir for döngüsünün burada düzgün çalıştığını düşünüyorsanız, doğru olursunuz. Ancak, R bir işlevsel dil olduğundan işlevsel bir yaklaşım gösterdim.

```R
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

Kodu çalıştırmak, Şekil 22 ' de gösterilen R cihaz çıktısından zaman serisi çiztiğini üretir. Zaman ekseninin, zaman serisi çizim yönteminin iyi bir avantajı olan Tarih birimi cinsinden olduğunu unutmayın.

![California süt üretimi ve fiyat verilerinin zaman serisi çizilerinde ilki](./media/r-quickstart/unnamed-chunk-161.png)

![California süt üretimi ve fiyat verilerinin zaman serisi çizme saniyesi](./media/r-quickstart/unnamed-chunk-162.png)

![California süt üretimi ve fiyat verilerinin üçüncü zaman serisi çizimleri](./media/r-quickstart/unnamed-chunk-163.png)

![California süt üretimi ve fiyat verilerinin dördüncü zaman serisi çizimleri](./media/r-quickstart/unnamed-chunk-164.png)

*Şekil 22. California süt üretimi ve fiyat verilerinin zaman serisi çizimleri.*

### <a name="a-trend-model"></a>Bir eğilim modeli

Bir zaman serisi nesnesi oluşturdunuz ve verilere baktığımızda, California mılm üretim verileri için bir eğilim modeli oluşturmaya başalım. Bunu bir zaman serisi gerileme ile yapabiliriz. Ancak, eğitim verilerinde gözlemlenen eğilimi doğru şekilde modellemek için bir eğim ve kesme noktası olması gereken çizim çizitide bir araya gelir.

Verilerin küçük ölçekli verileri verildiğinde, RStudio 'da eğilim için model oluşturacak ve sonra elde edilen modeli kesip Azure Machine Learning Studio klasik sürümüne yapıştıracağım. RStudio Bu etkileşimli analiz türü için etkileşimli bir ortam sağlar.

İlk denemede 3 ' e kadar üslerle bir polinom gerileme deneyeceğim. Bu tür modelleri aşırı sığdırma konusunda gerçek bir tehlike vardır. Bu nedenle, yüksek sıra terimlerinin önüne geçmek en iyisidir. `I()` işlevi, içeriğin (' olduğu gibi ') içeriğini (') olarak yorumlar ve bir gerileme denkleminde bir tam olarak yorumlanan bir işlev yazmanızı sağlar.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Bu, aşağıdakileri oluşturur.

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

Bu çıktıda P değerlerinden (`Pr(>|t|)`), kare içinde dönemin önemli olabileceğini görebiliriz. Kare içinde dönemi bırakarak bu modeli değiştirmek için `update()` işlevini kullanabilirim.

```R
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Bu, aşağıdakileri oluşturur.

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

Bu daha iyi bir şekilde görünür. Tüm koşullar önemlidir. Ancak, 2E-16 değeri varsayılan bir değerdir ve çok önemli bir şekilde alınmamalıdır.  

Sağlamlık bir test olarak, California süt üretim verilerinin bir zaman serisi çizimi olarak gösterilen eğilim eğrisini oluşturalım. Aşağıdaki kodu, modeli oluşturmak ve bir çizim yapmak için Azure Machine Learning Studio [Execute R betik][execute-r-script] modelinin (rstudio değil) klasik sürümüne ekledik. Sonuç Şekil 23 ' te gösterilir.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Eğilim modeliyle birlikte California mıli üretim verileri gösterildi](./media/r-quickstart/unnamed-chunk-18.png)

*Şekil 23. Eğilim modeliyle birlikte California mıli üretim verileri gösteriliyor.*

Eğilim modeli verileri oldukça iyi bir şekilde sığdırır. Ayrıca, model eğrisindeki tek wggles gibi çok fazla ekleme konusunda kanıt yok gibi görünüyor.  

### <a name="seasonal-model"></a>Mevsimlik modeli

El ile bir eğilim modeliyle, tek yapmanız gereken ve mevsimsiz etkileri içermelidir. Aylık ay efektini yakalamak için yılın ayı doğrusal modelde bir kukla değişken olarak kullanacağız. Bir modele Factor değişkenleri tanıdığınızda, kesme noktası hesaplanmamalıdır. Bunu yapmazsanız, formül aşırı belirtilmiştir ve R istenen faktörlerden birini bırakacak ancak kesme terimini tutacaktır.

Tatmin edici bir eğilim modeliniz olduğundan, yeni terimleri mevcut modele eklemek için `update()` işlevini kullanabiliriz. Güncelleştirme formülündeki-1, kesme terimini bırakır. RStudio 'da Şu anda devam ediliyor:

```R
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Bu, aşağıdakileri oluşturur.

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

Modelin artık bir kesme dönemi olmadığını ve 12 önemli ay faktörü olduğunu görüyoruz. Bu, tam olarak neyi görmek istiyorduk.

Ayrıca, mevsimlik modelinin ne kadar iyi çalıştığını görmek için California süt üretim verilerinin başka bir zaman serisi çizimi oluşturalım. Modeli oluşturmak ve bir çizim yapmak için, Azure Machine Learning Studio [Execute R betiğini][execute-r-script] klasik sürümünde aşağıdaki kodu ekledik.

```R
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

Bu kodun klasik Azure Machine Learning Studio sürümünde çalıştırılması Şekil 24 ' te gösterilen çizimi üretir.

![Mevsim etkileri dahil olmak üzere modelmılk üretimi](./media/r-quickstart/unnamed-chunk-20.png)

*Şekil 24. California mılmi, mevsimsel etkiler dahil olmak üzere modeldir.*

Şekil 24 ' te gösterilen verilere Sığdır bunun yerine teşvik. Hem eğilim hem de mevsimsel efekt (aylık değişim) makul bir şekilde görünür.

Modelinize başka bir denetim olarak göz atalım. Aşağıdaki kod, iki modelimizin tahmin edilen değerlerini hesaplar, mevsimlik modelinin kalan değerlerini hesaplar ve ardından eğitim verileri için bu fazlalıklar çizer.

```R
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

Fazlalık çizimi Şekil 25 ' te gösterilir.

![Eğitim verileri için mevsimlik modelinin fazlalıklar](./media/r-quickstart/unnamed-chunk-21.png)

*Şekil 25. Eğitim verileri için mevsimlik modelinin fazlalıklar.*

Bu fazlalıklar makul bir şekilde görünür. 2008-2009 recesine, ancak modelimizin özellikle de hesaba dahil olmadığı durumlar dışında belirli bir yapı yoktur.

Şekil 25 ' te gösterilen çizim, fazlalıklar içindeki zamana bağlı desenleri algılamak için yararlıdır. Kullandığım fazlalıklar için ve çizdiğim açık yaklaşım, çizim üzerinde zaman sırasına göre yer alır. Diğer taraftan, `milk.lm$residuals`çizdim, çizim zaman sıraya alınmıyor.

`plot.lm()` bir dizi tanılama grafiğini oluşturmak için de kullanabilirsiniz.

```R
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Bu kod Şekil 26 ' da gösterilen bir dizi tanılama grafiğini üretir.

![Dönemsel model için tanılama çizden ilki](./media/r-quickstart/unnamed-chunk-221.png)

![Dönemsel model için tanılama çizikinin saniyesi](./media/r-quickstart/unnamed-chunk-222.png)

![Mevsimlik modeli için tanılama çizbir üçüncü](./media/r-quickstart/unnamed-chunk-223.png)

![Mevsimlik modeli için dördüncü tanılama çizimleri](./media/r-quickstart/unnamed-chunk-224.png)

*Şekil 26. Mevsimlik modeli için tanılama çizimleri.*

Bu çizimler için çok sayıda etkili şekilde tanımlanmış bir işaret vardır, ancak harika bir sorun oluşmasına neden olmaz. Diğer bir deyişle, normal Q-Q çizinden kalan ve doğrusal modeller için önemli bir varsayım olan kalanlar

### <a name="forecasting-and-model-evaluation"></a>Tahmin ve model değerlendirmesi

Örneğimizi tamamlamaya yönelik yalnızca bir şey daha vardır. Tahminleri hesapladık ve hatayı gerçek verilerle ölçmemiz gerekiyor. Tahminimiz 12 ay 2013 için olacaktır. Bu tahmine yönelik bir hata ölçüsünü eğitim veri kümizin kapsamında olmayan gerçek verilere göre hesaplamanız için işlem yapabilirsiniz. Ayrıca, 18 yıllık eğitim verileriyle ilgili performansı, test verilerinin 12 ayı ile karşılaştırabiliriz.  

Zaman serisi modellerinin performansını ölçmek için bir dizi ölçüm kullanılır. Bu durumda kök ortalama kare (RMS) hatasını kullanacağız. Aşağıdaki işlev, RMS hatasını iki seri arasında hesaplar.  

```R
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

"Değer dönüşümleri" bölümünde ele aldığımız `log.transform()` işlevinde olduğu gibi, bu işlevde çok sayıda hata denetimi ve özel durum kurtarma kodu vardır. Kullanılan ilkeler aynıdır. İş, `tryCatch()`Sarmalanan iki yerde yapılır. İlk olarak, değerlerin günlükleriyle çalıştık olduğundan, zaman serisi üs olarak dağıtılır. İkinci olarak, gerçek RMS hatası hesaplanır.  

RMS hatasını ölçmek için bir işlev ile donatılmış, RMS hatalarını içeren bir veri çerçevesi oluşturup çıktısını verlim. Yalnızca eğilim modelinin koşullarını ve mevsimsel faktörlerle tüm modeli kapsayacaktır. Aşağıdaki kod, oluşturduğumuz iki doğrusal modeli kullanarak işi yapar.

```R
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

Bu kodu çalıştırmak, sonuç veri kümesi çıkış bağlantı noktasında Şekil 27 ' de gösterilen çıktıyı üretir.

![Modeller için RMS hatalarının karşılaştırılması](./media/r-quickstart/fig26.png)

*Şekil 27. Modeller için RMS hatalarının karşılaştırması.*

Bu sonuçlardan, modele mevsimsel faktörleri eklemenin, RMS hatasını önemli ölçüde azalttığını görüyoruz. Çok fazla değil, eğitim verileri için RMS hatası tahmine göre biraz daha küçüktür.

## <a id="appendixa"></a>RStudio belgelerine yönelik kılavuz

RStudio oldukça iyi belgelenmiştir. İşte, başlamanızı sağlamak için RStudio belgelerinin önemli bölümlerine bazı bağlantılar verilmiştir.

* **Projeler oluşturma** -R kodunuzu, rstudio kullanarak projeler halinde düzenleyebilir ve yönetebilirsiniz. Ayrıntılar için bkz. [projeleri kullanma](https://support.rstudio.com/hc/articles/200526207-Using-Projects) . Bu yönergeleri izlemenizi ve bu makaledeki R kodu örnekleri için bir proje oluşturmanızı tavsiye ederim.  
* **R Code-rstudio ' un düzenlenebilmesi ve yürütülmesi** r kodunu düzenleyip yürütmek için tümleşik bir ortam sağlar. Ayrıntılar için bkz. [kod düzenleniyor ve yürütülüyor](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code) .
* **Hata ayıklama** -rstudio güçlü hata ayıklama özellikleri içerir. Bu özellikler hakkında daha fazla bilgi için bkz. [RStudio Ile hata ayıklama](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio) . Kesme noktası sorun giderme özellikleri hakkında bilgi için bkz. [kesme noktası sorun giderme](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting).

## <a id="appendixb"></a>Daha fazla okuma

Bu R programlama öğreticisi, Azure Machine Learning Studio klasik sürümü ile R dilini kullanmak için gerekenler hakkında temel bilgileri içerir. R hakkında bilginiz yoksa, CRAN üzerinde iki tanıtım mevcuttur:

* [Yeni başlayanlar Için R](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) Ile Emmanuel paradne, başlamak için iyi bir yerdir.  
* W ile [R 'ye giriş](https://cran.r-project.org/doc/manuals/R-intro.html) . N. Venables et. Eşkenar. Biraz daha derinliğe gider.

R üzerinde başlamanıza yardımcı olabilecek birçok kitap vardır. İşte yararlı buldum.

* **R programlamanın sanatı:** Norman Matloff tarafından sunulan Istatistiksel yazılım tasarımının turu, R 'de programlamaya harika bir giriş niteliğindedir.  
* Paul Teetor tarafından yapılan **r tanımlama kitabı** , r kullanarak bir sorun ve çözüm yaklaşımı sağlar.  
* Robert Kabacoff tarafından **yapılacak işlem** başka bir kullanışlı tanıtım defteridir. Yardımcı [hızlı R Web sitesi](https://www.statmethods.net/) yararlı bir kaynaktır.
* **Kınns No** , can, r 'de programlarken karşılaşılabilecek çok sayıda karmaşık ve zor konuyla ilgilenen, büyük bir insanlık defteridir. Kitap, [R Inno](https://www.burns-stat.com/documents/books/the-r-inferno/)'da ücretsiz olarak kullanılabilir.
* R **'deki gelişmiş konulara** ayrıntılı bir bakış istiyorsanız, bu kitapta Hadley Wickhemi 'a kadar bir görünüm bulabilirsiniz. Bu kitabın çevrimiçi sürümü [http://adv-r.had.co.nz/](http://adv-r.had.co.nz/)adresinden ücretsiz olarak kullanılabilir.

R zaman serisi paketlerinin kataloğu, [Cran görev görünümü: zaman serisi analizi](https://cran.r-project.org/web/views/TimeSeries.html)içinde bulunabilir. Belirli zaman serisi nesne paketleri hakkında daha fazla bilgi için bu paketin belgelerine başvurmalısınız.

R ile Paul Cowpertwaıt ve Andrew Metcalfe ile kitap **giriş zaman serisi** , zaman serisi analizi için r kullanımına giriş sağlar. Daha çok sayıda teorik metin, R örnekleri sağlar.

Harika internet kaynakları aşağıda verilmiştir:

* Veri kampı, video dersleri ve kodlama alıştırmaları ile tarayıcınızın rahatlığını R. En son R teknikleri ve paketleri hakkında etkileşimli öğreticiler vardır. Ücretsiz [etkileşimli R öğreticisini](https://www.datacamp.com/courses/introduction-to-r)alın.
* Program aracılığıyla [kesin kılavuz olan R programlama hakkında bilgi edinin](https://www.programiz.com/r-programming) .
* Clarkson University 'ten Kelly siyaha kadar hızlı bir [R öğreticisi](https://www.cyclismo.org/tutorial/R/) .
* [Veri becerilerinizi geliştirmek Için en üstteki R dil kaynaklarında](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html)listelenen 60 R kaynağı vardır.

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
