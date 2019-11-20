---
title: Cihazlarda blok bloblarını depolama-Azure IoT Edge | Microsoft Docs
description: Katmanlama ve yaşam süresi özelliklerini anlayın, bkz. desteklenen blob Storage işlemleri ve BLOB depolama hesabınıza bağlanma.
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: arduppal
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 55edc69e706fad8888146e9d97541a1c2bae821d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163806"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>IoT Edge Azure Blob Storage ile verileri kenarda depolayın

IoT Edge 'de Azure Blob depolama, uç noktada bir [Blok Blobu](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) depolama çözümü sağlar. IoT Edge cihazınızdaki BLOB depolama modülü bir Azure Blok Blobu hizmeti gibi davranır, ancak blok Blobları IoT Edge cihazınızda yerel olarak depolanır. Bloblarınıza aynı Azure Storage SDK yöntemlerini veya zaten kullandığınız blob API çağrılarını kullanarak erişebilirsiniz. Bu makalede, IoT Edge cihazınızda bir blob hizmeti çalıştıran IoT Edge kapsayıcısında Azure Blob depolama ile ilgili kavramlar açıklanmaktadır.

Bu modül senaryolarda yararlı olur:
* verilerin işlenebilmesi veya buluta aktarılıncaya kadar yerel olarak depolanması gereken durumlar. Bu veriler, videolar, resimler, Finans verileri, barındırma verileri veya diğer yapılandırılmamış veriler olabilir.
* cihazlar sınırlı bağlantı içeren bir yerde bulunduğunda.
* verilere düşük gecikme süreli erişim sağlamak için verileri yerel olarak işlemek istediğinizde, mümkün olduğunca hızlı bir şekilde yanıt verebilirseniz.
* bant genişliği maliyetlerini azaltmak istediğinizde ve terabayt verisi buluta aktarılmaktan kaçının. Verileri yerel olarak işleyebilir ve yalnızca işlenen verileri buluta gönderebilirsiniz.

Hızlı giriş için videoyu izleyin
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

Bu modül **Devicetocloudupload** ve **deviceoto Delete** özellikleriyle birlikte gelir.

**Devicetocloudupload** , yapılandırılabilir bir işlevdir. Bu işlev, zaman aralıklı internet bağlantısı desteğiyle, verileri yerel blob depolamadan Azure 'a otomatik olarak yükler. Şunları yapmanıza olanak sağlar:

- DeviceToCloudUpload özelliğini açın/kapatın.
- Verilerin NewestFirst veya OldestFirst gibi Azure 'a kopyalandığı sırayı seçin.
- Verilerinizin karşıya yüklenmesini istediğiniz Azure Depolama hesabını belirtin.
- Azure 'a yüklemek istediğiniz kapsayıcıları belirtin. Bu modül hem kaynak hem de hedef kapsayıcı adlarını belirtmenize olanak tanır.
- Bulut depolamaya yükleme tamamlandıktan sonra Blobları hemen silme özelliğini seçin
- Tam blob yüklemesi yapın (`Put Blob` işlemi kullanarak) ve blok düzeyinde karşıya yükleme (`Put Block` ve `Put Block List` işlemlerini kullanarak).

Bu modül Blobun blokları içeriyorsa blok düzeyinde karşıya yükleme kullanır. Yaygın senaryolardan bazıları şunlardır:

- Uygulamanız, daha önce karşıya yüklenen bir Blobun bazı blokları güncelleştirir, bu modül tüm blobu değil yalnızca güncelleştirilmiş blokları karşıya yükler.
- Modül, blob 'u karşıya yüklüyor ve internet bağlantısı geri geldiğinde, bağlantı tekrar tekrar olduğunda blob tamamını değil yalnızca kalan blokları karşıya yükler.

Blob karşıya yükleme sırasında beklenmeyen bir işlem sonlandırması (güç hatası gibi) olursa, modül yeniden çevrimiçi olduktan sonra karşıya yükleme işlemi sırasında oluşan tüm bloklar tekrar karşıya yüklenir.

**Deviceoto silme** , yapılandırılabilir bir işlevdir. Bu işlev, belirtilen süre (dakika cinsinden ölçülür) sona erdiğinde bloblarınızı yerel depolamadan otomatik olarak siler. Şunları yapmanıza olanak sağlar:

- Deviceoto Delete özelliğini açın/kapatın.
- Blobların otomatik olarak silineceği süreyi dakika cinsinden (Deleteafsonlandırutes) belirtin.
- Deleteafsonlandırutes değerinin süresi dolarsa blobu karşıya yüklerken bekletme özelliğini seçin.


## <a name="prerequisites"></a>Önkoşullar

Bir Azure IoT Edge cihazı:

- [Linux](quickstart-linux.md) veya [Windows cihazları](quickstart.md)için Hızlı Başlangıç bölümündeki adımları izleyerek geliştirme makinenizi veya bir sanal makineyi IoT Edge bir cihaz olarak kullanabilirsiniz.

- Desteklenen işletim sistemleri ve mimarilerin bir listesi için [desteklenen Azure IoT Edge sistemleri](support.md#operating-systems) bölümüne bakın. IoT Edge modülündeki Azure Blob depolama, aşağıdaki mimarilere destek sunar:
    - Windows AMD64
    - Linux AMD64
    - Linux ARM32
    - Linux ARM64 (Önizleme)

Bulut kaynakları:

Azure'da standart katman [IoT Hub'ı](../iot-hub/iot-hub-create-through-portal.md).

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>deviceToCloudUpload ve Deviceoto Delete özellikleri

**Devicetoclouduploadproperties** ve **deviceoto deleteproperties**ayarlamak için modülün istenen özelliklerini kullanın. İstenen özellikler dağıtım sırasında ayarlanabilir veya daha sonra yeniden dağıtmak zorunda kalmadan modül ikizi düzenlenerek değiştirilebilir. Değerlerin doğru şekilde yayıldığından emin olmak için, `reported configuration` ve `configurationValidation` için "Module Ikizi" denetimini öneririz.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

Bu ayarın adı `deviceToCloudUploadProperties`. IoT Edge simülatör kullanıyorsanız, bu özellikler için değerleri, Açıklama bölümünde bulabileceğiniz ilgili ortam değişkenlerine ayarlayın.

| Özellik | Olası Değerler | Açıklama |
| ----- | ----- | ---- |
| uploadOn | doğru, yanlış | Varsayılan olarak `false` olarak ayarlayın. Özelliği etkinleştirmek istiyorsanız, bu alanı `true`olarak ayarlayın. <br><br> Ortam değişkeni: `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | Verilerin Azure 'a kopyalandığı sırayı seçmenizi sağlar. Varsayılan olarak `OldestFirst` olarak ayarlayın. Sıra, blob 'un son değiştirilme zamanına göre belirlenir. <br><br> Ortam değişkeni: `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`, verilerinizin karşıya yüklenmesini istediğiniz depolama hesabını belirtmenize olanak tanıyan bir bağlantı dizesidir. `Azure Storage Account Name`, `Azure Storage Account Key``End point suffix`belirtin. Verilerin karşıya yükleneceği Azure 'un uygun EndpointSuffix öğesini ekleyin; küresel Azure, kamu Azure ve Microsoft Azure Stack değişir. <br><br> Burada Azure Storage SAS bağlantı dizesini belirtmeyi seçebilirsiniz. Ancak bu özelliği süresi dolmuşsa güncelleştirmeniz gerekir. <br><br> Ortam değişkeni: `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Azure 'a yüklemek istediğiniz kapsayıcı adlarını belirtmenize olanak tanır. Bu modül hem kaynak hem de hedef kapsayıcı adlarını belirtmenize olanak tanır. Hedef kapsayıcı adını belirtmezseniz, kapsayıcı adı otomatik olarak `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`olarak atanır. Hedef kapsayıcı adı için şablon dizeleri oluşturabilir, olası değerler sütununa bakabilirsiniz. <br>*% h-> IoT Hub adı (3-50 karakter). <br>*% d-> IoT Edge cihaz KIMLIĞI (1-129 karakter). <br>*% d-> Modül adı (1-64 karakter). <br>*% c-> kaynak kapsayıcısı adı (3 ile 63 karakter arasında). <br><br>Kapsayıcı adının en büyük boyutu 63 karakterdir, çünkü kapsayıcının boyutu 63 karakteri aşarsa, her bir bölümü (IoTHubName, ıotedgedeviceıd, ModuleName, SourceContainerName) 15 olarak kırpacaktır karakterle. <br><br> Ortam değişkeni: `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| deleteAfterUpload | doğru, yanlış | Varsayılan olarak `false` olarak ayarlayın. `true`olarak ayarlandığında, bulut depolamaya yükleme tamamlandığında verileri otomatik olarak siler. <br><br> Ortam değişkeni: `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` |


### <a name="deviceautodeleteproperties"></a>Deviceoto Deleteproperties

Bu ayarın adı `deviceAutoDeleteProperties`. IoT Edge simülatör kullanıyorsanız, bu özellikler için değerleri, Açıklama bölümünde bulabileceğiniz ilgili ortam değişkenlerine ayarlayın.

| Özellik | Olası Değerler | Açıklama |
| ----- | ----- | ---- |
| deleteOn | doğru, yanlış | Varsayılan olarak `false` olarak ayarlayın. Özelliği etkinleştirmek istiyorsanız, bu alanı `true`olarak ayarlayın. <br><br> Ortam değişkeni: `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Süreyi dakika olarak belirtin. Bu değerin süresi dolarsa modül yerel depolamadan Blobları otomatik olarak siler. <br><br> Ortam değişkeni: `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | doğru, yanlış | Varsayılan olarak, `true`olarak ayarlanır ve Deleteafsonlandırutes süresi dolarsa blob 'u bulut depolamaya yüklerken korur. `false` olarak ayarlayabilirsiniz ve Deleteafsonlandırutes süresi dolduktan hemen sonra verileri siler. Note: Bu özelliğin iş için uploadOn, true olarak ayarlanmalıdır. <br><br> Ortam değişkeni: `deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>Yerel depolama alanı olarak SMB paylaşımının kullanımı
Windows konakta Bu modülün Windows kapsayıcısını dağıtırken, yerel depolama yolunuz olarak SMB paylaşma sağlayabilirsiniz.

SMB paylaşımının ve IoT cihazının karşılıklı güvenilen etki alanlarında bulunduğundan emin olun.

Windows çalıştıran IoT cihazında SMB paylaşımından yerel olarak eşleme yapmak için `New-SmbGlobalMapping` PowerShell komutunu çalıştırabilirsiniz.

Yapılandırma adımları aşağıda verilmiştir:
```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```
Örnek: <br>
`$creds = Get-Credential` <br>
`New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G: `

Bu komut, uzak SMB sunucusunda kimlik doğrulamak için kimlik bilgilerini kullanır. Ardından, uzak paylaşımın yolunu G: sürücü harfi ile eşleyin (kullanılabilir başka herhangi bir sürücü harfi olabilir). IoT cihazında artık G: sürücüsündeki bir yola eşlenen veri hacmi vardır. 

IoT cihazındaki kullanıcının uzak SMB paylaşımında okuma/yazma yapaabilmesini sağlayın.

Dağıtımınız için `<storage mount>` değeri **G:/ContainerData: C:/BlobRoot**olabilir. 

## <a name="granting-directory-access-to-container-user-on-linux"></a>Linux 'ta kapsayıcı kullanıcısına dizin erişimi verme
Linux kapsayıcıları için oluşturma seçeneklerinizde depolama için [birim bağlama](https://docs.docker.com/storage/volumes/) kullandıysanız, ek adımlar yapmanız gerekmez, ancak [bağlama bağlama](https://docs.docker.com/storage/bind-mounts/) kullandıysanız, hizmeti doğru bir şekilde çalıştırmak için bu adımlar gereklidir.

Kullanıcıların, çalışmalarını gerçekleştirmesi gereken minimum izinleri en düşük izinlerle sınırlamak için en az ayrıcalık ilkesinin ardından, bu modül bir Kullanıcı (ad: absıl, ID: 11000) ve bir Kullanıcı grubu (ad: Abe, KIMLIK: 11000) içerir. Kapsayıcı **kök** olarak başlatılırsa (varsayılan kullanıcı **kök**ise) hizmetimiz **düşük ayrıcalıklı bir** Kullanıcı olarak başlatılır. 

Bu davranış, hizmetin düzgün çalışması için konak yolu izinlerin yapılandırılmasını sağlar, aksi takdirde hizmet erişim reddedildi hatalarıyla çöker. Dizin bağlamasında kullanılan yolun kapsayıcı kullanıcı tarafından erişilebilir olması gerekir (örnek: absı1 11000). Konakta aşağıdaki komutları yürüterek kapsayıcı kullanıcıya dizin erişimi verebilirsiniz:

```terminal
sudo chown -R 11000:11000 <blob-dir> 
sudo chmod -R 700 <blob-dir> 
```

Örnek:<br>
`sudo chown -R 11000:11000 /srv/containerdata` <br>
`sudo chmod -R 700 /srv/containerdata `


Hizmeti, bir kullanıcı olarak bir kullanıcı olarak çalıştırmanız **gerekiyorsa, dağıtım**bildiriminizde "Kullanıcı" özelliği altındaki createOptions içinde özel kullanıcı kimliğinizi belirtebilirsiniz. Böyle bir durumda, varsayılan veya kök Grup KIMLIĞI `0`kullanmanız gerekir.

```json
"createOptions": { 
  "User": "<custom user ID>:0" 
} 
```
Şimdi, kapsayıcı kullanıcısına dizine erişim izni verin
```terminal
sudo chown -R <user ID>:<group ID> <blob-dir> 
sudo chmod -R 700 <blob-dir> 
```

## <a name="configure-log-files"></a>Günlük dosyalarını yapılandırma

Modülünüzün günlük dosyalarını yapılandırma hakkında daha fazla bilgi için, bu [üretim en iyi uygulamalarına](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics)bakın.

## <a name="connect-to-your-blob-storage-module"></a>BLOB depolama modülünüzü bağlama

IoT Edge cihazınızda blob depolamaya erişmek için modülünüzün yapılandırıldığı hesap adını ve hesap anahtarını kullanabilirsiniz.

IoT Edge cihazınızı, üzerinde yaptığınız herhangi bir depolama isteği için blob uç noktası olarak belirtin. IoT Edge cihaz bilgilerini ve yapılandırdığınız hesap adını kullanarak [açık depolama uç noktası için bir bağlantı dizesi oluşturabilirsiniz](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) .

- IoT Edge modülündeki Azure Blob depolama alanının çalıştığı aynı cihaza dağıtılan modüller için, blob uç noktası: `http://<module name>:11002/<account name>`.
- Farklı bir cihazda çalışan modüller veya uygulamalar için ağınız için doğru uç noktayı seçmeniz gerekir. Ağ kurulumunuza bağlı olarak, dış modülünüzün veya uygulamanızdan alınan veri trafiğinin IoT Edge modülünde Azure Blob depolamayı çalıştıran cihaza ulaşabilmesi için bir uç nokta biçimi seçin. Bu senaryonun blob uç noktası şunlardan biridir:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob depolama hızlı başlangıç örnekleri

Azure Blob depolama belgeleri, birkaç dilde hızlı başlangıç örnek kodunu içerir. Blob uç noktasını yerel BLOB depolama modülünüzün bağlanacağı şekilde değiştirerek, IoT Edge Azure Blob depolamayı test etmek için bu örnekleri çalıştırabilirsiniz.

Aşağıdaki hızlı başlangıç örnekleri, IoT Edge tarafından da desteklenen dilleri kullanır, bu nedenle bunları BLOB depolama modülünün yanı sıra IoT Edge modüller olarak dağıtabilirsiniz:

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
    - Modülün bu sürümü blob oluşturma süresi döndürmediğinden Bu SDK kullanılırken bilinen bir sorunla karşılaştık. Bu nedenle, liste Blobları gibi birkaç yöntem çalışmıyor. Geçici bir çözüm olarak, blob istemcisinde açıkça API sürümünü ' 2017-04-17 ' olarak ayarladı. <br>Örnek: `block_blob_service._X_MS_VERSION = '2017-04-17'`
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-v10.md)
- [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-v10.md)
- [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
- [Go](../storage/blobs/storage-quickstart-blobs-go.md)
- [PHP](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Azure Depolama Gezgini ile yerel depolamaya bağlanma

[Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) , yerel depolama hesabınıza bağlanmak için kullanabilirsiniz.

1. Azure Depolama Gezgini indir ve yükle

1. Bağlantı dizesi kullanarak Azure depolama 'ya bağlanma

1. Bağlantı dizesi girin: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Bağlanmak için adımları izleyin.

1. Yerel depolama hesabınız içinde kapsayıcı oluşturma

1. Dosya yüklemeyi blok Bloblar olarak başlatın.
   > [!NOTE]
   > Bu modül, sayfa bloblarını desteklemez.

1. Azure depolama hesaplarınızı Depolama Gezgini, aynı şekilde bağlamayı seçebilirsiniz. Bu yapılandırma, hem yerel depolama hesabınız hem de Azure Storage hesabınız için tek bir görünüm sağlar

## <a name="supported-storage-operations"></a>Desteklenen depolama işlemleri

IoT Edge BLOB depolama modülleri Azure Storage SDK 'larını kullanır ve Blok Blobu uç noktaları için Azure Storage API 'sinin 2017-04-17 sürümüyle tutarlıdır. 

Tüm Azure Blob depolama işlemleri IoT Edge Azure Blob depolama tarafından desteklenmediğinden, bu bölümde her birinin durumu listelenir.

### <a name="account"></a>Hesap

Destek

- Kapsayıcıları listeleme

Desteklenen

- Blob hizmeti özelliklerini al ve ayarla
- Ön kontrol blobu isteği
- Blob hizmeti istatistiklerini al
- Hesap bilgilerini al

### <a name="containers"></a>Kapsayıcılar

Destek

- Kapsayıcı oluştur ve Sil
- Kapsayıcı özelliklerini ve meta verileri al
- Blobları listeleme
- Kapsayıcı ACL 'sini al ve ayarla
- Kapsayıcı meta verilerini ayarla

Desteklenen

- Kira kapsayıcısı

### <a name="blobs"></a>Bloblar

Destek

- Blobu yerleştirme, edinme ve silme
- Blob özelliklerini al ve ayarla
- Blob meta verilerini al ve ayarla

Desteklenen

- Kira blobu
- Anlık görüntü blobu
- Kopyalama blobu Kopyala ve durdur
- Blobu geri al
- Blob katmanını ayarla

### <a name="block-blobs"></a>Blok blobları

Destek

- Yerleştirme bloğu
- Yerleştirme ve engelleme listesini al

Desteklenen

- Bloğu URL 'den koy

## <a name="event-grid-on-iot-edge-integration"></a>IoT Edge tümleştirmede Event Grid
> [!CAUTION]
> IoT Edge Event Grid tümleştirme önizlemededir

IoT Edge modülündeki bu Azure Blob depolama alanı, IoT Edge Event Grid ile tümleştirme sağlar. Bu tümleştirmeyle ilgili ayrıntılı bilgi için, [modülleri dağıtma, olayları yayımlama ve olay teslimini doğrulama hakkında öğreticiye](../event-grid/edge/react-blob-storage-events-locally.md)bakın.

## <a name="release-notes"></a>Sürüm Notları

Bu modül için [Docker Hub 'daki sürüm notları](https://hub.docker.com/_/microsoft-azure-blob-storage) aşağıda verilmiştir

## <a name="feedback"></a>Geri Bildirim

Bu modülün ve özelliklerinin kullanışlı ve kullanımı kolay olması için geri bildiriminiz bizim için önemlidir. Lütfen geri bildiriminizi paylaşabilir ve nasıl iyileştirebileceğimizi bize bildirin.

absiotfeedback@microsoft.com adresinden bize ulaşabilirsiniz

## <a name="next-steps"></a>Sonraki adımlar

[IoT Edge Azure Blob Storage 'ı dağıtmayı](how-to-deploy-blob.md) öğrenin

[IoT Edge blogundaki Azure Blob depolamada](https://aka.ms/abs-iot-blogpost) en son güncelleştirmeler ve duyuru ile güncel kalın
