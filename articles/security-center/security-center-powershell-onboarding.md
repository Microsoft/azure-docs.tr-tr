---
title: PowerShell ile Azure Güvenlik Merkezi 'ne ekleme
description: Bu belge, PowerShell cmdlet 'lerini kullanarak Azure Güvenlik Merkezi 'ni ekleme sürecinde size yol gösterir.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e400fcbf-f0a8-4e10-b571-5a0d0c3d0c67
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2018
ms.author: memildin
ms.openlocfilehash: b20b3c1e4216fe8065fbc8ac24c7d8097903fc5a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686376"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>PowerShell kullanarak Azure Güvenlik Merkezi 'Ni otomatik olarak ekleme

Azure Güvenlik Merkezi PowerShell modülünü kullanarak Azure iş yüklerinizi programlama yoluyla güvenli hale getirebilirsiniz.
PowerShell 'in kullanılması, görevleri otomatikleştirmenizi ve el ile görevlerde bulunan insan hatasından kaçınmanızı sağlar. Bu, özellikle yüzlerce ve binlerce kaynakla düzinelerce abonelik içeren büyük ölçekli dağıtımlarda faydalıdır; bunların hepsi başlangıçtan itibaren güvenli hale getirilmesi gerekir.

PowerShell kullanarak Azure Güvenlik Merkezi 'ni ekleme, Azure kaynaklarınızın kolayca hazırlanması ve yönetimini otomatikleştirmenizi ve gerekli güvenlik denetimlerini eklemenizi sağlar.

Bu makalede, aboneliklerinizde Güvenlik Merkezi 'ni kullanıma almak için ortamınızda değiştirilebilen ve kullanılabilecek örnek bir PowerShell betiği sunulmaktadır. 

Bu örnekte, şu KIMLIĞE sahip bir abonelikte güvenlik merkezini etkinleştireceğiz ve Güvenlik Merkezi 'nin standart katmanını uygulayarak yüksek düzeyde koruma sağlayan önerilen ayarları uygulayacağız: Gelişmiş tehdit koruması ve algılama özellikleri:

1. [Güvenlik Merkezi Standart koruma düzeyini](https://azure.microsoft.com/pricing/details/security-center/)ayarlayın. 
 
2. Log Analytics çalışma alanını, Microsoft Monitoring Agent abonelikle ilişkili VM 'lerde topladığı verileri (Bu örnekte, var olan bir Kullanıcı tanımlı çalışma alanı (myWorkspace) olarak ayarlayın.

3. Güvenlik Merkezi 'nin [Microsoft Monitoring Agent dağıtan](security-center-enable-data-collection.md#auto-provision-mma)otomatik aracı sağlamasını etkinleştirin.

5. [Güvenlik Merkezi uyarıları ve önemli olayları için kuruluşun CISO değerini güvenlik ilgili kişisi olarak](security-center-provide-security-contact-details.md)ayarlayın.

6. Güvenlik Merkezi 'nin [varsayılan güvenlik ilkelerini](tutorial-security-policy.md)atayın.

## <a name="prerequisites"></a>Ön koşullar

Güvenlik Merkezi cmdlet 'lerini çalıştırmadan önce Bu adımlar gerçekleştirilmelidir:

1.  PowerShell 'i yönetici olarak çalıştırın.
2.  PowerShell 'de aşağıdaki komutları çalıştırın:
      
        Set-ExecutionPolicy -ExecutionPolicy AllSigned
        Install-Module -Name Az.Security -Force

## <a name="onboard-security-center-using-powershell"></a>PowerShell kullanarak güvenlik merkezi 'ni ekleme

1.  Aboneliklerinizi Güvenlik Merkezi kaynak sağlayıcısına kaydedin:

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security' 

2.  İsteğe bağlı: aboneliklerin kapsam düzeyini (Fiyatlandırma Katmanı) ayarlayın (tanımlı değilse, fiyatlandırma katmanı boş olarak ayarlanır):

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Set-AzSecurityPricing -Name "default" -PricingTier "Standard"

3.  Aracıların rapor alacak bir Log Analytics çalışma alanı yapılandırın. Zaten oluşturduğunuz bir Log Analytics çalışma alanınızın olması gerekir, aboneliğin VM 'lerinin rapor alınacaktır. Aynı çalışma alanına raporlamak için birden çok abonelik tanımlayabilirsiniz. Tanımlı değilse, varsayılan çalışma alanı kullanılacaktır.

        Set-AzSecurityWorkspaceSetting -Name "default" -Scope
        "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"

4.  Azure VM 'lerinize Microsoft Monitoring Agent yüklemesini otomatik sağlama:
    
        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    
        Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision

    > [!NOTE]
    > Azure sanal makinelerinizin Azure Güvenlik Merkezi tarafından otomatik olarak korunduğundan emin olmak için otomatik sağlamayı etkinleştirmeniz önerilir.
    >

5.  İsteğe bağlı: seçtiğiniz abonelikler için güvenlik iletişim ayrıntılarını tanımlamanız önerilir ve bu, Güvenlik Merkezi tarafından oluşturulan uyarıların ve bildirimlerin alıcıları olarak kullanılacaktır:

        Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert 

6.  Varsayılan güvenlik merkezi ilkesi girişim atamasını yapın:

        Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
        $Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ '[Preview]: Enable Monitoring in Azure Security Center'}
        New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'

Artık Azure Güvenlik Merkezi 'Ni PowerShell ile başarıyla eklendi!

Artık bu PowerShell cmdlet 'lerini, abonelikler ve kaynaklar arasında programlı bir şekilde yinelemek için Otomasyon betikleriyle kullanabilirsiniz. Bu, zamandan tasarruf eder ve insan hatası olasılığını azaltır. Bu [örnek betiği](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) başvuru olarak kullanabilirsiniz.






## <a name="see-also"></a>Ayrıca bkz.
Güvenlik Merkezi 'ne ekleme işlemini otomatikleştirmek için PowerShell 'i nasıl kullanabileceğiniz hakkında daha fazla bilgi edinmek için aşağıdaki makaleye bakın:

* [Az. Security](https://docs.microsoft.com/powershell/module/az.security).

Güvenlik Merkezi hakkında daha fazla bilgi edinmek için aşağıdaki makaleye bakın:

* [Azure Güvenlik Merkezi'nde güvenlik ilkelerini ayarlama](tutorial-security-policy.md) -- Azure abonelikleriniz ve kaynak gruplarınız için güvenlik ilkelerini yapılandırma hakkında bilgi edinin.
* [Azure Güvenlik Merkezi'nde güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md) -- Güvenlik uyarılarını yönetme ve yanıtlama hakkında bilgi edinin.
* [Azure Güvenlik Merkezi ile ilgili SSS](security-center-faq.md) -- Hizmeti kullanımı ile ilgili sık sorulan soruları bulabilirsiniz.
