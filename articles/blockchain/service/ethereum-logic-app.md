---
title: Azure Logic Apps ile Ethereum blok zinciri bağlayıcısını kullanın
description: Akıllı sözleşme işlevlerini tetiklemek ve akıllı sözleşme olaylarına yanıt vermek için Azure Logic Apps ile Ethereum blok zinciri bağlayıcısını kullanın.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: bb23d6b9b42e1c51646765255870a14a1b5d39f7
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579934"
---
# <a name="use-the-ethereum-blockchain-connector-with-azure-logic-apps"></a>Azure Logic Apps ile Ethereum blok zinciri bağlayıcısını kullanın

Akıllı sözleşme eylemleri gerçekleştirmek ve akıllı sözleşme olaylarına yanıt vermek için [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) Ile [Ethereum blok zinciri bağlayıcısını](https://docs.microsoft.com/connectors/blockchainethereum/) kullanın. Örneğin, bir blok zinciri muhasebenden bilgi döndüren bir REST tabanlı mikro hizmet oluşturmak istediğinizi varsayalım. Bir mantıksal uygulama kullanarak, blok zinciri defterinde depolanan bilgileri sorgulayan HTTP isteklerini kabul edebilirsiniz.

## <a name="prerequisites"></a>Ön koşullar

İsteğe bağlı önkoşul hızlı başlangıcını doldurun [: Azure blok zinciri hizmeti Consortium ağına bağlanmak için Visual Studio Code kullanın](connect-vscode.md). Hızlı başlangıç, [Ethereum Için Azure blok zinciri geliştirme seti 'ni](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) yükleyip blok zinciri geliştirme ortamınızı ayarlamayı gösterir.

## <a name="create-a-logic-app"></a>Mantıksal uygulama oluşturma

Azure Logic Apps, sistemleri ve Hizmetleri tümleştirmeniz gerektiğinde iş süreçlerini ve iş akışlarını zamanlamanıza ve otomatikleştirmenize yardımcı olur. İlk olarak, Ethereum blok zinciri bağlayıcısını kullanan bir mantık oluşturursunuz.

1. [Azure portalında](https://portal.azure.com) **Kaynak oluştur** > **Tümleştirme** > **Mantıksal Uygulama**’yı seçin.
1. **Mantıksal uygulama oluştur**altında, mantıksal uygulamanızı nerede oluşturacağınız hakkında ayrıntılı bilgi sağlayın. İşiniz bittiğinde **Oluştur**' u seçin.

    Mantıksal uygulamalar oluşturma hakkında daha fazla bilgi için bkz. [Azure Logic Apps otomatik iş akışları oluşturma](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Azure uygulamanızı dağıtduktan sonra mantıksal uygulama kaynağınızı seçin.
1. Logic Apps tasarımcısında **Şablonlar**altında **boş mantıksal uygulama**' yı seçin.

Her mantıksal uygulama, belirli bir olay gerçekleştiğinde ya da belirli bir koşul karşılandığında tetiklenen bir tetikleyiciyle başlamalıdır. Tetikleyici her etkinleştirildiğinde Logic Apps altyapısı iş akışınızı başlatan ve çalıştıran bir mantıksal uygulama örneği oluşturur.

Ethereum blok zinciri bağlayıcısının bir tetikleyicisi ve birkaç eylemi vardır. Kullandığınız tetikleyici veya eylem, senaryonuza bağlıdır.

İş akışınız:

* Blok zincirinde bir olay gerçekleştiğinde tetikler, [olay tetikleyicisini kullanın](#use-the-event-trigger).
* Akıllı bir sözleşmeyi sorgular veya dağıtır, [eylemleri kullanın](#use-actions).
* Ortak bir senaryoyu takip [eden, geliştirici setini kullanarak iş akışı oluşturma](#generate-a-workflow).

## <a name="use-the-event-trigger"></a>Olay tetikleyicisini kullanma

Akıllı bir anlaşma olayı oluştuktan sonra bir mantıksal uygulamanın çalışmasını istediğinizde Ethereum blok zinciri olay tetikleyicilerini kullanın. Örneğin, bir akıllı anlaşma işlevi çağrıldığında bir e-posta göndermek istersiniz.

1. Logic Apps tasarımcısında, Ethereum blok zinciri bağlayıcısını seçin.
1. **Tetikleyiciler** sekmesinden **bir akıllı anlaşma olayının ne zaman gerçekleşeceğini**seçin.
1. Azure blok zinciri hizmeti ile [BIR API bağlantısı](#create-an-api-connection) değiştirin veya oluşturun.
1. Olayları denetlemek istediğiniz akıllı sözleşmeyle ilgili ayrıntıları girin.

    ![Olay tetikleyicisi özellikleriyle Logic Apps tasarımcı](./media/ethereum-logic-app/event-properties.png)

    | Özellik | Açıklama |
    |----------|-------------|
    | **Sözleşme ABı** | Sözleşme uygulaması ikili arabirimi (ABı) akıllı sözleşme arabirimlerini tanımlar. Daha fazla bilgi için bkz. [sözleşmeyi edınme ABI](#get-the-contract-abi). |
    | **Akıllı sözleşme adresi** | Sözleşme adresi, Ethereum blok zincirindeki akıllı anlaşma hedef adresidir. Daha fazla bilgi için bkz. [sözleşme adresini alın](#get-the-contract-address). |
    | **Olay adı** | Denetlenecek akıllı sözleşme olayını seçin. Olay mantıksal uygulamayı tetikler. |
    | **Aralık** ve **Sıklık** | Olayı ne sıklıkta denetlemek istediğinizi seçin. |

1. **Kaydet**’i seçin.

Mantıksal uygulamanızı tamamlayabilmeniz için Ethereum blok zinciri olay tetikleyicisine dayalı bir eylem gerçekleştiren yeni bir adım ekleyebilirsiniz. Örneğin, bir e-posta gönderin.

## <a name="use-actions"></a>Kullanım eylemleri

Mantıksal uygulamanın blok zinciri defterinde bir eylem gerçekleştirmesini istediğinizde Ethereum blok zinciri eylemlerini kullanın. Örneğin, bir mantıksal uygulamaya HTTP isteği yapıldığında bir akıllı anlaşma işlevi çağıran bir REST tabanlı mikro hizmet oluşturmak istersiniz.

Bağlayıcı eylemleri bir tetikleyici gerektirir. Bir mikro hizmet için HTTP istek tetikleyicisi gibi bir tetikleyiciden sonra bir sonraki adımla bir Ethereum blok zinciri bağlayıcı eylemini kullanabilirsiniz.

1. Logic Apps tasarımcısında, bir tetikleyiciyi izleyen **yeni adım** ' ı seçin.
1. Ethereum blok zinciri bağlayıcısını seçin.
1. **Eylemler** sekmesinde, kullanılabilir eylemlerden birini seçin.

    ![Eylem özellikleriyle Logic Apps tasarımcı](./media/ethereum-logic-app/action-properties.png)

1. Azure blok zinciri hizmeti ile [BIR API bağlantısı](#create-an-api-connection) değiştirin veya oluşturun.
1. Seçtiğiniz eyleme bağlı olarak, akıllı sözleşme işleviniz hakkında aşağıdaki ayrıntıları sağlayın.

    | Özellik | Açıklama |
    |----------|-------------|
    | **Sözleşme ABı** | Sözleşme ABı, akıllı sözleşme arabirimlerini tanımlar. Daha fazla bilgi için bkz. [sözleşmeyi edınme ABI](#get-the-contract-abi). |
    | **Sözleşme bytecode 'u** | Derlenen akıllı sözleşme bayt kodu. Daha fazla bilgi için bkz. [sözleşme bytecode bilgilerini edinme](#get-the-contract-bytecode). |
    | **Akıllı sözleşme adresi** | Sözleşme adresi, Ethereum blok zincirindeki akıllı anlaşma hedef adresidir. Daha fazla bilgi için bkz. [sözleşme adresini alın](#get-the-contract-address). |
    | **Akıllı sözleşme işlev adı** | Eylem için akıllı sözleşme işlev adını seçin. Liste, sözleşme ABı ayrıntılarından doldurulur. |

    Akıllı sözleşme işlev adını seçtikten sonra, işlev parametreleri için gerekli alanları görebilirsiniz. Senaryonuz için gereken değerleri veya dinamik içeriği girin.

Artık mantıksal uygulamanızı kullanabilirsiniz. Mantıksal uygulama olayı tetiklendiğinde, Ethereum blok zinciri eylemi çalışır. Örneğin, bir HTTP isteği tetikleyicisi akıllı bir sözleşmenin durum değerini sorgulamak için bir Ethereum blok zinciri eylemi çalıştırır. Bu sorgu, değeri döndüren bir HTTP yanıtına neden olur.

## <a name="generate-a-workflow"></a>İş akışı oluşturma

Ethereum Visual Studio Code uzantısı için Azure blok zinciri geliştirme seti, yaygın senaryolar için mantıksal uygulama iş akışları oluşturabilir. Dört senaryo mevcuttur:

* Azure SQL veritabanı örneğine veri yayımlama
* Azure Event Grid veya Azure Service Bus bir örneğine olay yayımlama
* Rapor yayımlama
* REST tabanlı mikro hizmet

 Azure blok zinciri geliştirme seti, blok zinciri geliştirmeyi basitleştirmek için Truffle kullanır. Akıllı sözleşmeye dayalı bir mantıksal uygulama oluşturmak için akıllı sözleşme için bir truffle çözümüne ihtiyacınız vardır. Ayrıca Azure blok zinciri hizmeti Consortium ağınıza da bir bağlantı gerekir. Daha fazla bilgi için bkz. [Visual Studio Code kullanarak bir Azure blok zinciri hizmeti konsorsiyum ağı hızlı başlangıç bağlantısı](connect-vscode.md).

Örneğin, aşağıdaki adımlar hızlı başlangıç **Helloblockzincirine** akıllı sözleşmeye DAYALı bir REST tabanlı mikro hizmet mantıksal uygulaması oluşturur:

1. Visual Studio Code Explorer kenar çubuğunda çözümünüzdeki **sözleşmeler** klasörünü genişletin.
1. **Helloblockzincirine. Nuevo** öğesine sağ tıklayın ve menüden **akıllı sözleşmeler Için mikro hizmetler oluştur** ' u seçin.

    ![Akıllı sözleşmeler için mikro hizmetler oluşturma seçimine sahip Visual Studio Code bölmesi](./media/ethereum-logic-app/generate-logic-app.png)

1. Komut paletinde **Logic App**' i seçin.
1. **Sözleşme adresini**girin. Daha fazla bilgi için bkz. [sözleşme adresini alın](#get-the-contract-address).
1. Mantıksal uygulama için Azure aboneliğini ve kaynak grubunu seçin.

    Mantıksal uygulama yapılandırması ve kod dosyaları **generatedLogicApp** dizininde oluşturulur.

1. **GeneratedLogicApp/Helloblockzincirleri** dizinini görüntüleyin. Her akıllı sözleşme işlevi, olayı ve özelliği için bir mantıksal uygulama JSON dosyası vardır.
1. **GeneratedLogicApp/Helloblockzincirleri/Service/Property ' i açın. RequestMessage. logicapp. JSON** dosyası ve içeriğini kopyalayın.

    ![Kopyalanacak kodu içeren JSON dosyası](./media/ethereum-logic-app/requestmessage.png)

1. Mantıksal uygulamanızda **mantıksal uygulama kod görünümü**' nü seçin. Var olan JSON 'ı oluşturulan mantıksal uygulama JSON ile değiştirin.

    ![Yeni değiştirilmiş uygulama kodu ile mantıksal uygulama kod görünümü](./media/ethereum-logic-app/code-view.png)

1. Tasarımcı görünümüne geçiş yapmak için **Tasarımcı** ' yı seçin.
1. Mantıksal uygulama, senaryoya yönelik temel adımları içerir. Ancak, Ethereum blok zinciri bağlayıcısının yapılandırma ayrıntılarını güncelleştirmeniz gerekir.
1. **Bağlantılar** adımını seçin ve Azure blok zinciri hizmeti Ile [bir API bağlantısı oluşturun](#create-an-api-connection) veya değiştirin.

    ![Bağlantı seçimiyle Tasarımcı görünümü](./media/ethereum-logic-app/microservice-logic-app.png)

1. Artık mantıksal uygulamanızı kullanabilirsiniz. REST tabanlı mikro hizmeti test etmek için mantıksal uygulama isteği URL 'sine bir HTTP POST isteği verin. Http **isteği alındığında** , ÖĞESINDEN **http post URL 'si** içeriğini kopyalayın.

    ![HTTP POST URL 'SI ile Logic Apps tasarımcı bölmesi](./media/ethereum-logic-app/post-url.png)

1. Bir HTTP POST isteği oluşturmak için kıvrımlı kullanın. \<yer tutucu metnini, *http post URL 'sini\>* ÖNCEKI adımdaki URL ile değiştirin.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    Kıvrımlı komutu mantıksal uygulamadan bir yanıt döndürür. Bu durumda, yanıt **RequestMessage** akıllı sözleşme işlevinin çıktıdır.

    ![RequestMessage akıllı sözleşme işlevinin kod çıktısı](./media/ethereum-logic-app/curl.png)

Geliştirme setini kullanma hakkında daha fazla bilgi için bkz. [Ethereum wiki Için Azure blok zinciri geliştirme seti sayfası](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki).

## <a name="create-an-api-connection"></a>API bağlantısı oluşturma

Ethereum blok zinciri Bağlayıcısı için bir blok zincirine yönelik bir API bağlantısı gereklidir. Birden çok Logic Apps için API bağlayıcısını kullanabilirsiniz. Bazı özellikler gereklidir ve diğerleri senaryonuza bağlıdır.

> [!IMPORTANT]
> Bir blok zincirinde işlem oluşturmak için bir özel anahtar veya hesap adresi ve parola gereklidir. Yalnızca bir kimlik doğrulama biçimi gereklidir. Hem özel anahtar hem de hesap ayrıntılarını sağlamanız gerekmez. Sözleşmelerin sorgulanması bir işlem gerektirmez. Sözleşme durumunu sorgulayan eylemler kullanıyorsanız, özel anahtar veya hesap adresi ve parola gerekli değildir.

Azure blok zinciri hizmeti üyesine bağlantı ayarlamanıza yardımcı olması için aşağıdaki listede, senaryonuza bağlı olarak gereken özellikler vardır.

| Özellik | Açıklama |
|----------|-------------|
|**Bağlantı adı** | API bağlantısının adı. Gereklidir. |
|**Ethereum RPC uç noktası** | Azure blok zinciri hizmeti işlem düğümünün HTTP adresi. Gereklidir. Daha fazla bilgi için bkz. [RPC uç noktasını edinme](#get-the-rpc-endpoint). |
|**Özel anahtar** | Ethereum hesabı özel anahtarı. İşlemler için özel anahtar veya hesap adresi ve parola gereklidir. Daha fazla bilgi için bkz. [özel anahtarı edinme](#get-the-private-key). |
|**Hesap adresi** | Azure blok zinciri hizmeti üyesi hesap adresi. İşlemler için özel anahtar veya hesap adresi ve parola gereklidir. Daha fazla bilgi için bkz. [Hesap adresini alın](#get-the-account-address). |
|**Hesap parolası** | Üye oluşturduğunuzda hesap parolası ayarlanır. Parolayı sıfırlama hakkında daha fazla bilgi için bkz. [Ethereum hesabı](consortium.md#ethereum-account).|

## <a name="get-the-rpc-endpoint"></a>RPC uç noktasını alın

Blok zinciri ağına bağlanmak için Azure blok zinciri hizmeti RPC uç noktası adresi gereklidir. Ethereum veya Azure portal için Azure blok zinciri geliştirme setini kullanarak uç nokta adresini alabilirsiniz.

**Geliştirme setini kullanmak için:**

1. Visual Studio Code 'de **Azure blok zinciri hizmeti** altında, konsorsiya sağ tıklayın.
1. **RPC uç nokta adresini kopyala**' yı seçin.

    ![RPC uç nokta adres seçimi ile konsorsiyumun gösterildiği Visual Studio Code bölmesi](./media/ethereum-logic-app/devkit-rpc.png)

    RPC uç noktası panonuza kopyalanır.

**Azure portal kullanmak için:**

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Azure blok zinciri hizmeti üyesine gidin. **İşlem düğümleri** ve varsayılan işlem düğümü bağlantısı ' nı seçin.

    ![İşlem düğümleri sayfası (varsayılan düğüm) seçimi](./media/ethereum-logic-app/transaction-nodes.png)

1. **Erişim anahtarlarına** > **bağlantı dizeleri** seçin.
1. Endpoint adresini **https (erişim anahtarı 1)** veya **https (erişim anahtarı 2)** konumundan kopyalayın.

    ![Bağlantı dizesi erişim anahtarlarıyla Azure portal](./media/ethereum-logic-app/connection-string.png)

    RPC uç noktası, Azure blok zinciri hizmeti üye işlem düğümünüz için adres ve erişim anahtarını içeren HTTPS URL 'sidir.

## <a name="get-the-private-key"></a>Özel anahtarı al

Blok zincirine bir işlem gönderirken kimlik doğrulaması yapmak için Ethereum hesabının özel anahtarını kullanabilirsiniz. Ethereum hesabınızın ortak ve özel anahtarları 12 sözcüklü bir anımsatıcı tarafından oluşturulmuştur. Ethereum için Azure blok zinciri geliştirme seti, bir Azure blok zinciri hizmeti Consortium üyesine bağlandığınızda bir anımsatıcı oluşturur. Endpoint adresini geliştirme seti uzantısını kullanarak alabilirsiniz.

1. Visual Studio Code ' de, komut paleti ' ni (F1) açın.
1. **Azure blok zincirini seçin: özel anahtar al**.
1. Consortium üyesine bağlanırken kaydettiğiniz anımsatıcı ' u seçin.

    ![Anımsatıcı seçme seçeneği içeren komut paleti](./media/ethereum-logic-app/private-key.png)

    Özel anahtar panonuza kopyalanır.

## <a name="get-the-account-address"></a>Hesap adresini al

Blok zincirine bir işlem gönderdiğinizde kimlik doğrulamak için üye hesabını ve parolayı kullanabilirsiniz. Üye oluşturduğunuzda parola ayarlanır.

1. Azure portal Azure blok zinciri hizmetine genel bakış sayfasına gidin.
1. **Üye hesabı** adresini kopyalayın.

    ![Üye hesap adresiyle genel bakış sayfası](./media/ethereum-logic-app/member-account.png)

Hesap adresi ve parola hakkında daha fazla bilgi için bkz. [Ethereum hesabı](consortium.md#ethereum-account).

## <a name="get-the-contract-abi"></a>Sözleşmeyi al ABı

Sözleşme ABı, akıllı sözleşme arabirimlerini tanımlar. Akıllı sözleşmeyle nasıl etkileşim kuracağınızı açıklar. Ethereum için Azure blok zinciri geliştirme setini kullanarak sözleşme ABı edinebilirsiniz. Ayrıca, Solidity derleyicisi tarafından oluşturulan sözleşme meta verileri dosyasından de alabilirsiniz.

**Geliştirme setini kullanmak için:**

Akıllı sözleşmenizi derlemek için geliştirme setini veya Truffle kullandıysanız, ABı sözleşmesini panoya kopyalamak için uzantısını kullanabilirsiniz.

1. Visual Studio Code gezgin bölmesinde, Solidity projenizin **Build/Contracts** klasörünü genişletin.
1. Sözleşme meta verileri JSON dosyasına sağ tıklayın. Dosya adı akıllı sözleşme adı ve ardından **. JSON** uzantısıdır.
1. **Sözleşme ABI Kopyala**' yı seçin.

    ![Sözleşmeyi Kopyala ABı seçimiyle Visual Studio Code bölmesi](./media/ethereum-logic-app/abi-devkit.png)

    Sözleşme ABı, panoya kopyalanır.

**Sözleşme meta verileri dosyasını kullanmak için:**

1. Solidity projenizin **Build/Contracts** klasöründe yer alan Sözleşme meta veri dosyasını açın. Dosya adı akıllı sözleşme adı ve ardından **. JSON** uzantısıdır.
1. JSON dosyasında **ABI** bölümünü bulun.
1. **ABI** JSON dizisini kopyalayın.

    ![ABı kodu sözleşme meta veri dosyasında](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-the-contract-bytecode"></a>Sözleşme bayt kodunu al

Sözleşme bayt kodu, Ethereum sanal makinesi tarafından yürütülen derlenmiş akıllı sözleşmedir. Ethereum için Azure blok zinciri geliştirme setini kullanarak sözleşme bytecode ' ü edinebilirsiniz. Ayrıca, Solidity derleyicisinden da alabilirsiniz.

**Geliştirme setini kullanmak için:**

Akıllı sözleşmenizi derlemek için geliştirme seti 'ni veya Truffle 'yı kullandıysanız, uzantıyı Pano bytecode 'suna kopyalamak için kullanabilirsiniz.

1. Visual Studio Code gezgin bölmesinde, Solidity projenizin **Build/Contracts** klasörünü genişletin.
1. Sözleşme meta verileri JSON dosyasına sağ tıklayın. Dosya adı akıllı sözleşme adı ve ardından **. JSON** uzantısıdır.
1. **Sözleşme bayt kodunu kopyala**' yı seçin.

    ![Sözleşmeyi Kopyala bytecode seçimine sahip Visual Studio Code bölmesi](./media/ethereum-logic-app/bytecode-devkit.png)

    Sözleşme bayt kodu panoya kopyalanır.

**Sözleşme meta verileri dosyasını kullanmak için:**

1. Solidity projenizin **Build/Contracts** klasöründe yer alan Sözleşme meta veri dosyasını açın. Dosya adı akıllı sözleşme adı ve ardından **. JSON** uzantısıdır.
1. JSON dosyasında **bytecode** öğesini bulun.
1. **Bytecode** değerini kopyalayın.

    ![Meta verilerde bytecode ile Visual Studio Code bölmesi](./media/ethereum-logic-app/bytecode-metadata.png)

**Solidity derleyicisini kullanmak için:**

Sözleşme bytecode 'u oluşturmak için komut `solc --bin <smart contract>.sol` kullanın.

## <a name="get-the-contract-address"></a>Sözleşme adresini al

Sözleşme adresi, Ethereum blok zincirindeki akıllı anlaşma hedef adresidir. Bu adresi, bir akıllı sözleşmenin işlemini veya sorgu durumunu göndermek için kullanırsınız. Sözleşme adresini Truffle geçiş çıktısından veya anlaşma meta veri dosyasından alabilirsiniz.

**Truffle geçişi çıkışını kullanmak için:**

Truffle, akıllı sözleşmenin dağıtımından sonra sözleşme adresini görüntüler. **Sözleşme adresini** çıktıdan kopyalayın.

![Visual Studio Code içindeki sözleşme adresiyle Truffle geçiş çıkışı](./media/ethereum-logic-app/contract-address-truffle.png)

**Sözleşme meta verileri dosyasını kullanmak için:**

1. Solidity projenizin **Build/Contracts** klasöründe yer alan Sözleşme meta veri dosyasını açın. Dosya adı akıllı sözleşme adı ve ardından **. JSON** uzantısıdır.
1. JSON dosyasındaki **ağlar** bölümünü bulun.
1. Özel ağlar bir tamsayı ağ KIMLIĞI ile tanımlanır. Ağ bölümünün içinde adres değerini bulun.
1. **Adres** değerini kopyalayın.

![Visual Studio Code içinde adres değeri olan meta veriler](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>Sonraki adımlar

[Logic Apps daha fazlasını yaparak](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true)videodaki yaygın senaryoları izleyin.
