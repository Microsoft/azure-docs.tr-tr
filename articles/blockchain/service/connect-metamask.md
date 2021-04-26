---
title: MetaMask 'i Azure blok zinciri hizmeti ağına bağlama
description: MetaMask kullanarak bir Azure blok zinciri hizmeti ağına bağlanın ve akıllı bir sözleşme dağıtın.
ms.date: 09/12/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 4a45e02a861ff20a4dc774668a4e008f9b42aeea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530446"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Hızlı başlangıç: akıllı bir sözleşmeyi bağlamak ve dağıtmak için MetaMask kullanma

Bu hızlı başlangıçta, bir Azure blok zinciri hizmeti ağına bağlanmak ve akıllı bir sözleşmeyi dağıtmak için yeniden karışımı kullanmak üzere MetaMask kullanacaksınız. Metamask, bir Ether cüzdan yönetmek ve akıllı sözleşme eylemleri gerçekleştirmek için bir tarayıcı uzantısıdır.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* [Hızlı başlangıç: Azure Portal veya hızlı başlangıç kullanarak bir blok zinciri üyesi oluşturma](create-member.md) [: Azure CLI kullanarak bir Azure blok zinciri hizmeti blok zinciri üyesi](create-member-cli.md) oluşturma
* [MetaMask Browser uzantısını](https://metamask.io) yükler
* MetaMask [cüzdan](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time) oluşturma

## <a name="get-endpoint-address"></a>Uç nokta adresini al

Blok zinciri ağına bağlanmak için Azure blok zinciri hizmeti uç noktası adresine ihtiyacınız vardır. Uç nokta adresi ve erişim anahtarları Azure portal.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Azure blok zinciri hizmeti üyesine gidin.
1. **İşlem düğümleri** ve varsayılan işlem düğümü bağlantısı ' nı seçin.

    ![Varsayılan işlem düğümünü seçin](./media/connect-metamask/transaction-nodes.png)

1. **Erişim anahtarlarına > bağlantı dizeleri** seçin.
1. Endpoint adresini **https 'den (erişim anahtarı 1)** kopyalayın.

    ![Bağlantı dizesi](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>MetaMask bağlama

1. MetaMask Browser uzantısını açın ve oturum açın.
1. Ağ açılan menüsünde  **özel RPC**' yi seçin.

    ![Özel RPC](./media/connect-metamask/custom-rpc.png)

1. Yeni **ağ > yenı RPC URL 'si**' nde, yukarıya kopyaladığınız uç nokta adresini yapıştırın.
1. **Kaydet**’i seçin.

    Bağlantı başarılı olduysa, özel ağ, ağ açılır penceresinde görüntülenir.

    ![Yeni ağ](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Akıllı sözleşme dağıtma

Yeniden karıştırma, tarayıcı tabanlı bir Solidity geliştirme ortamıdır. MetaMask ve yeniden karıştır kullanarak akıllı sözleşmeleri dağıtabilir ve bunlar üzerinde işlem yapabilirsiniz.

1. Tarayıcınızda `https://remix.ethereum.org` adresine gidin.
1. **Dosya** altındaki **giriş** sekmesinde **yeni dosya** ' yı seçin.

    Yeni dosyayı adlandırın `simple.sol` .

    ![Dosya oluştur](./media/connect-metamask/create-file.png)

    **Tamam**’ı seçin.
1. Yeniden karıştır düzenleyicisinde, aşağıdaki **basit akıllı sözleşme** kodunu yapıştırın.

    ```solidity
    pragma solidity ^0.5.0;
             
    contract simple {
        uint balance;
                 
        constructor() public{
            balance = 0;
        }
                 
        function add(uint _num) public {
            balance += _num;
        }
                 
        function get() public view returns (uint){
            return balance;
        }
    }
    ```

    **Basit sözleşme** , **Bakiye** adlı bir durum değişkeni bildirir. Tanımlanmış iki işlev vardır. **Add** işlevi **dengelemek** için bir sayı ekler. **Get** işlevi, **Bakiyenin** değerini döndürür.
1. Sözleşmeyi derlemek için önce Solidity derleyici bölmesini seçin, sonra  **Simple. Nuevo derlemesini** seçin.

    ![Ekran yakalama, derlenen bir sözleşmeyi gösterir.](./media/connect-metamask/compile.png)

1. **Dağıtım & Çalıştır** bölmesini seçin ve ardından **ortamı** , MetaMask ile blok zinciri üyesine bağlamak üzere **eklenmiş Web3** olarak ayarlayın.

    ![Çalışma sekmesi](./media/connect-metamask/injected-web3.png)

1. **Basit** sözleşmeyi seçin ve ardından **dağıtın**.

    ![Ekran yakalama, seçili bir sözleşmeye sahip ve seçili bir sözleşmeyle birlikte dağıtım ve çalıştırma işlemlerini gösterir.](./media/connect-metamask/deploy.png)


1. Bir MetaMask bildirimi, işlemi gerçekleştirmek için yeterli fon sayısını uyarır.

    Ortak bir blok zinciri ağı için, işlem maliyeti için ödeme yapmanız gerekir. Bu bir konsorsiyumde özel bir ağ olduğundan, gaz fiyatını sıfır olarak ayarlayabilirsiniz.

1.  **Gaz ücretini seçin > gelişmiş > düzenleyin**, **gaz fiyatını** 0 olarak ayarlayın.

    ![Gaz fiyatı](./media/connect-metamask/gas-price.png)

    **Kaydet**’i seçin.

1. Akıllı sözleşmeyi blok zincirine dağıtmak için **Onayla** ' yı seçin.
1. **Dağıtılan sözleşmeler** bölümünde, **basit** sözleşmeyi genişletin.

    ![Dağıtılan sözleşme](./media/connect-metamask/deployed-contract.png)

    İki eylem **ekleyin** ve **Al**, sözleşmede tanımlanan işlevlere eşleyin.

1. Blok zincirinde bir işlem **ekleme** işlemi gerçekleştirmek için eklemek üzere bir sayı girin ve **Ekle**' yi seçin. Remix 'ten bir gaz tahmin hatası iletisi alabilirsiniz: "işlemi, gaz gerektirmeyen bir özel blok zincirine gönderiyorsunuz." İşlemi zorlamak için **Işlem gönder** ' i seçin.
1. Sözleşmeyi dağıtırken olduğu gibi, bir MetaMask bildirimi, işlemi gerçekleştirmek için yeterli fon sayısını uyarır.

    Bu bir konsorsiyumun özel bir ağı olduğundan, gaz fiyatını sıfıra ayarlayabiliriz.

1. **Gaz ücretini seçin > gelişmiş > düzenleyin**, **gaz fiyatını** 0 olarak ayarlayın ve **Kaydet**' i seçin.
1. İşlemi blok zincirine göndermek için **Onayla** ' yı seçin.
1. Eylem **Al** ' ı seçin. Bu, düğüm verilerini sorgulamak için bir çağrıdır. Bir işlem gerekli değildir.

Remix 'in hata ayıklama bölmesi, blok zincirindeki işlemler hakkındaki ayrıntıları gösterir:

![Hata ayıklama geçmişi](./media/connect-metamask/debug.png)

**Basit sözleşme oluşturma** , **basit. Ekle** için işlem ve **basit. Get** öğesine çağrı yapabilirsiniz.

MetaMask içinde işlem geçmişini görmek için MetaMask Browser uzantısını açın ve dağıtılan sözleşmenin ve işlemlerin günlüğü için **Geçmiş** bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir Azure blok zinciri hizmeti işlem düğümüne bağlanmak, akıllı bir sözleşme dağıtmak ve blok zincirine bir işlem göndermek için MetaMask Browser uzantısını kullandınız. Bir işlem aracılığıyla akıllı sözleşme işlevi oluşturmak, derlemek, dağıtmak ve yürütmek için Ethereum ve truffle için Azure blok zinciri geliştirme setini kullanmak üzere bir sonraki öğreticiyi deneyin.

> [!div class="nextstepaction"]
> [Azure blok zinciri hizmetinde akıllı sözleşmeler oluşturma, derleme ve dağıtma](send-transaction.md)