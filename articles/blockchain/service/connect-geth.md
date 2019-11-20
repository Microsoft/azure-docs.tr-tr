---
title: Azure blok zinciri hizmeti 'ne bağlanmak için geth kullanma
description: Geth kullanarak bir Azure blok zinciri hizmeti ağına bağlanma
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: a26899e291c4f44d3c9d91032b2ee191ba03133a
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70931768"
---
# <a name="quickstart-use-geth-to-connect-to-a-transaction-node"></a>Hızlı Başlangıç: İşlem düğümüne bağlanmak için Geth kullanma

Geth, bir Azure blok zinciri hizmeti işlem düğümündeki bir geth örneğine iliştirmek için kullanabileceğiniz bir go Ethereum istemcidir.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* [Geth](https://github.com/ethereum/go-ethereum/wiki/geth) 'ı yükler
* [Hızlı başlangıç: Azure Portal](create-member.md) veya[hızlı başlangıcı kullanarak bir blok zinciri üyesi oluşturun: Azure CLı kullanarak Azure blok zinciri hizmeti blok zinciri üyesi oluşturma](create-member-cli.md)

## <a name="get-the-geth-connection-string"></a>Geth bağlantı dizesini al

Geth bağlantı dizesini Azure portal bulabilirsiniz.

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. Azure blok zinciri hizmeti üyesine gidin. **İşlem düğümleri** ve varsayılan işlem düğümü bağlantısı ' nı seçin.

    ![Varsayılan işlem düğümünü seçin](./media/connect-geth/transaction-nodes.png)

1. **Bağlantı dizelerini**seçin.
1. Bağlantı dizesini **https 'den (erişim anahtarı 1)** kopyalayın. Sonraki bölüm için komutuna ihtiyacınız vardır.

    ![Bağlantı dizesi](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Geth 'a bağlanma

1. Bir komut istemi veya kabuk açın.
1. İşlem düğümünüz üzerinde çalışan geth örneğine iliştirmek için geth Attach alt komutunu kullanın. Bağlantı dizesini Attach alt komutu için bir bağımsız değişken olarak yapıştırın. Örneğin,

    ```
    geth attach <connection string>
    ```

1. İşlem düğümünün Ethereum konsoluna bağlandıktan sonra Web3 JavaScript Dapp API 'sini veya yönetim API 'sini çağırabilirsiniz.

    Örneğin, Chainıd 'yi bulmak için aşağıdaki API 'yi kullanın.

    ```bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    Bu örnekte, Chainıd 297 ' dir.

    ![Azure blok zinciri hizmeti seçeneği](./media/connect-geth/geth-attach.png)

1. Konsola bağlantıyı kesmek için yazın `exit`.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure blok zinciri hizmeti işlem düğümündeki bir geth örneğine eklemek için geth istemcisini kullandınız. Bir işlem aracılığıyla akıllı sözleşme işlevi oluşturmak, derlemek, dağıtmak ve yürütmek için Ethereum ve truffle için Azure blok zinciri geliştirme setini kullanmak üzere bir sonraki öğreticiyi deneyin.

> [!div class="nextstepaction"]
> [Akıllı sözleşmeleri oluşturmak, derlemek ve dağıtmak için Visual Studio Code kullanma](send-transaction.md)