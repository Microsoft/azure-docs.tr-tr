---
title: Eşzamanlılık denetimi | Azure Marketi
description: Bulut İş Ortağı Portalı yayımlama API 'Leri için eşzamanlılık denetimi stratejileri.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 6e2f8922d42e40d14338f06be983d3913b20859d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819755"
---
# <a name="concurrency-control"></a>Eşzamanlılık denetimi

Bulut İş Ortağı Portalı yayımlama API 'Lerine yapılan her çağrının, hangi eşzamanlılık denetimi stratejisinin kullanılacağını açıkça belirtmesi gerekir. **IF-Match** üst bilgisinin SAĞLANMASı, HTTP 400 hata yanıtına neden olur. Eşzamanlılık denetimi için iki strateji sunuyoruz.

-   **İyimser** -güncelleştirmeyi gerçekleştiren istemci, verilerin son okumasından bu yana verilerin değişip değişmediğini doğrular.
-   **Son bir WINS** -istemci, son okuma zamanından bu yana başka bir uygulamanın onu değiştirmediğine bakılmaksızın verileri doğrudan güncelleştirir.

<a name="optimistic-concurrency-workflow"></a>İyimser eşzamanlılık iş akışı
-------------------------------

Kaynaklarınızda beklenmedik bir düzenleme yapılyapılmadığını garanti etmek için, aşağıdaki iş akışı ile iyimser eşzamanlılık stratejisini kullanmanızı öneririz.

1.  API 'Leri kullanarak bir varlığı alma. Yanıt, varlığın Şu anda depolanmış sürümünü tanımlayan bir ETag değeri içerir (Yanıt sırasında).
2.  Güncelleştirme sırasında, zorunlu **IF-Match** istek üst bilgisine aynı ETag değerini ekleyin.
3.  API, istekte alınan ETag değerini, bir atomik işlem içindeki varlığın geçerli ETag değeri ile karşılaştırır.
    *   ETag değerleri farklıysa, API bir `412 Precondition Failed` HTTP yanıtı döndürür. Bu hata, istemcinin en son aldığı bu yana başka bir işlemin varlığı güncelleştirdiğini veya istekte belirtilen ETag değerinin yanlış olduğunu gösterir.
    *  ETag değerleri aynıysa veya **IF-Match** üst bilgisi joker karakteri (`*`) IÇERIYORSA, API istenen işlemi gerçekleştirir. API işlemi ayrıca varlığın depolanan ETag değerini güncelleştirir.


> [!NOTE]
> **IF-Match** üst bilgisinde joker karakteri (*) BELIRTMEK, API 'nin son bir WINS eşzamanlılık stratejisini kullanarak sonuçlanır. Bu durumda, ETag karşılaştırması gerçekleşmez ve kaynak herhangi bir denetim olmadan güncelleştirilir. 
