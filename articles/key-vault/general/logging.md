---
title: Günlüğe kaydetme Azure Key Vault | Microsoft Docs
description: Bilgileri sağladığınız bir Azure depolama hesabına kaydeden Azure Key Vault için günlüğe kaydetmeyi etkinleştirerek anahtar kasalarınıza erişimi nasıl izleyeceğinizi öğrenin.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 12/18/2020
ms.author: mbaldwin
ms.openlocfilehash: 5847fcb2cf553e1fcc744877e52dbbdf1f24d992
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751841"
---
# <a name="azure-key-vault-logging"></a>Azure Key Vault günlüğü

Bir veya daha fazla Anahtar Kasası oluşturduktan sonra muhtemelen anahtar kasalarınızın nasıl ve ne zaman erişildiğini ve kim tarafından yapılacağını izlemek isteyeceksiniz. Bu, bilgileri, sağladığınız bir Azure depolama hesabına kaydeden Azure Key Vault için günlüğe kaydetmeyi etkinleştirerek yapabilirsiniz. Bunu ayarlamaya yönelik adım adım yönergeler için bkz. [Key Vault günlüğü etkinleştirme](howto-logging.md).

Kayıt bilgilerinizi 10 dakika (en çok) Anahtar Kasası işleminden sonra erişebilirsiniz. Çoğu durumda, bundan daha hızlı olacaktır.  Depolama hesabınızdaki günlükleri istediğiniz şekilde yönetebilirsiniz:

* Günlüklere kimlerin erişebileceğini kısıtlayarak günlüklerinizi güvenli hale getirmek için depolama hesabınızda standart Azure erişim denetimi yöntemlerini kullanın.
* Depolama hesabınızda tutmak istemediğiniz günlükleri silebilirsiniz.

Key Vault hakkında genel bilgi için bkz. [Azure Key Vault nedir?](overview.md). Key Vault nerede kullanılabildiği hakkında daha fazla bilgi için [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/key-vault/)bakın. [Key Vault Için Azure izleyici](../../azure-monitor/insights/key-vault-insights-overview.md)kullanma hakkında bilgi için.

## <a name="interpret-your-key-vault-logs"></a>Anahtar Kasası günlüklerinizi yorumlama

Günlüğe kaydetmeyi etkinleştirdiğinizde, belirtilen depolama hesabınız için **Öngörüler-logs-auditevent** adlı yeni bir kapsayıcı otomatik olarak oluşturulur. Birden çok Anahtar Kasası için günlükleri toplamak üzere bu depolama hesabını kullanabilirsiniz.

Tek tek bloblar JSON blobu olarak biçimlendirilip metin olarak depolanır. Bir örnek günlük girişine bakalım. 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

Aşağıdaki tabloda alan adları ve açıklamaları listelenmektedir:

| Alan adı | Description |
| --- | --- |
| **ışınızda** |UTC olarak tarih ve saat. |
| **resourceId** |Azure Resource Manager kaynak KIMLIĞI. Key Vault günlükleri için, her zaman Key Vault kaynak KIMLIĞI olur. |
| **operationName** |Sonraki tabloda belirtildiği gibi işlemin adı. |
| **operationVersion** |İstemci tarafından istenen sürümü REST API. |
| **alan** |Sonuç türü. Key Vault günlükleri için, `AuditEvent` tek, kullanılabilir değerdir. |
| **'ı** |REST API isteğinin sonucu. |
| **resultSignature** |HTTP durumu. |
| **resultDescription** |Kullanılabilir olduğunda sonuç hakkında ek açıklama. |
| **Ort** |Milisaniye cinsinden REST API'si isteğini sunmak için geçen süre. Buna ağ gecikme süresi dahil değildir; bu nedenle istemci tarafında ölçtüğünüz süre bu süreyle eşleşmeyebilir. |
| **callerIpAddress** |İsteği yapan istemcinin IP adresi. |
| **ID** |İstemci tarafı günlüklerini hizmet tarafı (Anahtar Kasası) günlükleriyle ilişkilendirmek için istemcinin geçirebileceği isteğe bağlı bir GUID. |
| **IDENTITY** |REST API isteğinde sunulan belirteçten kimlik. Bu, genellikle bir "Kullanıcı," bir "hizmet sorumlusu" veya "Kullanıcı + AppID" birleşimidir ve bir Azure PowerShell cmdlet 'inin sonucu olan bir istekte bulunur. |
| **özelliklerinin** |İşleme göre farklılık gösteren bilgiler (**OperationName**). Çoğu durumda bu alan istemci bilgilerini (istemci tarafından geçirilen kullanıcı aracısı dizesi), tam REST API istek URI 'sini ve HTTP durum kodunu içerir. Ayrıca, bir nesne bir isteğin sonucu olarak döndürüldüğünde (örneğin, **Keycreate** veya **vaultget**), anahtar URI (as `id` ), kasa URI 'si veya gizli URI 'yi de içerir. |

**OperationName** alan değerleri *objectverb* biçimindedir. Örnek:

* Tüm Anahtar Kasası işlemleri `Vault<action>` , ve gibi biçimdedir `VaultGet` `VaultCreate` .
* Tüm anahtar işlemleri `Key<action>` , ve gibi biçimdedir `KeySign` `KeyList` .
* Tüm gizli işlemler `Secret<action>` , ve gibi biçimdedir `SecretGet` `SecretListVersions` .

Aşağıdaki tabloda, **OperationName** değerleri ve karşılık gelen REST API komutları listelenmektedir:

### <a name="operation-names-table"></a>İşlem adları tablosu

# <a name="vault"></a>[Kasa](#tab/Vault)

| operationName | REST API komutu |
| --- | --- |
| **Kimlik Doğrulaması** |Azure Active Directory uç noktası aracılığıyla kimlik doğrulaması |
| **VaultGet** |[Bir anahtar kasası hakkında bilgi edinme](/rest/api/keyvault/vaults) |
| **VaultPut** |[Bir anahtar kasası oluşturma veya güncelleştirme](/rest/api/keyvault/vaults) |
| **VaultDelete** |[Bir anahtar kasasını silme](/rest/api/keyvault/vaults) |
| **VaultPatch** |[Bir anahtar kasasını güncelleştirme](/rest/api/keyvault/vaults) |
| **VaultList** |[Bir kaynak grubundaki tüm anahtar kasalarını listeleme](/rest/api/keyvault/vaults) |
| **Vaulttemizleme** |[Silinen kasayı temizle](/rest/api/keyvault/vaults/purgedeleted) |
| **VaultRecover** |Silinen kasayı kurtar|
| **VaultGetDeleted** |[Silinen kasayı al](/rest/api/keyvault/vaults/getdeleted) |
| **VaultListDeleted** |[Silinen kasaları Listele](/rest/api/keyvault/vaults/listdeleted) |
| **VaultAccessPolicyChangedEventGridNotification** | Kasa erişimi ilkesi değiştirilmiş olayı yayınlandı |

# <a name="keys"></a>[Anahtarlar](#tab/Keys)

| operationName | REST API komutu |
| --- | --- |
| **KeyCreate** |[Bir anahtar oluşturma](/rest/api/keyvault/createkey) |
| **KeyGet** |[Bir anahtar hakkında bilgi edinme](/rest/api/keyvault/getkey) |
| **KeyImport** |[Bir kasaya bir anahtar aktarma](/rest/api/keyvault/vaults) |
| **KeyDelete** |[Bir anahtarı silme](/rest/api/keyvault/deletekey) |
| **KeySign** |[Bir anahtar ile oturum açma](/rest/api/keyvault/sign) |
| **KeyVerify** |[Bir anahtar ile doğrulama](/rest/api/keyvault/vaults) |
| **KeyWrap** |[Bir anahtarı sarmalama](/rest/api/keyvault/wrapkey) |
| **KeyUnwrap** |[Bir anahtarı kaydırma](/rest/api/keyvault/unwrapkey) |
| **KeyEncrypt** |[Bir anahtar ile şifreleme](/rest/api/keyvault/encrypt) |
| **KeyDecrypt** |[Bir anahtar ile şifre çözme](/rest/api/keyvault/decrypt) |
| **KeyUpdate** |[Bir anahtarı güncelleştirme](/rest/api/keyvault/updatekey) |
| **KeyList** |[Bir kasadaki anahtarları listeleme](/rest/api/keyvault/getkeys) |
| **KeyListVersions** |[Bir anahtarın sürümlerini listeleme](/rest/api/keyvault/getkeyversions) |
| **KeyPurge** |[Bir anahtarı Temizleme](/rest/api/keyvault/purgedeletedkey) |
| **KeyBackup** |[Anahtar yedekleme](/rest/api/keyvault/backupkey) |
| **KeyRestore** |[Bir anahtarı geri yükleme](/rest/api/keyvault/restorekey) |
| **Anahtar kurtarma** |[Bir anahtarı kurtarma](/rest/api/keyvault/recoverdeletedkey) |
| **KeyGetDeleted** |[Silinen anahtarı al](/rest/api/keyvault/getdeletedkey) |
| **KeyListDeleted** |[Bir kasada silinen anahtarları listeleme](/rest/api/keyvault/getdeletedkeys) |
| **Keyyaklaştığında Expiryeventgridnotification** |Süre sonu olayını yayımlayan anahtar |
| **KeyExpiredEventGridNotification** |Anahtarın süre dolduğunda etkinlik yayınlandı |

# <a name="secrets"></a>[Gizli Diziler](#tab/Secrets)

| operationName | REST API komutu |
| --- | --- |
| **SecretSet** |[Gizli anahtar oluşturma](/rest/api/keyvault/updatecertificate) |
| **SecretGet** |[Gizli dizi alın](/rest/api/keyvault/getsecret) |
| **SecretUpdate** |[Gizli anahtarı güncelleştirme](/rest/api/keyvault/updatesecret) |
| **SecretDelete** |[Gizli anahtarı silme](/rest/api/keyvault/deletesecret) |
| **SecretList** |[Bir kasadaki gizli anahtarları listeleme](/rest/api/keyvault/getsecrets) |
| **SecretListVersions** |[Bir gizli anahtarın sürümlerini listeleme](/rest/api/keyvault/getsecretversions) |
| **Secrettemizleme** |[Gizli dizi Temizleme](/rest/api/keyvault/purgedeletedsecret) |
| **SecretBackup** |[Gizli dizi yedekleme](/rest/api/keyvault/backupsecret) |
| **SecretRestore** |[Gizli anahtarı geri yükleme](/rest/api/keyvault/restoresecret) |
| **SecretRecover** |[Gizli dizi kurtarma](/rest/api/keyvault/recoverdeletedsecret) |
| **SecretGetDeleted** |[Silinen gizli dizi al](/rest/api/keyvault/getdeletedsecret) |
| **SecretListDeleted** |[Bir kasada silinen gizli dizileri listeleyin](/rest/api/keyvault/getdeletedsecrets) |
| **Secretyaklaştığında Expiryeventgridnotification** |Süre sonu olayı yayımlanan gizli dizi |
| **Secretexpio Ventgridnotification** |Gizli zaman aşımına uğradı olayı yayımlandı |

# <a name="certificates"></a>[Sertifikalar](#tab/Cerificates)

| operationName | REST API komutu |
| --- | --- |
| **CertificateGet** |[Bir sertifika hakkında bilgi edinme](/rest/api/keyvault/getcertificate) |
| **Sertifikaoluştur** |[Sertifika oluşturma](/rest/api/keyvault/createcertificate) |
| **Certificateımport** |[Bir sertifikayı kasaya aktarma](/rest/api/keyvault/importcertificate) |
| **CertificateUpdate** |[Sertifikayı güncelleştirme](/rest/api/keyvault/updatecertificate) |
| **Sertifika listesi** |[Bir kasadaki sertifikaları listeleme](/rest/api/keyvault/getcertificates) |
| **CertificateListVersions** |[Bir sertifikanın sürümlerini listeleyin](/rest/api/keyvault/getcertificateversions) |
| **CertificateDelete** |[Sertifikayı silme](/rest/api/keyvault/deletecertificate) |
| **Sertifikatemizleme** |[Bir sertifikayı Temizleme](/rest/api/keyvault/purgedeletedcertificate) |
| **CertificateBackup** |[Sertifika yedekleme](/rest/api/keyvault/backupcertificate) |
| **CertificateRestore** |[Sertifikayı geri yükleme](/rest/api/keyvault/restorecertificate) |
| **Sertifika Aterecover** |[Sertifikayı kurtarma](/rest/api/keyvault/recoverdeletedcertificate) |
| **CertificateGetDeleted** |[Silinen sertifikayı al](/rest/api/keyvault/getdeletedcertificate) |
| **CertificateListDeleted** |[Bir kasada silinen sertifikaları listeleme](/rest/api/keyvault/getdeletedcertificates) |
| **CertificatePolicyGet** |[Sertifika ilkesini al](/rest/api/keyvault/getcertificatepolicy) |
| **CertificatePolicyUpdate** |[Sertifika ilkesini Güncelleştir](/rest/api/keyvault/updatecertificatepolicy) |
| **CertificatePolicySet** |[Sertifika İlkesi Oluştur](/rest/api/keyvault/createcertificate) |
| **CertificateContactsGet** |[Sertifika kişilerini al](/rest/api/keyvault/getcertificatecontacts) |
| **CertificateContactsSet** |[Sertifika kişilerini ayarla](/rest/api/keyvault/setcertificatecontacts) |
| **CertificateContactsDelete** |[Sertifika kişilerini silme](/rest/api/keyvault/deletecertificatecontacts) |
| **Certificateıssuerget** |[Sertifika vereni al](/rest/api/keyvault/getcertificateissuer) |
| **Certificateıssuerset** |[Sertifikayı vereni ayarla](/rest/api/keyvault/setcertificateissuer) |
| **Certificateıssuerupdate** |[Sertifikayı vereni Güncelleştir](/rest/api/keyvault/updatecertificateissuer) |
| **Certificateıssuerdelete** |[Sertifikayı vereni Sil](/rest/api/keyvault/deletecertificateissuer) |
| **Certificateıssuerslistesi** |[Sertifika verenler listeleyin](/rest/api/keyvault/getcertificateissuers) |
| **Sertifikakaydı** |Sertifika kaydetme |
| **CertificateRenew** |Sertifikayı yenileme |
| **CertificatePendingGet** |Bekleyen sertifikayı al |
| **CertificatePendingMerge** |Sertifika birleştirmesi bekleniyor |
| **CertificatePendingUpdate** |Sertifika güncelleştirmesi bekleniyor |
| **CertificatePendingDelete** |Bekleyen sertifikayı Sil |
| **CertificateNearExpiryEventGridNotification** |Süre sonu olayı yayımlanan sertifika |
| **CertificateExpiredEventGridNotification** |Sertifikanın zaman aşımına uğradığı olay yayımlandı |

---

## <a name="use-azure-monitor-logs"></a>Azure İzleyici günlüklerini kullanma

Key Vault günlüklerini gözden geçirmek için Azure Izleyici günlüklerinde Key Vault çözümünü kullanabilirsiniz `AuditEvent` . Azure Izleyici günlüklerinde, verileri analiz etmek ve ihtiyacınız olan bilgileri almak için günlük sorgularını kullanırsınız. 

Bunu ayarlama dahil daha fazla bilgi için bkz. [Azure izleyici 'de Azure Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Key Vault günlüğü etkinleştirme](howto-logging.md)
- [Azure izleyici](../../azure-monitor/index.yml)
- .NET Web uygulamasında Azure Key Vault kullanan bir öğretici için bkz. [bir Web uygulamasından Azure Key Vault kullanma](tutorial-net-create-vault-azure-web-app.md).
- Programlama başvuruları için bkz. [Azure Anahtar Kasası geliştirici kılavuzu](developers-guide.md).
- Azure Key Vault için Azure PowerShell 1,0 cmdlet 'lerinin bir listesi için bkz. [Azure Key Vault cmdlet 'leri](/powershell/module/az.keyvault/#key_vault).