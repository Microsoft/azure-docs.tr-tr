---
title: Azure Işlevlerini özdeş giriş için tasarlama
description: Azure Işlevlerini ıdempotent olarak oluşturma
author: craigshoemaker
ms.author: cshoe
ms.date: 9/12/2019
ms.topic: article
ms.service: azure-functions
manager: gwallace
ms.openlocfilehash: 39e785a1ca7a158ddb90a3e6ba914582c405612a
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997399"
---
# <a name="designing-azure-functions-for-identical-input"></a>Azure Işlevlerini özdeş giriş için tasarlama

Olay odaklı ve ileti tabanlı mimarinin gerçekliği, veri bütünlüğünü ve sistem kararlılığını korurken özdeş istekleri kabul etme gereksinimini belirler.

Göstermek için bir Asansör çağrı düğmesine göz önünde bulundurun. Düğmeye bastığınızda ışıklar açılır ve bir Asansör, kata bir Asansör gönderilir. Birkaç dakika sonra, başka bir Kullanıcı lobide sizi birleştirdiğinde. Bu kişi sizi ve ikinci kez aydınlatılan düğmeye basar. Bir Asansör çağrısı yapılacak bir komutun ıdempotent olduğunu hatırlatıldığınızı hatırladığınızdan, kendinize doğru bir şekilde ilerleyin.

Bir Asansör çağrı düğmesine basıldığında ikinci, üçüncü veya dördüncü bir kez nihai sonuç üzerinde hiçbir pul yoktur. Düğmeye bastığınızda, Asansör sayısı ne kadar olursa olsun, Asansör kata gönderilir. Asansör gibi ıdempotent sistemleri, aynı komutların kaç kez verildiğine bakılmaksızın aynı sonuca neden olacak.

Uygulamalar oluşturmak için aşağıdaki senaryoları göz önünde bulundurun:

- Envanter denetimi uygulamanız aynı ürünü birden çok kez silmeye çalışırsa ne olur?
- Aynı kişi için bir çalışan kaydı oluşturmak için birden fazla istek varsa insan kaynakları uygulamanız nasıl davranır?
- Bankacılık uygulamanız aynı çekme için 100 istek alırsa para ne olur?

Bir işlev isteklerinin aynı komutları alabileceği birçok bağlam vardır. Bazı durumlar şunlardır:

- Aynı isteği birçok kez gönderen yeniden deneme ilkeleri
- Önbelleğe alınmış komutlar uygulamaya yeniden yürütüldü
- Birden çok özdeş istek gönderen uygulama hataları

Veri bütünlüğünü ve sistem durumunu korumak için, bir ıdempotent uygulaması aşağıdaki davranışları içerebilen mantığı içerir:

- Silmeyi yürütmeye çalışmadan önce verilerin varlığını doğrulama
- Oluşturma eylemi yürütülmeye çalışmadan önce verilerin zaten mevcut olup olmadığını denetleme
- Verilerde nihai tutarlılık oluşturan mantığı mutabık kılma
- Eşzamanlılık denetimleri
- Çoğaltma algılaması
- Veri yeniliği doğrulaması
- Giriş verilerini doğrulamak için koruma mantığı

Son olarak, belirli bir eylemin mümkün olduğundan ve yalnızca bir kez yürütülene kadar her şey elde edilir.
