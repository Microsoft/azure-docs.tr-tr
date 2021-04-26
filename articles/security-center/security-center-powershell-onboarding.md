---
title: PowerShell ile Azure Güvenlik Merkezi 'ne ekleme
description: Bu belge, PowerShell cmdlet 'leriyle Azure Güvenlik Merkezi 'Ni etkinleştirme sürecinde size yol gösterir.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 04/19/2021
ms.author: memildin
ms.openlocfilehash: f9ab258f59279112d0b90c5d460e6761ac911a2d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713363"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>PowerShell kullanarak Azure Güvenlik Merkezi 'Ni otomatik olarak ekleme

Azure Güvenlik Merkezi PowerShell modülünü kullanarak Azure iş yüklerinizi programlama yoluyla güvenli hale getirebilirsiniz.
PowerShell 'in kullanılması, görevleri otomatikleştirmenizi ve el ile görevlerde bulunan insan hatasından kaçınmanızı sağlar. Bu, özellikle yüzlerce ve binlerce kaynakla düzinelerce abonelik içeren büyük ölçekli dağıtımlarda faydalıdır; bunların hepsi başlangıçtan itibaren güvenli hale getirilmesi gerekir.

PowerShell kullanarak Azure Güvenlik Merkezi 'ni ekleme, Azure kaynaklarınızın kolayca hazırlanması ve yönetimini otomatikleştirmenizi ve gerekli güvenlik denetimlerini eklemenizi sağlar.

Bu makalede, aboneliklerinizde Güvenlik Merkezi 'ni kullanıma almak için ortamınızda değiştirilebilen ve kullanılabilecek örnek bir PowerShell betiği sunulmaktadır. 

Bu örnekte, KIMLIĞI: d07c0080-170c-4c24-861d-9c817742786c olan bir abonelikte Güvenlik Merkezi 'ni etkinleştireceğiz ve Gelişmiş tehdit koruması ve algılama yetenekleri sağlayan Azure Defender 'ı etkinleştirerek yüksek düzeyde koruma sağlayan önerilen ayarları uygulayacağız:

1. [Azure Defender 'ı](azure-defender.md)etkinleştirin. 
 
2. Log Analytics aracısının, abonelikle ilişkili VM 'lerde topladığı verileri göndereceği Log Analytics çalışma alanını (Bu örnekte, var olan bir Kullanıcı tanımlı çalışma alanı (myWorkspace) ayarlayın.

3. Güvenlik Merkezi 'nin [Log Analytics aracısını dağıtan](security-center-enable-data-collection.md#auto-provision-mma)otomatik aracısının sağlamasını etkinleştirin.

5. [Güvenlik Merkezi uyarıları ve önemli olayları için kuruluşun CISO değerini güvenlik ilgili kişisi olarak](security-center-provide-security-contact-details.md)ayarlayın.

6. Güvenlik Merkezi 'nin [varsayılan güvenlik ilkelerini](tutorial-security-policy.md)atayın.

## <a name="prerequisites"></a>Önkoşullar

Güvenlik Merkezi cmdlet 'lerini çalıştırmadan önce Bu adımlar gerçekleştirilmelidir:

1. PowerShell 'i yönetici olarak çalıştırın.

1. PowerShell 'de aşağıdaki komutları çalıştırın:
      
    ```Set-ExecutionPolicy -ExecutionPolicy AllSigned```

    ```Install-Module -Name Az.Security -Force```

## <a name="onboard-security-center-using-powershell"></a>PowerShell kullanarak güvenlik merkezi 'ni ekleme

1. Aboneliklerinizi Güvenlik Merkezi kaynak sağlayıcısına kaydedin:

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security'```

1. İsteğe bağlı: aboneliklerin kapsam düzeyini (Azure Defender açık/kapalı) ayarlayın. Tanımsız ise, Defender kapalıdır:

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Set-AzSecurityPricing -Name "default" -PricingTier "Standard"```

1. Aracıların rapor alacak bir Log Analytics çalışma alanı yapılandırın. Zaten oluşturduğunuz bir Log Analytics çalışma alanınızın olması gerekir, aboneliğin VM 'lerinin rapor alınacaktır. Aynı çalışma alanına raporlamak için birden çok abonelik tanımlayabilirsiniz. Tanımlı değilse, varsayılan çalışma alanı kullanılacaktır.

    ```Set-AzSecurityWorkspaceSetting -Name "default" -Scope "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"```

1. Log Analytics aracısını Azure VM 'lerinize otomatik sağlama:
    
    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```
    
    ```Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision```

    > [!NOTE]
    > Azure sanal makinelerinizin Azure Güvenlik Merkezi tarafından otomatik olarak korunduğundan emin olmak için otomatik sağlamayı etkinleştirmeniz önerilir.
    >

1. İsteğe bağlı: seçtiğiniz abonelikler için [güvenlik iletişim ayrıntılarını tanımlamanız](security-center-provide-security-contact-details.md) önerilir ve bu, Güvenlik Merkezi tarafından oluşturulan uyarıların ve bildirimlerin alıcıları olarak kullanılacaktır:

    ```Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -AlertAdmin -NotifyOnAlert```

1. Varsayılan güvenlik merkezi ilkesi girişim atamasını yapın:

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'```

    ```$Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ 'Azure Security Benchmark'} New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'```

Azure Güvenlik Merkezi 'Ni PowerShell ile başarıyla eklendi.

Artık bu PowerShell cmdlet 'lerini, abonelikler ve kaynaklar arasında programlı bir şekilde yinelemek için Otomasyon betikleriyle kullanabilirsiniz. Bu, zamandan tasarruf eder ve insan hatası olasılığını azaltır. Bu [örnek betiği](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) başvuru olarak kullanabilirsiniz.




## <a name="see-also"></a>Ayrıca bkz.
Güvenlik Merkezi 'ne ekleme işlemini otomatikleştirmek için PowerShell 'i nasıl kullanabileceğiniz hakkında daha fazla bilgi edinmek için aşağıdaki makaleye bakın:

* [Az. Security](/powershell/module/az.security)

Güvenlik Merkezi hakkında daha fazla bilgi edinmek için aşağıdaki makaleye bakın:

* [Azure Güvenlik Merkezi'nde güvenlik ilkelerini ayarlama](tutorial-security-policy.md) -- Azure abonelikleriniz ve kaynak gruplarınız için güvenlik ilkelerini yapılandırma hakkında bilgi edinin.
* [Azure Güvenlik Merkezi 'nde güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md) --güvenlik uyarılarını yönetme ve yanıtlama hakkında bilgi edinin.