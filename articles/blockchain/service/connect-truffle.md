---
title: Truffle kullanarak bağlanma
description: Truffle kullanarak bir Azure blok zinciri hizmeti ağına bağlanma
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 08/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: ea64e3fe4789ac0558463ded81c8179db83469d5
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932592"
---
# <a name="quickstart-use-truffle-to-connect-to-a-transaction-node"></a>Hızlı Başlangıç: İşlem düğümüne bağlanmak için Truffle kullanma

Truffle, bir Azure blok zinciri hizmeti işlem düğümüne bağlanmak için kullanabileceğiniz bir blok zinciri geliştirme ortamıdır.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* [Hızlı başlangıç: Azure Portal](create-member.md) veya[hızlı başlangıcı kullanarak bir blok zinciri üyesi oluşturun: Azure CLı kullanarak Azure blok zinciri hizmeti blok zinciri üyesi oluşturma](create-member-cli.md)
* [Truffle](https://github.com/trufflesuite/truffle)'yi yükler. Truffle, [Node. js](https://nodejs.org), [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)gibi çeşitli araçların yüklenmesini gerektirir.
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/)'i yükler. Web3 için Python gereklidir.

## <a name="create-truffle-project"></a>Truffle projesi oluştur

1. Node. js komut istemi veya kabuğu açın.
1. Dizini, Truffle proje dizinini oluşturmak istediğiniz yere değiştirin.
1. Proje için bir dizin oluşturun ve yolu yeni dizin ile değiştirin. Örneğin,

    ``` bash
    mkdir truffledemo
    cd truffledemo
    ```

1. Truffle projesini başlatın.

    ``` bash
    truffle init
    ```

1. Proje klasörüne Ethereum JavaScript API Web3 'yi yükler. Şu anda sürüm Web3 sürüm 1.0.0-Beta. 37 gereklidir.

    ``` bash
    npm install web3@1.0.0-beta.37
    ```

    Yükleme sırasında NPM uyarıları alabilirsiniz.
    
## <a name="configure-truffle-project"></a>Truffle projesini yapılandırma

Truffle projesini yapılandırmak için Azure portal işlem düğümü bilgilerine ihtiyacınız vardır.

### <a name="transaction-node-endpoint-addresses"></a>İşlem düğümü uç noktası adresleri

1. Azure portal, varsayılan işlem düğümüne gidin ve **bağlantı dizelerini > işlem düğümleri**' ni seçin.
1. Uç nokta URL 'sini https 'den kopyalayın ve kaydedin **(erişim anahtarı 1)** . Öğreticide daha sonra akıllı sözleşme yapılandırma dosyası için uç nokta adreslerine ihtiyacınız vardır.

    ![İşlem uç noktası adresi](./media/connect-truffle/endpoint.png)

### <a name="edit-configuration-file"></a>Yapılandırma dosyasını Düzenle

Ardından, Truffle yapılandırma dosyasını işlem düğümü uç noktasıyla güncelleştirmeniz gerekir.

1. **Trufftademo** proje klasöründe, bir düzenleyicide Truffle yapılandırma dosyasını `truffle-config.js` açın.
1. Dosyanın içeriğini aşağıdaki yapılandırma bilgileriyle değiştirin. Uç nokta adresini içeren bir değişken ekleyin. Açılı ayracını önceki bölümden topladığınız değerlerle değiştirin.

    ``` javascript
    var defaultnode = "<default transaction node connection string>";   
    var Web3 = require("web3");
    
    module.exports = {
      networks: {
        defaultnode: {
          provider: new Web3.providers.HttpProvider(defaultnode),
          network_id: "*"
        }
      }
    }
    ```

1. Değişiklikleri `truffle-config.js`kaydedin.

## <a name="connect-to-transaction-node"></a>İşlem düğümüne bağlanma

İşlem düğümüne bağlanmak için *Web3* kullanın.

1. Varsayılan işlem düğümüne bağlanmak için Truffle konsolunu kullanın. Komut isteminde veya kabukta aşağıdaki komutu çalıştırın:

    ``` bash
    truffle console --network defaultnode
    ```

    Truffle varsayılan işlem düğümüne bağlanır ve etkileşimli bir konsol sağlar.

    İşlem düğümünüz ile etkileşim kurmak için **Web3** nesnesi üzerinde yöntemler çağırabilirsiniz.

1. Geçerli blok numarasını döndürmek için **Getblocknumber** yöntemini çağırın.

    ```bash
    web3.eth.getBlockNumber();
    ```

    Örnek çıktı:

    ```bash
    truffle(defaultnode)> web3.eth.getBlockNumber();
    18567
    ```
1. Truffle konsolundan çıkın.

    ```bash
    .exit
    ```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure blok zinciri hizmeti varsayılan işlem düğümünüz ile bağlantı kurmak için bir truffle projesi oluşturdunuz.

Bir işlem aracılığıyla akıllı sözleşme işlevi oluşturmak, derlemek, dağıtmak ve yürütmek için Ethereum ve truffle için Azure blok zinciri geliştirme setini kullanmak üzere bir sonraki öğreticiyi deneyin.

> [!div class="nextstepaction"]
> [Akıllı sözleşmeleri oluşturmak, derlemek ve dağıtmak için Visual Studio Code kullanma](send-transaction.md)
