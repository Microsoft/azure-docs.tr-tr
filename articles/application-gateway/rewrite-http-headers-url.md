---
title: HTTP üst bilgilerini ve URL 'YI Azure Application Gateway yeniden yazın | Microsoft Docs
description: Bu makalede, Azure Application Gateway HTTP üstbilgilerini ve URL 'YI yeniden yazma hakkında genel bir bakış sunulmaktadır
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: azhussai
ms.openlocfilehash: b7cf7c98e71da215eb30dcab556a88d6d2701591
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789456"
---
# <a name="rewrite-http-headers-and-url-with-application-gateway"></a>HTTP üstbilgilerini ve URL 'YI Application Gateway yeniden yazın

Application Gateway, istek ve yanıtların seçili içeriğini yeniden yazmanız için izin verir. Bu özellikle, URL 'Leri, sorgu dizesi parametrelerini ve değişiklik isteği ve yanıt üst bilgilerini çevirebilirsiniz. Ayrıca, URL veya belirtilen üstbilgilerin yalnızca belirli koşullar karşılandığında yeniden yazılabilir olmasını sağlamak için koşullar eklemenize de olanak tanır. Bu koşullar istek ve yanıt bilgilerine dayanır.

>[!NOTE]
>HTTP üstbilgisi ve URL yeniden yazma özellikleri yalnızca [Application Gateway v2 SKU 'su](application-gateway-autoscaling-zone-redundant.md) için kullanılabilir

## <a name="rewrite-types-supported"></a>Yeniden yazma türleri destekleniyor

### <a name="request-and-response-headers"></a>İstek ve yanıt üst bilgileri

HTTP üstbilgileri, bir istemci ve sunucunun bir istek veya Yanıt ile ek bilgi geçmesini sağlar. Bu üst bilgileri yeniden yazarak, HSTS/X-XSS-Protection gibi güvenlikle ilgili üst bilgi alanlarını ekleme, hassas bilgileri açığa çıkarmış olan yanıt üst bilgisi alanlarını kaldırma ve üst bilgiler Için X-Iletilen bağlantı noktası bilgilerini kaldırma gibi önemli görevleri gerçekleştirebilirsiniz.

Application Gateway, istek ve yanıt paketleri istemci ile arka uç havuzları arasında gidip gelirken HTTP istek ve yanıt üst bilgileri eklemenize ya da mevcut bilgileri kaldırmanıza veya güncelleştirmenize olanak tanır.

Azure portal kullanarak istek ve yanıt üst bilgilerini Application Gateway yeniden yazmayı öğrenmek için [buraya](rewrite-url-portal.md)bakın.

![görüntü](./media/rewrite-http-headers-url/header-rewrite-overview.png)


**Desteklenen üstbilgiler**

İstekler ve yanıtlarındaki tüm üst bilgileri, bağlantı ve yükseltme üstbilgileri dışında yeniden yazabilirsiniz. Ayrıca, uygulama ağ geçidini kullanarak özel üstbilgiler oluşturabilir ve bunlara yönlendirilmekte olan isteklere ve yanıtlara ekleyebilirsiniz.

### <a name="url-path-and-query-string"></a>URL yolu ve sorgu dizesi

Application Gateway URL yeniden yazma özelliği ile şunları yapabilirsiniz:

* İstek URL 'sinin ana bilgisayar adını, yolunu ve sorgu dizesini yeniden yazın 

* Bir dinleyicide bulunan tüm isteklerin URL 'sini veya yalnızca ayarladığınız bir veya daha fazla koşuldan eşleşen istekleri yeniden yazmayı seçin. Bu koşullar istek ve yanıt özelliklerine (istek, üst bilgi, yanıt üst bilgisi ve sunucu değişkenleri) dayanır.

* Asıl URL 'yi veya yeniden yazan URL 'yi temel alarak isteği (arka uç havuzunu seçin) yeniden yönlendirmeyi seçin

Azure portal kullanarak URL 'YI Application Gateway yeniden yazmayı öğrenmek için [buraya](rewrite-url-portal.md)bakın.

![Application Gateway bir URL 'YI yeniden yazma işlemini açıklayan diyagram.](./media/rewrite-http-headers-url/url-rewrite-overview.png)

## <a name="rewrite-actions"></a>Yeniden yazma eylemleri

Yeniden yazma işlemlerini, yeniden yazmak istediğiniz URL 'yi, istek üstbilgilerini veya yanıt üstbilgilerini ve bunları yeniden yazmak istediğiniz yeni değeri belirtmek için kullanabilirsiniz. URL veya yeni ya da var olan bir üst bilginin değeri bu tür değerlere ayarlanabilir:

* Metin
* İstek üst bilgisi. Bir istek üst bilgisi belirtmek için {http_req_ *HeaderName*} sözdizimini kullanmanız gerekir
* Yanıt üst bilgisi. Yanıt üst bilgisi belirtmek için {http_resp_ *HeaderName*} sözdizimini kullanmanız gerekir
* Sunucu değişkeni. Sunucu değişkeni belirtmek için {var_ *Servervariable*} sözdizimini kullanmanız gerekir. Desteklenen sunucu değişkenlerinin listesini görüntüleyin
* Metin, istek üst bilgisi, yanıt üst bilgisi ve sunucu değişkeni birleşimi. 

## <a name="rewrite-conditions"></a>Yeniden yazma koşulları

HTTP (S) isteklerinin ve yanıtlarının içeriğini değerlendirmek ve yalnızca bir veya daha fazla koşul karşılandığında yeniden yazma gerçekleştirmek için, isteğe bağlı bir yapılandırma olan yeniden yazma koşullarını kullanabilirsiniz. Uygulama ağ geçidi, isteklerin ve yanıtların içeriğini değerlendirmek için bu tür değişkenleri kullanır:

* İstekteki HTTP üstbilgileri
* Yanıttaki HTTP üstbilgileri
* Application Gateway Server değişkenleri

Belirtilen değişkenin mevcut olup olmadığını, belirtilen değişkenin belirli bir değerle eşleşip eşleşmediğini veya belirtilen değişkenin belirli bir Düzenle eşleşip eşleşmediğini değerlendirmek için bir koşul kullanabilirsiniz. 


### <a name="pattern-matching"></a>Desen Eşleştirme 

Application Gateway, koşuldaki kalıp eşleme için normal ifadeler kullanır. Koşullardaki normal ifade deseninin eşleşmesini ayarlamak için [Perl uyumlu normal ifadeler (PCRE) kitaplığını](https://www.pcre.org/) kullanabilirsiniz. Normal ifade söz dizimi hakkında bilgi edinmek için bkz. [Perl normal ifadeler ana sayfası](https://perldoc.perl.org/perlre.html).

### <a name="capturing"></a>Yakalayan

Daha sonra kullanmak üzere bir alt dize yakalamak için, koşul Regex tanımında onunla eşleşen alt deseninin etrafına parantez koyun. Ayracın ilk çifti, alt dizesini 1, ikinci çift 2 ve benzeri bir şekilde depolar. Dilediğiniz kadar parantez kullanabilirsiniz; Perl yalnızca bu yakalanan dizeleri temsil etmeniz için daha fazla numaralandırılmış değişken tanımlamaya devam eder. [Ref](https://docstore.mik.ua/orelly/perl/prog3/ch05_07.htm)'den bazı örnekler: 

*  /(\d) (\d)/# iki basamakla eşleşir, gruplar 1 ve 2 ' ye yakalanmalıdır 

* /(\d +)/# bir veya daha fazla basamakla eşleşiyor, Tümünü Grup 1 ' de yakalanıyor 

* /(\d) +/# bir basamakla bir veya daha çok kez eşleşiyor, en son grup 1 ' i yakalama

Yakalandıktan sonra, aşağıdaki biçimi kullanarak eylem kümesindeki bunlara başvurabilirsiniz:

* İstek üst bilgisi yakalama için {http_req_headerName_groupNumber} kullanmanız gerekir. Örneğin, {http_req_User-Agent_1} veya {http_req_User-Agent_2}
* Yanıt üst bilgisi yakalama için {http_resp_headerName_groupNumber} kullanmanız gerekir. Örneğin, {http_resp_Location_1} veya {http_resp_Location_2}
* Sunucu değişkeni için {var_serverVariableName_groupNumber} kullanmanız gerekir. Örneğin, {var_uri_path_1} veya {var_uri_path_2}

Tüm değeri kullanmak istiyorsanız, bu sayıdan bahsetmemelidir. {Http_req_headerName}, vb. biçimini yalnızca groupNumber olmadan kullanın.

## <a name="server-variables"></a>Sunucu değişkenleri

Application Gateway sunucu, istemciyle bağlantı ve bağlantıdaki geçerli istek hakkındaki yararlı bilgileri depolamak için sunucu değişkenlerini kullanır. Depolanan bilgilere örnek olarak istemcinin IP adresi ve Web tarayıcı türü dahildir. Sunucu değişkenleri dinamik olarak değişir; Örneğin, yeni bir sayfa yüklendiğinde veya bir form gönderildiğinde. Yeniden yazma koşullarını değerlendirmek ve üstbilgileri yeniden yazmak için bu değişkenleri kullanabilirsiniz. Üst bilgileri yeniden yazmak için sunucu değişkenlerinin değerini kullanmak için, {var_ *serverVariableName*} sözdiziminde bu değişkenleri belirtmeniz gerekir.

Application Gateway aşağıdaki sunucu değişkenlerini destekler:

|   Değişken adı    |                   Description                                           |
| ------------------------- | ------------------------------------------------------------ |
| add_x_forwarded_for_proxy | `client_ip`IP1, IP2, IP3, vb. biçiminde bu değişkene eklenen (Bu tablodaki açıklamaya bakın) X-iletilmiş istemci isteği üst bilgisi alanı. X-Iletilmiş-for alanı istemci isteği üstbilgisinde yoksa, `add_x_forwarded_for_proxy` değişken `$client_ip` değişkenine eşittir.   Bu değişken özellikle, üstbilginin yalnızca bağlantı noktası bilgisi olmadan yalnızca IP adresini içermesi için, Application Gateway tarafından ayarlanmış X-Iletilmiş-for üst bilgisini yeniden yazmak istediğinizde yararlıdır. |
| ciphers_supported         | İstemci tarafından desteklenen şifrelemelerin listesi.               |
| ciphers_used              | Kurulan bir TLS bağlantısı için kullanılan şifre dizeleri. |
| client_ip                 | Uygulama ağ geçidinin isteği aldığı istemcinin IP adresi. Uygulama ağ geçidi ve kaynak istemci öncesinde ters bir ara sunucu varsa, `client_ip` ters proxy 'nın IP adresini döndürür. |
| client_port               | İstemci bağlantı noktası.                                             |
| client_tcp_rtt            | İstemci TCP bağlantısıyla ilgili bilgiler. TCP_INFO yuva seçeneğini destekleyen sistemlerde kullanılabilir. |
| client_user               | HTTP kimlik doğrulaması kullanıldığında, kimlik doğrulaması için sağlanan Kullanıcı adı. |
| konak                      | Öncelik sırasına göre: istek satırından ana bilgisayar adı, konak istek üst bilgisi alanından ana bilgisayar adı veya bir istekle eşleşen sunucu adı. Örnek: istekte `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` ana bilgisayar değeri şu şekilde olacaktır: `contoso.com` |
| cookie_ *adı*             | *Ad* tanımlama bilgisi.                                           |
| http_method               | URL isteğini yapmak için kullanılan yöntem. Örneğin, GET veya POST. |
| http_status               | Oturum durumu. Örneğin, 200, 400 veya 403.           |
| http_version              | İstek Protokolü. Genellikle HTTP/1.0, HTTP/1.1 veya HTTP/2.0. |
| query_string              | İstenen URL 'de "?" öğesini izleyen değişken/değer çiftleri listesi. Örnek: istekte `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` query_string değer olacaktır `id=123&title=fabrikam` |
| received_bytes            | İsteğin uzunluğu (istek satırı, üst bilgi ve istek gövdesi dahil). |
| request_query             | İstek satırındaki bağımsız değişkenler.                           |
| request_scheme            | İstek düzeni: http veya https.                           |
| request_uri               | Tüm özgün istek URI 'SI (bağımsız değişkenlerle). Örnek: istekte `http://contoso.com:8080/article.aspx?id=123&title=fabrikam*` request_uri değer olacaktır `/article.aspx?id=123&title=fabrikam` |
| sent_bytes                | Bir istemciye gönderilen bayt sayısı.                        |
| server_port               | Bir isteği kabul eden sunucunun bağlantı noktası.              |
| ssl_connection_protocol   | Kurulan bir TLS bağlantısının protokolü.               |
| ssl_enabled               | Bağlantı, TLS modunda çalışıyorsa "açık". Aksi takdirde, boş bir dize. |
| uri_path                  | Web istemcisinin erişmek istediği konaktaki belirli kaynağı tanımlar. Bu, istek URI 'sinin bağımsız değişkenler olmadan bölümüdür. Örnek: istekte `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` uri_path değer olacaktır `/article.aspx` |

### <a name="mutual-authentication-server-variables-preview"></a>Karşılıklı kimlik doğrulama sunucusu değişkenleri (Önizleme)

Application Gateway, karşılıklı kimlik doğrulama senaryolarında aşağıdaki sunucu değişkenlerini destekler. Bu sunucu değişkenlerini diğer sunucu değişkenleriyle aynı şekilde kullanın. 

|   Değişken adı    |                   Description                                           |
| ------------------------- | ------------------------------------------------------------ |
| client_certificate        | Kurulan bir SSL bağlantısı için pek biçimindeki istemci sertifikası. |
| client_certificate_end_date| İstemci sertifikasının bitiş tarihi. |
| client_certificate_fingerprint| Kurulan bir SSL bağlantısı için istemci sertifikasının SHA1 parmak izi. |
| client_certificate_issuer | Kurulan bir SSL bağlantısı için istemci sertifikasının "verenin DN 'si" dizesi. |
| client_certificate_serial | Kurulan bir SSL bağlantısı için istemci sertifikasının seri numarası.  |
| client_certificate_start_date| İstemci sertifikasının başlangıç tarihi. |
| client_certificate_subject| Kurulan bir SSL bağlantısı için istemci sertifikasının "konu DN" dizesi. |
| client_certificate_verification| İstemci sertifikası doğrulamasının sonucu: *başarılı*, *başarısız: <reason>*, ya da bir sertifika yoksa *none* . | 

## <a name="rewrite-configuration"></a>Yeniden yazma yapılandırması

Bir yeniden yazma kuralı yapılandırmak için bir yeniden yazma kuralı kümesi oluşturmanız ve buna yeniden yazma kuralı yapılandırmasını eklemeniz gerekir.

Bir yeniden yazma kuralı kümesi şunları içerir:

* **İstek yönlendirme kuralı ilişkilendirmesi:** Yeniden yazma yapılandırması, kaynak dinleyicisiyle yönlendirme kuralı üzerinden ilişkilendirilir. Temel bir yönlendirme kuralı kullandığınızda, yeniden yazma yapılandırması bir kaynak dinleyicisi ile ilişkilendirilir ve genel bir üst bilgi yeniden yazma işlemi olur. Yol tabanlı bir yönlendirme kuralı kullandığınızda, URL yolu eşlemesinde yeniden yazma yapılandırması tanımlanmıştır. Bu durumda, yalnızca bir sitenin belirli yol alanı için geçerlidir. Birden çok yeniden yazma kümesi oluşturabilir ve her bir yeniden yazma kümesini birden çok dinleyiciyle uygulayabilirsiniz. Ancak belirli bir dinleyiciye yalnızca bir yeniden yazma kümesi uygulayabilirsiniz.

* **Yeniden yazma koşulu**: isteğe bağlı bir yapılandırmadır. Yeniden yazma koşulları, HTTP (S) isteklerinin ve yanıtlarının içeriğini değerlendirir. HTTP (S) isteği veya yanıtı yeniden yazma koşuluyla eşleşiyorsa, yeniden yazma eylemi gerçekleşir. Birden fazla koşulu bir eylemle ilişkilendirirseniz, eylem yalnızca tüm koşullar karşılandığında oluşur. Diğer bir deyişle, işlem mantıksal ve işlemdir.

* **Yeniden yazma türü**: kullanılabilir 3 tür yeniden yazma vardır:
   * İstek üst bilgilerini yeniden yazma 
   * Yanıt üst bilgilerini yeniden yazma
   * URL bileşenlerini yeniden yazma
      * **URL yolu**: yolun yeniden yazılması için gereken değer. 
      * **URL sorgu dizesi**: sorgu dizesinin yeniden yazılması için gereken değer. 
      * **Yol haritasını yeniden değerlendir**: URL yol eşlemesinin yeniden değerlendirilip değerlendirilmeyeceğini anlamak için kullanılır. İşaretlenmediyse, URL yolu eşlemesindeki yol düzeniyle eşleştirmek için özgün URL yolu kullanılacaktır. True olarak ayarlanırsa, URL yolu eşlemesi yeniden yazan yol ile eşleştirmeyi denetlemek için yeniden değerlendirilir. Bu anahtarı etkinleştirmek, isteği farklı bir arka uç havuzuna yeniden yazmaya yönlendirmenize yardımcı olur.

## <a name="rewrite-configuration-common-pitfalls"></a>Yapılandırma ortak sınırları yeniden yaz

* Temel istek yönlendirme kuralları için ' yol haritasını yeniden değerlendir ' kullanımına izin verilmez. Bu, temel bir yönlendirme kuralı için sonsuz değerlendirme döngüsünü önlemektir.

* Yol tabanlı yönlendirme kuralı için sonsuz değerlendirme döngüsünü engellemek üzere yol tabanlı yönlendirme kuralları için ' yeniden değerlendir yol haritası ' etkin olmayan en az 1 Koşullu yeniden yazma kuralı veya 1 yeniden yazma kuralı olması gerekir.

* Bir döngünün istemci girdilerine göre dinamik olarak oluşturulması durumunda gelen istekler 500 hata koduyla sonlandırılır. Application Gateway, böyle bir senaryoda herhangi bir düşüş olmadan diğer isteklere hizmet vermeye devam edecektir.

### <a name="using-url-rewrite-or-host-header-rewrite-with-web-application-firewall-waf_v2-sku"></a>Web uygulaması güvenlik duvarı (WAF_v2 SKU) ile URL yeniden yazma veya konak üstbilgi yeniden yazma kullanma

URL yeniden yazma veya konak üstbilgi yeniden yazma 'yı yapılandırdığınızda, WAF değerlendirmesi istek üstbilgisinde veya URL parametrelerinde değiştirildikten sonra olur (yeniden yazma sonrası). Application Gateway URL yeniden yazma veya ana bilgisayar üst bilgisi yeniden yazma yapılandırmasını kaldırdığınızda,, üst bilgi yeniden yazma işleminden önce WAF değerlendirmesi yapılır (önceden yazma). Bu sıra, WAF kurallarının arka uç havuzunuz tarafından alınacak son isteğe uygulanmasını sağlar.

Örneğin, üstbilgi için aşağıdaki üst bilgi yeniden yazma kuralına sahip olduğunu varsayalım `"Accept" : "text/html"` . üst bilgi değeri `"Accept"` öğesine eşitse `"text/html"` , değerini olarak yeniden yazın `"image/png"` .

Burada, yalnızca üst bilgi yeniden yazma işlemi yapılandırıldığında WAF değerlendirmesi tarihinde yapılır `"Accept" : "text/html"` . Ancak URL yeniden yazma veya ana bilgisayar üst bilgisi yeniden yazma 'yı yapılandırdığınızda, WAF değerlendirmesi tarihinde yapılır `"Accept" : "image/png"` .

>[!NOTE]
> WAF Application Gateway CPU kullanımında küçük bir artışa neden olması beklenen URL yeniden yazma işlemleri beklenmektedir. WAF Application Gateway URL yeniden yazma kurallarını etkinleştirdikten sonra kısa bir süre boyunca [CPU kullanım ölçümünü](high-traffic-support.md) izlemeniz önerilir.

### <a name="common-scenarios-for-header-rewrite"></a>Üst bilgi yeniden yazma için genel senaryolar

#### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Bağlantı noktası bilgilerini X-Iletilmiş-for üst bilgisinden kaldır

Application Gateway, istekleri arka uca iletmeden önce tüm isteklere X-Iletilmiş bir üst bilgi ekler. Bu üst bilgi, IP bağlantı noktalarının virgülle ayrılmış listesidir. Arka uç sunucularının yalnızca IP adreslerini içermesi için üstbilgilere ihtiyacı olan senaryolar olabilir. Bağlantı noktası bilgilerini X-Iletilmiş-for üst bilgisinden kaldırmak için üstbilgi yeniden yazma kullanabilirsiniz. Bunu yapmanın bir yolu, üst bilgiyi add_x_forwarded_for_proxy sunucu değişkenine ayarlamaya yönelik bir yoldur. Alternatif olarak, client_ip değişkenini de kullanabilirsiniz:

![Bağlantı noktasını kaldır](./media/rewrite-http-headers-url/remove-port.png)


#### <a name="modify-a-redirection-url"></a>Yeniden yönlendirme URL 'sini değiştirme

Bir arka uç uygulaması yeniden yönlendirme yanıtı gönderdiğinde, istemciyi arka uç uygulaması tarafından belirtilene göre farklı bir URL 'ye yönlendirmek isteyebilirsiniz. Örneğin, bir uygulama hizmeti bir uygulama ağ geçidinin arkasında barındırılıyorsa ve istemcinin göreli yoluna yeniden yönlendirme yapması için bunu yapmak isteyebilirsiniz. (Örneğin, contoso.azurewebsites.net/path1 öğesinden contoso.azurewebsites.net/path2 'e yeniden yönlendirme.)

App Service çok kiracılı bir hizmet olduğundan, isteği doğru uç noktaya yönlendirmek için istekteki ana bilgisayar üst bilgisini kullanır. Uygulama Hizmetleri \* , Application Gateway 'in etki alanı adından (contoso.azurewebsites.net contoso.com) farklı olan varsayılan bir. azurewebsites.net etki alanı adına sahiptir (deyin). İstemciden gelen özgün istek ana bilgisayar adı olarak uygulama ağ geçidinin etki alanı adına (contoso.com) sahip olduğundan, Application Gateway ana bilgisayar adını contoso.azurewebsites.net olarak değiştirir. Bu değişiklik, App Service 'in isteği doğru uç noktaya yönlendirebilmesi için yapar.

App Service bir yeniden yönlendirme yanıtı gönderdiğinde, uygulamanın konum üstbilgisindeki ana bilgisayar adını uygulama ağ geçidinden aldığı istekte olduğu gibi kullanır. Bu nedenle, istemci, `contoso.azurewebsites.net/path2` uygulama ağ geçidi () boyunca değil, isteği doğrudan bir hale getirir `contoso.com/path2` . Application Gateway 'i atlamak istenmez.

Konum üstbilgisindeki ana bilgisayar adını Application Gateway 'in etki alanı adına ayarlayarak bu sorunu çözebilirsiniz.

Burada, ana bilgisayar adını değiştirme adımları verilmiştir:

1. Yanıttaki konum üstbilgisinin azurewebsites.net içerip içermeyeceğini değerlendiren bir koşul içeren bir yeniden yazma kuralı oluşturun. Kalıbı girin `(https?):\/\/.*azurewebsites\.net(.*)$` .
2. Uygulama ağ geçidinin ana bilgisayar adına sahip olması için konum başlığını yeniden yazmak üzere bir eylem gerçekleştirin. Bunu `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` , üst bilgi değeri olarak girerek yapın. Alternatif olarak, `host` ana bilgisayar adını özgün istekle eşleşecek şekilde ayarlamak için sunucu değişkenini de kullanabilirsiniz.

![Konum üst bilgisini Değiştir](./media/rewrite-http-headers-url/app-service-redirection.png)

#### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Güvenlik açıklarını engellemek için güvenlik HTTP üst bilgilerini uygulayın

Uygulama yanıtında gerekli üst bilgileri uygulayarak çeşitli güvenlik açıklarını giderebilirsiniz. Bu güvenlik üstbilgileri, X-XSS-Protection, katı aktarım güvenliği ve Content-Security-Policy ' i içerir. Tüm yanıtlar için bu üst bilgileri ayarlamak üzere Application Gateway kullanabilirsiniz.

![Güvenlik üst bilgisi](./media/rewrite-http-headers-url/security-header.png)

### <a name="delete-unwanted-headers"></a>İstenmeyen üstbilgileri Sil

Bir HTTP yanıtından hassas bilgileri açığa çıkarmak için üstbilgileri kaldırmak isteyebilirsiniz. Örneğin, arka uç sunucu adı, işletim sistemi veya kitaplık ayrıntıları gibi bilgileri kaldırmak isteyebilirsiniz. Bu üst bilgileri kaldırmak için Application Gateway kullanabilirsiniz:

![Üst bilgi siliniyor](./media/rewrite-http-headers-url/remove-headers.png)

#### <a name="check-for-the-presence-of-a-header"></a>Üstbilginin varolup olmadığını denetle

Bir üst bilgi veya sunucu değişkeni varlığı için bir HTTP isteği veya yanıt üst bilgisini değerlendirebilirsiniz. Bu değerlendirme yalnızca belirli bir başlık mevcut olduğunda bir üstbilgi yeniden yazma işlemi gerçekleştirmek istediğinizde faydalıdır.

![Üst bilgi bulunup bulunmadığını denetleme](./media/rewrite-http-headers-url/check-presence.png)

### <a name="common-scenarios-for-url-rewrite"></a>URL yeniden yazma için genel senaryolar

#### <a name="parameter-based-path-selection"></a>Parametre tabanlı yol seçimi

Arka uç havuzunu bir üstbilginin değerine, URL 'nin bir bölümüne veya istekteki sorgu dizesine göre seçmek istediğiniz senaryoları gerçekleştirmek için, URL yeniden yazma özelliği ve yol tabanlı yönlendirme birleşimini kullanabilirsiniz. Örneğin, bir alışveriş web siteniz varsa ve ürün kategorisi URL 'de sorgu dizesi olarak geçirilirse ve isteği sorgu dizesine göre arka uca dolaştırmak istiyorsanız:

**Adım:**  Aşağıdaki görüntüde gösterildiği gibi bir yol haritası oluşturun

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-1.png" alt-text="URL yeniden yazma senaryosu 1-1.":::

**2. adım (a):** 3 yeniden yazma kuralına sahip bir yeniden yazma kümesi oluşturun: 

* İlk kural, *category = showes* için *query_string* değişkenini denetleyen ve URL yolunu/*listing1* 'e yeniden veren ve **yol haritasını yeniden değerlendiren** bir eyleme sahiptir

* İkinci kural, *category = torlar* için *query_string* değişkenini denetleyen ve URL yolunu/*listing2* 'e yeniden veren ve **yol haritasını yeniden değerlendiren** bir eyleme sahiptir

* Üçüncü kural, *category = aksesuarları* için *query_string* değişkenini denetleyen ve URL yolunu/*listing3* 'e yeniden veren ve **yol haritasını yeniden değerlendiren** bir eyleme sahip olan bir koşula sahiptir

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-2.png" alt-text="URL yeniden yazma senaryosu 1-2.":::

 

**2. adım (b):** Bu yeniden yazma kümesini yukarıdaki yol tabanlı kuralın varsayılan yoluyla ilişkilendirin

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-3.png" alt-text="URL yeniden yazma senaryosu 1-3.":::

Artık Kullanıcı *contoso.com/Listing?category=any* isterse, yol haritadaki yol desenlerinden hiçbiri (/listing1,/listing2,/listing3) eşleşmesinden bu yana varsayılan yol ile eşleştirilir. Yukarıdaki yeniden yazma kümesini bu yol ile ilişkilendirdikten sonra, bu yeniden yazma kümesi değerlendirilecek. Sorgu dizesi bu yeniden yazma kümesindeki 3 yeniden yazma kuralındaki koşulla eşleşmediğinden, yeniden yazma eylemi gerçekleşmeyecek ve bu nedenle, istek varsayılan yol ( *GenericList*) ile ilişkili arka uca değişmeden yönlendirilir.

Kullanıcı *contoso.com/Listing?category=Shoes* isterse, varsayılan yol eşleştirilir. Ancak, bu durumda, ilk kuraldaki koşul eşleşmeyecektir ve bu nedenle, koşulla ilişkili eylem, URL yolunu/*listing1*  olarak yeniden yazıp yol eşlemesini yeniden değerlendilecektir. Path-Map yeniden değerlendirildiğinde, istek artık, */listing1* düzeniyle ilişkili yol ile eşleşir ve Istek, Showeslistbackendpool olan bu Düzenle ilişkili olan arka uca yönlendirilir.

>[!NOTE]
>Bu senaryo, tanımlanan koşullara bağlı olarak herhangi bir üst bilgi veya tanımlama bilgisi değeri, URL yolu, sorgu dizesi veya sunucu değişkenine genişletilebilir ve temelde istekleri bu koşullara göre yönlendirmenizi sağlar.

#### <a name="rewrite-query-string-parameters-based-on-the-url"></a>Sorgu dizesi parametrelerini URL 'ye göre yeniden yaz

Kullanıcı görünür bağlantısının basit ve okunabilir olması gereken bir alışveriş web sitesinin senaryosunu göz önünde bulundurun, ancak arka uç sunucusunun doğru içeriği göstermesi için sorgu dizesi parametrelerine ihtiyacı vardır.

Bu durumda Application Gateway URL 'den parametreleri yakalayabilir ve URL 'lerden sorgu dizesi anahtar-değer çiftleri ekleyebilir. Örneğin, kullanıcının için yeniden yazmak istediğini varsayalım, `https://www.contoso.com/fashion/shirts` `https://www.contoso.com/buy.aspx?category=fashion&product=shirts` Bu, aşağıdaki URL yeniden yazma yapılandırması aracılığıyla elde edilebilir.

**Koşul** -sunucu değişkeni `uri_path` , modele eşitse `/(.+)/(.+)`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-1.png" alt-text="URL yeniden yazma senaryosu 2-1.":::

**Eylem** -URL yolunu `buy.aspx` ve sorgu dizesini ayarla `category={var_uri_path_1}&product={var_uri_path_2}`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-2.png" alt-text="URL yeniden yazma senaryosu 2-2.":::

Yukarıda açıklanan senaryoya ulaşmak için adım adım kılavuz için bkz. [Azure Portal kullanarak URL 'yi Application Gateway yeniden yazma](rewrite-url-portal.md)

### <a name="url-rewrite-vs-url-redirect"></a>URL yeniden yazma vs URL yönlendirmesi

URL yeniden yazma durumunda, istek arka uca gönderilmeden önce URL 'YI yeniden yazar Application Gateway. Bu, değişiklikler kullanıcıdan gizlendiğinden kullanıcıların tarayıcıda gördüklerinizi değiştirmeyecektir.

URL yeniden yönlendirme durumunda, Application Gateway istemciye yeni URL ile yeniden yönlendirme yanıtı gönderir. Bu durumda, istemcinin isteğini yeniden yönlendirmeye sunulan yeni URL 'ye yeniden göndermesi gerekir. Kullanıcının tarayıcıda gördüğü URL yeni URL 'ye güncelleştirilecek.

:::image type="content" source="./media/rewrite-http-headers-url/url-rewrite-vs-redirect.png" alt-text="Vs yeniden yönlendirmeyi yeniden yazın.":::

## <a name="limitations"></a>Sınırlamalar

- Bir yanıtta aynı ada sahip birden fazla üst bilgi varsa, bu üst bilgilerden birinin değerini yeniden yazmak yanıttaki diğer üstbilgilerin bırakılmasına neden olur. Yanıt içinde birden fazla Set-Cookie üst bilgisine sahip olabileceğinizden bu durum genellikle Set-Cookie üst bilgisinde meydana gelebilir. Bu tür bir senaryo, uygulama ağ geçidi ile uygulama hizmeti kullanırken ve uygulama ağ geçidinde tanımlama bilgisi tabanlı oturum benzeşimi yapılandırmış olduğunuz bir senaryodur. Bu durumda, yanıt iki Set-Cookie üst bilgi içerir: App Service tarafından kullanılan, örneğin: `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` ve uygulama ağ geçidi benzeşimi için bir diğeri (örneğin,) `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/` . Bu senaryodaki Set-Cookie başlıklarından birini yeniden yazmak, diğer Set-Cookie üstbilgisinin yanıttan kaldırılmasına neden olabilir.
- Uygulama ağ geçidi istekleri yeniden yönlendirmek veya özel bir hata sayfası göstermek üzere yapılandırıldığında, yeniden yazar desteklenmez.
- Üst bilgi adları, [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27)' de tanımlanan alfasayısal karakterleri ve belirli sembolleri içerebilir. Şu anda üstbilgi adlarında alt çizgi ( \_ ) özel karakterini desteklemiyoruz.
- Bağlantı ve yükseltme üstbilgileri yeniden yazılamıyor

## <a name="next-steps"></a>Sonraki adımlar

- [Azure portal kullanarak HTTP üstbilgilerini Application Gateway yeniden yazmayı öğrenin](rewrite-http-headers-portal.md)
- [Azure portal kullanarak URL 'YI Application Gateway yeniden yazmayı öğrenin](rewrite-url-portal.md)
