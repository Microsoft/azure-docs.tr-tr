---
title: Azure Cosmos DB’ye MongoDB uygulaması bağlama
description: Azure portal bir MongoDB uygulamasını bağlantı dizesi ile Azure Cosmos DB bağlama hakkında bilgi edinin
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.reviewer: sngun
adobe-target: true
adobe-target-activity: DocsExp-A/B-384740-MongoDB-2.8.2021
adobe-target-experience: Experience B
adobe-target-content: ./connect-mongodb-account-experimental
ms.openlocfilehash: b3e10931307914f1471b8a6fbffd38953ee4717b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101659486"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Azure Cosmos DB’ye MongoDB uygulaması bağlama
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

MongoDB bağlantı dizesi kullanarak MongoDB uygulamanızı Azure Cosmos DB'ye bağlamayı öğrenin. Ardından MongoDB uygulamanızın veri deposu olarak bir Azure Cosmos veritabanı kullanabilirsiniz.

Bu öğreticide bağlantı dizesi bilgilerini almak için iki yol sağlanır:

- .NET, Node.js, MongoDB kabuğu, Java ve Python sürücüleriyle kullanım için [hızlı başlangıç yöntemi](#get-the-mongodb-connection-string-by-using-the-quick-start)
- Diğer sürücülerle kullanılmak üzere [özel bağlantı dizesi yöntemi](#get-the-mongodb-connection-string-to-customize)

## <a name="prerequisites"></a>Önkoşullar

- Azure hesabı. Azure hesabınız yoksa, şimdi [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.
- Cosmos hesabı. Yönergeler için bkz. [MongoDB ve .NET SDK için Azure Cosmos DB API 'sini kullanarak Web uygulaması oluşturma](create-mongodb-dotnet.md).

## <a name="get-the-mongodb-connection-string-by-using-the-quick-start"></a>Hızlı Başlangıç kullanarak MongoDB bağlantı dizesini alın

1. Bir Internet tarayıcısında [Azure Portal](https://portal.azure.com)oturum açın.
2. **Azure Cosmos DB** dikey PENCERESINDE, API 'yi seçin.
3. Hesap dikey penceresinin sol bölmesinde **hızlı başlangıç**' a tıklayın.
4. Platformunuzu (**.net**, **Node.js**, **MongoDB kabuğu**, **Java**, **Python**) seçin. Sürücü veya aracınız listede görmüyorsanız endişelenmeyin, sürekli olarak daha fazla bağlantı kodu parçacığı belgeliyoruz. Görmek istediğiniz şekilde lütfen aşağıdaki açıklamayı inceleyin. Kendi bağlantınızı oluşturmayı öğrenmek için [hesabın bağlantı dizesi bilgilerini](#get-the-mongodb-connection-string-to-customize)okuyun.
5. Kod parçacığını kopyalayıp MongoDB uygulamanıza yapıştırın.

    :::image type="content" source="./media/connect-mongodb-account/QuickStartBlade.png" alt-text="Hızlı başlangıç dikey penceresi":::

## <a name="get-the-mongodb-connection-string-to-customize"></a>Özelleştirecek MongoDB bağlantı dizesini al

1. Bir Internet tarayıcısında [Azure Portal](https://portal.azure.com)oturum açın.
2. **Azure Cosmos DB** dikey PENCERESINDE, API 'yi seçin.
3. Hesap dikey penceresinin sol bölmesinde **bağlantı dizesi**' ne tıklayın.
4. **Bağlantı dizesi** dikey penceresi açılır. Önceden oluşturulmuş bir bağlantı dizesi dahil olmak üzere MongoDB için bir sürücü kullanarak hesaba bağlanmak için gerekli tüm bilgilere sahiptir.

   :::image type="content" source="./media/connect-mongodb-account/ConnectionStringBlade.png" alt-text="Bağlantı Dizesi dikey penceresi" lightbox= "./media/connect-mongodb-account/ConnectionStringBlade.png" :::

## <a name="connection-string-requirements"></a>Bağlantı dizesi gereksinimleri

> [!Important]
> Azure Cosmos DB katı güvenlik gereksinimleri ve standartları vardır. Azure Cosmos DB hesapları, *TLS* aracılığıyla kimlik doğrulaması ve güvenli iletişim gerektirir.

Azure Cosmos DB, standart MongoDB bağlantı dizesi URI biçimini destekler ve birkaç belirli gereksinimi vardır: Azure Cosmos DB hesapları, TLS aracılığıyla kimlik doğrulaması ve güvenli iletişim gerektirir. Bu nedenle, bağlantı dizesi biçimi:

`mongodb://username:password@host:port/[database]?ssl=true`

Bu dizenin değerleri, daha önce gösterilen **bağlantı dizesi** dikey penceresinde kullanılabilir:

* Kullanıcı adı (gerekli): Cosmos hesap adı.
* Parola (gerekli): Cosmos hesap parolası.
* Ana bilgisayar (gerekli): Cosmos hesabının FQDN 'SI.
* Bağlantı noktası (gerekli): 10255.
* Veritabanı (isteğe bağlı): bağlantının kullandığı veritabanı. Veritabanı sağlanmazsa, varsayılan veritabanı "test" dir.
* SSL = true (gerekli)

Örneğin, **bağlantı dizesi** dikey penceresinde gösterilen hesabı göz önünde bulundurun. Geçerli bir bağlantı dizesi:

`mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true`

## <a name="driver-requirements"></a>Sürücü gereksinimleri

Tel Protokolü sürüm 3,4 veya üstünü destekleyen tüm sürücüler MongoDB için Azure Cosmos DB API 'YI destekleyecektir.

Özellikle, istemci sürücüleri hizmet adı tanımlama (SNı) TLS uzantısını ve/veya appName bağlantı dizesi seçeneğini desteklemelidir. `appName`Parametresi sağlanmışsa, Azure Portal bağlantı dizesi değerinde bulunan ' de bulunması gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Studio 3T](mongodb-mongochef.md) 'ı Azure Cosmos DB MongoDB IÇIN API 'si ile nasıl kullanacağınızı öğrenin.
- MongoDB için Azure Cosmos DB API 'SI ile [Robo 3T kullanmayı](mongodb-robomongo.md) öğrenin.
- MongoDB için Azure Cosmos DB API 'siyle MongoDB [örneklerini](mongodb-samples.md) gezin.
