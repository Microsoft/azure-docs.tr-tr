---
title: Premium Encoder ile birden çok giriş dosyası ve bileşen özelliği-Azure | Microsoft Docs
description: Bu konu, birden çok giriş dosyası kullanmak ve Media Encoder Premium Workflow medya işlemcisine özel verileri geçirmek için setRuntimeProperties 'in nasıl kullanılacağını açıklamaktadır.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: 7fb35bdd-9891-4401-a65b-ef3cc8190e8a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: xpouyat
ms.reviewer: anilmur;juliako
ms.openlocfilehash: 27bdf82d4515678e28eadf07fe325860fe5df063
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "69015445"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Premium Encoder ile birden çok giriş dosyası ve bileşen özellikleri kullanma
## <a name="overview"></a>Genel Bakış
**Media Encoder Premium Workflow** medya işlemcisi ile bir görev gönderdiğinizde, bileşen özelliklerini özelleştirmeniz, KLIP listesi XML içeriğini belirtmeniz veya birden çok giriş dosyası göndermeniz gerekebilecek senaryolar vardır. Bazı örnekler şunlardır:

* Videoda metin yerleştirme ve her giriş videosunun çalışma zamanında metin değeri (örneğin, geçerli tarih) ayarlama.
* Klip Listesi XML 'sini özelleştirme (kırpma, vb. ile veya olmayan bir veya birkaç kaynak dosyayı belirtmek için).
* Video kodlanırken giriş videosunda bir logo görüntüsünü yerleştirme.
* Birden çok ses dili kodlaması.

**Media Encoder Premium Workflow** , görevi oluştururken veya birden çok giriş dosyası gönderdiğinizde iş akışındaki bazı özellikleri değiştirdiklerinizi bilmesini sağlamak Için, **setRuntimeProperties** ve/veya **içeren bir yapılandırma dizesi kullanmanız gerekir transcodeSource**. Bu konuda, bunların nasıl kullanılacağı açıklanmaktadır.

## <a name="configuration-string-syntax"></a>Yapılandırma dizesi sözdizimi
Kodlama görevinde ayarlanacak yapılandırma dizesi şuna benzer bir XML belgesi kullanır:

```xml
<?xml version="1.0" encoding="utf-8"?>
<transcodeRequest>
  <transcodeSource>
  </transcodeSource>
  <setRuntimeProperties>
    <property propertyPath="Media File Input/filename" value="VideoFileName.mp4" />
  </setRuntimeProperties>
</transcodeRequest>
```

Aşağıdaki C# kod, BIR dosyadan xml yapılandırmasını okuyan, doğru video dosya adı ile güncelleştiren ve bunu bir işteki göreve geçirir:

```csharp
string premiumConfiguration = ReadAllText(@"D:\home\site\wwwroot\Presets\SetRuntime.xml").Replace("VideoFileName", myVideoFileName);

// Declare a new job.
IJob job = _context.Jobs.Create("Premium Workflow encoding job");

// Get a media processor reference, and pass to it the name of the 
// processor to use for the specific task.
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

// Create a task with the encoding details, using a string preset.
ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                              processor,
                              premiumConfiguration,
                              TaskOptions.None);

// Specify the input assets
task.InputAssets.Add(workflow); // workflow asset
task.InputAssets.Add(video); // video asset with multiple files

// Add an output asset to contain the results of the job. 
// This output is specified as AssetCreationOptions.None, which 
// means the output asset is not encrypted. 
task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);
```

## <a name="customizing-component-properties"></a>Bileşen özelliklerini özelleştirme
### <a name="property-with-a-simple-value"></a>Basit değerli Özellik
Bazı durumlarda, bileşen özelliğini Media Encoder Premium Workflow tarafından yürütülecek iş akışı dosyası ile birlikte özelleştirmek yararlı olur.

Videolarınızdaki metni kaplayan bir iş akışı tasarlamış olduğunuzu ve metnin (örneğin, geçerli tarihin) çalışma zamanında ayarlanması gerektiğini varsayalım. Bunu, kodlama görevinden, yer paylaşımı bileşeninin Text özelliği için yeni değer olarak ayarlanacak metni göndererek yapabilirsiniz. Bu mekanizmayı iş akışındaki bir bileşenin diğer özelliklerini (örneğin, kaplamanın konumu veya rengi, AVC Kodlayıcısı için bit hızı, vb.) değiştirmek için kullanabilirsiniz.

**setRuntimeProperties** , iş akışının bileşenlerinde bir özelliği geçersiz kılmak için kullanılır.

Örnek:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
      <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
  </setRuntimeProperties>
</transcodeRequest>
```

### <a name="property-with-an-xml-value"></a>XML değeri olan özellik
XML değeri bekleyen bir özelliği ayarlamak için, kullanarak `<![CDATA[ and ]]>`kapsülleyebilirsiniz.

Örnek:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

> [!NOTE]
> Hemen sonra `<![CDATA[`bir satır başı yerleştirdiğinizden emin olun.

### <a name="propertypath-value"></a>propertyPath değeri
Önceki örneklerde, propertyPath "/Media File Input/filename" veya "/ınactivetimeout" veya "clipListXml" idi.
Bu, genel olarak, bileşenin adı, sonra özelliğin adıdır. Yol, "/primarySourceFile" (özellik iş akışının kökünde olduğundan) veya "/video Işleme/grafik kaplama/opaklık" (yani bir grupta yer aldığı için) gibi daha fazla veya daha az düzeye sahip olabilir.    

Yolu ve özellik adını denetlemek için her bir özelliğin hemen yanındaki eylem düğmesini kullanın. Bu eylem düğmesine tıklayıp **Düzenle**seçeneğini belirleyebilirsiniz. Bu, özelliğin gerçek adını ve hemen üzerindeki ad alanını gösterir.

![Eylem/düzenleme](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Özellik](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Birden çok giriş dosyası
**Media Encoder Premium Workflow** gönderdiğiniz her görev iki varlık gerektirir:

* Birincisi, bir iş akışı dosyası içeren bir *Iş akışı varlığıdır* . [İş akışı Tasarımcısı](media-services-workflow-designer.md)kullanarak iş akışı dosyalarını tasarlayabilirsiniz.
* İkinci bir, kodlamak istediğiniz medya dosyalarını içeren bir *medya* varlıktır.

Birden çok medya dosyasını **Media Encoder Premium Workflow** kodlayıcıya gönderdiğinizde aşağıdaki kısıtlamalar geçerlidir:

* Tüm medya dosyaları aynı *medya varlığı*içinde olmalıdır. Birden çok medya varlığı kullanılması desteklenmez.
* Bu medya kıymetinin birincil dosyasını ayarlamanız gerekir (ideal olarak, bu, kodlayıcının işlemesi istenen ana video dosyasıdır).
* **SetRuntimeProperties** ve/veya **transcodesource** öğesini içeren yapılandırma verilerini işlemciye geçirmek gereklidir.
  * **setRuntimeProperties** , FileName özelliğini veya iş akışının bileşenlerinde başka bir özelliği geçersiz kılmak için kullanılır.
  * **Transcodesource** , KLIP listesi XML içeriğini belirtmek için kullanılır.

İş akışındaki bağlantılar:

* Bir veya birkaç medya dosyası giriş bileşeni kullanırsanız ve dosya adını belirtmek için **setRuntimeProperties** kullanmayı planlıyorsanız, birincil dosya bileşeni PIN 'ini bunlara bağlanmayın. Birincil dosya nesnesiyle medya dosyası girişleri arasında bağlantı olmadığından emin olun.
* Clip List XML ve tek bir medya kaynağı bileşeni kullanmayı tercih ediyorsanız, her ikisini de birbirine bağlayabilirsiniz.

![Birincil kaynak dosyasından medya dosyası girişine bağlantı yok](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Dosya adı özelliğini ayarlamak için setRuntimeProperties kullanırsanız, birincil dosyadan medya dosya girişi bileşenlerine bağlantı yoktur.*

![Clip List XML 'den klip listesi kaynağına bağlantı](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Clip List XML 'i medya kaynağına bağlayıp, transcodeSource kullanabilirsiniz.*

### <a name="clip-list-xml-customization"></a>Klip Listesi XML özelleştirmesi
Çalışma zamanında iş akışındaki Clip List XML 'i, yapılandırma dizesi XML dosyasında **Transcodesource** kullanarak belirtebilirsiniz. Bu, klip listesi XML PIN 'inin iş akışındaki medya kaynağı bileşenine bağlanmasını gerektirir.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <transcodeSource>
      <clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
      </clipList>
    </transcodeSource>
    <setRuntimeProperties>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

' Ifadeler ' kullanarak çıkış dosyalarını adlandırmak üzere bu özelliği kullanmak için/primarySourceFile belirtmek istiyorsanız, klip listesinin geçersiz kılınmasından kaçınmak için, küçük resim listesi XML 'sini/primarySourceFile özelliğinden *sonra* bir özellik olarak geçirmeyi öneririz. /primarySourceFile ayarı.

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Ek çerçeveye doğru kırpma ile:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>Örnek 1: Videonun üstünde bir görüntünün kaplamasını

### <a name="presentation"></a>Sunum
Video kodlanırken, giriş videosunda bir logo resminin kaplamasını istediğiniz bir örnek düşünün. Bu örnekte, giriş videosu "Microsoft_HoloLens_Possibilities_816p24. mp4" olarak adlandırılmıştır ve logo "logo. png" olarak adlandırılmıştır. Aşağıdaki adımları gerçekleştirmeniz gerekir:

* İş akışı dosyası ile bir Iş akışı varlığı oluşturun (aşağıdaki örneğe bakın).
* İki dosya içeren bir medya varlığı oluşturun: Birincil dosya olarak Myınputvideo. mp4 ve MyLogo. png.
* Yukarıdaki giriş varlıklarıyla Media Encoder Premium Workflow medya işlemcisine bir görev gönderin ve aşağıdaki yapılandırma dizesini belirtin.

Yapılandırma:

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Yukarıdaki örnekte, video dosyasının adı medya dosyası giriş bileşenine ve primarySourceFile özelliğine gönderilir. Logo dosyasının adı, grafik kaplama bileşenine bağlı başka bir medya dosyası girişine gönderilir.

> [!NOTE]
> Video dosyası adı primarySourceFile özelliğine gönderilir. Bunun nedeni, örnek olarak Ifadeleri kullanarak doğru çıkış dosyası adını oluşturmak için iş akışında bu özelliği kullanmaktır.

### <a name="step-by-step-workflow-creation"></a>Adım adım iş akışı oluşturma
Aşağıda, giriş: video ve görüntü gibi iki dosya alan bir iş akışı oluşturma adımları verilmiştir. Videonun üzerinde görüntünün üst kısmında yer alır.

**İş akışı Tasarımcısı** açın ve **Dosya** > **Yeni çalışma alanı** > **dönüştürme kodu şeması**' nı seçin.

Yeni iş akışı üç öğe gösterir:

* Birincil kaynak dosyası
* Klip Listesi XML 'i
* Çıkış dosyası/varlık  

![Yeni kodlama Iş akışı](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Yeni kodlama Iş akışı*

Giriş medya dosyasını kabul etmek için, medya dosyası giriş bileşeni ekleme ile başlayın. İş akışına bir bileşen eklemek için depo arama kutusunda bunu bulun ve istenen girişi tasarımcı bölmesine sürükleyin.

Sonra, iş akışınızı tasarlamak için kullanılacak video dosyasını ekleyin. Bunu yapmak için, İş Akışı Tasarımcısı arka plan bölmesine tıklayın ve sağ Özellik bölmesinde birincil kaynak dosyası özelliğini arayın. Klasör simgesine tıklayın ve uygun video dosyasını seçin.

![Birincil dosya kaynağı](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Birincil dosya kaynağı*

Ardından, medya dosyası giriş bileşeninde video dosyasını belirtin.   

![Medya dosyası giriş kaynağı](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Medya dosyası giriş kaynağı*

Bu işlem tamamlandıktan hemen sonra medya dosyası giriş bileşeni, dosyayı inceler ve çıktı PIN 'ini inceledi dosyayı yansıtacak şekilde doldurur.

Sonraki adım, REC. 709 için renk alanını belirtmek üzere "video veri türü güncelleştiricisi" eklemektir. Veri düzeni/Düzen türü = yapılandırılabilir planar olarak ayarlanan bir "video biçimi dönüştürücüsü" ekleyin. Bu işlem, video akışını, kaplama bileşeninin bir kaynağı olarak alınabilecek bir biçime dönüştürür.

![video veri türü Güncelleştirici ve biçim dönüştürücüsü](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Video veri türü Güncelleştirici ve biçim dönüştürücüsü*

![Düzen türü = yapılandırılabilir Planar](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Düzen türü yapılandırılabilir Planar*

Daha sonra, bir video kaplama bileşeni ekleyin ve medya dosyası girişinin (sıkıştırılmamış) video PIN 'ini (sıkıştırılmamış) video PIN 'ini bağlayın.

Başka bir medya dosyası girişi ekleyin (logo dosyasını yüklemek için), bu bileşene tıklayın ve "medya dosyası giriş logosu" olarak yeniden adlandırın ve dosya özelliğinde bir görüntü (örneğin bir. png dosyası) seçin. Sıkıştırılmamış görüntü PIN 'ini, kaplamanın sıkıştırılmamış görüntü PIN 'ini bağlayın.

![Yer paylaşımı bileşeni ve resim dosya kaynağı](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Yer paylaşımı bileşeni ve resim dosya kaynağı*

Logo 'nin video üzerinde konumunu değiştirmek istiyorsanız (örneğin, videonun sol üst köşesinden yüzde 10 ' luk bir konuma konumlandırmak isteyebilirsiniz), "El Ile giriş" onay kutusunu temizleyin. Bunun için, bir medya dosyası girişi kullanarak, bir logo dosyasını bir kaplama bileşenine sağlamanız gerekir.

![Yer paylaşımı konumu](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Yer paylaşımı konumu*

Video akışını H. bir ile kodlamak için, AVC video Kodlayıcısı ve AAC Kodlayıcısı bileşenlerini tasarımcı yüzeyine ekleyin. PIN 'leri bağlayın.
AAC Kodlayıcısı 'nı ayarlayın ve ses biçimi dönüşümü/önceden ayarla ' yı seçin: 2,0 (L, R).

![Ses ve video kodlayıcıları](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Ses ve video kodlayıcıları*

Şimdi **ISO MPEG-4 Çoğullayıcı** ve **dosya çıkış** bileşenlerini ekleyin ve PIN 'leri gösterildiği gibi bağlayın.

![MP4 Çoğullayıcı ve dosya çıktısı](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 Çoğullayıcı ve dosya çıktısı*

Çıktı dosyasının adını ayarlamanız gerekir. **Dosya çıktısı** bileşenine tıklayın ve dosyanın ifadesini düzenleyin:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Dosya çıkış adı](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Dosya çıkış adı*

Doğru çalışıp çalışmadığını denetlemek için iş akışını yerel olarak çalıştırabilirsiniz.

Tamamlandıktan sonra, Azure Media Services ' de çalıştırabilirsiniz.

İlk olarak, Azure Media Services içinde iki dosya ile bir varlık hazırlayın: video dosyası ve logosu. Bunu .NET veya REST API kullanarak yapabilirsiniz. Bunu, Azure portal veya [Azure Media Services Gezgini](https://github.com/Azure/Azure-Media-Services-Explorer) 'ni (amo) kullanarak da yapabilirsiniz.

Bu öğreticide, amo ile varlıkları yönetme konusu gösterilmektedir. Bir varlığa dosya eklemenin iki yolu vardır:

* Yerel bir klasör oluşturun, içindeki iki dosyayı kopyalayın ve ardından dosyayı **varlık** sekmesine sürükleyin ve bırakın.
* Video dosyasını varlık olarak karşıya yükleyin, varlık bilgilerini görüntüleyin, dosyalar sekmesine gidin ve ek dosyayı (logo) karşıya yükleyin.

> [!NOTE]
> Varlık içinde bir birincil dosya (ana video dosyası) ayarladığınızdan emin olun.

![AMO 'daki varlık dosyaları](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*AMO 'daki varlık dosyaları*

Varlığı seçin ve Premium kodlayıcıyla kodlamayı seçin. İş akışını karşıya yükleyin ve seçin.

İşlemciye veri geçirmek için düğmeye tıklayın ve çalışma zamanı özelliklerini ayarlamak için aşağıdaki XML 'i ekleyin:

![AMO 'da Premium kodlayıcı](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*AMO 'da Premium kodlayıcı*

Ardından, aşağıdaki XML verilerini yapıştırın. Hem medya dosyası girişi hem de primarySourceFile için video dosyasının adını belirtmeniz gerekir. Logo için dosya adının adını belirtin.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*

Görevi oluşturmak ve çalıştırmak için .NET SDK kullanıyorsanız, bu XML verilerinin yapılandırma dizesi olarak geçirilmesi gerekir.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

İş tamamlandıktan sonra, çıkış varlığı içindeki MP4 dosyası kaplamayı görüntüler!

![Videoda kaplama](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Videoda kaplama*

Örnek iş akışını [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/)'dan indirebilirsiniz.

## <a name="example-2--multiple-audio-language-encoding"></a>Örnek 2: Birden çok ses dili kodlaması

[GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding)'da birden çok ses dili Encoding iş akışı örneği mevcuttur.

Bu klasör, bir MXF dosyasını birden çok ses parçası olan çok MP4 dosyaları varlığına kodlamak için kullanılabilen bir örnek iş akışı içerir.

Bu iş akışı, MXF dosyasının bir ses izi içerdiğini varsayar; Ek ses parçaları ayrı ses dosyaları (WAV veya MP4...) olarak geçirilmelidir.

Kodlamak için lütfen şu adımları izleyin:

* MXF dosyası ve ses dosyalarıyla (0 ila 18 ses dosyası) Media Services bir varlık oluşturun.
* MXF dosyasının birincil dosya olarak ayarlandığından emin olun.
* Premium Iş akışı Kodlayıcısı işlemcisini kullanarak bir iş ve görev oluşturun. Belirtilen iş akışını kullanın (MultiMP4-1080p-19audio-v1. Workflow).
* Setruntime. xml verilerini göreve geçirin (Azure Media Services Gezgini kullanıyorsanız, "XML verilerini iş akışına geçir" düğmesine basın.
  * Lütfen doğru dosya adları ve diller etiketlerini belirtmek için XML verilerini güncelleştirin.
  * İş akışında, Audio 1 adlı ses bileşenleri 18 ' e sahiptir.
  * RFC5646, Language etiketi için desteklenir.

```xml
<?xml version="1.0" encoding="utf-16"?>
<transcodeRequest>
  <setRuntimeProperties>
    <property propertyPath="Media File Input Video/filename" value="MainVideo.mxf" />
    <property propertyPath="Language/language_code" value="en" />
    <property propertyPath="/primarySourceFile" value="MainVideo.mxf" />
    <property propertyPath="Audio 1/Media File Input/filename" value="french-audio.wav" />
    <property propertyPath="Audio 1/Language/language_code" value="fr" />
    <property propertyPath="Audio 2/Media File Input/filename" value="german-audio.wav" />
    <property propertyPath="Audio 2/Language/language_code" value="de" />
    <property propertyPath="Audio 3/Media File Input/filename" value="japanese-audio.wav" />
    <property propertyPath="Audio 3/Language/language_code" value="ja" />
  </setRuntimeProperties>
</transcodeRequest>
```

* Kodlanmış varlık çok dilli ses parçaları içerir ve bu parçalar Azure Media Player seçilebilmelidir.

## <a name="see-also"></a>Ayrıca bkz.
* [Azure Media Services Premium kodlamaya giriş](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Azure Media Services içinde Premium kodlama kullanma](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Azure Media Services ile isteğe bağlı içeriği kodlama](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Media Encoder Premium Workflow biçimleri ve kodlayıcıları](media-services-premium-workflow-encoder-formats.md)
* [Örnek iş akışı dosyaları](https://github.com/Azure/azure-media-services-samples)
* [Azure Media Services Gezgini aracı](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
