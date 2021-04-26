---
title: Azure Stack Edge Pro GPU ile verileri depolama hesabına aktarma öğreticisi | Microsoft Docs
description: Azure Stack Edge Pro GPU cihazındaki yerel ve uç depolama hesaplarına nasıl ekleneceğini ve bağlanalabileceğinizi öğrenin.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: d20ade0fdf6262c08a4ae961d17b1ec8a9ba39f2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065812"
---
# <a name="tutorial-transfer-data-via-storage-accounts-with-azure-stack-edge-pro-gpu"></a>Öğretici: Azure Stack Edge Pro GPU ile depolama hesapları aracılığıyla veri aktarımı 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Bu öğreticide, Azure Stack Edge Pro cihazınıza depolama hesaplarının nasıl ekleneceğini ve bu hesapların nasıl bağlanacağı açıklanmaktadır. Depolama hesapları eklendikten sonra Azure Stack Edge Pro, verileri Azure 'a aktarabilir.

Bu yordamın tamamlanması yaklaşık 30 dakika sürebilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Depolama hesabı ekleme
> * Depolama hesabına Bağlan

 
## <a name="prerequisites"></a>Önkoşullar

Azure Stack Edge Pro 'ya depolama hesapları eklemeden önce şunları yaptığınızdan emin olun:

- Fiziksel cihazınızı [yükleme Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md)' da anlatıldığı şekilde yüklediniz.

- Fiziksel cihazı, [Azure Stack Edge Pro 'Yu etkinleştirme](azure-stack-edge-gpu-deploy-activate.md)bölümünde açıklandığı gibi etkinleştirdiniz.


## <a name="add-an-edge-storage-account"></a>Edge depolama hesabı ekleme

Bir Edge depolama hesabı oluşturmak için aşağıdaki yordamı uygulayın:

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]


## <a name="connect-to-the-edge-storage-account"></a>Edge depolama hesabına bağlanma

Artık, *http* veya *https* üzerinden Edge depolama REST API 'lerine bağlanabilirsiniz.

- *Https* güvenli ve önerilen bir yoldur.
- Güvenilen ağlar üzerinden bağlanılırken *http* kullanılır.

## <a name="connect-via-http"></a>Http üzerinden Bağlan

Uç depolama REST API 'Lerine http üzerinden bağlantı aşağıdaki adımları gerektirir:

- Azure tutarlı hizmet VIP ve BLOB hizmeti uç noktasını uzak konağa ekleme
- Bağlantıyı doğrulama 

Bu adımların her biri aşağıdaki bölümlerde açıklanmıştır.

### <a name="add-device-ip-address-and-blob-service-endpoint-to-the-remote-client"></a>Uzak istemciye cihaz IP adresi ve BLOB hizmeti uç noktası ekle

[!INCLUDE [Add device IP and blob service endpoint to host file](../../includes/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint.md)]


### <a name="verify-connection"></a>Bağlantıyı doğrulayın

Bağlantıyı doğrulamak için, genellikle önceki adımda topladığınız aşağıdaki bilgiler (farklılık gösterebilir) gerekir:

- Depolama hesabı adı.
- Depolama hesabı erişim anahtarı.
- Blob hizmeti uç noktası.

Depolama hesabı adı ve BLOB hizmeti uç noktası zaten var. Azure Resource Manager bir Azure PowerShell istemcisi kullanarak cihaza bağlanarak depolama hesabı erişim anahtarını alabilirsiniz.

[Azure Resource Manager aracılığıyla cihaza bağlanma](azure-stack-edge-gpu-connect-resource-manager.md)bölümündeki adımları izleyin. Azure Resource Manager aracılığıyla yerel cihaz API 'Lerinde oturum açtıktan sonra, cihazdaki depolama hesaplarının listesini alın. Aşağıdaki cmdlet'i çalıştırın:

`Get-AzureRMStorageAccount`

Cihazdaki depolama hesapları listesinden, erişim anahtarı için gereken depolama hesabını belirleyebilirsiniz. Depolama hesabı adını ve kaynak grubunu aklınızda edin.

Örnek bir çıktı aşağıda gösterilmiştir:

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccount

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime          ProvisioningState EnableHttpsTrafficOnly
------------------ ----------------- -------- -------     ----    ---------- ------------          ----------------- ----------------------
myasetiered1       myasetiered1      DBELocal StandardLRS Storage            11/27/2019 7:10:12 PM Succeeded         False
```

Erişim anahtarını almak için aşağıdaki cmdlet 'i çalıştırın:

`Get-AzureRmStorageAccountAccessKey`

Örnek bir çıktı aşağıda gösterilmiştir:

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasetiered1
Name: myasetiered1

KeyName Value    Permissions                                                                                
------- -----    -----------                                                                                
key1    Jb2brrNjRNmArFcDWvL4ufspJjlo+Nie1uh8Mp4YUOVQNbirA1uxEdHeV8Z0dXbsG7emejFWI9hxyR1T93ZncA==        Full
key2    6VANuHzHcJV04EFeyPiWRsFWnHPkgmX1+a3bt5qOQ2qIzohyskIF/2gfNMqp9rlNC/w+mBqQ2mI42QgoJSmavg==        Full
```

Bu anahtarı kopyalayın ve kaydedin. Bu anahtarı, Azure Depolama Gezgini kullanarak bağlantıyı doğrulamak için kullanacaksınız.

Bağlantının başarıyla yapıldığını doğrulamak için, bir dış depolama hesabına eklemek için Depolama Gezgini kullanın. Depolama Gezgini yoksa, [Depolama Gezgini indirin](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409).

[!INCLUDE [Verify connection using Storage Explorer](../../includes/azure-stack-edge-gateway-verify-connection-storage-explorer.md)]


## <a name="connect-via-https"></a>Https üzerinden Bağlan

Https üzerinden Azure Blob depolama REST API 'Lerine yönelik bağlantı aşağıdaki adımları gerektirir:

- Blob uç noktası sertifikanızı alın
- Sertifikayı istemci veya uzak ana bilgisayara aktarma
- İstemci veya uzak konağa cihaz IP ve BLOB hizmeti uç noktası ekleme
- Bağlantıyı yapılandırma ve doğrulama

Bu adımların her biri aşağıdaki bölümlerde açıklanmıştır.

### <a name="get-certificate"></a>Sertifika Al

Blob depolamaya HTTPS üzerinden erişmek için, cihaz için bir SSL sertifikası gerekir. Ayrıca, bu sertifikayı Azure Stack Edge Pro cihazınıza iliştirilmiş bir özel anahtara sahip *. pfx* dosyası olarak yükleyebilirsiniz. Oluşturma hakkında daha fazla bilgi için (yalnızca test ve dev amaçları için) ve bu sertifikaları Azure Stack Edge Pro cihazınıza yükleyin, şuraya gidin:

- [BLOB uç noktası sertifikası oluşturun](azure-stack-edge-gpu-manage-certificates.md#create-certificates-optional).
- [BLOB uç noktası sertifikasını karşıya yükleyin](azure-stack-edge-gpu-manage-certificates.md#upload-certificates).
- [Cihaza erişen istemcideki sertifikaları Içeri aktarın](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).

### <a name="import-certificate"></a>Sertifikayı içeri aktarma

Cihazdaki depolama hesaplarına bağlanmak için Azure Depolama Gezgini kullanıyorsanız, sertifikayı ped biçiminde Depolama Gezgini içine aktarmanız gerekir. Windows ortamında, Base-64 Encoded *. cer* , ped biçimiyle aynıdır.

Azure Depolama Gezgini sertifikaları içeri aktarmak için aşağıdaki adımları uygulayın:

1. Azure Depolama Gezgini Azure Stack API 'Leri hedeflediğinden emin olun. **> hedef API 'leri Azure Stack Düzenle**' ye gidin. İstendiğinde, değişikliğin etkili olması için Depolama Gezgini yeniden başlatın.

2. SSL sertifikalarını içeri aktarmak için, **sertifikaları Içeri aktar > > SSL sertifikalarını Düzenle**' ye gidin.

  
   ![Sertifikayı Depolama Gezgini içeri aktar](./media/azure-stack-edge-gpu-deploy-add-storage-accounts/import-cert-storage-explorer-1.png) 

3. İmzalama zinciri ve BLOB sertifikalarına gidin ve sağlayın. İmzalama zinciri ve BLOB sertifikası ped biçiminde olmalıdır ve bu, Windows sisteminde Base64 kodlamalı biçimde aynı. Sertifikaların başarıyla içeri aktarıldığına ilişkin bir bildirim alırsınız.


### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Cihaz IP adresi ve BLOB hizmeti uç noktası ekle

[ *Http* ÜZERINDEN bağlanılırken cihaz IP adresi ve BLOB hizmeti uç noktası eklemek](#add-device-ip-address-and-blob-service-endpoint-to-the-remote-client)için aynı adımları izleyin.

### <a name="configure-and-verify-connection"></a>Bağlantıyı yapılandırma ve doğrulama

[ *Http* üzerinden bağlanırken kullandığınız bağlantıyı yapılandırmak ve doğrulamak](#verify-connection)için adımları izleyin. Tek fark, *http kullan seçeneğini* işaretlenmemiş olarak bırakmanız gerektiğidir.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, aşağıdaki Azure Stack Edge Pro konuları hakkında bilgi edindiniz:

> [!div class="checklist"]
> * Depolama hesabı ekleme
> * Bir depolama hesabına bağlanma

Azure Stack Edge Pro 'Yu kullanarak verilerinizi nasıl dönüştürebileceğinizi öğrenmek için sonraki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro ile veri dönüştürme](./azure-stack-edge-j-series-deploy-configure-compute.md)
