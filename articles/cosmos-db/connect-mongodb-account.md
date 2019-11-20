---
title: MongoDB uygulamasını Azure Cosmos DB bağlama
description: MongoDB uygulamanızı Azure Cosmos DB bağlama hakkında bilgi edinin.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 05/21/2019
ms.reviewer: sngun
ms.openlocfilehash: 30b37b86bbe0e91887932423fa1884d8bd49333c
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757000"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>MongoDB uygulamasını Azure Cosmos DB bağlama
MongoDB uygulamanızı bir Azure Cosmos DB MongoDB bağlantı dizesi kullanarak nasıl bağlayacağınızı öğrenin. Daha sonra, MongoDB uygulamanız için veri deposu olarak bir Azure Cosmos veritabanını kullanabilirsiniz. 

Bu öğreticide bağlantı dizesi bilgilerinin alınması için iki yol sunulmaktadır:

- .NET, Node. js, MongoDB kabuğu, Java ve Python sürücüleriyle kullanım için [hızlı başlangıç yöntemi](#QuickstartConnection)
- Diğer sürücülerle kullanılmak üzere [özel bağlantı dizesi yöntemi](#GetCustomConnection)

## <a name="prerequisites"></a>Önkoşullar

- Bir Azure hesabı. Azure hesabınız yoksa, şimdi [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun. 
- Cosmos hesabı. Yönergeler için bkz. [MongoDB ve .NET SDK için Azure Cosmos DB API 'sini kullanarak Web uygulaması oluşturma](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Hızlı Başlangıç kullanarak MongoDB bağlantı dizesini alın
1. Bir Internet tarayıcısında [Azure Portal](https://portal.azure.com)oturum açın.
2. **Azure Cosmos DB** dikey PENCERESINDE, API 'yi seçin. 
3. Hesap dikey penceresinin sol bölmesinde **hızlı başlangıç**' a tıklayın. 
4. Platformunuzu seçin ( **.net**, **Node. js**, **MongoDB kabuğu**, **Java**, **Python**). Sürücü veya aracınız listede görmüyorsanız endişelenmeyin, sürekli olarak daha fazla bağlantı kodu parçacığı belgeliyoruz. Görmek istediğiniz şekilde lütfen aşağıdaki açıklamayı inceleyin. Kendi bağlantınızı oluşturmayı öğrenmek için [hesabın bağlantı dizesi bilgilerini](#GetCustomConnection)okuyun.
5. Kod parçacığını kopyalayıp MongoDB uygulamanıza yapıştırın.

    ![Hızlı başlangıç dikey penceresi](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a>Özelleştirecek MongoDB bağlantı dizesini al
1. Bir Internet tarayıcısında [Azure Portal](https://portal.azure.com)oturum açın.
2. **Azure Cosmos DB** dikey PENCERESINDE, API 'yi seçin. 
3. Hesap dikey penceresinin sol bölmesinde **bağlantı dizesi**' ne tıklayın. 
4. **Bağlantı dizesi** dikey penceresi açılır. Önceden oluşturulmuş bir bağlantı dizesi dahil olmak üzere MongoDB için bir sürücü kullanarak hesaba bağlanmak için gerekli tüm bilgilere sahiptir.

    ![Bağlantı Dizesi dikey penceresi](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Bağlantı dizesi gereksinimleri
> [!Important]
> Azure Cosmos DB katı güvenlik gereksinimleri ve standartları vardır. Azure Cosmos DB hesapların *SSL*aracılığıyla kimlik doğrulaması ve güvenli iletişim olması gerekir. 
>
>

Azure Cosmos DB, standart MongoDB bağlantı dizesi URI biçimini destekler ve birkaç belirli gereksinimi vardır: Azure Cosmos DB hesapları, SSL aracılığıyla kimlik doğrulaması ve güvenli iletişim gerektirir. Bu nedenle, bağlantı dizesi biçimi:

    mongodb://username:password@host:port/[database]?ssl=true

Bu dizenin değerleri, daha önce gösterilen **bağlantı dizesi** dikey penceresinde kullanılabilir:

* Kullanıcı adı (gerekli): Cosmos hesap adı.
* Parola (gerekli): Cosmos hesap parolası.
* Ana bilgisayar (gerekli): Cosmos hesabının FQDN 'SI.
* Bağlantı noktası (gerekli): 10255.
* Veritabanı (isteğe bağlı): bağlantının kullandığı veritabanı. Veritabanı sağlanmazsa, varsayılan veritabanı "test" dir.
* SSL = true (gerekli)

Örneğin, **bağlantı dizesi** dikey penceresinde gösterilen hesabı göz önünde bulundurun. Geçerli bir bağlantı dizesi:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Sonraki adımlar

- [Studio 3T](mongodb-mongochef.md) 'ı Azure Cosmos DB MongoDB IÇIN API 'si ile nasıl kullanacağınızı öğrenin.
- MongoDB için Azure Cosmos DB API 'SI ile [Robo 3T kullanmayı](mongodb-robomongo.md) öğrenin.
- MongoDB için Azure Cosmos DB API 'siyle MongoDB [örneklerini](mongodb-samples.md) gezin.
