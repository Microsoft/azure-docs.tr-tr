---
title: Azure 'da oturum açmak için Azure sanal makinesinde Yönetilen kimlikler kullanma
description: Komut dosyası istemcisi oturum açma ve kaynak erişimi için Azure kaynakları hizmet sorumlusu için Azure VM tarafından yönetilen bir kimlik kullanımı için adım adım yönergeler ve örnekler.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 59366f1a5b4bd0572af1b36f7be2f5bf91392660
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784794"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Azure VM 'de oturum açma için Azure kaynakları için Yönetilen kimlikler kullanma 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Bu makalede, Azure kaynakları hizmet sorumlusu için yönetilen kimlikleri kullanarak oturum açma için PowerShell ve CLı betik örnekleri ve hata işleme gibi önemli konularda rehberlik sağlanmıştır.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Bu makaledeki Azure PowerShell veya Azure CLı örneklerini kullanmayı planlıyorsanız, [Azure PowerShell](/powershell/azure/install-az-ps) veya [Azure CLI](/cli/azure/install-azure-cli)'nin en son sürümünü yüklediğinizden emin olun. 

> [!IMPORTANT]
> - Bu makaledeki tüm örnek betik, komut satırı istemcisinin Azure kaynakları için yönetilen kimliklere sahip bir VM 'de çalıştığını varsayar. Sanal makinenize uzaktan bağlanmak için Azure portal VM "Connect" özelliğini kullanın. Bir VM 'de Azure kaynakları için yönetilen kimlikleri etkinleştirme hakkında daha fazla bilgi için bkz. Azure portal veya varyant makalelerinden birini [kullanarak BIR VM 'de Azure kaynakları için yönetilen kimlikleri yapılandırma](qs-configure-portal-windows-vm.md)(POWERSHELL, CLI, bir şablon veya BIR Azure SDK kullanarak). 
> - Kaynak erişimi sırasında hata oluşmasını engellemek için, VM 'de Azure Resource Manager işlemlerine izin vermek üzere VM 'nin yönetilen kimliğine uygun kapsamda (VM veya üzeri) en az "okuyucu" erişiminin verilmesi gerekir. Ayrıntılar için [Azure Portal kullanarak bir kaynağa Azure kaynakları için Yönetilen kimlikler atama](howto-assign-access-portal.md) bölümüne bakın.

## <a name="overview"></a>Genel Bakış

Azure kaynakları için Yönetilen kimlikler, VM 'deki [Azure kaynakları için Yönetilen kimlikler etkinleştirildikten sonra oluşturulan](overview.md) bir [hizmet sorumlusu nesnesi](../develop/developer-glossary.md#service-principal-object) sağlar. Hizmet sorumlusuna Azure kaynaklarına erişim verilebilir ve oturum açma ve kaynak erişimi için betik/komut satırı istemcilerinin kimlik olarak kullanılması verilebilir. Geleneksel olarak, kendi kimliği altında güvenli kaynaklara erişmek için bir komut dosyası istemcisinin şunları yapması gerekir:  

   - Azure AD 'ye bir gizli/Web istemci uygulaması olarak kaydolmuş ve bunlarla yarışmalıdır
   - uygulamanın kimlik bilgilerini (büyük olasılıkla betiğe gömülü) kullanarak hizmet sorumlusu altında oturum açın

Azure kaynakları için Yönetilen kimlikler sayesinde, komut dosyası istemciniz artık Azure kaynakları hizmet sorumlusu için Yönetilen kimlikler altında oturum açabilse de bunu yapması gerekmez. 

## <a name="azure-cli"></a>Azure CLI

Aşağıdaki betik, aşağıdakilerin nasıl yapılacağını göstermektedir:

1. Azure kaynakları hizmet sorumlusu için VM 'nin yönetilen kimliği kapsamında Azure AD 'de oturum açın  
2. Azure Resource Manager çağırın ve VM 'nin hizmet sorumlusu KIMLIĞINI alın. CLı, belirteç alımı/kullanımını sizin için otomatik olarak yönetme işlemini gerçekleştirir. İçin sanal makine adınızı seçtiğinizden emin olun `<VM-NAME>` .  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Aşağıdaki betik, aşağıdakilerin nasıl yapılacağını göstermektedir:

1. Azure kaynakları hizmet sorumlusu için VM 'nin yönetilen kimliği kapsamında Azure AD 'de oturum açın  
2. VM hakkında bilgi almak için bir Azure Resource Manager cmdlet 'i çağırın. PowerShell, belirteç kullanımını sizin için otomatik olarak yönetme işlemini gerçekleştirir.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Azure hizmetleri için kaynak kimlikleri

Azure AD [kimlik doğrulamasını](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) destekleyen ve Azure kaynakları için Yönetilen kimlikler ve bunlara karşılık gelen kaynak kimlikleri ile test edilmiş bir kaynak listesi için bkz. Azure hizmetleri.

## <a name="error-handling-guidance"></a>Hata işleme Kılavuzu 

Aşağıdakiler gibi yanıtlar, sanal makinenin Azure kaynakları için yönetilen kimliğinin doğru şekilde yapılandırılmadığını gösterebilir:

- PowerShell: *Invoke-WebRequest: uzak sunucuya bağlanılamıyor*
- CLı: *MSI: `http://localhost:50342/oauth2/token` ' HTTPConnectionPool (Host = ' localhost ', bağlantı noktası = 50342) hatasından bir belirteç alınamadı* 

Bu hatalardan birini alırsanız, [Azure Portal](https://portal.azure.com) Azure sanal makinesine dönün ve **kimlik** sayfasına gidin ve **sistem atanın** "Evet" olarak ayarlandığından emin olun.

## <a name="next-steps"></a>Sonraki adımlar

- Azure VM 'de Azure kaynakları için yönetilen kimlikleri etkinleştirmek üzere bkz. [PowerShell kullanarak Azure VM 'de Azure kaynakları için yönetilen kimlikleri yapılandırma](qs-configure-powershell-windows-vm.md)veya Azure [CLI kullanarak bir Azure VM 'de Azure kaynakları Için Yönetilen kimlikler yapılandırma](qs-configure-cli-windows-vm.md)
