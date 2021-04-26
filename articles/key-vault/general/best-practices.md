---
title: Key Vault kullanmak için en iyi uygulamalar | Azure Key Vault | Microsoft Docs
description: Ayrı anahtar kasalarının ne zaman kullanılacağını, yedekleme, günlüğe kaydetme ve kurtarma seçeneklerini kullanma dahil olmak üzere Azure Key Vault için en iyi yöntemler hakkında bilgi edinin.
services: key-vault
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: mbaldwin
ms.openlocfilehash: 7cfa2059cc03b96db39183cfa5056c9934a02290
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814361"
---
# <a name="best-practices-to-use-key-vault"></a>Key Vault kullanmak için en iyi uygulamalar

## <a name="use-separate-key-vaults"></a>Ayrı anahtar kasaları kullanın

Önerimiz, ortam başına uygulama başına (geliştirme, ön üretim ve üretim) bir kasa kullanmaktır. Bu, ortamlar genelinde gizli dizileri paylaşmanıza ve ayrıca bir ihlal durumunda tehdidi azaltmanıza yardımcı olur.

## <a name="control-access-to-your-vault"></a>Kasanıza erişimi denetleme

Azure Key Vault, şifreleme anahtarlarını ve sertifikalar, bağlantı dizeleri ve parolalar gibi gizli dizileri koruyan bir bulut hizmetidir. Bu veriler hassas ve iş açısından kritik olduğundan, yalnızca yetkili uygulamalara ve kullanıcılara izin vererek anahtar kasalarınıza güvenli bir şekilde erişmeniz gerekir. Bu [makalede](security-features.md) Key Vault erişim modeline genel bir bakış sunulmaktadır. Kimlik doğrulama ve yetkilendirmeyi açıklar ve anahtar kasalarınıza erişimin güvenliğini nasıl sağlayabileceğinizi açıklar.

Kasanıza erişimi denetlerken öneriler aşağıdaki gibidir:
1. Aboneliğiniz, kaynak grubunuz ve Anahtar kasaları (Azure RBAC) için erişimi kilitleme
2. Her kasa için erişim ilkeleri oluşturma
3. Erişim vermek için en az ayrıcalık erişim sorumlusunu kullanın
4. Güvenlik Duvarı ve [sanal ağ hizmet uç noktalarını](overview-vnet-service-endpoints.md) aç

## <a name="backup"></a>Backup

Bir kasadaki nesnelerin güncelleştirme/silme/silme işlemleri sırasında kasanızın düzenli olarak arka pencerelerini aldığınızdan emin olun.

### <a name="azure-powershell-backup-commands"></a>Azure PowerShell yedekleme komutları

* [Yedekleme sertifikası](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate)
* [Yedekleme anahtarı](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey)
* [Yedekleme gizli dizisi](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

### <a name="azure-cli-backup-commands"></a>Azure CLı yedekleme komutları

* [Yedekleme sertifikası](/cli/azure/keyvault/certificate#az_keyvault_certificate_backup)
* [Yedekleme anahtarı](/cli/azure/keyvault/key#az_keyvault_key_backup)
* [Yedekleme gizli dizisi](/cli/azure/keyvault/secret#az_keyvault_secret_backup)


## <a name="turn-on-logging"></a>Günlüğü aç

Kasanızın [günlüğünü açın](logging.md) . Ayrıca uyarıları ayarlayın.

## <a name="turn-on-recovery-options"></a>Kurtarma seçeneklerini aç

1. [Geçici silme](soft-delete-overview.md)özelliğini açın.
2. Geçici silme etkin olduktan sonra bile gizli dizi/kasaların silinmesini zorlamak istiyorsanız Temizleme korumasını açın.