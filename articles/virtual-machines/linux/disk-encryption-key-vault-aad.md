---
title: Azure AD ile Azure disk şifrelemesi için bir Anahtar Kasası oluşturma ve yapılandırma (önceki sürüm)
description: Bu makalede, IaaS VM 'Leri için Microsoft Azure disk şifrelemesi kullanma önkoşulları sağlanır.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: be709fcbb45c95f092b24b53fd0c506187fcd8b3
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828542"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Azure AD ile Azure disk şifrelemesi için bir Anahtar Kasası oluşturma ve yapılandırma (önceki sürüm)

**Azure disk şifrelemesi 'nin yeni sürümü, VM disk şifrelemeyi etkinleştirmek için bir Azure AD uygulama parametresi sağlama gereksinimini ortadan kaldırır. Yeni sürümde, artık şifrelemeyi Etkinleştir adımını kullanarak Azure AD kimlik bilgilerini sağlamanız gerekmez. Yeni sürüm kullanılarak Azure AD uygulama parametreleri olmadan tüm yeni VM 'Ler şifrelenmelidir. Yeni sürümü kullanarak VM disk şifrelemesini etkinleştirme yönergelerini görüntülemek için bkz. [Azure disk şifrelemesi](disk-encryption-overview.md). Azure AD uygulama parametreleriyle zaten şifrelenmiş VM 'Ler hala desteklenmektedir ve AAD sözdizimi ile sürdürülmeye devam etmelidir.**

Azure disk şifrelemesi, disk şifreleme anahtarlarını ve gizli dizileri denetlemek ve yönetmek için Azure Key Vault kullanır.  Anahtar kasaları hakkında daha fazla bilgi için bkz. [Azure Key Vault kullanmaya başlama](../../key-vault/key-vault-get-started.md) ve [anahtar kasanızın güvenliğini sağlama](../../key-vault/key-vault-secure-your-key-vault.md). 

Azure AD ile Azure disk şifrelemesi ile kullanım için bir Anahtar Kasası oluşturma ve yapılandırma (önceki sürüm) üç adımdan oluşur:

1. Bir Anahtar Kasası oluşturun. 
2. Bir Azure AD uygulaması ve hizmet sorumlusu ayarlayın.
3. Azure AD uygulaması için Anahtar Kasası erişim ilkesini ayarlayın.
4. Anahtar Kasası Gelişmiş erişim ilkeleri ayarlayın.
 
Ayrıca, anahtar şifreleme anahtarı (KEK) oluşturabilir veya içeri aktarabilirsiniz.

[Araçların nasıl yükleneceğine ve Azure 'a nasıl bağlanaceğine](disk-encryption-key-vault.md#install-tools-and-connect-to-azure)ilişkin adımlar için bkz. [Azure disk şifrelemesi Için Anahtar Kasası oluşturma ve yapılandırma](disk-encryption-key-vault.md) makalesine bakın.

> [!Note]
> Bu makaledeki adımlar, [Azure disk şifrelemesi ÖNKOŞULLARı CLI betiği](https://github.com/ejarvi/ade-cli-getting-started) ve [Azure disk şifrelemesi önkoşulları PowerShell betiği](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)içinde otomatikleştirilir.


## <a name="create-a-key-vault"></a>Anahtar Kasası oluşturma 
Azure disk şifrelemesi, Anahtar Kasası aboneliğinizdeki disk şifreleme anahtarlarını ve gizli dizileri denetlemenize ve yönetmenize yardımcı olmak için [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) ile tümleşiktir. Bir Anahtar Kasası oluşturabilir veya mevcut bir Azure disk şifrelemesi kullanabilirsiniz. Anahtar kasaları hakkında daha fazla bilgi için bkz. [Azure Key Vault kullanmaya başlama](../../key-vault/key-vault-get-started.md) ve [anahtar kasanızın güvenliğini sağlama](../../key-vault/key-vault-secure-your-key-vault.md). Bir Anahtar Kasası oluşturmak için Kaynak Yöneticisi şablonu, Azure PowerShell veya Azure CLı kullanabilirsiniz. 


>[!WARNING]
>Şifreleme gizli dizileri 'nin bölgesel sınırların dışına bulunmadığından emin olmak için, Azure disk şifrelemesi Key Vault ve VM 'Lerin aynı bölgede birlikte bulunması gerekir. Şifrelenecek VM ile aynı bölgedeki bir Key Vault oluşturun ve kullanın. 


### <a name="bkmk_KVPSH"></a>PowerShell ile Anahtar Kasası oluşturma

[New-Azkeykasa](/powershell/module/az.keyvault/New-azKeyVault) cmdlet 'ini kullanarak Azure PowerShell bir Anahtar Kasası oluşturabilirsiniz. Key Vault ek cmdlet 'ler için, bkz [. az. Keykasası](/powershell/module/az.keyvault/). 

1. Gerekirse, [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)ile yeni bir kaynak grubu oluşturun.  Veri merkezi konumlarını listelemek için [Get-AzLocation](/powershell/module/az.resources/get-azlocation)kullanın. 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. [New-Azkeykasasını](/powershell/module/az.keyvault/New-azKeyVault) kullanarak yeni bir Anahtar Kasası oluşturma
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Daha sonra diskleri şifrelerken kullanılmak üzere döndürülen **kasa adı**, **kaynak grubu adı**, **kaynak kimliği**, **kasa URI 'si**ve **nesne kimliği** ' ni unutmayın. 


### <a name="bkmk_KVCLI"></a>Azure CLı ile Anahtar Kasası oluşturma
[Az keykasa](/cli/azure/keyvault#commands) komutlarını kullanarak anahtar kasanızı Azure CLI ile yönetebilirsiniz. Bir Anahtar Kasası oluşturmak için [az keykasacreate](/cli/azure/keyvault#az-keyvault-create)komutunu kullanın.

1. Gerekirse, [az Group Create](/cli/azure/group#az-group-create)ile yeni bir kaynak grubu oluşturun. Konumları listelemek için [az Account List-Locations](/cli/azure/account#az-account-list) kullanın 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. [Az keykasa Create](/cli/azure/keyvault#az-keyvault-create)komutunu kullanarak yeni bir Anahtar Kasası oluşturun.
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. **Kasa adı** (ad), **kaynak grubu adı**, **kaynak kimliği** (kimlik), **kasa URI 'Si**ve daha sonra kullanılmak üzere döndürülen **nesne kimliği** ' ni unutmayın. 

### <a name="bkmk_KVRM"></a>Kaynak Yöneticisi şablonuyla Anahtar Kasası oluşturma

[Kaynak Yöneticisi şablonunu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)kullanarak bir Anahtar Kasası oluşturabilirsiniz.

1. Azure hızlı başlangıç şablonunda **Azure 'A dağıt**' a tıklayın.
2. Abonelik, kaynak grubu, kaynak grubu konumu, Key Vault adı, nesne KIMLIĞI, yasal koşullar ve anlaşma ' ı seçin ve ardından **satın al**' a tıklayın. 


## <a name="bkmk_ADapp"></a>Bir Azure AD uygulaması ve hizmet sorumlusu ayarlama 
Azure 'da çalışan bir VM 'de şifrelemenin etkinleştirilmesi gerektiğinde, Azure disk şifrelemesi anahtar kasanıza şifreleme anahtarları oluşturur ve yazar. Anahtar Kasanızda şifreleme anahtarlarını yönetmek için Azure AD kimlik doğrulaması gerekir. Bu amaçla bir Azure AD uygulaması oluşturun. Kimlik doğrulama amacıyla, istemci gizli tabanlı kimlik doğrulaması veya [istemci sertifikası tabanlı Azure AD kimlik doğrulaması](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)kullanabilirsiniz.


### <a name="bkmk_ADappPSH"></a>Azure PowerShell ile bir Azure AD uygulaması ve hizmet sorumlusu ayarlama 
Aşağıdaki komutları yürütmek için [Azure AD PowerShell modülünü](/powershell/azure/active-directory/install-adv2)alın ve kullanın. 

1. [New-AzADApplication](/powershell/module/az.resources/new-azadapplication) PowerShell cmdlet 'ini kullanarak BIR Azure AD uygulaması oluşturun. MyApplicationHomePage ve MyApplicationUri istediğiniz herhangi bir değer olabilir.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $AzureAdApplication. ApplicationId, Azure AD ClientID 'dir ve $aadClientSecret, daha sonra Azure disk şifrelemeyi etkinleştirmek için kullanacağınız istemci gizli anahtarı olur. Azure AD İstemci gizliliğini uygun şekilde koruma. @No__t çalıştırmak-0, ApplicationId gösterecektir.


### <a name="bkmk_ADappCLI"></a>Azure CLı ile bir Azure AD uygulaması ve hizmet sorumlusu ayarlama

Hizmet sorumlularını, [az ad SP](/cli/azure/ad/sp) komutlarını kullanarak Azure CLI ile yönetebilirsiniz. Daha fazla bilgi için bkz. [Azure hizmet sorumlusu oluşturma](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Yeni bir hizmet sorumlusu oluşturun.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  Döndürülen uygulama kimliği, diğer komutlarda kullanılan Azure AD ClientID 'dir. Bu, az keykasası Set-Policy için kullanacağınız SPN 'YI de kullanabilirsiniz. Parola, daha sonra Azure disk şifrelemeyi etkinleştirmek için kullanmanız gereken istemci sırrı olur. Azure AD İstemci gizliliğini uygun şekilde koruma.
 
### <a name="bkmk_ADappRM"></a>Azure portal bir Azure AD uygulaması ve hizmet sorumlusu ayarlama
Azure AD uygulaması oluşturmak için, [kaynak makalesine erişebilen Azure Active Directory bir uygulama ve hizmet sorumlusu oluşturmak için portalı kullanma](../../active-directory/develop/howto-create-service-principal-portal.md) adımlarını kullanın. Aşağıda listelenen her adımın tamamlanması doğrudan makale bölümüne götürür. 

1. [Gerekli izinleri doğrulama](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Azure Active Directory uygulaması oluşturma](../../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - Uygulamayı oluştururken istediğiniz ad ve oturum açma URL 'sini kullanabilirsiniz.
3. [Uygulama kimliğini ve kimlik doğrulama anahtarını alın](../../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in). 
     - Kimlik doğrulama anahtarı, istemci sırrı ve set-AzVMDiskEncryptionExtension için AadClientSecret olarak kullanılır. 
        - Kimlik doğrulama anahtarı, uygulama tarafından Azure AD 'de oturum açmak için kimlik bilgileri olarak kullanılır. Azure portal, bu gizliliğe anahtarlar denir, ancak anahtar kasalarıyla ilgili hiçbir ilişki yoktur. Bu gizli anahtarı uygun şekilde koruyun. 
     - Uygulama KIMLIĞI daha sonra set-AzVMDiskEncryptionExtension için Aadclitıd ve set-AzKeyVaultAccessPolicy için ServicePrincipalName olarak kullanılacaktır. 

## <a name="bkmk_KVAP"></a>Azure AD uygulaması için Anahtar Kasası erişim ilkesini ayarlama
Şifreleme gizli dizilerini belirtilen bir Key Vault yazmak için Azure disk şifrelemesi, Key Vault gizli dizileri yazma izinlerine sahip Azure Active Directory uygulamanın Istemci KIMLIĞI ve Istemci gizli anahtarı gerektirir. 

> [!NOTE]
> Azure disk şifrelemesi, Azure AD İstemci uygulamanıza aşağıdaki erişim ilkelerini yapılandırmanızı gerektirir: _WrapKey_ ve izinleri _ayarlayın_ .

### <a name="bkmk_KVAPPSH"></a>Azure PowerShell Azure AD uygulaması için Anahtar Kasası erişim ilkesini ayarlama
Azure AD uygulamanız, kasadaki anahtarlara veya gizli anahtara erişmek için haklara ihtiyaç duyuyor. Uygulamaya izin vermek için [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet 'ini kullanarak, istemci kimliğini (uygulama kaydedildiğinde oluşturulan) _– servicePrincipalName_ parametre değeri olarak kullanın. Daha fazla bilgi edinmek için [Azure Key Vault](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)blog gönderisine bakın. adım adım. 

1. PowerShell ile AD uygulaması için Anahtar Kasası erişim ilkesini ayarlayın.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="bkmk_KVAPCLI"></a>Azure CLı ile Azure AD uygulaması için Anahtar Kasası erişim ilkesini ayarlama
Erişim ilkesini ayarlamak için [az keykasası Set-Policy](/cli/azure/keyvault#az-keyvault-set-policy) komutunu kullanın. Daha fazla bilgi için bkz. [clı 2,0 kullanarak Key Vault yönetme](../../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

Aşağıdaki komutla gizli anahtar almak ve anahtarları kaydırmak için Azure CLı erişimi aracılığıyla oluşturduğunuz hizmet sorumlusuna izin verin:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="bkmk_KVAPRM"></a>Portal ile Azure AD uygulaması için Anahtar Kasası erişim ilkesini ayarlama

1. Anahtar kasanızın bulunduğu kaynak grubunu açın.
2. Anahtar kasanızı seçin, **erişim ilkeleri**' ne gidin ve **Yeni Ekle**' ye tıklayın.
3. **Asıl seçin**altında oluşturduğunuz Azure AD uygulamasını arayın ve seçin. 
4. **Anahtar izinleri**Için, **şifreleme işlemleri**altında **Sarla tuşu** ' nı işaretleyin.
5. **Gizli izinler**Için, **gizli yönetim işlemleri**altında **Ayarla** ' yı işaretleyin.
6. Erişim ilkesini kaydetmek için **Tamam** ' ı tıklatın. 

![Azure Key Vault şifreleme işlemleri-Wrap tuşu](./media/disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault gizli izinleri-ayarla](./media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="bkmk_KVper"></a>Anahtar Kasası Gelişmiş erişim ilkelerini ayarlama
Azure platformunun, birimleri önyüklemek ve şifrelerini çözmek için sanal makine için kullanılabilir hale getirmek üzere anahtar kasasındaki şifreleme anahtarlarına veya gizli anahtarlara erişmesi gerekir. Anahtar kasasında disk şifrelemeyi etkinleştirin veya dağıtımlar başarısız olur.  

### <a name="bkmk_KVperPSH"></a>Azure PowerShell ile Anahtar Kasası Gelişmiş erişim ilkeleri ayarlama
 Anahtar Kasası için disk şifrelemeyi etkinleştirmek üzere [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) Anahtar Kasası PowerShell cmdlet 'ini kullanın.

  - **Key Vault disk şifrelemesi Için etkinleştir:** Azure disk şifrelemesi için EnabledForDiskEncryption gereklidir.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Gerekirse dağıtım için Key Vault etkinleştirin:** Bu Anahtar Kasası kaynak oluşturma bölümünde başvuruluyorsa, örneğin bir sanal makine oluştururken, Microsoft. COMPUTE kaynak sağlayıcısı 'nın bu anahtar kasasından gizli dizileri almasını sağlar.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Gerekirse, şablon dağıtımı için Key Vault etkinleştirin:** Bu anahtar kasası bir şablon dağıtımında başvuruluyorsa, Azure Resource Manager Bu anahtar kasasından gizli dizileri almasını sağlar.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="bkmk_KVperCLI"></a>Azure CLı kullanarak Anahtar Kasası Gelişmiş erişim ilkelerini ayarlama
Anahtar Kasası için disk şifrelemeyi etkinleştirmek üzere [az keykasatıon Update](/cli/azure/keyvault#az-keyvault-update) kullanın. 

 - **Key Vault disk şifrelemesi Için etkinleştir:** Etkin-disk şifrelemesi gereklidir. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Gerekirse dağıtım için Key Vault etkinleştirin:** Sanal makinelerin kasadan gizli dizi olarak depolanan sertifikaları almasına izin verin.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Gerekirse, şablon dağıtımı için Key Vault etkinleştirin:** Kaynak Yöneticisi kasalardan gizli dizileri almasına izin verin.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a>Azure portal aracılığıyla Anahtar Kasası Gelişmiş erişim ilkeleri ayarlama

1. Keykasanızı seçin, **erişim ilkeleri**' ne gidin ve **Gelişmiş erişim Ilkelerini göstermek için tıklayın**.
2. **Birim şifrelemesi Için Azure disk şifrelemesi 'ne erişimi etkinleştir**etiketli kutuyu seçin.
3. **Dağıtım Için Azure sanal makinelerine erişimi etkinleştir** ' i seçin ve/veya gerekirse **şablon dağıtımı Için Azure Resource Manager erişimi etkinleştirin**. 
4. **Kaydet**'e tıklayın.

![Azure Anahtar Kasası Gelişmiş erişim ilkeleri](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a>Anahtar şifreleme anahtarı ayarlama (isteğe bağlı)
Şifreleme anahtarları için ek bir güvenlik katmanı için anahtar şifreleme anahtarı (KEK) kullanmak istiyorsanız, anahtar kasanıza bir KEK ekleyin. Anahtar kasasında anahtar şifreleme anahtarı oluşturmak için [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet 'ini kullanın. Ayrıca, şirket içi anahtar yönetimi HSM 'nizden bir KEK içeri aktarabilirsiniz. Daha fazla bilgi için bkz. [Key Vault belgeleri](../../key-vault/key-vault-hsm-protected-keys.md). Anahtar şifreleme anahtarı belirtildiğinde Azure disk şifrelemesi, Key Vault yazmadan önce şifreleme gizli dizilerini kaydırmak için bu anahtarı kullanır. 

* Anahtar oluştururken bir RSA anahtar türü kullanın. Azure disk şifrelemesi henüz eliptik eğri anahtarlarını kullanmayı desteklemiyor.

* Anahtar Kasası gizli ve KEK URL 'Lerinin sürümü oluşturulmalıdır. Azure, sürüm oluşturma kısıtlaması uygular. Geçerli gizli ve KEK URL 'Leri için aşağıdaki örneklere bakın:

  * Geçerli bir gizli dizi URL 'SI örneği: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Geçerli bir KEK URL örneği: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure disk şifrelemesi, Anahtar Kasası gizli dizileri ve KEK URL 'Lerinin bir parçası olarak bağlantı noktası numaraları belirtilmesini desteklemez. Desteklenmeyen ve desteklenen Anahtar Kasası URL 'Leri örnekleri için aşağıdaki örneklere bakın:

  * Kabul edilmeyen Anahtar Kasası URL 'SI *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Kabul edilebilir Anahtar Kasası URL 'SI: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="bkmk_KEKPSH"></a>Azure PowerShell ile anahtar şifreleme anahtarı ayarlama 
PowerShell betiğini kullanmadan önce, betikteki adımları anlamak için Azure disk şifrelemesi önkoşulları hakkında bilgi sahibi olmanız gerekir. Örnek betik, ortamınız için değişiklikler gerektirebilir. Bu betik, tüm Azure disk şifrelemesi önkoşullarını oluşturur ve var olan bir IaaS sanal makinesini şifreler, anahtar şifreleme anahtarını kullanarak disk şifreleme anahtarını sarmalama. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name  $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName  $KVRGname;
     
 #Step 4: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
      $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="bkmk_Cert"></a>Sertifika tabanlı kimlik doğrulaması (isteğe bağlı)
Sertifika kimlik doğrulamasını kullanmak isterseniz, anahtar kasanıza bir tane yükleyebilir ve istemciye dağıtabilirsiniz. PowerShell betiğini kullanmadan önce, betikteki adımları anlamak için Azure disk şifrelemesi önkoşulları hakkında bilgi sahibi olmanız gerekir. Örnek betik, ortamınız için değişiklikler gerektirebilir.

     
 ```powershell

 # Fill in "MyKeyVaultResourceGroup", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="bkmk_CertKEK"></a>Sertifika tabanlı kimlik doğrulaması ve bir KEK (isteğe bağlı)

Sertifika kimlik doğrulamasını kullanmak ve şifreleme anahtarını bir KEK ile kaydırmak isterseniz, aşağıdaki betiği örnek olarak kullanabilirsiniz. PowerShell betiğini kullanmadan önce, betikteki adımları anlamak için önceki tüm Azure disk şifrelemesi önkoşullarını bilmeniz gerekir. Örnek betik, ortamınız için değişiklikler gerektirebilir.

> [!IMPORTANT]
> Azure AD Sertifika tabanlı kimlik doğrulaması şu anda Linux sanal makinelerinde desteklenmez.



     
 ```powershell
# Fill in 'MyKeyVaultResourceGroup', 'MySecureVault', and 'MyLocation' (if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>Sonraki adımlar

[Linux sanal makinelerinde Azure AD ile Azure disk şifrelemesini etkinleştirme (önceki sürüm)](disk-encryption-linux-aad.md)
