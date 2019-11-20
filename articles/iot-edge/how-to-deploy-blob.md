---
title: Azure Blob Storage modülünü cihazlara dağıtma-Azure IoT Edge | Microsoft Docs
description: Verileri kenarda depolamak için IoT Edge cihazınıza bir Azure Blob depolama modülü dağıtın.
author: arduppal
ms.author: arduppal
ms.date: 08/07/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.reviewer: arduppal
manager: mchad
ms.openlocfilehash: 603c407f93294f2c9697dcee4c3d5d829033d5d9
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176966"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>IoT Edge modülünde Azure Blob Storage 'ı cihazınıza dağıtma

IoT Edge bir cihaza modül dağıtmanın birkaç yolu vardır ve bunların hepsi IoT Edge modüllerde Azure Blob depolama için çalışır. En basit iki yöntem Azure portal veya Visual Studio Code şablonlarını kullanmaktır.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliğinizdeki bir [IoT Hub 'ı](../iot-hub/iot-hub-create-through-portal.md) .
- IoT Edge çalışma zamanı yüklü [IoT Edge bir cihaz](how-to-register-device.md) .
- [Visual Studio Code](https://code.visualstudio.com/) ve Visual Studio Code dağıtım yapıyorsanız [Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) .

## <a name="deploy-from-the-azure-portal"></a>Azure portal dağıtma

Azure portal, dağıtım bildirimi oluşturma ve dağıtımı bir IoT Edge cihazına gönderme işlemlerinde size rehberlik eder.

### <a name="select-your-device"></a>Cihazınızı seçin

1. [Azure Portal](https://portal.azure.com) oturum açın ve IoT Hub 'ınıza gidin.
1. Menüden **IoT Edge** seçin.
1. Cihaz listesinden hedef cihazın KIMLIĞINE tıklayın.
1. **Modülleri Ayarlama**'yı seçin.

### <a name="configure-a-deployment-manifest"></a>Dağıtım bildirimi yapılandırma

Dağıtım bildirimi, hangi modüllerin dağıtılacağını, modüller arasında verilerin nasıl akacağını ve modül TWINS 'in istenen özelliklerini tanımlayan bir JSON belgesidir. Azure portal, JSON belgesini el ile oluşturmak yerine bir dağıtım bildirimi oluşturma konusunda size yol gösteren bir sihirbaza sahiptir. Üç adım vardır: **modüller ekleme**, **rotalar belirtme**ve **dağıtımı İnceleme**.

#### <a name="add-modules"></a>Modül Ekle

1. Sayfanın **dağıtım modülleri** bölümünde **Ekle**' yi seçin.

1. Açılan listedeki modül türlerinden **IoT Edge modül**' ı seçin.

1. Modül için bir ad girin ve ardından kapsayıcı görüntüsünü belirtin:

   - **Ad** -azureblobstorageoniotedge
   - **Görüntü URI 'si** -MCR.Microsoft.com/Azure-Blob-Storage:latest

   > [!IMPORTANT]
   > Modüller için çağrılar yaptığınızda büyük/küçük harfe duyarlı Azure IoT Edge ve depolama SDK 'Sı varsayılan olarak küçük harfe duyarlıdır. [Azure Marketi](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) 'ndeki modülün adı **AzureBlobStorageonIoTEdge**olsa da, adın küçük harfe değiştirilmesi IoT Edge modüldeki Azure Blob depolama ile olan bağlantılarınızın kesilmemesini sağlamaya yardımcı olur.

1. Varsayılan **kapsayıcı oluşturma seçenekleri** değerleri, kapsayıcınıza ait bağlantı noktası bağlamalarını tanımlar, ancak depolama hesabı bilgilerinizi ve cihazınızda depolama için bir bağlama eklemeniz gerekir. Portalda varsayılan JSON değerini aşağıdaki JSON ile değiştirin:

   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage mount>"
       ],
       "PortBindings":{
         "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

1. Aşağıdaki bilgilerle kopyaladığınız JSON 'yi güncelleştirin:

   - `<your storage account name>`, anımsayabileceğiniz bir adla değiştirin. Hesap adları, küçük harf ve sayılarla 3 ile 24 karakter uzunluğunda olmalıdır. Boşluk yok.

   - `<your storage account key>`, 64 baytlık bir Base64 anahtarıyla değiştirin. [Generateplus](https://generate.plus/en/base64)gibi araçlarla bir anahtar oluşturabilirsiniz. Diğer modüllerden blob depolamaya erişmek için bu kimlik bilgilerini kullanacaksınız.

   - `<storage mount>`, kapsayıcı işletim sisteminize göre değiştirin. Blob modülünün verilerini depolamasını istediğiniz IoT Edge cihazınızda bir [birimin](https://docs.docker.com/storage/volumes/) veya mutlak yolun adını belirtin. Depolama alanı, cihazınızdaki bir konumu modüldeki bir konum kümesine eşler.

     - Linux kapsayıcıları için, biçim *\<depolama yolu veya birim >:/blobroot*. Örneğin:
         - [birim bağlama](https://docs.docker.com/storage/volumes/)kullan: **My-Volume:/blobroot** 
         - [bağlama bağlama](https://docs.docker.com/storage/bind-mounts/): **/SRV/containerdata:/blobroot**kullanın. [Kapsayıcı kullanıcısına dizin erişimi sağlamak](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux) için adımları izlediğinizden emin olun
     - Windows kapsayıcıları için, biçim *\<depolama yolu veya birim >: C:/BlobRoot*. Örneğin:
         - [birim bağlama](https://docs.docker.com/storage/volumes/): **My-Volume: C:/blobroot**kullanın. 
         - [bağlama bağlama](https://docs.docker.com/storage/bind-mounts/): **c:/Containerdata: c:/blobroot**kullanın.
         - Yerel sürücünüzü kullanmak yerine, SMB ağ konumunuzu eşleyebilirsiniz, daha fazla bilgi için bkz. [yerel depolama alanı olarak SMB paylaşımının kullanımı](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Depolama bağlama değerinin, modüldeki belirli bir konuma işaret eden ikinci yarısını değiştirmeyin. Depolama alanı, Linux kapsayıcıları için: **/blobroot** ve Windows kapsayıcıları için **C:/blobroot** ile her zaman bitmelidir.

1. Modülünüzün [Devicetoclouduploadproperties](how-to-store-data-blob.md#devicetoclouduploadproperties) ve [deviceoto deleteproperties](how-to-store-data-blob.md#deviceautodeleteproperties) özelliklerini ayarlayarak aşağıdaki JSON 'yi kopyalayıp, **set Module ikizi 'in istenen özellikler** kutusuna yapıştırın. Her bir özelliği uygun bir değerle yapılandırın, kaydedin ve dağıtıma devam edin. IoT Edge simülatörü kullanıyorsanız, bu özellikler için değerleri, [Devicetoclouduploadproperties](how-to-store-data-blob.md#devicetoclouduploadproperties) ve [deviceoto deleteproperties](how-to-store-data-blob.md#deviceautodeleteproperties)'in Açıklama bölümünde bulabileceğiniz bu özellikler için ilgili ortam değişkenlerine ayarlayın.

   ```json
   {
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading":<true,false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload":<true,false>
       }
     }
   }

      ```

   ![kapsayıcı oluşturma seçeneklerini ayarlama, Deviceoto Deleteproperties ve deviceToCloudUploadProperties özellikleri](./media/how-to-deploy-blob/iotedge-custom-module.png)

   Modülünüzün dağıtılmasından sonra deviceToCloudUploadProperties ve Deviceoto Deleteproperties yapılandırma hakkında bilgi için bkz. [Ikizi Module düzenleme](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). İstenen özellikler hakkında daha fazla bilgi için bkz. [istenen özellikleri tanımlama veya güncelleştirme](module-composition.md#define-or-update-desired-properties).

1. **Kaydet**’i seçin.

1. Rotalar bölümüne devam etmek için **İleri ' yi** seçin.

#### <a name="specify-routes"></a>Rotaları belirtin

Varsayılan yolları koruyun ve gözden geçirme bölümüne devam etmek için **İleri** ' yi seçin.

#### <a name="review-deployment"></a>Dağıtımı gözden geçir

İnceleme Bölümü, önceki iki bölümdeki seçimlerinize göre oluşturulan JSON dağıtım bildirimini gösterir. Ayrıca, eklemediğiniz iki modül de vardır: **$edgeAgent** ve **$edgeHub**. Bu iki modül [IoT Edge çalışma zamanını](iot-edge-runtime.md) yapar ve her dağıtımda gerekli varsayılanlar olur.

Dağıtım bilgilerinizi gözden geçirin ve ardından **Gönder**' i seçin.

### <a name="verify-your-deployment"></a>Dağıtımınızı doğrulama

Dağıtımı gönderdikten sonra IoT Hub 'ınızın **IoT Edge** sayfasına dönersiniz.

1. Bilgilerini açmak için dağıtıma hedeflenmiş IoT Edge cihazı seçin.
1. Cihaz ayrıntılarında, BLOB depolama modülünün hem **dağıtım** hem de **cihaz tarafından bildirilen**olarak listelendiğini doğrulayın.

Modülün cihazda başlatılması ve sonra IoT Hub geri bildirilmesi birkaç dakika sürebilir. Güncelleştirilmiş durumu görmek için sayfayı yenileyin.

## <a name="deploy-from-visual-studio-code"></a>Visual Studio Code dağıtma

Azure IoT Edge, Edge çözümleri geliştirmenize yardımcı olmak için Visual Studio Code şablonlar sağlar. Blob Storage modülü ile yeni bir IoT Edge çözümü oluşturmak ve dağıtım bildirimini yapılandırmak için aşağıdaki adımları kullanın.

1.  > **komut paletini** **görüntüle** ' yi seçin.

1. Komut paletinde **Azure IoT Edge: New IoT Edge solution** komutunu girin ve çalıştırın.

   ![Yeni IoT Edge çözüm Çalıştır](./media/how-to-develop-csharp-module/new-solution.png)

   Çözümünüzü oluşturmak için komut paletindeki yönergeleri izleyin.

   | Alan | Değer |
   | ----- | ----- |
   | Klasör seçin | Çözüm dosyalarını oluşturmak için Visual Studio Code geliştirme makinenizdeki konumunu seçin. |
   | Çözüm adı sağlayın | Çözümünüz için açıklayıcı bir ad girin veya varsayılan **EdgeSolution**kabul edin. |
   | Modül şablonunu seçin | **Var olan modülü seçin (tam görüntü URL 'Si girin)** . |
   | Modül adı sağlayın | Modülünüzün **azureblobstorageoniotedge**gibi bir tam küçük harf adı girin.<br /><br />IoT Edge modülünde Azure Blob depolama için küçük harfli bir ad kullanılması önemlidir. IoT Edge modüllerle ilgili olarak büyük/küçük harfe duyarlıdır ve depolama SDK varsayılan olarak küçük harfe duyarlıdır. |
   | Modül için Docker görüntüsü sağlama | Görüntü URI 'sini sağlayın: **MCR.Microsoft.com/Azure-Blob-Storage:latest** |

   Visual Studio Code verdiğiniz bilgileri alır, bir IoT Edge çözümü oluşturur ve sonra yeni bir pencerede yükler. Çözüm şablonu, BLOB depolama modülü görüntünüzü içeren bir dağıtım bildirim şablonu oluşturur, ancak modülün oluşturma seçeneklerini yapılandırmanız gerekir.

1. Yeni çözüm çalışma alanınızda *Deployment. Template. JSON* ' i açın ve **modüller** bölümünü bulun. Aşağıdaki yapılandırma değişikliklerini yapın:

   1. Bu dağıtım için gerekli olmadığından **SimulatedTemperatureSensor** modülünü silin.

   1. Aşağıdaki kodu kopyalayıp `createOptions` alanına yapıştırın:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage mount>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Güncelleştirme modülü createOptions-Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. `<your storage account name>`, anımsayabileceğiniz bir adla değiştirin. Hesap adları, küçük harf ve sayılarla 3 ile 24 karakter uzunluğunda olmalıdır. Boşluk yok.

1. `<your storage account key>`, 64 baytlık bir Base64 anahtarıyla değiştirin. [Generateplus](https://generate.plus/en/base64)gibi araçlarla bir anahtar oluşturabilirsiniz. Diğer modüllerden blob depolamaya erişmek için bu kimlik bilgilerini kullanacaksınız.

1. `<storage mount>`, kapsayıcı işletim sisteminize göre değiştirin. Blob modülünün verilerini depolamasını istediğiniz IoT Edge cihazınızda bir [birimin](https://docs.docker.com/storage/volumes/) veya mutlak yolun adını belirtin. Depolama alanı, cihazınızdaki bir konumu modüldeki bir konum kümesine eşler.  

      
     - Linux kapsayıcıları için, biçim *\<depolama yolu veya birim >:/blobroot*. Örneğin:
         - [birim bağlama](https://docs.docker.com/storage/volumes/)kullan: **My-Volume:/blobroot** 
         - [bağlama bağlama](https://docs.docker.com/storage/bind-mounts/): **/SRV/containerdata:/blobroot**kullanın. [Kapsayıcı kullanıcısına dizin erişimi sağlamak](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux) için adımları izlediğinizden emin olun
     - Windows kapsayıcıları için, biçim *\<depolama yolu veya birim >: C:/BlobRoot*. Örneğin:
         - [birim bağlama](https://docs.docker.com/storage/volumes/): **My-Volume: C:/blobroot**kullanın. 
         - [bağlama bağlama](https://docs.docker.com/storage/bind-mounts/): **c:/Containerdata: c:/blobroot**kullanın.
         - Yerel sürücünüzü kullanmak yerine, SMB ağ konumunuzu eşleyebilirsiniz, daha fazla bilgi için bkz. [yerel depolama alanı olarak SMB paylaşımının kullanımı](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Depolama bağlama değerinin, modüldeki belirli bir konuma işaret eden ikinci yarısını değiştirmeyin. Depolama alanı, Linux kapsayıcıları için: **/blobroot** ve Windows kapsayıcıları için **C:/blobroot** ile her zaman bitmelidir.

1. Aşağıdaki JSON 'ı *Deployment. Template. JSON* dosyasına ekleyerek modülünüzün [Devicetoclouduploadproperties](how-to-store-data-blob.md#devicetoclouduploadproperties) ve [deviceoto deleteproperties özelliklerini](how-to-store-data-blob.md#deviceautodeleteproperties) yapılandırın. Her bir özelliği uygun bir değerle yapılandırın ve dosyayı kaydedin. IoT Edge simülatörü kullanıyorsanız, bu özellikler için değerleri, [Devicetoclouduploadproperties](how-to-store-data-blob.md#devicetoclouduploadproperties) ve [deviceoto deleteproperties](how-to-store-data-blob.md#deviceautodeleteproperties) 'in Açıklama bölümünde bulabileceğiniz bu özellikler için ilgili ortam değişkenlerine ayarlayın.

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading": <true, false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload": <true, false>
       }
     }
   }
   ```

   ![azureblobstorageoniotedge-Visual Studio Code için istenen özellikleri ayarlama](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   Modülünüzün dağıtılmasından sonra deviceToCloudUploadProperties ve Deviceoto Deleteproperties yapılandırma hakkında bilgi için bkz. [Ikizi Module düzenleme](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Kapsayıcı oluşturma seçenekleri, yeniden başlatma ilkesi ve istenen durum hakkında daha fazla bilgi için bkz. [Edgeagent istenen özellikleri](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. *deployment.template.json* dosyasını kaydedin.

1. **Deployment. Template. JSON** öğesine sağ tıklayın ve **IoT Edge dağıtım bildirimi oluştur**' u seçin.

1. Visual Studio Code, *Deployment. Template. JSON* içinde verdiğiniz bilgileri alır ve yeni bir dağıtım bildirim dosyası oluşturmak için kullanır. Dağıtım bildirimi, çözüm çalışma alanınızdaki yeni bir **yapılandırma** klasöründe oluşturulur. Bu dosyayı aldıktan sonra, [Azure clı 2,0 ile](how-to-deploy-modules-cli.md) [Visual Studio Code Azure IoT Edge modülleri dağıtma](how-to-deploy-modules-vscode.md) veya Azure IoT Edge modülleri Dağıtma bölümündeki adımları izleyebilirsiniz.

## <a name="deploy-multiple-module-instances"></a>Birden çok modül örneği dağıtma

IoT Edge modülünde Azure Blob Storage 'ın birden fazla örneğini dağıtmak istiyorsanız, farklı bir depolama yolu sağlamanız ve modülün bağlandığı `HostPort` değerini değiştirmeniz gerekir. BLOB depolama modülleri her zaman kapsayıcıda 11002 numaralı bağlantı noktasını kullanıma sunar, ancak konakta hangi bağlantı noktasını bağladığına ilişkin bildirimi de belirtebilirsiniz.

`HostPort` değerini değiştirmek için **kapsayıcı oluşturma seçeneklerini** (Azure Portal) veya **createoptions** alanını (Visual Studio Code içindeki *Deployment. Template. JSON* dosyasında) düzenleyin:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Ek BLOB depolama modüllerine bağlandığınızda, uç noktayı güncelleştirilmiş konak bağlantı noktasını işaret etmek üzere değiştirin.

## <a name="next-steps"></a>Sonraki adımlar
[IoT Edge Azure Blob depolama](how-to-store-data-blob.md) hakkında daha fazla bilgi edinin

Dağıtım bildirimlerinin nasıl çalıştığı ve nasıl oluşturulacağı hakkında daha fazla bilgi için bkz. [IoT Edge modüllerinin nasıl kullanılabileceğini, yapılandırılacağını ve yeniden kullanıldığını anlayın](module-composition.md).
