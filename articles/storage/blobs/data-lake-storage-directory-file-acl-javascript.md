---
title: Azure Data Lake Storage 2. verileri yönetmek için JavaScript (Node.js) kullanın
description: Hiyerarşik ad alanı etkinleştirilmiş depolama hesaplarındaki dizinleri ve dosyaları yönetmek için, JavaScript için Azure Storage Data Lake istemci kitaplığı 'nı kullanın.
author: normesta
ms.service: storage
ms.date: 03/19/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: 678af3e2fb4111593ece0cc2cdf3811cf0e793a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774771"
---
# <a name="use-javascript-sdk-in-nodejs-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Node.js Azure Data Lake Storage 2. dizinleri ve dosyaları yönetmek için JavaScript SDK 'sını kullanın

Bu makalede, hiyerarşik bir ad alanına sahip depolama hesaplarında dizin ve dosya oluşturmak ve yönetmek için Node.js nasıl kullanılacağı gösterilmektedir.

Dizinlerin ve dosyaların erişim denetim listelerini (ACL) alma, ayarlama ve güncelleştirme hakkında bilgi edinmek için, [Azure Data Lake Storage 2. ACL 'leri yönetmek üzere Node.js Içindeki JavaScript SDK 'Sını kullanma](data-lake-storage-acl-javascript.md)konusuna bakın.

[Paket (düğüm Paket Yöneticisi)](https://www.npmjs.com/package/@azure/storage-file-datalake)  |  [Örnekler](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)  |  [Geri bildirimde](https://github.com/Azure/azure-sdk-for-java/issues) bulunun

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).

- Hiyerarşik ad alanı etkin olan bir depolama hesabı. Bir tane oluşturmak için [Bu](create-data-lake-storage-account.md) yönergeleri izleyin.

- Bu paketi bir Node.js uygulamasında kullanıyorsanız, Node.js 8.0.0 veya daha yüksek bir sürüme sahip olmanız gerekir.

## <a name="set-up-your-project"></a>Projenizi ayarlama

Bir Terminal penceresi açarak ve ardından aşağıdaki komutu yazarak JavaScript için Data Lake istemci kitaplığı 'nı yüklersiniz.

```javascript
npm install @azure/storage-file-datalake
```

`storage-file-datalake`Bu ifadeyi kod dosyanızın en üstüne yerleştirerek paketi içeri aktarın. 

```javascript
const {
AzureStorageDataLake,
DataLakeServiceClient,
StorageSharedKeyCredential
} = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Hesaba Bağlan 

Bu makaledeki kod parçacıklarını kullanmak için depolama hesabını temsil eden bir **DataLakeServiceClient** örneği oluşturmanız gerekir. 

### <a name="connect-by-using-an-account-key"></a>Hesap anahtarı kullanarak bağlan

Bu, bir hesaba bağlanmanın en kolay yoludur. 

Bu örnek, bir hesap anahtarı kullanarak bir **DataLakeServiceClient** örneği oluşturur.

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```

> [!NOTE]
> Bu yetkilendirme yöntemi yalnızca Node.js uygulamalar için geçerlidir. Kodunuzu bir tarayıcıda çalıştırmayı planlıyorsanız, Azure Active Directory (Azure AD) kullanarak yetkilendirme yapabilirsiniz.

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Azure Active Directory kullanarak bağlanma (Azure AD)

Uygulamanızın kimliğini Azure AD ile doğrulamak için [Azure Identity Client Library for js](https://www.npmjs.com/package/@azure/identity) ' i kullanabilirsiniz.

Bu örnek, bir istemci KIMLIĞI, bir istemci parolası ve bir kiracı KIMLIĞI kullanarak bir **DataLakeServiceClient** örneği oluşturur.  Bu değerleri almak için bkz. [bir istemci uygulamasından istekleri yetkilendirmek Için Azure AD 'den belirteç alma](../common/storage-auth-aad-app.md).

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> Daha fazla örnek için bkz. [js Için Azure kimlik istemci kitaplığı](https://www.npmjs.com/package/@azure/identity) belgeleri.

## <a name="create-a-container"></a>Kapsayıcı oluşturma

Bir kapsayıcı dosyalarınız için bir dosya sistemi görevi görür. Bir **filesystemclient** örneği alarak ve sonra **Filesystemclient. Create** metodunu çağırarak bir tane oluşturabilirsiniz.

Bu örnek adlı bir kapsayıcı oluşturur `my-file-system` . 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Dizin oluşturma

Bir **directoryclient** örneği alarak ve sonra **directoryclient. Create** metodunu çağırarak bir dizin başvurusu oluşturun.

Bu örnek, bir kapsayıcıya adlı bir dizin ekler `my-directory` . 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Bir dizini yeniden adlandırma veya taşıma

**Directoryclient. Rename** metodunu çağırarak bir dizini yeniden adlandırın veya taşıyın. İstenen dizinin yolunu bir parametre olarak geçirin. 

Bu örnek, bir alt dizini ada yeniden adlandırır `my-directory-renamed` .

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

Bu örnek adlı dizini adlı bir `my-directory-renamed` dizinin alt dizinine taşır `my-directory-2` . 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Bir dizini silme

**Directoryclient. Delete** yöntemini çağırarak bir dizini silin.

Bu örnek adlı bir dizini siler `my-directory` .   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="upload-a-file-to-a-directory"></a>Dizine dosya yükleme

İlk olarak bir dosya okuyun. Bu örnek Node.js modülünü kullanır `fs` . Ardından, bir **fileclient** örneği oluşturup, sonra **fileclient. Create** metodunu çağırarak hedef dizinde bir dosya başvurusu oluşturun. **Fileclient. Append** metodunu çağırarak bir dosyayı karşıya yükleyin. **Fileclient. Flush** yöntemini çağırarak karşıya yüklemeyi tamamladığınızdan emin olun.

Bu örnek, bir metin dosyasını adlı bir dizine yükler `my-directory` . '

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="download-from-a-directory"></a>Bir dizinden indir

İlk olarak, indirmek istediğiniz dosyayı temsil eden bir **Filesystemclient** örneği oluşturun. Dosyayı okumak için **Filesystemclient. Read** metodunu kullanın. Sonra dosyayı yazın. Bu örnek `fs` bunu yapmak için Node.js modülünü kullanır. 

> [!NOTE]
> Dosya indirme yöntemi yalnızca Node.js uygulamalar için geçerlidir. Kodunuzu bir tarayıcıda çalıştırmayı planlıyorsanız, bunu bir tarayıcıda nasıl yapacağınızı gösteren bir örnek için bkz. [JavaScript için istemci kitaplığı Data Lake Azure Storage dosyası](https://www.npmjs.com/package/@azure/storage-file-datalake) .

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }   
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>Dizin içeriğini listeleme

Bu örnek, adlı bir dizinde bulunan her bir dizin ve dosyanın adlarını yazdırır `my-directory` .

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>Ayrıca bkz.

- [Paket (düğüm Paket Yöneticisi)](https://www.npmjs.com/package/@azure/storage-file-datalake)
- [Örnekler](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
- [Geri bildirimde bulunun](https://github.com/Azure/azure-sdk-for-java/issues)