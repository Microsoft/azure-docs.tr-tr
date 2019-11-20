---
title: Sınırlar ve sınırlar-Soru-Cevap Oluşturma
titleSuffix: Azure Cognitive Services
description: Soru-Cevap Oluşturma Bilgi Bankası ve hizmetinin bölümleri için meta sınırlara sahiptir. Test ve yayımlamak için bilgi bankanızı bu sınırlar dahilinde tutmanız önemlidir.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 4effd14029eaaee1e1c22cdb814096820e19e089
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794021"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Bilgi tabanı sınırlarını ve sınırlarını Soru-Cevap Oluşturma

Aşağıda belirtilen Soru-Cevap Oluşturma limitleri [Azure bilişsel arama fiyatlandırma katmanı sınırlarının](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) ve [soru-cevap oluşturma fiyatlandırma katmanı sınırlarının](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)bir birleşimidir. Kaynak başına kaç bilgi tabanı oluşturabileceğiniz ve her bilgi tabanının ne kadar büyüeceği hakkında bilgi sahibi olmak için her iki limit kümesini de bilmeniz gerekir.

## <a name="knowledge-bases"></a>Bilgi Bankası tabanları

En fazla bilgi Bankası sayısı, [Azure bilişsel arama katmanı sınırlarına](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)göre belirlenir.

|**Azure Bilişsel Arama katmanı** | **Ücretsiz** | **Temel** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|İzin verilen en fazla yayımlanan bilgi tabanı sayısı|2|14|49|199|199|2\.999|

 Örneğin, katmanınızda 15 ' in izin verilen dizini varsa, 14 bilgi tabanı (yayımlanan Bilgi Bankası başına 1 Dizin) yayımlayabilirsiniz. On beşinci Dizin, `testkb`, yazma ve test için tüm bilgi tabanları için kullanılır. 

## <a name="extraction-limits"></a>Ayıklama sınırları

### <a name="maximum-number-of-files"></a>En fazla dosya sayısı

Ayıklanabilen en fazla dosya sayısı ve en büyük dosya boyutu, **[soru-cevap oluşturma fiyatlandırma katmanı limitlerinizi](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** temel alır.

### <a name="maximum-number-of-deep-links-from-url"></a>URL 'den en fazla derin bağlantı sayısı

Bir URL sayfasından QnAs ayıklanmasıyla gezinilebilen en fazla derin bağlantı sayısı **20**' dir.

## <a name="metadata-limits"></a>Meta veri sınırları

### <a name="by-azure-cognitive-search-pricing-tier"></a>Azure Bilişsel Arama fiyatlandırma katmanına göre

Bilgi Bankası başına en fazla meta veri alanı sayısı, **[Azure bilişsel arama katmanı limitlerinizi](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** temel alır.

|**Azure Bilişsel Arama katmanı** | **Ücretsiz** | **Temel** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Soru-Cevap Oluşturma Hizmeti başına en fazla meta veri alanı (tüm KBs 'lerde)|1000|100 *|1000|1000|1000|1000|

### <a name="by-name-and-value"></a>Ada ve değere göre

Meta veri adı ve değeri için uzunluk ve kabul edilebilir karakterler aşağıdaki tabloda listelenmiştir.

|Öğe|İzin verilen karakterler|Regex model eşleşmesi|En fazla karakter|
|--|--|--|--|
|Ad|Belirlemesine<br>alfasayısal (harfler ve rakamlar)<br>`_` (alt çizgi)|`^[a-zA-Z0-9_]+$`|100|
|Değer|Hariç her şeye izin verir<br>`:` (iki nokta üst üste)<br>`|` (dikey boru)|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Bilgi Bankası içerik limitleri
Bilgi bankasındaki içeriğe ilişkin genel sınırlamalar:
* Yanıt metninin uzunluğu: 25.000
* Soru metninin uzunluğu: 1.000
* Meta veri anahtarı/değer metninin uzunluğu: 100
* Meta veri adı için desteklenen karakterler: Alfabeller, rakamlar ve `_`  
* Meta veri değeri için desteklenen karakterler: tümü `:` ve `|` hariç 
* Dosya adı uzunluğu: 200
* Desteklenen dosya biçimleri: ". tsv", ". pdf", ". txt", ". docx", ". xlsx".
* Alternatif soruların maksimum sayısı: 300
* En yüksek soru-cevap çifti sayısı: seçilen **[Azure bilişsel arama katmanına](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** bağlıdır. Soru ve yanıt çifti, Azure Bilişsel Arama dizininde bulunan bir belgeyle eşlenir. 
* URL/HTML sayfası: 1.000.000 karakter

## <a name="create-knowledge-base-call-limits"></a>Bilgi Bankası çağrı sınırları oluşturma:
Bunlar her bir bilgi tabanı oluşturma eyleminin sınırlarını temsil eder; diğer bir deyişle, *BB oluştur* veya CREATEıNFO API 'sini çağırma ' ya tıklayın.
* Yanıt başına en fazla alternatif soru sayısı: 300
* En fazla URL sayısı: 10
* En fazla dosya sayısı: 10

## <a name="update-knowledge-base-call-limits"></a>Bilgi Bankası çağrı sınırlarını güncelleştirme
Bunlar her bir güncelleştirme eyleminin sınırlarını temsil eder; diğer bir deyişle, *kayıt ve eğitim* ' i ve sonra UPDATEıNFO API 'sini çağırır.
* Her kaynak adının uzunluğu: 300
* En fazla eklenen veya silinen diğer soru sayısı: 300
* Eklenen veya silinen en fazla meta veri alanı sayısı: 10
* Yenilenebilir en fazla URL sayısı: 5

## <a name="next-steps"></a>Sonraki adımlar

[Hizmet fiyatlandırma katmanlarının](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker)ne zaman ve nasıl değiştirileceğini öğrenin.
