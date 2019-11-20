---
title: Azure CDN için standart kurallar altyapısından koşulları Eşleştir | Microsoft Docs
description: Azure Content Delivery Network için standart kurallar altyapısından eşleşme koşulları için başvuru belgeleri (Azure CDN).
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: c4c2b1f334e37691655b18d2c629fbd8edc95382
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171600"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Azure CDN için standart kurallar altyapısından koşulları Eşleştir

Azure Content Delivery Network için [standart kurallar altyapısında](cdn-standard-rules-engine.md) (Azure CDN), bir kural bir veya daha fazla eşleşme koşulu ve bir eylemden oluşur. Bu makalede, Azure CDN için standart kurallar altyapısında kullanabileceğiniz eşleştirme koşullarına ilişkin ayrıntılı açıklamalar sağlanmaktadır.

Bir kuralın ilk bölümü eşleşme koşullarıdır veya eşleştirme koşulları kümesidir. Azure CDN için standart kurallar altyapısında her kurala en fazla dört eşleşme koşulu olabilir. Bir eşleşme koşulu, tanımlı eylemlerin gerçekleştirildiği belirli istek türlerini tanımlar. Birden çok eşleşme koşulu kullanırsanız, eşleşme koşulları ve Logic kullanılarak birlikte gruplandırılır.

Örneğin, bir eşleşme koşulunu şu şekilde kullanabilirsiniz:

- İstekleri belirli bir IP adresine, ülkeye veya bölgeye göre filtreleyin.
- İstekleri üst bilgi bilgisine göre filtreleyin.
- Mobil cihazlardan veya masaüstü cihazlardan gelen istekleri filtreleyin.

## <a name="match-conditions"></a>Koşulları Eşleştir

Aşağıdaki eşleşme koşulları, Azure CDN Standart kurallar altyapısında kullanılmak üzere kullanılabilir. 

### <a name="device-type"></a>Cihaz türü 

Bir mobil cihazdan veya masaüstü cihazdan yapılan istekleri tanımlar.  

#### <a name="required-fields"></a>Gerekli alanlar

İşleç | Desteklenen değerler
---------|----------------
Eşittir, eşit değildir | Mobil, masaüstü

### <a name="http-version"></a>HTTP sürümü

İsteği HTTP sürümüne göre tanımlar.

#### <a name="required-fields"></a>Gerekli alanlar

İşleç | Desteklenen değerler
---------|----------------
Eşittir, eşit değildir | 2,0, 1,1, 1,0, 0,9, All

### <a name="request-cookies"></a>İstek tanımlama bilgileri

Gelen istekteki tanımlama bilgisi bilgilerine göre istekleri tanımlar.

#### <a name="required-fields"></a>Gerekli alanlar

Tanımlama bilgisi adı | İşleç | Tanımlama bilgisi değeri | Örnek dönüştürme
------------|----------|--------------|---------------
Dize | [Standart işleç listesi](#standard-operator-list) | String, Int | Dönüştürme yok, büyük harf, küçük harf

#### <a name="key-information"></a>Anahtar bilgileri

- Bir tanımlama bilgisi adı belirttiğinizde joker karakter değerlerini (yıldız işaretleri (\*) dahil) kullanamazsınız. tam bir tanımlama bilgisi adı kullanın.
- Bu eşleşme koşulunun her örneği için yalnızca tek bir tanımlama bilgisi adı belirtebilirsiniz.
- Tanımlama bilgisi adı karşılaştırmaları büyük/küçük harfe duyarlıdır.
- Birden çok tanımlama bilgisi değeri belirtmek için, her tanımlama bilgisi değeri arasında tek bir boşluk kullanın. 
- Tanımlama bilgisi değerleri joker değerlerden faydalanabilir.
- Joker karakter değeri belirtilmemişse, yalnızca tam eşleşme bu eşleşme koşulunu karşılar. Örneğin, "Value" değeri "Value" ile eşleşir, ancak "değer1" olarak eşleşmez. 

### <a name="post-argument"></a>Post bağımsız değişkeni

İstekte kullanılan POST isteği yöntemi için tanımlanan bağımsız değişkenlere göre istekleri tanımlar. 

#### <a name="required-fields"></a>Gerekli alanlar

Bağımsız değişken adı | İşleç | Bağımsız değişken değeri | Örnek dönüştürme
--------------|----------|----------------|---------------
Dize | [Standart işleç listesi](#standard-operator-list) | String, Int | Dönüştürme yok, büyük harf, küçük harf

### <a name="query-string"></a>Sorgu dizesi

Belirli bir sorgu dizesi parametresi içeren istekleri tanımlar. Bu parametre, belirli bir düzenle eşleşen bir değere ayarlanır. İstek URL 'sindeki sorgu dizesi parametreleri (örneğin, **Parameter = değer**) bu koşulun karşılanıp karşılanmadığını belirtir. Bu eşleştirme koşulu, bir sorgu dizesi parametresini adına göre tanımlar ve parametre değeri için bir veya daha fazla değeri kabul eder.

#### <a name="required-fields"></a>Gerekli alanlar

İşleç | Sorgu dizesi | Örnek dönüştürme
---------|--------------|---------------
[Standart işleç listesi](#standard-operator-list) | String, Int | Dönüştürme yok, büyük harf, küçük harf

### <a name="remote-address"></a>Uzak adres

İstekleri isteyanın konumuna veya IP adresine göre tanımlar.

#### <a name="required-fields"></a>Gerekli alanlar

İşleç | Desteklenen değerler
---------|-----------------
Herhangi biri | Yok
Coğrafi eşleşme | Ülke kodu
IP eşleşmesi | IP adresi (boşlukla ayrılmış)
Hiçbirini değil | Yok
Coğrafi eşleşme değil | Ülke kodu
IP eşleşmesi değil | IP adresi (boşlukla ayrılmış)

#### <a name="key-information"></a>Anahtar bilgileri

- CıDR gösterimini kullanın.
- Birden çok IP adresi ve IP adres bloğu belirtmek için, değerler arasında tek bir boşluk kullanın:
  - **IPv4 örneği**: *1.2.3.4 10.20.30.40* , 1.2.3.4 veya 10.20.30.40 adresinden gelen isteklerle eşleşir.
  - **IPv6 örneği**: *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:8*0, 1:2:3:4:5:6:7:8 ya da 10:20:30:40:50:60:70:80 adresinden gelen isteklerle eşleşir.
- Bir IP adres bloğunun sözdizimi, ardından bir eğik çizgi ve ön ek boyutu gelen temel IP adresidir. Örneğin:
  - **IPv4 örneği**: *5.5.5.64/26* , 5.5.5.64 ile 5.5.5.127 arası adreslerden gelen isteklerle eşleşir.
  - **IPv6 örneği**: *1:2:3:/48* , 1:2:3:0:0:0:0:0 ile 1:2: 3: ffff: ffff: ffff: ffff: ffff adresinden gelen tüm isteklerle eşleşir.

### <a name="request-body"></a>İstek gövdesi

İstek gövdesinde görüntülenen belirli bir metne göre istekleri tanımlar.

#### <a name="required-fields"></a>Gerekli alanlar

İşleç | İstek gövdesi | Örnek dönüştürme
---------|--------------|---------------
[Standart işleç listesi](#standard-operator-list) | String, Int | Dönüştürme yok, büyük harf, küçük harf

### <a name="request-header"></a>İstek üst bilgisi

İstekte belirli bir üst bilgiyi kullanan istekleri tanımlar.

#### <a name="required-fields"></a>Gerekli alanlar

Üst bilgi adı | İşleç | Üst bilgi değeri | Örnek dönüştürme
------------|----------|--------------|---------------
Dize | [Standart işleç listesi](#standard-operator-list) | String, Int | Dönüştürme yok, büyük harf, küçük harf

### <a name="request-method"></a>İstek yöntemi

Belirtilen istek yöntemini kullanan istekleri tanımlar.

#### <a name="required-fields"></a>Gerekli alanlar

İşleç | Desteklenen değerler
---------|----------------
Eşittir, eşit değildir | AL, POSTALA, KOY, SIL, HEAD, SEÇENEKLER, IZLE

#### <a name="key-information"></a>Anahtar bilgileri

- Yalnızca GET isteği yöntemi Azure CDN önbelleğe alınmış içerik oluşturabilir. Diğer tüm istek yöntemleri ağ üzerinden proxy olarak kullanılır. 

### <a name="request-protocol"></a>İstek Protokolü

Kullanılan belirtilen protokolü kullanan istekleri tanımlar.

#### <a name="required-fields"></a>Gerekli alanlar

İşleç | Desteklenen değerler
---------|----------------
Eşittir, eşit değildir | HTTP, HTTPS

### <a name="request-url"></a>İstek URL'si

Belirtilen URL ile eşleşen istekleri tanımlar.

#### <a name="required-fields"></a>Gerekli alanlar

İşleç | İstek URL'si | Örnek dönüştürme
---------|-------------|---------------
[Standart işleç listesi](#standard-operator-list) | String, Int | Dönüştürme yok, büyük harf, küçük harf

#### <a name="key-information"></a>Anahtar bilgileri

- Bu kural koşulunu kullandığınızda protokol bilgilerini eklediğinizden emin olun. Örneğin: *https://www.\<yourdomain\>.com* .

### <a name="url-file-extension"></a>URL dosya uzantısı

İstenen URL 'deki dosya adında belirtilen dosya uzantısını içeren istekleri tanımlar.

#### <a name="required-fields"></a>Gerekli alanlar

İşleç | Dahili numara | Örnek dönüştürme
---------|-----------|---------------
[Standart işleç listesi](#standard-operator-list) | String, Int | Dönüştürme yok, büyük harf, küçük harf

#### <a name="key-information"></a>Anahtar bilgileri

- Uzantı için, önde gelen bir nokta eklemeyin; Örneğin, *. html*yerine *HTML* kullanın.

### <a name="url-file-name"></a>URL dosyası adı

İstenen URL 'de belirtilen dosya adını içeren istekleri tanımlar.

#### <a name="required-fields"></a>Gerekli alanlar

İşleç | Dosya adı | Örnek dönüştürme
---------|-----------|---------------
[Standart işleç listesi](#standard-operator-list) | String, Int | Dönüştürme yok, büyük harf, küçük harf

#### <a name="key-information"></a>Anahtar bilgileri

- Birden çok dosya adı belirtmek için, her dosya adını tek bir boşlukla ayırın. 

### <a name="url-path"></a>URL yolu

İstek URL 'sinde belirtilen yolu içeren istekleri tanımlar.

#### <a name="required-fields"></a>Gerekli alanlar

İşleç | Değer | Örnek dönüştürme
---------|-------|---------------
[Standart işleç listesi](#standard-operator-list) | String, Int | Dönüştürme yok, büyük harf, küçük harf

#### <a name="key-information"></a>Anahtar bilgileri

- Dosya adı değeri joker değerlerden faydalanabilir. Örneğin her bir dosya adı deseninin bir veya daha fazla yıldız işareti (*), her yıldız işareti bir veya daha fazla karakterle eşleşen bir dizi olabilir.

## <a name="reference-for-rules-engine-match-conditions"></a>Kural altyapısı eşleştirme koşulları başvurusu

### <a name="standard-operator-list"></a>Standart işleç listesi

Standart işleç listesinden değerleri kabul eden kurallar için aşağıdaki işleçler geçerlidir:

- Herhangi biri
- Eşittir 
- Contains 
- Şununla başlar 
- Şununla biter 
- Küçüktür
- Küçüktür veya eşittir
- Büyüktür
- Büyük veya eşittir
- Hiçbirini değil
- İçermez
- Şununla başlamaktadır 
- Şununla bitmiyor 
- Küçüktür
- Küçüktür veya eşittir
- Şundan büyük değil
- Büyük veya eşit değil

*Küçüktür ve* *büyüktür*gibi sayısal işleçler için, kullanılan karşılaştırma uzunluğa göre belirlenir. Bu durumda, Match koşulunun değeri, karşılaştırmak istediğiniz uzunluğa eşit bir tamsayı olmalıdır. 

## <a name="next-steps"></a>Sonraki adımlar

- [Azure CDN genel bakış](cdn-overview.md)
- [Standart kurallar altyapısı başvurusu](cdn-standard-rules-engine-reference.md)
- [Standart kurallar altyapısındaki eylemler](cdn-standard-rules-engine-actions.md)
- [Standart kurallar altyapısını kullanarak HTTPS 'yi zorla](cdn-standard-rules-engine.md)
