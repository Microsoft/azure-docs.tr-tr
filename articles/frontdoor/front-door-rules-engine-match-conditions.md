---
title: Azure ön kapı kuralları motorları koşullara göre eşleşiyor
description: Bu makalede, Azure ön kapı kuralları altyapısından kullanılabilen çeşitli eşleşme koşullarının bir listesi sunulmaktadır.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: duau
ms.openlocfilehash: 23c3afc9d222c7964c3f957b810a545a737a3262
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106068209"
---
# <a name="azure-front-door-rules-engine-match-conditions"></a>Azure ön kapı kuralları altyapısı eşleştirme koşulları

[AFD kural altyapısında](front-door-rules-engine.md), bir kural sıfır veya daha fazla eşleşme koşulu ve bir eylemden oluşur. Bu makalede, AFD kural altyapısında kullanabileceğiniz eşleştirme koşullarına ilişkin ayrıntılı açıklamalar sağlanmaktadır.

Bir kuralın ilk bölümü eşleşme koşullarıdır veya eşleştirme koşulları kümesidir. Bir kural, en fazla 10 eşleşme koşullarından oluşabilir. Bir eşleşme koşulu, tanımlı eylemlerin yapıldığı belirli istek türlerini tanımlar. Birden çok eşleşme koşulu kullanırsanız, eşleşme koşulları ve Logic kullanılarak birlikte gruplandırılır. Birden çok değeri ("boşlukla ayrılmış" olarak belirtilen) destekleyen tüm eşleşme koşulları için "OR" işleci kabul edilir.

Örneğin, bir eşleşme koşulunu şu şekilde kullanabilirsiniz:

- İstekleri belirli bir IP adresine, ülkeye veya bölgeye göre filtreleyin.
- İstekleri üst bilgi bilgisine göre filtreleyin.
- Mobil cihazlardan veya masaüstü cihazlardan gelen istekleri filtreleyin.

Aşağıdaki eşleşme koşulları, Azure ön kapı kuralları altyapısında kullanılabilir.  

## <a name="device-type"></a>Cihaz Türü 

Bir mobil cihazdan veya masaüstü cihazdan yapılan istekleri tanımlar.  

#### <a name="required-fields"></a>Gerekli alanlar

Operatör | Desteklenen değerler
---------|----------------
Eşittir, eşit değildir | Mobil, masaüstü

## <a name="post-argument"></a>Post bağımsız değişkeni

İstekte kullanılan POST isteği yöntemi için tanımlanan bağımsız değişkenlere göre istekleri tanımlar. 

#### <a name="required-fields"></a>Gerekli alanlar

Bağımsız değişken adı | Operatör | Bağımsız değişken değeri | Örnek dönüştürme
--------------|----------|----------------|---------------
Dize | [Standart işleç listesi](#standard-operator-list) | String, Int | Küçük harf, büyük harf, kesme, boşluk kaldırma, URL kodlama, URL kod çözme

## <a name="query-string"></a>Sorgu dizesi

Belirli bir sorgu dizesi parametresi içeren istekleri tanımlar. Bu parametre, belirli bir düzenle eşleşen bir değere ayarlanır. İstek URL 'sindeki sorgu dizesi parametreleri (örneğin, **Parameter = değer**) bu koşulun karşılanıp karşılanmadığını belirtir. Bu eşleştirme koşulu, bir sorgu dizesi parametresini adına göre tanımlar ve parametre değeri için bir veya daha fazla değeri kabul eder.

#### <a name="required-fields"></a>Gerekli alanlar

Operatör | Sorgu dizesi | Örnek dönüştürme
---------|--------------|---------------
[Standart işleç listesi](#standard-operator-list) | String, Int | Küçük harf, büyük harf, kesme, boşluk kaldırma, URL kodlama, URL kod çözme

## <a name="remote-address"></a>Uzak adres

İstekleri isteyanın konumuna veya IP adresine göre tanımlar.

#### <a name="required-fields"></a>Gerekli alanlar

Operatör | Desteklenen değerler
---------|-----------------
Coğrafi eşleşme | Ülke kodu
IP eşleşmesi | IP adresi (boşlukla ayrılmış)
Coğrafi eşleşme değil | Ülke kodu
IP eşleşmesi değil | IP adresi (boşlukla ayrılmış)

#### <a name="key-information"></a>Önemli bilgiler

- CıDR gösterimini kullanın.
- Birden çok IP adresi ve IP adres bloğu belirtmek için, değerler arasında tek bir boşluk kullanın:
  - **IPv4 örneği**: *1.2.3.4 10.20.30.40* , 1.2.3.4 veya 10.20.30.40 adresinden gelen isteklerle eşleşir.
  - **IPv6 örneği**: *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* , 1:2:3:4:5:6:7:8 ya da 10:20:30:40:50:60:70:80 adresinden gelen isteklerle eşleşir.
- Bir IP adres bloğunun sözdizimi, ardından bir eğik çizgi ve ön ek boyutu gelen temel IP adresidir. Örnek:
  - **IPv4 örneği**: *5.5.5.64/26* , 5.5.5.64 ile 5.5.5.127 arası adreslerden gelen isteklerle eşleşir.
  - **IPv6 örneği**: *1:2:3:/48* , 1:2:3: ffff: ffff: ffff: ffff: ffff arasındaki 1:2:3:0:0:0:0:0 adresten gelen isteklerden eşleşir.

## <a name="request-body"></a>İstek gövdesi

İstek gövdesinde görüntülenen belirli bir metne göre istekleri tanımlar.

#### <a name="required-fields"></a>Gerekli alanlar

Operatör | İstek gövdesi | Örnek dönüştürme
---------|--------------|---------------
[Standart işleç listesi](#standard-operator-list) | String, Int | Küçük harf, büyük harf, kesme, boşluk kaldırma, URL kodlama, URL kod çözme

## <a name="request-header"></a>İstek üst bilgisi

İstekte belirli bir üst bilgiyi kullanan istekleri tanımlar.

#### <a name="required-fields"></a>Gerekli alanlar

Üst bilgi adı | Operatör | Üst bilgi değeri | Örnek dönüştürme
------------|----------|--------------|---------------
Dize | [Standart işleç listesi](#standard-operator-list) | String, Int | Küçük harf, büyük harf, kesme, boşluk kaldırma, URL kodlama, URL kod çözme

## <a name="request-method"></a>İstek yöntemi

Belirtilen istek yöntemini kullanan istekleri tanımlar.

#### <a name="required-fields"></a>Gerekli alanlar

Operatör | Desteklenen değerler
---------|----------------
Eşittir, eşit değildir | AL, POSTALA, KOY, SIL, HEAD, SEÇENEKLER, IZLE

#### <a name="key-information"></a>Önemli bilgiler

- Yalnızca GET isteği yöntemi Azure ön kapıda önbelleğe alınmış içerik oluşturabilir. Diğer tüm istek yöntemleri ağ üzerinden proxy olarak kullanılır. 

## <a name="request-protocol"></a>İstek Protokolü

Kullanılan belirtilen protokolü kullanan istekleri tanımlar.

#### <a name="required-fields"></a>Gerekli alanlar

Operatör | Desteklenen değerler
---------|----------------
Eşittir, eşit değildir | HTTP, HTTPS

## <a name="request-url"></a>İstek URL’si

Belirtilen URL ile eşleşen istekleri tanımlar.

#### <a name="required-fields"></a>Gerekli alanlar

Operatör | İstek URL’si | Örnek dönüştürme
---------|-------------|---------------
[Standart işleç listesi](#standard-operator-list) | String, Int | Küçük harf, büyük harf, kesme, boşluk kaldırma, URL kodlama, URL kod çözme

#### <a name="key-information"></a>Önemli bilgiler

- Bu kural koşulunu kullandığınızda protokol bilgilerini eklediğinizden emin olun. Örneğin: `https://www.<yourdomain\>.com`.

## <a name="request-file-extension"></a>İstek dosyası uzantısı

İstenen URL 'deki dosya adında belirtilen dosya uzantısını içeren istekleri tanımlar.

#### <a name="required-fields"></a>Gerekli alanlar

Operatör | Dahili numara | Örnek dönüştürme
---------|-----------|---------------
[Standart işleç listesi](#standard-operator-list) | String, Int | Küçük harf, büyük harf, kesme, boşluk kaldırma, URL kodlama, URL kod çözme

#### <a name="key-information"></a>Önemli bilgiler

- Uzantı için, önde gelen bir nokta eklemeyin; Örneğin, *. html* yerine *HTML* kullanın.

## <a name="request-file-name"></a>İstek dosyası adı

İstenen URL 'de belirtilen dosya adını içeren istekleri tanımlar.

#### <a name="required-fields"></a>Gerekli alanlar

Operatör | Dosya adı | Örnek dönüştürme
---------|-----------|---------------
[Standart işleç listesi](#standard-operator-list) | String, Int | Küçük harf, büyük harf, kesme, boşluk kaldırma, URL kodlama, URL kod çözme

#### <a name="key-information"></a>Önemli bilgiler

- Birden çok dosya adı belirtmek için, her dosya adını ENTER tuşuna basarak ayırın. 

## <a name="request-path"></a>İstek yolu

İstek URL 'sinde belirtilen yolu içeren istekleri tanımlar.

#### <a name="required-fields"></a>Gerekli alanlar

İşleç | Değer | Örnek dönüştürme
---------|-------|---------------
[Standart işleç listesi](#standard-operator-list) | String, Int | Küçük harf, büyük harf, kesme, boşluk kaldırma, URL kodlama, URL kod çözme

#### <a name="key-information"></a>Önemli bilgiler

- Bu kural koşulunu kullandığınızda, eşleşmenin ilk yoldan sonra başlayacağını unutmayın `/` . Örneğin: ile `https://www.<yourdomain>.com/folder/page` eşleşme başlar `folder/page` .

## <a name="standard-operator-list"></a>Standart işleç listesi

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

*Küçüktür ve* *büyüktür* gibi sayısal işleçler için, kullanılan karşılaştırma uzunluğa göre belirlenir. Match koşulunun değeri, karşılaştırmak istediğiniz uzunluğa eşit bir tamsayı olmalıdır. 


## <a name="next-steps"></a>Sonraki adımlar

- İlk [kural altyapılarınızı](front-door-tutorial-rules-engine.md)yapılandırmayı öğrenin. 
- [Kural altyapısı eylemleri](front-door-rules-engine-actions.md) hakkında daha fazla bilgi edinin
- [Azure ön kapı kuralları altyapısı](front-door-rules-engine.md) hakkında daha fazla bilgi edinin
