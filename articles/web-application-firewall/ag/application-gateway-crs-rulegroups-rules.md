---
title: Yukarı, kural grupları ve kuralları
titleSuffix: Azure Web Application Firewall
description: Bu sayfa, Web uygulaması güvenlik duvarı ile ilgili bilgi grupları ve kuralları hakkında bilgiler sağlar.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 0303f09e5e704a18576bf50d1f00007f7f86f320
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075368"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>Web uygulaması güvenlik duvarı, kural grupları ve kuralları

Application Gateway Web uygulaması güvenlik duvarı (WAF), Web uygulamalarını ortak güvenlik açıklarına ve açıklardan yararlanan korur. Bu, OWASP Core kural kümelerine 3,1, 3,0 veya 2.2.9 göre tanımlanan kurallar aracılığıyla yapılır. Bu kurallar kural temelinde devre dışı bırakılabilir. Bu makale, sunulan geçerli kuralları ve kural kümelerini içerir.

## <a name="core-rule-sets"></a>Çekirdek kural kümeleri

Application Gateway WAF, varsayılan olarak 3,0 ile önceden yapılandırılmış olarak gelir. Ancak bunun yerine, 2.2.9 3,1 veya yukarı kullanmayı seçebilirsiniz. Bu 3,1, Java bulaşmalarına karşı savunma, karşıya dosya yükleme denetimleri, sabit hatalı pozitif sonuçlar ve daha fazlası için yeni kural kümeleri sunar. Bu 3,0, 2.2.9 ile karşılaştırıldığında azaltılmış hatalı pozitif sonuçlar sunar. Ayrıca, [kuralları gereksinimlerinize uyacak şekilde özelleştirebilirsiniz](application-gateway-customize-waf-rules-portal.md).

> [!div class="mx-imgBorder"]
> kuralları ![yönetir](../media/application-gateway-crs-rulegroups-rules/managed-rules-01.png)

WAF aşağıdaki Web açıklarına karşı koruma sağlar:

- SQL ekleme saldırıları
- Siteler arası betik saldırıları
- Komut ekleme, HTTP isteği, HTTP yanıtı bölme ve uzak dosya ekleme gibi diğer yaygın saldırılar
- HTTP protokol ihlalleri
- Eksik konak Kullanıcı Aracısı ve kabul başlıkları gibi HTTP protokol bozuklukları
- Botlar, gezginler ve tarayıcılar
- Yaygın uygulama yapılandırması hataları (örneğin, Apache ve IIS)

### <a name="owasp-crs-31"></a>OWASP YUKARı 3,1

Aşağıdaki tabloda gösterildiği gibi, bu 3,1 ' de 13 kural grubu bulunur. Her grup, devre dışı bırakılabilen birden çok kural içerir.

|Kural grubu|Açıklama|
|---|---|
|**[Genel](#general-31)**|Genel Grup|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-31)**|Kilitleme yöntemleri (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-31)**|Bağlantı noktası ve ortam tarayıcımalarına karşı koruma|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-31)**|Protokol ve kodlama sorunlarına karşı koruma|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-31)**|Üst bilgi ekleme, istek tepki verme ve yanıt bölme için koruma|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-31)**|Dosya ve yol saldırılarına karşı koruma|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-31)**|Uzak dosya ekleme (RFI) saldırılarına karşı koruma|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-31)**|Yeniden koru uzaktan kod yürütme saldırıları|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-31)**|PHP ekleme saldırılarına karşı koruma|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-31)**|Siteler arası betik saldırılarına karşı koruma|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-31)**|SQL ekleme saldırılarına karşı koruma|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-31)**|Oturum düzeltme saldırılarına karşı koruma|
|**[ISTEK-944-UYGULAMA-SALDıRı-OTURUM-JAVA](#crs944-31)**|JAVA saldırılarına karşı koruma|

### <a name="owasp-crs-30"></a>OWASP YUKARı 3,0

Aşağıdaki tabloda gösterildiği gibi, bu 3,0, 12 kural grubunu içerir. Her grup, devre dışı bırakılabilen birden çok kural içerir.

|Kural grubu|Açıklama|
|---|---|
|**[Genel](#general-30)**|Genel Grup|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-30)**|Kilitleme yöntemleri (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-30)**|Bağlantı noktası ve ortam tarayıcımalarına karşı koruma|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-30)**|Protokol ve kodlama sorunlarına karşı koruma|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-30)**|Üst bilgi ekleme, istek tepki verme ve yanıt bölme için koruma|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-30)**|Dosya ve yol saldırılarına karşı koruma|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-30)**|Uzak dosya ekleme (RFI) saldırılarına karşı koruma|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-30)**|Yeniden koru uzaktan kod yürütme saldırıları|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-30)**|PHP ekleme saldırılarına karşı koruma|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-30)**|Siteler arası betik saldırılarına karşı koruma|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-30)**|SQL ekleme saldırılarına karşı koruma|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-30)**|Oturum düzeltme saldırılarına karşı koruma|

### <a name="owasp-crs-229"></a>OWASP, 2.2.9

2\.2.9, aşağıdaki tabloda gösterildiği gibi 10 kural grubunu içerir. Her grup, devre dışı bırakılabilen birden çok kural içerir.

|Kural grubu|Açıklama|
|---|---|
|**[crs_20_protocol_violations](#crs20)**|Protokol ihlallerine karşı koruma (geçersiz karakterler veya istek gövdesi ile al)|
|**[crs_21_protocol_anomalies](#crs21)**|Yanlış başlık bilgilerine karşı koruma|
|**[crs_23_request_limits](#crs23)**|Sınırlamaları aşan bağımsız değişkenlere veya dosyalara karşı koruma|
|**[crs_30_http_policy](#crs30)**|Kısıtlanmış yöntemlere, üstbilgilere ve dosya türlerine karşı koruma|
|**[crs_35_bad_robots](#crs35)**|Web gezginlerine ve tarayıcımalarına karşı koruma|
|**[crs_40_generic_attacks](#crs40)**|Genel saldırılara karşı koruma (oturum armatürü, uzak dosya ekleme ve PHP ekleme gibi)|
|**[crs_41_sql_injection_attacks](#crs41sql)**|SQL ekleme saldırılarına karşı koruma|
|**[crs_41_xss_attacks](#crs41xss)**|Siteler arası betik saldırılarına karşı koruma|
|**[crs_42_tight_security](#crs42)**|Yol çapraz geçiş saldırılarına karşı koruma|
|**[crs_45_trojans](#crs45)**|Arka kapı Truva atlarına karşı koruma|

Application Gateway üzerinde Web uygulaması güvenlik duvarı kullanılırken aşağıdaki kural grupları ve kuralları kullanılabilir.

# <a name="owasp-31tabowasp31"></a>[OWASP 3,1](#tab/owasp31)

## <a name="owasp31"></a>Kural kümeleri

### <a name="general-31"></a> <p x-ms-format-detection="none">Genel</p>

|RuleId|Açıklama|
|---|---|
|200004|Olası çok parçalı eşleşmeyen sınır.|

### <a name="crs911-31"></a> <p x-ms-format-detection="none">ISTEK-911-YÖNTEM-ZORLAMA</p>

|RuleId|Açıklama|
|---|---|
|911100|Yönteme ilke tarafından izin verilmiyor|


### <a name="crs913-31"></a> <p x-ms-format-detection="none">ISTEK-913-TARAYıCı-ALGıLAMA</p>

|RuleId|Açıklama|
|---|---|
|913100|Güvenlik tarayıcısıyla ilişkili Kullanıcı Aracısı bulundu|
|913101|Betik/genel HTTP istemcisiyle ilişkili Kullanıcı Aracısı bulundu|
|913102|Web Gezgini/bot ile ilişkili Kullanıcı Aracısı bulundu|
|913110|Güvenlik tarayıcısıyla ilişkili istek üst bilgisi bulundu|
|913120|Güvenlik tarayıcısıyla ilişkili istek dosya adı/bağımsız değişken bulundu|


### <a name="crs920-31"></a> <p x-ms-format-detection="none">ISTEK-920-PROTOKOL-ZORLAMA</p>

|RuleId|Açıklama|
|---|---|
|920100|Geçersiz HTTP Istek satırı|
|920120|Çok parçalı/form-veri atlama denendi|
|920121|Çok parçalı/form-veri atlama denendi|
|920130|İstek gövdesi ayrıştırılamadı.|
|920140|Çok parçalı istek gövdesi kesin doğrulama başarısız oldu|
|920160|Content-Length HTTP üst bilgisi sayısal değil.|
|920170|Gövde Içerikli GET veya HEAD Isteği.|
|920171|Aktarım kodlaması ile al veya HEAD Isteği.|
|920180|POST isteği için Content-Length üst bilgisi eksik.|
|920190|Range = geçersiz son bayt değeri.|
|920200|Range = çok fazla alan (6 veya daha fazla)|
|920201|Range = PDF isteği için çok fazla alan (35 veya daha fazla)|
|920202|Range = PDF isteği için çok fazla alan (6 veya daha fazla)|
|920210|Birden çok/çakışan bağlantı üst bilgisi verisi bulundu.|
|920220|URL kodlaması kötüye kullanımı saldırı girişimi|
|920230|Birden çok URL kodlaması algılandı|
|920240|URL kodlaması kötüye kullanımı saldırı girişimi|
|920250|UTF8 kötüye kullanımı kötüye saldırı girişimi|
|920260|Unicode tam/yarı genişlik kötüye kullanımı saldırı girişimi|
|920270|İstekte geçersiz karakter (null karakter)|
|920271|İstekte geçersiz karakter (yazdırılamayan karakterler)|
|920272|İstekteki karakter geçersiz (ASCII 127 ' ün altında yazdırılabilir karakterlerin dışında)|
|920273|İstekte geçersiz karakter (çok katı küme dışında)|
|920274|İstek üst bilgilerinde geçersiz karakter (çok katı küme dışında)|
|920280|İstekte bir konak üstbilgisi eksik|
|920290|Boş ana bilgisayar üstbilgisi|
|920300|İstekte bir Accept üst bilgisi eksik|
|920310|İstekte boş bir Accept üst bilgisi yok|
|920311|İstekte boş bir Accept üst bilgisi yok|
|920320|Eksik Kullanıcı Aracısı üstbilgisi|
|920330|Boş Kullanıcı Aracısı üstbilgisi|
|920340|Içerik Içeren istek ancak Içerik türü üstbilgisi eksik|
|920341|İçerik içeren istek Content-Type üst bilgisi gerektiriyor|
|920350|Ana bilgisayar üst bilgisi sayısal bir IP adresidir|
|920360|Bağımsız değişken adı çok uzun|
|920370|Bağımsız değişken değeri çok uzun|
|920380|İstekte çok fazla bağımsız değişken var|
|920390|Toplam bağımsız değişken boyutu aşıldı|
|920400|Karşıya yüklenen dosya boyutu çok büyük|
|920410|Karşıya yüklenen toplam dosya boyutu çok büyük|
|920420|İlke tarafından istek içerik türüne izin verilmiyor|
|920430|İlke tarafından HTTP protokol sürümüne izin verilmiyor|
|920440|URL dosya uzantısı ilkeyle kısıtlıdır|
|920450|HTTP üstbilgisi ilkeyle kısıtlıdır (% @ {MATCHED_VAR})|
|920460|Olağan dışı kaçış karakterleri|
|920470|Geçersiz Content-Type üst bilgisi|
|920480|Content-Type üst bilgisinde karakter kümesi parametresini kısıtla|

### <a name="crs921-31"></a> <p x-ms-format-detection="none">ISTEK-921-PROTOKOL-SALDıRı</p>

|RuleId|Açıklama|
|---|---|
|921110|HTTP Isteği, Uytoze saldırısı|
|921120|HTTP yanıt bölme saldırısı|
|921130|HTTP yanıt bölme saldırısı|
|921140|Üst bilgiler aracılığıyla HTTP üst bilgisi ekleme saldırısı|
|921150|Yük aracılığıyla HTTP üst bilgisi ekleme saldırısı (CR/LF algılandı)|
|921151|Yük aracılığıyla HTTP üst bilgisi ekleme saldırısı (CR/LF algılandı)|
|921160|Yük (CR/LF ve üstbilgi-Name algılandı) aracılığıyla HTTP üst bilgisi ekleme saldırısı|
|921170|HTTP parametre Kirlilii|
|921180|HTTP parametre Kirlilii (% {TX. 1})|

### <a name="crs930-31"></a> <p x-ms-format-detection="none">ISTEK-930-UYGULAMA SALDıRıSı-LFI</p>

|RuleId|Açıklama|
|---|---|
|930100|Yol çapraz geçiş saldırısı (/.. /)|
|930110|Yol çapraz geçiş saldırısı (/.. /)|
|930120|İşletim sistemi dosyası erişim denemesi|
|930130|Kısıtlanmış dosya erişim denemesi|

### <a name="crs931-31"></a> <p x-ms-format-detection="none">ISTEK-931-UYGULAMA-SALDıRı-RFI</p>

|RuleId|Açıklama|
|---|---|
|931100|Olası uzak dosya ekleme (RFI) saldırısı = IP adresini kullanan URL parametresi|
|931110|Olası uzak dosya ekleme (RFI) saldırısı = ortak RFI güvenlik açığı parametre adı kullanılan w/URL yükü|
|931120|Olası uzak dosya ekleme (RFI) saldırısı = URL yükü kullanıldı w/sondaki soru Işareti karakteri (?)|
|931130|Olası uzak dosya ekleme (RFI) saldırısı = etki alanı başvurusu/bağlantı|

### <a name="crs932-31"></a> <p x-ms-format-detection="none">ISTEK-932-UYGULAMA-SALDıRı-RCE</p>

|RuleId|Açıklama|
|---|---|
|932100|Uzaktan komut yürütme: Unix komut ekleme|
|932105|Uzaktan komut yürütme: Unix komut ekleme|
|932106|Uzaktan komut yürütme: Unix komut ekleme|
|932110|Uzak komut yürütme: Windows komut ekleme|
|932115|Uzak komut yürütme: Windows komut ekleme|
|932120|Uzak komut yürütme = Windows PowerShell komutu bulundu|
|932130|Uzak komut yürütme = Unix kabuğu Ifadesi bulundu|
|932140|Uzak komut yürütme = Windows FOR/IF komutu bulundu|
|932160|Uzak komut yürütme = UNIX kabuk kodu bulundu|
|932170|Uzak komut yürütme = Shellşok (CVE-2014-6271)|
|932171|Uzak komut yürütme = Shellşok (CVE-2014-6271)|
|932180|Kısıtlanmış dosya yükleme denemesi|
|932190|Uzak komut yürütme: joker karakter atlama tekniği denemesi|

### <a name="crs933-31"></a> <p x-ms-format-detection="none">ISTEK-933-UYGULAMA-SALDıRı-PHP</p>

|RuleId|Açıklama|
|---|---|
|933100|PHP ekleme saldırısı = açma/kapatma etiketi bulundu|
|933110|PHP ekleme saldırısı = PHP betik dosyası karşıya yüklemesi bulundu|
|933111|PHP ekleme saldırısı: PHP betik dosyası yüklemesi bulundu|
|933120|PHP ekleme saldırısı = yapılandırma yönergesi bulundu|
|933130|PHP ekleme saldırısı = değişkenler bulundu|
|933131|PHP ekleme saldırısı: değişkenler bulundu|
|933140|PHP ekleme saldırısı: g/ç akışı bulundu|
|933150|PHP ekleme saldırısı = yüksek riskli PHP Işlev adı bulundu|
|933151|PHP ekleme saldırısı: orta riskli PHP Işlev adı bulundu|
|933160|PHP ekleme saldırısı = yüksek riskli PHP Işlev çağrısı bulundu|
|933161|PHP ekleme saldırısı: düşük değerli PHP Işlev çağrısı bulundu|
|933170|PHP ekleme saldırısı: serileştirilmiş nesne ekleme|
|933180|PHP ekleme saldırısı = değişken Işlev çağrısı bulundu|
|933190|PHP ekleme saldırısı: PHP kapatma etiketi bulundu|

### <a name="crs941-31"></a> <p x-ms-format-detection="none">ISTEK-941-UYGULAMA-SALDıRı-XSS</p>

|RuleId|Açıklama|
|---|---|
|941100|Libinjection aracılığıyla algılanan XSS saldırısı|
|941101|Libinjection aracılığıyla algılanan XSS saldırısı|
|941110|XSS filtresi-Kategori 1 = komut dosyası etiketi vektörü|
|941130|XSS filtresi-kategori 3 = öznitelik vektörü|
|941140|XSS filtresi-kategori 4 = JavaScript URI vektörü|
|941150|XSS filtresi-kategori 5 = Izin verilmeyen HTML öznitelikleri|
|941160|NoScript XSS ınjectionchecker: HTML ekleme|
|941170|NoScript XSS ınjectionchecker: öznitelik ekleme|
|941180|Düğüm-Doğrulayıcı kara anahtar sözcükleri|
|941190|Stil sayfalarını kullanarak XSS|
|941200|VML çerçevelerini kullanarak XSS|
|941210|Karıştırılmış JavaScript kullanılarak XSS|
|941220|Karıştırılmış VB betiği kullanılarak XSS|
|941230|' Embed ' etiketi kullanılarak XSS|
|941240|' Import ' veya ' Implementation ' özniteliği kullanılarak XSS|
|941250|IE XSS filtreleri-saldırı algılandı|
|941260|' Meta ' etiketi kullanılarak XSS|
|941270|' Link ' href kullanılarak XSS|
|941280|' Base ' etiketi kullanılarak XSS|
|941290|' Applet ' etiketi kullanılarak XSS|
|941300|' Object ' etiketi kullanılarak XSS|
|941310|ABD-ASCII hatalı biçimlendirilmiş kodlama XSS filtresi-saldırı algılandı.|
|941320|Olası XSS saldırısı algılandı-HTML etiketi Işleyicisi|
|941330|IE XSS filtreleri-saldırı algılandı.|
|941340|IE XSS filtreleri-saldırı algılandı.|
|941350|UTF-7 kodlama IE XSS-Saldırı algılandı.|


### <a name="crs942-31"></a> <p x-ms-format-detection="none">ISTEK-942-UYGULAMA-SALDıRı-SQLI</p>

|RuleId|Açıklama|
|---|---|
|942100|Libinjection aracılığıyla SQL ekleme saldırısı algılandı|
|942110|SQL ekleme saldırısı: ortak ekleme testi algılandı|
|942130|SQL ekleme saldırısı: SQL tautology algılandı.|
|942140|SQL ekleme saldırısı = ortak VERITABANı adları algılandı|
|942150|SQL ekleme saldırısı|
|942160|Sleep () veya kıyaslama () kullanarak görünmeyen SQLi testleri algılar.|
|942170|Koşullu sorgular dahil SQL kıyaslama ve uyku ekleme girişimlerini algılar|
|942180|Temel SQL kimlik doğrulaması atlama girişimlerini algılar 1/3|
|942190|MSSQL kod yürütmeyi ve bilgi toplama girişimlerini algılar|
|942200|MySQL yorumunu algılar-/Space-belirsizleştirilmiş ınjecve backtick sonlandırma|
|942210|Zincirleme SQL ekleme girişimlerini algılar 1/2|
|942220|Tamsayı taşması saldırıları aranıyor, bunlar 3.0.00738585072 dışında skipbalık 'tan alınmıştır|
|942230|Koşullu SQL ekleme girişimlerini algılar|
|942240|MySQL karakter kümesi anahtarını ve MSSQL DoS girişimlerini algılar|
|942250|Daha fazla EŞLEŞMEYI algılar, BIRLEŞTIRIN ve yürütün|
|942251|İçlerini algılar|
|942260|Temel SQL kimlik doğrulaması atlama girişimlerini algılar 2/3|
|942270|Temel SQL ekleme için aranıyor. MySQL Oracle ve diğerleri için ortak saldırı dizesi|
|942280|Postgres pg_sleep ekleme, waitfor gecikme ve veritabanı kapatması girişimlerini algılar|
|942290|Temel MongoDB SQL ekleme girişimlerini bulur|
|942300|MySQL açıklamalarını, koşullarını ve ch (a) r eklemelerini algılar|
|942310|Zincirleme SQL ekleme girişimlerini algılar 2/2|
|942320|MySQL ve PostgreSQL saklı yordamını/işlev kodlarını algılar|
|942330|Klasik SQL ekleme Probler 1/2 ' i algılar|
|942340|Temel SQL kimlik doğrulaması atlama girişimlerini algılar 3/3|
|942350|MySQL UDF ekleme ve diğer veri/yapı işleme girişimlerini algılar|
|942360|Art arda eklenmiş temel SQL ekleme ve SQLLFI girişimlerini algılar|
|942361|Basit SQL ekleme 'yi, alter veya Union anahtar sözcüğüne göre algılar|
|942370|Klasik SQL ekleme Probler 2/2 ' i algılar|
|942380|SQL ekleme saldırısı|
|942390|SQL ekleme saldırısı|
|942400|SQL ekleme saldırısı|
|942410|SQL ekleme saldırısı|
|942420|Kısıtlanmış SQL karakter anomali algılama (tanımlama bilgileri): özel karakter sayısı (8) aşıldı|
|942421|Kısıtlanmış SQL karakter anomali algılama (tanımlama bilgileri): özel karakter sayısı aşıldı (3)|
|942430|Kısıtlanmış SQL karakter anomali algılama (args): özel karakter sayısı aşıldı (12)|
|942431|Kısıtlanmış SQL karakter anomali algılama (args): özel karakter sayısı aşıldı (6)|
|942432|Kısıtlanmış SQL karakter anomali algılama (args): özel karakter sayısı aşıldı (2)|
|942440|SQL açıklama sırası algılandı.|
|942450|SQL onaltılı kodlama tanımlandı|
|942460|Meta karakter anomali algılama uyarısı-yinelenen sözcük olmayan karakterler|
|942470|SQL ekleme saldırısı|
|942480|SQL ekleme saldırısı|
|942490|Klasik SQL ekleme Probler 3/3 ' i algılar|

### <a name="crs943-31"></a> <p x-ms-format-detection="none">ISTEK-943-UYGULAMA-SALDıRı-OTURUM-DÜZELTME</p>

|RuleId|Açıklama|
|---|---|
|943100|Olası oturum oluşturma saldırısı = HTML 'de tanımlama bilgisi değerlerini ayarlama|
|943110|Olası oturum armatürü saldırısı = SessionID parametre adı ve etki alanı dışı başvuran|
|943120|Olası oturum armatürü saldırısı = SessionID parametre adı, başvuran yok|

### <a name="crs944-31"></a> <p x-ms-format-detection="none">ISTEK-944-UYGULAMA-SALDıRı-OTURUM-JAVA</p>

|RuleId|Açıklama|
|---|---|
|944120|Olası yük yürütme ve uzak komut yürütme|
|944130|Kuşkulu Java sınıfları|
|944200|Java serisini kaldırma Apache Commons ile yararlanma|

# <a name="owasp-30tabowasp30"></a>[OWASP 3,0](#tab/owasp30)

## <a name="owasp30"></a>Kural kümeleri

### <a name="general-30"></a> <p x-ms-format-detection="none">Genel</p>

|RuleId|Açıklama|
|---|---|
|200004|Olası çok parçalı eşleşmeyen sınır.|

### <a name="crs911-30"></a> <p x-ms-format-detection="none">ISTEK-911-YÖNTEM-ZORLAMA</p>

|RuleId|Açıklama|
|---|---|
|911100|Yönteme ilke tarafından izin verilmiyor|


### <a name="crs913-30"></a> <p x-ms-format-detection="none">ISTEK-913-TARAYıCı-ALGıLAMA</p>

|RuleId|Açıklama|
|---|---|
|913100|Güvenlik tarayıcısıyla ilişkili Kullanıcı Aracısı bulundu|
|913110|Güvenlik tarayıcısıyla ilişkili istek üst bilgisi bulundu|
|913120|Güvenlik tarayıcısıyla ilişkili istek dosya adı/bağımsız değişken bulundu|
|913101|Betik/genel HTTP istemcisiyle ilişkili Kullanıcı Aracısı bulundu|
|913102|Web Gezgini/bot ile ilişkili Kullanıcı Aracısı bulundu|

### <a name="crs920-30"></a> <p x-ms-format-detection="none">ISTEK-920-PROTOKOL-ZORLAMA</p>

|RuleId|Açıklama|
|---|---|
|920100|Geçersiz HTTP Istek satırı|
|920130|İstek gövdesi ayrıştırılamadı.|
|920140|Çok parçalı istek gövdesi kesin doğrulama başarısız oldu|
|920160|Content-Length HTTP üst bilgisi sayısal değil.|
|920170|Gövde Içerikli GET veya HEAD Isteği.|
|920180|POST isteği için Content-Length üst bilgisi eksik.|
|920190|Range = geçersiz son bayt değeri.|
|920210|Birden çok/çakışan bağlantı üst bilgisi verisi bulundu.|
|920220|URL kodlaması kötüye kullanımı saldırı girişimi|
|920240|URL kodlaması kötüye kullanımı saldırı girişimi|
|920250|UTF8 kötüye kullanımı kötüye saldırı girişimi|
|920260|Unicode tam/yarı genişlik kötüye kullanımı saldırı girişimi|
|920270|İstekte geçersiz karakter (null karakter)|
|920280|İstekte bir konak üstbilgisi eksik|
|920290|Boş ana bilgisayar üstbilgisi|
|920310|İstekte boş bir Accept üst bilgisi yok|
|920311|İstekte boş bir Accept üst bilgisi yok|
|920330|Boş Kullanıcı Aracısı üstbilgisi|
|920340|Içerik Içeren istek ancak Içerik türü üstbilgisi eksik|
|920350|Ana bilgisayar üst bilgisi sayısal bir IP adresidir|
|920380|İstekte çok fazla bağımsız değişken var|
|920360|Bağımsız değişken adı çok uzun|
|920370|Bağımsız değişken değeri çok uzun|
|920390|Toplam bağımsız değişken boyutu aşıldı|
|920400|Karşıya yüklenen dosya boyutu çok büyük|
|920410|Karşıya yüklenen toplam dosya boyutu çok büyük|
|920420|İlke tarafından istek içerik türüne izin verilmiyor|
|920430|İlke tarafından HTTP protokol sürümüne izin verilmiyor|
|920440|URL dosya uzantısı ilkeyle kısıtlıdır|
|920450|HTTP üstbilgisi ilkeyle kısıtlıdır (% @ {MATCHED_VAR})|
|920200|Range = çok fazla alan (6 veya daha fazla)|
|920201|Range = PDF isteği için çok fazla alan (35 veya daha fazla)|
|920230|Birden çok URL kodlaması algılandı|
|920300|İstekte bir Accept üst bilgisi eksik|
|920271|İstekte geçersiz karakter (yazdırılamayan karakterler)|
|920320|Eksik Kullanıcı Aracısı üstbilgisi|
|920272|İstekteki karakter geçersiz (ASCII 127 ' ün altında yazdırılabilir karakterlerin dışında)|
|920202|Range = PDF isteği için çok fazla alan (6 veya daha fazla)|
|920273|İstekte geçersiz karakter (çok katı küme dışında)|
|920274|İstek üst bilgilerinde geçersiz karakter (çok katı küme dışında)|
|920460|Olağan dışı kaçış karakterleri|

### <a name="crs921-30"></a> <p x-ms-format-detection="none">ISTEK-921-PROTOKOL-SALDıRı</p>

|RuleId|Açıklama|
|---|---|
|921100|HTTP Isteği Evleki saldırısı.|
|921110|HTTP Isteği, Uytoze saldırısı|
|921120|HTTP yanıt bölme saldırısı|
|921130|HTTP yanıt bölme saldırısı|
|921140|Üst bilgiler aracılığıyla HTTP üst bilgisi ekleme saldırısı|
|921150|Yük aracılığıyla HTTP üst bilgisi ekleme saldırısı (CR/LF algılandı)|
|921160|Yük (CR/LF ve üstbilgi-Name algılandı) aracılığıyla HTTP üst bilgisi ekleme saldırısı|
|921151|Yük aracılığıyla HTTP üst bilgisi ekleme saldırısı (CR/LF algılandı)|
|921170|HTTP parametre Kirlilii|
|921180|HTTP parametre Kirlilii (% @ {TX. 1})|

### <a name="crs930-30"></a> <p x-ms-format-detection="none">ISTEK-930-UYGULAMA SALDıRıSı-LFI</p>

|RuleId|Açıklama|
|---|---|
|930100|Yol çapraz geçiş saldırısı (/.. /)|
|930110|Yol çapraz geçiş saldırısı (/.. /)|
|930120|İşletim sistemi dosyası erişim denemesi|
|930130|Kısıtlanmış dosya erişim denemesi|

### <a name="crs931-30"></a> <p x-ms-format-detection="none">ISTEK-931-UYGULAMA-SALDıRı-RFI</p>

|RuleId|Açıklama|
|---|---|
|931100|Olası uzak dosya ekleme (RFI) saldırısı = IP adresini kullanan URL parametresi|
|931110|Olası uzak dosya ekleme (RFI) saldırısı = ortak RFI güvenlik açığı parametre adı kullanılan w/URL yükü|
|931120|Olası uzak dosya ekleme (RFI) saldırısı = URL yükü kullanıldı w/sondaki soru Işareti karakteri (?)|
|931130|Olası uzak dosya ekleme (RFI) saldırısı = etki alanı başvurusu/bağlantı|

### <a name="crs932-30"></a> <p x-ms-format-detection="none">ISTEK-932-UYGULAMA-SALDıRı-RCE</p>

|RuleId|Açıklama|
|---|---|
|932120|Uzak komut yürütme = Windows PowerShell komutu bulundu|
|932130|Uzak komut yürütme = Unix kabuğu Ifadesi bulundu|
|932140|Uzak komut yürütme = Windows FOR/IF komutu bulundu|
|932160|Uzak komut yürütme = UNIX kabuk kodu bulundu|
|932170|Uzak komut yürütme = Shellşok (CVE-2014-6271)|
|932171|Uzak komut yürütme = Shellşok (CVE-2014-6271)|

### <a name="crs933-30"></a> <p x-ms-format-detection="none">ISTEK-933-UYGULAMA-SALDıRı-PHP</p>

|RuleId|Açıklama|
|---|---|
|933100|PHP ekleme saldırısı = açma/kapatma etiketi bulundu|
|933110|PHP ekleme saldırısı = PHP betik dosyası karşıya yüklemesi bulundu|
|933120|PHP ekleme saldırısı = yapılandırma yönergesi bulundu|
|933130|PHP ekleme saldırısı = değişkenler bulundu|
|933150|PHP ekleme saldırısı = yüksek riskli PHP Işlev adı bulundu|
|933160|PHP ekleme saldırısı = yüksek riskli PHP Işlev çağrısı bulundu|
|933180|PHP ekleme saldırısı = değişken Işlev çağrısı bulundu|
|933151|PHP ekleme saldırısı = orta riskli PHP Işlev adı bulundu|
|933131|PHP ekleme saldırısı = değişkenler bulundu|
|933161|PHP ekleme saldırısı = düşük değerli PHP Işlev çağrısı bulundu|
|933111|PHP ekleme saldırısı = PHP betik dosyası karşıya yüklemesi bulundu|

### <a name="crs941-30"></a> <p x-ms-format-detection="none">ISTEK-941-UYGULAMA-SALDıRı-XSS</p>

|RuleId|Açıklama|
|---|---|
|941100|Libinjection aracılığıyla algılanan XSS saldırısı|
|941110|XSS filtresi-Kategori 1 = komut dosyası etiketi vektörü|
|941130|XSS filtresi-kategori 3 = öznitelik vektörü|
|941140|XSS filtresi-kategori 4 = JavaScript URI vektörü|
|941150|XSS filtresi-kategori 5 = Izin verilmeyen HTML öznitelikleri|
|941180|Düğüm-Doğrulayıcı kara anahtar sözcükleri|
|941190|Stil sayfalarını kullanarak XSS|
|941200|VML çerçevelerini kullanarak XSS|
|941210|Karıştırılmış JavaScript kullanılarak XSS|
|941220|Karıştırılmış VB betiği kullanılarak XSS|
|941230|' Embed ' etiketi kullanılarak XSS|
|941240|' Import ' veya ' Implementation ' özniteliği kullanılarak XSS|
|941260|' Meta ' etiketi kullanılarak XSS|
|941270|' Link ' href kullanılarak XSS|
|941280|' Base ' etiketi kullanılarak XSS|
|941290|' Applet ' etiketi kullanılarak XSS|
|941300|' Object ' etiketi kullanılarak XSS|
|941310|ABD-ASCII hatalı biçimlendirilmiş kodlama XSS filtresi-saldırı algılandı.|
|941330|IE XSS filtreleri-saldırı algılandı.|
|941340|IE XSS filtreleri-saldırı algılandı.|
|941350|UTF-7 kodlama IE XSS-Saldırı algılandı.|
|941320|Olası XSS saldırısı algılandı-HTML etiketi Işleyicisi|

### <a name="crs942-30"></a> <p x-ms-format-detection="none">ISTEK-942-UYGULAMA-SALDıRı-SQLI</p>

|RuleId|Açıklama|
|---|---|
|942100|Libinjection aracılığıyla SQL ekleme saldırısı algılandı|
|942110|SQL ekleme saldırısı: ortak ekleme testi algılandı|
|942130|SQL ekleme saldırısı: SQL tautology algılandı.|
|942140|SQL ekleme saldırısı = ortak VERITABANı adları algılandı|
|942160|Sleep () veya kıyaslama () kullanarak görünmeyen SQLi testleri algılar.|
|942170|Koşullu sorgular dahil SQL kıyaslama ve uyku ekleme girişimlerini algılar|
|942190|MSSQL kod yürütmeyi ve bilgi toplama girişimlerini algılar|
|942200|MySQL yorumunu algılar-/Space-belirsizleştirilmiş ınjecve backtick sonlandırma|
|942230|Koşullu SQL ekleme girişimlerini algılar|
|942260|Temel SQL kimlik doğrulaması atlama girişimlerini algılar 2/3|
|942270|Temel SQL ekleme için aranıyor. MySQL Oracle ve diğerleri için ortak saldırı dizesi.|
|942290|Temel MongoDB SQL ekleme girişimlerini bulur|
|942300|MySQL açıklamalarını, koşullarını ve ch (a) r eklemelerini algılar|
|942310|Zincirleme SQL ekleme girişimlerini algılar 2/2|
|942320|MySQL ve PostgreSQL saklı yordamını/işlev kodlarını algılar|
|942330|Klasik SQL ekleme Probler 1/2 ' i algılar|
|942340|Temel SQL kimlik doğrulaması atlama girişimlerini algılar 3/3|
|942350|MySQL UDF ekleme ve diğer veri/yapı işleme girişimlerini algılar|
|942360|Art arda eklenmiş temel SQL ekleme ve SQLLFI girişimlerini algılar|
|942370|Klasik SQL ekleme Probler 2/2 ' i algılar|
|942150|SQL ekleme saldırısı|
|942410|SQL ekleme saldırısı|
|942430|Kısıtlanmış SQL karakter anomali algılama (args): özel karakter sayısı aşıldı (12)|
|942440|SQL açıklama sırası algılandı.|
|942450|SQL onaltılı kodlama tanımlandı|
|942251|İçlerini algılar|
|942460|Meta karakter anomali algılama uyarısı-yinelenen sözcük olmayan karakterler|

### <a name="crs943-30"></a> <p x-ms-format-detection="none">ISTEK-943-UYGULAMA-SALDıRı-OTURUM-DÜZELTME</p>

|RuleId|Açıklama|
|---|---|
|943100|Olası oturum oluşturma saldırısı = HTML 'de tanımlama bilgisi değerlerini ayarlama|
|943110|Olası oturum armatürü saldırısı = SessionID parametre adı ve etki alanı dışı başvuran|
|943120|Olası oturum armatürü saldırısı = SessionID parametre adı, başvuran yok|

# <a name="owasp-229tabowasp2"></a>[OWASP 2.2.9](#tab/owasp2)

## <a name="owasp229"></a>Kural kümeleri

### <a name="crs20"></a>crs_20_protocol_violations

|RuleId|Açıklama|
|---|---|
|960911|Geçersiz HTTP Istek satırı|
|981227|Apache hatası = Istekte Geçersiz URI.|
|960912|İstek gövdesi ayrıştırılamadı.|
|960914|Çok parçalı istek gövdesi kesin doğrulama başarısız oldu|
|960915|Çok parçalı ayrıştırıcı, olası bir eşleşmeyen sınır algıladı.|
|960016|Content-Length HTTP üst bilgisi sayısal değil.|
|960011|Gövde Içerikli GET veya HEAD Isteği.|
|960012|POST isteği için Content-Length üst bilgisi eksik.|
|960902|Kimlik kodlamasının geçersiz kullanımı.|
|960022|HTTP 1,0 için üstbilgiye Izin verilmiyor.|
|960020|Pragma üst bilgisi, HTTP/1.1 istekleri için Cache-Control üst bilgisi gerektirir.|
|958291|Range = alanı var ve 0 ile başlar.|
|958230|Range = geçersiz son bayt değeri.|
|958295|Birden çok/çakışan bağlantı üst bilgisi verisi bulundu.|
|950107|URL kodlaması kötüye kullanımı saldırı girişimi|
|950109|Birden çok URL kodlaması algılandı|
|950108|URL kodlaması kötüye kullanımı saldırı girişimi|
|950801|UTF8 kötüye kullanımı kötüye saldırı girişimi|
|950116|Unicode tam/yarı genişlik kötüye kullanımı saldırı girişimi|
|960901|İstekte geçersiz karakter|
|960018|İstekte geçersiz karakter|

### <a name="crs21"></a>crs_21_protocol_anomalies

|RuleId|Açıklama|
|---|---|
|960008|İstekte bir konak üstbilgisi eksik|
|960007|Boş ana bilgisayar üstbilgisi|
|960015|İstekte bir Accept üst bilgisi eksik|
|960021|İstekte boş bir Accept üst bilgisi yok|
|960009|İstekte Kullanıcı Aracısı üst bilgisi eksik|
|960006|Boş Kullanıcı Aracısı üstbilgisi|
|960904|Içerik Içeren istek ancak Içerik türü üstbilgisi eksik|
|960017|Ana bilgisayar üst bilgisi sayısal bir IP adresidir|

### <a name="crs23"></a>crs_23_request_limits

|RuleId|Açıklama|
|---|---|
|960209|Bağımsız değişken adı çok uzun|
|960208|Bağımsız değişken değeri çok uzun|
|960335|İstekte çok fazla bağımsız değişken var|
|960341|Toplam bağımsız değişken boyutu aşıldı|
|960342|Karşıya yüklenen dosya boyutu çok büyük|
|960343|Karşıya yüklenen toplam dosya boyutu çok büyük|

### <a name="crs30"></a>crs_30_http_policy

|RuleId|Açıklama|
|---|---|
|960032|Yönteme ilke tarafından izin verilmiyor|
|960010|İlke tarafından istek içerik türüne izin verilmiyor|
|960034|İlke tarafından HTTP protokol sürümüne izin verilmiyor|
|960035|URL dosya uzantısı ilkeyle kısıtlıdır|
|960038|HTTP üstbilgisi ilkeyle kısıtlıdır|

### <a name="crs35"></a>crs_35_bad_robots

|RuleId|Açıklama|
|---|---|
|990002|İstek, siteyi tarayan bir güvenlik tarayıcısı olduğunu belirtir|
|990901|İstek, siteyi tarayan bir güvenlik tarayıcısı olduğunu belirtir|
|990902|İstek, siteyi tarayan bir güvenlik tarayıcısı olduğunu belirtir|
|990012|Standart dışı Web sitesi Gezgini|

### <a name="crs40"></a>crs_40_generic_attacks

|RuleId|Açıklama|
|---|---|
|960024|Meta karakter anomali algılama uyarısı-yinelenen sözcük olmayan karakterler|
|950008|Belgelenmemiş ColdFusion etiketlerinin ekleme|
|950010|LDAP ekleme saldırısı|
|950011|SSI ekleme saldırısı|
|950018|Evrensel PDF XSS URL 'SI algılandı.|
|950019|E-posta ekleme saldırısı|
|950012|HTTP Isteği Evleki saldırısı.|
|950910|HTTP yanıt bölme saldırısı|
|950911|HTTP yanıt bölme saldırısı|
|950117|Uzak dosya ekleme saldırısı|
|950118|Uzak dosya ekleme saldırısı|
|950119|Uzak dosya ekleme saldırısı|
|950120|Olası uzak dosya ekleme (RFI) saldırısı = etki alanı başvurusu/bağlantı|
|981133|Kural 981133|
|981134|Kural 981134|
|950009|Oturum düzeltme saldırısı|
|950003|Oturum armatürü|
|950000|Oturum armatürü|
|950005|Uzak dosya erişim denemesi|
|950002|Sistem komutuna erişim|
|950006|Sistem komutu ekleme|
|959151|PHP ekleme saldırısı|
|958976|PHP ekleme saldırısı|
|958977|PHP ekleme saldırısı|

### <a name="crs41sql"></a>crs_41_sql_injection_attacks

|RuleId|Açıklama|
|---|---|
|981231|SQL açıklama sırası algılandı.|
|981260|SQL onaltılı kodlama tanımlandı|
|981320|SQL ekleme saldırısı = ortak VERITABANı adları algılandı|
|981300|Kural 981300|
|981301|Kural 981301|
|981302|Kural 981302|
|981303|Kural 981303|
|981304|Kural 981304|
|981305|Kural 981305|
|981306|Kural 981306|
|981307|Kural 981307|
|981308|Kural 981308|
|981309|Kural 981309|
|981310|Kural 981310|
|981311|Kural 981311|
|981312|Kural 981312|
|981313|Kural 981313|
|981314|Kural 981314|
|981315|Kural 981315|
|981316|Kural 981316|
|981317|SQL SELECT Ifadesinin anomali algılama uyarısı|
|950007|Kör SQL ekleme saldırısı|
|950001|SQL ekleme saldırısı|
|950908|SQL ekleme saldırısı.|
|959073|SQL ekleme saldırısı|
|981272|Sleep () veya kıyaslama () kullanarak görünmeyen SQLi testleri algılar.|
|981250|Koşullu sorgular dahil SQL kıyaslama ve uyku ekleme girişimlerini algılar|
|981241|Koşullu SQL ekleme girişimlerini algılar|
|981276|Temel SQL ekleme için aranıyor. MySQL Oracle ve diğerleri için ortak saldırı dizesi.|
|981270|Temel MongoDB SQL ekleme girişimlerini bulur|
|981253|MySQL ve PostgreSQL saklı yordamını/işlev kodlarını algılar|
|981251|MySQL UDF ekleme ve diğer veri/yapı işleme girişimlerini algılar|

### <a name="crs41xss"></a>crs_41_xss_attacks

|RuleId|Açıklama|
|---|---|
|973336|XSS filtresi-Kategori 1 = komut dosyası etiketi vektörü|
|973338|XSS filtresi-kategori 3 = JavaScript URI vektörü|
|981136|Kural 981136|
|981018|Kural 981018|
|958016|Siteler arası betik oluşturma (XSS) saldırısı|
|958414|Siteler arası betik oluşturma (XSS) saldırısı|
|958032|Siteler arası betik oluşturma (XSS) saldırısı|
|958026|Siteler arası betik oluşturma (XSS) saldırısı|
|958027|Siteler arası betik oluşturma (XSS) saldırısı|
|958054|Siteler arası betik oluşturma (XSS) saldırısı|
|958418|Siteler arası betik oluşturma (XSS) saldırısı|
|958034|Siteler arası betik oluşturma (XSS) saldırısı|
|958019|Siteler arası betik oluşturma (XSS) saldırısı|
|958013|Siteler arası betik oluşturma (XSS) saldırısı|
|958408|Siteler arası betik oluşturma (XSS) saldırısı|
|958012|Siteler arası betik oluşturma (XSS) saldırısı|
|958423|Siteler arası betik oluşturma (XSS) saldırısı|
|958002|Siteler arası betik oluşturma (XSS) saldırısı|
|958017|Siteler arası betik oluşturma (XSS) saldırısı|
|958007|Siteler arası betik oluşturma (XSS) saldırısı|
|958047|Siteler arası betik oluşturma (XSS) saldırısı|
|958410|Siteler arası betik oluşturma (XSS) saldırısı|
|958415|Siteler arası betik oluşturma (XSS) saldırısı|
|958022|Siteler arası betik oluşturma (XSS) saldırısı|
|958405|Siteler arası betik oluşturma (XSS) saldırısı|
|958419|Siteler arası betik oluşturma (XSS) saldırısı|
|958028|Siteler arası betik oluşturma (XSS) saldırısı|
|958057|Siteler arası betik oluşturma (XSS) saldırısı|
|958031|Siteler arası betik oluşturma (XSS) saldırısı|
|958006|Siteler arası betik oluşturma (XSS) saldırısı|
|958033|Siteler arası betik oluşturma (XSS) saldırısı|
|958038|Siteler arası betik oluşturma (XSS) saldırısı|
|958409|Siteler arası betik oluşturma (XSS) saldırısı|
|958001|Siteler arası betik oluşturma (XSS) saldırısı|
|958005|Siteler arası betik oluşturma (XSS) saldırısı|
|958404|Siteler arası betik oluşturma (XSS) saldırısı|
|958023|Siteler arası betik oluşturma (XSS) saldırısı|
|958010|Siteler arası betik oluşturma (XSS) saldırısı|
|958411|Siteler arası betik oluşturma (XSS) saldırısı|
|958422|Siteler arası betik oluşturma (XSS) saldırısı|
|958036|Siteler arası betik oluşturma (XSS) saldırısı|
|958000|Siteler arası betik oluşturma (XSS) saldırısı|
|958018|Siteler arası betik oluşturma (XSS) saldırısı|
|958406|Siteler arası betik oluşturma (XSS) saldırısı|
|958040|Siteler arası betik oluşturma (XSS) saldırısı|
|958052|Siteler arası betik oluşturma (XSS) saldırısı|
|958037|Siteler arası betik oluşturma (XSS) saldırısı|
|958049|Siteler arası betik oluşturma (XSS) saldırısı|
|958030|Siteler arası betik oluşturma (XSS) saldırısı|
|958041|Siteler arası betik oluşturma (XSS) saldırısı|
|958416|Siteler arası betik oluşturma (XSS) saldırısı|
|958024|Siteler arası betik oluşturma (XSS) saldırısı|
|958059|Siteler arası betik oluşturma (XSS) saldırısı|
|958417|Siteler arası betik oluşturma (XSS) saldırısı|
|958020|Siteler arası betik oluşturma (XSS) saldırısı|
|958045|Siteler arası betik oluşturma (XSS) saldırısı|
|958004|Siteler arası betik oluşturma (XSS) saldırısı|
|958421|Siteler arası betik oluşturma (XSS) saldırısı|
|958009|Siteler arası betik oluşturma (XSS) saldırısı|
|958025|Siteler arası betik oluşturma (XSS) saldırısı|
|958413|Siteler arası betik oluşturma (XSS) saldırısı|
|958051|Siteler arası betik oluşturma (XSS) saldırısı|
|958420|Siteler arası betik oluşturma (XSS) saldırısı|
|958407|Siteler arası betik oluşturma (XSS) saldırısı|
|958056|Siteler arası betik oluşturma (XSS) saldırısı|
|958011|Siteler arası betik oluşturma (XSS) saldırısı|
|958412|Siteler arası betik oluşturma (XSS) saldırısı|
|958008|Siteler arası betik oluşturma (XSS) saldırısı|
|958046|Siteler arası betik oluşturma (XSS) saldırısı|
|958039|Siteler arası betik oluşturma (XSS) saldırısı|
|958003|Siteler arası betik oluşturma (XSS) saldırısı|
|973300|Olası XSS saldırısı algılandı-HTML etiketi Işleyicisi|
|973301|XSS saldırısı algılandı|
|973302|XSS saldırısı algılandı|
|973303|XSS saldırısı algılandı|
|973304|XSS saldırısı algılandı|
|973305|XSS saldırısı algılandı|
|973306|XSS saldırısı algılandı|
|973307|XSS saldırısı algılandı|
|973308|XSS saldırısı algılandı|
|973309|XSS saldırısı algılandı|
|973311|XSS saldırısı algılandı|
|973313|XSS saldırısı algılandı|
|973314|XSS saldırısı algılandı|
|973331|IE XSS filtreleri-saldırı algılandı.|
|973315|IE XSS filtreleri-saldırı algılandı.|
|973330|IE XSS filtreleri-saldırı algılandı.|
|973327|IE XSS filtreleri-saldırı algılandı.|
|973326|IE XSS filtreleri-saldırı algılandı.|
|973346|IE XSS filtreleri-saldırı algılandı.|
|973345|IE XSS filtreleri-saldırı algılandı.|
|973324|IE XSS filtreleri-saldırı algılandı.|
|973323|IE XSS filtreleri-saldırı algılandı.|
|973348|IE XSS filtreleri-saldırı algılandı.|
|973321|IE XSS filtreleri-saldırı algılandı.|
|973320|IE XSS filtreleri-saldırı algılandı.|
|973318|IE XSS filtreleri-saldırı algılandı.|
|973317|IE XSS filtreleri-saldırı algılandı.|
|973329|IE XSS filtreleri-saldırı algılandı.|
|973328|IE XSS filtreleri-saldırı algılandı.|

### <a name="crs42"></a>crs_42_tight_security

|RuleId|Açıklama|
|---|---|
|950103|Yol çapraz geçişi saldırısı|

### <a name="crs45"></a>crs_45_trojans

|RuleId|Açıklama|
|---|---|
|950110|Arka kapı erişimi|
|950921|Arka kapı erişimi|
|950922|Arka kapı erişimi|

---

## <a name="next-steps"></a>Sonraki adımlar

- [Azure portal kullanarak Web uygulaması güvenlik duvarı kurallarını özelleştirme](application-gateway-customize-waf-rules-portal.md)
