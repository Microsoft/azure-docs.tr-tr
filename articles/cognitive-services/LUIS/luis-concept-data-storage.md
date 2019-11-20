---
title: Veri depolama-LUSıS
titleSuffix: Azure Cognitive Services
description: LUIS anahtarı ile belirtilen bölgeyi karşılık gelen bir Azure veri deposunda şifrelenmiş verileri depolar.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: e3028a9f046edb4013d8a2d6ec88d8c3cb2ca489
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639227"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Veri depolama ve Bilişsel hizmetler Language Understanding (LUIS) kaldırılması
LUIS anahtarı ile belirtilen bölgeyi karşılık gelen bir Azure veri deposunda şifrelenmiş verileri depolar. Bu veriler 30 gün boyunca saklanır. 

## <a name="export-and-delete-app"></a>Dışarı aktarma ve uygulamayı Sil
Kullanıcılar üzerinde tam denetime sahip [verme](luis-how-to-start-new-app.md#export-app) ve [silme](luis-how-to-start-new-app.md#delete-app) uygulama. 

## <a name="utterances"></a>Konuşmalar

Utterslar iki farklı yerde depolanabilir. 

* **Yazma işlemi**sırasında, yazmalar oluşturulur ve amaç içinde depolanır. Başarılı bir Lua uygulaması için amaçlarla ilgili bir amaç gereklidir. Uygulama yayımlandıktan ve uç noktada sorguları aldıktan sonra, uç nokta isteğinin QueryString `log=false`, uç nokta ile depolandığını belirler. Uç nokta depolanıyorsa, bu, **Gözden geçirme uç noktası** utaları bölümünde, portalın **Build** bölümünde bulunan etkin öğrenme utslerini bir parçası haline gelir. 
* **Uç nokta konutlerini gözden geçirdikten**ve bir amaca yönelik olarak bir e-posta eklediğinizde, söylenişi artık incelenmek üzere uç nokta çeşidinin bir parçası olarak depolanmaz. Uygulamanın hedefleri için eklenmiştir. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Bir amacınızdan örnek söyleyeni silme

Eğitim için kullanılan örnek konuşma Sil [LUIS](luis-reference-regions.md). LUIS uygulamanızdan bir örnek utterance silerseniz, LUIS web hizmetinden kaldırılır ve dışarı aktarma için kullanılamaz.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Etkin öğrenime göre gözden geçirdiklerinizi silme

Konuşma içinde LUIS önerir kullanıcı konuşma listesinden silebilirsiniz  **[gözden geçirme endpoint konuşma sayfası](luis-how-to-review-endpoint-utterances.md)** . Konuşma bu listeden silme önerilmesini engelliyor, ancak bunları günlüklerinden silmez.

Etkin öğrenimi istemiyorsanız, [etkin öğrenmeyi devre dışı](luis-how-to-review-endpoint-utterances.md#disable-active-learning)bırakabilirsiniz. Etkin öğrenmeyi devre dışı bırakmak, günlüğü de devre dışı bırakır

### <a name="disable-logging-utterances"></a>Günlüğe kaydetme belgelerini devre dışı bırak
[Etkin öğrenmeyi devre dışı bırakmak](luis-how-to-review-endpoint-utterances.md#disable-active-learning) günlüğe kaydetmeyi devre dışı bırakır.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Hesap silme
Bir hesabı silerseniz, tüm uygulamalar, kendi örnek konuşma ve günlükleri birlikte silinir. Hesabın önce 60 gün boyunca veriler korunur ve veriler kalıcı olarak silinir.

Hesap silme, kullanılabilir **ayarları** sayfası. Hesabınızın adını almak için sağ üst gezinti çubuğunda seçin **ayarları** sayfası.

## <a name="data-inactivity-as-an-expired-subscription"></a>Veri etkin olmama süresi dolmuş bir Aboneliğim olarak
Veri saklama ve silme amacı doğrultusunda, etkin olmayan bir LUIS uygulaması olabilir. _Microsoft'un takdirine bağlı olarak_ süresi dolmuş bir Aboneliğim kabul edilir. Uygulama Son 90 gün boyunca aşağıdaki ölçütleri karşılıyorsa etkin olmadığı varsayılır: 

* Oluşmuş **hiçbir** kendisine yapılan çağrılar.
* Değiştirilmedi.
* Değil sahip geçerli bir anahtar atanır.
* Oturum açma yapılmamış.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Dışarı aktarma ve uygulama silme hakkında bilgi edinin](luis-how-to-start-new-app.md)
