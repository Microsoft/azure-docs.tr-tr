---
title: Azure Key Vault depolanan bir gizli dizi, anahtar veya sertifikayı yedekleyin | Microsoft Docs
description: Azure Key Vault depolanan bir gizli anahtarı, anahtarı veya sertifikayı yedeklemeye yardımcı olması için bu belgeyi kullanın.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 3/18/2021
ms.author: sudbalas
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 399f55d379f99a784fed97d7e9f72c456eb1f914
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484821"
---
# <a name="azure-key-vault-backup"></a>Azure Key Vault yedekleme

Bu belgede, Anahtar Kasanızda saklanan gizli dizileri, anahtarları ve sertifikaları nasıl yedekleyeceğiniz gösterilmektedir. Yedekleme, önemli olmayan olayda anahtar kasanıza erişiminizi kaybetmeniz için size tüm gizli dizilerinizi çevrimdışı bir şekilde bir kopyasını sağlamaya yöneliktir.

## <a name="overview"></a>Genel Bakış

Azure Key Vault, kullanılabilirliği korumanıza ve veri kaybını önlemenize yardımcı olacak özellikler otomatik olarak sağlanır. Gizli dizileri yalnızca kritik bir iş gerekçenizi yedeklemeniz durumunda yedekleyin. Anahtar Kasanızda gizli dizi yedeklemesi, parolaların süre dolduğunda veya döndürürken birden çok günlük, izin ve yedek kümesini koruma gibi işlem sorunları ortaya çıkarabilir.

Key Vault olağanüstü durum senaryolarında kullanılabilirliği korur ve Kullanıcı müdahalesi olmadan eşleştirilmiş bir bölgeye istekleri otomatik olarak devreder. Daha fazla bilgi için bkz. [kullanılabilirlik ve yedeklilik Azure Key Vault](./disaster-recovery-guidance.md).

Gizli dizileri yanlışlıkla veya kötü amaçlı olarak silmeye karşı koruma istiyorsanız, Anahtar Kasanızda geçici silme ve koruma özelliklerini temizleyin. Daha fazla bilgi için bkz. [Azure Key Vault geçici genel bakış](./soft-delete-overview.md).

## <a name="limitations"></a>Sınırlamalar

> [!IMPORTANT]
> Key Vault, bir anahtar, gizli dizi ya da sertifika nesnesinin 500 ' den fazla sürümünü yedekleme olanağını desteklemez. Anahtar, gizli dizi ya da sertifika nesnesi yedeklenmeye çalışılması hataya neden olabilir. Bir anahtar, gizli dizi ya da sertifikanın önceki sürümlerini silmek mümkün değildir.

Key Vault Şu anda tek bir işlemde tüm anahtar kasasını yedeklemek için bir yol sağlamıyor. Bir anahtar kasasının otomatik yedeklemesini yapmak için bu belgede listelenen komutları kullanma girişimleri hatalara neden olabilir ve Microsoft veya Azure Key Vault ekibi tarafından desteklenmez. 

Ayrıca aşağıdaki sonuçları göz önünde bulundurun:

* Birden çok sürüme sahip gizli dizileri yedeklemek zaman aşımı hatalarına neden olabilir.
* Bir yedekleme, bir zaman noktası anlık görüntüsü oluşturur. Gizlilikler bir yedekleme sırasında yenilenebilir ve şifreleme anahtarlarının eşleşmemesi nedeniyle bu durum oluşabilir.
* Saniyedeki istek için Anahtar Kasası hizmeti sınırlarını aşarsanız, anahtar kasanızın kısıtlanacak ve yedekleme başarısız olur.

## <a name="design-considerations"></a>Tasarım konusunda dikkat edilmesi gerekenler

Gizli dizi, anahtar veya sertifika gibi bir Anahtar Kasası nesnesini yedeklerken, yedekleme işlemi nesneyi şifreli bir blob olarak indirir. Bu Blobun Azure dışından şifresi çözülemiyor. Bu bloba kullanılabilir verileri almak için, blobu aynı Azure aboneliği ve [Azure Coğrafya](https://azure.microsoft.com/global-infrastructure/geographies/)içindeki bir anahtar kasasında geri yüklemeniz gerekir.

## <a name="prerequisites"></a>Önkoşullar

Bir Anahtar Kasası nesnesini yedeklemek için, şunları yapmanız gerekir: 

* Azure aboneliğinde katkıda bulunan düzeyi veya daha yüksek izinler.
* Yedeklemek istediğiniz gizli dizileri içeren bir birincil anahtar Kasası.
* Gizliliklerin geri yükleneceği ikincil Anahtar Kasası.

## <a name="back-up-and-restore-from-the-azure-portal"></a>Azure portal yedekleme ve geri yükleme

Azure portal kullanarak nesneleri yedeklemek ve geri yüklemek için bu bölümdeki adımları izleyin.

### <a name="back-up"></a>Yedekleme

1. Azure portala gidin.
2. Anahtar kasanızı seçin.
3. Yedeklemek istediğiniz nesneye (gizli, anahtar veya sertifika) gidin.

    ![Anahtar kasasındaki anahtarlar ayarının ve bir nesnenin nerede seçileceğini gösteren ekran görüntüsü.](../media/backup-1.png)

4. Nesneyi seçin.
5. **Yedeklemeyi indir**' i seçin.

    ![Anahtar kasasında yedeği Indir düğmesinin nerede seçileceğini gösteren ekran görüntüsü.](../media/backup-2.png)
    
6. **İndir**'i seçin.

    ![Anahtar kasasında Indir düğmesinin nerede seçileceğini gösteren ekran görüntüsü.](../media/backup-3.png)
    
7. Şifrelenmiş blobu güvenli bir yerde depolayın.

### <a name="restore"></a>Geri Yükleme

1. Azure portala gidin.
2. Anahtar kasanızı seçin.
3. Geri yüklemek istediğiniz nesne türüne (gizli, anahtar veya sertifika) gidin.
4. **Yedeği geri yükle**' yi seçin.

    ![Anahtar kasasında yedeklemenin geri yükleme seçimini gösteren ekran görüntüsü.](../media/backup-4.png)
    
5. Şifrelenmiş blob 'u depoladığınız konuma gidin.
6. **Tamam**’ı seçin.

## <a name="back-up-and-restore-from-the-azure-cli-or-azure-powershell"></a>Azure CLı veya Azure PowerShell yedekleme ve geri yükleme

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
## Log in to Azure
az login

## Set your subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a provider
az provider register -n Microsoft.KeyVault

## Back up a certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```azurepowershell
## Log in to Azure
Connect-AzAccount

## Set your subscription
Set-AzContext -Subscription '{AZURE SUBSCRIPTION ID}'

## Back up a certificate in Key Vault
Backup-AzKeyVaultCertificate -VaultName '{Certificate Name}' -Name '{Key Vault Name}'

## Back up a key in Key Vault
Backup-AzKeyVaultKey -VaultName '{Key Name}' -Name '{Key Vault Name}'

## Back up a secret in Key Vault
Backup-AzKeyVaultSecret -VaultName '{Key Vault Name}' -Name '{Secret Name}'

## Restore a certificate in Key Vault
Restore-AzKeyVaultCertificate -VaultName '{Key Vault Name}' -InputFile '{File Path}'

## Restore a key in Key Vault
Restore-AzKeyVaultKey -VaultName '{Key Vault Name}' -InputFile '{File Path}'

## Restore a secret in Key Vault
Restore-AzKeyVaultSecret -VaultName '{Key Vault Name}' -InputFile '{File Path}'
```
---

## <a name="next-steps"></a>Sonraki adımlar

Key Vault için [günlüğe kaydetmeyi ve izlemeyi](./logging.md) açın.
