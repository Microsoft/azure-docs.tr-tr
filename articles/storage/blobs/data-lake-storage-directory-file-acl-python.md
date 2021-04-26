---
title: Azure Data Lake Storage 2. verileri yönetmek için Python kullanma
description: Hiyerarşik ad alanı etkinleştirilmiş depolama hesaplarındaki dizinleri ve dosyaları yönetmek için Python kullanın.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: a143c0aa19241b532cabff95fe6bf85679e4007c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652301"
---
# <a name="use-python-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. dizinleri ve dosyaları yönetmek için Python kullanma

Bu makalede, bir hiyerarşik ad alanı olan depolama hesaplarında dizin ve dosya oluşturmak ve yönetmek için Python 'un nasıl kullanılacağı gösterilmektedir.

Dizinlerin ve dosyaların erişim denetim listelerini (ACL) alma, ayarlama ve güncelleştirme hakkında bilgi edinmek için bkz. [Azure Data Lake Storage 2. ACL 'leri yönetmek Için Python kullanma](data-lake-storage-acl-python.md).

[Paket (Python paket dizini)](https://pypi.org/project/azure-storage-file-datalake/)  |  [Örnekler](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  [API başvurusu](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  [Gen1 to Gen2 Mapping](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Geri bildirimde](https://github.com/Azure/azure-sdk-for-python/issues) bulunun

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).

- Hiyerarşik ad alanı etkin olan bir depolama hesabı. Bir tane oluşturmak için [Bu](create-data-lake-storage-account.md) yönergeleri izleyin.

## <a name="set-up-your-project"></a>Projenizi ayarlama

[PIP](https://pypi.org/project/pip/)'Yi kullanarak Python için Azure Data Lake Storage istemci kitaplığı 'nı kullanın.

```
pip install azure-storage-file-datalake
```

Bu import deyimlerini, kod dosyanızın en üstüne ekleyin.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Hesaba Bağlan

Bu makaledeki kod parçacıklarını kullanmak için depolama hesabını temsil eden bir **DataLakeServiceClient** örneği oluşturmanız gerekir. 

### <a name="connect-by-using-an-account-key"></a>Hesap anahtarı kullanarak bağlan

Bu, bir hesaba bağlanmanın en kolay yoludur. 

Bu örnek, bir hesap anahtarı kullanarak bir **DataLakeServiceClient** örneği oluşturur.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::

- `storage_account_name`Yer tutucu değerini depolama hesabınızın adıyla değiştirin.

- `storage_account_key`Yer tutucu değerini depolama hesabı erişim anahtarınızla değiştirin.

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Azure Active Directory kullanarak bağlanma (Azure AD)

Azure AD ile uygulamanızın kimliğini doğrulamak için [Python Için Azure kimlik istemci kitaplığını](https://pypi.org/project/azure-identity/) kullanabilirsiniz.

Bu örnek, bir istemci KIMLIĞI, bir istemci parolası ve bir kiracı KIMLIĞI kullanarak bir **DataLakeServiceClient** örneği oluşturur.  Bu değerleri almak için bkz. [bir istemci uygulamasından istekleri yetkilendirmek Için Azure AD 'den belirteç alma](../common/storage-auth-aad-app.md).

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Daha fazla örnek için bkz. [Python Için Azure kimlik istemci kitaplığı](https://pypi.org/project/azure-identity/) belgeleri.

## <a name="create-a-container"></a>Kapsayıcı oluşturma

Bir kapsayıcı dosyalarınız için bir dosya sistemi görevi görür. **FileSystemDataLakeServiceClient.create_file_system** yöntemini çağırarak bir tane oluşturabilirsiniz.

Bu örnek adlı bir kapsayıcı oluşturur `my-file-system` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>Dizin oluşturma

**FileSystemClient.create_directory** yöntemini çağırarak bir dizin başvurusu oluşturun.

Bu örnek, bir kapsayıcıya adlı bir dizin ekler `my-directory` . 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Bir dizini yeniden adlandırma veya taşıma

**DataLakeDirectoryClient.rename_directory** yöntemini çağırarak bir dizini yeniden adlandırın veya taşıyın. İstenen dizinin yolunu bir parametre olarak geçirin. 

Bu örnek, bir alt dizini ada yeniden adlandırır `my-subdirectory-renamed` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_RenameDirectory":::

## <a name="delete-a-directory"></a>Bir dizini silme

**DataLakeDirectoryClient.delete_directory** yöntemini çağırarak bir dizini silin.

Bu örnek adlı bir dizini siler `my-directory` .  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Dizine dosya yükleme

İlk olarak, **Datalakefileclient** sınıfının bir örneğini oluşturarak hedef dizinde bir dosya başvurusu oluşturun. **DataLakeFileClient.append_data** yöntemini çağırarak bir dosyayı karşıya yükleyin. **DataLakeFileClient.flush_data** yöntemini çağırarak karşıya yüklemeyi tamamladığınızdan emin olun.

Bu örnek, adlı bir dizine bir metin dosyası yükler `my-directory` .   

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFile":::

> [!TIP]
> Dosya boyutunuz büyükse, kodunuzun **DataLakeFileClient.append_data** yöntemine birden çok çağrı yapması gerekir. Bunun yerine **DataLakeFileClient.upload_data** yöntemini kullanmayı düşünün. Bu şekilde, tüm dosyayı tek bir çağrıda karşıya yükleyebilirsiniz. 

## <a name="upload-a-large-file-to-a-directory"></a>Bir dizine büyük bir dosya yükleme

**DataLakeFileClient.append_data** yöntemine birden çok çağrı yapmak zorunda kalmadan büyük dosyaları karşıya yüklemek için **DataLakeFileClient.upload_data** yöntemini kullanın.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Bir dizinden indir 

Yazmak için yerel bir dosya açın. Ardından, indirmek istediğiniz dosyayı temsil eden bir **Datalakefileclient** örneği oluşturun. Dosyadaki baytları okumak için **DataLakeFileClient.read_file** çağırın ve ardından bu baytları yerel dosyaya yazın. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DownloadFromDirectory":::

## <a name="list-directory-contents"></a>Dizin içeriğini listeleme

**FileSystemClient.get_paths** yöntemini çağırarak ve sonra sonuçlar arasında sıralama yaparak dizin içeriğini listeleyin.

Bu örnek, adlı bir dizinde bulunan her bir alt dizin ve dosyanın yolunu yazdırır `my-directory` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Ayrıca bkz.

- [API başvuru belgeleri](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [Paket (Python paket dizini)](https://pypi.org/project/azure-storage-file-datalake/)
- [Örnekler](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Gen1 to Gen2 Mapping](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Bilinen sorunlar](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Geri bildirimde bulunun](https://github.com/Azure/azure-sdk-for-python/issues)