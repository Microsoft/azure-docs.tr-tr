---
title: Azure Data Lake Storage 2. ACL 'Leri ayarlamak için Java kullanma
description: Hiyerarşik ad alanı (HNS) etkin olan depolama hesaplarında erişim denetim listelerini (ACL) yönetmek için Java için Azure depolama kitaplıklarını kullanın.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: e7d6156fe5cd8ab32ff159bda64e0c06cfbac406
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654479"
---
# <a name="use-java-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. ACL 'Leri yönetmek için Java kullanın

Bu makalede, dizinlerin ve dosyaların erişim denetim listelerini almak, ayarlamak ve güncelleştirmek için Java 'nın nasıl kullanılacağı gösterilmektedir. 

ACL devralma, bir üst dizin altında oluşturulan yeni alt öğeler için zaten kullanılabilir. Ancak, bu değişiklikleri her alt öğe için ayrı ayrı yapmak zorunda kalmadan ACL 'Leri bir üst dizinin varolan alt öğelerine yinelemeli olarak ekleyebilir, güncelleştirebilir ve kaldırabilirsiniz. 

[Paket (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  |  [Örnekler](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  |  [API başvurusu](/java/api/overview/azure/storage-file-datalake-readme)  |  [Gen1 to Gen2 Mapping](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Geri bildirimde](https://github.com/Azure/azure-sdk-for-java/issues) bulunun

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).

- Hiyerarşik ad alanı (HNS) etkin olan bir depolama hesabı. Bir tane oluşturmak için [Bu](create-data-lake-storage-account.md) yönergeleri izleyin.

- Azure CLı sürümü `2.6.0` veya üzeri.

- Aşağıdaki güvenlik izinlerinden biri:

  - Hedef kapsayıcının, üst kaynak grubunun veya aboneliğin kapsamındaki [Depolama Blobu veri sahibi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) rolüne atanan bir Azure ACTIVE DIRECTORY (ad) [güvenlik sorumlusu](../../role-based-access-control/overview.md#security-principal) .  

  - ACL ayarlarını uygulamayı planladığınız Hedef kapsayıcının veya dizinin sahibi olan kullanıcı. ACL 'Leri yinelemeli olarak ayarlamak için, hedef kapsayıcı veya dizindeki tüm alt öğeleri içerir.
  
  - Depolama hesabı anahtarı.

## <a name="set-up-your-project"></a>Projenizi ayarlama

Başlamak için [Bu sayfayı](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) açın ve Java kitaplığı 'nın en son sürümünü bulun. Sonra, *pom.xml* dosyasını metin düzenleyicinizde açın. Bu sürüme başvuran bir bağımlılık öğesi ekleyin.

İstemci uygulamanızın kimlik doğrulamasını Azure Active Directory (AD) kullanarak ve sonra Azure gizli Istemci kitaplığına bir bağımlılık ekleyin. Bkz.  [projenize gizli Istemci kitaplığı paketi ekleme](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Ardından, bu Imports deyimlerini kod dosyanıza ekleyin.

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.AccessControlChangeCounters;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

## <a name="connect-to-the-account"></a>Hesaba Bağlan

Bu makaledeki kod parçacıklarını kullanmak için depolama hesabını temsil eden bir **DataLakeServiceClient** örneği oluşturmanız gerekir. 

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Azure Active Directory kullanarak bağlanma (Azure AD)

Uygulamanızı Azure AD ile doğrulamak için [Azure kimlik istemci kitaplığı](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) 'nı kullanabilirsiniz.

İstemci KIMLIĞI, bir istemci parolası ve kiracı KIMLIĞI alın. Bunu yapmak için bkz. [bir istemci uygulamasından istekleri yetkilendirmek Için Azure AD 'den belirteç alma](../common/storage-auth-aad-app.md). Bu işlemin bir parçası olarak, güvenlik sorumlusuna aşağıdaki [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md) rollerinden birini atamanız gerekir. 

|Rol|ACL ayarı özelliği|
|--|--|
|[Depolama Blob Verileri Sahibi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Hesaptaki tüm dizinler ve dosyalar.|
|[Depolama Blob Verileri Katkıda Bulunanı](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Yalnızca güvenlik sorumlusuna ait olan dizinler ve dosyalar.|

Bu örnek, bir istemci KIMLIĞI, bir istemci parolası ve bir kiracı KIMLIĞI kullanarak bir **DataLakeServiceClient** örneği oluşturur.  

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> Daha fazla örnek için bkz. [Java Için Azure kimlik istemci kitaplığı](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) belgeleri.

### <a name="connect-by-using-an-account-key"></a>Hesap anahtarı kullanarak bağlan

Bu, bir hesaba bağlanmanın en kolay yoludur. 

Bu örnek, bir hesap anahtarı kullanarak bir **DataLakeServiceClient** örneği oluşturur.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>ACL 'Leri ayarla

Bir ACL *ayarladığınızda* , tüm ACL 'leri dahil olmak üzere tüm ACL 'yi **değiştirirsiniz** . Bir güvenlik sorumlusunun izin düzeyini değiştirmek veya var olan girişleri etkilemeden ACL 'ye yeni bir güvenlik sorumlusu eklemek istiyorsanız, ACL 'yi *güncelleştirmeniz* gerekir. Bunu değiştirmek yerine bir ACL 'yi güncelleştirmek için, bu makalenin [ACL 'Leri Güncelleştir](#update-acls) bölümüne bakın.  

ACL 'yi *ayarlamayı* seçerseniz, sahip olan kullanıcı için bir giriş, sahip olan grup için bir giriş ve diğer tüm kullanıcılar için bir girdi eklemeniz gerekir. Sahip olan Kullanıcı, sahip olan grup ve diğer tüm kullanıcılar hakkında daha fazla bilgi edinmek için bkz. [Kullanıcılar ve kimlikler](data-lake-storage-access-control.md#users-and-identities).

Bu bölümde nasıl yapılacağı gösterilmektedir:

- Bir dizinin ACL 'sini ayarlama
- Bir dosyanın ACL 'sini ayarlama
- ACL’leri özyinelemeli olarak belirleme 

### <a name="set-the-acl-of-a-directory"></a>Bir dizinin ACL 'sini ayarlama

Bu örnek, adlı bir dizinin ACL 'sini alır ve ayarlar `my-directory` . Bu örnek, sahip olan kullanıcıya okuma, yazma ve yürütme izinlerini verir, sahip olan gruba yalnızca okuma ve yürütme izinleri verir ve diğerlerinin tüm okuma erişimini sağlar.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageDirectoryACLs":::

Ayrıca, bir kapsayıcının kök dizininin ACL 'sini de alabilir ve ayarlayabilirsiniz. Kök dizini almak için `""` **Datalakefilesystemclient. getdirectoryclient** metoduna boş bir dize () geçirin.

### <a name="set-the-acl-of-a-file"></a>Bir dosyanın ACL 'sini ayarlama

Bu örnek, adlı bir dosyanın ACL 'sini alır ve ayarlar `upload-file.txt` . Bu örnek, sahip olan kullanıcıya okuma, yazma ve yürütme izinlerini verir, sahip olan gruba yalnızca okuma ve yürütme izinleri verir ve diğerlerinin tüm okuma erişimini sağlar.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageFileACLs":::

### <a name="set-acls-recursively"></a>ACL’leri özyinelemeli olarak belirleme

**Datalakedirectoryclient. Setaccesscontrolözyinelemeli** yöntemini çağırarak ACL 'leri yinelemeli olarak ayarlayın. Bu yöntemi [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) nesnelerinin bir [listesini](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) geçirin. Her [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) bir ACL girdisi tanımlar.

**Varsayılan** bir ACL girişi ayarlamak Istiyorsanız, [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) ve pass öğesinin **setdefaultscope** yöntemini **doğru** değerine çağırabilirsiniz. 

Bu örnek, adlı bir dizinin ACL 'sini ayarlar `my-parent-directory` . Bu yöntem `isDefaultScope` , varsayılan ACL 'nin ayarlanmış olup olmayacağını belirten adlı bir Boole parametresini kabul eder. Bu parametre, [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)öğesinin **setdefaultscope** metoduna yapılan her çağrıda kullanılır. ACL 'nin girişleri, sahip olan kullanıcıya okuma, yazma ve yürütme izinleri verir, sahip olan gruba yalnızca okuma ve yürütme izinleri verir ve tüm diğerlerine erişim vermez. Bu örnekteki son ACL girişi, "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" okuma ve yürütme izinlerine sahip belirli bir kullanıcıya izin verir.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_SetACLRecursively":::

## <a name="update-acls"></a>ACL 'Leri Güncelleştir

Bir ACL 'yi *güncelleştirdiğinizde* ACL 'yi DEğIşTIRMEk yerine ACL 'yi değiştirirsiniz. Örneğin, ACL 'de listelenen diğer güvenlik sorumlularını etkilemeden ACL 'ye yeni bir güvenlik sorumlusu ekleyebilirsiniz.  ACL 'yi güncelleştirmek yerine değiştirmek için, bu makalenin [ACL 'Leri ayarlama](#set-acls) bölümüne bakın.

Bu bölümde nasıl yapılacağı gösterilmektedir:

- ACL güncelleştirme
- ACL 'Leri yinelemeli olarak güncelleştir

### <a name="update-an-acl"></a>ACL güncelleştirme

İlk olarak, [Pathaccesscontrol. getAccessControlList](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist) metodunu çağırarak BIR dizinin ACL 'sini alın. ACL girdilerinin listesini [Pathaccesscontrollistentry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry)türünde yeni bir **liste** nesnesine kopyalayın. Ardından, güncelleştirmek istediğiniz girdiyi bulun ve listede değiştirin. [Datalakedirectoryclient. setAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) metodunu çağırarak ACL 'yi ayarlayın.

Bu örnek, `my-parent-directory` diğer tüm kullanıcılar için girişi değiştirerek adlı bir DIZININ ACL 'sini günceller. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACL":::

Ayrıca, bir kapsayıcının kök dizininin ACL 'sini de alabilir ve ayarlayabilirsiniz. Kök dizini almak için `""` **Datalakefilesystemclient. getdirectoryclient** metoduna boş bir dize () geçirin.

### <a name="update-acls-recursively"></a>ACL 'Leri yinelemeli olarak güncelleştir

Bir ACL 'yi yinelemeli olarak güncelleştirmek için, güncelleştirmek istediğiniz ACL girdisiyle yeni bir ACL nesnesi oluşturun ve ACL 'yi Güncelleştir işleminde bu nesneyi kullanın. Mevcut ACL 'yi kullanmayın, yalnızca görüntülenecek ACL girdilerini sağlayın.

**Datalakedirectoryclient. Updateaccesscontrolözyinelemeli** yöntemini çağırarak ACL 'leri yinelemeli olarak güncelleştirin.  Bu yöntemi [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) nesnelerinin bir [listesini](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) geçirin. Her [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) bir ACL girdisi tanımlar. 

**Varsayılan** bir ACL girişini güncelleştirmek Istiyorsanız, [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) ve pass öğesinin **setdefaultscope** yöntemine **doğru** değerini verebilirsiniz. 

Bu örnekte, yazma izniyle bir ACL girişi güncelleştirilir. Bu yöntem `isDefaultScope` , varsayılan ACL 'nin güncelleştirilmesini isteyip istemediğinizi belirten adlı bir Boole parametresini kabul eder. Bu parametre, [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)öğesinin **setdefaultscope** metoduna yapılan çağrıda kullanılır. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACLRecursively":::

## <a name="remove-acl-entries"></a>ACL girdilerini kaldır

Bir veya daha fazla ACL girişini kaldırabilirsiniz. Bu bölümde nasıl yapılacağı gösterilmektedir:

- ACL girişini kaldırma
- ACL girdilerini yinelemeli olarak kaldır

### <a name="remove-an-acl-entry"></a>ACL girişini kaldırma

İlk olarak, [Pathaccesscontrol. getAccessControlList](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist) metodunu çağırarak BIR dizinin ACL 'sini alın. ACL girdilerinin listesini [Pathaccesscontrollistentry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry)türünde yeni bir **liste** nesnesine kopyalayın. Sonra kaldırmak istediğiniz girdiyi bulun ve **list** nesnesinin **Remove** metodunu çağırın. [Datalakedirectoryclient. setAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) metodunu ÇAĞıRARAK güncelleştirilmiş ACL 'yi ayarlayın.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>ACL girdilerini yinelemeli olarak kaldır

ACL girdilerini yinelemeli olarak kaldırmak için, ACL girdisi kaldırılacak yeni bir ACL nesnesi oluşturun ve ardından ACL 'yi kaldır işleminde bu nesneyi kullanın. Mevcut ACL 'yi almaz, kaldırılacak ACL girdilerini sağlamanız yeterlidir.

**Datalakedirectoryclient. removeAccessControlRecursive** YÖNTEMINI çağırarak acl girişlerini kaldırın. Bu yöntemi [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) nesnelerinin bir [listesini](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) geçirin. Her [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) bir ACL girdisi tanımlar. 

**Varsayılan** bir ACL girişini kaldırmak Istiyorsanız, [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) ve pass öğesinin **setdefaultscope** yöntemine **true** değerini verebilirsiniz.  

Bu örnek, adlı dizinin ACL 'sinden bir ACL girişini kaldırır `my-parent-directory` . Bu yöntem `isDefaultScope` , girişin varsayılan ACL 'den kaldırılması gerekip gerekmediğini belirten adlı bir Boole parametresini kabul eder. Bu parametre, [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)öğesinin **setdefaultscope** metoduna yapılan çağrıda kullanılır.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLRecursively":::

## <a name="recover-from-failures"></a>Hatalardan kurtarma

Çalışma zamanı veya izin hatalarıyla karşılaşabilirsiniz. Çalışma zamanı hataları için işlemi baştan başlatın. Güvenlik sorumlusu, değiştirilen Dizin hiyerarşisindeki bir dizinin veya dosyanın ACL 'sini değiştirmek için yeterli izne sahip değilse, izin hataları oluşabilir. İzin sorununu çözün ve sonra bir devamlılık belirteci kullanarak işlemi hata noktasından sürdürmeyi seçin ya da işlemi baştan başlatın. Baştan başlatmayı tercih ediyorsanız devamlılık belirtecini kullanmanız gerekmez. ACL girdilerini negatif bir etki olmadan yeniden uygulayabilirsiniz.

Bu örnek hata durumunda bir devamlılık belirteci döndürür. Uygulama, hata bağlandıktan sonra bu örnek yöntemi yeniden çağırabilir ve devamlılık belirtecini geçiyolabilir. Bu örnek yöntem ilk kez çağrılırsa, uygulama `null` devamlılık belirteci parametresi için değerini geçirebilir.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ResumeSetACLRecursively":::

İşlemin, izin hatalarıyla kesintiye uğramadan tamamlanmasını istiyorsanız, bunu belirtebilirsiniz.

İşlemin kesintisiz tamamlanmasını sağlamak için bir [PathSetAccessControlRecursiveOptions](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) nesnesinin **Setdevam OnFailure** metodunu çağırın ve **true** değerini geçirin.

Bu örnek ACL girdilerini yinelemeli olarak ayarlar. Bu kod bir izin hatasıyla karşılaşırsa, hatayı kaydeder ve yürütmeye devam eder. Bu örnek konsola başarısızlık sayısını yazdırır.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ContinueOnFailure":::

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Ayrıca bkz.

- [API başvuru belgeleri](/java/api/overview/azure/storage-file-datalake-readme)
- [Paket (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
- [Örnekler](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
- [Gen1 to Gen2 Mapping](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Bilinen sorunlar](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Geri bildirimde bulunun](https://github.com/Azure/azure-sdk-for-java/issues)
- [Azure Data Lake Storage 2. 'de erişim denetimi modeli](data-lake-storage-access-control.md)
- [Azure Data Lake Storage 2. 'de erişim denetim listeleri (ACL 'Ler)](data-lake-storage-access-control.md)