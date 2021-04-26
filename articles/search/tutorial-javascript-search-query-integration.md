---
title: 'JavaScript öğreticisi: arama tümleştirmesi vurgulamaları'
titleSuffix: Azure Cognitive Search
description: Arama etkin Web sitesinde kullanılan JavaScript SDK arama sorgularını anlayın
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: cf4e1b1ecf209b587a45ca4c43607bfa95155aee
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104726941"
---
# <a name="4---search-integration-highlights"></a>4-tümleştirmede Arama konuları

Önceki derslerde, bir statik Web uygulamasına arama eklediniz. Bu ders, tümleştirme oluşturmaya yönelik temel adımları vurgular. Aramanızı JavaScript uygulamanızla tümleştirme hakkında bir sayfa arıyorsanız, bu makalede bilmeniz gerekenler açıklanmaktadır.

## <a name="azure-sdk-azuresearch-documents"></a>Azure SDK @azure/search-documents 

Işlev uygulaması Bilişsel Arama için Azure SDK 'sını kullanır:

* NPM [@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)
* Başvuru belgeleri: [Istemci kitaplığı](/javascript/api/overview/azure/search-documents-readme)

Işlev uygulaması, kaynak adınızı, kaynak anahtarınızı ve dizin adını kullanarak SDK aracılığıyla bulut tabanlı Bilişsel Arama API 'sine kimlik doğrular. Gizlilikler statik Web uygulaması ayarlarında depolanır ve Işlevin ortam değişkenleri olarak çekilir. 

## <a name="configure-secrets-in-a-configuration-file"></a>Yapılandırma dosyasında gizli dizileri yapılandırma

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/config.js" highlight="3,4" :::

## <a name="azure-function-search-the-catalog"></a>Azure Işlevi: katalogda arama

`Search` [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/index.js) bir arama terimi alır ve bir eşleşme listesi döndürerek arama dizinindeki belgeler arasında arama yapar. 

Arama API 'SI için yönlendirme [function.jsbağlamalarda](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/function.json) bulunur.

Azure Işlevi arama yapılandırma bilgilerini çeker ve sorguyu yerine getirir.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Search/index.js" highlight="4-9, 75" :::

## <a name="client-search-from-the-catalog"></a>İstemci: katalogdan arama

Aşağıdaki kodla, yanıt verme istemcisinde Azure Işlevini çağırın. 

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Search/Search.js" highlight="40-51" :::

## <a name="azure-function-suggestions-from-the-catalog"></a>Azure Işlevi: katalogdan öneriler

`Suggest`Bir Kullanıcı yazarken [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/index.js) bir arama terimi alır ve arama dizinindeki belgeler genelinde kitap başlıkları ve yazar gibi arama terimleri, küçük bir eşleşme listesi döndürüyor. 

Arama öneri aracı, `sg` toplu karşıya yükleme sırasında kullanılan [şema dosyasında](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/bulk-insert/good-books-index.json) tanımlanmıştır.

Öneri API 'SI için yönlendirme [function.js](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/function.json) bağlamalarda bulunur.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Suggest/index.js" highlight="4-9, 21" :::

## <a name="client-suggestions-from-the-catalog"></a>İstemci: katalogdan öneriler

TH işlevi API 'SI, bileşen başlatma kapsamında tepki verilen uygulamada çağrılır `\src\components\SearchBar\SearchBar.js` :

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/components/SearchBar/SearchBar.js" highlight="52-60" :::

## <a name="azure-function-get-specific-document"></a>Azure Işlevi: belirli bir belgeyi al 

`Lookup` [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/index.js) bir kimlik alır ve arama dizininden belge nesnesini döndürür. 

Arama API 'SI için yönlendirme [function.jsbağlamalarda](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/function.json) bulunur.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Lookup/index.js" highlight="4-9, 17" :::

## <a name="client-get-specific-document"></a>İstemci: belirli bir belgeyi al 

Bu işlev API 'SI, bileşen başlatma kapsamında tepki verilen uygulamada çağrılır `\src\pages\Details\Detail.js` :

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Details/Details.js" highlight="19-29" :::

## <a name="next-steps"></a>Sonraki adımlar

* [Azure SQL verilerinin dizinini oluştur](search-indexer-tutorial.md)
