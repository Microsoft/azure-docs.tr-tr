---
title: Sınırlar ve sınırlar-Soru-Cevap Oluşturma
description: Soru-Cevap Oluşturma Bilgi Bankası ve hizmetinin bölümleri için meta sınırlara sahiptir. Test ve yayımlamak için bilgi bankanızı bu sınırlar dahilinde tutmanız önemlidir.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: ad498575b029f918538909a9b5b2d52c71c1389c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816377"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Bilgi tabanı sınırlarını ve sınırlarını Soru-Cevap Oluşturma

Aşağıda belirtilen Soru-Cevap Oluşturma limitleri [Azure bilişsel arama fiyatlandırma katmanı sınırlarının](../../search/search-limits-quotas-capacity.md) ve [soru-cevap oluşturma fiyatlandırma katmanı sınırlarının](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)bir birleşimidir. Kaynak başına kaç bilgi tabanı oluşturabileceğiniz ve her bilgi tabanının ne kadar büyüeceği hakkında bilgi sahibi olmak için her iki limit kümesini de bilmeniz gerekir.

## <a name="knowledge-bases"></a>Bilgi Bankası tabanları

En fazla bilgi Bankası sayısı, [Azure bilişsel arama katmanı sınırlarına](../../search/search-limits-quotas-capacity.md)göre belirlenir.

|**Azure Bilişsel Arama katmanı** | **Ücretsiz** | **Temel** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|İzin verilen en fazla yayımlanan bilgi tabanı sayısı|2|14|49|199|199|2.999|

 Örneğin, katmanınızda 15 ' in izin verilen dizini varsa, 14 bilgi tabanı (yayımlanan Bilgi Bankası başına 1 Dizin) yayımlayabilirsiniz. On beşinci Dizin, `testkb` yazma ve test için tüm bilgi tabanları için kullanılır.

## <a name="extraction-limits"></a>Ayıklama sınırları

### <a name="file-naming-constraints"></a>Dosya adlandırma kısıtlamaları

Dosya adları aşağıdaki karakterleri içeremez:

|Karakter kullanma|
|--|
|Tek tırnak `'`|
|Çift tırnak `"`|

### <a name="maximum-file-size"></a>En büyük dosya boyutu

|Biçimlendir|En büyük dosya boyutu (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>En fazla dosya sayısı

Ayıklanabilen en fazla dosya sayısı ve en büyük dosya boyutu, **[soru-cevap oluşturma fiyatlandırma katmanı limitlerinizi](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** temel alır.

> [!NOTE]
> Soru-Cevap Oluşturma yönetilen (Önizleme), eklenebilecek kaynak sayısını sınırlayan ücretsiz bir hizmettir. Yönetim API 'Leri ve tahmin API 'Leri için aktarım hızı şu anda saniyede 10 işlem üzerinden yapılır.

### <a name="maximum-number-of-deep-links-from-url"></a>URL 'den en fazla derin bağlantı sayısı

Bir URL sayfasından QnAs ayıklanmasıyla gezinilebilen en fazla derin bağlantı sayısı **20**' dir.

## <a name="metadata-limits"></a>Meta veri sınırları

Meta veriler metin tabanlı anahtar olarak sunulur: gibi bir değer çifti `product:windows 10` . Daha küçük bir durumda depolanır ve karşılaştırılır.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Azure Bilişsel Arama fiyatlandırma katmanına göre

Bilgi Bankası başına en fazla meta veri alanı sayısı, **[Azure bilişsel arama katmanı limitlerinizi](../../search/search-limits-quotas-capacity.md)** temel alır.

|**Azure Bilişsel Arama katmanı** | **Ücretsiz** | **Temel** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Soru-Cevap Oluşturma Hizmeti başına en fazla meta veri alanı (tüm KBs 'lerde)|1.000|100 *|1.000|1.000|1.000|1.000|

### <a name="by-name-and-value"></a>Ada ve değere göre

Meta veri adı ve değeri için uzunluk ve kabul edilebilir karakterler aşağıdaki tabloda listelenmiştir.

|Öğe|İzin verilen karakterler|Regex model eşleşmesi|En fazla karakter|
|--|--|--|--|
|Ad (anahtar)|Belirlemesine<br>alfasayısal (harfler ve rakamlar)<br>`_` adında<br> Boşluk içermemelidir.|`^[a-zA-Z0-9_]+$`|100|
|Değer|Hariç her şeye izin verir<br>`:` üste<br>`|` (dikey boru)<br>Yalnızca bir değere izin verilir.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Bilgi Bankası içerik limitleri
Bilgi bankasındaki içeriğe ilişkin genel sınırlamalar:
* Yanıt metninin uzunluğu: 25.000 karakter
* Soru metninin uzunluğu: 1.000 karakter
* Meta veri anahtar metninin uzunluğu: 100 karakter
* Meta veri değeri metninin uzunluğu: 500 karakter
* Meta veri adı için desteklenen karakterler: harfler, rakamlar ve `_`
* Meta veri değeri için desteklenen karakterler: tümü `:` ve dışında `|`
* Dosya adı uzunluğu: 200
* Desteklenen dosya biçimleri: ". tsv", ". pdf", ". txt", ". docx", ". xlsx".
* Alternatif soruların maksimum sayısı: 300
* En yüksek soru-cevap çifti sayısı: seçilen **[Azure bilişsel arama katmanına](../../search/search-limits-quotas-capacity.md#document-limits)** bağlıdır. Soru ve yanıt çifti, Azure Bilişsel Arama dizininde bulunan bir belgeyle eşlenir.
* URL/HTML sayfası: 1.000.000 karakter

## <a name="create-knowledge-base-call-limits"></a>Bilgi Bankası çağrı sınırları oluşturma:
Bunlar her bir bilgi tabanı oluşturma eyleminin sınırlarını temsil eder; diğer bir deyişle, *BB oluştur* veya CREATEıNFO API 'sini çağırma ' ya tıklayın.
* Yanıt başına önerilen en fazla farklı soru sayısı: 300
* En fazla URL sayısı: 10
* En fazla dosya sayısı: 10
* Çağrı başına izin verilen en fazla QnAs sayısı: 1000

## <a name="update-knowledge-base-call-limits"></a>Bilgi Bankası çağrı sınırlarını güncelleştirme
Bunlar her bir güncelleştirme eyleminin sınırlarını temsil eder; diğer bir deyişle, *kayıt ve eğitim* ' i ve sonra UPDATEıNFO API 'sini çağırır.
* Her kaynak adının uzunluğu: 300
* Önerilen en fazla sayıda alternatif soru eklendi veya silindi: 300
* Eklenen veya silinen en fazla meta veri alanı sayısı: 10
* Yenilenebilir en fazla URL sayısı: 5
* Çağrı başına izin verilen en fazla QnAs sayısı: 1000

## <a name="add-unstructured-file-limits"></a>Yapılandırılmamış dosya sınırları Ekle

> [!NOTE]
> * Sınırın izin verdiğinden daha büyük dosyalar kullanmanız gerekiyorsa, API 'ye göndermeden önce dosyayı daha küçük dosyalara kesebilirsiniz. 

Bu, bir *KB oluşturmak* veya CREATEKNOWLEDGE API 'sini çağırmak için yapılandırılmamış dosyalar kullanıldığında sınırları temsil eder:
* Dosya uzunluğu: ilk 32000 karakteri çıkaracağız
* Dosya başına en fazla 3 yanıt.

## <a name="prebuilt-question-answering-limits"></a>Önceden oluşturulmuş soru yanıtlama limitleri

> [!NOTE]
> * Sınırın izin verdiğinden daha büyük belgeler kullanmanız gerekiyorsa, API 'ye göndermeden önce metni daha küçük parçalara ayırın. 
> * Belge, tek bir metin karakteri dizesidir.  

Bunlar, *yanıt oluşturmak* veya GENERATEANSWER API 'sini çağırmak Için önceden oluşturulmuş API kullanıldığında sınırları temsil eder:
* Belge sayısı: 5
* Tek bir belgenin en büyük boyutu: 5.120 karakter
* Belge başına en fazla 3 yanıt.

## <a name="next-steps"></a>Sonraki adımlar

[Hizmet fiyatlandırma katmanlarının](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku)ne zaman ve nasıl değiştirileceğini öğrenin.
