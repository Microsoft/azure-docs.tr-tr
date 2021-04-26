---
title: Azure yönetilen HSM günlüğü
description: Yönetilen HSM günlüğü ile çalışmaya başlamanıza yardımcı olması için bu öğreticiyi kullanın.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 03/30/2021
ms.author: mbaldwin
ms.openlocfilehash: 0d5749894fd277ff6a2f77e3db9721e6989d72ac
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109246"
---
# <a name="managed-hsm-logging"></a>Yönetilen HSM günlüğü 

Bir veya daha fazla yönetilen HSM oluşturduktan sonra muhtemelen, HSMss 'nizin nasıl ve ne zaman erişildiğini ve kim tarafından izleneceğini izlemek isteyeceksiniz. Bu, bilgileri sağladığınız bir Azure depolama hesabına kaydeden günlüğe kaydetmeyi etkinleştirerek yapabilirsiniz. Belirtilen depolama hesabınız için **Öngörüler-logs-auditevent** adlı yeni bir kapsayıcı otomatik olarak oluşturulur. Birden çok yönetilen HSM için günlükleri toplamak üzere bu depolama hesabını kullanabilirsiniz.

Günlük bilgilerinizin 10 dakikaya (en fazla), yönetilen HSM işleminden sonra erişebilirsiniz. Çoğu durumda, bundan daha hızlı olacaktır.  Depolama hesabınızdaki günlükleri istediğiniz şekilde yönetebilirsiniz:

* Günlüklerinize erişebilecek kişileri kısıtlayarak güvenliklerini sağlamak için standart Azure erişim denetimi yöntemlerini kullanın.
* Depolama hesabınızda tutmak istemediğiniz günlükleri silebilirsiniz.

Yönetilen HSM günlüğü ile çalışmaya başlamanıza yardımcı olması için bu öğreticiyi kullanın. Bir depolama hesabı oluşturacak, günlüğe kaydetmeyi etkinleştireceksiniz ve toplanan günlük bilgilerini yorumlayacak.  

> [!NOTE]
> Bu öğretici, yönetilen HSM 'ler veya anahtarlar oluşturma yönergelerini içermez. Bu makalede, tanılama günlüğünü güncelleştirmek için Azure CLı yönergeleri sağlanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları tamamlayabilmeniz için aşağıdaki öğelere sahip olmanız gerekir:

* Bir Microsoft Azure aboneliği. Hesabınız yoksa, [ücretsiz deneme için kaydolabilirsiniz](https://azure.microsoft.com/pricing/free-trial).
* Azure CLı sürüm 2.12.0 veya üzeri. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme]( /cli/azure/install-azure-cli).
* Aboneliğinizde yönetilen bir HSM. Bkz. [hızlı başlangıç: Azure CLI kullanarak](quick-create-cli.md) yönetilen bir HSM sağlama ve etkinleştirme.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Azure aboneliğinize bağlanma

Anahtar günlüğü ayarlamanın ilk adımı, Azure CLı 'yı günlüğe kaydetmek istediğiniz yönetilen HSM 'ye işaret etmek için kullanılır.

```azurecli-interactive
az login
```

CLı aracılığıyla oturum açma seçenekleri hakkında daha fazla bilgi için [Azure CLI ile oturum açma](/cli/azure/authenticate-azure-cli) bölümüne göz atın

Yönetilen HSM 'nizi oluşturmak için kullandığınız aboneliği belirtmeniz gerekebilir. Hesabınıza yönelik abonelikleri görmek için aşağıdaki komutu girin:

## <a name="identify-the-managed-hsm-and-storage-account"></a>Yönetilen HSM ve depolama hesabını tanımla

```azurecli-interactive
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name ContosoMHSMLogs --query id -o tsv)
```

## <a name="enable-logging"></a>Günlü kaydını etkinleştir

Yönetilen HSM için günlüğe kaydetmeyi etkinleştirmek üzere, yeni depolama hesabı ve yönetilen HSM için oluşturduğumuz değişkenlerle birlikte **az Monitor Diagnostic-Settings Create** komutunu kullanın. Ayrıca, **-Enabled** bayrağını **$true** olarak ayarlayacağız ve kategoriyi **auditevent** (yönetilen HSM günlüğü için tek kategori) olarak ayarlayacağız:

Bu çıktı, günlük kaydının yönetilen HSM 'niz için etkin olduğunu onaylar ve depolama hesabınıza bilgi kaydeder.

İsteğe bağlı olarak, günlüklerinizi için eski günlüklerin otomatik olarak silineceği bir bekletme ilkesi ayarlayabilirsiniz. Örneğin, **-RetentionEnabled** bayrağını **$true** olarak ayarlayarak bekletme ilkesi ayarlayın ve 90 günden eski günlüklerin otomatik olarak silinmesi için **-retentionındays** parametresini **90** olarak ayarlayın.

```azurecli-interactive
az monitor diagnostic-settings create --name ContosoMHSM-Diagnostics --resource $hsmresource --logs '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource
```

Günlüğe kaydedilenler:

* Erişim izinlerinin, sistem hatalarının veya hatalı isteklerin sonucu olarak başarısız istekler dahil tüm kimliği doğrulanmış REST API istekleri.
* Yönetilen HSM kaynağında, oluşturma, silme ve Etiketler gibi öznitelikleri güncelleştirme dahil olmak üzere yönetilen düzlem işlemleri.
* Başlatma & indirme, kurtarma başlatma, yükleme gibi güvenlik etki alanına ilgili işlemler
* Tam HSM yedekleme, geri yükleme ve seçmeli geri yükleme işlemleri
* Rol atamaları oluşturma/görüntüleme/silme ve özel rol tanımlarını oluşturma/görüntüleme/silme gibi rol yönetimi işlemleri
* Anahtarlar üzerinde aşağıdakiler de dahil olmak üzere işlemler:
  * Anahtarları oluşturma, değiştirme veya silme.
  * Anahtarları imzalama, doğrulama, şifreleme, şifre çözme, sarmalama ve geri sarma, anahtarları listeleme.
  * Anahtar yedekleme, geri yükleme, Temizleme
* Bir 401 yanıtına neden olan kimliği doğrulanmamış istekler. Örnek olarak, hatalı biçimlendirilmiş veya geçerliliği olmayan ya da geçersiz bir belirtece sahip bir taşıyıcı belirteci olmayan isteklerdir.  

## <a name="access-your-logs"></a>Günlüklerinize erişme

Yönetilen HSM günlükleri, verdiğiniz depolama hesabındaki **Öngörüler-logs-auditevent** kapsayıcısında depolanır. Günlükleri görüntülemek için Blobları indirmeniz gerekir. Azure depolama hakkında daha fazla bilgi için bkz. [Azure CLI ile Blobları oluşturma, indirme ve listeleme](../../storage/blobs/storage-quickstart-blobs-cli.md).

Tek blob 'lar bir JSON olarak biçimlendirilen metin olarak depolanır. Bir örnek günlük girişine bakalım. Aşağıdaki örnekte, yönetilen HSM 'ye tam yedekleme oluşturma isteği gönderildiğinde günlük girişi gösterilmektedir.

```json
[
  {
    "TenantId": "766eaf62-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "time": "2020-08-31T19:52:39.763Z",
    "resourceId": "/SUBSCRIPTIONS/A1BA9AAA-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/MANAGEDHSMS/CONTOSOMHSM",
    "operationName": "BackupCreate",
    "operationVersion": "7.0",
    "category": "AuditEvent",
    "resultType": "Success",
    "properties": {
        "PoolType": "M-HSM",
        "sku_Family": "B",
        "sku_Name": "Standard_B1"
    },
    "durationMs": 488,
    "callerIpAddress": "X.X.X.X",
    "identity": "{\"claim\":{\"appid\":\"04b07795-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"http_schemas_microsoft_com_identity\":{\"claims\":{\"objectidentifier\":\"b1c52bf0-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"}},\"http_schemas_xmlsoap_org_ws_2005_05_identity\":{\"claims\":{\"upn\":\"admin@contoso.com\"}}}}",
    "clientInfo": "azsdk-python-core/1.7.0 Python/3.8.2 (Linux-4.19.84-microsoft-standard-x86_64-with-glibc2.29) azsdk-python-azure-keyvault/7.2",
    "correlationId": "8806614c-ebc3-11ea-9e9b-00155db778ad",
    "subnetId": "(unknown)",
    "httpStatusCode": 202,
    "PoolName": "mhsmdemo",
    "requestUri": "https://ContosoMHSM.managedhsm.azure.net/backup",
    "resourceGroup": "ContosoResourceGroup",
    "resourceProvider": "MICROSOFT.KEYVAULT",
    "resource": "ContosoMHSM",
    "resourceType": "managedHSMs"
  }
]
```



## <a name="use-azure-monitor-logs"></a>Azure İzleyici günlüklerini kullanma

Yönetilen HSM **auditevent** günlüklerini gözden geçirmek Için Azure izleyici günlüklerinde Key Vault çözümünü kullanabilirsiniz. Azure Izleyici günlüklerinde, verileri analiz etmek ve ihtiyacınız olan bilgileri almak için günlük sorgularını kullanırsınız.

Bunu ayarlama dahil daha fazla bilgi için bkz. [Azure izleyici 'de Azure Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Sonraki adımlar

- Yönetilen bir HSM sağlamak ve kullanmak için [en iyi uygulamalar](best-practices.md) hakkında bilgi edinin
- Yönetilen bir HSM ['Yi yedekleme ve geri yükleme](backup-restore.md) hakkında bilgi edinin
