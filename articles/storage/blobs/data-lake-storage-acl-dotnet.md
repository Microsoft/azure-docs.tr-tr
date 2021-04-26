---
title: Azure Data Lake Storage 2. ACL 'Leri ayarlamak için .NET kullanın
description: Hiyerarşik ad alanı (HNS) etkin olan depolama hesaplarında erişim denetim listelerini (ACL) yönetmek için .NET kullanın.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 626e89d8d758d5fe31ef6c913076a9154274bb61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654480"
---
# <a name="use-net-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. ACL 'Leri yönetmek için .NET kullanın

Bu makalede, dizin ve dosyaların erişim denetim listelerini almak, ayarlamak ve güncelleştirmek için .NET kullanımı gösterilmektedir.

ACL devralma, bir üst dizin altında oluşturulan yeni alt öğeler için zaten kullanılabilir. Ancak, bu değişiklikleri her alt öğe için ayrı ayrı yapmak zorunda kalmadan ACL 'Leri bir üst dizinin varolan alt öğelerine yinelemeli olarak ekleyebilir, güncelleştirebilir ve kaldırabilirsiniz.

[Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  [Örnekler](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  [ÖZYINELEMELI ACL örneği](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)  |  [API başvurusu](/dotnet/api/azure.storage.files.datalake)  |  [Gen1 to Gen2 Mapping](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [Geri bildirimde](https://github.com/Azure/azure-sdk-for-net/issues) bulunun

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).

- Hiyerarşik ad alanı (HNS) etkin olan bir depolama hesabı. Bir tane oluşturmak için [Bu](create-data-lake-storage-account.md) yönergeleri izleyin.

- Azure CLı sürümü `2.6.0` veya üzeri.

- Aşağıdaki güvenlik izinlerinden biri:

  - Hedef kapsayıcının, üst kaynak grubunun veya aboneliğin kapsamındaki [Depolama Blobu veri sahibi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) rolüne atanan bir Azure ACTIVE DIRECTORY (ad) [güvenlik sorumlusu](../../role-based-access-control/overview.md#security-principal) .  

  - ACL ayarlarını uygulamayı planladığınız Hedef kapsayıcının veya dizinin sahibi olan kullanıcı. ACL 'Leri yinelemeli olarak ayarlamak için, hedef kapsayıcı veya dizindeki tüm alt öğeleri içerir.
  
  - Depolama hesabı anahtarı.

## <a name="set-up-your-project"></a>Projenizi ayarlama

Başlamak için [Azure. Storage. Files. DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet paketini yükledikten sonra.

1. Bir komut penceresi açın (örneğin: Windows PowerShell).

2. Proje dizininizden komutunu kullanarak Azure. Storage. Files. DataLake önizleme paketini yükledikten sonra `dotnet add package` .

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.6.0 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

   Ardından, bu using deyimlerini kod dosyanızın en üstüne ekleyin.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   ```

## <a name="connect-to-the-account"></a>Hesaba Bağlan

Bu makaledeki kod parçacıklarını kullanmak için depolama hesabını temsil eden bir [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) örneği oluşturmanız gerekir. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Azure Active Directory kullanarak bağlanma (AD)

> [!NOTE]
> Erişimi yetkilendirmek için Azure Active Directory (Azure AD) kullanıyorsanız, güvenlik sorumlusuna [Depolama Blobu veri sahibi rolü](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)atandığından emin olun. ACL izinlerinin nasıl uygulandığı ve bunların nasıl değiştirileceği hakkında daha fazla bilgi edinmek için  [Azure Data Lake Storage 2. erişim denetimi modeli](./data-lake-storage-access-control-model.md)' ne bakın.

Azure AD ile uygulamanızın kimliğini doğrulamak için [.net Için Azure Identity istemci kitaplığı](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) 'nı kullanabilirsiniz.

Paketi yükledikten sonra, bu using ifadesini kod dosyanızın en üstüne ekleyin.

```csharp
using Azure.Identity;
```

İstemci KIMLIĞI, bir istemci parolası ve kiracı KIMLIĞI alın. Bunu yapmak için bkz. [bir istemci uygulamasından istekleri yetkilendirmek Için Azure AD 'den belirteç alma](../common/storage-auth-aad-app.md). Bu işlemin bir parçası olarak, güvenlik sorumlusuna aşağıdaki [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md) rollerinden birini atamanız gerekir. 

|Rol|ACL ayarı özelliği|
|--|--|
|[Depolama Blob Verileri Sahibi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Hesaptaki tüm dizinler ve dosyalar.|
|[Depolama Blob Verileri Katkıda Bulunanı](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Yalnızca güvenlik sorumlusuna ait olan dizinler ve dosyalar.|

Bu örnek, bir istemci KIMLIĞI, bir istemci parolası ve bir kiracı KIMLIĞI kullanarak bir [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) örneği oluşturur.  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Daha fazla örnek için bkz. [.net Için Azure kimlik istemci kitaplığı](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) belgeleri.

### <a name="connect-by-using-an-account-key"></a>Hesap anahtarı kullanarak bağlan

Bu, bir hesaba bağlanmanın en kolay yoludur.

Bu örnek, bir hesap anahtarı kullanarak bir [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) örneği oluşturur.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>ACL 'Leri ayarla

Bir ACL *ayarladığınızda* , tüm ACL 'leri dahil olmak üzere tüm ACL 'yi **değiştirirsiniz** . Bir güvenlik sorumlusunun izin düzeyini değiştirmek veya var olan girişleri etkilemeden ACL 'ye yeni bir güvenlik sorumlusu eklemek istiyorsanız, ACL 'yi *güncelleştirmeniz* gerekir. Bunu değiştirmek yerine bir ACL 'yi güncelleştirmek için, bu makalenin [ACL 'Leri Güncelleştir](#update-acls) bölümüne bakın.  

ACL 'yi *ayarlamayı* seçerseniz, sahip olan kullanıcı için bir giriş, sahip olan grup için bir giriş ve diğer tüm kullanıcılar için bir girdi eklemeniz gerekir. Sahip olan Kullanıcı, sahip olan grup ve diğer tüm kullanıcılar hakkında daha fazla bilgi edinmek için bkz. [Kullanıcılar ve kimlikler](data-lake-storage-access-control.md#users-and-identities).

Bu bölümde nasıl yapılacağı gösterilmektedir:

- Bir dizinin ACL 'sini ayarlama
- Bir dosyanın ACL 'sini ayarlama
- ACL’leri özyinelemeli olarak belirleme

### <a name="set-the-acl-of-a-directory"></a>Bir dizinin ACL 'sini ayarlama

[Datalakedirectoryclient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) yöntemini çağırarak bir dizinin erişim denetim LISTESINI (ACL) alın ve [Datalakedirectoryclient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) metodunu çağırarak ACL 'yi ayarlayın.

Bu örnek, adlı bir dizinin ACL 'sini alır ve ayarlar `my-directory` . Dize, `user::rwx,group::r-x,other::rw-` sahip olan kullanıcıya okuma, yazma ve yürütme izinleri verir, sahip olan gruba yalnızca okuma ve yürütme izinleri verir ve diğerlerinin tüm okuma ve yazma izinlerini verir.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ACLDirectory":::

Ayrıca, bir kapsayıcının kök dizininin ACL 'sini de alabilir ve ayarlayabilirsiniz. Kök dizini almak için `""` [Datalakefilesystemclient. getdirectoryclient](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getdirectoryclient) metoduna boş bir dize () geçirin.

### <a name="set-the-acl-of-a-file"></a>Bir dosyanın ACL 'sini ayarlama

[Datalakefileclient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) yöntemini çağırarak bir dosyanın erişim denetim LISTESINI (ACL) alın ve [Datalakefileclient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) metodunu çağırarak ACL 'yi ayarlayın.

Bu örnek, adlı bir dosyanın ACL 'sini alır ve ayarlar `my-file.txt` . Dize, `user::rwx,group::r-x,other::rw-` sahip olan kullanıcıya okuma, yazma ve yürütme izinleri verir, sahip olan gruba yalnızca okuma ve yürütme izinleri verir ve diğerlerinin tüm okuma ve yazma izinlerini verir.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_FileACL":::

### <a name="set-acls-recursively"></a>ACL’leri özyinelemeli olarak belirleme

**Datalakedirectoryclient. SetAccessControlRecursiveAsync** yöntemini çağırarak ACL 'leri yinelemeli olarak ayarlayın. Bu yönteme [Pathaccesscontrolıdıtem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) [listesi](/dotnet/api/system.collections.generic.list-1) geçirin. Her [Pathaccesscontrolıdıtem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) bir ACL girdisi tanımlar.

**Varsayılan** bir ACL girişi ayarlamak Istiyorsanız [pathaccesscontrolıdıtem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) öğesinin [pathaccesscontrolıdıtem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) özelliğini **true** olarak ayarlayabilirsiniz. 

Bu örnek, adlı bir dizinin ACL 'sini ayarlar `my-parent-directory` . Bu yöntem `isDefaultScope` , varsayılan ACL 'nin ayarlanmış olup olmayacağını belirten adlı bir Boole parametresini kabul eder. Bu parametre [Pathaccesscontrolıdıtem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)kurucusunda kullanılır. ACL 'nin girişleri, sahip olan kullanıcıya okuma, yazma ve yürütme izinleri verir, sahip olan gruba yalnızca okuma ve yürütme izinleri verir ve tüm diğerlerine erişim vermez. Bu örnekteki son ACL girişi, "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "okuma ve yürütme izinlerine sahip belirli bir kullanıcıya izin verir.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_SetACLRecursively":::

Toplu iş boyutu belirterek ACL 'Leri yinelemeli olarak toplu olarak ayarlayan bir örnek görmek için bkz. .NET [örneği](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

## <a name="update-acls"></a>ACL 'Leri Güncelleştir

Bir ACL 'yi *güncelleştirdiğinizde* ACL 'yi DEğIşTIRMEk yerine ACL 'yi değiştirirsiniz. Örneğin, ACL 'de listelenen diğer güvenlik sorumlularını etkilemeden ACL 'ye yeni bir güvenlik sorumlusu ekleyebilirsiniz.  ACL 'yi güncelleştirmek yerine değiştirmek için, bu makalenin [ACL 'Leri ayarlama](#set-acls) bölümüne bakın.

Bu bölümde nasıl yapılacağı gösterilmektedir:

- ACL güncelleştirme
- ACL 'Leri yinelemeli olarak güncelleştir

### <a name="update-an-acl"></a>ACL güncelleştirme

İlk olarak, [Datalakedirectoryclient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) yöntemini çağırarak BIR dizinin ACL 'sini alın. ACL girişlerinin listesini yeni bir [Pathaccesscontrol](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) nesneleri [listesine](/dotnet/api/system.collections.generic.list-1) kopyalayın. Ardından, güncelleştirmek istediğiniz girdiyi bulun ve listede değiştirin. [Datalakedirectoryclient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) metodunu çağırarak ACL 'yi ayarlayın.

Bu örnek, diğer tüm kullanıcılar için ACL girişini değiştirerek bir kapsayıcının kök ACL 'sini günceller. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACL":::

### <a name="update-acls-recursively"></a>ACL 'Leri yinelemeli olarak güncelleştir

Bir ACL 'yi yinelemeli olarak güncelleştirmek için, güncelleştirmek istediğiniz ACL girdisiyle yeni bir ACL nesnesi oluşturun ve ACL 'yi Güncelleştir işleminde bu nesneyi kullanın. Mevcut ACL 'yi kullanmayın, yalnızca görüntülenecek ACL girdilerini sağlayın.

**Datalakedirectoryclient. UpdateAccessControlRecursiveAsync** yöntemini ÇAĞıRARAK bir ACL 'yi yinelemeli olarak güncelleştirin.  Bu yönteme [Pathaccesscontrolıdıtem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) [listesi](/dotnet/api/system.collections.generic.list-1) geçirin. Her [Pathaccesscontrolıdıtem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) bir ACL girdisi tanımlar.

**Varsayılan** bir ACL girişini güncelleştirmek Istiyorsanız, [pathaccesscontrolıdıtem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) öğesinin [Pathaccesscontrolıdıtem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) özelliğini **true** olarak ayarlayabilirsiniz.

Bu örnekte, yazma izniyle bir ACL girişi güncelleştirilir. Bu yöntem `isDefaultScope` , varsayılan ACL 'nin güncelleştirilmesini isteyip istemediğinizi belirten adlı bir Boole parametresini kabul eder. Bu parametre [Pathaccesscontrolıdıtem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)kurucusunda kullanılır.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACLsRecursively":::

Bir toplu iş boyutu belirterek ACL 'Leri yinelemeli olarak toplu halde güncelleştiren bir örnek görmek için bkz. .NET [örneği](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

## <a name="remove-acl-entries"></a>ACL girdilerini kaldır

Bir veya daha fazla ACL girişini kaldırabilirsiniz. Bu bölümde nasıl yapılacağı gösterilmektedir:

- ACL girişini kaldırma
- ACL girdilerini yinelemeli olarak kaldır

### <a name="remove-an-acl-entry"></a>ACL girişini kaldırma

İlk olarak, [Datalakedirectoryclient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) yöntemini çağırarak BIR dizinin ACL 'sini alın. ACL girişlerinin listesini yeni bir [Pathaccesscontrol](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) nesneleri [listesine](/dotnet/api/system.collections.generic.list-1) kopyalayın. Sonra kaldırmak istediğiniz girdiyi bulun ve koleksiyonun [Remove](/dotnet/api/system.collections.ilist.remove) metodunu çağırın. [Datalakedirectoryclient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) metodunu ÇAĞıRARAK güncelleştirilmiş ACL 'yi ayarlayın.

Bu örnek, diğer tüm kullanıcılar için ACL girişini değiştirerek bir kapsayıcının kök ACL 'sini günceller. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>ACL girdilerini yinelemeli olarak kaldır

ACL girdilerini yinelemeli olarak kaldırmak için, ACL girdisi kaldırılacak yeni bir ACL nesnesi oluşturun ve ardından ACL 'yi kaldır işleminde bu nesneyi kullanın. Mevcut ACL 'yi almaz, kaldırılacak ACL girdilerini sağlamanız yeterlidir. 

**Datalakedirectoryclient. RemoveAccessControlRecursiveAsync** YÖNTEMINI çağırarak acl girişlerini kaldırın. Bu yönteme [Pathaccesscontrolıdıtem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) [listesi](/dotnet/api/system.collections.generic.list-1) geçirin. Her [Pathaccesscontrolıdıtem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) bir ACL girdisi tanımlar. 

**Varsayılan** bir ACL girişini kaldırmak Istiyorsanız, [pathaccesscontrolıdıtem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) öğesinin [pathaccesscontrolıdıtem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) özelliğini **true** olarak ayarlayabilirsiniz. 

Bu örnek, adlı dizinin ACL 'sinden bir ACL girişini kaldırır `my-parent-directory` . Bu yöntem `isDefaultScope` , girişin varsayılan ACL 'den kaldırılması gerekip gerekmediğini belirten adlı bir Boole parametresini kabul eder. Bu parametre [Pathaccesscontrolıdıtem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)kurucusunda kullanılır.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLRecursively":::

Toplu iş boyutu belirterek, toplu işteki ACL 'Leri yinelemeli olarak kaldıran bir örnek görmek için bkz. .NET [örneği](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

## <a name="recover-from-failures"></a>Hatalardan kurtarma

ACL 'Leri yinelemeli olarak değiştirirken çalışma zamanı veya izin hatalarıyla karşılaşabilirsiniz. Çalışma zamanı hataları için işlemi baştan başlatın. Güvenlik sorumlusu, değiştirilen Dizin hiyerarşisindeki bir dizinin veya dosyanın ACL 'sini değiştirmek için yeterli izne sahip değilse, izin hataları oluşabilir. İzin sorununu çözün ve sonra bir devamlılık belirteci kullanarak işlemi hata noktasından sürdürmeyi seçin ya da işlemi baştan başlatın. Baştan başlatmayı tercih ediyorsanız devamlılık belirtecini kullanmanız gerekmez. ACL girdilerini negatif bir etki olmadan yeniden uygulayabilirsiniz.

Bu örnek hata durumunda bir devamlılık belirteci döndürür. Uygulama, hata bağlandıktan sonra bu örnek yöntemi yeniden çağırabilir ve devamlılık belirtecini geçiyolabilir. Bu örnek yöntem ilk kez çağrılırsa, uygulama `null` devamlılık belirteci parametresi için değerini geçirebilir. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ResumeContinuationToken":::

Toplu iş boyutu belirterek ACL 'Leri yinelemeli olarak toplu olarak ayarlayan bir örnek görmek için bkz. .NET [örneği](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

İşlemin, izin hatalarıyla kesintiye uğramadan tamamlanmasını istiyorsanız, bunu belirtebilirsiniz.

İşlemin kesintisiz tamamlanmasını sağlamak için bir **Accesscontrolchangedobir** nesnesi geçirin ve bu nesnenin **devam eden olayını** olarak ayarlayın ``true`` .

Bu örnek ACL girdilerini yinelemeli olarak ayarlar. Bu kod bir izin hatasıyla karşılaşırsa, hatayı kaydeder ve yürütmeye devam eder. Bu örnek konsola başarısızlık sayısını yazdırır.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ContinueOnFailure":::

Toplu iş boyutu belirterek ACL 'Leri yinelemeli olarak toplu olarak ayarlayan bir örnek görmek için bkz. .NET [örneği](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Ayrıca bkz.

- [API başvuru belgeleri](/dotnet/api/azure.storage.files.datalake)
- [Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [Örnekler](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Gen1 to Gen2 Mapping](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [Bilinen sorunlar](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Geri bildirimde bulunun](https://github.com/Azure/azure-sdk-for-net/issues)
- [Azure Data Lake Storage 2. 'de erişim denetimi modeli](data-lake-storage-access-control.md)
- [Azure Data Lake Storage 2. 'de erişim denetim listeleri (ACL 'Ler)](data-lake-storage-access-control.md)