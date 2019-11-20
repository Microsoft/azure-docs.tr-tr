---
title: Paper varlık öznitelikleri-Akademik Bilgi API
titlesuffix: Azure Cognitive Services
description: Akademik Bilgi API 'sindeki kağıt varlığıyla kullanabileceğiniz öznitelikleri öğrenin.
services: cognitive-services
author: DarrinEide
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: fc3bb5987c31fe718951a3cae02ed7c4ddc29957
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123069"
---
# <a name="paper-entity"></a>Kağıt varlığı

> [!NOTE]
> Aşağıdaki öznitelikler kağıt varlığına özgüdür. (Ty = ' 0 ')

Ad | Açıklama | Tür | İşlemler
--- | --- | --- | ---
AA. AID | Yazar ilişki KIMLIĞI | Int64 | Eşittir
AA. AfN | Yazar ilişki adı | Dize | Eşittir, StartsWith
AA. Auıd | Yazar KIMLIĞI | Int64 | Eşittir
AA. AuN | Normalleştirilmiş yazar adı | Dize | Eşittir, StartsWith
AA. Davun | Özgün yazar adı | Dize | None
AA. Bafn | Özgün ilişki adı | Dize | None
AA. Malar | Yazar listesinde sayısal konum | Int32 | Eşittir
BT | BibTex belge türü (' a ': Journal makalesi, ' b ': Book, ' c ': kitap bölümü, ' p ': konferans kağıdı) | Dize | None
BV | BibTex mekan adı | Dize | None
C. CID | Konferans Serisi KIMLIĞI | Int64 | Eşittir
C.CN | Konferans Serisi adı | Dize | Eşittir, StartsWith
BILGISI | Alıntı sayısı | Int32 | None  
CitCon | Alıntı bağlamları</br></br>Başvurulan kağıt KIMLIĞI ve yazılı bağlamın listesi (örn. [{123: ["kahverengi parçalar, kağıt 123 ' de başvurulduğu şekilde atlanmak üzere bilinir", "yavaş köpekler, kağıt 123 ' de gösterildiği gibi bir geçmiş yanlış) | Özel | None
D | Yayımlanma tarihi YYYY-AA-GG biçiminde | Tarih | Eşittir, ısbetween
DN | Özgün kağıt başlığı | Dize | None
DOı | Dijital nesne tanımlayıcısı | Dize | Eşittir, StartsWith
E | Genişletilmiş meta veriler</br></br>**Önemli**: Bu öznitelik kullanımdan kaldırılmıştır ve yalnızca eski uygulamalar için desteklenir. Bu özniteliğin tek başına istenir (yani Attributes = ID, TI, E), tüm genişletilmiş meta veri özniteliklerinin *SERILEŞTIRILMIŞ JSON dizesinde* döndürülmesiyle sonuçlanır</br></br>Genişletilmiş meta verilerde bulunan tüm öznitelikler artık üst düzey bir öznitelik olarak kullanılabilir ve bu şekilde istenebilir (örneğin, öznitelikler = ID, Ti, DOı, IA) | [Genişletildiğinde](#extended) | None
ECC | Tahmini alıntı sayısı | Int32 | None
F. DFN | Özgün çalışma adı alanı | Dize | None
F. FID | Çalışma KIMLIĞI alanı | Int64 | Eşittir
F. FN | Çalışma adının normalleştirilmiş alanı | Dize | Eşittir, StartsWith
FC | Yayındaki kağıdın ilk sayfası | Dize | Eşittir
I | Yayın sorunu | Dize | Eşittir
IA | Ters soyut | [Ters çevir Tebstract](#invertedabstract) | None
Kimlik | Kağıt KIMLIĞI | Int64 | Eşittir
J.JId | Günlük KIMLIĞI | Int64 | Eşittir
J. JN | Günlük adı | Dize | Eşittir, StartsWith
'Nın | Yayındaki kağıdın son sayfası | Dize | Eşittir
IÇERMIYOR | Yayımcı | Dize | None
Yönergelerinin | Yayın türü (0: bilinmiyor, 1: günlük makalesi, 2: patent, 3: konferans kağıdı, 4: kitap bölümü, 5: kitap, 6: kitap başvuru girişi, 7: veri kümesi, 8: depo | Dize | Eşittir
RId | Başvurulan kağıt kimliklerinin listesi | Int64 [] | Eşittir
S | Kağıdın kaynak URL 'Lerinin listesi, ilgiye göre sıralanır | [Kaynak](#source)[] | None
Varyantı | Normalleştirilmiş başlık | Dize | Eşittir, StartsWith
Yönetim | Yayın hacmi | Dize | Eşittir
VFN | Günlüğün veya konferans mekanın tam adı | Dize | None
VSN | Günlüğün veya konferans mekanın kısa adı | Dize | None
W | Başlıktaki benzersiz sözcükler | String[] | Eşittir
E | Yayımlanan yıl | Int32 | Eşittir, ısbetween

## <a name="extended"></a>Genişletildiğinde
> [!IMPORTANT]
> Bu öznitelik kullanımdan kaldırılmıştır ve yalnızca eski uygulamalar için desteklenir. Bu özniteliğin tek başına istenir (yani Attributes = ID, TI, E), tüm genişletilmiş meta veri özniteliklerinin *SERILEŞTIRILMIŞ JSON dizesinde* döndürülmesiyle sonuçlanır</br></br>Genişletilmiş meta verilerde bulunan tüm öznitelikler artık üst düzey bir öznitelik olarak kullanılabilir ve bu şekilde istenebilir (örneğin, öznitelikler = ID, Ti, DOı, IA)

> [!IMPORTANT]
> "E" kullanarak ayrı ayrı genişletilmiş öznitelikler isteme desteği. kapsam, yani "E. DN" kullanım dışıdır. Teknik olarak desteklenmeye devam ederken, "E" kullanarak ayrı ayrı genişletilmiş öznitelikler istiyor. kapsam, "E" nesnesinin ve en üst düzey özniteliğin bir parçası olarak JSON yanıtında iki yerde döndürülen öznitelik değerinin oluşmasına neden olur.

Ad | Açıklama | Tür | İşlemler
--- | --- | --- | ---
BT | BibTex belge türü (' a ': Journal makalesi, ' b ': Book, ' c ': kitap bölümü, ' p ': konferans kağıdı) | Dize | None
BV | BibTex mekan adı | Dize | None
BILGISI | Alıntı bağlamları</br></br>Başvurulan kağıt KIMLIĞI ve yazılı bağlamın listesi (örn. [{123: ["kahverengi parçalar, kağıt 123 ' de başvurulduğu şekilde atlanmak üzere bilinir", "yavaş köpekler, kağıt 123 ' de gösterildiği gibi bir geçmiş yanlış) | Özel | None
DN | Özgün kağıt başlığı | Dize | None
DOı | Dijital nesne tanımlayıcısı | Dize | None
FC | Yayındaki kağıdın ilk sayfası | Dize | None
I | Yayın sorunu | Dize | None
IA | Ters soyut | [Ters çevir Tebstract](#invertedabstract) | None
'Nın | Yayındaki kağıdın son sayfası | Dize | None
IÇERMIYOR | Yayımcı | Dize | None
S | Kağıdın kaynak URL 'Lerinin listesi, ilgiye göre sıralanır | [Kaynak](#source)[] | None
Yönetim | Yayın hacmi | Dize | None
VFN | Günlüğün veya konferans mekanın tam adı | Dize | None
VSN | Günlüğün veya konferans mekanın kısa adı | Dize | None

## <a name="invertedabstract"></a>Ters çevir Tebstract

> [!IMPORTANT]
> Evirübstract öznitelikleri Return özniteliği olarak doğrudan istenemez. Tek bir özniteliğe ihtiyacınız varsa, en üst düzey "Ia" özniteliğini istemeniz gerekir, yani IA 'yi almak için. Indexlength istek öznitelikleri = IA

Ad | Açıklama | Tür | İşlemler
--- | --- | --- | ---
Dizin uzunluğu | Dizindeki öğe sayısı (soyut sözcük sayısı) | Int32 | None
Ters Tedindex | Soyut sözcüklerin ve bunlara karşılık gelen konumlarından oluşan bir liste özgün soyut (örn. [{"": [0, 15, 30]}, {"kahverengi": [1]}, {"Fox": [2]}]) | Özel | None

## <a name="source"></a>Kaynak

> [!IMPORTANT]
> Kaynak öznitelikleri, dönüş özniteliği olarak doğrudan istenemez. Tek bir özniteliğe ihtiyacınız varsa, en üst düzey "S" özniteliğini istemeniz gerekir, yani S. U istek öznitelikleri = S

Ad | Açıklama | Tür | İşlemler
--- | --- | --- | ---
Kalite | Kaynak URL türü (1: HTML, 2: metin, 3: PDF, 4: DOC, 5: PPT, 6: XLS, 7: PS) | Dize | None
U | Kaynak URL 'SI | Dize | None
