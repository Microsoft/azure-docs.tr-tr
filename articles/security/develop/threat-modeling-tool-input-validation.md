---
title: Giriş doğrulaması-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: Threat Modeling Tool kullanıma sunulan tehditler için azaltmaları
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: f443bf3111d2ab97874bdc62ec1370d17e2fc406
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728052"
---
# <a name="security-frame-input-validation--mitigations"></a>Güvenlik çerçevesi: Giriş doğrulaması | Karşı 
| Ürün/hizmet | Makale |
| --------------- | ------- |
| **Web uygulaması** | <ul><li>[Güvenilmeyen stil sayfaları kullanan tüm dönüşümler için XSLT komut dosyalarını devre dışı bırak](#disable-xslt)</li><li>[Kullanıcı denetlenebilir içerik içerebilen her sayfanın otomatik MIME algılaması dışında olduğundan emin olun](#out-sniffing)</li><li>[XML varlık çözünürlüğünü Harden veya devre dışı bırakma](#xml-resolution)</li><li>[Http. sys kullanan uygulamalar URL Kurallaştırma doğrulaması gerçekleştirir](#app-verification)</li><li>[Kullanıcılardan dosya kabul edilirken uygun denetimlerin yerinde olduğundan emin olun](#controls-users)</li><li>[Veri erişimi için Web uygulamasında tür kullanımı uyumlu parametrelerin kullanıldığından emin olun](#typesafe)</li><li>[MVC toplu atama güvenlik açığı 'nı engellemek için ayrı model bağlama sınıfları veya bağlama filtresi listeleri kullanın](#binding-mvc)</li><li>[İşlemeden önce güvenilmeyen Web çıkışını kodla](#rendering)</li><li>[Tüm dize türü model özelliklerinde giriş doğrulaması ve filtreleme gerçekleştirme](#typemodel)</li><li>[Temizleme, tüm karakterleri kabul eden form alanlarına uygulanmalıdır, örn. zengin metin Düzenleyicisi](#richtext)</li><li>[Oluşturulmuş kodlamaya sahip olmayan bir havuza DOM öğeleri atamayın](#inbuilt-encode)</li><li>[Uygulamanın içindeki tüm yeniden yönlendirmeler kapalı veya güvenli şekilde tamamlandı](#redirect-safe)</li><li>[Denetleyici yöntemleri tarafından kabul edilen tüm dize türü parametrelerinde giriş doğrulamasını Uygula](#string-method)</li><li>[Normal ifade işleme için üst sınır zaman aşımını ayarla çünkü hatalı normal ifadeler nedeniyle DoS 'yi önler](#dos-expression)</li><li>[Razor görünümlerinde HTML. RAW kullanmaktan kaçının](#html-razor)</li></ul> | 
| **Veritabanı** | <ul><li>[Saklı yordamlarda dinamik sorgular kullanmayın](#stored-proc)</li></ul> |
| **Web API** | <ul><li>[Web API yöntemlerinde model doğrulamasının yapıldığından emin olun](#validation-api)</li><li>[Web API yöntemleri tarafından kabul edilen tüm dize türü parametrelerinde giriş doğrulamasını Uygula](#string-api)</li><li>[Veri erişimi için Web API 'sinde tür kullanımı uyumlu parametrelerin kullanıldığından emin olun](#typesafe-api)</li></ul> | 
| **Azure belge DB** | <ul><li>[Azure Cosmos DB için parametreli SQL sorguları kullanın](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[Şema bağlama aracılığıyla WCF giriş doğrulaması](#schema-binding)</li><li>[Parametre Inspectors aracılığıyla WCF girişi doğrulama](#parameters)</li></ul> |

## <a id="disable-xslt"></a>Güvenilmeyen stil sayfaları kullanan tüm dönüşümler için XSLT komut dosyalarını devre dışı bırak

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [XSLT güvenliği](https://msdn.microsoft.com/library/ms763800(v=vs.85).aspx), [XsltSettings. EnableScript özelliği](https://msdn.microsoft.com/library/system.xml.xsl.xsltsettings.enablescript.aspx) |
| **Adımları** | XSLT, `<msxml:script>` öğe kullanılarak stil sayfaları içinde betik oluşturmayı destekler. Bu, özel işlevlerin XSLT dönüşümünde kullanılmasına izin verir. Betik, dönüşüm gerçekleştiren işlem bağlamında yürütülür. Güvenilmeyen kodun yürütülmesini engellemek için güvenilmeyen bir ortamda XSLT betiği devre dışı bırakılmalıdır. *.NET kullanıyorsanız:* XSLT komut dosyası varsayılan olarak devre dışıdır; Ancak, `XsltSettings.EnableScript` özelliği aracılığıyla açıkça etkinleştirilmemiş olduğundan emin olmanız gerekir.|

### <a name="example"></a>Örnek 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Örnek
MSXML 6,0 kullanıyorsanız, XSLT komut dosyası varsayılan olarak devre dışıdır; Ancak, XML DOM nesne özelliği AllowXsltScript aracılığıyla açıkça etkinleştirilmemiş olduğundan emin olmanız gerekir. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Örnek
MSXML 5 veya daha fazlasını kullanıyorsanız, XSLT komut dosyası varsayılan olarak etkindir ve açıkça devre dışı bırakmanız gerekir. AllowXsltScript XML DOM nesnesi özelliğini false olarak ayarlayın. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a id="out-sniffing"></a>Kullanıcı denetlenebilir içerik içerebilen her sayfanın otomatik MIME algılaması dışında olduğundan emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [IE8 güvenlik bölümü V-kapsamlı koruma](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx)  |
| **Adımları** | <p>Kullanıcı denetlenebilir içerik içerebilen her sayfa için HTTP üstbilgisini `X-Content-Type-Options:nosniff`kullanmanız gerekir. Bu gereksinimle uyum sağlamak için, yalnızca Kullanıcı tarafından denetlenebilir içerik içerebilen sayfalar için gerekli üst bilgi sayfasını sayfaya göre ayarlayabilir veya uygulamadaki tüm sayfalar için küresel olarak ayarlayabilirsiniz.</p><p>Bir Web sunucusundan alınan her dosya türü, içeriğin yapısını (yani, görüntü, metin, uygulama vb.) açıklayan ilişkili bir [MIME türüne](https://en.wikipedia.org/wiki/Mime_type) ( *içerik türü*de denir) sahiptir.</p><p>X-Content-Type-Options üst bilgisi, geliştiricilerin içeriklerinin MIME önlenmesini belirtmelerine izin veren bir HTTP başlığıdır. Bu üstbilgi, MIME algılaması saldırılarını azaltmak için tasarlanmıştır. Internet Explorer 8 ' de (ıE8) Bu üst bilgi için destek eklendi</p><p>X-Content-Type-Options, yalnızca Internet Explorer 8 (ıE8) kullanıcıları tarafından faydalanır. Internet Explorer 'ın önceki sürümleri, şu anda X-Content-Type-Options üst bilgisine uymaz</p><p>Internet Explorer 8 (ve üzeri), bir MIME algılaması geri çevirme özelliği uygulamak için tek büyük tarayıcılardır. Diğer büyük tarayıcılar (Firefox, Safari, Chrome) benzer özellikler uygulayacağından, bu öneri bu tarayıcıların söz dizimini da içerecek şekilde güncelleştirilecektir</p>|

### <a name="example"></a>Örnek
Gerekli üst bilgiyi uygulamadaki tüm sayfalar için genel olarak etkinleştirmek üzere aşağıdakilerden birini yapabilirsiniz: 

* Uygulama Internet Information Services (IIS) ile barındırılıyorsa, Web. config dosyasına üst bilgiyi ekleyin 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* Genel uygulama\_BeginRequest aracılığıyla üstbilgiyi ekleyin 

``` 
void Application_BeginRequest(object sender, EventArgs e)
{
  this.Response.Headers[""X-Content-Type-Options""] = ""nosniff"";
} 
```

* Özel HTTP modülünü Uygula 

``` 
public class XContentTypeOptionsModule : IHttpModule 
  {
    #region IHttpModule Members 
    public void Dispose() 
    { 

    } 
    public void Init(HttpApplication context)
    { 
      context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders); 
    } 
    #endregion 
    void context_PreSendRequestHeaders(object sender, EventArgs e) 
      { 
        HttpApplication application = sender as HttpApplication; 
        if (application == null) 
          return; 
        if (application.Response.Headers[""X-Content-Type-Options ""] != null) 
          return; 
        application.Response.Headers.Add(""X-Content-Type-Options "", ""nosniff""); 
      } 
  } 

``` 

* Gerekli üstbilgiyi tek tek yanıtlara ekleyerek yalnızca belirli sayfalar için etkinleştirebilirsiniz: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a id="xml-resolution"></a>XML varlık çözünürlüğünü Harden veya devre dışı bırakma

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [XML varlık genişletmesi](https://capec.mitre.org/data/definitions/197.html), [XML hizmet reddi saldırıları ve savunmaları](https://msdn.microsoft.com/magazine/ee335713.aspx), [MSXML güvenliğine genel bakış](https://msdn.microsoft.com/library/ms754611(v=VS.85).aspx), [MSXML kodu güvenliğini sağlamaya yönelik En Iyi uygulamalar](https://msdn.microsoft.com/library/ms759188(VS.85).aspx), [nsxmlparserdelegate protokol başvurusu](https://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html), [dış başvuruları çözme ](https://msdn.microsoft.com/library/5fcwybb2.aspx) |
| **Adımları**| <p>Yaygın olarak kullanılmamış olsa da, XML ayrıştırıcısının, makro varlıklarını belgenin kendisi veya dış kaynaklardan tanımlanmış değerlerle genişletmesine izin veren bir XML özelliği vardır. Örneğin, belge "Microsoft" değeriyle "COMPANYNAME" bir varlık tanımlayabilir, böylece "&companyname;" metni belgede her göründüğünde, otomatik olarak Microsoft metni ile değiştirilmiştir. Veya belge, Microsoft stok 'nin geçerli değerini getirmek üzere bir dış Web hizmetine başvuran "MSFTStock" varlığını tanımlayabilir.</p><p>Sonra, belgede "&MSFTStock;" herhangi bir zaman görünür, otomatik olarak geçerli hisse senedi fiyatıyla değiştirilmiştir. Ancak, bu işlev hizmet reddi (DoS) koşulları oluşturmak için de kullanılabilir. Saldırgan, sistemdeki tüm kullanılabilir belleği tüketen bir üstel genişleme XML bombasını oluşturmak için birden çok varlığı iç içe alabilir. </p><p>Alternatif olarak, sınırsız miktarda veri akışı yapan veya yalnızca iş parçacığını askıda bir dış başvuru oluşturabilir. Sonuç olarak, tüm ekipler, kendi uygulamaları kullanmıyorsa iç ve/veya dış XML varlık çözümlemesini tamamen devre dışı bırakmalıdır ya da bu işlev ise uygulamanın varlık çözümlemesi için kullanabileceği bellek miktarını ve süreyi el ile sınırlayabilir kesinlikle gerekli. Uygulamanız için varlık çözümlemesi gerekmiyorsa devre dışı bırakın. </p>|

### <a name="example"></a>Örnek
.NET Framework kod için aşağıdaki yaklaşımları kullanabilirsiniz:

```csharp
XmlTextReader reader = new XmlTextReader(stream);
reader.ProhibitDtd = true;

XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);

// for .NET 4
XmlReaderSettings settings = new XmlReaderSettings();
settings.DtdProcessing = DtdProcessing.Prohibit;
XmlReader reader = XmlReader.Create(stream, settings);
```
`XmlTextReader` Öğesinin `ProhibitDtd` varsayılandeğeri`XmlReaderSettings` true, ancak içinde false olduğunu unutmayın. XmlReaderSettings kullanıyorsanız, Probitdtd 'yi açıkça true olarak ayarlamanız gerekmez, ancak bunu yaptığınız güvenliği sake için tavsiye edilir. Ayrıca, XmlDocument sınıfının varsayılan olarak varlık çözümlemesine izin verdiğini unutmayın. 

### <a name="example"></a>Örnek
XMLDocuments için varlık çözünürlüğünü devre dışı bırakmak için, `XmlDocument.Load(XmlReader)` Load yönteminin aşırı yüklemesini kullanın ve aşağıdaki kodda gösterildiği gibi XmlReader bağımsız değişkeninde uygun özellikleri çözümlemeyi devre dışı bırakmak için ayarlayın: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Örnek
Uygulamanız için varlık çözünürlüğünü devre dışı bırakmak mümkün değilse, XmlReaderSettings. Maxkaraktersfromentities özelliğini uygulamanızın gereksinimlerine göre makul bir değere ayarlayın. Bu, olası üstel genişleme DoS saldırılarının etkisini sınırlandırır. Aşağıdaki kod bu yaklaşımın bir örneğini sağlar: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Örnek
Satır içi varlıkları çözmeniz gerekiyorsa ancak dış varlıkları çözmeniz gerekmiyorsa, XmlReaderSettings. XmlResolver özelliğini null olarak ayarlayın. Örneğin: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
MSXML6 'da, Probitdtd 'nin varsayılan olarak true (DTD işlemesini devre dışı bırakır) olarak ayarlandığını unutmayın. Apple OSX/iOS kodu için kullanabileceğiniz iki XML ayrıştırıcıları vardır: NSXMLParser ve libXML2. 

## <a id="app-verification"></a>Http. sys kullanan uygulamalar URL Kurallaştırma doğrulaması gerçekleştirir

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | <p>Http. sys kullanan tüm uygulamalar aşağıdaki yönergeleri izlemelidir:</p><ul><li>URL uzunluğunu 16.384 karakterden (ASCII veya Unicode) fazla olmayacak şekilde sınırlayın. Bu, varsayılan Internet Information Services (IIS) 6 ayarını temel alan mutlak en büyük URL uzunluğudur. Mümkünse, Web siteleri bu değerden daha kısa bir uzunluğa sahip olmalıdır</li><li>.NET FX 'teki kurallı kullanım kurallarından yararlanabilmeleri için standart .NET Framework dosya g/ç sınıflarını (FILESTREAM gibi) kullanın</li><li>Bilinen dosya adlarının açıkça bir izin listesi oluşturun</li><li>Bilinen dosya türlerini açıkça Reddet UrlScan reddedecelerdir: exe, bat, cmd, com, htw, Ise, IDQ, htr, IDC, SHTM [l], STM, Printer, ini, Pol, DAT dosyaları</li><li>Aşağıdaki özel durumları yakalayın:<ul><li>System. ArgumentException (cihaz adları için)</li><li>System. NotSupportedException (veri akışları için)</li><li>System. ıO. FileNotFoundException (geçersiz kaçan dosya adları için)</li><li>System. ıO. DirectoryNotFoundException (geçersiz kaçan dizin için)</li></ul></li><li>Win32 dosya g/ç API *'lerini çağırmayın* . Geçersiz bir URL 'de kullanıcıya düzgün bir 400 hatası döndürür ve gerçek hatayı günlüğe kaydeder.</li></ul>|

## <a id="controls-users"></a>Kullanıcılardan dosya kabul edilirken uygun denetimlerin yerinde olduğundan emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [Kısıtlanmamış dosya yükleme](https://www.owasp.org/index.php/Unrestricted_File_Upload), [Dosya imza tablosu](https://www.garykessler.net/library/file_sigs.html) |
| **Adımları** | <p>Karşıya yüklenen dosyalar, uygulamalar için önemli bir riski temsil eder.</p><p>Birçok saldırının ilk adımı, sisteme saldırıya uğramakta bir kod edinmelidir. Daha sonra saldırının yalnızca yürütülen kodu alması için bir yol bulması gerekir. Bir dosya yükleme işleminin kullanılması, saldırganın ilk adımı yerine getirmesinin sağlanmasına yardımcı olur. Sınırsız dosya yükleme işleminin sonuçları, aşırı yüklenmiş bir dosya sistemi veya veritabanı, arka uç sistemlerine yönelik saldırıları iletme ve basit savunma dahil olmak üzere farklılık gösterebilir.</p><p>Uygulamanın karşıya yüklenen dosyayı ve özellikle nerede depolandığına bağlıdır. Dosya karşıya yüklemelerinin sunucu tarafında doğrulanması eksik. Karşıya dosya yükleme işlevselliği için aşağıdaki güvenlik denetimlerinin uygulanması gerekir:</p><ul><li>Dosya Uzantısı denetimi (yalnızca geçerli bir izin verilen dosya türü kümesi kabul edilmelidir)</li><li>En büyük dosya boyutu sınırı</li><li>Dosya Webroot 'e yüklenmemelidir; konum, sistem dışı sürücüdeki bir dizin olmalıdır</li><li>Dosya üzerine yazılmasına engel olmak için, karşıya yüklenen dosya adı biraz rastgele olacak şekilde adlandırma kuralına uyulmalıdır.</li><li>Dosyalar diske yazılmadan önce virüsten koruma için taranmalıdır</li><li>Dosya adının ve diğer tüm meta verilerin (ör. dosya yolu) kötü amaçlı karakterler için doğrulanıp doğrulanması</li><li>Bir kullanıcının, bir kullanıcı tarafından işaretlenmiş bir dosyayı karşıya yüklemesini engellemek için dosya biçimi imzası işaretlenmelidir (ör., uzantıyı txt olarak değiştirerek bir exe dosyası karşıya yükleniyor)</li></ul>| 

### <a name="example"></a>Örnek
Dosya biçimi imza doğrulamasında ilgili son nokta için, Ayrıntılar için aşağıdaki sınıfa başvurun: 

```csharp
        private static Dictionary<string, List<byte[]>> fileSignature = new Dictionary<string, List<byte[]>>
                    {
                    { ".DOC", new List<byte[]> { new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 } } },
                    { ".DOCX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".PDF", new List<byte[]> { new byte[] { 0x25, 0x50, 0x44, 0x46 } } },
                    { ".ZIP", new List<byte[]> 
                                            {
                                              new byte[] { 0x50, 0x4B, 0x03, 0x04 },
                                              new byte[] { 0x50, 0x4B, 0x4C, 0x49, 0x54, 0x55 },
                                              new byte[] { 0x50, 0x4B, 0x53, 0x70, 0x58 },
                                              new byte[] { 0x50, 0x4B, 0x05, 0x06 },
                                              new byte[] { 0x50, 0x4B, 0x07, 0x08 },
                                              new byte[] { 0x57, 0x69, 0x6E, 0x5A, 0x69, 0x70 }
                                                }
                                            },
                    { ".PNG", new List<byte[]> { new byte[] { 0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A } } },
                    { ".JPG", new List<byte[]>
                                    {
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE1 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE8 }
                                    }
                                    },
                    { ".JPEG", new List<byte[]>
                                        { 
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 }
                                        }
                                        },
                    { ".XLS", new List<byte[]>
                                            {
                                              new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 },
                                              new byte[] { 0x09, 0x08, 0x10, 0x00, 0x00, 0x06, 0x05, 0x00 },
                                              new byte[] { 0xFD, 0xFF, 0xFF, 0xFF }
                                            }
                                            },
                    { ".XLSX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".GIF", new List<byte[]> { new byte[] { 0x47, 0x49, 0x46, 0x38 } } }
                };

        public static bool IsValidFileExtension(string fileName, byte[] fileData, byte[] allowedChars)
        {
            if (string.IsNullOrEmpty(fileName) || fileData == null || fileData.Length == 0)
            {
                return false;
            }

            bool flag = false;
            string ext = Path.GetExtension(fileName);
            if (string.IsNullOrEmpty(ext))
            {
                return false;
            }

            ext = ext.ToUpperInvariant();

            if (ext.Equals(".TXT") || ext.Equals(".CSV") || ext.Equals(".PRN"))
            {
                foreach (byte b in fileData)
                {
                    if (b > 0x7F)
                    {
                        if (allowedChars != null)
                        {
                            if (!allowedChars.Contains(b))
                            {
                                return false;
                            }
                        }
                        else
                        {
                            return false;
                        }
                    }
                }

                return true;
            }

            if (!fileSignature.ContainsKey(ext))
            {
                return true;
            }

            List<byte[]> sig = fileSignature[ext];
            foreach (byte[] b in sig)
            {
                var curFileSig = new byte[b.Length];
                Array.Copy(fileData, curFileSig, b.Length);
                if (curFileSig.SequenceEqual(b))
                {
                    flag = true;
                    break;
                }
            }

            return flag;
        }
```

## <a id="typesafe"></a>Veri erişimi için Web uygulamasında tür kullanımı uyumlu parametrelerin kullanıldığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | <p>Parametreler koleksiyonunu kullanırsanız, SQL girişi, çalıştırılabilir kod olarak bunun yerine değişmez değer olarak değerlendirir. Parametreler koleksiyonu, giriş verilerinde tür ve uzunluk kısıtlamalarını zorlamak için kullanılabilir. Aralığın dışındaki değerler özel bir durum tetikler. Tür açısından güvenli SQL parametreleri kullanılmazsa saldırganlar, filtrelenmemiş girişte gömülü ekleme saldırılarını yürütebiliyor olabilir.</p><p>Filtrelenmemiş girişte gerçekleşebilecek olası SQL ekleme saldırılarına karşı SQL sorguları oluştururken tür kullanımı güvenli parametreleri kullanın. Tür güvenli parametreleri, saklı yordamlar ve dinamik SQL deyimleriyle kullanabilirsiniz. Parametreler, çalıştırılabilir kod olarak değil, veritabanı tarafından değişmez değer olarak değerlendirilir. Parametreler, türü ve uzunluğu için de denetlenir.</p>|

### <a name="example"></a>Örnek 
Aşağıdaki kod, saklı yordam çağrılırken SqlParameterCollection ile tür kullanımı güvenli parametrelerinin nasıl kullanılacağını gösterir. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
Yukarıdaki kod örneğinde, giriş değeri 11 karakterden daha uzun olamaz. Veriler parametresi tarafından tanımlanan tür veya uzunluğa uygun değilse, SqlParameter sınıfı bir özel durum oluşturur. 

## <a id="binding-mvc"></a>MVC toplu atama güvenlik açığı 'nı engellemek için ayrı model bağlama sınıfları veya bağlama filtresi listeleri kullanın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | MVC5, MVC6 |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [Meta veri öznitelikleri](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.metadatatypeattribute), [ortak anahtar güvenlik açığı ve hafifletme](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation), [ASP.NET MVC 'de toplu ATAMAYA yönelik KAPSAMLı kılavuz](https://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [MVC kullanarak EF ile çalışmaya](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) başlama |
| **Adımları** | <ul><li>**Ne zaman daha fazla deftere nakil güvenlik açığına bakmalıyım?-** Kullanıcı girişinden model sınıfları bağlayan herhangi bir yerde, daha fazla yayınlama güvenlik açığı oluşabilir. MVC gibi çerçeveler, düz eski CLR nesneleri (POCOs) dahil olmak üzere özel .NET sınıflarında Kullanıcı verilerini temsil edebilir. MVC, bu model sınıflarını istekteki verilerle otomatik olarak doldurur ve Kullanıcı girişiyle ilgilenmek için kullanışlı bir gösterim sağlar. Bu sınıflar, Kullanıcı tarafından ayarlanmaması gereken özellikler içermekle, uygulama, uygulamanın asla hedeflendiğine yönelik veri denetimine izin veren aşırı ve olmayan saldırılara karşı savunmasız olabilir. MVC model bağlama gibi, nesne/ilişkisel maptesel gibi veritabanı erişim teknolojileri de Entity Framework benzer şekilde veritabanı verilerini temsil etmek için POCO nesnelerini kullanmayı da destekler. Bu veri modeli sınıfları, MVC 'nin Kullanıcı girişiyle ilgilenirken veritabanı verileriyle ilgili olarak aynı kolaylığa sahiptir. Hem MVC hem de veritabanı, POCO nesneleri gibi benzer modelleri desteklediği için, her iki amaç için de aynı sınıfları yeniden kullanmak kolaydır. Bu uygulama, kaygıları ayırmayı koruyamaz ve istenmeyen özelliklerin model bağlamaya sunulabileceği, daha fazla gönderim saldırıları olanaklı hale gelen bir ortak alandır.</li><li>**Neden filtrelenmemiş veritabanı modeli SıNıFLARıMı MVC eylemlerim için parametre olarak kullanmalıyım?-** MVC model bağlaması bu sınıftaki herhangi bir şeyi bağlayacağından. Veriler görünümünüzde görünmese de, kötü niyetli bir Kullanıcı bu verilerle birlikte bir HTTP isteği gönderebilir ve bu, eyleminiz, veritabanı sınıfının Kullanıcı girişi için kabul etmesi gereken verilerin şekli olduğunu belirttiğinden, MVC bunu bir şekilde bağlar.</li><li>**Model bağlama için kullanılan şekilden neden ilgilenmeliyim?-** Aşırı geniş modellerle ASP.NET MVC model bağlamasının kullanılması, bir uygulamayı aşırı gönderme saldırılarına sunar. Daha fazla gönderim, saldırganların, bir öğenin fiyatını geçersiz kılma veya bir hesabın güvenlik ayrıcalıkları gibi geliştiricilerin ne kadar iyi bir şekilde değişiklik kurmasını sağlar. Uygulamalar, model bağlama aracılığıyla izin verilen güvenilmeyen giriş için açık bir anlaşma sağlamak üzere eyleme özgü bağlama modellerini (veya belirli bir izin verilen özellik filtresi listesini) kullanmalıdır.</li><li>**Ayrı bağlama modelleriniz yalnızca kodu çoğaltmaktır mi?-** Hayır, kaygıları ayrımı çok önemlidir. İşlem yöntemlerinde veritabanı modellerini yeniden kullanırsanız, bu sınıftaki herhangi bir özelliğin (veya alt özellik) bir HTTP isteğindeki kullanıcı tarafından ayarlanarak söylediğini söyleyerek görürsünüz. Bu, MVC 'nin yapmak istediğiniz şey değilse, bunun yerine Kullanıcı girişinden hangi verilerin gelebileceklerini göstermek için bir filtre listesi veya ayrı bir sınıf şekli gerekir.</li><li>**Kullanıcı girişi için ayrı bağlama modellerim varsa, tüm veri ek açıklama özniteliklerini yinelemem gerekir mi?** Gerekli değildir. Model bağlama sınıfındaki meta verilere bağlantı sağlamak için veritabanı modeli sınıfında MetadataTypeAttribute öğesini kullanabilirsiniz. MetadataTypeAttribute tarafından başvurulan türün başvuru türünün bir alt kümesi olması gerektiğini unutmayın (daha az sayıda özelliği olabilir, ancak daha fazla olamaz).</li><li>**Verileri Kullanıcı giriş modelleri ve veritabanı modelleri arasında geri ve ileri taşımak sıkıcı bir. Yalnızca yansıma kullanarak tüm özelliklerin üzerine kopyalayabilir miyim? -** Evet. Bağlama modellerinde görüntülenen tek özellikler, Kullanıcı girişi için güvenli olduğunu belirledikleridir. Bu iki model arasında ortak olan tüm özellikleri kopyalamak için yansıma kullanımını engelleyen bir güvenlik nedeni yoktur.</li><li>**[Bind (exclude = "â € ¦")] hakkında. Ayrı bağlama modelleri yerine bunu kullanabilir miyim? -** Bu yaklaşım önerilmez. [Bind (exclude = "â € ¦")] kullanımı, yeni bir özelliğin varsayılan olarak bağlanabilir olduğu anlamına gelir. Yeni bir özellik eklendiğinde, tasarımın varsayılan olarak güvenli olmasını sağlamak yerine, şeyleri güvende tutmaya dikkat etmeniz için ek bir adım vardır. Geliştiriciye bağlı olarak, bir özellik her eklendiğinde bu listeyi denetleme riskli olur.</li><li>**[Bind (Include = "â € ¦")] düzenleme işlemleri için faydalıdır?-** Eşleşen. [Bind (Include = "â € ¦")] yalnızca INSERT-Style işlemleri için uygundur (yeni veri ekleme). Güncelleştirme stili işlemler (varolan verileri yeniden düzeltme) için farklı bağlama modellerine sahip olan veya izin verilen özelliklerin açık bir listesini UpdateModel veya TryUpdateModel olarak geçirerek başka bir yaklaşım kullanın. Bir düzenleme işleminde [bind (Include = "â € ¦")] özniteliği eklemek, MVC 'nin bir nesne örneği oluşturacak ve yalnızca listelenen özellikleri ayarlayacağı ve diğerlerinin varsayılan değerlerinde ayrıldığı anlamına gelir. Veriler kalıcı olduğunda, tüm atlanan özellikler için değerleri varsayılan olarak sıfırlamak için mevcut varlığın tamamen yerini alır. Örneğin, ısadmin bir düzenleme işleminde [bind (Include = "â € ¦")] özniteliğinde atlanırsa, adı bu eylem aracılığıyla düzenlenen herhangi bir Kullanıcı ısadmin = false olarak sıfırlanır (düzenlenen herhangi bir kullanıcı yönetici durumunu kaybeder). Belirli özellikler için güncelleştirmeleri engellemek istiyorsanız Yukarıdaki diğer yaklaşımlardan birini kullanın. MVC araçları 'nın bazı sürümleri, düzenleme eylemlerinde [bind (Include = "â € ¦")] ile denetleyici sınıfları ürettiğine ve bu listeden bir özelliğin kaldırılması, çok sayıda kaldırma saldırılarını engelleyecek anlamına gelmez. Bununla birlikte, yukarıda açıklandığı gibi, bu yaklaşım amaçlanan gibi çalışmaz ve bunun yerine, atlanan özelliklerde bulunan tüm verileri varsayılan değerlerine sıfırlayacaktır.</li><li>**Oluşturma Işlemleri için, farklı bağlama modelleri yerine [bind (Include = "â € ¦")] kullanan uyarılar var mı?-** Yes. İlk olarak bu yaklaşım düzenleme senaryolarında çalışmaz, bu da tüm çapraz yazma güvenlik açıklarını azaltmak için iki ayrı yaklaşımın kullanılmasını gerektirir. İkincisi, ayrı bağlama modelleri Kullanıcı girişi için kullanılan şekil ve kalıcılık için kullanılan şekil arasındaki kaygıları ayırmayı zorlar, bir [bind (Include = "â € ¦")] yapmaz. Üçüncü olarak, [bind (Include = "â € ¦")] yalnızca en üst düzey özellikleri işleyebilir. özniteliğinde yalnızca alt özelliklerin ("Details.Name" gibi) bölümlerine izin vermezsiniz. Son olarak, [bind (Include = "â € ¦")] kullanılarak en önemlisi, sınıfın model bağlama için kullanıldığı her seferinde hatırlanabilecek ek bir adım ekler. Yeni bir eylem yöntemi doğrudan veri sınıfına bağlanır ve bir [bind (Include = "â € ¦")] özniteliğini eklemeyi unutursa, bu, [bind (Include = "â € ¦")] yaklaşımının varsayılan olarak biraz daha güvenli hale getirilmesine neden olur. [Bind (Include = "â € ¦")] kullanıyorsanız, veri sınıflarınızın eylem yöntemi parametresi olarak göründüğü her seferinde bunu belirtmek için her zaman dikkat edin.</li><li>**Oluşturma işlemleri için, [bind (Include = "â € ¦")] özniteliğini model sınıfına koymak ne olacak? Bu yaklaşım, özniteliği her eylem yöntemine yerleştirmeyi unutmamanız gereksinimini ortadan kaldırmaz mi? -** Bu yaklaşım bazı durumlarda işe yarar. Model türünün kendisinde [bind (Include = "â € ¦")] kullanımı (Bu sınıfı kullanan eylem parametreleri yerine), her eylem yöntemine [bind (Include = "â € ¦")] özniteliğini dahil etmeyi unutmayın. Doğrudan sınıfında özniteliği kullanılması, model bağlama amacıyla bu sınıfın ayrı bir yüzey alanını oluşturur. Ancak, bu yaklaşım model sınıfı başına yalnızca bir model bağlama şeklinin kullanılmasına izin verir. Bir eylem yönteminin bir alanın model bağlamaya izin verilmesi gerekiyorsa (örneğin, Kullanıcı rollerini güncelleştiren salt yönetici bir eylem) ve diğer eylemlerin bu alanın model bağlamasını önlemesi gerekir. Bu yaklaşım çalışmaz. Her sınıfta yalnızca bir model bağlama şekli olabilir; farklı eylemlere farklı model bağlama şekilleri gerekiyorsa, eylem yöntemlerinde ayrı model bağlama sınıfları veya ayrı [BIND (Include = "â € ¦")] özniteliklerini kullanarak bu ayrı şekilleri temsil etmesi gerekir.</li><li>**Bağlama modelleri nelerdir? Görüntüleme modelleriyle aynı şey mi? -** Bunlar birbiriyle ilgili iki kavramlardır. Bağlama modeli terimi, bir eylemin parametre listesinde kullanılan model sınıfına başvurur (MVC modeli bağlamalarından eylem yöntemine geçirilen şekil). Terim görünümü modeli bir eylem yönteminden bir görünüme geçirilen model sınıfına başvurur. Bir görünüme özgü model kullanmak, bir eylem yönteminden bir görünüme veri geçirmek için yaygın bir yaklaşımdır. Genellikle, bu şekil model bağlama için de uygundur ve terim görünümü modeli, her iki yerde de kullanılan modele başvurmak için kullanılabilir. Bu yordam, kesin olması için özellikle, toplu atama amaçları için önemli olan, eyleme geçen şekle odaklanan şekilde bağlama modelleri hakkında bilgi alır.</li></ul>| 

## <a id="rendering"></a>İşlemeden önce güvenilmeyen Web çıkışını kodla

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel, Web Forms, MVC5, MVC6 |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [ASP.net](https://msdn.microsoft.com/library/ms998274.aspx), siteler arası komut dosyası [oluşturma](https://cwe.mitre.org/data/definitions/79.html), [XSS (siteler arası betik oluşturma) engellemesini engelleme sayfası](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet) |
| **Adımları** | Siteler arası betik oluşturma (genellikle XSS olarak kısaltılır), çevrimiçi hizmetler veya Web 'den girdi tüketen herhangi bir uygulama/bileşen için bir saldırı vektörü olur. XSS Güvenlik açıkları, bir saldırganın savunmasız bir Web uygulaması aracılığıyla başka bir kullanıcının makinesinde betik yürütmesine izin verebilir. Kötü amaçlı betikler, tanımlama bilgilerini çalmak ve başka bir şekilde JavaScript aracılığıyla bir kurban 'in makinesiyle izin vermek için kullanılabilir. Kullanıcı girişi doğrulanırken XSS, bir Web sayfasında işlenmeden önce iyi biçimlendirilmiş ve kodlama olduğundan emin olarak engellenir. Giriş doğrulaması ve çıkış kodlaması, Web koruma kitaplığı kullanılarak yapılabilir. Yönetilen kod (C\#, vb.net, vb.) için, Kullanıcı girişinin bildirim aldığı içeriğe bağlı olarak Web Koruması (önleme-XSS) kitaplığından bir veya daha fazla uygun kodlama yöntemi kullanın:| 

### <a name="example"></a>Örnek

```csharp
* Encoder.HtmlEncode 
* Encoder.HtmlAttributeEncode 
* Encoder.JavaScriptEncode 
* Encoder.UrlEncode
* Encoder.VisualBasicScriptEncode 
* Encoder.XmlEncode 
* Encoder.XmlAttributeEncode 
* Encoder.CssEncode 
* Encoder.LdapEncode 
```

## <a id="typemodel"></a>Tüm dize türü model özelliklerinde giriş doğrulaması ve filtreleme gerçekleştirme

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Generic, MVC5, MVC6 |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [Doğrulama ekleme](https://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), [bir MVC uygulamasındaki model verilerini](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx)doğrulama, [ASP.NET MVC uygulamalarınız için temel ilkeler](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Adımları** | <p>Uygulamanın kötü amaçlı kullanıcı girdilerine karşı korunmuş olduğundan emin olmak için, uygulamanın kullanılmadan önce tüm giriş parametrelerinin doğrulanması gerekir. Bir beyaz liste doğrulama stratejisiyle sunucu tarafında normal ifade doğrulamaları kullanarak giriş değerlerini doğrulayın. Bu yöntemlere geçirilen ayıklanmış Kullanıcı girdileri/parametreleri kod ekleme güvenlik açıklarına neden olabilir.</p><p>Web uygulamaları için, giriş noktaları form alanları, QueryStrings, tanımlama bilgileri, HTTP üstbilgileri ve Web hizmeti parametreleri de içerebilir.</p><p>Model bağlama sırasında aşağıdaki giriş doğrulama denetimlerinin gerçekleştirilmesi gerekir:</p><ul><li>İzin verilen karakterleri ve izin verilen en fazla uzunluğu kabul etmek için model özelliklerine cevap içerisinde RegularExpression ek açıklaması ile açıklama eklenmelidir</li><li>Denetleyici yöntemleri ModelState geçerliliğini gerçekleştirmelidir</li></ul>|

## <a id="richtext"></a>Temizleme, tüm karakterleri kabul eden form alanlarına uygulanmalıdır, örn. zengin metin Düzenleyicisi

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [Güvenli olmayan girişi kodlama](https://msdn.microsoft.com/library/ff647397.aspx#paght000003_step3), [HTML temizleme](https://github.com/mganss/HtmlSanitizer) |
| **Adımları** | <p>Kullanmak istediğiniz tüm statik biçimlendirme etiketlerini belirler. Yaygın olarak `<b>` kullanılan bir uygulama, (kalın) ve `<i>` (italik) gibi güvenli HTML öğeleriyle biçimlendirmeyi kısıtlamadır.</p><p>Verileri yazmadan önce HTML kodlaması yapın. Bu, çalıştırılabilir kod olarak değil, metin olarak işlenmesine neden olan kötü amaçlı betik güvenliğini sağlar.</p><ol><li>\@ Sayfa yönergesine validateRequest = "false" özniteliğini ekleyerek ASP.NET istek doğrulamasını devre dışı bırak</li><li>Dize girişini HtmlEncode yöntemiyle kodla</li><li>Bir StringBuilder kullanın ve izin vermek istediğiniz HTML öğelerinde kodlamayı seçmeli olarak kaldırmak için Replace metodunu çağırın</li></ol><p>Başvurularda bulunan sayfa, ASP.NET istek doğrulamasını ayarlayarak `ValidateRequest="false"`devre dışı bırakır. BT HTML-girişi kodlar ve seçime bağlı `<b>` `<i>` olarak izin verir. Ayrıca, HTML temizleme için bir .NET kitaplığı da kullanılabilir.</p><p>Htmltemizleyen, XSS saldırılarına yol açabilecek yapıların HTML parçalarını ve belgelerini temizlemeye yönelik bir .NET kitaplığıdır. HTML ve CSS 'yi ayrıştırmak, işlemek ve işlemek için AngleSharp kullanır. Htmltemizleme, bir NuGet paketi olarak yüklenebilir ve Kullanıcı girişi, uygun olduğu şekilde sunucu tarafında, ilgili HTML veya CSS Temizleme yöntemleriyle geçirilebilir. Lütfen bir güvenlik denetimi olarak temizleme 'nin yalnızca son seçenek olarak değerlendirilmelidir.</p><p>Giriş doğrulama ve çıkış kodlaması daha iyi güvenlik denetimleri olarak değerlendirilir.</p> |

## <a id="inbuilt-encode"></a>Oluşturulmuş kodlamaya sahip olmayan bir havuza DOM öğeleri atamayın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | Birçok JavaScript işlevi, varsayılan olarak kodlama yapmayın. Bu tür işlevler aracılığıyla DOM öğelerine güvenilmeyen giriş atarken, siteler arası komut dosyası (XSS) yürütmelerinin oluşmasına neden olabilir.| 

### <a name="example"></a>Örnek
Aşağıdakiler güvenli olmayan örneklerdir: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Kullanmayın `innerHtml`; bunun yerine kullanın `innerText`. Benzer şekilde, yerine `$("#elm").html()`kullanın`$("#elm").text()` 

## <a id="redirect-safe"></a>Uygulamanın içindeki tüm yeniden yönlendirmeler kapalı veya güvenli şekilde tamamlandı

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [OAuth 2,0 yetkilendirme çerçevesi-açık Redirectors](https://tools.ietf.org/html/rfc6749#section-10.15) |
| **Adımları** | <p>Kullanıcı tarafından sağlanan bir konuma yeniden yönlendirme gerektiren uygulama tasarımı, olası yeniden yönlendirme hedeflerini, önceden tanımlanmış bir "güvenli" site veya etki alanı listesine sınırlandırmalıdır. Uygulamadaki tüm yeniden yönlendirmeler kapatılmalıdır/Safe olmalıdır.</p><p>Bunu yapmak için:</p><ul><li>Tüm yeniden yönlendirmeleri tanımla</li><li>Her yeniden yönlendirme için uygun bir azaltma uygulayın. Uygun azaltmaları, yeniden yönlendirme beyaz listesini veya Kullanıcı onayını içerir. Açık yeniden yönlendirme güvenlik açığı olan bir Web sitesi veya hizmeti Facebook/OAuth/OpenID kimlik sağlayıcılarını kullanıyorsa bir saldırgan, kullanıcının oturum açma belirtecini çalabilir ve bu kullanıcının kimliğine bürünebilir. Bu, RFC 6749 "OAuth 2,0 yetkilendirme çerçevesi", Bölüm 10,15 "açık yeniden yönlendirmeler" bölümünde belgelenen OAuth kullanılırken bir açık riskidir.</li></ul>|

## <a id="string-method"></a>Denetleyici yöntemleri tarafından kabul edilen tüm dize türü parametrelerinde giriş doğrulamasını Uygula

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Generic, MVC5, MVC6 |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [MVC uygulamasındaki model verilerini doğrulama](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [ASP.NET MVC uygulamalarınız için temel ilkeler](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Adımları** | Bağımsız değişken olarak model değil, ilkel veri türü kabul eden yöntemler için, normal Ifade kullanılarak giriş doğrulaması yapılmalıdır. Burada Regex. IsMatch geçerli bir Regex düzeniyle kullanılmalıdır. Giriş, belirtilen normal Ifadeyle eşleşmezse, denetim daha fazla ilerlemeymemelidir ve doğrulama hatası ile ilgili yeterli bir uyarı görüntülenmelidir.| 

## <a id="dos-expression"></a>Normal ifade işleme için üst sınır zaman aşımını ayarla çünkü hatalı normal ifadeler nedeniyle DoS 'yi önler

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel, Web Forms, MVC5, MVC6  |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [DefaultRegexMatchTimeout özelliği](https://msdn.microsoft.com/library/system.web.configuration.httpruntimesection.defaultregexmatchtimeout.aspx) |
| **Adımları** | Çok sayıda geri izlemeye neden olan, hatalı oluşturulmuş normal ifadelerle hizmet reddi saldırıları sağlamak için genel varsayılan zaman aşımını ayarlayın. İşlem süresi tanımlı üst sınırdan daha uzun sürerse, zaman aşımı özel durumu oluşturur. Hiçbir şey yapılandırılmamışsa, zaman aşımı sonsuz olur.| 

### <a name="example"></a>Örnek
Örneğin, aşağıdaki yapılandırma işleme 5 saniyeden fazla sürerse bir RegexMatchTimeoutException oluşturur: 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a id="html-razor"></a>Razor görünümlerinde HTML. RAW kullanmaktan kaçının

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | MVC5, MVC6 |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| Adım | ASP.NET Web sayfaları (Razor) otomatik HTML kodlaması gerçekleştirir. Gömülü kod nugal (@ blokları) tarafından yazdırılan tüm dizeler otomatik olarak HTML kodlanır. Ancak, `HtmlHelper.Raw` yöntemi çağrıldığında, HTML kodlu biçimlendirme döndürür. `Html.Raw()` Yardımcı yöntemi kullanılıyorsa, Razor 'nin sağladığı otomatik kodlama korumasını atlar.|

### <a name="example"></a>Örnek
Aşağıdakiler güvenli olmayan bir örnektir: 

```csharp
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
Biçimlendirme görüntülemesi gerekmiyorsa `Html.Raw()` kullanmayın. Bu yöntem, çıkış kodlamasını örtük olarak gerçekleştirmez. Diğer ASP.NET yardımcıları kullanın, örneğin`@Html.DisplayFor()` 

## <a id="stored-proc"></a>Saklı yordamlarda dinamik sorgular kullanmayın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Database | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | <p>SQL ekleme saldırısı, veritabanında rastgele komutları çalıştırmak için giriş doğrulamasında güvenlik açıklarına karşı yararlanır. Uygulamanız veritabanına erişmek için dinamik SQL deyimleri oluşturmak üzere giriş kullandığında bu durum oluşabilir. Kodunuzun ham Kullanıcı girişi içeren dizeleri geçen saklı yordamları kullanıyorsa de bu durum oluşabilir. Saldırgan, SQL ekleme saldırısını kullanarak veritabanında rastgele komutlar yürütebilir. Tüm SQL deyimleri (saklı yordamlarda SQL deyimleri dahil) parametreli olmalıdır. Parametreli SQL deyimleri, kesin olarak yazıldıklarından bağımsız olarak SQL 'e (tek tırnak gibi) özel anlamı olan karakterleri kabul eder. |

### <a name="example"></a>Örnek
Aşağıda, güvenli olmayan dinamik saklı yordamın bir örneği verilmiştir: 

```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteria]
(
  @productName nvarchar(200) = NULL,
  @startPrice float = NULL,
  @endPrice float = NULL
)
AS
 BEGIN
  DECLARE @sql nvarchar(max)
  SELECT @sql = ' SELECT ProductID, ProductName, Description, UnitPrice, ImagePath' +
       ' FROM dbo.Products WHERE 1 = 1 '
       PRINT @sql
  IF @productName IS NOT NULL
     SELECT @sql = @sql + ' AND ProductName LIKE ''%' + @productName + '%'''
  IF @startPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice > ''' + CONVERT(VARCHAR(10),@startPrice) + ''''
  IF @endPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice < ''' + CONVERT(VARCHAR(10),@endPrice) + ''''

  PRINT @sql
  EXEC(@sql)
 END
```

### <a name="example"></a>Örnek
Aşağıdakiler, aynı saklı yordamın güvenli şekilde uygulanmasıdır: 
```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteriaSecure]
(
             @productName nvarchar(200) = NULL,
             @startPrice float = NULL,
             @endPrice float = NULL
)
AS
       BEGIN
             SELECT ProductID, ProductName, Description, UnitPrice, ImagePath
             FROM dbo.Products where
             (@productName IS NULL or ProductName like '%'+ @productName +'%')
             AND
             (@startPrice IS NULL or UnitPrice > @startPrice)
             AND
             (@endPrice IS NULL or UnitPrice < @endPrice)         
       END
```

## <a id="validation-api"></a>Web API yöntemlerinde model doğrulamasının yapıldığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web API'si | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | MVC5, MVC6 |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [ASP.NET Web API 'de model doğrulaması](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Adımları** | Bir istemci bir Web API 'sine veri gönderdiğinde, herhangi bir işlem yapmadan önce verilerin doğrulanması zorunludur. Modelleri giriş olarak kabul eden ASP.NET Web API 'Leri için, modelin özelliklerinde doğrulama kuralları ayarlamak için modeller üzerinde veri ek açıklamaları kullanın.|

### <a name="example"></a>Örnek
Aşağıdaki kod, aynısını gösterir: 

```csharp
using System.ComponentModel.DataAnnotations;

namespace MyApi.Models
{
    public class Product
    {
        public int Id { get; set; }
        [Required]
        [RegularExpression(@"^[a-zA-Z0-9]*$", ErrorMessage="Only alphanumeric characters are allowed.")]
        public string Name { get; set; }
        public decimal Price { get; set; }
        [Range(0, 999)]
        public double Weight { get; set; }
    }
}
```

### <a name="example"></a>Örnek
API denetleyicilerinin Action yönteminde, modelin geçerliliği aşağıda gösterildiği gibi açıkça denetlenmelidir: 

```csharp
namespace MyApi.Controllers
{
    public class ProductsController : ApiController
    {
        public HttpResponseMessage Post(Product product)
        {
            if (ModelState.IsValid)
            {
                // Do something with the product (not shown).

                return new HttpResponseMessage(HttpStatusCode.OK);
            }
            else
            {
                return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
            }
        }
    }
}
```

## <a id="string-api"></a>Web API yöntemleri tarafından kabul edilen tüm dize türü parametrelerinde giriş doğrulamasını Uygula

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web API'si | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel, MVC 5, MVC 6 |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [MVC uygulamasındaki model verilerini doğrulama](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [ASP.NET MVC uygulamalarınız için temel ilkeler](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Adımları** | Bağımsız değişken olarak model değil, ilkel veri türü kabul eden yöntemler için, normal Ifade kullanılarak giriş doğrulaması yapılmalıdır. Burada Regex. IsMatch geçerli bir Regex düzeniyle kullanılmalıdır. Giriş, belirtilen normal Ifadeyle eşleşmezse, denetim daha fazla ilerlemeymemelidir ve doğrulama hatası ile ilgili yeterli bir uyarı görüntülenmelidir.|

## <a id="typesafe-api"></a>Veri erişimi için Web API 'sinde tür kullanımı uyumlu parametrelerin kullanıldığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web API'si | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | Yok  |
| **Adımları** | <p>Parametreler koleksiyonunu kullanırsanız, SQL girişi, çalıştırılabilir kod olarak bunun yerine değişmez değer olarak değerlendirir. Parametreler koleksiyonu, giriş verilerinde tür ve uzunluk kısıtlamalarını zorlamak için kullanılabilir. Aralığın dışındaki değerler özel bir durum tetikler. Tür açısından güvenli SQL parametreleri kullanılmazsa saldırganlar, filtrelenmemiş girişte gömülü ekleme saldırılarını yürütebiliyor olabilir.</p><p>Filtrelenmemiş girişte gerçekleşebilecek olası SQL ekleme saldırılarına karşı SQL sorguları oluştururken tür kullanımı güvenli parametreleri kullanın. Tür güvenli parametreleri, saklı yordamlar ve dinamik SQL deyimleriyle kullanabilirsiniz. Parametreler, çalıştırılabilir kod olarak değil, veritabanı tarafından değişmez değer olarak değerlendirilir. Parametreler, türü ve uzunluğu için de denetlenir.</p>|

### <a name="example"></a>Örnek
Aşağıdaki kod, saklı yordam çağrılırken SqlParameterCollection ile tür kullanımı güvenli parametrelerinin nasıl kullanılacağını gösterir. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
Yukarıdaki kod örneğinde, giriş değeri 11 karakterden daha uzun olamaz. Veriler parametresi tarafından tanımlanan tür veya uzunluğa uygun değilse, SqlParameter sınıfı bir özel durum oluşturur. 

## <a id="sql-docdb"></a>Cosmos DB için parametreli SQL sorguları kullanın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure belge DB | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [Azure Cosmos DB 'da SQL Parametreleştirme duyurusu](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Adımları** | Azure Cosmos DB yalnızca salt yazılır sorguları desteklese de, sorgular Kullanıcı girişiyle birleştirerek oluşturulursa SQL ekleme işlemi yine de mümkündür. Kötü amaçlı SQL sorguları oluşturarak, bir kullanıcının aynı koleksiyon dahilinde erişimleri olmaması mümkün olabilir. Sorgular kullanıcı girişine göre oluşturulursa parametreli SQL sorguları kullanın. |

## <a id="schema-binding"></a>Şema bağlama aracılığıyla WCF giriş doğrulaması

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | WCF | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel, NET Framework 3 |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [MSDN](https://msdn.microsoft.com/library/ff647820.aspx) |
| **Adımları** | <p>Doğrulamanın olmaması, farklı tür ekleme saldırılarına yol açar.</p><p>İleti doğrulama, WCF uygulamanızın korunmasında bir savunma hattını temsil eder. Bu yaklaşımda, WCF hizmeti işlemlerini kötü amaçlı bir istemci tarafından gerçekleştirilen saldırılara karşı korumak için şemaları kullanarak iletileri doğrularsınız. İstemciyi kötü amaçlı bir hizmet tarafından gerçekleştirilen saldırılara karşı korumak için istemci tarafından alınan tüm iletileri doğrulayın. İleti doğrulama, işlemler ileti sözleşmelerini veya veri sözleşmelerini kullanırken, bu durum parametre doğrulaması kullanılarak yapılamadıkları zaman iletileri doğrulamayı mümkün kılar. İleti doğrulama, şemalarda daha fazla esneklik sağlayarak ve geliştirme süresini azaltarak doğrulama mantığı oluşturmanıza olanak sağlar. Şemalar, kuruluşun içindeki farklı uygulamalar arasında yeniden kullanılabilir ve veri gösterimi için standartlar oluşturabilir. Ayrıca, ileti doğrulama, iş mantığını temsil eden sözleşmeleri içeren daha karmaşık veri türleri tükettiklerinde işlemleri korumanıza olanak sağlar.</p><p>İleti doğrulaması gerçekleştirmek için, ilk olarak hizmetinizin işlemlerini ve bu işlemler tarafından tüketilen veri türlerini temsil eden bir şema oluşturursunuz. Ardından, hizmete gönderilen/alınan iletileri doğrulamak için özel bir istemci ileti denetçisi ve özel dağıtıcı ileti denetçisi uygulayan bir .NET sınıfı oluşturursunuz. Sonra, hem istemcide hem de hizmette ileti doğrulamayı etkinleştirmek için özel bir uç nokta davranışı uygulayın. Son olarak, bir özel yapılandırma öğesini, hizmet veya istemcinin yapılandırma dosyasında genişletilmiş özel uç nokta davranışını sergileme olanağı sağlayan sınıfta uygular "</p>|

## <a id="parameters"></a>Parametre Inspectors aracılığıyla WCF girişi doğrulama

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | WCF | 
| **SDL aşaması**               | Oluşturma |  
| **İlgili teknolojiler** | Genel, NET Framework 3 |
| **Öznitelikler**              | Yok  |
| **Başvur**              | [MSDN](https://msdn.microsoft.com/library/ff647875.aspx) |
| **Adımları** | <p>Giriş ve veri doğrulama, WCF uygulamanızın korunmasında önemli bir savunma hattını temsil eder. Hizmeti kötü amaçlı bir istemci tarafından saldırıya karşı korumak için WCF hizmeti işlemlerinde sunulan tüm parametreleri doğrulamanız gerekir. Buna karşılık, istemciyi kötü amaçlı bir hizmet tarafından saldırıya karşı korumak için istemci tarafından alınan tüm dönüş değerlerini de doğrulamanız gerekir</p><p>WCF, Özel uzantılar oluşturarak WCF çalışma zamanı davranışını özelleştirmenize imkan tanıyan farklı genişletilebilirlik noktaları sağlar. İleti denetçileri ve parametre denetçileri, bir istemci ile hizmet arasında geçiş yapmak için kullanılan iki genişletilebilirlik mekanizmaktır. Giriş doğrulaması için parametre Denetçiler ' i ve yalnızca bir hizmetin içinde ve dışında tüm ileti akışını incelemeniz gerektiğinde ileti denetçiler kullanmanız gerekir.</p><p>Giriş doğrulaması gerçekleştirmek için, hizmetinizdeki işlemlerle ilgili parametreleri doğrulamak üzere bir .NET sınıfı oluşturacaksınız ve özel bir parametre denetçisi uygulayacaksınız. Daha sonra hem istemcide hem de hizmette doğrulamayı etkinleştirmek için özel bir uç nokta davranışı uygulayacaksınız. Son olarak, bir özel yapılandırma öğesini, hizmet veya istemcinin yapılandırma dosyasında genişletilmiş özel uç nokta davranışını sergileme olanağı sağlayan sınıfta uygulayacaksınız</p>|
