---
title: Verilerinizi yönetmek için Azure Cosmos DB gezginini kullanın
description: Azure Cosmos DB Explorer, Azure Cosmos DB depolanan verileri görüntülemenize ve yönetmenize olanak tanıyan tek başına bir Web tabanlı arabirimdir.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/23/2020
ms.author: dech
ms.openlocfilehash: d4baa92fe4aa2ed402c394198684c4deec2bf9f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96348611"
---
# <a name="work-with-data-using-azure-cosmos-db-explorer"></a>Azure Cosmos DB gezginini kullanarak verilerle çalışma 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB Explorer, Azure Cosmos DB depolanan verileri görüntülemenize ve yönetmenize olanak tanıyan tek başına bir Web tabanlı arabirimdir. Azure Cosmos DB gezgin, Azure Cosmos DB bir hesap oluştururken Azure portal kullanılabilen mevcut **Veri Gezgini** sekmeye eşdeğerdir. Azure Cosmos DB Explorer 'ın mevcut veri Gezgini üzerinde başlıca avantajları şunlardır:

* Verilerinizi görüntülemek, sorguları çalıştırmak, saklı yordamları, Tetikleyicileri ve sonuçlarını görüntülemek için gerçek bir tam ekran vardır.  

* Veritabanı hesabınıza ve koleksiyonlarına Azure portal veya aboneliğine erişimi olmayan diğer kullanıcılara geçici veya kalıcı okuma veya okuma erişimi sağlayabilirsiniz.  

* Sorgu sonuçlarını Azure portal veya aboneliğine erişimi olmayan diğer kullanıcılarla paylaşabilirsiniz.  

## <a name="access-azure-cosmos-db-explorer"></a>Azure Cosmos DB Gezgini 'ne erişin

1. [Azure Portal](https://portal.azure.com/)oturum açın. 

2. **Tüm kaynaklardan** Azure Cosmos DB hesabınıza gidin, anahtarlar ' ı seçin ve **birincil bağlantı dizesini** kopyalayın.  

3. Adresine gidin https://cosmos.azure.com/ , bağlantı dizesini yapıştırın ve **Bağlan**' ı seçin. Bağlantı dizesini kullanarak, Azure Cosmos DB Gezgini 'ne herhangi bir zaman sınırlaması olmadan erişebilirsiniz.  

   Diğer kullanıcıların Azure Cosmos DB hesabınıza geçici olarak erişmesini sağlamak istiyorsanız, okuma-yazma ve okuma erişimi URL 'Lerini kullanarak bunu yapabilirsiniz. 

4. **Veri Gezgini** dikey penceresini açın, **tam ekran aç**' ı seçin. Açılır iletişim kutusunda, iki erişim URL 'sini görüntüleyebilirsiniz – **okuma-yazma** ve **okuma**. Bu URL 'Ler Azure Cosmos DB hesabınızı diğer kullanıcılarla geçici olarak paylaşmanızı sağlar. Hesap erişimi, yeni bir erişim URL 'SI veya bağlantı dizesi kullanarak yeniden bağlanabildikten sonra 24 saat sonra sona erer. 

   **Okuma-yazma** : URL 'yi diğer kullanıcılarla Read-Write paylaştığınızda, bu ilgili hesapla ilişkili veritabanlarını, koleksiyonları, sorguları ve diğer kaynakları görüntüleyebilir ve değiştirebilirler.

   **Okuma** -salt okuma URL 'sini diğer kullanıcılarla paylaştığınızda, bu ilgili hesapla ilişkili veritabanlarını, koleksiyonları, sorguları ve diğer kaynakları görüntüleyebilirler. Örneğin, bir sorgunun sonuçlarını Azure portal veya Azure Cosmos DB hesabınıza erişimi olmayan takım arkadaşlarınızla paylaşmak istiyorsanız, bu URL ile birlikte sağlayabilirsiniz.

   Hesabı açmak istediğiniz erişim türünü seçin ve **Aç**' a tıklayın. Gezgin 'i açtıktan sonra, deneyim, Azure portal Veri Gezgini sekmesi ile aynı olur.

   :::image type="content" source="./media/data-explorer/open-data-explorer-with-access-url.png" alt-text="Azure Cosmos DB Gezgini'ni açın":::

## <a name="known-issues"></a>Bilinen sorunlar

Şu anda, Azure Cosmos DB Gremlin ve Tablo API'si hesapları için geçici okuma/yazma veya okuma erişimini paylaşmanıza olanak sağlayan **açık tam ekran** deneyimi henüz desteklenmiyor. Bağlantı dizesini Azure Cosmos DB Explorer 'a geçirerek Gremlin ve Tablo API'si hesaplarınızı görüntülemeye devam edebilirsiniz. 

Şu anda, bir UUID içeren belgeleri görüntülemek Veri Gezgini desteklenmez. Bu, yalnızca bu belgeleri içeren tek tek belgeleri veya sorguları görüntülemek için koleksiyonları yüklemeyi etkilemez. Bu belgeleri görüntülemek ve yönetmek için, kullanıcılar başlangıçta bu belgeleri oluşturmak için kullanılan aracı kullanmaya devam etmelidir.

HTTP-401 hataları alan müşteriler, özellikle de hesabın özel bir rolü varsa, müşterinin Azure hesabı için Azure RBAC izinlerinin yetersiz olması nedeniyle olabilir. Herhangi bir özel rol `Microsoft.DocumentDB/databaseAccounts/listKeys/*` Azure Active Directory kimlik bilgilerini kullanarak oturum açıyorsanız Veri Gezgini kullanmak için eyleme sahip olmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

Artık verilerinizi yönetmek için Azure Cosmos DB Gezgini 'ni kullanmaya nasıl başladığınıza öğrendiğinize göre şunları yapabilirsiniz:

* SQL söz dizimini kullanarak [sorgu](./sql-query-getting-started.md) tanımlamayı başlatın ve saklı yordamları, UDF 'leri, Tetikleyicileri kullanarak [sunucu tarafı programlama](stored-procedures-triggers-udfs.md) işlemi gerçekleştirin.
