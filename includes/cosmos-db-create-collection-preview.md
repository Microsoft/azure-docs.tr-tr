---
title: include dosyası
description: include dosyası
services: cosmos-db
author: deborahc
ms.service: cosmos-db
ms.topic: include
ms.date: 11/19/2018
ms.author: dech
ms.custom: include file
ms.openlocfilehash: 331886f01345aba576cd8f96f95077f9bbdae704
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/15/2019
ms.locfileid: "68002671"
---
Artık bir veritabanı ve kapsayıcı oluşturmak için Azure portal Veri Gezgini aracı 'nı kullanabilirsiniz. 

1. Yeni **Veri Gezgini** > **kapsayıcı**' ya tıklayın. 
    
    **Kapsayıcı Ekle** alanı en sağda görüntülenir, görmek için sağa kaydırmanız gerekebilir.

    ![Azure portal Veri Gezgini, kapsayıcı Ekle dikey penceresi](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection-preview.png)

2. **Kapsayıcı Ekle** sayfasında, yeni kapsayıcının ayarlarını girin.

    |Ayar|Önerilen değer|Açıklama
    |---|---|---|
    |**Veritabanı Kimliği**|Görevler|Yeni veritabanınızın adı olarak *Görevler* girin. Veritabanı adı 1 ila 255 karakterden oluşmalı, boşlukla bitmemeli ve şu karakterleri içermemelidir: /, \\, # ve ?. Veritabanı **Işleme sağlama** seçeneğini kontrol edin, veritabanı içindeki tüm kapsayıcılar üzerinde veritabanı için sağlanan aktarım hızını paylaşmanıza olanak sağlar. Bu seçenek maliyet tasarruflarıyla de yardımcı olur. |
    |**Aktarım hızı**|400|Aktarım hızını saniyede 400 istek birimi (RU/s) olarak bırakın. Daha sonra gecikme süresini azaltmak isterseniz aktarım hızının ölçeğini artırabilirsiniz.| 
    |**Kapsayıcı KIMLIĞI**|Öğeler|*Öğeleri* yeni kapsayıcının adı olarak girin. Kapsayıcı kimlikleri, veritabanı adlarıyla aynı karakter gereksinimlerine sahiptir.|
    |**Bölüm anahtarı**| /kategori| Bu makalede açıklanan örnek, bölüm anahtarı olarak */category* kullanır. Bölüm anahtarının ayarlanması, uygulamanızın depolama ve aktarım hızı ihtiyaçlarını karşılamak için koleksiyonunuzu ölçeklendirmesine Azure Cosmos DB olanak tanır. Genellikle, Bölüm anahtarının iyi bir seçimi, geniş bir farklı değer aralığına sahiptir ve iş yükünüz genelinde depolama ve istek hacminin hatta dağıtımına neden olur. [Bölümlendirme hakkında daha fazla bilgi edinin.](../articles/cosmos-db/partitioning-overview.md)|
    
    Önceki ayarlara ek olarak, kapsayıcı için isteğe bağlı olarak **benzersiz anahtarlar** ekleyebilirsiniz. Bu örnekte bu alanı boş bırakalım. Benzersiz anahtarlar sayesinde geliştiriciler veritabanına bir veri bütünlüğü katmanı ekleyebilir. Bir kapsayıcı oluştururken benzersiz bir anahtar ilkesi oluşturarak, bölüm anahtarı başına bir veya daha fazla değerin benzersizliği olduğundan emin olursunuz. Daha fazla bilgi edinmek için [Azure Cosmos DB'de benzersiz anahtarlar](../articles/cosmos-db/unique-keys.md) makalesine bakın.
    
    **Tamam**’ı seçin. Veri Gezgini yeni veritabanını ve kapsayıcıyı görüntüler.

