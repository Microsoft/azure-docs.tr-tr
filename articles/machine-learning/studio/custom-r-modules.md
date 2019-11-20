---
title: Özel R modülleri tanımlama
titleSuffix: ML Studio (classic) - Azure
description: Bu konu başlığı altında, özel bir R Studio (klasik) yazmak ve dağıtmak açıklanmaktadır. Özel R modüllerinin ne olduğunu ve bunları tanımlamak için hangi dosyaların kullanılacağını açıklar.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 6c81cd927ac26779cab042d1d4e54f2e8c02918c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838872"
---
# <a name="define-custom-r-modules-for-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio için özel R modülleri tanımlama (klasik)

Bu konu başlığı altında, özel bir R Studio (klasik) yazmak ve dağıtmak açıklanmaktadır. Özel R modüllerinin ne olduğunu ve bunları tanımlamak için hangi dosyaların kullanılacağını açıklar. Modül tanımlayan dosyaların nasıl oluşturulduğunu ve bir Machine Learning çalışma alanında dağıtım için modülün nasıl kaydettirildiğini gösterir. Özel modülün tanımında kullanılan öğeler ve öznitelikler daha ayrıntılı olarak açıklanmıştır. Yardımcı işlevsellik ve dosyaları kullanma ve birden çok çıkış de ele alınmıştır. 



## <a name="what-is-a-custom-r-module"></a>Özel bir R modülü nedir?
**Özel modül** , çalışma alanınıza yüklenebilen ve Azure Machine Learning Studio denemesinin klasik sürümünün bir parçası olarak yürütülebilecek Kullanıcı tanımlı bir modüldür. **Özel bir r modülü** , Kullanıcı tanımlı R işlevini yürüten özel bir modüldür. **R** , algoritmaları uygulamak için İstatistikçilerin ve veri bilimcileri tarafından yaygın olarak kullanılan istatistiksel bilgi işlem ve grafik için bir programlama dilidir. Şu anda, R Özel modüllerde desteklenen tek dildir, ancak daha sonraki sürümlere yönelik destek, gelecek sürümler için zamanlanır.

Özel modüller, diğer tüm modüllerle aynı şekilde kullanılabileceğini anlamak için Azure Machine Learning Studio klasik sürümünde **birinci sınıf durumuna** sahiptir. Bunlar yayımlanmış denemeleri veya görselleştirmelere dahil diğer modüllerle yürütülürler. Modül tarafından uygulanan algoritma, kullanılacak giriş ve çıkış bağlantı noktaları, modelleme parametreleri ve diğer çeşitli çalışma zamanı davranışları üzerinde denetiminiz vardır. Özel modüller içeren bir deneme de kolay paylaşım için Azure Yapay Zeka Galerisi yayımlanabilir.

## <a name="files-in-a-custom-r-module"></a>Özel bir R modülündeki dosyalar
Özel bir R modülü, en az iki dosyada içeren bir. zip dosyası tarafından tanımlanır:

* Modülün açığa çıkarılan R işlevini uygulayan bir **kaynak dosyası**
* Özel modül arabirimini açıklayan bir **XML tanım dosyası**

Ek yardımcı dosyalar, özel modülden erişilebilen işlevselliği sağlayan. zip dosyasına da dahil edilebilir. Bu seçenek, hızlı başlangıç örneğini takip eden **XML tanım dosyasındaki** başvuru bölümü öğelerinin **bağımsız değişkenler** bölümünde ele alınmıştır.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Hızlı başlangıç örneği: özel bir R modülünü tanımlama, paketleme ve kaydetme
Bu örnek, özel bir R modülü tarafından istenen dosyaların nasıl oluşturulduğunu, bunları bir ZIP dosyasına paketlemeyi ve sonra modülü Machine Learning çalışma alanınıza kaydetmeyi gösterir. Örnek zip paketi ve örnek dosyalar [Download CustomAddRows. zip dosyasından](https://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409)indirilebilir.

## <a name="the-source-file"></a>Kaynak dosya
İki veri kümesinden (veri çerçevelerinden) satırları (gözlemleme) birleştirmek için kullanılan **satır ekle** modülünün standart uygulamasını değiştiren **özel bir satır ekle** modülünün örneğini düşünün. Standart **satır ekleme** modülü, ikinci giriş veri kümesinin satırlarını `rbind` algoritmasını kullanarak ilk giriş veri kümesinin sonuna ekler. Benzer şekilde, özelleştirilmiş `CustomAddRows` işlevi iki veri kümesini kabul eder, ancak ek bir giriş olarak Boole takas parametresini de kabul eder. Swap parametresi **false**olarak ayarlandıysa, standart uygulamayla aynı veri kümesini döndürür. Ancak Swap parametresi **true**ise, işlevi ilk giriş veri kümesinin satırlarını ikinci veri kümesinin sonuna ekler. **Özel satır ekle** modülü tarafından kullanıma sunulan r `CustomAddRows` işlevinin uygulamasını içeren CustomAddRows. r dosyası aşağıdaki R koduna sahiptir.

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) 
    {
        if (swap)
        {
            return (rbind(dataset2, dataset1));
        }
        else
        {
            return (rbind(dataset1, dataset2));
        } 
    } 

### <a name="the-xml-definition-file"></a>XML tanım dosyası
Bu `CustomAddRows` işlevini bir Azure Machine Learning Studio modülünün klasik sürümü olarak göstermek için, **özel satır ekleme** modülünün nasıl görünmesi ve davranması gerektiğini belirtmek üzere bir XML tanım dosyası oluşturulmalıdır. 

    <!-- Defined a module using an R Script -->
    <Module name="Custom Add Rows">
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another. Dataset 2 is concatenated to Dataset 1 when Swap is FALSE, and vice versa when Swap is TRUE.</Description>

    <!-- Specify the base language, script file and R function to use for this module. -->        
        <Language name="R" 
         sourceFile="CustomAddRows.R" 
         entryPoint="CustomAddRows" />  

    <!-- Define module input and output ports -->
    <!-- Note: The values of the id attributes in the Input and Arg elements must match the parameter names in the R Function CustomAddRows defined in CustomAddRows.R. -->
        <Ports>
            <Input id="dataset1" name="Dataset 1" type="DataTable">
                <Description>First input dataset</Description>
            </Input>
            <Input id="dataset2" name="Dataset 2" type="DataTable">
                <Description>Second input dataset</Description>
            </Input>
            <Output id="dataset" name="Dataset" type="DataTable">
                <Description>The combined dataset</Description>
            </Output>
        </Ports>

    <!-- Define module parameters -->
        <Arguments>
            <Arg id="swap" name="Swap" type="bool" >
                <Description>Swap input datasets.</Description>
            </Arg>
        </Arguments>
    </Module>


XML dosyasındaki **giriş** ve **bağımsız değişken** öğelerinin **kimlik** özniteliklerinin değerinin, CustomAddRows. r DOSYASıNDAKI R kodunun işlev parametre adlarıyla tam olarak eşleşmesi gerektiğini unutmayın: (*dataSet1*, *DataSet2*, ve örnekte *takas* edin). Benzer şekilde, **Language** öğesinin **entryPoint** özniteliğinin değeri, R betiğindeki işlevin adıyla aynı olmalıdır: (örnekteki*CustomAddRows* ). 

Buna karşılık, **output** öğesi için **ID** özniteliği R betiğindeki herhangi bir değişkene karşılık gelmiyor. Birden fazla çıktı gerektiğinde, **çıktılar** öğeleri xml dosyasında bildirildiği *sırada* , sonuçları ile R işlevinden bir liste döndürür.

### <a name="package-and-register-the-module"></a>Modülü paketleme ve kaydetme
Bu iki dosyayı *CustomAddRows. R* ve *CustomAddRows. xml* olarak kaydedin ve iki dosyayı bir *CustomAddRows. zip* dosyasında birlikte zip halinde saklayın.

Bunları Machine Learning çalışma alanınıza kaydetmek için, klasik Machine Learning Studio çalışma alanınıza gidin, alt kısımdaki **+ Yeni** düğmesine tıklayın ve **ZIP paketinden Modül->** ' i seçerek yeni **özel satırları** karşıya yükleyin birimi.

![Zip karşıya yükleme](./media/custom-r-modules/upload-from-zip-package.png)

**Özel satır ekle** modülü artık Machine Learning denemeleri tarafından erişilemeye hazırdır.

## <a name="elements-in-the-xml-definition-file"></a>XML tanım dosyasındaki öğeler
### <a name="module-elements"></a>Modül öğeleri
**Modül** Öğesı, XML dosyasında özel bir modül tanımlamak için kullanılır. Birden çok modül, birden çok **Modül** öğesi kullanılarak tek bir XML dosyasında tanımlanabilir. Çalışma alanınızdaki her modülün benzersiz bir adı olmalıdır. Mevcut bir özel modülle aynı ada sahip özel bir modül kaydedin ve var olan modülün yenisiyle yerini alır. Ancak, özel modüller mevcut Azure Machine Learning Studio (klasik) modülle aynı ada sahip olabilir. Bu durumda, modül paleti **özel** kategorisinde görünürler.

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


**Module** öğesi içinde, iki isteğe bağlı iki öğe belirtebilirsiniz:

* modüle gömülü bir **sahip** öğesi  
* Modül için hızlı yardım 'da ve Machine Learning kullanıcı arabirimindeki modülün üzerine geldiğinizde görüntülenen metni içeren bir **Açıklama** öğesi.

Modül öğelerinde karakter sınırlarına yönelik kurallar:

* **Module** öğesindeki **name** özniteliğinin değeri 64 karakter uzunluğunda olmalıdır. 
* **Description** öğesinin içeriği 128 karakter uzunluğunda olmalıdır.
* **Owner** öğesinin içeriği 32 karakter uzunluğunda olmalıdır.

Modülün sonuçları belirleyici veya belirleyici olmayan bir değer olabilir. * * varsayılan olarak, tüm modüller belirleyici olarak kabul edilir. Diğer bir deyişle, bir giriş parametrelerinin ve verilerin değişmeyen bir kümesi verildiğinde, modül aynı sonuçları Eacs_sayı_üret veya çalıştırıldığı bir işlev süresi döndürmelidir. Bu davranış verildiğinde, Azure Machine Learning Studio klasik sürümü yalnızca bir parametre veya giriş verileri değiştiyse belirleyici olarak işaretlenen modülleri yeniden çalıştırır. Önbelleğe alınmış sonuçları döndürmek, denemeleri 'in çok daha hızlı yürütülmesini sağlar.

S_SAYI_ÜRET veya geçerli tarih ya da saati döndüren bir işlev gibi belirleyici olmayan işlevler vardır. Modülünüzün belirleyici olmayan bir işlev kullanması durumunda, isteğe bağlı **ısbelirleyici** özniteliğini **false**olarak ayarlayarak modülün belirleyici olmadığını belirtebilirsiniz. Bu, modül girişi ve parametreleri değişmemiş olsa bile, deneme her çalıştırıldığında modülün yeniden çalıştırıldığını yöntem. 

### <a name="language-definition"></a>Dil tanımı
XML tanım dosyanızdaki **Language** öğesi özel modül dilini belirtmek için kullanılır. Şu an için yalnızca R dili desteklenmektedir. **SourceFile** özniteliğinin değeri, modül çalıştırıldığında çağrılacak Işlevi içeren R dosyasının adı olmalıdır. Bu dosya ZIP paketinin parçası olmalıdır. **EntryPoint** özniteliğinin değeri, Çağrılmakta olan işlevin adıdır ve kaynak dosyada ile tanımlanmış geçerli bir işlevle eşleşmelidir.

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>Bağlantı Noktaları
Özel bir modülün giriş ve çıkış bağlantı noktaları, XML tanım dosyasının **bağlantı noktaları** bölümünün alt öğelerinde belirtilmiştir. Bu öğelerin sırası, kullanıcılar tarafından karşılaşılan (UX) düzeni belirler. XML dosyasının **Ports** öğesinde listelenen ilk alt **girdi** veya **Çıkış** , Machine Learning UX içinde en soldaki giriş bağlantı noktası olur.
Her giriş ve çıkış bağlantı noktası, fare imlecini Machine Learning kullanıcı arabirimindeki bağlantı noktası üzerine getirdiğinizde gösterilen metni belirten isteğe bağlı bir **Description** alt öğesine sahip olabilir.

**Bağlantı noktası kuralları**:

* En fazla **giriş ve çıkış bağlantı noktası** sayısı her biri için 8 ' dir.

### <a name="input-elements"></a>Giriş öğeleri
Giriş bağlantı noktaları, R işlevinizdeki ve çalışma alanınıza veri geçirmenize olanak sağlar. Giriş bağlantı noktaları için desteklenen **veri türleri** şunlardır: 

**DataTable:** Bu tür, R işlevinizde bir Data. Frame olarak geçirilir. Aslında, Machine Learning tarafından desteklenen ve **DataTable** ile uyumlu olan herhangi bir tür (ÖRNEĞIN, CSV dosyaları veya arff dosyaları) bir veri. çerçeveye otomatik olarak dönüştürülür. 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
           </Input>

Her bir **DataTable** giriş bağlantı noktasıyla ilişkili **ID** özniteliği benzersiz bir değere sahip olmalı ve bu değer R işlevinizdeki karşılık gelen adlandırılmış parametreyle eşleşmelidir.
Bir denemenize giriş olarak geçirilmedi isteğe bağlı **DataTable** bağlantı noktaları, R işlevine **null** değer geçirilmiş ve giriş bağlı değilse, isteğe bağlı ZIP bağlantı noktalarında yok sayılır. **IsOptional** özniteliği hem **DataTable** hem de **ZIP** türleri için isteğe bağlıdır ve varsayılan olarak *false 'tur* .

**Posta kodu:** Özel modüller bir ZIP dosyasını girdi olarak kabul edebilir. Bu giriş, işlevinizin R çalışma dizinine paketten bulunur

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
           </Input>

Özel R modülleri için, bir ZIP bağlantı noktasının KIMLIĞI, R işlevinin herhangi bir parametresiyle eşleşmek zorunda değildir. Bunun nedeni, ZIP dosyasının R çalışma dizinine otomatik olarak ayıklanabileceği bir çalışmadır.

**Giriş kuralları:**

* **Giriş** öğesinin **ID** özniteliğinin değeri geçerli bir R değişkeni adı olmalıdır.
* **Giriş** öğesinin **ıd** özniteliğinin değeri 64 karakterden daha uzun olmamalıdır.
* **Input** öğesinin **name** özniteliğinin değeri 64 karakterden daha uzun olmamalıdır.
* **Description** öğesinin içeriği 128 karakterden daha uzun olmamalıdır
* **Giriş** öğesinin **Type** özniteliğinin değeri *zip* veya *DataTable*olmalıdır.
* **Input** öğesinin **IsOptional** özniteliğinin değeri gerekli değildir (ve belirtilmediğinde varsayılan olarak *false* 'dur); Ancak belirtilmişse, *true* veya *false*olmalıdır.

### <a name="output-elements"></a>Çıkış öğeleri
**Standart çıkış bağlantı noktaları:** Çıkış bağlantı noktaları, daha sonra sonraki modüller tarafından kullanılabilecek olan R işlevinizdeki dönüş değerleriyle eşleştirilir. *DataTable* Şu anda desteklenen tek standart çıkış bağlantı noktası türüdür. ( *Öğrenenler* ve *dönüşümler* için destek sağlanır.) *DataTable* çıkışı şöyle tanımlanır:

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

Özel R modüllerindeki çıktılar için, **ID** özniteliğinin değeri R betiğindeki herhangi bir şeyle eşleşmelidir, ancak benzersiz olmalıdır. Tek modüllü bir çıktıda, R işlevinin dönüş değeri bir *Data. Frame*olmalıdır. Desteklenen bir veri türünde birden fazla nesnenin çıktısını almak için, uygun çıkış bağlantı noktalarının XML tanım dosyasında belirtilmesi gerekir ve nesneler liste olarak döndürülmelidir. Çıkış nesneleri soldan sağa çıkış bağlantı noktalarına atanır ve nesnelerin döndürülen listeye yerleştirildiği sırayı yansıtır.

Örneğin, özgün iki veri kümesini ( *dataSet1* ve *DataSet2*) yeni birleştirilmiş veri kümesine, *veri kümesine*(sırayla, soldan sağa, *veri*kümesi, gibi) çıkarmak için **özel satır ekle** modülünü değiştirmek istiyorsanız, *dataSet1*, *DataSet2*), ardından CustomAddRows. xml dosyasında çıkış bağlantı noktalarını aşağıdaki gibi tanımlayın:

    <Ports> 
        <Output id="dataset" name="Dataset Out" type="DataTable"> 
            <Description>New Dataset</Description> 
        </Output> 
        <Output id="dataset1_out" name="Dataset 1 Out" type="DataTable"> 
            <Description>First Dataset</Description> 
        </Output> 
        <Output id="dataset2_out" name="Dataset 2 Out" type="DataTable"> 
            <Description>Second Dataset</Description> 
        </Output> 
        <Input id="dataset1" name="Dataset 1" type="DataTable"> 
            <Description>First Input Table</Description>
        </Input> 
        <Input id="dataset2" name="Dataset 2" type="DataTable"> 
            <Description>Second Input Table</Description> 
        </Input> 
    </Ports> 


Ve bir listedeki nesne listesini ' CustomAddRows. R ' içinde doğru sırada döndürün:

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
        if (swap) { dataset <- rbind(dataset2, dataset1)) } 
        else { dataset <- rbind(dataset1, dataset2)) 
        } 
    return (list(dataset, dataset1, dataset2)) 
    } 

**Görselleştirme çıkışı:** Ayrıca, R grafik cihazından ve konsol çıktısından çıktıyı görüntüleyen *görselleştirme*türünde bir çıkış bağlantı noktası da belirtebilirsiniz. Bu bağlantı noktası R işlevi çıkışının bir parçası değildir ve diğer çıkış bağlantı noktası türlerinin sırasını engellemez. Özel modüllere bir görselleştirme bağlantı noktası eklemek için, **tür** özniteliği için *görselleştirme* değeri olan bir **çıktı** öğesi ekleyin:

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>

**Çıkış kuralları:**

* **Output** öğesinin **ID** özniteliğinin değeri geçerli bir R değişkeni adı olmalıdır.
* **Output** öğesinin **ıd** özniteliğinin değeri 32 karakterden daha uzun olmamalıdır.
* **Output** öğesinin **name** özniteliğinin değeri 64 karakterden daha uzun olmamalıdır.
* **Output** öğesinin **Type** özniteliğinin değeri *görselleştirme*olmalıdır.

### <a name="arguments"></a>Bağımsız Değişkenler
Ek veriler, **arguments** öğesinde tanımlanan modül parametreleri aracılığıyla R işlevine geçirilebilir. Bu parametreler, modül seçildiğinde Machine Learning kullanıcı arabiriminin en sağdaki Özellikler bölmesinde görünür. Bağımsız değişkenler desteklenen türlerden herhangi biri olabilir veya gerektiğinde özel bir numaralandırma oluşturabilirsiniz. **Bağlantı noktaları** öğelerine benzer şekilde, **bağımsız değişkenler** öğeleri, fareyi parametre adının üzerine getirdiğinizde görüntülenen metni belirten isteğe bağlı bir **Description** öğesine sahip olabilir.
Bir modül için defaultValue, minValue ve maxValue gibi isteğe bağlı özellikler bir **Özellikler** öğesine öznitelik olarak herhangi bir bağımsız değişkene eklenebilir. **Properties** öğesi için geçerli özellikler bağımsız değişken türüne bağlıdır ve sonraki bölümde desteklenen bağımsız değişken türleriyle açıklanır. **IsOptional** özelliği **"true"** olarak ayarlanan bağımsız değişkenler kullanıcının bir değer girmesini gerektirmez. Bağımsız değişkene bir değer sağlanmazsa, bağımsız değişken giriş noktası işlevine geçirilmez. İsteğe bağlı olarak, giriş noktası işlev tanımında NULL değeri atanmış olan giriş noktası işlevinin bağımsız değişkenleri. İsteğe bağlı bir bağımsız değişken, Kullanıcı tarafından bir değer sağlanmışsa, Örneğin Min veya Max gibi diğer bağımsız değişken kısıtlamalarını uygular.
Giriş ve çıkışlarla birlikte, her parametrenin kendileriyle ilişkili benzersiz KIMLIK değerleri olması önemlidir. Hızlı başlangıç örneğimizde ilişkili kimlik/parametre *takas*edildi.

### <a name="arg-element"></a>Arg öğesi
Modül parametresi, XML tanım dosyasının **arguments** bölümünün **arg** alt öğesi kullanılarak tanımlanır. **Bağlantı noktaları** bölümündeki alt öğelerde olduğu gibi, **bağımsız değişkenler** bölümünde parametrelerin sıralaması, UX içinde karşılaşılan düzeni tanımlar. Parametreler, Kullanıcı arabirimindeki yukarıdan aşağı doğru, XML dosyasında tanımlandıkları sırada görünür. Parametreler için Machine Learning tarafından desteklenen türler burada listelenmiştir. 

**int** – bir ınteger (32-bit) tür parametresi.

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* *Isteğe bağlı özellikler*: **Min**, **Max**, **Default** ve **IsOptional**

**Double** : çift tür parametresi.

    <Arg id="doubleValue1" name="Double Param" type="double">
        <Properties min="0.000" max="0.999" default="0.3" />
        <Description>Double Parameter</Description>
    </Arg>


* *Isteğe bağlı özellikler*: **Min**, **Max**, **Default** ve **IsOptional**

**bool** : UX içindeki bir onay kutusuyla temsil edilen bir Boole parametresi.

    <Arg id="boolValue1" name="Boolean Param" type="bool">
        <Properties default="true" />
        <Description>Boolean Parameter</Description>
    </Arg>



* *Isteğe bağlı özellikler*: **varsayılan** -ayarlanmamışsa false

**dize**: standart bir dize

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>    

* *Isteğe bağlı özellikler*: **varsayılan** ve **IsOptional**

**Columnpicker**: bir sütun seçim parametresi. Bu tür UX 'de bir sütun Seçicisi olarak işlenir. Burada, hedef bağlantı noktası türü *DataTable*olması gereken sütunların seçildiği bağlantı noktasının kimliğini belirtmek Için bu **özellik** öğesi kullanılır. Sütun seçiminin sonucu R işlevine, seçili sütun adlarını içeren dizelerin bir listesi olarak geçirilir. 

        <Arg id="colset" name="Column set" type="ColumnPicker">      
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *Gerekli özellikler*: **PortID** - *DATATABLE*türünde bir giriş öğesinin kimliğiyle eşleşir.
* *Isteğe bağlı özellikler*:
  
  * **allowedtypes** -seçebileceğiniz sütun türlerini filtreler. Geçerli değerler şunlardır: 
    
    * Rakamlardan
    * Boole
    * Kategorik
    * Dize
    * Etiket
    * Özellik
    * Puan
    * Tümü
  * **varsayılan** -sütun seçici için geçerli varsayılan seçimler şunları içerir: 
    
    * None
    * NumericFeature
    * NumericLabel
    * NumericScore
    * NumericAll
    * BooleanFeature
    * BooleanLabel
    * BooleanScore
    * BooleanAll
    * CategoricalFeature
    * CategoricalLabel
    * CategoricalScore
    * CategoricalAll
    * StringFeature
    * StringLabel
    * StringScore
    * StringAll
    * AllLabel
    * AllFeature
    * AllScore
    * Tümü

**Açılan menü**: Kullanıcı tarafından belirtilen numaralandırılan (açılan) liste. Açılan öğeler, bir **öğe** öğesi kullanılarak **Özellikler** öğesi içinde belirtilir. Her **öğenin** **kimliği** benzersiz olmalı ve geçerli bir R değişkeni olmalıdır. Bir **öğenin** **adının** değeri hem gördüğünüz metin hem de R işlevine geçirilen değer olarak işlev görür.

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>    

* *Isteğe bağlı özellikler*:
  * **varsayılan** -varsayılan özelliğin değeri, **öğe** öğelerinden birindeki bir kimlik değeri ile eşleşmelidir.

### <a name="auxiliary-files"></a>Yardımcı dosyalar
Özel modül ZIP dosyanıza yerleştirilmiş herhangi bir dosya, yürütme süresi boyunca kullanılabilir olacaktır. Mevcut dizin yapıları korunur. Bu, dosya kaynağını yerel olarak ve Azure Machine Learning Studio yürütmenin klasik sürümünde aynı şekilde çalıştığı anlamına gelir. 

> [!NOTE]
> Tüm yolların ' src/' öneki olması için tüm dosyaların ' src ' dizinine ayıklandığına dikkat edin.
> 
> 

Örneğin, veri kümesinden NAs içeren herhangi bir satırı kaldırmak ve ayrıca, CustomAddRows ' a yazmadan önce yinelenen satırları kaldırmak ve bir RemoveDupNARows. R dosyasında bunu yapan bir R işlevi zaten yazmış olmanız gerektiğini varsayalım:

    RemoveDupNARows <- function(dataFrame) {
        #Remove Duplicate Rows:
        dataFrame <- unique(dataFrame)
        #Remove Rows with NAs:
        finalDataFrame <- dataFrame[complete.cases(dataFrame),]
        return(finalDataFrame)
    }
CustomAddRows işlevindeki RemoveDupNARows. R yardımcı dosyasını kaynak bölümünde bulabilirsiniz:

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) {
        source("src/RemoveDupNARows.R")
            if (swap) { 
                dataset <- rbind(dataset2, dataset1))
             } else { 
                  dataset <- rbind(dataset1, dataset2)) 
             } 
        dataset <- removeDupNARows(dataset)
        return (dataset)
    }

Ardından, özel bir R modülü olarak ' CustomAddRows. R ', ' CustomAddRows. xml ' ve ' RemoveDupNARows. R ' içeren bir ZIP dosyası yükleyin.

## <a name="execution-environment"></a>Yürütme ortamı
R betiğinin yürütme ortamı r **betiği yürüt komut dosyası** modülüyle aynı r sürümünü kullanır ve aynı varsayılan paketleri kullanabilir. Özel modüle özel modül ZIP paketine dahil etmek için ek R paketleri de ekleyebilirsiniz. Bunları kendi R ortamınızda yaptığınız gibi R betiğe de yüklemeniz yeterlidir. 

**Yürütme ortamının sınırlamaları** şunlardır:

* Kalıcı olmayan dosya sistemi: özel modül çalıştırıldığında yazılan dosyalar aynı modülün birden fazla çalıştırmasında kalıcı değildir.
* Ağ erişimi yok

