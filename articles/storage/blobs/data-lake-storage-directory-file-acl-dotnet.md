---
title: Azure Data Lake Storage 2. verileri yönetmek için .NET kullanın
description: Hiyerarşik ad alanı etkinleştirilmiş depolama hesaplarındaki dizinleri ve dosyaları yönetmek için .NET için Azure Storage istemci kitaplığı 'nı kullanın.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 52e993a22a512a94c8b5b8b050205db0c4ce0b1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100650329"
---
# <a name="use-net-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. içindeki dizinleri ve dosyaları yönetmek için .NET kullanın

Bu makalede, .NET kullanarak hiyerarşik bir ad alanı olan depolama hesaplarında dizin ve dosya oluşturma ve bunları yönetme işlemlerinin nasıl yapılacağı gösterilmektedir.

Dizinlerin ve dosyaların erişim denetim listelerini (ACL) alma, ayarlama ve güncelleştirme hakkında bilgi edinmek için bkz. [Azure Data Lake Storage 2. ACL 'leri yönetmek için .NET kullanma](data-lake-storage-acl-dotnet.md).

[Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  [Örnekler](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  [API başvurusu](/dotnet/api/azure.storage.files.datalake)  |  [Gen1 to Gen2 Mapping](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [Geri bildirimde](https://github.com/Azure/azure-sdk-for-net/issues) bulunun

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).

- Hiyerarşik ad alanı etkin olan bir depolama hesabı. Bir tane oluşturmak için [Bu](create-data-lake-storage-account.md) yönergeleri izleyin.

## <a name="set-up-your-project"></a>Projenizi ayarlama

Başlamak için [Azure. Storage. Files. DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet paketini yükledikten sonra.

NuGet paketlerinin nasıl yükleneceğine ilişkin daha fazla bilgi için bkz. [NuGet Paket Yöneticisi 'ni kullanarak Visual Studio 'da paketleri yüklemek ve yönetmek](/nuget/consume-packages/install-use-packages-visual-studio).

Ardından, bu using deyimlerini kod dosyanızın en üstüne ekleyin.

```csharp
using Azure;
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;

```

## <a name="connect-to-the-account"></a>Hesaba Bağlan

Bu makaledeki kod parçacıklarını kullanmak için depolama hesabını temsil eden bir [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) örneği oluşturmanız gerekir. 

### <a name="connect-by-using-an-account-key"></a>Hesap anahtarı kullanarak bağlan

Bu, bir hesaba bağlanmanın en kolay yoludur. 

Bu örnek, bir hesap anahtarı kullanarak bir [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) örneği oluşturur.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Azure Active Directory kullanarak bağlanma (Azure AD)

Azure AD ile uygulamanızın kimliğini doğrulamak için [.net Için Azure Identity istemci kitaplığı](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) 'nı kullanabilirsiniz.

Bu örnek, bir istemci KIMLIĞI, bir istemci parolası ve bir kiracı KIMLIĞI kullanarak bir [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) örneği oluşturur.  Bu değerleri almak için bkz. [bir istemci uygulamasından istekleri yetkilendirmek Için Azure AD 'den belirteç alma](../common/storage-auth-aad-app.md).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Daha fazla örnek için bkz. [.net Için Azure kimlik istemci kitaplığı](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) belgeleri.

## <a name="create-a-container"></a>Kapsayıcı oluşturma

Bir kapsayıcı dosyalarınız için bir dosya sistemi görevi görür. [DataLakeServiceClient. CreateFileSystem](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync) metodunu çağırarak bir tane oluşturabilirsiniz.

Bu örnek adlı bir kapsayıcı oluşturur `my-file-system` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>Dizin oluşturma

[Datalakefilesystemclient. CreateDirectoryAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync) yöntemini çağırarak bir dizin başvurusu oluşturun.

Bu örnek, bir kapsayıcıya adlı bir dizin ekler `my-directory` ve sonra adlı bir alt dizin ekler `my-subdirectory` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Bir dizini yeniden adlandırma veya taşıma

[Datalakedirectoryclient. RenameAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) yöntemini çağırarak bir dizini yeniden adlandırın veya taşıyın. İstenen dizinin yolunu bir parametre olarak geçirin. 

Bu örnek, bir alt dizini ada yeniden adlandırır `my-subdirectory-renamed` .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_RenameDirectory":::

Bu örnek adlı dizini adlı bir `my-subdirectory-renamed` dizinin alt dizinine taşır `my-directory-2` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>Bir dizini silme

[Datalakedirectoryclient. Delete](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete) yöntemini çağırarak bir dizini silin.

Bu örnek adlı bir dizini siler `my-directory` .  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Dizine dosya yükleme

İlk olarak, [Datalakefileclient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) sınıfının bir örneğini oluşturarak hedef dizinde bir dosya başvurusu oluşturun. [Datalakefileclient. Appendadsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) yöntemini çağırarak bir dosyayı karşıya yükleyin. [Datalakefileclient. FlushAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) yöntemini çağırarak karşıya yüklemeyi tamamladığınızdan emin olun.

Bu örnek, adlı bir dizine bir metin dosyası yükler `my-directory` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFile":::

> [!TIP]
> Dosya boyutunuz büyükse, kodunuzun [Datalakefileclient. Appendadsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync)öğesine birden çok çağrı yapması gerekir. Bunun yerine [Datalakefileclient. UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) yöntemini kullanmayı düşünün. Bu şekilde, tüm dosyayı tek bir çağrıda karşıya yükleyebilirsiniz.
>
> Bir örnek için sonraki bölüme bakın.

## <a name="upload-a-large-file-to-a-directory"></a>Bir dizine büyük bir dosya yükleme

[Datalakefileclient. Appendadsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) yöntemine birden çok çağrı yapmak zorunda kalmadan büyük dosyaları karşıya yüklemek Için [Datalakefileclient. uploadasync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) yöntemini kullanın.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Bir dizinden indir 

İlk olarak, indirmek istediğiniz dosyayı temsil eden bir [Datalakefileclient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) örneği oluşturun. [Datalakefileclient. ReadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) yöntemini kullanın ve bir [Stream](/dotnet/api/system.io.stream) nesnesi elde etmek için dönüş değerini ayrıştırın. Akıştan bir dosyaya bayt kaydetmek için herhangi bir .NET dosya işleme API 'sini kullanın. 

Bu örnek, bir dosyaya bayt kaydetmek için bir [BinaryReader](/dotnet/api/system.io.binaryreader) ve [FILESTREAM](/dotnet/api/system.io.filestream) kullanır. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DownloadBinaryFromDirectory":::

## <a name="list-directory-contents"></a>Dizin içeriğini listeleme

[Filesystemclient. GetPathsAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) yöntemini çağırarak ve sonra sonuçlar arasında sıralama yaparak dizin içeriğini listeleyin.

Bu örnek, adlı bir dizinde bulunan her bir dosyanın adını yazdırır `my-directory` .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Ayrıca bkz.

- [API başvuru belgeleri](/dotnet/api/azure.storage.files.datalake)
- [Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [Örnekler](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Gen1 to Gen2 Mapping](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [Bilinen sorunlar](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Geri bildirimde bulunun](https://github.com/Azure/azure-sdk-for-net/issues)