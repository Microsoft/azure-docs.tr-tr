---
title: Azure Cosmos DB verilerini yedekten geri yükleme
description: Bu makalede bir yedekten Azure Cosmos DB verilerinin nasıl geri yükleneceği, verilerin geri yüklenmesi için Azure desteği ile iletişim kurma, veriler geri yüklendikten sonra gerçekleştirilecek adımlar açıklanmaktadır.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 19ca835ca8211202cd358ac2ec3695675183a372
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240774"
---
# <a name="restore-data-from-a-backup-in-azure-cosmos-db"></a>Azure Cosmos DB bir yedekten veri geri yükleme 

Veritabanınızı veya bir kapsayıcıyı yanlışlıkla silerseniz, [bir destek bileti]( https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) oluşturabilir veya verileri otomatik çevrimiçi yedeklemelerden geri yüklemek için [Azure desteği 'ni çağırabilirsiniz]( https://azure.microsoft.com/support/options/) . Azure desteği, yalnızca **Standart**, **Geliştirici**ve planlardan daha yüksek planlar gibi seçili planlar için kullanılabilir. Azure desteği, **temel** plan ile kullanılamaz. Farklı destek planları hakkında bilgi edinmek için bkz. [Azure destek planları](https://azure.microsoft.com/support/plans/) sayfası. 

Yedeklemenin belirli bir anlık görüntüsünü geri yüklemek için Azure Cosmos DB, verilerin söz konusu anlık görüntüye ait yedekleme döngüsünün süresi boyunca kullanılabilir olmasını gerektirir.

## <a name="request-a-restore"></a>Geri yükleme iste

Geri yükleme isteğinde bulunulmadan önce aşağıdaki ayrıntılara sahip olmanız gerekir:

* Abonelik KIMLIĞINIZI hazırlayın.

* Verilerinizin yanlışlıkla silinme veya değiştirilme şeklini temel alarak, ek bilgilere sahip olmak için hazırlık yapmanız gerekir. Daha iyi bir süre hassas durumda olabilecek geri ve geriye doğru bir şekilde en aza indirmek için bilgilerin ileride kullanılabilir olması önerilir.

* Azure Cosmos DB hesabının tamamı silinirse, silinen hesabın adını belirtmeniz gerekir. Silinen hesapla aynı ada sahip başka bir hesap oluşturursanız, bunu seçmek için doğru hesabı belirlemesine yardımcı olduğundan destek ekibi ile paylaşabilirsiniz. Geri yükleme durumunun karışıklık olduğunu en aza indirecek için, silinen her bir hesap için farklı destek biletleri dosya dosyası yapmanız önerilir.

* Bir veya daha fazla veritabanı silinirse Azure Cosmos hesabının yanı sıra Azure Cosmos veritabanı adlarını da sağlamanız ve aynı ada sahip yeni bir veritabanının mevcut olup olmadığını belirtmeniz gerekir.

* Bir veya daha fazla kapsayıcı silinirse, Azure Cosmos hesap adını, veritabanı adlarını ve kapsayıcı adlarını sağlamanız gerekir. Ve aynı ada sahip bir kapsayıcının var olup olmadığını belirtin.

* Verilerinizi yanlışlıkla silmiş veya bozdıysanız, Azure Cosmos DB ekibin verileri yedeklerden geri yüklemenize yardımcı olması için, 8 saat içinde [Azure desteği](https://azure.microsoft.com/support/options/) 'ne başvurmalısınız.
  
  * Veritabanınızı veya kapsayıcınızı yanlışlıkla sildiyseniz, bir sev B veya sev C Azure destek talebi açın. 
  * Yanlışlıkla, kapsayıcıda bazı belgeleri sildiyseniz veya bozuksa bir destek talebi açın. 

Veri bozulması oluştuğunda ve bir kapsayıcıdaki belgeler değiştirilirse veya silinirse, **kapsayıcıyı mümkün olan en kısa sürede silin**. Kapsayıcıyı silerek, yedeklemelerin üzerine yazılmasını engellemek Azure Cosmos DB kaçınabilirsiniz. Bazı nedenlerle silme işlemi mümkün değilse, bir bileti mümkün olan en kısa sürede dosya yapmanız gerekir. Azure Cosmos hesap adına, veritabanı adlarına, kapsayıcı adlarına ek olarak, verilerin geri yüklenebileceği zaman noktasını belirtmeniz gerekir. Bu anda en iyi kullanılabilir yedeklemeleri belirlememize yardımcı olmak için mümkün olduğunca kesin olması önemlidir. Saati UTC olarak belirtmek de önemlidir. 

Aşağıdaki ekran görüntüsünde, Azure portal kullanarak verileri geri yüklemek için bir kapsayıcı (koleksiyon/grafik/tablo) için bir destek isteği oluşturma işlemlerinin nasıl yapılacağı gösterilmektedir. İsteğin önceliklendirmemize yardımcı olması için veri türü, geri yükleme amacı, verilerin silindiği zaman gibi ek ayrıntılar sağlayın.

![Azure portal kullanarak bir yedekleme destek isteği oluşturun](./media/how-to-backup-and-restore/backup-support-request-portal.png)

## <a name="post-restore-actions"></a>Geri yükleme sonrası eylemler

Verileri geri yükledikten sonra, yeni hesabın adı (genellikle biçimde `<original-name>-restored1`) ve hesabın geri yüklendiği saat hakkında bir bildirim alırsınız. Geri yüklenen hesap, aynı sağlanmış işleme, dizin oluşturma ilkelerine sahip olacak ve özgün hesapla aynı bölgede. Abonelik Yöneticisi veya coadmin (olan bir Kullanıcı, geri yüklenen hesabı görebilir.

Veriler geri yüklendikten sonra, geri yüklenen hesaptaki verileri inceleyip doğrulamanız ve beklediğiniz sürümü içerdiğinden emin olmanız gerekir. Her şey iyi görünüyorsa, [Azure Cosmos DB değişiklik akışını](change-feed.md) veya [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)kullanarak verileri özgün hesabınıza geri geçirmeniz gerekir.

Verileri geçirdikten hemen sonra kapsayıcıyı veya veritabanını silmeniz önerilir. Geri yüklenen veritabanlarını veya kapsayıcıları silmezseniz, bunlar istek birimleri, depolama ve çıkış maliyeti olur.

## <a name="next-steps"></a>Sonraki adımlar

Ardından, aşağıdaki makaleleri kullanarak verileri özgün hesabınıza geri geçirme hakkında bilgi edinebilirsiniz:

* Geri yükleme isteği oluşturmak için Azure desteğine başvurun, [Azure Portal bir bilet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) oluşturun
* Verileri Azure Cosmos DB taşımak için [Cosmos DB değişiklik akışını kullanın](change-feed.md) .

* Verileri Azure Cosmos DB taşımak için [Azure Data Factory kullanın](../data-factory/connector-azure-cosmos-db.md) .
