---
title: Azure Data Lake Storage 2. ACL 'Leri yönetmek için Python kullanma
description: Hiyerarşik ad alanı (HNS) etkin olan depolama hesaplarında Python yönetimi erişim denetim listelerini (ACL) kullanın.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: ba864aa1aa2462f21e05ab5e779c8e715d6bb973
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654474"
---
# <a name="use-python-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. ACL 'Leri yönetmek için Python kullanma

Bu makalede, dizin ve dosyaların erişim denetim listelerini almak, ayarlamak ve güncelleştirmek için Python 'un nasıl kullanılacağı gösterilmektedir. 

ACL devralma, bir üst dizin altında oluşturulan yeni alt öğeler için zaten kullanılabilir. Ancak, bu değişiklikleri her alt öğe için ayrı ayrı yapmak zorunda kalmadan ACL 'Leri bir üst dizinin varolan alt öğelerine yinelemeli olarak ekleyebilir, güncelleştirebilir ve kaldırabilirsiniz. 

[Paket (Python paket dizini)](https://pypi.org/project/azure-storage-file-datalake/)  |  [Örnekler](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  [ÖZYINELEMELI ACL örnekleri](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)  |  [API başvurusu](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  [Gen1 to Gen2 Mapping](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Geri bildirimde](https://github.com/Azure/azure-sdk-for-python/issues) bulunun

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).

- Hiyerarşik ad alanı (HNS) etkin olan bir depolama hesabı. Bir tane oluşturmak için [Bu](create-data-lake-storage-account.md) yönergeleri izleyin.

- Azure CLı sürümü `2.6.0` veya üzeri.

- Aşağıdaki güvenlik izinlerinden biri:

  - Hedef kapsayıcının, üst kaynak grubunun veya aboneliğin kapsamındaki [Depolama Blobu veri sahibi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) rolüne atanan bir Azure ACTIVE DIRECTORY (ad) [güvenlik sorumlusu](../../role-based-access-control/overview.md#security-principal) .  

  - ACL ayarlarını uygulamayı planladığınız Hedef kapsayıcının veya dizinin sahibi olan kullanıcı. ACL 'Leri yinelemeli olarak ayarlamak için, hedef kapsayıcı veya dizindeki tüm alt öğeleri içerir.
  
  - Depolama hesabı anahtarı.

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

### <a name="connect-by-using-azure-active-directory-ad"></a>Azure Active Directory kullanarak bağlanma (AD)

> [!NOTE]
> Erişimi yetkilendirmek için Azure Active Directory (Azure AD) kullanıyorsanız, güvenlik sorumlusuna [Depolama Blobu veri sahibi rolü](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)atandığından emin olun. ACL izinlerinin nasıl uygulandığı ve bunların nasıl değiştirileceği hakkında daha fazla bilgi edinmek için  [Azure Data Lake Storage 2. erişim denetimi modeli](./data-lake-storage-access-control-model.md)' ne bakın.

Azure AD ile uygulamanızın kimliğini doğrulamak için [Python Için Azure kimlik istemci kitaplığını](https://pypi.org/project/azure-identity/) kullanabilirsiniz.

İstemci KIMLIĞI, bir istemci parolası ve kiracı KIMLIĞI alın. Bunu yapmak için bkz. [bir istemci uygulamasından istekleri yetkilendirmek Için Azure AD 'den belirteç alma](../common/storage-auth-aad-app.md). Bu işlemin bir parçası olarak, güvenlik sorumlusuna aşağıdaki [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md) rollerinden birini atamanız gerekir. 

|Rol|ACL ayarı özelliği|
|--|--|
|[Depolama Blob Verileri Sahibi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Hesaptaki tüm dizinler ve dosyalar.|
|[Depolama Blob Verileri Katkıda Bulunanı](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Yalnızca güvenlik sorumlusuna ait olan dizinler ve dosyalar.|

Bu örnek, bir istemci KIMLIĞI, bir istemci parolası ve bir kiracı KIMLIĞI kullanarak bir **DataLakeServiceClient** örneği oluşturur.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Daha fazla örnek için bkz. [Python Için Azure kimlik istemci kitaplığı](https://pypi.org/project/azure-identity/) belgeleri.

### <a name="connect-by-using-an-account-key"></a>Hesap anahtarı kullanarak bağlan

Bu, bir hesaba bağlanmanın en kolay yoludur.

Bu örnek, bir hesap anahtarı kullanarak bir **DataLakeServiceClient** örneği oluşturur.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::
 
- `storage_account_name`Yer tutucu değerini depolama hesabınızın adıyla değiştirin.

- `storage_account_key`Yer tutucu değerini depolama hesabı erişim anahtarınızla değiştirin.

## <a name="set-acls"></a>ACL 'Leri ayarla

Bir ACL *ayarladığınızda* , tüm ACL 'leri dahil olmak üzere tüm ACL 'yi **değiştirirsiniz** . Bir güvenlik sorumlusunun izin düzeyini değiştirmek veya var olan girişleri etkilemeden ACL 'ye yeni bir güvenlik sorumlusu eklemek istiyorsanız, ACL 'yi *güncelleştirmeniz* gerekir. Bunu değiştirmek yerine bir ACL 'yi güncelleştirmek için, bu makalenin [ACL 'Leri Güncelleştir](#update-acls-recursively) bölümüne bakın.  

Bu bölümde nasıl yapılacağı gösterilmektedir:

- Bir dizinin ACL 'sini ayarlama
- Bir dosyanın ACL 'sini ayarlama

### <a name="set-the-acl-of-a-directory"></a>Bir dizinin ACL 'sini ayarlama

**DataLakeDirectoryClient.get_access_control** yöntemini çağırarak bir dizinin erişim denetim LISTESINI (ACL) alın ve **DATALAKEDIRECTORYCLIENT.SET_ACCESS_CONTROL** metodunu çağırarak ACL 'yi ayarlayın.

Bu örnek, adlı bir dizinin ACL 'sini alır ve ayarlar `my-directory` . Dize, `rwxr-xrw-` sahip olan kullanıcıya okuma, yazma ve yürütme izinleri verir, sahip olan gruba yalnızca okuma ve yürütme izinleri verir ve diğerlerinin tüm okuma ve yazma izinlerini verir.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ACLDirectory":::

Ayrıca, bir kapsayıcının kök dizininin ACL 'sini de alabilir ve ayarlayabilirsiniz. Kök dizini almak için **FileSystemClient._get_root_directory_client** yöntemini çağırın.

### <a name="set-the-acl-of-a-file"></a>Bir dosyanın ACL 'sini ayarlama

**DataLakeFileClient.get_access_control** yöntemini çağırarak bir dosyanın erişim denetim LISTESINI (ACL) alın ve **DATALAKEFILECLIENT.SET_ACCESS_CONTROL** metodunu çağırarak ACL 'yi ayarlayın.

Bu örnek, adlı bir dosyanın ACL 'sini alır ve ayarlar `my-file.txt` . Dize, `rwxr-xrw-` sahip olan kullanıcıya okuma, yazma ve yürütme izinleri verir, sahip olan gruba yalnızca okuma ve yürütme izinleri verir ve diğerlerinin tüm okuma ve yazma izinlerini verir.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_FileACL":::

## <a name="set-acls-recursively"></a>ACL’leri özyinelemeli olarak belirleme

Bir ACL *ayarladığınızda* , tüm ACL 'leri dahil olmak üzere tüm ACL 'yi **değiştirirsiniz** . Bir güvenlik sorumlusunun izin düzeyini değiştirmek veya var olan girişleri etkilemeden ACL 'ye yeni bir güvenlik sorumlusu eklemek istiyorsanız, ACL 'yi *güncelleştirmeniz* gerekir. Bunu değiştirmek yerine bir ACL 'yi güncelleştirmek için, bu makalenin [ACL 'leri yinelemeli olarak güncelleştir](#update-acls-recursively) bölümüne bakın.

**DataLakeDirectoryClient.set_access_control_recursive** yöntemini çağırarak ACL 'leri yinelemeli olarak ayarlayın.

**Varsayılan** bir ACL girişi ayarlamak istiyorsanız, DIZEYI `default:` her ACL Giriş dizesinin başına ekleyin.

Bu örnek, adlı bir dizinin ACL 'sini ayarlar `my-parent-directory` .

Bu yöntem `is_default_scope` , varsayılan ACL 'nin ayarlanmış olup olmayacağını belirten adlı bir Boole parametresini kabul eder. Bu parametre ise `True` , ACL girişlerinin listesi öncesinde dize olur `default:` .

ACL 'nin girişleri, sahip olan kullanıcıya okuma, yazma ve yürütme izinleri verir, sahip olan gruba yalnızca okuma ve yürütme izinleri verir ve tüm diğerlerine erişim vermez. Bu örnekteki son ACL girişi, "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "okuma ve yürütme izinlerine sahip belirli bir kullanıcıya izin verir. Bu girişler, sahip olan kullanıcıya okuma, yazma ve yürütme izinleri verir, sahip olan gruba yalnızca okuma ve yürütme izinleri verir ve tüm diğerlerine erişim vermez. Bu örnekteki son ACL girişi, "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "okuma ve yürütme izinlerine sahip belirli bir kullanıcıya izin verir.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_SetACLRecursively":::

Bir toplu iş boyutu belirterek ACL 'Leri yinelemeli olarak toplu halde işleyen bir örnek görmek için bkz. Python [örneği](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

## <a name="update-acls-recursively"></a>ACL 'Leri yinelemeli olarak güncelleştir

Bir ACL 'yi *güncelleştirdiğinizde* ACL 'yi DEğIşTIRMEk yerine ACL 'yi değiştirirsiniz. Örneğin, ACL 'de listelenen diğer güvenlik sorumlularını etkilemeden ACL 'ye yeni bir güvenlik sorumlusu ekleyebilirsiniz.  ACL 'yi güncelleştirmek yerine değiştirmek için, bu makalenin [ACL 'Leri ayarlama](#set-acls) bölümüne bakın.

Bir ACL 'yi yinelemeli olarak güncelleştirmek için, güncelleştirmek istediğiniz ACL girdisiyle yeni bir ACL nesnesi oluşturun ve ACL 'yi Güncelleştir işleminde bu nesneyi kullanın. Mevcut ACL 'yi kullanmayın, yalnızca görüntülenecek ACL girdilerini sağlayın. **DataLakeDirectoryClient.update_access_control_recursive** yöntemini ÇAĞıRARAK bir ACL 'yi yinelemeli olarak güncelleştirin. **Varsayılan** bir ACL girişini güncelleştirmek istiyorsanız, DIZEYI `default:` her ACL Giriş dizesinin başına ekleyin.

Bu örnekte, yazma izniyle bir ACL girişi güncelleştirilir.

Bu örnek, adlı bir dizinin ACL 'sini ayarlar `my-parent-directory` . Bu yöntem `is_default_scope` , varsayılan ACL 'nin güncelleştirilmesini isteyip istemediğinizi belirten adlı bir Boole parametresini kabul eder. Bu parametre ise `True` , GÜNCELLEŞTIRILMIŞ ACL girişi öncesinde dize olur `default:` .  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_UpdateACLsRecursively":::

Bir toplu iş boyutu belirterek ACL 'Leri yinelemeli olarak toplu halde işleyen bir örnek görmek için bkz. Python [örneği](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

## <a name="remove-acl-entries-recursively"></a>ACL girdilerini yinelemeli olarak kaldır

Bir veya daha fazla ACL girişini kaldırabilirsiniz. ACL girdilerini yinelemeli olarak kaldırmak için, ACL girdisi kaldırılacak yeni bir ACL nesnesi oluşturun ve ardından ACL 'yi kaldır işleminde bu nesneyi kullanın. Mevcut ACL 'yi almaz, kaldırılacak ACL girdilerini sağlamanız yeterlidir. 

**DataLakeDirectoryClient.remove_access_control_recursive** YÖNTEMINI çağırarak acl girişlerini kaldırın. **Varsayılan** bir ACL girişini kaldırmak istiyorsanız, dizeyi `default:` acl giriş dizesinin başına ekleyin. 

Bu örnek, adlı dizinin ACL 'sinden bir ACL girişini kaldırır `my-parent-directory` . Bu yöntem `is_default_scope` , girişin varsayılan ACL 'den kaldırılması gerekip gerekmediğini belirten adlı bir Boole parametresini kabul eder. Bu parametre ise `True` , GÜNCELLEŞTIRILMIŞ ACL girişi öncesinde dize olur `default:` . 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

Bir toplu iş boyutu belirterek ACL 'Leri yinelemeli olarak toplu halde işleyen bir örnek görmek için bkz. Python [örneği](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

## <a name="recover-from-failures"></a>Hatalardan kurtarma

Çalışma zamanı veya izin hatalarıyla karşılaşabilirsiniz. Çalışma zamanı hataları için işlemi baştan başlatın. Güvenlik sorumlusu, değiştirilen Dizin hiyerarşisindeki bir dizinin veya dosyanın ACL 'sini değiştirmek için yeterli izne sahip değilse, izin hataları oluşabilir. İzin sorununu çözün ve sonra bir devamlılık belirteci kullanarak işlemi hata noktasından sürdürmeyi seçin ya da işlemi baştan başlatın. Baştan başlatmayı tercih ediyorsanız devamlılık belirtecini kullanmanız gerekmez. ACL girdilerini negatif bir etki olmadan yeniden uygulayabilirsiniz.

Bu örnek hata durumunda bir devamlılık belirteci döndürür. Uygulama, hata bağlandıktan sonra bu örnek yöntemi yeniden çağırabilir ve devamlılık belirtecini geçiyolabilir. Bu örnek yöntem ilk kez çağrılırsa, uygulama ``None`` devamlılık belirteci parametresi için değerini geçirebilir.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ResumeContinuationToken":::

Bir toplu iş boyutu belirterek ACL 'Leri yinelemeli olarak toplu halde işleyen bir örnek görmek için bkz. Python [örneği](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

İşlemin, izin hatalarıyla kesintiye uğramadan tamamlanmasını istiyorsanız, bunu belirtebilirsiniz.

İşlemin kesintisiz tamamlanmasını sağlamak için **DataLakeDirectoryClient.set_access_control_recursive** yöntemine devamlılık belirteci geçirmeyin.

Bu örnek ACL girdilerini yinelemeli olarak ayarlar. Bu kod bir izin hatasıyla karşılaşırsa, hatayı kaydeder ve yürütmeye devam eder. Bu örnek konsola başarısızlık sayısını yazdırır.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ContinueOnFailure":::

Bir toplu iş boyutu belirterek ACL 'Leri yinelemeli olarak toplu halde işleyen bir örnek görmek için bkz. Python [örneği](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Ayrıca bkz.

- [API başvuru belgeleri](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [Paket (Python paket dizini)](https://pypi.org/project/azure-storage-file-datalake/)
- [Örnekler](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Gen1 to Gen2 Mapping](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Bilinen sorunlar](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Geri bildirimde bulunun](https://github.com/Azure/azure-sdk-for-python/issues)
- [Azure Data Lake Storage 2. 'de erişim denetimi modeli](data-lake-storage-access-control.md)
- [Azure Data Lake Storage 2. 'de erişim denetim listeleri (ACL 'Ler)](data-lake-storage-access-control.md)