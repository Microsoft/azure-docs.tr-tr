---
title: Veri Yöneticisi Azure Cosmos DB güncelleştirmek için Blockzincirini kullanın
description: Blok zinciri verilerini Azure Cosmos DB göndermek için Blockzincirini Veri Yöneticisi kullanın
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chroyal
ms.openlocfilehash: 3f2d0df2c094d8455aa29e79ad3c6acc0aa52dd4
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73586484"
---
# <a name="tutorial-use-blockchain-data-manager-to-send-data-to-azure-cosmos-db"></a>Öğretici: Azure Cosmos DB 'a veri göndermek için blok zinciri Veri Yöneticisi kullanma

Bu öğreticide, blok zinciri işlem verilerini Azure Cosmos DB kaydetmek üzere Azure blok zinciri hizmeti için blok zinciri Veri Yöneticisi kullanırsınız. Blok Veri Yöneticisi zinciri, blok zinciri defter verilerini Azure Event Grid konularına yakalar, dönüştürür ve sunar. Azure Event Grid, bir Azure Cosmos DB veritabanında belge oluşturmak için Azure Logic App bağlayıcısını kullanırsınız. Öğretici ile işiniz bittiğinde, Azure Cosmos DB Veri Gezgini blok zinciri işlem verilerini keşfedebilirsiniz.

[![blok zinciri işlem ayrıntısı](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Bir blok zinciri Veri Yöneticisi örneği oluşturma
> * İşlem özelliklerini ve olayları çözmek için bir blok zinciri uygulaması ekleme
> * İşlem verilerini depolamak için bir Azure Cosmos DB hesabı ve veritabanı oluşturma
> * Azure Cosmos DB Azure Event Grid bir konu bağlamak için bir Azure Logic App oluşturun
> * Bir blok zinciri defterine işlem gönderme
> * Kodu çözülen işlem verilerini Azure Cosmos DB görüntüle

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Ön koşullar

* [Hızlı başlangıç: Azure Portal veya hızlı başlangıç kullanarak bir blok zinciri üyesi oluşturma](create-member.md) [: Azure CLI kullanarak bir Azure blok zinciri hizmeti blok zinciri üyesi](create-member-cli.md) oluşturma
* [Hızlı başlangıç: Azure blok zinciri hizmeti Consortium ağına bağlanmak için Visual Studio Code kullanın](connect-vscode.md). Hızlı başlangıç, [Ethereum Için Azure blok zinciri geliştirme seti 'ni](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) yükleyip blok zinciri geliştirme ortamınızı ayarlamayı gösterir.
* Tüm [öğretici: akıllı sözleşmeleri oluşturmak, derlemek ve dağıtmak için Visual Studio Code kullanın](send-transaction.md). Öğreticide, örnek bir akıllı sözleşme oluşturma işlemi adım adım açıklanmaktadır.
* [Event Grid konu başlığı](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) oluşturma
* [Azure Event Grid Içindeki olay işleyicileri](../../event-grid/event-handlers.md) hakkında bilgi edinin

## <a name="create-instance"></a>Örnek Oluştur

Bir blok zinciri Veri Yöneticisi örneği, bir Azure blok zinciri hizmeti işlem düğümünü bağlar ve izler. Örnek, işlem düğümünden tüm ham blok ve ham işlem verilerini yakalar. Giden bağlantı, blok zinciri verilerini Azure Event Grid gönderir. Örneği oluştururken tek bir giden bağlantı yapılandırırsınız.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Önkoşul hızlı başlangıcı ' nda oluşturduğunuz Azure blok zinciri hizmeti üyesine gidin [: Azure Portal kullanarak bir blok zinciri üyesi oluşturma](create-member.md). **Blok zinciri veri Yöneticisi**seçin.
1. **Add (Ekle)** seçeneğini belirleyin.

    ![Blok zinciri ekleme Veri Yöneticisi](./media/data-manager-cosmosdb/add-instance.png)

    Şu ayrıntıları girin:

    Ayar | Örnek | Açıklama
    --------|---------|------------
    Ad | myizleyici | Bağlı bir blok zinciri Veri Yöneticisi için benzersiz bir ad girin.
    İşlem düğümü | myblockchainmember | Ön koşul içinde oluşturduğunuz Azure blok zinciri hizmeti üyesinin varsayılan işlem düğümünü seçin.
    Bağlantı adı | Cosmosdb | Blok zinciri işlem verilerinin gönderildiği giden bağlantıya ilişkin benzersiz bir ad girin.
    Olay Kılavuzu uç noktası | myTopic | Önkoşul bölümünde oluşturduğunuz bir olay Kılavuzu konusu seçin. Note: blok zinciri Veri Yöneticisi örneği ve olay Kılavuzu konusu aynı abonelikte olmalıdır.

1. **Tamam**’ı seçin.

    Bir blok zinciri Veri Yöneticisi örneği oluşturmak bir dakikadan kısa sürer. Örnek dağıtıldıktan sonra otomatik olarak başlatılır. Çalışan bir blok zinciri Veri Yöneticisi örneği, işlem düğümündeki blok zinciri olaylarını yakalar ve olay kılavuzuna veri gönderir.

## <a name="add-application"></a>Uygulama ekleme

Engelleme zincirinin olay ve özellik durumunun kodunu çözen Veri Yöneticisi için **helloblockzincirle** blok zinciri uygulamasını ekleyin. Blok zinciri Veri Yöneticisi, uygulamayı eklemek için akıllı sözleşme ABı ve bytecode dosyası gerektirir.

### <a name="get-contract-abi-and-bytecode"></a>Sözleşme ABı ve bytecode 'u al

Sözleşme ABı, akıllı sözleşme arabirimlerini tanımlar. Akıllı sözleşmeyle nasıl etkileşim kuracağınızı açıklar. Contract ABı 'ı panoya kopyalamak için [Ethereum uzantısı Için Azure blok zinciri geliştirme seti](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) ' ni kullanabilirsiniz.

1. Visual Studio Code gezgin bölmesinde, önkoşul öğreticisinde oluşturduğunuz **helloblockzincirleri** Solidity projesinin **Build/Contracts** klasörünü genişletin [: akıllı sözleşmeleri oluşturmak, derlemek ve dağıtmak için Visual Studio Code kullanın](send-transaction.md).
1. Sözleşme meta verileri JSON dosyasına sağ tıklayın. Dosya adı akıllı sözleşme adı ve ardından **. JSON** uzantısıdır.
1. **Sözleşme ABI Kopyala**' yı seçin.

    ![Sözleşmeyi Kopyala ABı seçimiyle Visual Studio Code bölmesi](./media/data-manager-cosmosdb/abi-devkit.png)

    Sözleşme ABı, panoya kopyalanır.

1. **ABI** DIZISINI bir JSON dosyası olarak kaydedin. Örneğin, *ABI. JSON*. Dosyayı sonraki bir adımda kullanırsınız.

Blok zinciri Veri Yöneticisi, akıllı sözleşme için dağıtılan bayt kodunu gerektirir. Dağıtılan bytecode, akıllı sözleşme bytecode değerinden farklı. Derlenen anlaşma meta veri dosyasından dağıtılan bayt kodunu alabilirsiniz.

1. Solidity projenizin **Build/Contracts** klasöründe yer alan Sözleşme meta veri dosyasını açın. Dosya adı akıllı sözleşme adı ve ardından **. JSON** uzantısıdır.
1. JSON dosyasında **Deployedbytecode** öğesini bulun.
1. Tırnak işaretleri olmadan onaltılık değeri kopyalayın.

    ![Meta verilerde bytecode ile Visual Studio Code bölmesi](./media/data-manager-portal/bytecode-metadata.png)

1. **Bytecode** DEĞERINI bir JSON dosyası olarak kaydedin. Örneğin, *bytecode. JSON*. Dosyayı sonraki bir adımda kullanırsınız.

Aşağıdaki örnek, VS Code düzenleyicide açık olan *ABI. JSON* ve *bytecode. JSON* dosyalarını gösterir. Dosyalarınız benzer görünmelidir.

![ABI. JSON ve bytecode. JSON dosyaları örneği](./media/data-manager-cosmosdb/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Sözleşme ABı ve bytecode URL 'SI oluştur

Blok zinciri Veri Yöneticisi, bir uygulama eklenirken sözleşme ABı ve bytecode dosyalarına bir URL tarafından erişilebilmesini gerektirir. Bir Azure depolama hesabı kullanarak özel olarak erişilebilen bir URL sağlayabilirsiniz.

#### <a name="create-storage-account"></a>Depolama hesabı oluştur

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Sözleşme dosyalarını karşıya yükle

1. Depolama hesabı için yeni bir kapsayıcı oluşturun. **Kapsayıcı > kapsayıcıları**seçin.

    ![Depolama hesabı kapsayıcısı oluşturma](./media/data-manager-cosmosdb/create-container.png)

    | Ayar | Açıklama |
    |---------|-------------|
    | Ad  | Kapsayıcıyı adlandırın. Örneğin, *smartcontract* |
    | Genel erişim düzeyi | *Özel (anonim erişim yok)* seçeneğini belirleyin |

1. Kapsayıcıyı oluşturmak için **Tamam**'ı seçin.
1. Kapsayıcıyı seçin ve ardından **karşıya yükle**' yi seçin.
1. [Sözleşmeyi al ABI ve bytecode](#get-contract-abi-and-bytecode) bölümünde oluşturduğunuz JSON dosyalarını seçin.

    ![Blobu karşıya yükle](./media/data-manager-cosmosdb/upload-blobs.png)

    **Karşıya Yükle**’yi seçin.

#### <a name="generate-url"></a>URL Oluştur

Her blob için, paylaşılan erişim imzası oluşturun.

1. ABı JSON blob 'unu seçin.
1. **SAS oluştur** ' u seçin
1. İstenen erişim imzası süre sonunu ayarlayın **ve ardından blob SAS belirteci oluştur ve URL 'yi**seçin.

    ![SAS belirteci oluştur](./media/data-manager-cosmosdb/generate-sas.png)

1. **BLOB SAS URL 'sini** kopyalayın ve sonraki bölüm için kaydedin.
1. Bytecode JSON blobu için [URL Oluştur](#generate-url) adımlarını yineleyin.

### <a name="add-helloblockchain-application-to-instance"></a>Örneğe helloblockzincirleri uygulaması ekleme

1. Örnek listesinden Blockzincirine Veri Yöneticisi örneğinizi seçin.
1. **Blok zinciri uygulamaları**' nı seçin.
1. **Add (Ekle)** seçeneğini belirleyin.

    ![Blok zinciri uygulaması ekleme](./media/data-manager-cosmosdb/add-application.png)

    Blok zinciri uygulamasının adını ve akıllı sözleşme ABı ve bytecode URL 'Lerini girin.

    Ayar | Açıklama
    --------|------------
    Ad | İzlenecek blok zinciri uygulaması için benzersiz bir ad girin.
    Sözleşme ABı | Sözleşme ABı dosyasının URL yolu. Daha fazla bilgi için bkz. [sözleşme ABI ve bytecode URL 'Si oluşturma](#create-contract-abi-and-bytecode-url).
    Sözleşme bytecode 'u | Bytecode dosyasının URL yolu. Daha fazla bilgi için bkz. [sözleşme ABI ve bytecode URL 'Si oluşturma](#create-contract-abi-and-bytecode-url).

1. **Tamam**’ı seçin.

    Uygulama oluşturulduktan sonra uygulama, blok zinciri uygulamaları listesinde görünür.

    ![Blok zinciri uygulama listesi](./media/data-manager-cosmosdb/artifact-list.png)

Azure Storage hesabını silebilir veya daha fazla blok zinciri uygulaması yapılandırmak için kullanabilirsiniz. Azure Depolama hesabını silmek isterseniz, kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, ilişkili depolama hesabını ve kaynak grubuyla ilişkili diğer tüm kaynakları da siler.

## <a name="create-azure-cosmos-db"></a>Azure Cosmos DB oluştur

[!INCLUDE [cosmos-db-create-storage-account](../../../includes/cosmos-db-create-dbaccount.md)]

### <a name="add-a-database-and-container"></a>Veritabanı ve kapsayıcı ekleme

Veritabanı ve kapsayıcı oluşturmak için Azure portal Veri Gezgini kullanabilirsiniz.

1. Azure Cosmos DB hesabı sayfanızda sol gezinti **Veri Gezgini** seçin ve ardından **yeni kapsayıcı**' yı seçin.
1. **Kapsayıcı Ekle** bölmesinde, yeni kapsayıcının ayarlarını girin.

    ![Kapsayıcı ayarları ekle](./media/data-manager-cosmosdb/add-container.png)

    | Ayar | Açıklama
    |---------|-------------|
    | Veritabanı Kimliği | Yeni veritabanının adı olarak **blok zinciri-verileri** girin. |
    | İşleme | Aktarım hızını saniyede **400** istek BIRIMI (ru/s) olarak bırakın. Daha sonra gecikme süresini azaltmak isterseniz aktarım hızının ölçeğini artırabilirsiniz.|
    | Kapsayıcı KIMLIĞI | Yeni kapsayıcının adı olarak **iletileri** girin. |
    | Bölüm anahtarı | Bölüm anahtarı olarak **/MessageType** kullanın. |

1. **Tamam**’ı seçin. Veri Gezgini yeni veritabanını ve oluşturduğunuz kapsayıcıyı görüntüler.

## <a name="create-logic-app"></a>Mantıksal Uygulama oluşturma

Azure Logic Apps, sistemleri ve Hizmetleri tümleştirmeniz gerektiğinde iş süreçlerini ve iş akışlarını zamanlamanıza ve otomatikleştirmenize yardımcı olur. Azure Cosmos DB Event Grid bağlamak için bir mantıksal uygulama kullanabilirsiniz.

1. [Azure portalında](https://portal.azure.com) **Kaynak oluştur** > **Tümleştirme** > **Mantıksal Uygulama**’yı seçin.
1. Mantıksal uygulamanızı nerede oluşturacağınız hakkında ayrıntılı bilgi sağlayın. İşiniz bittiğinde **Oluştur**' u seçin.

    Mantıksal uygulamalar oluşturma hakkında daha fazla bilgi için bkz. [Azure Logic Apps otomatik iş akışları oluşturma](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Azure uygulamanızı dağıtduktan sonra mantıksal uygulama kaynağınızı seçin.
1. Logic Apps tasarımcısında **Şablonlar**altında **boş mantıksal uygulama**' yı seçin.

### <a name="add-event-grid-trigger"></a>Event Grid tetikleyicisi Ekle

Her mantıksal uygulama, belirli bir olay gerçekleştiğinde ya da belirli bir koşul karşılandığında tetiklenen bir tetikleyiciyle başlamalıdır. Tetikleyici her etkinleştirildiğinde Logic Apps altyapısı iş akışınızı başlatan ve çalıştıran bir mantıksal uygulama örneği oluşturur. Event Grid blok zinciri işlem verilerini Cosmos DB 'e göndermesi için Azure Event Grid tetikleyicisi kullanın.

1. Logic Apps tasarımcısında, **Azure Event Grid** bağlayıcısını arayıp seçin.
1. **Tetikleyiciler** sekmesinden **bir kaynak olay meydana geldiğinde**öğesini seçin.
1. Event Grid konusundan bir API bağlantısı oluşturun.

    ![Olay Kılavuzu tetikleyici ayarları](./media/data-manager-cosmosdb/event-grid-trigger.png)

    | Ayar | Açıklama
    |---------|-------------|
    | Abonelik | Event Grid konusunu içeren aboneliği seçin. |
    | Kaynak Türü | **Microsoft. EventGrid. konular**' ı seçin. |
    | Kaynak Adı | Blok zinciri Veri Yöneticisi işlem verileri iletilerini gönderdiği Event Grid konusunun adını seçin. |

### <a name="add-cosmos-db-action"></a>Cosmos DB eylem Ekle

Her işlem için Cosmos DB bir belge oluşturmak için bir eylem ekleyin. İletileri sınıflandırmak için bölüm anahtarı olarak işlem iletisi türünü kullanın.

1. **Yeni adım**'ı seçin.
1. **Bir eylem seçin**sayfasında **Azure Cosmos DB**aratın.
1. **Belge oluştur veya güncelleştir > Azure Cosmos DB > eylemler**' i seçin.
1. Cosmos DB veritabanınıza bir API bağlantısı oluşturun.

    ![Cosmos DB bağlantı ayarları](./media/data-manager-cosmosdb/cosmosdb-connection.png)

    | Ayar | Açıklama
    |---------|-------------|
    | Bağlantı Adı | Event Grid konusunu içeren aboneliği seçin. |
    | DocumentDB hesabı | [Azure Cosmos DB hesabı oluştur](#create-azure-cosmos-db) bölümünde oluşturduğunuz DocumentDB hesabını seçin. |

1. Daha önce oluşturduğunuz Azure Cosmos DB veritabanı **kimliğini** ve **koleksiyon kimliğini** [bir veritabanı ve kapsayıcı Ekle](#add-a-database-and-container) bölümünde girin.

1. **Belge** ayarını seçin. *Dinamik Içerik Ekle* açılır penceresinde, **ifade** ' ı seçin ve aşağıdaki ifadeyi kopyalayıp yapıştırın:

    ```
    addProperty(triggerBody()?['data'], 'id', utcNow())
    ```

    İfade, iletinin veri bölümünü alır ve KIMLIĞI bir zaman damgası değerine ayarlar.

1. **Yeni parametre Ekle** ' yi seçin ve **bölüm anahtarı değeri**' ni seçin.
1. **Bölüm anahtarı değerini** `"@{triggerBody()['data']['MessageType']}"`olarak ayarlayın. Değer çift tırnak işareti içine alınmalıdır.

    ![Cosmos DB ayarlarla Logic Apps tasarımcı](./media/data-manager-cosmosdb/create-action.png)

    Değer, Bölüm anahtarını işlem iletisi türüne ayarlar.

1. **Kaydet**’i seçin.

Mantıksal uygulama Event Grid konusunu izler. Blok zinciri Veri Yöneticisi yeni bir işlem iletisi gönderildiğinde, mantıksal uygulama Cosmos DB bir belge oluşturur.

## <a name="send-a-transaction"></a>İşlem gönder

Sonra, oluşturduğunuz şeyi test etmek için blok zinciri defterine bir işlem gönderin. Önkoşul öğreticisinde oluşturduğunuz **SendRequest. js** betiğini kullanın [: akıllı sözleşmeleri oluşturmak, derlemek ve dağıtmak için Visual Studio Code kullanın](send-transaction.md).

VS Code Terminal bölmesinde, komut dosyasını Konsorsiyumu blok zinciri ağınızda yürütmek için Truffle ' yi kullanın. Terminal bölmesi menü çubuğunda açılan menüde **Terminal** sekmesini ve **PowerShell** ' i seçin.

``` PowerShell
truffle exec sendrequest.js --network <blockchain network>
```

\<blok zinciri ağı\>, **Truffle-config. js**içinde tanımlanan blok zinciri ağının adıyla değiştirin.

![İşlem gönder](./media/data-manager-cosmosdb/send-request.png)

## <a name="view-transaction-data"></a>İşlem verilerini görüntüle

Blockzincirinizi Azure Cosmos DB Veri Yöneticisi bağladığınıza göre, blok zinciri işlem iletilerini Cosmos DB Veri Gezgini görüntüleyebilirsiniz.

1. Cosmos DB Veri Gezgini görünümüne gidin. Örneğin, **cosmosdb blok zinciri > Veri Gezgini > blok zinciri-veri > iletileri > öğeleri**.

    ![Cosmos DB Veri Gezgini](./media/data-manager-cosmosdb/data-explorer.png)

    Veri Gezgini, Cosmos DB veritabanında oluşturulan blok zinciri veri iletilerini listeler.

1. Öğe KIMLIĞI ' ni seçerek ve eşleşen işlem karması ile iletiyi bularak mesajlara göz atabilirsiniz.

    [![blok zinciri işlem ayrıntısı](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

    Ham işlem iletisi işlem hakkında ayrıntılı bilgi içerir. Ancak Özellik bilgileri şifrelenir.

    Helloblockzincirine akıllı sözleşmeyi blok zinciri Veri Yöneticisi örneğine eklediyseniz, kodu çözülmüş Özellik bilgilerini içeren bir **Contractproperties** ileti türü de gönderilir.

1. İşlem için **Contractproperties** iletisini bulun. Bu, listedeki bir sonraki ileti olmalıdır.

    [![blok zinciri işlem ayrıntısı](./media/data-manager-cosmosdb/properties-msg.png)](./media/data-manager-cosmosdb/properties-msg.png#lightbox)

    **Decodedproperties** dizisi işlemin özelliklerini içerir.

Tebrikler! Blok zinciri Veri Yöneticisi ve Azure Cosmos DB kullanarak bir işlem ileti Gezginini başarıyla oluşturdunuz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, bu öğretici için kullandığınız kaynakları ve kaynak gruplarını silebilirsiniz. Bir kaynak grubunu silmek için:

1. Azure portal sol gezinti bölmesindeki **kaynak grubu** ' na gidin ve silmek istediğiniz kaynak grubunu seçin.
1. **Kaynak grubunu sil**'i seçin. Kaynak grubu adını girip **Sil**' i seçerek silmeyi doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

Blok zinciri ile tümleştirme hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Azure Logic Apps ile Ethereum blok zinciri bağlayıcısını kullanma](ethereum-logic-app.md)
