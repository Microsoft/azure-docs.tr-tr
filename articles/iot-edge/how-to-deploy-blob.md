---
title: Modül üzerinde blob Storage 'ı cihazınıza dağıtma-Azure IoT Edge
description: Verileri kenarda depolamak için IoT Edge cihazınıza bir Azure Blob depolama modülü dağıtın.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: f766a77ee55351e498a379146826ba1d5507bc93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201179"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>IoT Edge modülünde Azure Blob Depolama'yı cihazınıza dağıtma

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge bir cihaza modül dağıtmanın birkaç yolu vardır ve bunların hepsi IoT Edge modüllerde Azure Blob depolama için çalışır. En basit iki yöntem Azure portal veya Visual Studio Code şablonlarını kullanmaktır.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliğinizdeki bir [IoT Hub 'ı](../iot-hub/iot-hub-create-through-portal.md) .
- IoT Edge bir cihaz.

  Ayarlanmış bir IoT Edge cihazınız yoksa bir Azure sanal makinesinde bir tane oluşturabilirsiniz. [Bir sanal Linux cihazı oluşturmak](quickstart-linux.md) veya [bir sanal Windows cihazı oluşturmak](quickstart.md)için hızlı başlangıç makalelerinden birindeki adımları izleyin.

- [Visual Studio Code](https://code.visualstudio.com/) ve Visual Studio Code dağıtım yapıyorsanız [Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) .

## <a name="deploy-from-the-azure-portal"></a>Azure portal dağıtma

Azure portal, dağıtım bildirimi oluşturma ve dağıtımı bir IoT Edge cihazına gönderme işlemlerinde size rehberlik eder.

### <a name="select-your-device"></a>Cihazınızı seçin

1. [Azure Portal](https://portal.azure.com) oturum açın ve IoT Hub 'ınıza gidin.
1. Menüden **IoT Edge** seçin.
1. Cihaz listesinden hedef cihazın KIMLIĞINE tıklayın.
1. **Modülleri Ayarlama**'yı seçin.

### <a name="configure-a-deployment-manifest"></a>Dağıtım bildirimi yapılandırma

Dağıtım bildirimi, hangi modüllerin dağıtılacağını, modüller arasında verilerin nasıl akacağını ve modül TWINS 'in istenen özelliklerini tanımlayan bir JSON belgesidir. Azure portal, dağıtım bildirimi oluşturma konusunda size yol gösteren bir sihirbaza sahiptir. Sekmelerde düzenlenmiş üç adım vardır: **modüller**, **rotalar** ve **Gözden geçirme + oluştur**.

#### <a name="add-modules"></a>Modül ekle

1. Sayfanın **IoT Edge modüller** **bölümünde açılan menü ekle '** ye tıklayın ve **IoT Edge modülü** ' nü seçerek **IoT Edge modülü Ekle** sayfasını görüntüleyin.

2. **Modül ayarları** sekmesinde, modül için bir ad sağlayın ve ardından KAPSAYıCı görüntüsü URI 'sini belirtin:

   Örnekler:
  
   - **IoT Edge modül adı**: `azureblobstorageoniotedge`
   - **Görüntü URI 'si**: `mcr.microsoft.com/azure-blob-storage:latest`

   ![Ekran görüntüsü, ı T uç modülü Ekle sayfasının modül ayarları sekmesini gösterir.](./media/how-to-deploy-blob/addmodule-tab1.png)

   Bu yordamda açıklandığı gibi **Modül ayarlarında**, **kapsayıcı oluşturma seçeneklerinde** ve **module ikizi Settings** sekmelerinde değer belirtene kadar **Ekle** ' yi seçmeyin.

   > [!IMPORTANT]
   > Modüller için çağrılar yaptığınızda büyük/küçük harfe duyarlı Azure IoT Edge ve depolama SDK 'Sı varsayılan olarak küçük harfe duyarlıdır. [Azure Marketi](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) 'ndeki modülün adı **AzureBlobStorageonIoTEdge** olsa da, adın küçük harfe değiştirilmesi IoT Edge modüldeki Azure Blob depolama ile olan bağlantılarınızın kesilmemesini sağlamaya yardımcı olur.

3. **Kapsayıcı oluşturma seçenekleri** sekmesini açın.

   ![Ekran görüntüsü, ı T uç modülü Ekle sayfasının kapsayıcı oluşturma seçenekleri sekmesini gösterir.](./media/how-to-deploy-blob/addmodule-tab3.png)

   Depolama hesabı bilgilerini ve cihazınızda depolama için bir bağlama sağlamak üzere aşağıdaki JSON 'ı kopyalayıp kutuya yapıştırın.
  
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

4. Kapsayıcıya kopyaladığınız JSON 'ı güncelleştirme aşağıdaki bilgileri içeren bir **seçenek oluşturun** :

   - `<your storage account name>`Hatırlayabileceğiniz bir adla değiştirin. Hesap adları, küçük harf ve sayılarla 3 ile 24 karakter uzunluğunda olmalıdır. Boşluk yok.

   - `<your storage account key>`64 baytlık bir Base64 anahtarıyla değiştirin. [Generateplus](https://generate.plus/en/base64)gibi araçlarla bir anahtar oluşturabilirsiniz. Diğer modüllerden blob depolamaya erişmek için bu kimlik bilgilerini kullanacaksınız.

   - Öğesini `<storage mount>` kapsayıcı işletim sisteminize göre değiştirin. Blob modülünün verilerini depolayacağı IoT Edge cihazınızda, bir [birimin](https://docs.docker.com/storage/volumes/) veya mutlak yolun adını belirtin. Depolama alanı, cihazınızdaki bir konumu modüldeki bir konum kümesine eşler.

     - Linux kapsayıcıları için şu biçim **\<your storage path or volume> :/blobroot**. Örnek:
         - [birim bağlama](https://docs.docker.com/storage/volumes/)kullan:`my-volume:/blobroot`
         - [bağlama bağlama](https://docs.docker.com/storage/bind-mounts/)kullan: `/srv/containerdata:/blobroot` . [Kapsayıcı kullanıcısına dizin erişimi sağlamak](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux) için adımları izlediğinizden emin olun
     - Windows kapsayıcıları için, biçim **\<your storage path or volume> : C:/blobroot**. Örnek:
         - [birim bağlama](https://docs.docker.com/storage/volumes/)kullan: `my-volume:C:/BlobRoot` .
         - [bağlama bağlama](https://docs.docker.com/storage/bind-mounts/)kullan: `C:/ContainerData:C:/BlobRoot` .
         - Yerel sürücünüzü kullanmak yerine, SMB ağ konumunuzu eşleyebilirsiniz, daha fazla bilgi için bkz. [yerel depolama alanı olarak SMB paylaşımının kullanımı](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > IoT Edge modülündeki blob depolamada belirli bir konuma işaret eden depolama bağlama değerinin ikinci yarısını değiştirmeyin. Depolama alanı, Linux kapsayıcıları için: **/blobroot** ve Windows kapsayıcıları için **C:/blobroot** ile her zaman bitmelidir.

5. **Modül Ikizi ayarları** sekmesinde, aşağıdaki JSON 'u kopyalayın ve kutuya yapıştırın.

   ![Ekran görüntüsü, ı T uç modülü Ekle sayfasının modül Ikizi ayarları sekmesini gösterir.](./media/how-to-deploy-blob/addmodule-tab4.png)

   Her özelliği, yer tutucularla gösterildiği gibi uygun bir değerle yapılandırın. IoT Edge simülatörü kullanıyorsanız, [Devicetoclouduploadproperties](how-to-store-data-blob.md#devicetoclouduploadproperties) ve [Deviceoto deleteproperties](how-to-store-data-blob.md#deviceautodeleteproperties)bölümünde açıklandığı gibi bu özellikler için değerleri ilgili ortam değişkenlerine ayarlayın.

   ```json
   {
     "deviceAutoDeleteProperties": {
       "deleteOn": <true, false>,
       "deleteAfterMinutes": <timeToLiveInMinutes>,
       "retainWhileUploading": <true,false>
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
       "deleteAfterUpload": <true,false>
     }
   }
   ```

   Modülünüzün dağıtılmasından sonra deviceToCloudUploadProperties ve Deviceoto Deleteproperties yapılandırma hakkında bilgi için bkz. [Ikizi Module düzenleme](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). İstenen özellikler hakkında daha fazla bilgi için bkz. [istenen özellikleri tanımlama veya güncelleştirme](module-composition.md#define-or-update-desired-properties).

6. **Add (Ekle)** seçeneğini belirleyin.

7. **İleri ' yi seçin:** rotalar bölümüne devam etmek için yollar.

#### <a name="specify-routes"></a>Rotaları belirtin

Varsayılan yolları koruyun ve Ileri ' yi seçin: Gözden geçirme bölümüne devam etmek için, İnceleme **+ Oluştur** seçeneğini belirleyin.

#### <a name="review-deployment"></a>Dağıtımı gözden geçir

İnceleme Bölümü, önceki iki bölümdeki seçimlerinize göre oluşturulan JSON dağıtım bildirimini gösterir. Ayrıca, eklemediğiniz iki modül de vardır: **$edgeAgent** ve **$edgeHub**. Bu iki modül [IoT Edge çalışma zamanını](iot-edge-runtime.md) yapar ve her dağıtımda gerekli varsayılanlar olur.

Dağıtım bilgilerinizi gözden geçirin ve ardından **Oluştur**' u seçin.

### <a name="verify-your-deployment"></a>Dağıtımınızı doğrulama

Dağıtımı oluşturduktan sonra IoT Hub 'ınızın **IoT Edge** sayfasına dönersiniz.

1. Bilgilerini açmak için dağıtıma hedeflenmiş IoT Edge cihazı seçin.
1. Cihaz ayrıntılarında, BLOB depolama modülünün hem **dağıtım** hem de **cihaz tarafından bildirilen** olarak listelendiğini doğrulayın.

Modülün cihazda başlatılması ve sonra IoT Hub geri bildirilmesi birkaç dakika sürebilir. Güncelleştirilmiş durumu görmek için sayfayı yenileyin.

## <a name="deploy-from-visual-studio-code"></a>Visual Studio Code dağıtma

Azure IoT Edge, Edge çözümleri geliştirmenize yardımcı olmak için Visual Studio Code şablonlar sağlar. Blob Storage modülü ile yeni bir IoT Edge çözümü oluşturmak ve dağıtım bildirimini yapılandırmak için aşağıdaki adımları kullanın.

1. **Görünüm**  >  **komut paleti**' ni seçin.

1. Komut paletinde **Azure IoT Edge: New IoT Edge solution** komutunu girin ve çalıştırın.

   ![Yeni IoT Edge çözüm Çalıştır](./media/how-to-develop-csharp-module/new-solution.png)

   Çözümünüzü oluşturmak için komut paletindeki yönergeleri izleyin.

   | Alan | Değer |
   | ----- | ----- |
   | Klasör seçin | Çözüm dosyalarını oluşturmak için Visual Studio Code geliştirme makinenizdeki konumunu seçin. |
   | Çözüm adı sağlayın | Çözümünüz için açıklayıcı bir ad girin veya varsayılan **EdgeSolution** kabul edin. |
   | Modül şablonunu seçin | **Var olan modülü seçin (tam görüntü URL 'Si girin)**. |
   | Modül adı sağlayın | Modülünüzün **azureblobstorageoniotedge** gibi bir tam küçük harf adı girin.<br/><br/>IoT Edge modülünde Azure Blob depolama için küçük harfli bir ad kullanılması önemlidir. IoT Edge modüllerle ilgili olarak büyük/küçük harfe duyarlıdır ve depolama SDK varsayılan olarak küçük harfe duyarlıdır. |
   | Modül için Docker görüntüsü sağlama | Görüntü URI 'sini sağlayın: **MCR.Microsoft.com/Azure-Blob-Storage:latest** |

   Visual Studio Code verdiğiniz bilgileri alır, bir IoT Edge çözümü oluşturur ve sonra yeni bir pencerede yükler. Çözüm şablonu, BLOB depolama modülü görüntünüzü içeren bir dağıtım bildirim şablonu oluşturur, ancak modülün oluşturma seçeneklerini yapılandırmanız gerekir.

1. Yeni çözüm çalışma alanınızda *deployment.template.js* açın ve **modüller** bölümünü bulun. Aşağıdaki yapılandırma değişikliklerini yapın:

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

1. `<your storage account name>`Hatırlayabileceğiniz bir adla değiştirin. Hesap adları, küçük harf ve sayılarla 3 ile 24 karakter uzunluğunda olmalıdır. Boşluk yok.

1. `<your storage account key>`64 baytlık bir Base64 anahtarıyla değiştirin. [Generateplus](https://generate.plus/en/base64)gibi araçlarla bir anahtar oluşturabilirsiniz. Diğer modüllerden blob depolamaya erişmek için bu kimlik bilgilerini kullanacaksınız.

1. Öğesini `<storage mount>` kapsayıcı işletim sisteminize göre değiştirin. Blob modülünün verilerini depolamasını istediğiniz IoT Edge cihazınızda bir [birimin](https://docs.docker.com/storage/volumes/) veya mutlak yolun adını belirtin. Depolama alanı, cihazınızdaki bir konumu modüldeki bir konum kümesine eşler.  

     - Linux kapsayıcıları için şu biçim **\<your storage path or volume> :/blobroot**. Örnek:
         - [birim bağlama](https://docs.docker.com/storage/volumes/)kullan:`my-volume:/blobroot`
         - [bağlama bağlama](https://docs.docker.com/storage/bind-mounts/)kullan: `/srv/containerdata:/blobroot` . [Kapsayıcı kullanıcısına dizin erişimi sağlamak](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux) için adımları izlediğinizden emin olun
     - Windows kapsayıcıları için, biçim **\<your storage path or volume> : C:/blobroot**. Örnek:
         - [Birim bağlama](https://docs.docker.com/storage/volumes/)kullan: `my-volume:C:/BlobRoot` .
         - [Bağlama bağlama](https://docs.docker.com/storage/bind-mounts/)kullan: `C:/ContainerData:C:/BlobRoot` .
         - Yerel sürücünüzü kullanmak yerine, SMB ağ konumunuzu eşleyebilirsiniz. Daha fazla bilgi için bkz. [yerel depolama alanı olarak SMB paylaşımının kullanımı](how-to-store-data-blob.md#using-smb-share-as-your-local-storage).

     > [!IMPORTANT]
     > IoT Edge modülündeki blob depolamada belirli bir konuma işaret eden depolama bağlama değerinin ikinci yarısını değiştirmeyin. Depolama alanı, Linux kapsayıcıları için: **/blobroot** ve Windows kapsayıcıları için **C:/blobroot** ile her zaman bitmelidir.

1. Aşağıdaki JSON dosyasını *deployment.template.js* dosyasına ekleyerek modülünüzün [Devicetoclouduploadproperties](how-to-store-data-blob.md#devicetoclouduploadproperties) ve [deviceoto deleteproperties](how-to-store-data-blob.md#deviceautodeleteproperties) ' i yapılandırın. Her bir özelliği uygun bir değerle yapılandırın ve dosyayı kaydedin. IoT Edge simülatörü kullanıyorsanız, bu özellikler için değerleri, [Devicetoclouduploadproperties](how-to-store-data-blob.md#devicetoclouduploadproperties) ve [deviceoto deleteproperties](how-to-store-data-blob.md#deviceautodeleteproperties) 'in Açıklama bölümünde bulabileceğiniz bu özellikler için ilgili ortam değişkenlerine ayarlayın.

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

1. **deployment.template.jsüzerinde** sağ tıklayın ve **IoT Edge dağıtım bildirimi oluştur**' u seçin.

1. Visual Studio Code, *üzerindedeployment.template.js* verdiğiniz bilgileri alır ve yeni bir dağıtım bildirim dosyası oluşturmak için onu kullanır. Dağıtım bildirimi, çözüm çalışma alanınızdaki yeni bir **yapılandırma** klasöründe oluşturulur. Bu dosyayı aldıktan sonra, [Azure clı 2,0 ile](how-to-deploy-modules-cli.md) [Visual Studio Code Azure IoT Edge modülleri dağıtma](how-to-deploy-modules-vscode.md) veya Azure IoT Edge modülleri Dağıtma bölümündeki adımları izleyebilirsiniz.

## <a name="deploy-multiple-module-instances"></a>Birden çok modül örneği dağıtma

IoT Edge modülünde Azure Blob Storage 'ın birden fazla örneğini dağıtmak istiyorsanız, farklı bir depolama yolu sağlamanız ve `HostPort` modülün bağlandığı değeri değiştirmeniz gerekir. BLOB depolama modülleri her zaman kapsayıcıda 11002 numaralı bağlantı noktasını kullanıma sunar, ancak konakta hangi bağlantı noktasını bağladığına ilişkin bildirimi de belirtebilirsiniz.

Değeri değiştirmek için **kapsayıcı oluşturma seçeneklerini** (Azure Portal) veya **createoptions** alanını (Visual Studio Code içindeki *deployment.template.js* dosyasında) düzenleyin `HostPort` :

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Ek BLOB depolama modüllerine bağlandığınızda, uç noktayı güncelleştirilmiş konak bağlantı noktasını işaret etmek üzere değiştirin.

## <a name="configure-proxy-support"></a>Proxy desteğini yapılandırma

Kuruluşunuz bir ara sunucu kullanıyorsa, edgeAgent ve edgeHub çalışma zamanı modülleri için ara sunucu desteğini yapılandırmanız gerekecektir. Bu işlem iki görevi içerir:

- Çalışma zamanı Daemon 'ları ve cihazdaki IoT Edge aracısını yapılandırın.
- Dağıtım bildirimi JSON dosyasındaki modüller için HTTPS_PROXY ortam değişkenini ayarlayın.

Bu işlem, [bir proxy sunucusu üzerinden iletişim kurmak için IoT Edge cihazı yapılandırma](how-to-configure-proxy-support.md)bölümünde açıklanmaktadır.

Ayrıca, bir BLOB depolama modülü Ayrıca bildirim dağıtım dosyasında HTTPS_PROXY ayarı gerektirir. Dağıtım bildirimi dosyasını doğrudan düzenleyebilir veya Azure portal kullanabilirsiniz.

1. Azure portal IoT Hub 'ınıza gidin ve sol bölmedeki menüden **IoT Edge** ' i seçin.

1. Yapılandırılacak modülün bulunduğu cihazı seçin.

1. **Modülleri Ayarlama**'yı seçin.

1. Sayfanın **IoT Edge modüller** bölümünde blob Storage modülünü seçin.

1. **IoT Edge modülünü Güncelleştir** sayfasında, **ortam değişkenleri** sekmesini seçin.

1. `HTTPS_PROXY` **Değer** için **ad** ve proxy URL 'si ekleyin.

      ![Ekran görüntüsü, belirtilen değerleri girebileceğiniz g. u uç modülünü Güncelleştir bölmesini gösterir.](./media/how-to-deploy-blob/https-proxy-config.png)

1. **Güncelleştir**' e ve ardından **+ Oluştur**' a tıklayın.

1. Proxy 'nin dağıtım bildiriminde modüle eklendiğini ve **Oluştur**' u seçmesini unutmayın.

1. Cihaz ayrıntıları sayfasından modülü seçerek ve **IoT Edge modülleri ayrıntıları** sayfasının alt bölümünde **ortam değişkenleri** sekmesini seçerek ayarı doğrulayın.

      ![Ekran görüntüsü ortam değişkenleri sekmesini gösterir.](./media/how-to-deploy-blob/verify-proxy-config.png)

## <a name="next-steps"></a>Sonraki adımlar

[IoT Edge üzerinde Azure Blob depolama](how-to-store-data-blob.md)hakkında daha fazla bilgi edinin.

Dağıtım bildirimlerinin nasıl çalıştığı ve nasıl oluşturulacağı hakkında daha fazla bilgi için bkz. [IoT Edge modüllerinin nasıl kullanılabileceğini, yapılandırılacağını ve yeniden kullanıldığını anlayın](module-composition.md).
