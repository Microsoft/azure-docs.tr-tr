---
title: Azure İzleyici müşteri tarafından yönetilen anahtar
description: Azure Key Vault bir anahtar kullanarak Log Analytics çalışma alanlarınızdaki verileri şifrelemek üzere müşteri tarafından yönetilen anahtarı yapılandırmaya yönelik bilgiler ve adımlar.
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 01/10/2021
ms.openlocfilehash: 4033421095ead47e2bd1e97c4f2f42672644d7df
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364864"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure İzleyici müşteri tarafından yönetilen anahtar 

Azure Izleyici 'deki veriler, Microsoft tarafından yönetilen anahtarlarla şifrelenir. Çalışma Alanlarınızdaki verileri ve kaydedilen sorguları korumak için kendi şifreleme anahtarınızı kullanabilirsiniz. Müşteri tarafından yönetilen bir anahtar belirttiğinizde, bu anahtar verilerinize erişimi korumak ve denetlemek için kullanılır ve yapılandırıldıktan sonra, çalışma alanlarına gönderilen tüm veriler Azure Key Vault anahtarınızla şifrelenir. Müşteri tarafından yönetilen anahtarlar erişim denetimlerini yönetmek için çok daha fazla esneklik sunar.

Yapılandırmadan önce aşağıdaki [sınırlamaları ve kısıtlamaları](#limitationsandconstraints) incelemenizi öneririz.

## <a name="customer-managed-key-overview"></a>Müşteri tarafından yönetilen anahtara genel bakış

[Bekleyen şifreleme](../../security/fundamentals/encryption-atrest.md) , kuruluşlarda ortak bir gizlilik ve güvenlik gereksinimidir. Azure 'un bekleyen şifrelemeyi tamamen yönetmesine izin verebilir, şifreleme ve şifreleme anahtarlarını yakından yönetmek için çeşitli seçenekleriniz vardır.

Azure Izleyici, tüm veri ve kaydedilmiş sorguların Microsoft tarafından yönetilen anahtarlar (MMK) kullanılarak Rest 'te şifrelenmesini sağlar. Azure Izleyici Ayrıca, [Azure Key Vault](../../key-vault/general/overview.md)depolanan kendi anahtarınızı kullanarak şifreleme için bir seçenek sunar. Bu, size istediğiniz zaman verilerinize erişimi iptal etmeye yönelik bir denetim sağlar. Azure Izleyici şifreleme kullanımı, [Azure depolama şifrelemesiyle](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption) aynı şekilde çalışır.

Müşteri tarafından yönetilen anahtar, daha yüksek koruma düzeyi ve denetimi sağlayan [adanmış kümeler](./logs-dedicated-clusters.md) üzerine dağıtılır. Adanmış kümelere alınan veriler iki kez şifrelenir: Microsoft tarafından yönetilen anahtarlar veya müşteri tarafından yönetilen anahtarlar kullanılarak hizmet düzeyinde bir kez ve altyapı düzeyinde iki farklı şifreleme algoritması ve iki farklı anahtar kullanan bir kez. [Çift şifreleme](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) , şifreleme algoritmalarından veya anahtarlardan birinin tehlikeye girdiği bir senaryoya karşı koruma sağlar. Bu durumda, ek şifreleme katmanı verilerinizi korumaya devam eder. Adanmış küme ayrıca verilerinizi [kasa](#customer-lockbox-preview) denetimiyle korumanıza olanak sağlar.

Son 14 gün içinde alınan veriler, verimli sorgu altyapısı işlemi için etkin-önbellek (SSD-desteklenen) olarak da tutulur. Bu veriler, müşteri tarafından yönetilen anahtar yapılandırmasına bakılmaksızın Microsoft anahtarlarıyla şifreli olarak kalır, ancak SSD verileri üzerindeki denetiminiz [anahtar iptalinde](#key-revocation)kalır. 2021 ilk yarısında, SSD verilerinin müşteri tarafından yönetilen anahtarla şifrelenmesini sağlamak için çalışıyoruz.

Log Analytics adanmış kümeler 1000 GB/gün üzerinden başlayan bir kapasite ayırma [fiyatlandırma modeli](./logs-dedicated-clusters.md#cluster-pricing-model) kullanır.

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Azure Izleyici 'de müşteri tarafından yönetilen anahtarın nasıl çalıştığı

Azure Izleyici Azure Key Vault erişim sağlamak için yönetilen kimlik kullanır. Log Analytics kümesinin kimliği, küme düzeyinde desteklenir. Birden çok çalışma alanı üzerinde müşteri tarafından yönetilen anahtar korumasına izin vermek için yeni bir Log Analytics *küme* kaynağı, Key Vault ve Log Analytics çalışma alanlarınız arasında ara kimlik bağlantısı olarak gerçekleştirilir. Kümenin depolaması, \' Azure Active Directory üzerinden Azure Key Vault kimlik doğrulaması yapmak Için *küme* kaynağıyla ilişkili yönetilen kimliği kullanır. 

Müşteri tarafından yönetilen anahtar yapılandırmasından sonra adanmış kümenize bağlı olan çalışma alanlarına yeni alınan veriler anahtarınızla şifrelenir. Her zaman çalışma alanlarının kümeden bağlantısını kaldırabilirsiniz. Yeni veriler daha sonra, yeni ve eski verilerinizi sorunsuz bir şekilde sorgulayabilmeniz için Log Analytics depolama ve Microsoft anahtarıyla şifrelenmiş olarak alınır.

> [!IMPORTANT]
> Müşteri tarafından yönetilen anahtar özelliği bölgesel olarak belirlenir. Azure Key Vault, kümeniz ve bağlı Log Analytics çalışma alanlarınızın aynı bölgede olması gerekir, ancak bunlar farklı aboneliklerde olabilir.

![Müşteri tarafından yönetilen anahtara genel bakış](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log Analytics *küme* kaynağı, Key Vault izinleri olan yönetilen kimliğe sahip--kimlik, adanmış Log Analytics küme depolamasına yayılmıştır
3. Adanmış Log Analytics kümesi
4. *Küme* kaynağına bağlı çalışma alanları 

### <a name="encryption-keys-operation"></a>Şifreleme anahtarları işlemi

Depolama veri şifrelemesi ile ilgili 3 tür anahtar vardır:

- **Kek** anahtar şifreleme anahtarı (müşteri tarafından yönetilen anahtarınız)
- **AEK** hesabı şifreleme anahtarı
- **Dek** -veri şifreleme anahtarı

Aşağıdaki kurallar geçerlidir:

- Log Analytics küme depolama hesapları, AEK olarak bilinen her depolama hesabı için benzersiz şifreleme anahtarı oluşturur.
- AEK, diske yazılan her veri bloğunu şifrelemek için kullanılan anahtarlar olan DEKs 'leri türetmek için kullanılır.
- Key Vault ' de anahtarınızı yapılandırıp kümede buna başvurduğunuzda, Azure depolama, veri şifreleme ve şifre çözme işlemleri gerçekleştirmek için AEK 'i sarmalamak ve sarmalamak üzere Azure Key Vault istekleri gönderir.
- KEK 'niz Key Vault hiçbir şekilde ayrılmayacaktır.
- Azure depolama, Azure Active Directory üzerinden Azure Key Vault kimlik doğrulaması yapmak ve erişmek için *küme* kaynağıyla ilişkili yönetilen kimliği kullanır.

### <a name="customer-managed-key-provisioning-steps"></a>Customer-Managed anahtar sağlama adımları

1. Azure Key Vault oluşturma ve anahtar depolama
1. Küme oluşturuluyor
1. Key Vault izinler veriliyor
1. Anahtar tanımlayıcı ayrıntıları ile küme güncelleştiriliyor
1. Log Analytics çalışma alanlarını bağlama

Müşteri tarafından yönetilen anahtar yapılandırması şu anda Azure portal desteklenmez ve sağlama [PowerShell](/powershell/module/az.operationalinsights/), [CLI](/cli/azure/monitor/log-analytics) veya [rest](/rest/api/loganalytics/) istekleri aracılığıyla gerçekleştirilebilir.

### <a name="asynchronous-operations-and-status-check"></a>Zaman uyumsuz işlemler ve durum denetimi

Yapılandırma adımlarının bazıları hızla tamamlanamadığından zaman uyumsuz olarak çalışır. `status`In yanıtı, şu hata kodu ile birlikte ' InProgress ', ' Updating ', ' siliyor ', ' SUCCEEDED veya ' Failed ' gibi bir durumda olabilir.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Yok

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Yok

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Yok

# <a name="rest"></a>[REST](#tab/rest)

REST kullanılırken, yanıt başlangıçta HTTP durum kodu 202 (kabul edildi) ve üst bilgi olarak *Azure-AsyncOperation* özelliği döndürür:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

*Azure-AsyncOperation* üstbilgisindeki uç noktaya bir get isteği göndererek zaman uyumsuz işlemin durumunu kontrol edebilirsiniz:
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

---

## <a name="storing-encryption-key-kek"></a>Şifreleme anahtarını depolama (KEK)

Zaten oluşturmak için gereken bir Azure Key Vault oluşturun veya kullanın veya veri şifrelemesi için kullanılacak bir anahtarı içeri aktarın. Azure Key Vault anahtarınızı ve Azure Izleyici 'deki verilerinize erişimi korumak için kurtarılabilir olarak yapılandırılmalıdır. Bu yapılandırmayı Key Vault Özellikler altında doğrulayabilirsiniz, hem *geçici silme* hem de *Temizleme koruması* etkinleştirilmelidir.

![Geçici silme ve Temizleme koruması ayarları](media/customer-managed-keys/soft-purge-protection.png)

Bu ayarlar, CLı ve PowerShell aracılığıyla Key Vault güncelleştirilebilen:

- [Geçici Silme](../../key-vault/general/soft-delete-overview.md)
- Geçici silme işleminden sonra bile gizli/kasaların silinmesini zorlamak için [koruma koruyucuları temizle](../../key-vault/general/soft-delete-overview.md#purge-protection)

## <a name="create-cluster"></a>Küme oluşturma

Kümeler iki [yönetilen kimlik türünü](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)destekler: sistem tarafından atanan ve Kullanıcı tarafından atanan, senaryonuza bağlı olarak bir kümede tek bir kimlik tanımlanabilir. 
- Kimlik `type` "*Systemassigned*" olarak ayarlandığında, sistem tarafından atanan yönetilen kimlik daha basittir ve küme oluşturma ile otomatik olarak oluşturulur. Bu kimlik daha sonra, sarmalama ve sarmalama işlemleri için Key Vault depolama erişimi vermek üzere kullanılabilir. 
  
  Sistem tarafından atanan yönetilen kimlik için kümede kimlik ayarları
  ```json
  {
    "identity": {
      "type": "SystemAssigned"
      }
  }
  ```

- Küme oluştururken müşteri tarafından yönetilen anahtarı yapılandırmak istiyorsanız, önceden Key Vault için bir anahtar ve Kullanıcı tarafından atanan bir kimliğiniz olmalıdır ve sonra bu ayarlarla kümeyi oluşturmanız gerekir: kimlik, `type`  `UserAssignedIdentities` kimliğiniz *kaynak kimliğiyle* birlikte "userassigned" olarak atanır.

  Kullanıcı tarafından atanan yönetilen kimlik için kümede kimlik ayarları
  ```json
  {
  "identity": {
  "type": "UserAssigned",
    "userAssignedIdentities": {
      "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.ManagedIdentity/UserAssignedIdentities/<cluster-assigned-managed-identity>"
      }
  }
  ```

> [!IMPORTANT]
> Key Vault Private-Link (vNet) ise, Kullanıcı tarafından atanan yönetilen kimliği kullanamazsınız. Bu senaryoda, sistem tarafından atanan yönetilen kimliği kullanabilirsiniz.

[Adanmış kümeler](./logs-dedicated-clusters.md#creating-a-cluster)makalesinde gösterilen yordamı izleyin. 

## <a name="grant-key-vault-permissions"></a>Key Vault izinleri verme

Kümenize izin vermek için Key Vault erişim ilkesi oluşturun. Bu izinler, Azure Izleyici depolaması 'nın temelini oluşturmak tarafından kullanılır. Key Vault Azure portal açın ve bu ayarlarla bir ilke oluşturmak için *"erişim ilkeleri"* ve *"+ Erişim İlkesi Ekle"* seçeneğine tıklayın:

- Anahtar izinleri: *' Al '*, *' Wrap Key '* ve *' Wrap Key '* seçeneğini belirleyin.
- Asıl öğe seçin: kümede kullanılan kimlik türüne (sistem veya Kullanıcı tarafından atanan yönetilen kimlik) bağlı olarak, sistem tarafından atanan yönetilen kimlik veya Kullanıcı tarafından yönetilen kimlik adı için küme adı ya da küme asıl KIMLIĞI girin.

![Key Vault izinleri verme](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

Key Vault, anahtarınızı korumak ve Azure Izleyici verilerinize erişmek için kurtarılabilir olarak yapılandırıldığını doğrulamak için *Get* izni gerekir.

## <a name="update-cluster-with-key-identifier-details"></a>Anahtar tanımlayıcı ayrıntıları ile kümeyi güncelleştirme

Kümedeki tüm işlemler `Microsoft.OperationalInsights/clusters/write` eylem iznini gerektirir. Bu izin, eylemi içeren sahip veya katkıda bulunan veya `*/write` eylemi içeren Log Analytics katkıda bulunan rolü aracılığıyla verilebilir `Microsoft.OperationalInsights/*` .

Bu adım, Azure Izleyici depolama alanını veri şifreleme için kullanılacak anahtar ve sürümle güncelleştirir. Güncelleştirilirken, yeni anahtarınız depolama anahtarını sarmalamak ve sarmalamak için kullanılır (AEK).

Anahtar tanımlayıcı ayrıntılarını almak için Azure Key Vault ' de anahtarınızın güncel sürümünü seçin.

![Key Vault izinleri verme](media/customer-managed-keys/key-identifier-8bit.png)

Anahtar tanımlayıcı ayrıntıları ile kümedeki KeyVaultProperties 'i güncelleştirin.

>[!NOTE]
>Anahtar döndürme iki modu destekler: otomatik döndürme veya açık anahtar sürümü güncelleştirme, size en uygun yaklaşımı öğrenmek için bkz. [anahtar döndürme](#key-rotation) .

İşlem zaman uyumsuzdur ve tamamlanması biraz zaman alabilir.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Yok

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --key-name "key-name" --key-vault-uri "key-uri" --key-version "key-version"
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/cluster-name?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
  },
  "sku": {
    "name": "CapacityReservation",
    "capacity": 1000
  }
}
```

**Response**

Anahtarın kaç dakika boyunca yayılmasının tamamlanmasını sağlar. Güncelleştirme durumunu iki şekilde kontrol edebilirsiniz:
1. Yanıttan Azure-AsyncOperation URL değerini kopyalayın ve [zaman uyumsuz işlemler durum denetimini](#asynchronous-operations-and-status-check)izleyin.
2. Küme üzerinde bir GET isteği gönderin ve *Keyvaultproperties* özelliklerine bakın. Son güncelleştirdiğiniz anahtar, yanıtta döndürmelidir.

Anahtar güncelleştirmesi tamamlandığında GET isteğinin yanıtı şuna benzemelidir: 202 (kabul edildi) ve üst bilgi
```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
      },
    "provisioningState": "Succeeded",
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

---

## <a name="link-workspace-to-cluster"></a>Çalışma alanını kümeye bağla

> [!IMPORTANT]
> Bu adım yalnızca Log Analytics kümesi sağlama işleminin tamamlanmasından sonra gerçekleştirilmelidir. Çalışma alanlarını bağlarsanız ve sağlama öncesinde verileri içe alıyorsa, alınan veriler bırakılır ve geri alınamaz.

Ve dahil olmak üzere bu işlemi gerçekleştirmek için hem çalışma alanınız hem de kümeniz için ' Write ' izinlerinizin olması gerekir `Microsoft.OperationalInsights/workspaces/write` `Microsoft.OperationalInsights/clusters/write` .

[Adanmış kümeler](./logs-dedicated-clusters.md#link-a-workspace-to-cluster)makalesinde gösterilen yordamı izleyin.

## <a name="key-revocation"></a>Anahtar iptali

> [!IMPORTANT]
> - Verilerinize erişimi iptal etmenin önerilen yolu, Key Vault anahtarınızı devre dışı bırakarak veya erişim ilkesini silmenizi sağlar.
> - Kümenin `identity` `type` "none" olarak ayarlanması aynı zamanda verilerinize erişimi de iptal eder, ancak bu yaklaşım, destek isteği açılmadan restating içinde, iptal etme işleminden dönmemek için bu yaklaşım önerilmez `identity` .

Küme depolama, her zaman bir saat veya daha kısa bir süre içinde anahtar izinlerinde yapılacak değişikliklere göre değişir ve depolama alanı kullanılamaz hale gelir. Kümenizle bağlantılı çalışma alanlarına alınan yeni veriler bırakılır ve geri alınamaz, verilerin erişilemez hale gelir ve bu çalışma alanlarındaki sorgular başarısız olur. Önceden alınan veriler, kümeniz ve çalışma alanlarınız silinmediği sürece depolamada kalır. Erişilemeyen veriler veri bekletme ilkesine tabidir ve bekletmeye ulaşıldığında temizlenir. Son 14 gün içinde alınan veriler, verimli sorgu altyapısı işlemi için etkin-önbellek (SSD-desteklenen) olarak da tutulur. Bu, anahtar iptali işleminde silinir ve erişilemez duruma gelir.

Küme depolama, şifreleme anahtarını sarmalamadan ve erişildikten sonra, verilerin alımı ve sorgusunun 30 dakika içinde sürdürülmeye çalışılması için Key Vault düzenli olarak denetler.

## <a name="key-rotation"></a>Anahtar döndürme

Anahtar döndürme iki moda sahiptir: 
- Otomatik döndürme-kümenizi güncelleştirdiğinizde ```"keyVaultProperties"``` ancak özelliği atladığınızda ```"keyVersion"``` ya da olarak ayarlarsanız ```""``` , depolama en son sürümleri otomatik olarak kullanır.
- Açık anahtar sürüm güncelleştirmesi-kümenizi güncelleştirdiğinizde ve özellikte anahtar sürümü sağladığınızda ```"keyVersion"``` , yeni anahtar sürümlerinin kümede açık bir güncelleştirme olması gerekir ```"keyVaultProperties"``` , bkz. [anahtarı anahtar tanımlayıcı ayrıntıları ile güncelleştirme](#update-cluster-with-key-identifier-details). Key Vault ' de yeni anahtar sürümü oluşturursanız ancak kümede güncelleştirmezseniz, Log Analytics küme depolaması önceki anahtarınızı kullanmaya devam eder. Kümedeki yeni anahtarı güncelleştirmeden önce eski anahtarınızı devre dışı bırakır veya silerseniz, [anahtar iptal](#key-revocation) durumuna sahip olursunuz.

Verilerin her zaman Key Vault ' de yeni anahtar şifreleme anahtarı (KEK) ile şifrelenmesi sırasında, veriler her zaman hesap şifreleme anahtarıyla (AEK) şifrelendiğinden, tüm verileriniz anahtar döndürme işleminden sonra erişilebilir durumda kalır.

## <a name="customer-managed-key-for-saved-queries"></a>Kayıtlı sorgular için müşteri tarafından yönetilen anahtar

Log Analytics ' de kullanılan sorgu dili ifade edilebilir ve sorgulara eklediğiniz açıklamalarda veya sorgu söz diziminde gizli bilgiler içerebilir. Bazı kuruluşlar, bu tür bilgilerin müşteri tarafından yönetilen anahtar ilkesi altında korunmasını gerektirir ve sorgularınızı anahtarınızla şifreli olarak kaydetmeniz gerekir. Azure Izleyici, çalışma alanınıza bağlıyken kendi depolama hesabınızda anahtarınızla şifrelenen *kayıtlı aramaları* ve *günlük uyarıları* sorgularını depolamanıza olanak sağlar. 

> [!NOTE]
> Log Analytics sorguları, kullanılan senaryoya bağlı olarak çeşitli depolarda kaydedilebilir. Müşteri tarafından yönetilen anahtar yapılandırmasına bakılmaksızın sorgular Microsoft anahtarı (MMK) ile şifrelenmeye devam eder: Azure Izleyici, Azure panoları, Azure mantıksal uygulaması, Azure Notebooks ve Otomasyon Runbook 'larındaki çalışma kitapları.

Kendi depolama alanınızı (BYOS) getirip çalışma alanınıza bağladığınızda, hizmet *kayıtlı aramaları* ve *günlük uyarıları* sorgularını depolama hesabınıza yükler. Diğer bir deyişle, Log Analytics kümesindeki verileri şifrelemek için kullandığınız anahtarı veya farklı bir anahtarı kullanarak depolama hesabını ve [geri kalan şifreleme ilkesini](../../storage/common/customer-managed-keys-overview.md) denetlersiniz. Bununla birlikte, bu depolama hesabıyla ilişkili maliyetlerden de sorumlu olursunuz. 

**Sorgular için müşteri tarafından yönetilen anahtarı ayarlamadan önce dikkat edilecek noktalar**
* Hem çalışma alanınız hem de depolama hesabınızda ' Write ' izinlerine sahip olmanız gerekir
* Log Analytics çalışma alanınız bulunduğundan, depolama hesabınızı aynı bölgede oluşturduğunuzdan emin olun
* Depolamadaki *aramalar* , hizmet yapıtları olarak değerlendirilir ve bunların biçimi değişebilir
* Mevcut *kaydetme aramaları* çalışma alanınızdan kaldırılır. Yapılandırma öncesinde ihtiyacınız olan aramaları kopyalayın ve *kaydeder* . *Kayıtlı aramalarınızı* [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch) kullanarak görüntüleyebilirsiniz
* Sorgu geçmişi desteklenmiyor ve çalıştırdığınız sorguları göremezsiniz
* Sorguları kaydetmek amacıyla tek bir depolama hesabını çalışma alanına bağlayabilirsiniz, ancak her ikisi de *kaydedilmiş aramalar* ve *log-Alerts* sorguları kullanabilir.
* Panoya sabitle desteklenmiyor

**Kayıtlı arama sorguları için KCG 'LERI yapılandırma**

Çalışma alanınıza *sorgu* için bir depolama hesabı bağlayın-- *kayıtlı aramalar* sorguları depolama hesabınıza kaydedilir. 

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Yok

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type Query --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Yapılandırmadan sonra, yeni *Kaydedilmiş arama* sorgusu, depolama alanına kaydedilir.

**KCG 'LERI log-Alerts sorguları için yapılandırma**

Çalışma alanınıza *Uyarılar* için bir depolama hesabı bağlayın-- *log-Alerts* sorguları depolama hesabınıza kaydedilir. 

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Yok

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type ALerts --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Yapılandırmadan sonra, tüm yeni uyarı sorgusu depolama alanına kaydedilir.

## <a name="customer-lockbox-preview"></a>Müşteri Kasası (Önizleme)

Kasa, destek isteği sırasında verilerinize erişmek için Microsoft mühendis isteğini onaylama veya reddetme denetimi sağlar.

Azure Izleyici 'de, Log Analytics adanmış kümenize bağlı olan çalışma alanlarındaki veriler üzerinde bu denetime sahip olursunuz. Kasa denetimi, kasa korumalı aboneliğiniz altındaki kümenin depolama hesaplarında yalıtılmış olduğu Log Analytics adanmış bir kümede depolanan veriler için geçerlidir.  

[Microsoft Azure için müşteri kasası](../../security/fundamentals/customer-lockbox-overview.md) hakkında daha fazla bilgi edinin

## <a name="customer-managed-key-operations"></a>Customer-Managed anahtar işlemleri

Customer-Managed anahtar adanmış kümede verilmiştir ve bu işlemlere [adanmış küme makalesinde](./logs-dedicated-clusters.md#change-cluster-properties) başvurulur

- Kaynak grubundaki tüm kümeleri al  
- Abonelikteki tüm kümeleri al
- Kümede *Kapasite ayırmayı* Güncelleştir
- Kümede *Billingtype* 'ı güncelleştirme
- Çalışma alanının kümeden bağlantısını kaldırma
- Kümeyi silme

## <a name="limitations-and-constraints"></a>Sınırlamalar ve kısıtlamalar

- Bölge başına en fazla küme sayısı ve abonelik 2 ' dir

- Bir kümeye bağlanabilen maksimum çalışma alanı sayısı 1000 ' dir

- Bir çalışma alanını kümenize bağlayabilir ve sonra bağlantısını kesebilirsiniz. Belirli çalışma alanındaki çalışma alanı bağlantı işlemlerinin sayısı, 30 günlük bir dönemde 2 ile sınırlıdır.

- Müşteri tarafından yönetilen anahtar şifrelemesi, yapılandırma zamanından sonra yeni alınan veriler için geçerlidir. Yapılandırmadan önce alınan veriler, Microsoft anahtarıyla şifreli olarak kalır. Müşteri tarafından yönetilen anahtar yapılandırmasından önce ve sonra gelen verileri sorunsuz bir şekilde sorgulayabilirsiniz.

- Azure Key Vault kurtarılabilir olarak yapılandırılmalıdır. Bu özellikler varsayılan olarak etkinleştirilmemiştir ve CLı veya PowerShell kullanılarak yapılandırılmalıdır:<br>
  - [Geçici Silme](../../key-vault/general/soft-delete-overview.md)
  - Geçici silme işleminden sonra bile gizli/kasaların silinmesini zorlamak için [Temizleme koruması](../../key-vault/general/soft-delete-overview.md#purge-protection) açılmalıdır.

- Küme başka bir kaynak grubuna veya aboneliğe taşıma Şu anda desteklenmiyor.

- Azure Key Vault, kümeniz ve çalışma alanlarınızın aynı bölgede ve aynı Azure Active Directory (Azure AD) kiracısında olması gerekir, ancak bunlar farklı aboneliklerde olabilir.

- Kasa Çin 'de Şu anda kullanılamıyor. 

- Desteklenen bölgelerde, Ekim 2020 ' den oluşturulan kümeler için [Çift şifreleme](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) otomatik olarak yapılandırılır. Kümede bir GET isteği göndererek ve `isDoubleEncryptionEnabled` değerin `true` çift şifrelemeyi etkin kümeler için olduğunu gözlemleyerek, kümenizin Çift şifreleme için yapılandırılıp yapılandırılmadığını doğrulayabilirsiniz. 
  - Bir küme oluşturur ve "<Region-adı> kümeler için çift şifrelemeyi desteklemez.", REST istek gövdesine ekleyerek kümeyi, Çift şifrelemeyi oluşturmaya devam edebilirsiniz `"properties": {"isDoubleEncryptionEnabled": false}` .
  - Küme oluşturulduktan sonra çift şifreleme ayarı değiştirilemez.

  - Kümeniz Kullanıcı tarafından atanan yönetilen kimlik ile ayarlandıysa, `UserAssignedIdentities` ile ayarı `None` kümeyi askıya alır ve verilerinize erişimi önler, ancak iptali iptal edin ve destek isteği açılmadan kümeyi etkinleştirin. Bu sınırlama, sistem tarafından atanan yönetilen kimliğe uygulandı.

  - Key Vault Private-Link (vNet) ise, Kullanıcı tarafından yönetilen anahtarı Kullanıcı tarafından atanan yönetilen kimlikle kullanamazsınız. Bu senaryoda, sistem tarafından atanan yönetilen kimliği kullanabilirsiniz.

## <a name="troubleshooting"></a>Sorun giderme

- Key Vault kullanılabilirliği ile davranış
  - Normal işlemde--depolama, kısa süreler için AEK önbelleğe alınır ve düzenli aralıklarla sarmalaması için Key Vault geri gider.
    
  - Geçici bağlantı hataları--depolama, anahtarların kısa bir süre boyunca önbellekte kalmasına izin vererek geçici hataları (zaman aşımları, bağlantı hataları, DNS sorunları) işler ve bu da tüm küçük sinyalleri 'leri kullanılabilirliğinden fazla. Sorgu ve alma özellikleri kesinti olmadan devam eder.
    
  - Canlı site--yaklaşık 30 dakikalık bir işlem, depolama hesabının kullanılamaz hale gelmesine neden olur. Sorgu özelliği kullanılamaz ve veri kaybını önlemek için Microsoft anahtar kullanarak birkaç saat boyunca önbelleğe alınır. Key Vault erişimi geri yüklendiğinde, sorgu kullanılabilir hale gelir ve geçici önbelleğe alınmış veriler veri deposuna alınır ve müşterinin yönettiği anahtarla şifrelenir.

  - Key Vault erişim oranı--Azure Izleyici depolaması 'nın sarmalama ve sarmalama işlemleri için Key Vault, 6 ila 60 saniye arasındadır.

- Küme sağlama veya güncelleştirme durumundayken kümenizi güncelleştirirseniz güncelleştirme başarısız olur.

- Bir küme oluştururken çakışma hatası alırsanız, kümenizi son 14 gün içinde silmiş ve bu da geçici silme döneminde olabilir. Küme adı, geçici silme döneminde ayrılmış kalır ve bu adla yeni bir küme oluşturamazsınız. Bu ad, küme kalıcı olarak silindiğinde geçici silme süresinden sonra serbest bırakılır.

- Başka bir kümeyle bağlantı varsa, kümeye çalışma alanı bağlantısı başarısız olur.

- Bir küme oluşturur ve KeyVaultProperties 'i hemen belirtirseniz, sistem kimliği kümeye atanana kadar erişim ilkesi tanımlanmadığından işlem başarısız olabilir.

- Mevcut kümeyi KeyVaultProperties ile güncelleştirirseniz ve ' Get ' anahtar erişimi Ilkesi Key Vault eksikse, işlem başarısız olur.

- Kümenizin dağıtımı başarısız olursa, Azure Key Vault, kümenizin ve bağlı Log Analytics çalışma alanlarınızın aynı bölgede olduğunu doğrulayın. Farklı aboneliklerde olabilir.

- Anahtar sürümünüzü Key Vault güncelleştirir ve kümedeki yeni anahtar tanımlayıcı ayrıntılarını güncelleştirmemeniz durumunda, Log Analytics kümesi önceki anahtarınızı kullanmaya devam eder ve verileriniz erişilemez hale gelir. Veri alımı ve veri sorgulama yeteneği sağlamak için kümedeki yeni anahtar tanımlayıcı ayrıntılarını güncelleştirin.

- Bazı işlemler uzun sürer ve işlemin tamamlanması biraz zaman alabilir; bunlar küme oluşturma, küme anahtarı güncelleştirme ve küme silme işlemlerini gerçekleştirebilir. İşlem durumunu iki şekilde denetleyebilirsiniz:
  1. REST kullanırken, Azure-AsyncOperation URL değerini yanıttan kopyalayın ve [zaman uyumsuz işlemler durum denetimini](#asynchronous-operations-and-status-check)izleyin.
  2. Kümeye veya çalışma alanına GET isteği gönderin ve yanıtı gözlemleyin. Örneğin, bağlantısız çalışma alanının *Özellikler* bölümünde *kümeresourceıd* yok.

- Hata iletileri
  
  **Küme oluşturma**
  -  400--küme adı geçerli değil. Küme adı, a-z, A-Z, 0-9 ve 3-63 uzunluğunda karakterler içerebilir.
  -  400--isteğin gövdesi null veya hatalı biçimde.
  -  400--SKU adı geçersiz. SKU adını Capacityreservatıon olarak ayarlayın.
  -  400--kapasite sağlandı ancak SKU, Capacityreservatıon değil. SKU adını Capacityreservatıon olarak ayarlayın.
  -  400--SKU 'da eksik kapasite. Kapasite değerini 100 (GB) adımlarında 1000 veya üzeri olarak ayarlayın.
  -  400--SKU 'da kapasite Aralık içinde değil. Çalışma alanınızdaki en az 1000 ve ' kullanım ve tahmini maliyet ' altında kullanılabilir olan en fazla izin verilen kapasiteye kadar olmalıdır.
  -  400--kapasite 30 gün boyunca kilitlidir. Güncelleştirme sonrasında kapasiteyi düşürmek 30 gün daha olur.
  -  400--SKU ayarlanmadı. SKU adını, 100 (GB) adımlarında Capacityreservatıon ve Capacity değerini 1000 veya üzeri olarak ayarlayın.
  -  400--Identity null ya da boş. SystemAssigned türüyle kimliği ayarlayın.
  -  400--KeyVaultProperties oluşturma sırasında ayarlanır. Anahtar Vaultproperties kümesini küme oluşturulduktan sonra güncelleştirin.
  -  400--işlem şu anda yürütülemiyor. Zaman uyumsuz işlem başarılı dışında bir durumda. Herhangi bir güncelleştirme işlemi gerçekleştirilmeden önce kümenin işlemini tamamlaması gerekir.

  **Küme güncelleştirmesi**
  -  400--küme silme durumunda. Zaman uyumsuz işlem devam ediyor. Herhangi bir güncelleştirme işlemi gerçekleştirilmeden önce kümenin işlemini tamamlaması gerekir.
  -  400--KeyVaultProperties boş değil, ancak biçimi hatalı. Bkz. [anahtar tanımlayıcısı güncelleştirmesi](#update-cluster-with-key-identifier-details).
  -  400--Key Vault anahtarı doğrulanamadı. İzin eksikliği veya anahtar bulunmadığı için olabilir. Key Vault ' de [anahtar ve erişim ilkesini ayarlamış](#grant-key-vault-permissions) olduğunuzdan emin olun.
  -  400--anahtar kurtarılabilir değil. Key Vault, geçici-silme ve Temizleme koruması olarak ayarlanmalıdır. [Key Vault belgelerine](../../key-vault/general/soft-delete-overview.md) bakın
  -  400--işlem şu anda yürütülemiyor. Zaman uyumsuz işlemin tamamlanmasını bekleyin ve yeniden deneyin.
  -  400--küme silme durumunda. Zaman uyumsuz işlemin tamamlanmasını bekleyin ve yeniden deneyin.

  **Küme al**
    -  404--küme bulunamadı, küme silinmiş olabilir. Bu ada sahip bir küme oluşturmaya ve çakışmaya yol açmaya çalışırsanız, küme 14 gün boyunca geçici silme aşamasında olur. Bu dosyayı kurtarmak için desteğe başvurabilirsiniz veya yeni bir küme oluşturmak için başka bir ad kullanabilirsiniz. 

  **Küme silme**
    -  409--sağlama durumundayken bir kümeyi silemez. Zaman uyumsuz işlemin tamamlanmasını bekleyin ve yeniden deneyin.

  **Çalışma alanı bağlantısı**
  -  404--çalışma alanı bulunamadı. Belirttiğiniz çalışma alanı yok veya silinmiş.
  -  409--işlemdeki çalışma alanı bağlantısı veya bağlantıyı kaldırma işlemi.
  -  400--küme bulunamadı, belirttiğiniz küme yok veya silinmiş. Bu ada sahip bir küme oluşturmaya ve çakışmaya yol açmaya çalışırsanız, küme 14 gün boyunca geçici silme aşamasında olur. Bu uygulamayı kurtarmak için desteğe başvurabilirsiniz.

  **Çalışma alanına bağlantıyı kaldır**
  -  404--çalışma alanı bulunamadı. Belirttiğiniz çalışma alanı yok veya silinmiş.
  -  409--işlemdeki çalışma alanı bağlantısı veya bağlantıyı kaldırma işlemi.
## <a name="next-steps"></a>Sonraki adımlar

- [Log Analytics adanmış küme faturalaması](./manage-cost-storage.md#log-analytics-dedicated-clusters) hakkında bilgi edinin
- [Log Analytics çalışma alanlarının doğru tasarımı](./design-logs-deployment.md) hakkında bilgi edinin