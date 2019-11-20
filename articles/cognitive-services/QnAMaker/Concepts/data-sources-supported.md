---
title: Desteklenen veri kaynakları-Soru-Cevap Oluşturma
titleSuffix: Azure Cognitive Services
description: Soru-Cevap Oluşturma Web sayfaları, PDF dosyaları veya MS Word belge dosyaları ya da yapılandırılmış QnA içerik dosyaları olarak depolanan soru-cevap çiftlerini otomatik olarak ayıklar.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.openlocfilehash: a1fcc1bec5db3ad64dc045cf5e1a449fce808132
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721211"
---
# <a name="data-sources-for-qna-maker-content"></a>Soru-Cevap Oluşturma içerik için veri kaynakları

Soru-Cevap Oluşturma, SSS, ürün el kitabı, yönergeler, destek belgeleri ve Web sayfaları, PDF dosyaları ya da MS Word Doc dosyaları olarak depolanan ilkeler gibi yarı yapılandırılmış içeriklerden soru-cevap çiftlerini otomatik olarak ayıklar. İçerik, yapılandırılmış QnA içerik dosyalarından Bilgi Bankası 'na da eklenebilir. 

<a name="data-types"></a>

## <a name="file-and-url-data-types"></a>Dosya ve URL veri türleri

Aşağıdaki tabloda, Soru-Cevap Oluşturma tarafından desteklenen içerik ve dosya biçimlerinin türleri özetlenmektedir.

|Kaynak Türü|İçerik türü| Örnekler|
|--|--|--|
|URL'si|SSS<br> (Düz, bölümler ile veya konu başlığı ile)<br>Destek sayfaları <br> (Tek sayfalı nasıl yapılır makaleleri, sorun giderme makaleleri vb.)|[Düz SSS](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Bağlantılarla Ilgili SSS](https://www.microsoft.com/en-us/software-download/faq),<br> [Konularda SSS giriş sayfası](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Destek makalesi](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/BELGE|Sık<br> Ürün el kitabı,<br> Broşürler<br> T<br> El ilanı Ilkesi,<br> Destek Kılavuzu,<br> Yapılandırılmış QnA,<br> benzerlerini.|[Yapılandırılmış QnA. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Örnek ürün el ile. PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Örnek semi-structured. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Örnek Teknik İnceleme. PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br>[Örnek multi-turn. docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)|
|\* Excel|Yapılandırılmış QnA dosyası<br> (RTF, HTML desteği dahil)|[Örnek QnA SSS. xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|\* TXT/TSV|Yapılandırılmış QnA dosyası|[Örnek Chit-Chat. tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

### <a name="import-and-export-knowledge-base"></a>Bilgi bankasını içeri ve dışarı aktarma

Aktarılan bilgi tabanlarından **TSV ve xls dosyaları**yalnızca soru-cevap oluşturma portalındaki **Ayarlar** sayfasından dosyaları içeri aktarılarak kullanılabilir. Bunlar Bilgi Bankası oluşturma sırasında veya **Ayarlar** sayfasındaki **+ Dosya Ekle** veya **+ URL Ekle** özelliğinden veri kaynağı olarak kullanılamaz. 

## <a name="data-source-locations"></a>Veri kaynağı konumları

Veri kaynağı konumları, kimlik doğrulaması gerektirmeyen **Genel URL 'ler veya dosyalardır**. 

Veri kaynağınız için kimlik doğrulaması gerekiyorsa, bu verileri Soru-Cevap Oluşturma almak için aşağıdaki yöntemleri göz önünde bulundurun:

* [Dosyayı el Ile indirin](#download-file-from-authenticated-data-source-location) ve soru-cevap oluşturma alın
* Kimliği doğrulanmış [SharePoint konumu](#import-file-from-authenticated-sharepoint) için içeri aktarma dosyası 

### <a name="download-file-from-authenticated-data-source-location"></a>Kimliği doğrulanmış veri kaynağı konumundan dosya indir

Kimliği doğrulanmış bir dosyaya (kimliği doğrulanmış bir SharePoint konumunda değil) veya URL 'niz varsa, dosyayı kimliği doğrulanmış siteden yerel bilgisayarınıza indirmek için alternatif bir seçenek, dosyayı yerel bilgisayarınızdan Bilgi Bankası 'na eklemektir.

### <a name="import-file-from-authenticated-sharepoint"></a>Kimliği doğrulanmış SharePoint 'ten dosya al 

[SharePoint veri kaynağı konumlarına](../How-to/add-sharepoint-datasources.md) kimliği doğrulanmış **dosyalar**sağlama izni verilir. SharePoint kaynakları, Web sayfaları değil, dosyalar olmalıdır. URL, gibi bir Web uzantısıyla sonlanıyorsa **. ASPX**, SharePoint 'ten soru-cevap oluşturma içine aktarılmaz.


## <a name="faq-urls"></a>SSS URL 'Leri

Soru-Cevap Oluşturma, 3 farklı formda SSS Web sayfalarını destekleyebilir: düz SSS sayfaları, bağlantılarla ilgili SSS sayfaları, konular giriş sayfası ile SSS sayfaları.

### <a name="plain-faq-pages"></a>Düz SSS sayfaları

Bu en yaygın SSS sayfası türüdür ve burada, yanıtlar aynı sayfada soruları hemen izler. 

Aşağıda, düz bir SSS sayfasına örnek verilmiştir:

![Bilgi Bankası için düz SSS sayfası örneği](../media/qnamaker-concepts-datasources/plain-faq.png) 

 
### <a name="faq-pages-with-links"></a>Bağlantılarla ilgili SSS sayfaları 

Bu tür bir SSS sayfasında, sorular birlikte toplanır ve aynı sayfanın farklı bölümlerinde ya da farklı sayfalarda bulunan yanıtlara bağlanır.

Aşağıda, aynı sayfada bulunan bölümlerde bulunan bağlantılarla ilgili bir SSS sayfası örneği verilmiştir:

 ![Bilgi Bankası için bölüm bağlantısı SSS sayfası örneği](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


### <a name="faq-pages-with-a-topics-homepage"></a>Konular giriş sayfası ile SSS sayfaları

Bu tür bir SSS, her konunun ilgili QnAs 'ın farklı bir sayfada bağlantısı olduğu konuları içeren bir giriş sayfasına sahiptir. Burada, Soru-Cevap Oluşturma ilgili soruları ayıklamak için tüm bağlantılı sayfalara gezinir &.

Aşağıda, konu başlığı sayfasının farklı sayfalardaki SSS bölümlerine bağlantı bulunduğu bir SSS sayfasına örnek verilmiştir. 

 ![Ayrıntılı bağlantı SSS sayfası bir Bilgi Bankası için örnek](../media/qnamaker-concepts-datasources/topics-faq.png) 


### <a name="support-urls"></a>Destek URL 'Leri

Soru-Cevap Oluşturma, belirli bir görevin nasıl gerçekleştirileceğini, belirli bir sorunun nasıl tanılanacağı ve çözümleneceğini ve belirli bir işlem için en iyi uygulamaların hangileri olduğunu betimleyen web makaleleri gibi yarı yapılandırılmış destek Web sayfalarını işleyebilir. Ayıklama, hiyerarşik başlıkları olan açık bir yapıya sahip içerikte en iyi şekilde kullanılır.

> [!NOTE]
> Destek makaleleri için ayıklama yeni bir özelliktir ve erken aşamalarındayken. İyi yapılandırılmış olan ve karmaşık üstbilgiler/altbilgiler içermeyen basit sayfalar için en iyi şekilde kullanılır.

![Soru-Cevap Oluşturma, açık bir yapının hiyerarşik başlıklarla sunulduğu yarı yapılandırılmış Web sayfalarından ayıklanmasını destekler](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>PDF/DOC dosyaları

Soru-Cevap Oluşturma, bir PDF veya DOC dosyasındaki yarı yapılandırılmış içeriği işleyebilir ve QnAs 'a dönüştürebilir. Daha iyi ayıklanabilen bir dosya, içeriğin bazı yapılandırılmış biçimde düzenlendiği ve iyi tanımlanmış bölümlerde temsil edildiği bir dosyadır. Bölümler daha fazla alt bölümleri veya alt konuları bozulabilir. Ayıklama, hiyerarşik başlıkları olan açık bir yapıya sahip belgelerde en iyi şekilde kullanılır.

Soru-Cevap Oluşturma yazı tipi boyutu, yazı tipi stili, numaralandırma, renkler gibi görsel ipuçlarına göre dosyadaki bölümleri ve alt bölümleri ve ilişkileri tanımlar. Yarı yapılandırılmış PDF veya DOC dosyaları el ile, SSS, yönergeler, Ilkeler, broşürler, el Ilanları ve diğer birçok dosya türü olabilir. Aşağıda bu dosyaların bazı örnek türleri verilmiştir.

### <a name="product-manuals"></a>Ürün kılavuzları

Bir el ile, genellikle bir ürüne eşlik eden rehberlik malzemeleridir. Kullanıcının ürünü ayarlama, kullanma, bakımını yapma ve sorun giderme konusunda yardımcı olur. Soru-Cevap Oluşturma bir el ile işlediğinde, başlıkları ve alt başlıkları sorular olarak ve sonraki içeriği yanıt olarak ayıklar. [Buraya](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)bir örnek bakın.

Aşağıda Dizin sayfası ve hiyerarşik içerik içeren bir el ile örnek verilmiştir

 ![Bilgi Bankası için ürün el Ile örnek](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> Ayıklama, içindekiler tablosu ve/veya dizin sayfası ve hiyerarşik başlıkları olan açık bir yapı içeren el ile en iyi şekilde kullanılır.

### <a name="brochures-guidelines-papers-and-other-files"></a>Broşürler, yönergeler, incelemeler ve diğer dosyalar

Diğer birçok belge türü, bir açık yapıya ve düzene sahip olmaları şartıyla QA çiftleri oluşturmak için de işlenebilir. Bunlar arasında şunlar yer alır: Broşürler, yönergeler, raporlar, teknik incelemeler, bilimsel incelemeler, ilkeler, kitaplar vb. [Buraya](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)bir örnek bakın.

Bir dizin olmadan yarı yapılandırılmış bir belge örneği aşağıda verilmiştir:

 ![Azure Blob depolama yarı yapılandırılmış belge](../media/qnamaker-concepts-datasources/semi-structured-doc.png) 

### <a name="structured-qna-document"></a>Yapılandırılmış QnA belgesi

Belge dosyalarında yapılandırılmış soru-cevap biçimi, her satırda değişen sorular ve yanıtlar biçiminde, satır başına bir soru ve aşağıdaki satırdaki yanıtı aşağıda gösterildiği gibi bir şekilde oluşturulur: 

```text
Question1

Answer1

Question2

Answer2
```

Aşağıda bir yapılandırılmış QnA Word belgesi örneği verilmiştir:

 ![Bilgi Bankası için yapılandırılmış QnA belgesi örneği](../media/qnamaker-concepts-datasources/structured-qna-doc.png) 

## <a name="structured-txt-tsv-and-xls-files"></a>Yapılandırılmış *txt*, *TSV* ve *xls* dosyaları

Bir Bilgi Bankası oluşturmak veya genişletmek için yapılandırılmış *. txt*, *. tsv* veya *. xls* dosyaları biçimindeki qnas de soru-cevap oluşturma yüklenebilir.  Bunlar düz metin olabilir veya RTF ya da HTML 'de içeriğe sahip olabilir. 

| Soru  | Yanıt  | Meta veriler (1 anahtar: 1 değer) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Kaynak dosyadaki tüm ek sütunlar yok sayılır.

### <a name="example-of-structured-excel-file"></a>Yapılandırılmış Excel dosyası örneği

Aşağıda, bir yapılandırılmış QnA *. xls* dosyası olan HTML içeriğiyle ilgili bir örnek verilmiştir:

 ![Bilgi Bankası için yapılandırılmış QnA Excel örneği](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Excel dosyasında tek yanıt için alternatif soruların örneği

Aşağıda, tek bir yanıt için çeşitli alternatif sorulara sahip bir yapılandırılmış QnA *. xls* dosyası örneği verilmiştir:

 ![Excel dosyasında tek yanıt için alternatif soruların örneği](../media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Dosya alındıktan sonra, soru-cevap çifti aşağıda gösterildiği gibi Bilgi Bankası 'nda bulunur:

 ![Bilgi Bankası 'na aktarılan tek yanıt için alternatif soruların ekran görüntüsü](../media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>İçeri aktarma yoluyla yapılandırılmış veri biçimi

Bilgi bankasını içeri aktarmak, mevcut bilgi bankasındaki içeriğin yerini alır. İçeri aktarma, veri kaynağı bilgilerini içeren bir yapılandırılmış. tsv dosyası gerektirir. Bu bilgiler, soru-cevap çiftlerini ve onları belirli bir veri kaynağına göre gruplamak Soru-Cevap Oluşturma yardımcı olur.

| Soru  | Yanıt  | Kaynak| Meta veriler (1 anahtar: 1 değer) |          
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Düzenleme|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Bilgi tabanına genel olarak ekle

Bilgi Bankası 'nı doldurmak için önceden mevcut içeriğiniz yoksa, Soru-Cevap Oluşturma Bilgi Bankası 'nda QnAs edıtoru ekleyebilirsiniz. Bilgi bankaınızı [buradan](../How-To/edit-knowledge-base.md)güncelleştirmeyi öğrenin.

<a href="#formatting-considerations"></a>

## <a name="formatting-considerations"></a>Biçimlendirme konuları

Bir dosya veya URL 'YI içeri aktardıktan sonra Soru-Cevap Oluşturma, içeriğinizi [markı biçiminde](https://en.wikipedia.org/wiki/Markdown)dönüştürür ve depolar. Dönüştürme işlemi, metne `\n\n`gibi yeni satırlar ekler. Markaşağı biçimi hakkında bilgi, dönüştürülen içeriği anlamanıza ve Bilgi Bankası içeriğinizi yönetmenize yardımcı olur. 

İçeriğinizi doğrudan bilgi tabanınız içinde ekler veya düzenlersiniz, zengin metin içeriği oluşturmak veya zaten yanıtdaki markaşağı biçim içeriğini değiştirmek için **markı biçimlendirme** kullanın. Soru-Cevap Oluşturma, içeriğinize zengin metin özellikleri getirmek için markaşağı biçiminin çoğunu destekler. Ancak, bir sohbet bot gibi istemci uygulaması aynı markı biçimleri kümesini desteklemiyor olabilir. İstemci uygulamanın yanıt görüntüsünü test etmek önemlidir. 

Aşağıda Soru-Cevap Oluşturma kullanabileceğiniz markın biçimlerinin listesi verilmiştir: 

|Amaç|Biçimlendir|Örnek markaşağı|İşleme<br>Sohbet bot 'ta gösterildiği gibi|
|--|--|--|--|
2 cümle arasında yeni bir satır.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![iki cümle arasındaki yeni çizgiyi Biçimlendir](../media/qnamaker-concepts-datasources/format-newline.png)|
|H1 ile H6 arasındaki üstbilgiler, `#` sayısı hangi üstbilgiyi gösterir. 1 `#` H1 'dir.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![markaşağı başlıkları ile biçimlendirme](../media/qnamaker-concepts-datasources/format-headers.png)<br>![markaşağı üstbilgileri ile Biçimlendir H1 ile H5](../media/qnamaker-concepts-datasources/format-h1-h5.png)|
|İtalik |`*text*`|`How do I create a bot with *QnA Maker*?`|![italik ile biçimlendirme](../media/qnamaker-concepts-datasources/format-italics.png)|
|Dize (koyu)|`**text**`|`How do I create a bot with **QnA Maker**?`|![kalın için güçlü işaret ile biçimlendirme](../media/qnamaker-concepts-datasources/format-strong.png)|
|Bağlantı URL 'SI|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![URL için biçim (köprü)](../media/qnamaker-concepts-datasources/format-url.png)|
|\* Ortak görüntü URL 'SI|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![ortak görüntü URL 'SI için biçim ](../media/qnamaker-concepts-datasources/format-image-url.png)|
|Üstünü|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![üstü çizili biçim](../media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Kalın ve italik|`***text***`|`How can I create a ***QnA Maker*** bot?`|![kalın ve italik biçim](../media/qnamaker-concepts-datasources/format-bold-italics.png)|
|Bağlantı için kalın URL|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![kalın URL için biçim](../media/qnamaker-concepts-datasources/format-bold-url.png)|
|Bağlantı için italik URL|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![italik URL için biçim](../media/qnamaker-concepts-datasources/format-url-italics.png)|
|Kaçış markın sembolleri|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![italik URL için biçim](../media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Sıralı liste|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>Yukarıdaki örnek, markaşağı içinde yerleşik olarak bulunan otomatik numaralandırmayı kullanır.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>Yukarıdaki örnek, açık numaralandırma kullanır.|![sıralı liste için biçim](../media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Sırasız liste|`\n * item1 \n * item2`<br>or<br>`\n - item1 \n - item2`|`This is an ordered list: \n * List item 1 \n * List item 2`|![Sırasız liste için biçim](../media/qnamaker-concepts-datasources/format-unordered-list.png)|
|İç içe listeler|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>Sıralı ve sıralanmamış listeleri birlikte iç içe yerleştirebilirsiniz. `\t`sekme, alt öğenin girintileme düzeyini gösterir.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![iç içe sıralanmamış liste için biçim](../media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![iç içe geçmiş liste için biçim](../media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

\* Soru-Cevap Oluşturma görüntüyü hiçbir şekilde işlemez. Bu, görüntüyü işlemek için istemci uygulamanın rolüdür. 

Güncelleştirme/değiştirme Bilgi Bankası API 'Lerini kullanarak içerik eklemek istiyorsanız ve içerik/dosya HTML etiketleri içeriyorsa, etiketlerin açılış ve kapanışının kodlanmış biçimde dönüştürülmesini sağlamak için dosyanızdaki HTML 'yi koruyabilirsiniz.

| HTML 'yi koru  | API isteğindeki temsili  | KB cinsinden temsil |
|-----------|---------|-------------------------|
| Evet | \&lt; br\&gt; | &lt;br&gt; |
| Evet | \&lt; H3\&gt; üst bilgi\&lt;/H3\&gt; | &lt;H3&gt;üst bilgisi&lt;/H3&gt; |

Ayrıca, CR LF (\r\n), BB 'ye dönüştürülür. LF (\n) olduğu gibi tutulur. Bir kaçış sırasını \t veya \n gibi atlamak istiyorsanız ters eğik çizgi kullanabilirsiniz, örneğin: '\\\\r\\\\n ' ve '\\\\t '

## <a name="editing-your-knowledge-base-locally"></a>Bilgi bankaınızı yerel olarak Düzenle

Bilgi Bankası oluşturulduktan sonra, yerel dosyalar aracılığıyla dışarı aktarmak ve yeniden içeri aktarmak yerine [soru-cevap oluşturma portalındaki](https://qnamaker.ai)Bilgi Bankası metninde düzenleme yapmanız önerilir. Ancak, bir bilgi bankasını yerel olarak düzenlemeniz gerektiği zamanlar olabilir. 

Bilgi Bankası 'nı **Ayarlar** sayfasından dışarı aktarın ve Bilgi Bankası 'Nı Microsoft Excel ile düzenleyin. İçe aktarılmış TSV Dosyanızı düzenlemek için başka bir uygulama kullanmayı seçerseniz, uygulama tam olarak uyumlu olmadığından sözdizimi hataları verebilir. Microsoft Excel 'in TSV dosyaları genellikle biçimlendirme hataları sunmaz. 

Düzenlemelerinizi tamamladıktan sonra, **Ayarlar** sayfasından TSV dosyasını yeniden içeri aktarın. Bu işlem, geçerli bilgi bankasını içeri aktarılan Bilgi Bankası ile tamamen değiştirir. 

## <a name="testing-your-markdown"></a>Marka testinizi test etme

Markın 'ı doğrulamak için **[Commonmark](https://commonmark.org/help/tutorial/index.html)** öğreticisini kullanın. Öğreticide hızlı kopyalama/yapıştırma doğrulaması için bir **deneyin** özelliği bulunur. 

## <a name="version-control-for-data-in-your-knowledge-base"></a>Bilgi bankasındaki veriler için sürüm denetimi

Veriler için sürüm denetimi, **Ayarlar** sayfasındaki [içeri/dışarı aktarma özelliği](development-lifecycle-knowledge-base.md#version-control-of-a-knowledge-base) aracılığıyla sağlanır. 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Soru-Cevap Oluşturma Hizmeti ayarlama](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Ayrıca bkz. 

[Soru-Cevap Oluşturma’ya genel bakış](../Overview/overview.md)
