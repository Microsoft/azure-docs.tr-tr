---
title: Azure Active Directory'de öznitelik eşlemeleri için ifadeler yazma | Microsoft Docs
description: İfade eşlemeleri otomatik Azure Active Directory'de SaaS uygulama nesnelerin sağlama sırasında öznitelik değerleri kabul edilebilir bir biçime dönüştürmek için kullanmayı öğrenin.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd7abdeef7c13c272a0e4bbf2075c6eda8f73a07
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162389"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Azure Active Directory'de öznitelik eşlemeleri için ifadeler yazma
Bir SaaS uygulaması için sağlama yapılandırdığınızda, belirtebilmeniz için öznitelik eşlemelerini türdeki bir ifade eşleme biridir. Bu, kullanıcılarınızın verileri fazla SaaS uygulaması için kabul edilebilir biçimlere dönüştürme olanak tanıyan bir betik gibi ifade yazmanız gerekir.

## <a name="syntax-overview"></a>Söz dizimi genel bakış
Öznitelik eşlemeleri için ifadeler sözdizimi Applications (VBA) işlevleri için Visual Basic reminiscent aşağıdaki gibidir.

* Tüm ifade İşlevler, bağımsız değişkenleri parantez içinde bir adından oluşur bakımından tanımlanmış olması gerekir: <br>
  *Fonksiyonadı (`<<argument 1>>`,`<<argument N>>`)*
* İçindeki diğer işlevleri iç içe. Örneğin: <br> *FunctionOne (FunctionTwo (`<<argument1>>`))*
* İşlevlere üç farklı türde bağımsız değişkenler geçirebilirsiniz:
  
  1. Öznitelik, köşeli ayraçlar içine alınmalıdır. Örneğin: [attributeName]
  2. Dize sabitleri çift tırnak içine alınmalıdır. Örneğin: "Birleşik Devletler"
  3. Diğer işlevler. Örneğin: Functionone (`<<argument1>>`, functiontwo (`<<argument2>>`))
* Dize sabitleri için bir ters eğik çizgi (\) veya tırnak işareti (") dizedeki gerekiyorsa, eğik çizgi (\) simgesiyle kaçınılmalıdır. Örneğin: "Şirket adı: \\"Contoso\\" "

## <a name="list-of-functions"></a>İşlevlerin listesi
[Sona Ekle](#append) &nbsp; [FormatDateTime](#formatdatetime) [](#join) birleşimi &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [](#not) [](#mid) &nbsp; Mnormalizediactik [](#normalizediacritics) dili &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [Selectuniquevalue değerini](#selectuniquevalue) [değiştirme](#replace) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [ Singleapprotaatama](#singleapproleassignment) &nbsp; &nbsp; &nbsp; [](#stripspaces) [](#split)bölünmüş StripSpaces&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp; &nbsp; &nbsp; Aşağıgeç&nbsp; [](#switch) &nbsp; &nbsp; &nbsp; [](#tolower) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [ToUpper](#toupper)

---
### <a name="append"></a>Ekle
**İşlev:**<br> Append(Source, Suffix)

**Açıklama:**<br> Kaynak dize değerini alır ve soneki, sonuna ekler.

**Parametreler:**<br> 

| Ad | Gerekli / yinelenen | Tür | Notlar |
| --- | --- | --- | --- |
| **Kaynak** |Gerekli |Dize |Genellikle kaynak nesneden özniteliğin adı. |
| **suffix** |Gerekli |Dize |Kaynak değerin sonuna istediğiniz dize. |

---
### <a name="formatdatetime"></a>formatDateTime
**İşlev:**<br> FormatDateTime (kaynak, inputFormat, outputFormat)

**Açıklama:**<br> Bir tarih dizesini bir biçimden alır ve farklı bir biçime dönüştürür.

**Parametreler:**<br> 

| Ad | Gerekli / yinelenen | Tür | Notlar |
| --- | --- | --- | --- |
| **Kaynak** |Gerekli |Dize |Genellikle kaynak nesneden özniteliğin adı. |
| **inputFormat** |Gerekli |Dize |Kaynak değeri beklenen biçimi. Desteklenen biçimler için bkz: [ https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx ](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Gerekli |Dize |Çıkış tarih biçimi. |

---
### <a name="join"></a>Birleştir
**İşlev:**<br> Birleştirme (ayırıcı, kaynak1, kaynak2,...)

**Açıklama:**<br> Birden çok birleştirebilirsiniz dışında join() Append() için benzer **kaynak** dize değerleri tek bir dize olarak ve her bir değeri ile ayrılmış bir **ayırıcı** dize.

Kaynak değerlerinden biri çok değerli bir öznitelik ise, bu öznitelikteki her değer, ayırıcı değeriyle ayırarak birlikte birleştirilir.

**Parametreler:**<br> 

| Ad | Gerekli / yinelenen | Tür | Notlar |
| --- | --- | --- | --- |
| **Ayırıcı** |Gerekli |Dize |Kaynak değerler bir dizeye birleştirilmiş, ayırmak için kullanılan dize. Olabilir "" ayırıcı gerekiyorsa. |
| **... kaynak1 kaynakN** |Değişken-sayısı gerekli |Dize |Değerleri birlikte birleştirilecek dize. |

---
### <a name="mid"></a>Orta
**İşlev:**<br> Mid (kaynak, başlangıç, uzunluk)

**Açıklama:**<br> Kaynak değerin bir alt dizeyi döndürür. Bir alt dizenin yalnızca bazı kaynak dizeden karakterleri içeren bir dizedir.

**Parametreler:**<br> 

| Ad | Gerekli / yinelenen | Tür | Notlar |
| --- | --- | --- | --- |
| **Kaynak** |Gerekli |Dize |Genellikle özniteliğin adı. |
| **start** |Gerekli |integer |İçinde dizin **kaynak** alt dizenin başladığı dize. Dizedeki ilk karakter dizini 1 gerekir, ikinci karakter 2 dizine sahip ve benzeri. |
| **Uzunluğu** |Gerekli |integer |Alt dizenin uzunluğu. Uzunluğu dışında sona ererse **kaynak** dize işlevi alt dizeyi döndürecektir **Başlat** sonuna kadar dizin **kaynak** dize. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**İşlev:**<br> NormalizeDiacritics(source)

**Açıklama:**<br> Bir dize bağımsız değişkeni gerektirir. Dizeyi döndürür, ancak tüm Aksan karakteriyle değiştirildiğini aksanlı eşdeğer karakterlerle. Genellikle kullanıcı asıl adı, SAM hesap adlarını ve e-posta adresleri gibi çeşitli kullanıcı tanımlayıcılarını kullanılabilir yasal değerlerine adlarını ve soyadlarını aksanlı karakterleri (vurgu işaretleri) içeren dönüştürmek için kullanılır.

**Parametreler:**<br> 

| Ad | Gerekli / yinelenen | Tür | Notlar |
| --- | --- | --- | --- |
| **Kaynak** |Gerekli |Dize | Genellikle ad veya soyadı özniteliği. |

---
### <a name="not"></a>değil
**İşlev:**<br> Not(Source)

**Açıklama:**<br> Boole değeri çevirir **kaynak**. Varsa **kaynak** değeridir "*True*", döndürür "*False*". Aksi halde döndürür "*True*".

**Parametreler:**<br> 

| Ad | Gerekli / yinelenen | Tür | Notlar |
| --- | --- | --- | --- |
| **Kaynak** |Gerekli |Boole dizesi |Beklenen **kaynak** değerleri "true" veya "false" şeklindedir. |

---
### <a name="replace"></a>Değiştir
**İşlev:**<br> Değiştir (kaynak, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, şablon)

**Açıklama:**<br>
Bir dize içindeki değerleri değiştirir. Sağlanan parametreler bağlı olarak farklı şekilde çalışır:

* Zaman **oldValue** ve **replacementValue** sağlanır:
  
  * **Kaynaktaki** tüm **OldValue** yinelemelerini **replacementvalue** ile değiştirir
* Zaman **oldValue** ve **şablon** sağlanır:
  
  * Tüm oluşumlarını değiştirir **oldValue** içinde **şablon** ile **kaynak** değeri
* **Regexmodel** ve **replacementvalue** sağlandığında:

  * İşlevi, **Regexmodel** öğesini **kaynak** dizeye uygular ve **replacementvalue** için dizeyi oluşturmak üzere Regex grup adlarını kullanabilirsiniz
* Zaman **regexPattern**, **regexGroupName**, **replacementValue** sağlanır:
  
  * İşlev, **kaynak** dizeye **regexmodel** uygular ve **regexgroupname** Ile eşleşen tüm değerleri **replacementvalue** ile değiştirir
* **Regexgroupname**, **replacementAttributeName** sağlandığında:
  
  * Varsa **kaynak** değeri **kaynak** döndürülür
  * **Kaynakta** bir değer varsa, Işlev **regexmodel** öğesini **kaynak** dizeye uygular ve **Regexgroupname** ile eşleşen tüm değerleri **replacementAttributeName** ile ilişkili değerle değiştirir

**Parametreler:**<br> 

| Ad | Gerekli / yinelenen | Tür | Notlar |
| --- | --- | --- | --- |
| **Kaynak** |Gerekli |Dize |Genellikle **kaynak** nesneden özniteliğin adı. |
| **oldValue** |İsteğe bağlı |Dize |İçinde değiştirilecek değer **kaynak** veya **şablon**. |
| **regexPattern** |İsteğe bağlı |Dize |Regex deseni olarak değiştirilecek değeri **kaynak**. Ya da **Replacementpropertyname** kullanıldığında, **replacementpropertyname**öğesinden değer çıkarmak için bir model. |
| **regexGroupName** |İsteğe bağlı |Dize |Grup içinde adını **regexPattern**. Yalnızca **Replacementpropertyname** kullanıldığında, bu grubun değerini **replacementpropertyname**öğesinden **replacementvalue** olarak ayıklayacağız. |
| **replacementValue** |İsteğe bağlı |Dize |Eski değiştirmek için yeni değeri. |
| **replacementAttributeName** |İsteğe bağlı |Dize |Değiştirme değeri için kullanılacak özniteliğin adı |
| **Şablonu** |İsteğe bağlı |Dize |**Şablon** değeri sağlandığında, şablon içinde **OldValue** aranacağı ve bunu **kaynak** değerle değiştirecek. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**İşlev:**<br> SelectUniqueValue (uniqueValueRule1, uniqueValueRule2, uniqueValueRule3,...)

**Açıklama:**<br> En az benzersiz değer oluşturma kuralları ifadeleri kullanılarak tanımlanmış olan iki bağımsız değişken gerektirir. İşlev, her kural değerlendirir ve ardından hedef uygulama/dizinindeki benzersizlik için oluşturulan değeri denetler. Bir döndürülen ilk benzersiz değeri bulundu. Tüm değerleri hedefte zaten varsa, giriş kalacakları ve nedeni denetim günlüklerinde günlüğe. Sağlanan bağımsız değişken sayısı için üst sınır yoktur.

> [!NOTE]
> - Bu üst düzey bir işlev, iç içe olamaz.
> - Bu işlev, eşleşen önceliği olan özniteliklere uygulanamaz.  
> - Bu işlev yalnızca girişi oluşturma için kullanılmak üzere tasarlanmıştır. Bir öznitelik ile kullanırken, ayarlayın **uygulamak eşleme** özelliğini **yalnızca nesne oluşturma sırasında**.
> - Bu işlev şu anda yalnızca "Workday for User sağlamasını Active Directory" için desteklenir. Diğer sağlama uygulamalarıyla birlikte kullanılamaz. 


**Parametreler:**<br> 

| Ad | Gerekli / yinelenen | Tür | Notlar |
| --- | --- | --- | --- |
| **... uniqueValueRule1 uniqueValueRuleN** |En az 2 gerekli, en üst sınır. |Dize | Değerlendirilecek benzersiz değer oluşturma kurallarının listesi. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**İşlev:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Açıklama:**<br> Belirli bir uygulama için kullanıcıya atanan tüm Approtaatamalar listesinden tek bir Approtaatama döndürür. Approtaatamalar nesnesini tek bir rol adı dizesine dönüştürmek için bu işlev gereklidir. En iyi yöntem, tek seferde bir kullanıcıya yalnızca bir Approelatama atandığından emin olmak ve birden çok rol atanmamışsa döndürülen rol dizesinin tahmin edilebilir olamayacağını unutmayın. 

**Parametreler:**<br> 

| Ad | Gerekli / yinelenen | Tür | Notlar |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Gerekli |Dize |**[appRoleAssignments]**  nesne. |

---
### <a name="split"></a>Böl
**İşlev:**<br> Böl (kaynak, sınırlayıcı)

**Açıklama:**<br> Belirtilen sınırlayıcı karakteri kullanarak bir dizeyi çok değerli bir diziye böler.

**Parametreler:**<br> 

| Ad | Gerekli / yinelenen | Tür | Notlar |
| --- | --- | --- | --- |
| **Kaynak** |Gerekli |Dize |**Kaynak** güncelleştirmek için değer. |
| **ayırıcı** |Gerekli |Dize |Dizeyi ayırmak için kullanılacak karakteri belirtir (örneğin: ",") |

---
### <a name="stripspaces"></a>StripSpaces
**İşlev:**<br> StripSpaces(source)

**Açıklama:**<br> Tüm alan kaldırır ("") karakter kaynak dizesi.

**Parametreler:**<br> 

| Ad | Gerekli / yinelenen | Tür | Notlar |
| --- | --- | --- | --- |
| **Kaynak** |Gerekli |Dize |**Kaynak** güncelleştirmek için değer. |

---
### <a name="switch"></a>Anahtar
**İşlev:**<br> Anahtar (kaynak, defaultValue, key1, value1, key2, value2,...)

**Açıklama:**<br> Zaman **kaynak** değeri eşleşen bir **anahtarı**, döndürür **değer** söz konusu **anahtar**. Varsa **kaynak** değeri döndürür bir anahtarı eşleşmiyor **defaultValue**.  **Anahtar** ve **değer** parametreleri her zaman çiftler halinde gelmelidir. İşlev her zaman bir çift sayı parametre bekliyor.

**Parametreler:**<br> 

| Ad | Gerekli / yinelenen | Tür | Notlar |
| --- | --- | --- | --- |
| **Kaynak** |Gerekli |Dize |**Kaynak** güncelleştirmek için değer. |
| **defaultValue** |İsteğe bağlı |Dize |Tüm anahtarları kaynak eşleşmediğinde kullanılacak varsayılan değeri. Boş bir dize olabilir (""). |
| **anahtar** |Gerekli |Dize |**Anahtar** Karşılaştırılacak **kaynak** ile değeri. |
| **value** |Gerekli |Dize |İçin değiştirme değeri **kaynak** anahtarıyla eşleşen. |

---
### <a name="tolower"></a>toLower
**İşlev:**<br> ToLower (kaynak, kültür)

**Açıklama:**<br> Bir *kaynak* dize değeri alır ve belirtilen kültür kurallarını kullanarak küçük harfe dönüştürür. Belirtilen bir *kültür* bilgisi yoksa, sabit kültür kullanacaktır.

**Parametreler:**<br> 

| Ad | Gerekli / yinelenen | Tür | Notlar |
| --- | --- | --- | --- |
| **Kaynak** |Gerekli |Dize |Genellikle kaynak nesneden özniteliğin adı |
| **ayarı** |İsteğe Bağlı |Dize |RFC 4646 ' i temel alan kültür adı biçimi *languagecode2-Country/regioncode2*, burada *languagecode2* iki harfli dil kodu ve *Ülke/regioncode2* ise iki harfli alt kültür kodudur. Japonca (Japonya) için ja-JP ve Ingilizce (Birleşik Devletler) için en-US sayılabilir. İki harfli dil kodunun kullanılamadığı durumlarda ISO 639-2 ' den türetilen üç harfli bir kod kullanılır.|

---
### <a name="toupper"></a>toUpper
**İşlev:**<br> ToUpper (kaynak, kültür)

**Açıklama:**<br> Bir *kaynak* dize değeri alır ve belirtilen kültür kurallarını kullanarak büyük harfe dönüştürür. Belirtilen bir *kültür* bilgisi yoksa, sabit kültür kullanacaktır.

**Parametreler:**<br> 

| Ad | Gerekli / yinelenen | Tür | Notlar |
| --- | --- | --- | --- |
| **Kaynak** |Gerekli |Dize |Genellikle kaynak nesneden özniteliğin adı. |
| **ayarı** |İsteğe Bağlı |Dize |RFC 4646 ' i temel alan kültür adı biçimi *languagecode2-Country/regioncode2*, burada *languagecode2* iki harfli dil kodu ve *Ülke/regioncode2* ise iki harfli alt kültür kodudur. Japonca (Japonya) için ja-JP ve Ingilizce (Birleşik Devletler) için en-US sayılabilir. İki harfli dil kodunun kullanılamadığı durumlarda ISO 639-2 ' den türetilen üç harfli bir kod kullanılır.|

## <a name="examples"></a>Örnekler
### <a name="strip-known-domain-name"></a>Şerit bilinen etki alanı adı
Bir kullanıcı adı almak için bir kullanıcının e-posta bilinen etki alanı adından çıkarmanız gerekir. <br>
Örneğin, "contoso.com" etki alanı varsa, aşağıdaki ifade kullanabilirsiniz:

**İfade:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Giriş / Çıkış örneği:** <br>

* **Giriş** (posta): "john.doe@contoso.com"
* **Çıkış**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>Kullanıcı adı için sabit bir sonek ekleme
Salesforce korumalı alan kullanıyorsanız, ek bir sonek eşitlemeden önce tüm kullanıcı adları eklemek gerekebilir.

**İfade:** <br>
`Append([userPrincipalName], ".test")`

**Örnek giriş/çıkış:** <br>

* **Giriş**: (userPrincipalName): "John.Doe@contoso.com"
* **ÇIKIŞ**: "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Birleştirme bölümleri adının ilk ve son tarafından kullanıcı diğer adı oluştur
Bir kullanıcı diğer kullanıcının adını, ilk 3 harf ve kullanıcının soyadını ilk 5 harfini yararlanarak oluşturmanız gerekiyor.

**İfade:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Örnek giriş/çıkış:** <br>

* **Giriş** (Bu): "John"
* **Giriş** (Soyadı): In
* **ÇIKIŞ**:  "Cantikan"

### <a name="remove-diacritics-from-a-string"></a>Bir dizeden Aksanları Kaldır
Vurgu işaretlerinin içermeyen eşdeğer karakterlerle vurgu işaretleri içeren karakter değiştirmeniz gerekir.

**İfade:** <br>
NormalizeDiacritics([givenName])

**Örnek giriş/çıkış:** <br>

* **Giriş** (Bu): "Zoë"
* **ÇIKIŞ**:  "Bure"

### <a name="split-a-string-into-a-multi-valued-array"></a>Dizeyi çok değerli bir diziye bölme
Virgülle ayrılmış dizelerin bir listesini almanız ve bunları Salesforce 'ın PermissionSets özniteliği gibi bir çok değerli özniteliğe takılmış bir diziye bölmeniz gerekir. Bu örnekte, Azure AD 'de extensionAttribute5 'de izin kümelerinin bir listesi doldurulmuştur.

**İfade:** <br>
Böl ([extensionAttribute5], ",")

**Örnek giriş/çıkış:** <br>

* **Giriş** (extensionAttribute5): "PermissionSetOne, Izinionsettingwo"
* **Çıkış**: ["Permissionsetone", "Permissionsettingwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Belirli bir biçimde bir dize olarak çıkış tarihi
Belirli bir biçimdeki bir SaaS uygulamasına tarihleri göndermek istediğiniz. <br>
Örneğin, tarihleri biçimlendirmek için ServiceNow istiyorsunuz.

**İfade:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Örnek giriş/çıkış:**

* **Giriş** (extensionAttribute1): "20150123105347.1 Z"
* **ÇIKIŞ**:  "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Önceden tanımlanmış seçenekleri kümesi temel alınarak bir değeri değiştirin

Azure AD'de depolanan eyalet koduna göre kullanıcının saat dilimi tanımlamak gerekir. <br>
Önceden tanımlanmış seçeneklerden herhangi biri durum kodu eşleşmiyorsa, "Avustralya/Sidney" varsayılan değeri kullanın.

**İfade:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Örnek giriş/çıkış:**

* **Giriş** (durum): "QLD"
* **ÇIKIŞ**: "Avustralya/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Normal ifade kullanarak karakterleri değiştirme
Normal ifade değeriyle eşleşen karakterler bulmanız ve bunları kaldırmanız gerekir.

**İfade:** <br>

Replace ([Mailtakma ad],, "[a-zA-Z_] *",, "",,)

**Örnek giriş/çıkış:**

* **Giriş** (Mailtakma ad: "john_doe72"
* **ÇIKIŞ**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Oluşturulan userPrincipalName (UPN) değerini küçük harfe Dönüştür
Aşağıdaki örnekte, UPN değeri PreferredFirstName ve PreferredLastName kaynak alanları birleştirerek oluşturulur ve tüm karakterleri küçük harfe dönüştürmek için ToLower işlevi oluşturulan dize üzerinde çalışır. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Örnek giriş/çıkış:**

* **Giriş** (PreferredFirstName): "John"
* **Giriş** (PreferredLastName): Uludağ
* **ÇIKIŞ**: "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>UserPrincipalName (UPN) özniteliği için benzersiz bir değer oluşturur
Bağlı kullanıcının ilk adını, ikinci adı ve Soyadı, UPN özniteliği için değer atamadan önce hedef AD dizininde UPN özniteliği için bir değer ve kendi benzersizlik denetimi oluşturmanız gerekiyor.

**İfade:** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Örnek giriş/çıkış:**

* **Giriş** (PreferredFirstName): "John"
* **Giriş** (PreferredLastName): Uludağ
* **Çıkış**: "John.Smith@contoso.com" değilse UPN değerini John.Smith@contoso.com dizininde zaten mevcut değil
* **Çıkış**: "J.Smith@contoso.com" değilse UPN değerini John.Smith@contoso.com dizinde zaten var.
* **Çıkış**: "Jo.Smith@contoso.com" Yukarıdaki iki UPN değeri dizinde zaten mevcutsa

## <a name="related-articles"></a>İlgili makaleler
* [Kullanıcı sağlama/sağlamayı kaldırma SaaS uygulamaları için otomatik hale getirin](user-provisioning.md)
* [Kullanıcı sağlama için öznitelik eşlemelerini özelleştirme](customize-application-attributes.md)
* [Kullanıcı sağlama için kapsam oluşturma filtresi](define-conditional-rules-for-provisioning-user-accounts.md)
* [Kullanıcıların ve grupların Azure Active Directory'den uygulamalara otomatik olarak hazırlanmasını etkinleştirmek için SCIM'yi kullanma](use-scim-to-provision-users-and-groups.md)
* [Hesap sağlama bildirimleri](user-provisioning.md)
* [SaaS uygulamalarını tümleştirme hakkında öğreticiler listesi](../saas-apps/tutorial-list.md)
