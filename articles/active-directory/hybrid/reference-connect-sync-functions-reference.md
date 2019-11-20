---
title: 'Azure AD Connect eşitleme: İşlevler başvurusu | Microsoft Docs'
description: Azure AD Connect eşitlemede bildirime dayalı sağlama ifadelerinin başvurusu.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4f525ca0-be0e-4a2e-8da1-09b6b567ed5f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c3102480e316c634930c356ae02f769767b7d08
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900039"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Azure AD Connect eşitleme: İşlevler Başvurusu
Azure AD Connect, işlevler eşitleme sırasında bir öznitelik değerini işlemek için kullanılır.  
İşlevlerinin sözdizimi aşağıdaki biçim kullanılarak ifade edilir:  
`<output type> FunctionName(<input type> <position name>, ..)`

Bir işlev aşırı yüklenmişse ve birden çok sözdizimleri kabul ediyorsa, tüm geçerli sözdizimler listelenir.  
İşlevler kesin bir şekilde türdedir ve geçirilen türün belgelenen tür ile eşleştiğini doğrular.  
Tür eşleşmezse bir hata atılır.

Türler aşağıdaki sözdizimiyle ifade edilir:

* **bin** – ikili
* **bool** – Boolean
* **dt** : UTC Tarih/saat
* **enum** – bilinen sabitlerin numaralandırması
* **Exp** – Boolean olarak değerlendirilmesi beklenen ifadesi
* **mvbin** – çok değerli ikili
* **mvstr** – çok değerli dize
* **mvref** – birden çok değerli başvuru
* **num** – sayısal
* **ref** – başvuru
* **Str** – dize
* **var** : (neredeyse) diğer tür bir değişken
* **void** – değer döndürmez

**Mvbin**, **mvstr**ve **mvref** türlerine sahip işlevler yalnızca çok değerli özniteliklerde çalışabilir. **Bin**, **Str**ve **ref** içeren işlevler hem tek değerli hem de birden çok değerli özniteliklerde çalışır.

## <a name="functions-reference"></a>İşlevler Başvurusu

| İşlevlerin listesi |  |  |  |  |
| --- | --- | --- | --- | --- |
| **Sertifika** | | | | |
| [Certbu Sionıds](#certextensionoids) |[CertFormat](#certformat) |[CertFriendlyName](#certfriendlyname) |[CertHashString](#certhashstring) | |
| [Certısuer](#certissuer) |[Certısuerdn](#certissuerdn) |[Certısueroıd](#certissueroid) |[Certkeyalgorithd](#certkeyalgorithm) | |
| [CertKeyAlgorithmParams](#certkeyalgorithmparams) |[Certnameınfo](#certnameinfo) |[CertNotAfter](#certnotafter) |[CertNotBefore](#certnotbefore) | |
| [Certpublickeyoıd](#certpublickeyoid) |[CertPublicKeyParametersOid](#certpublickeyparametersoid) |[CertSerialNumber](#certserialnumber) |[CertSignatureAlgorithmOid](#certsignaturealgorithmoid) | |
| [CertSubject](#certsubject) |[CertSubjectNameDN](#certsubjectnamedn) |[Certsubjectnameoıd](#certsubjectnameoid) |[Certparmak Izi](#certthumbprint) | |
[CertVersion](#certversion) |[IsCert](#iscert) | | | |
| **Dönüştürü** | | | | |
| [CBool](#cbool) |[CDate](#cdate) |[CGuid](#cguid) |[ConvertFromBase64](#convertfrombase64) | |
| [ConvertToBase64](#converttobase64) |[ConvertFromUTF8Hex](#convertfromutf8hex) |[ConvertToUTF8Hex](#converttoutf8hex) |[CNum](#cnum) | |
| [CRef](#cref) |[CStr](#cstr) |[StringFromGuid](#stringfromguid) |[StringFromSid](#stringfromsid) | |
| **Tarih/saat** | | | | |
| [DateAdd](#dateadd) |[Tarih Fromnum](#datefromnum) |[FormatDateTime](#formatdatetime) |[Sunuldu](#now) | |
| [NumFromDate](#numfromdate) | | | | |
| **Dizinden** | | | | |
| [DNComponent](#dncomponent) |[DNComponentRev](#dncomponentrev) |[Çıkar Edncomponent](#escapedncomponent) | | |
| **Değerlendirmesinin** | | | | |
| [IsBitSet](#isbitset) |[IsDate](#isdate) |[IsEmpty](#isempty) |[IsGuid](#isguid) | |
| [IsNull](#isnull) |[IsNullOrEmpty](#isnullorempty) |[IsNumeric](#isnumeric) |[Olmasına](#ispresent) | |
| [Isstrıng](#isstring) | | | | |
| **Tiğin** | | | | |
| [BitAnd](#bitand) |[BitOr](#bitor) |[Rasgelenum](#randomnum) | | |
| **Çoklu değerli** | | | | |
| [Vardır](#contains) |[Biriktirme](#count) |[Öğesi](#item) |[Imornull](#itemornull) | |
| [Birleştir](#join) |[RemoveDuplicates](#removeduplicates) |[Ayırmayı](#split) | | |
| **Program akışı** | | | | |
| [Hata:](#error) |[MAYAN](#iif) |[Seç](#select) |[Değiştirebilirsiniz](#switch) | |
| [Olmadığı](#where) |[Kullanılarak](#with) | | | |
| **Text** | | | | |
| [GUID](#guid) |[InStr](#instr) |[InStrRev](#instrrev) |[LCase](#lcase) | |
| [Sol](#left) |[Tepe](#len) |[LTrim](#ltrim) |[Orta](#mid) | |
| [Asma sol](#padleft) |[Asma sağ](#padright) |[PCase](#pcase) |[Değiştir](#replace) | |
| [ReplaceChars](#replacechars) |[Right](#right) |[RTrim](#rtrim) |[Kırpma](#trim) | |
| [UCase](#ucase) |[Word](#word) | | | |

---
### <a name="bitand"></a>BitAnd
**Açıklama:**  
BitAnd işlevi, belirtilen bitleri bir değer üzerinde ayarlar.

**Sözdizimi**  
`num BitAnd(num value1, num value2)`

* Değer1, değer2: bir arada olması gereken sayısal değerler

**Açıklamalarının**  
Bu işlev, her iki parametreyi de ikili gösterimine dönüştürür ve bir bit şu şekilde ayarlar:

* 0- *değer1* ve *değer2* içindeki karşılık gelen bitlerin biri veya her ikisi 0 ise
* 1-karşılık gelen bitlerin her ikisi de 1 ' dir.

Diğer bir deyişle, her iki parametrenin de karşılık gelen bitlerinin 1 olduğu durumlar dışında her durumda 0 döndürür.

**Örnek:**  
`BitAnd(&HF, &HF7)`  
Onaltılık "F" ve "F7" değerinin bu değeri değerlendirmesi nedeniyle 7 döndürür.

---
### <a name="bitor"></a>BitOr
**Açıklama:**  
BitOr işlevi, belirtilen bitleri bir değer üzerinde ayarlar.

**Sözdizimi**  
`num BitOr(num value1, num value2)`

* Değer1, değer2: bir arada olması gereken sayısal değerler

**Açıklamalarının**  
Bu işlev, her iki parametreyi de ikili gösterimine dönüştürür ve maskede ve bayrakta karşılık gelen bitlerin biri veya her ikisi 1 ise ve her ikisi de 0 ise 0 olarak ayarlanır. Diğer bir deyişle, her iki parametrenin de karşılık gelen bitlerinin 0 olduğu durumlar dışında her durumda 1 döndürür.

---
### <a name="cbool"></a>CBool
**Açıklama:**  
CBool işlevi, değerlendirilen ifadeye bağlı olarak bir Boole değeri döndürür

**Sözdizimi**  
`bool CBool(exp Expression)`

**Açıklamalarının**  
İfade sıfır olmayan bir değer olarak değerlendirilirse, CBool true değerini döndürür, aksi takdirde false döndürür.

**Örnek:**  
`CBool([attrib1] = [attrib2])`  

Her iki öznitelik de aynı değere sahip olduğunda true döndürür.

---
### <a name="cdate"></a>CDate
**Açıklama:**  
CDate işlevi bir dizeden UTC tarih saati döndürür. DateTime, Sync içinde yerel bir öznitelik türü değildir ancak bazı işlevler tarafından kullanılır.

**Sözdizimi**  
`dt CDate(str value)`

* Deeri Tarih, saat ve isteğe bağlı saat dilimine sahip bir dize

**Açıklamalarının**  
Döndürülen dize her zaman UTC olarak olur.

**Örnek:**  
`CDate([employeeStartTime])`  
Çalışanın başlangıç zamanına bağlı olarak bir tarih saat döndürür

`CDate("2013-01-10 4:00 PM -8")`  
"2013-01-11 12:00" temsil eden bir tarih saat döndürür


---
### <a name="certextensionoids"></a>Certbu Sionıds
**Açıklama:**  
Bir sertifika nesnesinin tüm kritik uzantılarının OID değerlerini döndürür.

**Sözdizimi**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: X. 509.440 sertifikasının bayt dizisi temsili. Byte dizisi ikili (DER) kodlanmış veya Base64 ile kodlanmış X. 509.952 verileri olabilir.

---
### <a name="certformat"></a>CertFormat
**Açıklama:**  
Bu X. 509v3 sertifikası biçiminin adını döndürür.

**Sözdizimi**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: X. 509.440 sertifikasının bayt dizisi temsili. Byte dizisi ikili (DER) kodlanmış veya Base64 ile kodlanmış X. 509.952 verileri olabilir.

---
### <a name="certfriendlyname"></a>CertFriendlyName
**Açıklama:**  
Bir sertifika için ilişkili diğer adı döndürür.

**Sözdizimi**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: X. 509.440 sertifikasının bayt dizisi temsili. Byte dizisi ikili (DER) kodlanmış veya Base64 ile kodlanmış X. 509.952 verileri olabilir.

---
### <a name="certhashstring"></a>CertHashString
**Açıklama:**  
X. 509v3 sertifikası için bir onaltılı dize olarak SHA1 karma değerini döndürür.

**Sözdizimi**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: X. 509.440 sertifikasının bayt dizisi temsili. Byte dizisi ikili (DER) kodlanmış veya Base64 ile kodlanmış X. 509.952 verileri olabilir.

---
### <a name="certissuer"></a>Certısuer
**Açıklama:**  
X. 509v3 sertifikasını veren sertifika yetkilisinin adını döndürür.

**Sözdizimi**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: X. 509.440 sertifikasının bayt dizisi temsili. Byte dizisi ikili (DER) kodlanmış veya Base64 ile kodlanmış X. 509.952 verileri olabilir.

---
### <a name="certissuerdn"></a>Certısuerdn
**Açıklama:**  
Sertifika verenin ayırt edici adını döndürür.

**Sözdizimi**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: X. 509.440 sertifikasının bayt dizisi temsili. Byte dizisi ikili (DER) kodlanmış veya Base64 ile kodlanmış X. 509.952 verileri olabilir.

---
### <a name="certissueroid"></a>Certısueroıd
**Açıklama:**  
Sertifika verenin OID 'sini döndürür.

**Sözdizimi**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: X. 509.440 sertifikasının bayt dizisi temsili. Byte dizisi ikili (DER) kodlanmış veya Base64 ile kodlanmış X. 509.952 verileri olabilir.

---
### <a name="certkeyalgorithm"></a>Certkeyalgorithd
**Açıklama:**  
Bu X. 509v3 sertifikası için anahtar algoritması bilgilerini bir dize olarak döndürür.

**Sözdizimi**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: X. 509.440 sertifikasının bayt dizisi temsili. Byte dizisi ikili (DER) kodlanmış veya Base64 ile kodlanmış X. 509.952 verileri olabilir.

---
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Açıklama:**  
X. 509v3 sertifikası için anahtar algoritması parametrelerini onaltılık bir dize olarak döndürür.

**Sözdizimi**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: X. 509.440 sertifikasının bayt dizisi temsili. Byte dizisi ikili (DER) kodlanmış veya Base64 ile kodlanmış X. 509.952 verileri olabilir.

---
### <a name="certnameinfo"></a>Certnameınfo
**Açıklama:**  
Bir sertifikadan konu ve veren adlarını döndürür.

**Sözdizimi**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: X. 509.440 sertifikasının bayt dizisi temsili. Byte dizisi ikili (DER) kodlanmış veya Base64 ile kodlanmış X. 509.952 verileri olabilir.
*   X509NameType: Konunun X509NameType değeri.
*   ıncludesıssuername: veren adını içerecek şekilde doğru; Aksi takdirde, false.

---
### <a name="certnotafter"></a>CertNotAfter
**Açıklama:**  
Bir sertifikanın artık geçerli olmadığı yerel saat içindeki tarihi döndürür.

**Sözdizimi**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: X. 509.440 sertifikasının bayt dizisi temsili. Byte dizisi ikili (DER) kodlanmış veya Base64 ile kodlanmış X. 509.952 verileri olabilir.

---
### <a name="certnotbefore"></a>CertNotBefore
**Açıklama:**  
Yerel saat içinde bir sertifikanın geçerli hale geldiği tarihi döndürür.

**Sözdizimi**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: X. 509.440 sertifikasının bayt dizisi temsili. Byte dizisi ikili (DER) kodlanmış veya Base64 ile kodlanmış X. 509.952 verileri olabilir.

---
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**Açıklama:**  
X. 509v3 sertifikası için ortak anahtarın OID 'sini döndürür.

**Sözdizimi**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: X. 509.440 sertifikasının bayt dizisi temsili. Byte dizisi ikili (DER) kodlanmış veya Base64 ile kodlanmış X. 509.952 verileri olabilir.

---
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Açıklama:**  
X. 509v3 sertifikası için ortak anahtar parametrelerinin OID 'sini döndürür.

**Sözdizimi**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: X. 509.440 sertifikasının bayt dizisi temsili. Byte dizisi ikili (DER) kodlanmış veya Base64 ile kodlanmış X. 509.952 verileri olabilir.

---
### <a name="certserialnumber"></a>CertSerialNumber
**Açıklama:**  
X. 509v3 sertifikasının seri numarasını döndürür.

**Sözdizimi**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: X. 509.440 sertifikasının bayt dizisi temsili. Byte dizisi ikili (DER) kodlanmış veya Base64 ile kodlanmış X. 509.952 verileri olabilir.

---
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Açıklama:**  
Bir sertifikanın imzasını oluşturmak için kullanılan algoritmanın OID 'sini döndürür.

**Sözdizimi**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: X. 509.440 sertifikasının bayt dizisi temsili. Byte dizisi ikili (DER) kodlanmış veya Base64 ile kodlanmış X. 509.952 verileri olabilir.

---
### <a name="certsubject"></a>CertSubject
**Açıklama:**  
Bir sertifikadan konu ayırt edici adını alır.

**Sözdizimi**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: X. 509.440 sertifikasının bayt dizisi temsili. Byte dizisi ikili (DER) kodlanmış veya Base64 ile kodlanmış X. 509.952 verileri olabilir.

---
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**Açıklama:**  
Bir sertifikadan konu ayırt edici adını döndürür.

**Sözdizimi**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: X. 509.440 sertifikasının bayt dizisi temsili. Byte dizisi ikili (DER) kodlanmış veya Base64 ile kodlanmış X. 509.952 verileri olabilir.

---
### <a name="certsubjectnameoid"></a>Certsubjectnameoıd
**Açıklama:**  
Bir sertifikadan konu adının OID 'sini döndürür.

**Sözdizimi**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: X. 509.440 sertifikasının bayt dizisi temsili. Byte dizisi ikili (DER) kodlanmış veya Base64 ile kodlanmış X. 509.952 verileri olabilir.

---
### <a name="certthumbprint"></a>Certparmak Izi
**Açıklama:**  
Bir sertifikanın parmak izini döndürür.

**Sözdizimi**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: X. 509.440 sertifikasının bayt dizisi temsili. Byte dizisi ikili (DER) kodlanmış veya Base64 ile kodlanmış X. 509.952 verileri olabilir.

---
### <a name="certversion"></a>CertVersion
**Açıklama:**  
Sertifikanın X. 509.952 biçim sürümünü döndürür.

**Sözdizimi**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: X. 509.440 sertifikasının bayt dizisi temsili. Byte dizisi ikili (DER) kodlanmış veya Base64 ile kodlanmış X. 509.952 verileri olabilir.

---
### <a name="cguid"></a>CGuid
**Açıklama:**  
CGuid işlevi bir GUID 'nin dize gösterimini ikili gösterimine dönüştürür.

**Sözdizimi**  
`bin CGuid(str GUID)`

* Bu düzende biçimlendirilen bir dize: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx veya {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

---
### <a name="contains"></a>İçerir
**Açıklama:**  
Contains işlevi, bir dizeyi çok değerli bir özniteliğin içinde bulur

**Sözdizimi**  
`num Contains (mvstring attribute, str search)`-büyük-küçük harfe duyarlı  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)`-büyük-küçük harfe duyarlı

* öznitelik: aranacak çok değerli öznitelik.
* Search: özniteliğinde bulunacak dize.
* Casetype: Caseduyarsız veya CaseSensitive.

Dizenin bulunduğu çok değerli öznitelikteki dizini döndürür. dize bulunamazsa 0 döndürülür.

**Açıklamalarının**  
Birden çok değerli dize öznitelikleri için arama, değerlerde alt dizeleri bulur.  
Başvuru öznitelikleri için, aranan dizenin eşleşme olarak kabul edileceği değerle tam olarak eşleşmesi gerekir.

**Örnek:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
ProxyAddresses özniteliğinin bir birincil e-posta adresi varsa (büyük harfli "SMTP:"), ardından proxyAddress özniteliğini döndürün, aksi takdirde bir hata döndürün.

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**Açıklama:**  
ConvertFromBase64 işlevi, belirtilen Base64 kodlamalı değeri normal bir dizeye dönüştürür.

**Sözdizimi**  
`str ConvertFromBase64(str source)`-kodlama için Unicode varsayılır  
`str ConvertFromBase64(str source, enum Encoding)`

* kaynaktaki Base64 kodlamalı dize  
* Şifreleme Unicode, ASCII, UTF8

**Örnek**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Her iki örnek de "*Hello World!* " döndürür

---
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex
**Açıklama:**  
ConvertFromUTF8Hex işlevi, belirtilen UTF8 onaltılı kodlamalı değeri bir dizeye dönüştürür.

**Sözdizimi**  
`str ConvertFromUTF8Hex(str source)`

* kaynaktaki UTF8 2 baytlık kodlanmış

**Açıklamalarının**  
Bu işlev ve ConvertFromBase64 ([], UTF8) arasındaki fark, sonucun DN özniteliği için kullanımı kolay.  
Bu biçim, Azure Active Directory tarafından DN olarak kullanılır.

**Örnek:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
"*Hello World!* " döndürür

---
### <a name="converttobase64"></a>ConvertToBase64
**Açıklama:**  
ConvertToBase64 işlevi bir dizeyi Unicode Base64 dizesine dönüştürür.  
Bir tamsayılar dizisinin değerini, Base-64 basamakları ile kodlanmış eşdeğer dize gösterimine dönüştürür.

**Sözdizimi**  
`str ConvertToBase64(str source)`

**Örnek:**  
`ConvertToBase64("Hello world!")`  
"Sablagwabebek Vacaadilevahiababkacea" döndürür

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Açıklama:**  
ConvertToUTF8Hex işlevi bir dizeyi UTF8 onaltılık kodlanmış bir değere dönüştürür.

**Sözdizimi**  
`str ConvertToUTF8Hex(str source)`

**Açıklamalarının**  
Bu işlevin çıkış biçimi Azure Active Directory tarafından, DN öznitelik biçimi olarak kullanılır.

**Örnek:**  
`ConvertToUTF8Hex("Hello world!")`  
48656C6C6F20776F726C6421 döndürür

---
### <a name="count"></a>Count
**Açıklama:**  
Count işlevi, birden çok değerli bir öznitelikteki öğelerin sayısını döndürür

**Sözdizimi**  
`num Count(mvstr attribute)`

---
### <a name="cnum"></a>CNum
**Açıklama:**  
CNum işlevi bir dize alır ve sayısal bir veri türü döndürür.

**Sözdizimi**  
`num CNum(str value)`

---
### <a name="cref"></a>CRef
**Açıklama:**  
Bir dizeyi başvuru özniteliğine dönüştürür

**Sözdizimi**  
`ref CRef(str value)`

**Örnek:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

---
### <a name="cstr"></a>CStr
**Açıklama:**  
CStr işlevi bir dize veri türüne dönüştürür.

**Sözdizimi**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* deeri Sayısal bir değer, başvuru özniteliği veya Boole olabilir.

**Örnek:**  
`CStr([dn])`  
"CN = ali, DC = contoso, DC = com" döndürebilir

---
### <a name="dateadd"></a>DateAdd
**Açıklama:**  
Belirtilen zaman aralığının eklendiği tarihi içeren bir tarih döndürür.

**Sözdizimi**  
`dt DateAdd(str interval, num value, dt date)`

* aralığında Eklemek istediğiniz zaman aralığı olan dize ifadesi. Dize aşağıdaki değerlerden birine sahip olmalıdır:
  * yyyy yıl
  * s çeyreği
  * a ayı
  * Yılın y günü
  * d günü
  * b hafta Içi
  * WW haftası
  * h saati
  * n dakika
  * s saniye
* deeri Eklemek istediğiniz birim sayısı. Pozitif olabilir (gelecekte tarihleri almak için) veya negatif (geçmişte tarihleri almak için).
* güncel Aralığın eklendiği tarihi temsil eden tarih saat.

**Örnek:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
3 ay ekler ve "2001-04-01" temsil eden bir tarih saat döndürür.

---
### <a name="datefromnum"></a>Tarih Fromnum
**Açıklama:**  
DateFromNum işlevi, AD 'nin tarih biçimindeki bir değeri bir tarih saat türüne dönüştürür.

**Sözdizimi**  
`dt DateFromNum(num value)`

**Örnek:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
2012-01-01 23:00:00 temsil eden bir tarih saat döndürür

---
### <a name="dncomponent"></a>DNComponent
**Açıklama:**  
DNComponent işlevi, soldan bir belirtilen DN bileşeninin değerini döndürür.

**Sözdizimi**  
`str DNComponent(ref dn, num ComponentNumber)`

* DN: yorumlamaya yönelik başvuru özniteliği
* ComponentNumber: Verilecek DN 'deki bileşen

**Örnek:**  
`DNComponent(CRef([dn]),1)`  
DN "CN = ali, OU =..." ise, "ali döndürür

---
### <a name="dncomponentrev"></a>DNComponentRev
**Açıklama:**  
DNComponentRev işlevi, sağdan (bitiş) belirtilen bir DN bileşeni değerini döndürür.

**Sözdizimi**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* DN: yorumlamaya yönelik başvuru özniteliği
* ComponentNumber-döndürülecek DN 'deki bileşen
* Seçenekler: DC – "DC =" ile tüm bileşenleri yoksay

**Örnek:**  
DN "CN = ali, OU = Atlanta, OU = GA, OU = US, DC = contoso, DC = com" ise  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
Her ikisi de bıze döndürün.

---
### <a name="error"></a>Hata
**Açıklama:**  
Hata işlevi özel bir hata döndürmek için kullanılır.

**Sözdizimi**  
`void Error(str ErrorMessage)`

**Örnek:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
AccountName özniteliği yoksa, nesne üzerinde bir hata oluşturur.

---
### <a name="escapedncomponent"></a>Çıkar Edncomponent
**Açıklama:**  
Kaçış Edncomponent işlevi bir DN 'nin bir bileşenini alır ve LDAP 'de temsil edilebilmesi için onu çıkar.

**Sözdizimi**  
`str EscapeDNComponent(str value)`

**Örnek:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
DisplayName özniteliğinde, LDAP 'de kaçışması gereken karakterler olsa bile, nesnenin bir LDAP dizininde oluşturulabilse emin olur.

---
### <a name="formatdatetime"></a>formatDateTime
**Açıklama:**  
FormatDateTime işlevi, bir tarih/saati belirtilen biçime sahip bir dizeye biçimlendirmek için kullanılır

**Sözdizimi**  
`str FormatDateTime(dt value, str format)`

* değer: Tarih saat biçiminde bir değer
* Biçim: dönüştürülecek biçimi temsil eden bir dize.

**Açıklamalarının**  
Biçim için olası değerler burada bulunabilir: [Biçim işlevi Için özel tarih ve saat biçimleri](https://docs.microsoft.com/dax/custom-date-and-time-formats-for-the-format-function).

**Örnek:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
"2007-12-25" ile sonuçlanır.

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
"20140905081453.0 Z" ile sonuçlanabilir

---
### <a name="guid"></a>Guid
**Açıklama:**  
İşlev GUID 'Si yeni bir rastgele GUID oluşturur

**Sözdizimi**  
`str Guid()`

---
### <a name="iif"></a>MAYAN
**Açıklama:**  
IıF işlevi, belirli bir koşula göre olası bir değer kümesinden birini döndürür.

**Sözdizimi**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* koşul: doğru veya yanlış olarak değerlendirilebilen herhangi bir değer veya ifade.
* valueIfTrue: Koşul true olarak değerlendirilirse döndürülen değer.
* valueIfFalse: Koşul false olarak değerlendirilirse, döndürülen değer.

**Örnek:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Kullanıcı bir ınters ise, "t-" bir kullanıcının diğer adını döndürür. Bu, başka bir kullanıcının diğer adını olduğu gibi döndürür.

---
### <a name="instr"></a>InStr
**Açıklama:**  
InStr işlevi bir dizedeki alt dizenin ilk oluşumunu bulur

**Sözdizimi**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck: Aranacak dize
* stringmatch: bulunan dize
* Başlat: alt dizeyi bulmak için başlangıç konumu
* Compare: vbTextCompare veya vbBinaryCompare

**Açıklamalarının**  
Alt dizenin bulunduğu konumu veya bulunmazsa 0 değerini döndürür.

**Örnek:**  
`InStr("The quick brown fox","quick")`  
Evalues 'a 5

`InStr("repEated","e",3,vbBinaryCompare)`  
7 olarak değerlendirilir

---
### <a name="instrrev"></a>InStrRev
**Açıklama:**  
InStrRev işlevi bir dizedeki alt dizenin son oluşumunu bulur

**Sözdizimi**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: Aranacak dize
* stringmatch: bulunan dize
* Başlat: alt dizeyi bulmak için başlangıç konumu
* Compare: vbTextCompare veya vbBinaryCompare

**Açıklamalarının**  
Alt dizenin bulunduğu konumu veya bulunmazsa 0 değerini döndürür.

**Örnek:**  
`InStrRev("abbcdbbbef","bb")`  
7 döndürür

---
### <a name="isbitset"></a>IsBitSet
**Açıklama:**  
Bir bit ayarlandıysa veya belirtilmemişse işlev, test olarak ayarlandı

**Sözdizimi**  
`bool IsBitSet(num value, num flag)`

* değer: değerlendirilen sayısal bir değer. bayrak: değerlendirilecek biti olan sayısal bir değer

**Örnek:**  
`IsBitSet(&HF,4)`  
"F" onaltılık değerinde bit "4" ayarlandığı için true değerini döndürür

---
### <a name="isdate"></a>IsDate
**Açıklama:**  
İfade bir DateTime türü olarak değerlendirilüyorsa, IsDate işlevi true olarak değerlendirilir.

**Sözdizimi**  
`bool IsDate(var Expression)`

**Açıklamalarının**  
CDate () işleminin başarılı olup olmadığını anlamak için kullanılır.

---
### <a name="iscert"></a>IsCert
**Açıklama:**  
Ham veriler .NET X509Certificate2 Certificate nesnesine seri hale getirilebiliyorsa, true döndürür.

**Sözdizimi**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: X. 509.440 sertifikasının bayt dizisi temsili. Byte dizisi ikili (DER) kodlanmış veya Base64 ile kodlanmış X. 509.952 verileri olabilir.
---
### <a name="isempty"></a>IsEmpty
**Açıklama:**  
Özniteliği CS veya MV içinde varsa ancak boş bir dize olarak değerlendirilirse, IsEmpty işlevi true olarak değerlendirilir.

**Sözdizimi**  
`bool IsEmpty(var Expression)`

---
### <a name="isguid"></a>IsGuid
**Açıklama:**  
Dize bir GUID 'ye dönüştürülebiliyorsa, IsGuid işlevi true olarak değerlendirilir.

**Sözdizimi**  
`bool IsGuid(str GUID)`

**Açıklamalarının**  
GUID şu desenlerden birini izleyen bir dize olarak tanımlanır: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx veya {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

CGuid () işleminin başarılı olup olmadığını anlamak için kullanılır.

**Örnek:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
StrAttribute bir GUID biçimine sahipse, ikili bir temsili döndürün, aksi takdirde null döndürün.

---
### <a name="isnull"></a>IsNull
**Açıklama:**  
İfade null olarak değerlendirilirse, IsNull işlevi true döndürür.

**Sözdizimi**  
`bool IsNull(var Expression)`

**Açıklamalarının**  
Bir öznitelik için, bir null özniteliğin yokluğuna göre ifade edilir.

**Örnek:**  
`IsNull([displayName])`  
Öznitelik CS veya MV içinde yoksa, true döndürür.

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**Açıklama:**  
İfade null veya boş bir dize ise, IsNullOrEmpty işlevi true değerini döndürür.

**Sözdizimi**  
`bool IsNullOrEmpty(var Expression)`

**Açıklamalarının**  
Özniteliği için, öznitelik yoksa veya varsa ancak boş bir dize ise, bu true olarak değerlendirilir.  
Bu işlevin tersi ısun olarak adlandırılmıştır.

**Örnek:**  
`IsNullOrEmpty([displayName])`  
Öznitelik yoksa veya CS ya da MV içindeki boş bir dize ise true döndürür.

---
### <a name="isnumeric"></a>IsNumeric
**Açıklama:**  
IsNumeric işlevi, bir ifadenin sayı türü olarak değerlendirilemeyeceğini belirten bir Boole değeri döndürür.

**Sözdizimi**  
`bool IsNumeric(var Expression)`

**Açıklamalarının**  
CNum () ifadesinin ayrıştırılmasında başarılı olup olmadığını anlamak için kullanılır.

---
### <a name="isstring"></a>Isstrıng
**Açıklama:**  
İfade bir dize türü olarak değerlendirilebiliyorsa, IsString işlevi true olarak değerlendirilir.

**Sözdizimi**  
`bool IsString(var expression)`

**Açıklamalarının**  
CStr () ifadesinin ayrıştırmak için başarılı olup olmadığını belirlemekte kullanılır.

---
### <a name="ispresent"></a>Olmasına
**Açıklama:**  
İfade null olmayan ve boş olmayan bir dize olarak değerlendirilirse, ıssun işlevi true değerini döndürür.

**Sözdizimi**  
`bool IsPresent(var expression)`

**Açıklamalarının**  
Bu işlevin tersi IsNullOrEmpty olarak adlandırılmıştır.

**Örnek:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Öğe
**Açıklama:**  
Item işlevi, birden çok değerli dize/öznitelikten bir öğe döndürür.

**Sözdizimi**  
`var Item(mvstr attribute, num index)`

* öznitelik: çok değerli öznitelik
* Dizin: Çoklu değerli dizedeki bir öğenin dizini.

**Açıklamalarının**  
İkinci işlev, dizini çok değerli öznitelikteki bir öğeye döndürdüğünden, öğe işlevi Contains işleviyle birlikte kullanışlıdır.

Dizin sınırların dışında olduğunda bir hata oluşturur.

**Örnek:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Birincil e-posta adresini döndürür.

---
### <a name="itemornull"></a>Imornull
**Açıklama:**  
ItemOrNull işlevi, birden çok değerli dize/öznitelikten bir öğe döndürür.

**Sözdizimi**  
`var ItemOrNull(mvstr attribute, num index)`

* öznitelik: çok değerli öznitelik
* Dizin: Çoklu değerli dizedeki bir öğenin dizini.

**Açıklamalarının**  
İkinci işlev, dizini çok değerli öznitelikteki bir öğeye döndürdüğünden, ItemOrNull işlevi Contains işleviyle birlikte kullanışlıdır.

Dizin sınırların dışında ise null değeri döndürür.

---
### <a name="join"></a>Birleştir
**Açıklama:**  
JOIN işlevi, birden çok değerli bir dize alır ve her öğe arasında belirtilen ayırıcı ile tek değerli bir dize döndürür.

**Sözdizimi**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* özniteliğe Birleştirilecek dizeleri içeren çok değerli öznitelik.
* ayırıcı Döndürülen dizedeki alt dizeleri ayırmak için kullanılan herhangi bir dize. Atlanırsa, boşluk karakteri ("") kullanılır. Sınırlayıcı sıfır uzunluklu bir dize ("") veya Nothing ise, listedeki tüm öğeler sınırlayıcı olmadan birleştirilir.

**Açıklamalar**  
JOIN ve Split işlevleri arasında eşlik vardır. JOIN işlevi, tek bir dize döndürmek için bir dizi dizeyi alır ve bunları bir sınırlayıcı dize kullanarak birleştirir. Split işlevi bir dize alır ve bir dize dizisi döndürecek şekilde sınırlayıcıda ayırır. Bununla birlikte, bir temel fark, birleştirmenin herhangi bir sınırlayıcı dizeyle dizeleri birleştireceği ve yalnızca tek bir karakter sınırlayıcısı kullanarak dizeleri ayırabilirler.

**Örnek:**  
`Join([proxyAddresses],",")`  
Şunu döndürebilir: "SMTP:john.doe@contoso.com,smtp:jd@contoso.com"

---
### <a name="lcase"></a>LCase
**Açıklama:**  
LCase işlevi bir dizedeki tüm karakterleri küçük harfe dönüştürür.

**Sözdizimi**  
`str LCase(str value)`

**Örnek:**  
`LCase("TeSt")`  
"Test" döndürür.

---
### <a name="left"></a>Sol
**Açıklama:**  
Left işlevi bir dizenin sol tarafında belirtilen sayıda karakteri döndürür.

**Sözdizimi**  
`str Left(str string, num NumChars)`

* dize: karakterlerin döndürdüğü dize
* NumChars: dizenin başından (solda) döndürülecek karakter sayısını tanımlayan sayı

**Açıklamalarının**  
Dizedeki ilk Numchar karakterlerini içeren bir dize:

* NumChars = 0 ise boş bir dize döndürür.
* NumChars 0 <, giriş dizesi döndürür.
* Dize null ise boş dize döndürün.

Dize Numchar 'lar içinde belirtilen sayıdan daha az karakter içeriyorsa, dize ile özdeş bir dize (yani, 1 parametresindeki tüm karakterleri içeren) döndürülür.

**Örnek:**  
`Left("John Doe", 3)`  
"Joh" döndürür.

---
### <a name="len"></a>Tepe
**Açıklama:**  
Len işlevi bir dizedeki karakter sayısını döndürür.

**Sözdizimi**  
`num Len(str value)`

**Örnek:**  
`Len("John Doe")`  
8 döndürür

---
### <a name="ltrim"></a>LTrim
**Açıklama:**  
LTrim işlevi bir dizeden öndeki boşlukları kaldırır.

**Sözdizimi**  
`str LTrim(str value)`

**Örnek:**  
`LTrim(" Test ")`  
"Test" döndürür

---
### <a name="mid"></a>Orta
**Açıklama:**  
Mid işlevi, bir dizedeki belirli bir konumdan belirtilen sayıda karakteri döndürür.

**Sözdizimi**  
`str Mid(str string, num start, num NumChars)`

* dize: karakterlerin döndürdüğü dize
* başlangıç: dizeden karakter döndürecek başlangıç konumunu tanımlayan sayı
* NumChars: dizedeki konumdan döndürülecek karakter sayısını tanımlayan sayı

**Açıklamalarının**  
Dizeden başlangıç konumundan başlayarak numChars karakterlerini döndürün.  
Konumdan başlangıç dizesinde numChars karakter içeren bir dize:

* NumChars = 0 ise boş bir dize döndürür.
* NumChars 0 <, giriş dizesi döndürür.
* Başlangıç > dize uzunluğunu, dönüş giriş dizesi.
* Başlangıç < = 0 ise, giriş dizesi döndürür.
* Dize null ise boş dize döndürün.

Konumda, mümkün olan çok sayıda karakter döndürüldüğü için konumdan başlangıç dizesinde kalan numChar karakteri yoksa.

**Örnek:**  
`Mid("John Doe", 3, 5)`  
"Hn do" döndürür.

`Mid("John Doe", 6, 999)`  
"Tikan" döndürür

---
### <a name="now"></a>Şimdi
**Açıklama:**  
Now işlevi, bilgisayarınızın sistem tarih ve saatine göre geçerli tarih ve saati belirten bir tarih saat döndürür.

**Sözdizimi**  
`dt Now()`

---
### <a name="numfromdate"></a>NumFromDate
**Açıklama:**  
NumFromDate işlevi, AD 'nin tarih biçiminde bir tarih döndürür.

**Sözdizimi**  
`num NumFromDate(dt value)`

**Örnek:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
129699324000000000 döndürür

---
### <a name="padleft"></a>Asma sol
**Açıklama:**  
Asma sol işlevi, bir dizeyi belirtilen bir doldurma karakteri kullanarak belirli bir uzunluğa bıraktı.

**Sözdizimi**  
`str PadLeft(str string, num length, str padCharacter)`

* dize: pad olarak kullanılacak dize.
* uzunluklu İstenen dize uzunluğunu temsil eden bir tamsayı.
* Asma karakteri: Doldurma karakteri olarak kullanılacak tek bir karakterden oluşan bir dize

**Açıklamalarının**

* Dize uzunluğu uzunluğundan küçükse, bir uzunluk uzunluğuna eşit olana kadar, asma karakteri tekrar tekrar dizenin başlangıcına (sola) eklenir.
* Asma karakteri bir boşluk karakteri olabilir, ancak null bir değer olamaz.
* Dize uzunluğu uzunluğuna eşit veya daha büyük ise, dize değiştirilmeden döndürülür.
* Dize uzunluğuna eşit veya daha büyük bir uzunluğa sahipse dize ile özdeş bir dize döndürülür.
* Dize uzunluğu uzunluğundan küçükse, istenen uzunlukta yeni bir dize, bir asma karakteriyle doldurulmuş dize içeren döndürülür.
* Dize null ise, işlev boş bir dize döndürür.

**Örnek:**  
`PadLeft("User", 10, "0")`  
"000000User" döndürür.

---
### <a name="padright"></a>PadRight
**Açıklama:**  
Asma sağ işlevi, bir dizeyi belirtilen bir doldurma karakteri kullanarak belirtilen uzunluğa sağ tablası.

**Sözdizimi**  
`str PadRight(str string, num length, str padCharacter)`

* dize: pad olarak kullanılacak dize.
* uzunluklu İstenen dize uzunluğunu temsil eden bir tamsayı.
* Asma karakteri: Doldurma karakteri olarak kullanılacak tek bir karakterden oluşan bir dize

**Açıklamalarının**

* Dize uzunluğu uzunluğundan küçükse, bir uzunluk uzunluğuna eşit olana kadar, asma karakteri tekrar dizenin sonuna (sağına) eklenir.
* Asma karakteri bir boşluk karakteri olabilir, ancak null bir değer olamaz.
* Dize uzunluğu uzunluğuna eşit veya daha büyük ise, dize değiştirilmeden döndürülür.
* Dize uzunluğuna eşit veya daha büyük bir uzunluğa sahipse dize ile özdeş bir dize döndürülür.
* Dize uzunluğu uzunluğundan küçükse, istenen uzunlukta yeni bir dize, bir asma karakteriyle doldurulmuş dize içeren döndürülür.
* Dize null ise, işlev boş bir dize döndürür.

**Örnek:**  
`PadRight("User", 10, "0")`  
"User000000" döndürür.

---
### <a name="pcase"></a>PCase
**Açıklama:**  
PCase işlevi bir dizedeki her bir boşlukla ayrılmış sözcüğün ilk karakterini büyük harflere dönüştürür ve diğer tüm karakterler küçük harfe dönüştürülür.

**Sözdizimi**  
`String PCase(string)`

**Açıklamalarının**

* Bu işlev, bir kısaltma gibi tamamen büyük harfli bir kelimeyi dönüştürmek için şu anda uygun bir büyük harf sağlamıyor.

**Örnek:**  
`PCase("TEsT")`  
"Test" döndürür.

`PCase(LCase("TEST"))`  
"Test" döndürür

---
### <a name="randomnum"></a>Rasgelenum
**Açıklama:**  
Rasgelenum işlevi, belirtilen Aralık arasında rastgele bir sayı döndürür.

**Sözdizimi**  
`num RandomNum(num start, num end)`

* başlangıç: oluşturulacak rastgele değerin alt sınırını tanımlayan bir sayı
* End: oluşturulacak rastgele değerin üst sınırını tanımlayan sayı

**Örnek:**  
`Random(100,999)`  
734 döndürebilir.

---
### <a name="removeduplicates"></a>RemoveDuplicates
**Açıklama:**  
Removeyinelemelerini işlevi, birden çok değerli dizeyi alır ve her değerin benzersiz olduğundan emin olur.

**Sözdizimi**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Örnek:**  
`RemoveDuplicates([proxyAddresses])`  
Tüm yinelenen değerlerin kaldırıldığı bir ayıklanmış proxyAddress özniteliği döndürür.

---
### <a name="replace"></a>Değiştir
**Açıklama:**  
Replace işlevi bir dizenin tüm oluşumlarını başka bir dizeye koyar.

**Sözdizimi**  
`str Replace(str string, str OldValue, str NewValue)`

* dizisinde İçindeki değerleri değiştirmek için bir dize.
* OldValue Aranacak ve değiştirilecek dize.
* Değer Değiştirilecek dize.

**Açıklamalarının**  
İşlevi aşağıdaki özel adları tanır:

* \n – yeni satır
* \r – satır başı
* \t – sekme

**Örnek:**  
`Replace([address],"\r\n",", ")`  
CRLF 'yi bir virgül ve boşluk ile değiştirir ve "One Microsoft Way, Redmond, WA, ABD" ile sonuçlanabiliyor

---
### <a name="replacechars"></a>ReplaceChars
**Açıklama:**  
ReplaceChars işlevi, Replacemodel dizesinde bulunan tüm karakter tekrarlamalarını değiştirir.

**Sözdizimi**  
`str ReplaceChars(str string, str ReplacePattern)`

* dizisinde İçindeki karakterlerin yerini alacak bir dize.
* Replacemodel: değiştirilecek karakterlerle bir sözlük içeren bir dize.

Biçim {source1}: {Target1}, {SOURCE2}: {Target2}, {Sourgothic}, {targetN}; burada kaynak, ile değiştirilecek dizeyi bulmak ve hedeflemek için gereken karakterdir.

**Açıklamalarının**

* İşlevi, tanımlanan kaynakların her oluşumunu alır ve bunları hedeflerle değiştirir.
* Kaynak tam olarak bir (Unicode) karakter olmalıdır.
* Kaynak boş veya bir karakterden uzun olamaz (ayrıştırma hatası).
* Hedefte birden çok karakter olabilir, örneğin ö: OE, β: ss.
* Hedef, karakterin kaldırılması gerektiğini belirten boş olabilir.
* Kaynak, büyük/küçük harfe duyarlıdır ve tam eşleşme olmalıdır.
* , (Virgül) ve: (iki nokta üst üste) ayrılmış karakterlerdir ve bu işlev kullanılarak değiştirilemez.
* Replacemodel dizesindeki boşluklar ve diğer beyaz karakterler yok sayılır.

**Örnek:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Raksmorgas döndürür

`ReplaceChars("O’Neil",%ReplaceString%)`  
"Oneıl" döndürür, tek çentik kaldırılacak şekilde tanımlanır.

---
### <a name="right"></a>Sağ
**Açıklama:**  
Sağ işlev, bir dizenin sağından (bitiş) belirtilen sayıda karakteri döndürür.

**Sözdizimi**  
`str Right(str string, num NumChars)`

* dize: karakterlerin döndürdüğü dize
* NumChars: dizenin sonundan (sağda) döndürülecek karakter sayısını tanımlayan sayı

**Açıklamalarının**  
NumChars karakterleri dizenin son konumundan döndürülür.

Dizedeki son numChars karakterlerini içeren bir dize:

* NumChars = 0 ise boş bir dize döndürür.
* NumChars 0 <, giriş dizesi döndürür.
* Dize null ise boş dize döndürün.

Dize Numchar 'lar içinde belirtilen sayıdan daha az karakter içeriyorsa, String ile özdeş bir dize döndürülür.

**Örnek:**  
`Right("John Doe", 3)`  
"Tikan" döndürür.

---
### <a name="rtrim"></a>RTrim
**Açıklama:**  
RTrim işlevi bir dizeden sondaki boşlukları kaldırır.

**Sözdizimi**  
`str RTrim(str value)`

**Örnek:**  
`RTrim(" Test ")`  
"Test" döndürür.

---
### <a name="select"></a>Seçim
**Açıklama:**  
Belirtilen işleve bağlı olarak, birden çok değerli bir öznitelik (veya bir ifadenin çıktısı) içindeki tüm değerleri işleyin.

**Sözdizimi**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* maddesinin Çoklu değerli öznitelikteki bir öğeyi temsil eder
* öznitelik: çok değerli öznitelik
* ifade: bir değer koleksiyonu döndüren bir ifade
* koşul: öznitelikte bir öğeyi işleyebilirler herhangi bir işlev

**Örnekler:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Kısa çizgiler (-) kaldırıldıktan sonra, çok değerli öznitelikteki tüm değerleri, diğer telefondaki bir değere döndürün.

---
### <a name="split"></a>Böl
**Açıklama:**  
Split işlevi, sınırlayıcıyla ayrılmış bir dize alır ve bunu çok değerli bir dize yapar.

**Sözdizimi**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* değer: ayırıcı karakter ayırmak için bir dize.
* sınırlayıcı: sınırlayıcı olarak kullanılacak tek karakter.
* limit: döndürebilirler maksimum değer sayısı.

**Örnek:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
ProxyAddress özniteliği için yararlı 2 öğesi olan çok değerli bir dize döndürür.

---
### <a name="stringfromguid"></a>StringFromGuid
**Açıklama:**  
StringFromGuid işlevi bir ikili GUID alır ve bir dizeye dönüştürür

**Sözdizimi**  
`str StringFromGuid(bin GUID)`

---
### <a name="stringfromsid"></a>StringFromSid
**Açıklama:**  
StringFromSid işlevi, bir güvenlik tanımlayıcısı içeren bir bayt dizisini dizeye dönüştürür.

**Sözdizimi**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="switch"></a>Anahtar
**Açıklama:**  
Switch işlevi, değerlendirilen koşullara göre tek bir değer döndürmek için kullanılır.

**Sözdizimi**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* ifadeyi Değerlendirmek istediğiniz varyant ifadesi.
* deeri Karşılık gelen ifade doğru ise döndürülecek değer.

**Açıklamalarının**  
Switch işlevi bağımsız değişken listesi, ifade ve değer çiftlerinden oluşur. İfadeler soldan sağa değerlendirilir ve true olarak değerlendirilecek ilk ifadeyle ilişkili değer döndürülür. Parçalar düzgün şekilde eşleştirilmemişse, bir çalışma zamanı hatası oluşur.

Örneğin, expr1 true ise, anahtar değer1 döndürür. Expr-1 false ise, ancak Expr-2 true ise, Switch değeri-2 değerini döndürür ve bu şekilde devam eder.

Anahtar şu durumlarda bir şey döndürmez:

* Deyimlerden hiçbiri doğru değil.
* İlk true ifadesinde karşılık gelen bir değer null.

Yalnızca birini döndürse de, anahtar tüm ifadeleri değerlendirir. Bu nedenle, istenmeyen yan etkileri için izlemeniz gerekir. Örneğin, herhangi bir ifadenin değerlendirmesi sıfıra bölme hatasıyla sonuçlanırsa bir hata oluşur.

Değer aynı zamanda bir özel dize döndüren Error işlevi olabilir.

**Örnek:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Bazı önemli şehirlerde konuşulan dili döndürür, aksi takdirde bir hata döndürür.

---
### <a name="trim"></a>Kırp
**Açıklama:**  
Trim işlevi bir dizeden baştaki ve sondaki boşlukları kaldırır.

**Sözdizimi**  
`str Trim(str value)`  

**Örnek:**  
`Trim(" Test ")`  
"Test" döndürür.

`Trim([proxyAddresses])`  
ProxyAddress özniteliğinde her bir değer için öndeki ve sondaki boşlukları kaldırır.

---
### <a name="ucase"></a>UCase
**Açıklama:**  
UCase işlevi bir dizedeki tüm karakterleri büyük harfe dönüştürür.

**Sözdizimi**  
`str UCase(str string)`

**Örnek:**  
`UCase("TeSt")`  
"TEST" döndürür.

---
### <a name="where"></a>Konum

**Açıklama:**  
Belirli bir koşula bağlı olarak, birden çok değerli bir öznitelikten (veya bir ifadenin çıktısından) değerlerin bir alt kümesini döndürür.

**Sözdizimi**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* maddesinin Çoklu değerli öznitelikteki bir öğeyi temsil eder
* öznitelik: çok değerli öznitelik
* koşul: doğru veya yanlış olarak değerlendirilebilen herhangi bir ifade
* ifade: bir değer koleksiyonu döndüren bir ifade

**Örnek:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
Sertifika değerlerini, zaman aşımına ermeyen çok değerli öznitelik userCertificate değerine döndürün.

---
### <a name="with"></a>Şununla birlikte:
**Açıklama:**  
WITH işlevi, karmaşık ifadede bir veya daha fazla kez görüntülenen alt ifadeyi temsil eden bir değişkeni kullanarak karmaşık bir ifadeyi basitleştirmek için bir yol sağlar.

**Sözdizimi**
`With(var variable, exp subExpression, exp complexExpression)`  
* değişken Alt ifadeyi temsil eder.
* alt Ifade: değişkene göre temsil edilen alt ifade.
* complexExpression: Karmaşık bir ifade.

**Örnek:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
İşlevsel olarak eşdeğerdir:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
Bu, userCertificate özniteliğinde yalnızca geçerliliği olmayan sertifika değerlerini döndürür.


---
### <a name="word"></a>Word
**Açıklama:**  
Word işlevi, kullanılacak sınırlayıcıları ve döndürülecek sözcük numarasını açıklayan parametrelere göre dize içinde içerilen bir sözcük döndürür.

**Sözdizimi**  
`str Word(str string, num WordNumber, str delimiters)`

* String: bir sözcüğün döndürdüğü dize.
* WordNumber: hangi sözcük sayısının dönmesi gerektiğini belirleyen bir sayı.
* Sınırlayıcılar: sözcükleri tanımlamak için kullanılması gereken sınırlayıcıları temsil eden bir dize

**Açıklamalarının**  
Sınırlayıcıdaki karakterlerden biri tarafından ayrılan dizedeki her karakter dizesi, sözcük olarak tanımlanır:

* Sayı < 1 ise boş bir dize döndürür.
* Dize null ise, boş bir dize döndürür.

Dize sayı olan sözcüklerden daha az sözcük içeriyorsa veya dize sınırlayıcılar tarafından tanımlanan herhangi bir sözcük içermiyorsa, boş bir dize döndürülür.

**Örnek:**  
`Word("The quick brown fox",3," ")`  
"Kahverengi" döndürür

`Word("This,string!has&many separators",3,",!&#")`  
"Sahip" döndürmelidir

## <a name="additional-resources"></a>Ek Kaynaklar
* [Bildirim temelli sağlama Ifadelerini anlama](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Azure AD Connect eşitleme: Eşitleme seçeneklerini özelleştirme](how-to-connect-sync-whatis.md)
* [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md)
