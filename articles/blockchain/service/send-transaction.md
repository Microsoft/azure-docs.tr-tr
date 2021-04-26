---
title: Akıllı sözleşmeleri oluşturma, derleme, & dağıtma öğreticisi-Azure blok zinciri hizmeti
description: Azure blok zinciri hizmetinde akıllı bir sözleşme oluşturmak, derlemek ve dağıtmak için Visual Studio Code 'de Ethereum uzantısı için Azure blok zinciri geliştirme seti 'ni kullanma hakkında öğretici.
ms.date: 11/30/2020
ms.topic: tutorial
ms.reviewer: caleteet
ms.openlocfilehash: 4c2df952480d2c30de10838c3d0f7714fc7e6126
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628654"
---
# <a name="tutorial-create-build-and-deploy-smart-contracts-on-azure-blockchain-service"></a>Öğretici: Azure blok zinciri hizmetinde akıllı sözleşmeler oluşturma, derleme ve dağıtma

Bu öğreticide, Azure blok zinciri hizmetinde akıllı bir sözleşme oluşturmak, derlemek ve dağıtmak için Visual Studio Code 'de Ethereum uzantısı için Azure blok zinciri geliştirme setini kullanın. Ayrıca, bir işlem aracılığıyla akıllı sözleşme işlevini yürütmek için geliştirme setini da kullanırsınız.

Ethereum için Azure blok zinciri geliştirme setini şu şekilde kullanabilirsiniz:

> [!div class="checklist"]
> * Akıllı sözleşme oluşturma
> * Akıllı sözleşme dağıtma
> * Bir işlem aracılığıyla akıllı sözleşme işlevini yürütme

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* [Hızlı başlangıç: Azure blok zinciri hizmeti Consortium ağına bağlanmak için Visual Studio Code kullanma](connect-vscode.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Ethereum uzantısı için Azure blok zinciri geliştirme seti](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15. x veya üzeri](https://nodejs.org/download)
* [Git 2.10. x veya üzeri](https://git-scm.com)
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLı 6.0.0](https://github.com/trufflesuite/ganache-cli)

Windows 'da, Node-JP modülü için yüklü bir C++ derleyicisi gereklidir. MSBuild araçlarını kullanabilirsiniz:

* Visual Studio 2017 yüklüyse, NPM 'yi komutuyla MSBuild araçlarını kullanacak şekilde yapılandırın `npm config set msvs_version 2017 -g`
* Visual Studio 2019 yüklüyse, NPM için MS derleme araçları yolunu ayarlayın. Örneğin, `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Aksi takdirde, tek başına VS Build araçlarını kullanarak `npm install --global windows-build-tools` yükseltilmiş bir *yönetici olarak çalıştır* komut kabuğu ' nu kullanın.

Node-cayp hakkında daha fazla bilgi için [GitHub 'daki node-cayp deposuna](https://github.com/nodejs/node-gyp)bakın.

## <a name="create-a-smart-contract"></a>Akıllı sözleşme oluşturma

Ethereum için Azure blok zinciri geliştirme seti, sözleşmeleri dolandırmaya, oluşturmaya ve dağıtmanıza yardımcı olmak için proje şablonları ve truffle araçlarını kullanır. Başlamadan önce önkoşul hızlı başlangıcını doldurun [: Azure blok zinciri hizmeti Consortium ağına bağlanmak için Visual Studio Code kullanın](connect-vscode.md). Hızlı başlangıç, Ethereum için Azure blok zinciri geliştirme setini yükleme ve yapılandırma sürecinde size rehberlik eder.

1. VS Code komut paletinde, **blok Zinciri: New Solidity Project**' i seçin.
1. **Temel proje oluştur** seçeneğini belirleyin.
1. Adlı yeni bir klasör oluşturun `HelloBlockchain` ve **Yeni proje yolu**' nu seçin.

Azure blok zinciri geliştirme seti sizin için yeni bir Solidity projesi oluşturur ve başlatır. Temel proje, örnek bir **Helloblockzincirine** akıllı sözleşme ve Azure blok zinciri hizmeti 'nde Consortium üyelemenize dağıtmak için gereken tüm dosyaları içerir. Projenin oluşturulması birkaç dakika sürebilir. Azure blok zinciri için çıktıyı seçerek VS Code 'ın Terminal panelinde ilerleme durumunu izleyebilirsiniz.

Proje yapısı aşağıdaki örneğe benzer şekilde görünür:

   ![Solidity projesi](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Akıllı sözleşme oluşturma

Akıllı sözleşmeler, projenin **sözleşmeler** dizininde bulunur. Akıllı sözleşmeleri bir blok zincirine dağıtmadan önce derleyebilirsiniz. Projenizdeki tüm akıllı sözleşmeleri derlemek için, **Yapı sözleşmeleri** komutunu kullanın.

1. VS Code Gezgini kenar çubuğunda, projenizdeki **sözleşmeler** klasörünü genişletin.
1. **Helloblockzincirine. Nuevo** öğesine sağ tıklayın ve menüden **sözleşmeleri oluştur** ' u seçin.

    ![Yapı sözleşmeleri menüsünü seçin ](./media/send-transaction/build-contracts.png)

Azure blok zinciri geliştirme seti, akıllı sözleşmeleri derlemek için Truffle kullanır.

![Truffle derleyici çıkışı](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Akıllı sözleşme dağıtma

Truffle, sözleşmelerinizi bir Ethereum ağına dağıtmak için geçiş betikleri kullanır. Geçişler, projenin **geçiş** dizininde bulunan JavaScript dosyalarıdır.

1. Akıllı sözleşmenizi dağıtmak için, **Helloblockzincirine. Nuevo** öğesine sağ tıklayın ve menüden **sözleşmeleri dağıt** ' ı seçin.
1. Komut paletinde Azure blok zinciri Konsorsiyumu ağınızı seçin. Projeyi oluştururken, konsorsiyum blok zinciri ağı projenin Truffle yapılandırma dosyasına eklendi.
1. **Anımsatıcı üret**' i seçin. Bir dosya adı seçin ve anımsatıcı dosyasını proje klasörüne kaydedin. Örneğin, `myblockchainmember.env`. Anımsatıcı dosyası, blok zinciri üyesiyseniz bir Ethereum özel anahtarı oluşturmak için kullanılır.

Azure blok zinciri geliştirme seti, sözleşmeleri blok zincirine dağıtmak üzere geçiş betiğini yürütmek için Truffle kullanır.

![Sözleşme başarıyla dağıtıldı](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function&quot;></a>Sözleşme işlevini çağırma
**Helloblockzincirleri** sözleşmesinin **SendRequest** işlevi **RequestMessage** durum değişkenini değiştirir. Bir blok zinciri ağının durumunun değiştirilmesi bir işlem aracılığıyla yapılır. **SendRequest** işlevini bir işlem aracılığıyla yürütmek için bir komut dosyası oluşturabilirsiniz.

1. Truffle projenizin kökünde yeni bir dosya oluşturun ve bunu adlandırın `sendrequest.js` . Aşağıdaki Web3 JavaScript kodunu dosyaya ekleyin.

    ```javascript
    var HelloBlockchain = artifacts.require(&quot;HelloBlockchain");
        
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling SendRequest function for contract ", instance.address);
        return instance.SendRequest("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Azure blok zinciri geliştirme seti bir proje oluşturduğunda, Truffle yapılandırma dosyası, Consortium blok zinciri ağ uç noktası ayrıntılarınız ile oluşturulur. Projenizdeki **truffle-config.js** açın. Yapılandırma dosyasında iki ağ listelenir: bir tane geliştirme ve bir tane, konsorsiyum ile aynı ada sahip.
1. VS Code Terminal bölmesinde, komut dosyasını Konsorsiyumu blok zinciri ağınızda yürütmek için Truffle ' yi kullanın. Terminal bölmesi menü çubuğunda açılan menüde **Terminal** sekmesini ve **PowerShell** ' i seçin.

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    \<blockchain network\> **truffle-config.js** tanımlanan blok zinciri ağının adıyla değiştirin.

Truffle, betiği blok zinciri ağınızda yürütür.

![İşlemin gönderildiğini gösteren çıkış](./media/send-transaction/execute-transaction.png)

Bir sözleşmenin işlevini bir işlem aracılığıyla yürüttüğünüzde, bir blok oluşturuluncaya kadar işlem işlenmez. Bir işlem yoluyla yürütülmesi amaçlanan işlevler, dönüş değeri yerine bir işlem KIMLIĞI döndürür.

## <a name="query-contract-state"></a>Sözleşme durumunu sorgula

Akıllı sözleşme işlevleri, durum değişkenlerinin geçerli değerini döndürebilir. Bir durum değişkeninin değerini döndürecek bir işlev ekleyelim.

1. **Helloblockzincirine. Nuevo** Içinde, **helloblockzincirine** akıllı sözleşmeye bir **GetMessage** işlevi ekleyin.

    ``` solidity
    function getMessage() public view returns (string memory)
    {
        if (State == StateType.Request)
            return RequestMessage;
        else
            return ResponseMessage;
    }
    ```

    İşlevi, sözleşmenin geçerli durumuna bağlı olarak bir durum değişkeninde depolanan iletiyi döndürür.

1. Akıllı sözleşmede değişiklikleri derlemek için **Helloblockzincirine. Nuevo** öğesine sağ tıklayın ve menüden **sözleşmeleri derle** ' yi seçin.
1. Dağıtmak için, **Helloblockzincirine. Nuevo** öğesine sağ tıklayın ve menüden **sözleşmeleri dağıt** ' ı seçin. İstendiğinde, komut paletinde Azure blok zinciri Konsorsiyumu ağınızı seçin.
1. Sonra, **GetMessage** işlevini çağırmak için kullanarak bir betik oluşturun. Truffle projenizin kökünde yeni bir dosya oluşturun ve bunu adlandırın `getmessage.js` . Aşağıdaki Web3 JavaScript kodunu dosyaya ekleyin.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling getMessage function for contract ", instance.address);
        return instance.getMessage();
      }).then(function(result) {
        console.log("Request message value: ", result);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. VS Code, Terminal bölmesinde, blok zinciri ağınızda betiği yürütmek için Truffle kullanın. Terminal bölmesi menü çubuğunda açılan menüde **Terminal** sekmesini ve **PowerShell** ' i seçin.

    ```bash
    truffle exec getmessage.js --network <blockchain network>
    ```

    \<blockchain network\> **truffle-config.js** tanımlanan blok zinciri ağının adıyla değiştirin.

Betik, getMessage işlevini çağırarak akıllı sözleşmeyi sorgular. **RequestMessage** durum değişkeninin geçerli değeri döndürüldü.

![RequestMessage durum değişkeninin geçerli değerini gösteren GetMessage sorgusundan alınan çıkış](./media/send-transaction/execute-get.png)

Değerin **Merhaba, blok zinciri!** olmadığına dikkat edin. Bunun yerine, döndürülen değer bir yer tutucudur. Sözleşmeyi değiştirdiğinizde ve dağıttığınızda, değiştirilen sözleşme yeni bir adreste dağıtılır ve durum değişkenlerine akıllı sözleşme oluşturucusunda değerler atanır. Truffle örnek **2_deploy_contracts.js** geçiş betiği, akıllı sözleşmeyi dağıtır ve bir yer tutucu değerini bağımsız değişken olarak geçirir. Oluşturucu **RequestMessage** durum değişkenini yer tutucu değerine ayarlar ve döndürülen değer.

1. **RequestMessage** durum değişkenini ayarlamak ve değeri sorgulamak için **sendrequest.js** ve **getmessage.js** betikleri yeniden çalıştırın.

    ![İstek Iletisini gösteren SendRequest ve GetMessage betikleri çıkışı ayarlandı](./media/send-transaction/execute-set-get.png)

    **sendrequest.js** **RequestMessage** durum değişkenini **Merhaba, blok zinciri!** olarak ayarlar **getmessage.js** , **isteği RequestMessage** durum değişkeninin değeri için sorgular ve **Merhaba, blok zinciri!** döndürür.
## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, `myResourceGroup` *blok zinciri üye önkoşulu oluştur* hızlı başlangıcı ' nda oluşturduğunuz kaynak grubunu silerek kaynakları silebilirsiniz.

Kaynak grubunu silmek için:

1. Azure portal sol gezinti bölmesindeki **kaynak grubu** ' na gidin ve silmek istediğiniz kaynak grubunu seçin.
1. **Kaynak grubunu sil**'i seçin. Kaynak grubu adını girip **Sil**' i seçerek silmeyi doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure blok zinciri geliştirme seti 'ni kullanarak örnek bir Solidity projesi oluşturdunuz. Azure blok zinciri hizmeti 'nde barındırılan bir blok zinciri konsorsiyum ağı üzerinde bir işlem aracılığıyla işlev olarak adlandırılan akıllı bir sözleşmeyi oluşturup dağıttığınız.

> [!div class="nextstepaction"]
> [Azure blok zinciri hizmetini kullanarak blok zinciri uygulamaları geliştirme](develop.md)
