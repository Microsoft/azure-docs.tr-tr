---
title: Azure Cosmos DB bağlanmak için Robo 3T kullanın
description: MongoDB için Robo 3T ve Azure Cosmos DB API 'sini kullanarak Azure Cosmos DB nasıl bağlanacağınızı öğrenin
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/23/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: e096de5eea9bf5bbd47b976976192e62eb6aa33e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93096421"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB için Azure Cosmos DB API'siyle Robo 3T kullanma
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Robo 3T kullanarak Cosmos hesabına bağlanmak için şunları yapmanız gerekir:

* [Robo 3T](https://robomongo.org/) indirme ve yükleme
* Cosmos DB [bağlantı dizesi](connect-mongodb-account.md) bilgilerinizi içermelidir

> [!NOTE]
> Şu anda Robo 3T v 1.2 ve alt sürümleri, Cosmos DB MongoDB için API 'SI ile desteklenmektedir.

## <a name="connect-using-robo-3t"></a>Robo 3T kullanarak bağlanma

Cosmos hesabınızı Robo 3T bağlantı yöneticisine eklemek için aşağıdaki adımları uygulayın:

1. Azure Cosmos DB API MongoDB ile yapılandırılan Cosmos hesabınız için [buradaki](connect-mongodb-account.md)yönergeleri kullanarak bağlantı bilgilerini alın.

    :::image type="content" source="./media/mongodb-robomongo/connectionstringblade.png" alt-text="Bağlantı dizesi dikey penceresinin ekran görüntüsü":::
2. *Robomongo 'yu* uygulamasını çalıştırın.

3. Bağlantılarınızı yönetmek için **Dosya** altındaki bağlantı düğmesine tıklayın. Ardından, **MongoDB bağlantıları** penceresinde **Oluştur** ' a tıklayarak **bağlantı ayarları** penceresini açarsınız.

4. **Bağlantı ayarları** penceresinde bir ad seçin. Ardından, 1. adımdaki bağlantı bilgilerinizin **ana bilgisayar** ve **bağlantı noktasını** bulun ve sırasıyla **Adres** ve **bağlantı noktasına** girin.

    :::image type="content" source="./media/mongodb-robomongo/manageconnections.png" alt-text="Robomongo 'yu Manage bağlantılarının ekran görüntüsü":::
5. **Kimlik doğrulama** sekmesinde **kimlik doğrulama gerçekleştir**' e tıklayın. Ardından, veritabanınızı (varsayılan *yönetici*), **Kullanıcı adını** ve **parolayı** girin.
**Kullanıcı adı** ve **parola** , adım 1 ' deki bağlantı bilgileriniz içinde bulunabilir.

    :::image type="content" source="./media/mongodb-robomongo/authentication.png" alt-text="Robomongo 'yu kimlik doğrulama sekmesinin ekran görüntüsü":::
6. **SSL** SEKMESINDE, **SSL protokolünü kullan**' ı Işaretleyin ve **kimlik doğrulama yöntemini** **otomatik olarak imzalanan sertifikayla** değiştirin.

    :::image type="content" source="./media/mongodb-robomongo/SSL.png" alt-text="Robomongo 'yu SSL sekmesinin ekran görüntüsü":::
7. Son olarak, bağlanabilediğinizi doğrulamak için **Test** ' e tıklayın, sonra **kaydedin**.

## <a name="next-steps"></a>Sonraki adımlar

- [Studio 3T](mongodb-mongochef.md) 'ı Azure Cosmos DB MongoDB IÇIN API 'si ile nasıl kullanacağınızı öğrenin.
- MongoDB için Azure Cosmos DB API 'siyle MongoDB [örneklerini](mongodb-samples.md) gezin.
