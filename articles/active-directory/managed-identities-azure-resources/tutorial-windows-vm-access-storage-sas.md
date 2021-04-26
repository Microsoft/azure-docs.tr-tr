---
title: Öğretici `:` SAS kimlik bilgilerini kullanarak Azure depolama 'ya erişmek için yönetilen kimliği kullanma-Azure AD
description: Depolama hesabı erişim anahtarı yerine SAS kimlik bilgilerini kullanarak Azure depolama 'ya erişmek için Windows VM sistem tarafından atanan bir yönetilen kimliğin nasıl kullanılacağını gösteren bir öğretici.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4f8b23d8f717e430e865e391a40692773f0beace
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776402"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage-via-a-sas-credential"></a>Öğretici: SAS kimlik bilgileri aracılığıyla Azure depolama 'ya erişmek için Windows VM sistem tarafından atanan bir yönetilen kimlik kullanma

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Bu öğreticide, bir Windows sanal makinesi (VM) için bir depolama paylaşılan erişim Imzası (SAS) kimlik bilgisi almak üzere sistem tarafından atanan bir kimliğin nasıl kullanılacağı gösterilmektedir. Özellikle, bir [Hizmet SAS kimlik bilgileri](../../storage/common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

Bir hizmet SAS, bir hesap erişim anahtarı açığa çıkarmadan sınırlı süre ve belirli bir hizmet (bizim örneğimizde, blob hizmeti) için bir depolama hesabındaki nesnelere sınırlı erişim verme olanağı sağlar. Depolama işlemleri yaparken, örneğin Depolama SDK'sını kullanırken SAS kimlik bilgilerini olağan şekilde kullanabilirsiniz. Bu öğreticide, Azure Storage PowerShell kullanarak bir blobu karşıya yüklemeyi ve indirmeyi gösteririz. Şunları öğrenirsiniz:

> [!div class="checklist"]
> * Depolama hesabı oluşturma
> * VM'nize Resource Manager'da yer alan depolama hesabı SAS için erişim verme 
> * VM'nizin kimliğini kullanarak erişim belirteci alma ve Resource Manager'dan SAS almak için bu belirteci kullanma 

## <a name="prerequisites"></a>Önkoşullar

- Yönetilen kimliklerin anlaşılmasıdır. Azure kaynakları için yönetilen kimlikler özelliği hakkında bilgi sahibi değilseniz bu [genel bakışı](overview.md) inceleyin. 
- Azure hesabı, [ücretsiz bir hesap için kaydolun](https://azure.microsoft.com/free/).
- Gerekli kaynak oluşturma ve rol yönetimi adımlarını gerçekleştirmek için uygun kapsamda (aboneliğiniz veya kaynak grubunuz) "sahip" izinleri. Rol atama ile ilgili yardıma ihtiyacınız varsa, [Azure abonelik kaynaklarınıza erişimi yönetmek Için Azure rolleri atama](../../role-based-access-control/role-assignments-portal.md)konusuna bakın.
- Ayrıca, sistem tarafından atanmış Yönetilen kimlikler etkinleştirilmiş bir Windows sanal makinesine de ihtiyacınız vardır.
  - Bu öğretici için bir sanal makine oluşturmanız gerekiyorsa, [sistem tarafından atanan kimliğin etkin olduğu bir sanal makine oluşturma](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) başlıklı makaleyi izleyebilirsiniz.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma 

Henüz bir depolama hesabınız yoksa, şimdi oluşturacaksınız. Ayrıca, bu adımı atlayabilir ve sanal makinenizin sistem tarafından atanan yönetilen kimlik erişimine mevcut bir depolama hesabının SAS kimlik bilgilerine izin verebilirsiniz. 

1. Azure portalının sol üst köşesinde bulunan **+/Yeni hizmet oluştur** düğmesine tıklayın.
2. **Depolama**'ya ve **Depolama Hesabı**'na tıklayın; yeni bir "Depolama hesabı oluştur" paneli görüntülenir.
3. Daha sonra kullanacağınız depolama hesabı için bir ad girin.  
4. **Dağıtım modeli** ve **Hesap türü** sırasıyla "Kaynak Yöneticisi" ve "genel amaçlı" olarak ayarlanmalıdır. 
5. **Abonelik** ve **Kaynak Grubu** değerlerinin, önceki adımda VM'nizi oluştururken belirttiklerinizle eşleştiğinden emin olun.
6. **Oluştur**’a tıklayın.

    ![Yeni depolama hesabı oluşturma](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Depolama hesabında bir blob kapsayıcısı oluşturma

Daha sonra yeni depolama hesabına dosya yükleyecek ve indireceğiz. Dosyalar için blob depolaması gerektiğinden, dosyanın depolanacağı bir blob kapsayıcısı oluşturmalıyız.

1. Yeni oluşturulan depolama hesabınıza geri gidin.
2. Sol tarafta, "Blob hizmeti" öğesinin altındaki **Kapsayıcılar** bağlantısına tıklayın.
3. Sayfanın en üstündeki **+ Kapsayıcı**'ya tıklayın; "Yeni kapsayıcı" paneli belirir.
4. Kapsayıcıya bir ad verin, erişim düzeyini seçin ve ardından **Tamam**'a tıklayın. Belirttiğiniz ad bu öğreticide daha sonra kullanılacaktır. 

    ![Depolama kapsayıcısı oluşturma](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-a-storage-sas"></a>Depolama SAS değerini kullanmak için VM'nize sistem tarafından atanan yönetilen kimliği erişimi verme 

Azure Depolama Azure AD kimlik doğrulamayı yerel olarak desteklemez.  Ancak, Kaynak Yöneticisi 'dan bir depolama SAS almak için yönetilen bir kimlik kullanabilir, sonra da SAS kullanarak depolamaya erişebilirsiniz.  Bu adımda, VM sistem tarafından atanan yönetilen kimliğinize depolama hesabının SAS değeri için erişim verirsiniz.   

1. Yeni oluşturulan depolama hesabınıza geri gidin.   
2. Sol bölmedeki **Erişim denetimi (IAM)** bağlantısına tıklayın.  
3. VM 'niz için yeni bir rol ataması eklemek üzere sayfanın üstünde **+ rol ataması Ekle** ' ye tıklayın
4. Sayfanın sağ tarafında, **Rol** olarak "Depolama Hesabı Katılımcısı" seçeneğini ayarlayın.  
5. Sonraki açılan listede **Erişimin atanacağı hedef** olarak "Sanal Makine" seçeneğini ayarlayın.  
6. Ardından, uygun aboneliğin **Abonelik**’te listelendiğinden emin olun ve sonra **Kaynak Grubu**’nu "Tüm kaynak grupları" olarak ayarlayın.  
7. Son olarak, **Seç**'in altındaki açılan listede Windows Sanal Makinenizi seçin ve **Kaydet**'e tıklayın. 

    ![Alternatif resim metni](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>VM kimliğini kullanarak erişim belirteci alma ve Azure Resource Manager çağrısı yapmak için bunu kullanma 

Öğreticinin geri kalanı için sanal makinenizde çalışacağız.

Bu bölümde Azure Resource Manager PowerShell cmdlet’lerini kullanmanız gerekir.  Bunu yüklemediyseniz, devam etmeden önce [en son sürümünü indirin](/powershell/azure/).

1. Azure portalında **Sanal Makineler**'e gidin, Windows sanal makinenize gidin ve ardından **Genel Bakış** sayfasında üst kısımdaki **Bağlan**'a tıklayın.
2. Windows VM 'yi oluştururken eklediğiniz **Kullanıcı adınızı** ve **parolanızı** girin. 
3. Artık sanal makineyle bir **Uzak Masaüstü bağlantısı** oluşturduğunuza göre.
4. PowerShell 'i uzak oturumda açın ve Azure kaynakları uç noktası için yerel yönetilen kimlikten bir Azure Resource Manager belirteci almak için Invoke-WebRequest kullanın.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > "Resource" parametre değeri Azure AD'nin beklediği değerle tam olarak eşleşmelidir. Azure Resource Manager kaynak kimliği kullanıldığında, URI'nin sonundaki eğik çizgiyi de eklemelisiniz.
    
    Ardından, $response nesnesinde JavaScript Nesne Gösterimi (JSON) biçimlendirilmiş dizesi olarak depolanan “Content” öğesini ayıklayın. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Ardından, yanıttan erişim belirtecini ayıklayın.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Depolama çağrıları yapmak için Azure Resource Manager'dan SAS kimlik bilgileri alma 

Şimdi, bir depolama SAS kimlik bilgileri oluşturmak için önceki bölümde elde ettiğimiz erişim belirtecini kullanarak Kaynak Yöneticisi çağırmak için PowerShell 'i kullanın. SAS kimlik bilgilerine sahip olduktan sonra depolama işlemlerini çağırabiliriz.

Bu istek için, aşağıdaki HTTP istek parametrelerini kullanarak SAS kimlik bilgilerini oluşturacağız:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Bu parametreler SAS kimlik bilgileri için isteğin POST gövdesine eklenmiştir. SAS kimliği oluştururken kullanılan parametreler hakkında daha fazla bilgi için bkz. [Liste Hizmeti SAS REST başvurusu](/rest/api/storagerp/storageaccounts/listservicesas).

İlk olarak, parametreleri JSON olarak dönüştürdükten sonra, `listServiceSas` SAS kimlik bilgisini oluşturmak için depolama uç noktasını çağırın:

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> URL büyük/küçük harfe duyarlıdır; dolayısıyla daha önce Kaynak Grubunu adlandırırken kullandığınız büyük/küçük harf düzenini kullanmaya dikkat edin ("resourceGroups" adındaki büyük "G" harfi dahil). 

Artık SAS kimlik bilgisini yanıttan ayıklayabiliriz:

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

SAS kimlik bilgileri 'ni inceleyerek şuna benzer bir şey görürsünüz:

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

Sonra "test.txt" adlı bir dosya oluştururuz. Ardından cmdlet ile kimlik doğrulamak için SAS kimlik bilgisini kullanın `New-AzStorageContent` , dosyayı blob kapsayımuza yükleyin, sonra dosyayı indirin.

```bash
echo "This is a test text file." > test.txt
```

Önce, `Install-Module Azure.Storage` kullanarak Azure Depolama cmdlet'lerini yüklediğinizden emin olun. Sonra `Set-AzStorageBlobContent` PowerShell cmdlet'ini kullanarak yeni oluşturduğunuz blobu karşıya yükleyin:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <STORAGE-ACCOUNT-NAME> -SasToken $sasCred
Set-AzStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Yanıt:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

Ayrıca `Get-AzStorageBlobContent` PowerShell cmdlet'ini kullanarak yeni karşıya yüklediğiniz blobu da indirebilirsiniz:

```powershell
Get-AzStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Yanıt:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir SAS kimlik bilgisi kullanarak Azure depolama 'ya erişmek için Windows VM 'nin sistem tarafından atanan yönetilen kimliğini nasıl kullanacağınızı öğrendiniz.  Azure Depolama SAS hakkında daha fazla bilgi edinmek için bkz:

> [!div class="nextstepaction"]
>[Paylaşılan erişim imzaları (SAS) kullanma](../../storage/common/storage-sas-overview.md)
