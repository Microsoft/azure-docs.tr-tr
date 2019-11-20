---
title: Blok zincirini yapılandırma Veri Yöneticisi-Azure portal
description: Azure portal kullanarak blok zinciri oluşturma ve yönetme Veri Yöneticisi.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: chroyal
ms.openlocfilehash: 484322fb0486eeb4ab67366d32350c69a18da743
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605914"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Azure portal kullanarak blok zinciri Veri Yöneticisi yapılandırma

Blok zinciri verilerini yakalamak ve bir Azure Event Grid konusuna göndermek için Azure blok zinciri hizmeti için Blockzincirini Veri Yöneticisi yapılandırın.

Bir blok zinciri Veri Yöneticisi örneği yapılandırmak için şunları yapın:

* Azure blok zinciri hizmeti işlem düğümü için bir blok zinciri Veri Yöneticisi örneği oluşturma
* Blok zinciri uygulamalarınızı ekleyin

## <a name="prerequisites"></a>Ön koşullar

* [Hızlı başlangıç: Azure Portal veya hızlı başlangıç kullanarak bir blok zinciri üyesi oluşturma](create-member.md) [: Azure CLI kullanarak bir Azure blok zinciri hizmeti blok zinciri üyesi](create-member-cli.md) oluşturma
* [Event Grid konu başlığı](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) oluşturma
* [Azure Event Grid Içindeki olay işleyicileri](../../event-grid/event-handlers.md) hakkında bilgi edinin

## <a name="create-instance"></a>Örnek Oluştur

Bir blok zinciri Veri Yöneticisi örneği, bir Azure blok zinciri hizmeti işlem düğümünü bağlar ve izler. Yalnızca işlem düğümüne erişimi olan kullanıcılar bir bağlantı oluşturabilir. Örnek, işlem düğümünden tüm ham blok ve ham işlem verilerini yakalar.

Giden bağlantı, blok zinciri verilerini Azure Event Grid gönderir. Örneği oluştururken tek bir giden bağlantı yapılandırırsınız. Blok zinciri Veri Yöneticisi, belirli bir blok zinciri Veri Yöneticisi örneği için birden çok Event Grid konu giden bağlantısını destekler. Blok zinciri verilerini tek bir hedefe gönderebilir veya birden çok hedefe blok zinciri verileri gönderebilirsiniz. Başka bir hedef eklemek için örneğe ek giden bağlantılar eklemeniz yeterlidir.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Blok zinciri Veri Yöneticisi bağlamak istediğiniz Azure blok zinciri hizmeti üyesine gidin. **Blok zinciri veri Yöneticisi**seçin.
1. **Add (Ekle)** seçeneğini belirleyin.

    ![Blok zinciri ekleme Veri Yöneticisi](./media/data-manager-portal/add-instance.png)

    Şu ayrıntıları girin:

    Ayar | Açıklama
    --------|------------
    Ad | Bağlı bir blok zinciri Veri Yöneticisi için benzersiz bir ad girin. Blok zinciri Veri Yöneticisi adı küçük harf ve rakam içerebilir ve en fazla 20 karakter uzunluğunda olabilir.
    İşlem düğümü | Bir işlem düğümü seçin. Yalnızca okuma erişiminizin bulunduğu işlem düğümleri listelenir.
    Bağlantı adı | Blok zinciri işlem verilerinin gönderildiği giden bağlantıya ilişkin benzersiz bir ad girin.
    Olay Kılavuzu uç noktası | Blok zinciri Veri Yöneticisi örneğiyle aynı abonelikte bir olay Kılavuzu konusu seçin.

1. **Tamam**’ı seçin.

    Bir blok zinciri Veri Yöneticisi örneği oluşturmak bir dakikadan kısa sürer. Örnek dağıtıldıktan sonra otomatik olarak başlatılır. Çalışan bir blok zinciri Veri Yöneticisi örneği, işlem düğümündeki blok zinciri olaylarını yakalar ve giden bağlantılara veri gönderir.

    Yeni örnek, Azure blok zinciri hizmeti üyesine yönelik blok zinciri Veri Yöneticisi örnekleri listesinde görünür.

    ![Blok zinciri veri üyesi örneklerinin listesi](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Blok zinciri uygulaması ekleme

Bir blok zinciri uygulaması eklerseniz, blok zinciri uygulama için olay ve özellik durumunun kodunu çözer Veri Yöneticisi. Aksi takdirde, yalnızca ham blok ve ham işlem verileri gönderilir. Blok zinciri Veri Yöneticisi sözleşme dağıtıldığında sözleşme adreslerini de bulur. Bir blok zinciri Veri Yöneticisi örneğine birden çok blok zinciri uygulaması ekleyebilirsiniz.

> [!IMPORTANT]
> Şu anda, Solidity [dizi türlerini](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) veya [eşleme türlerini](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) bildiren blok zinciri uygulamaları tam olarak desteklenmez. Bir dizi veya eşleme türü olarak tanımlanan özellikler, *Contractpropertiesmsg* veya *Decodedcontracteventsmsg* iletilerinde kodu çözülür.

Blok zinciri Veri Yöneticisi, uygulamayı eklemek için akıllı sözleşme ABı ve dağıtılan bir bytecode dosyası gerektirir.

### <a name="get-contract-abi-and-bytecode"></a>Sözleşme ABı ve bytecode 'u al

Sözleşme ABı, akıllı sözleşme arabirimlerini tanımlar. Akıllı sözleşmeyle nasıl etkileşim kuracağınızı açıklar. Contract ABı 'ı panoya kopyalamak için [Ethereum uzantısı Için Azure blok zinciri geliştirme seti](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) ' ni kullanabilirsiniz.

1. Visual Studio Code gezgin bölmesinde, Solidity projenizin **Build/Contracts** klasörünü genişletin.
1. Sözleşme meta verileri JSON dosyasına sağ tıklayın. Dosya adı akıllı sözleşme adı ve ardından **. JSON** uzantısıdır.
1. **Sözleşme ABI Kopyala**' yı seçin.

    ![Sözleşmeyi Kopyala ABı seçimiyle Visual Studio Code bölmesi](./media/data-manager-portal/abi-devkit.png)

    Sözleşme ABı, panoya kopyalanır.

1. **ABI** DIZISINI bir JSON dosyası olarak kaydedin. Örneğin, *ABI. JSON*. Dosyayı sonraki bir adımda kullanırsınız.

Blok zinciri Veri Yöneticisi, akıllı sözleşme için dağıtılan bayt kodunu gerektirir. Dağıtılan bytecode, akıllı sözleşme bytecode değerinden farklı. Derlenen anlaşma meta veri dosyasından dağıtılan bayt kodunu alabilirsiniz.

1. Solidity projenizin **Build/Contracts** klasöründe yer alan Sözleşme meta veri dosyasını açın. Dosya adı akıllı sözleşme adı ve ardından **. JSON** uzantısıdır.
1. JSON dosyasında **Deployedbytecode** öğesini bulun.
1. Tırnak işaretleri olmadan onaltılık değeri kopyalayın.

    ![Meta verilerde bytecode ile Visual Studio Code bölmesi](./media/data-manager-portal/bytecode-metadata.png)

1. **Bytecode** DEĞERINI bir JSON dosyası olarak kaydedin. Örneğin, *bytecode. JSON*. Dosyayı sonraki bir adımda kullanırsınız.

Aşağıdaki örnek, VS Code düzenleyicide açık olan *ABI. JSON* ve *bytecode. JSON* dosyalarını gösterir. Dosyalarınız benzer görünmelidir.

![ABI. JSON ve bytecode. JSON dosyaları örneği](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Sözleşme ABı ve bytecode URL 'SI oluştur

Blok zinciri Veri Yöneticisi, bir uygulama eklenirken sözleşme ABı ve bytecode dosyalarına bir URL tarafından erişilebilmesini gerektirir. Bir Azure depolama hesabı kullanarak özel olarak erişilebilen bir URL sağlayabilirsiniz.

#### <a name="create-storage-account"></a>Depolama hesabı oluştur

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Sözleşme dosyalarını karşıya yükle

1. Depolama hesabı için yeni bir kapsayıcı oluşturun. **Kapsayıcı > kapsayıcıları**seçin.

    ![Depolama hesabı kapsayıcısı oluşturma](./media/data-manager-portal/create-container.png)

    | Alan | Açıklama |
    |-------|-------------|
    | Ad  | Kapsayıcıyı adlandırın. Örneğin, *smartcontract* |
    | Genel erişim düzeyi | *Özel (anonim erişim yok)* seçeneğini belirleyin |

1. Kapsayıcıyı oluşturmak için **Tamam**'ı seçin.
1. Kapsayıcıyı seçin ve ardından **karşıya yükle**' yi seçin.
1. [Sözleşmeyi al ABI ve bytecode](#get-contract-abi-and-bytecode) bölümünde oluşturduğunuz JSON dosyalarını seçin.

    ![Blobu karşıya yükle](./media/data-manager-portal/upload-blobs.png)

    **Karşıya Yükle**’yi seçin.

#### <a name="generate-url"></a>URL Oluştur

Her blob için, paylaşılan erişim imzası oluşturun.

1. ABı JSON blob 'unu seçin.
1. **SAS oluştur** ' u seçin
1. İstenen erişim imzası süre sonunu ayarlayın **ve ardından blob SAS belirteci oluştur ve URL 'yi**seçin.

    ![SAS belirteci oluştur](./media/data-manager-portal/generate-sas.png)

1. **BLOB SAS URL 'sini** kopyalayın ve sonraki bölüm için kaydedin.
1. Bytecode JSON blobu için [URL Oluştur](#generate-url) adımlarını yineleyin.

### <a name="add-application-to-instance"></a>Örneğe uygulama ekle

1. Örnek listesinden Blockzincirine Veri Yöneticisi örneğinizi seçin.
1. **Blok zinciri uygulamaları**' nı seçin.
1. **Add (Ekle)** seçeneğini belirleyin.

    ![Blok zinciri uygulaması ekleme](./media/data-manager-portal/add-application.png)

    Blok zinciri uygulamasının adını ve akıllı sözleşme ABı ve bytecode URL 'Lerini girin.

    Ayar | Açıklama
    --------|------------
    Ad | İzlenecek blok zinciri uygulaması için benzersiz bir ad girin.
    Sözleşme ABı | Sözleşme ABı dosyasının URL yolu. Daha fazla bilgi için bkz. [sözleşme ABI ve bytecode URL 'Si oluşturma](#create-contract-abi-and-bytecode-url).
    Sözleşme bytecode 'u | Bytecode dosyasının URL yolu. Daha fazla bilgi için bkz. [sözleşme ABI ve bytecode URL 'Si oluşturma](#create-contract-abi-and-bytecode-url).

1. **Tamam**’ı seçin.

    Uygulama oluşturulduktan sonra uygulama, blok zinciri uygulamaları listesinde görünür.

    ![Blok zinciri uygulama listesi](./media/data-manager-portal/artifact-list.png)

Azure Storage hesabını silebilir veya daha fazla blok zinciri uygulaması yapılandırmak için kullanabilirsiniz. Azure Depolama hesabını silmek isterseniz, kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, ilişkili depolama hesabını ve kaynak grubuyla ilişkili diğer tüm kaynakları da siler.

## <a name="stop-instance"></a>Örneği durdur

Blok zinciri olaylarını yakalamayı durdurmak ve giden bağlantılara veri göndermek istediğinizde blok zinciri Yöneticisi örneğini durdurun. Örnek durdurulduğunda, blok zinciri Veri Yöneticisi için ücret alınmaz. Daha fazla bilgi için bkz. [Fiyatlandırma](https://azure.microsoft.com/pricing/details/blockchain-service).

1. **Genel Bakış ' a** gidin ve **Durdur**' u seçin.

    ![Örneği durdur](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>Sonraki adımlar

Blok zinciri Veri Yöneticisi ve Azure Cosmos DB kullanarak bir blok zinciri işlem iletisi Gezgini oluşturmayı deneyin.

> [!div class="nextstepaction"]
> [Öğretici: Azure Cosmos DB 'a veri göndermek için blok zinciri Veri Yöneticisi kullanma](data-manager-cosmosdb.md)