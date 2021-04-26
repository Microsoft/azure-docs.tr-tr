---
title: Cloud Services Dağıtma (genişletilmiş destek)-SDK
description: Azure SDK kullanarak Cloud Services (genişletilmiş destek) dağıtma
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: d36bae57a9e1609e053326cf7288b5b1bc470cef
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166896"
---
# <a name="deploy-cloud-services-extended-support-by-using-the-azure-sdk"></a>Azure SDK kullanarak Cloud Services (genişletilmiş destek) dağıtma

Bu makalede, birden çok rolü olan (Web rolü ve çalışan rolü) ve uzak masaüstü uzantısının bulunduğu bir Cloud Services (genişletilmiş destek) örneğini dağıtmak için [Azure SDK](https://azure.microsoft.com/downloads/) 'nın nasıl kullanılacağı gösterilmektedir. Cloud Services (genişletilmiş destek), Azure Resource Manager dayalı Azure Cloud Services dağıtım modelidir.

> [!IMPORTANT]
> Cloud Services (genişletilmiş destek) Şu anda genel önizleme aşamasındadır. Bu önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve bunu üretim iş yükleri için önermiyoruz. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Başlamadan önce

Cloud Services (genişletilmiş destek) için [dağıtım önkoşullarını](deploy-prerequisite.md) gözden geçirin ve ilişkili kaynaklar oluşturun.

## <a name="deploy-cloud-services-extended-support"></a>Cloud Services Dağıtma (genişletilmiş destek)
1. [Azure işlem SDK 'Sı NuGet paketini](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute/43.0.0-preview) yükledikten sonra standart bir kimlik doğrulama mekanizması kullanarak istemciyi başlatın.

    ```csharp
        public class CustomLoginCredentials : ServiceClientCredentials
    {
        private string AuthenticationToken { get; set; }
        public override void InitializeServiceClient<T>(ServiceClient<T> client)
           {
               var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantID}");
               var credential = new ClientCredential(clientId: "{clientID}", clientSecret: "{clientSecret}");
               var result = authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);
               if (result == null) throw new InvalidOperationException("Failed to obtain the JWT token");
               AuthenticationToken = result.Result.AccessToken;
           }
        public override async Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
           {
                if (request == null) throw new ArgumentNullException("request");
                if (AuthenticationToken == null) throw new InvalidOperationException("Token Provider Cannot Be Null");
                request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", AuthenticationToken);
                request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
                //request.Version = new Version(apiVersion);
                await base.ProcessHttpRequestAsync(request, cancellationToken);
            }
    
        var creds = new CustomLoginCredentials();
        m_subId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
        ResourceManagementClient m_ResourcesClient = new ResourceManagementClient(creds);
        NetworkManagementClient m_NrpClient = new NetworkManagementClient(creds);
        ComputeManagementClient m_CrpClient = new ComputeManagementClient(creds);
        StorageManagementClient m_SrpClient = new StorageManagementClient(creds);
        m_ResourcesClient.SubscriptionId = m_subId;
        m_NrpClient.SubscriptionId = m_subId;
        m_CrpClient.SubscriptionId = m_subId;
        m_SrpClient.SubscriptionId = m_subId;
    ```

2. Azure Resource Manager NuGet paketini yükleyerek yeni bir kaynak grubu oluşturun.

    ```csharp 
    var resourceGroups = m_ResourcesClient.ResourceGroups;
    var m_location = “East US”;
    var resourceGroupName = "ContosoRG";//provide existing resource group name, if created already
    var resourceGroup = new ResourceGroup(m_location); 
    resourceGroup = await resourceGroups.CreateOrUpdateAsync(resourceGroupName, resourceGroup);
    ```

3. Hizmet paketi (. cspkg) ve hizmet yapılandırma (. cscfg) dosyalarını depolayabileceğiniz bir depolama hesabı ve kapsayıcı oluşturun. [Azure Storage NuGet paketini](https://www.nuget.org/packages/Azure.Storage.Common/)yükler. Mevcut bir depolama hesabı kullanıyorsanız, bu adım isteğe bağlıdır. Depolama hesabı adı benzersiz olmalıdır.

    ```csharp
    string storageAccountName = “ContosoSAS”
    var stoInput = new StorageAccountCreateParameters
       {
            Location = m_location,
            Kind = Microsoft.Azure.Management.Storage.Models.Kind.StorageV2,
            Sku = new Microsoft.Azure.Management.Storage.Models.Sku(SkuName.StandardRAGRS),
        };
            StorageAccount storageAccountOutput = m_SrpClient.StorageAccounts.Create(rgName,
            storageAccountName, stoInput);
            bool created = false;
            while (!created)
               {
                    Thread.Sleep(600);
                    var stos = m_SrpClient.StorageAccounts.ListByResourceGroup(rgName);
                    created =
                    stos.Any(
                        t =>
                        StringComparer.OrdinalIgnoreCase.Equals(t.Name, storageAccountName));
                }
        
    StorageAccount storageAccountOutput = m_SrpClient.StorageAccounts.GetProperties(rgName, storageAccountName);.
    var accountKeyResult = m_SrpClient.StorageAccounts.ListKeysWithHttpMessagesAsync(rgName, storageAccountName).Result;
    CloudStorageAccount storageAccount = new CloudStorageAccount(new StorageCredentials(storageAccountName, accountKeyResult.Body.Keys.FirstOrDefault(). Value), useHttps: true);
        
    var blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExistsAsync().Wait();
    sharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddDays(-1);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddDays(2);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
    ```

4. Hizmet paketi (. cspkg) dosyasını depolama hesabına yükleyin. Paket URL 'SI, herhangi bir depolama hesabından paylaşılan erişim imzası (SAS) URI 'SI olabilir.

    ```csharp
    CloudBlockBlob cspkgblockBlob = container.GetBlockBlobReference(“ContosoApp.cspkg”);
    cspkgblockBlob.UploadFromFileAsync(“./ContosoApp/ContosoApp.cspkg”). Wait();
    
    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string cspkgsasContainerToken = cspkgblockBlob.GetSharedAccessSignature(sasConstraints);
    
    //Return the URI string for the container, including the SAS token.
    string cspkgSASUrl = cspkgblockBlob.Uri + cspkgsasContainerToken;
    ```

5. Hizmet yapılandırma (. cscfg) dosyanızı depolama hesabına yükleyin. Hizmet yapılandırmasını dize XML veya URL biçimi olarak belirtin.

    ```csharp
    CloudBlockBlob cscfgblockBlob = container.GetBlockBlobReference(“ContosoApp.cscfg”);
    cscfgblockBlob.UploadFromFileAsync(“./ContosoApp/ContosoApp.cscfg”). Wait();
    
    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string sasCscfgContainerToken = cscfgblockBlob.GetSharedAccessSignature(sasConstraints);
    
    //Return the URI string for the container, including the SAS token.
    string cscfgSASUrl = cscfgblockBlob.Uri + sasCscfgContainerToken;
    ```

6. Sanal ağı ve alt ağı oluşturun. [Azure Network NuGet paketini](https://www.nuget.org/packages/Azure.ResourceManager.Network/)yükler. Mevcut bir ağ ve alt ağ kullanıyorsanız, bu adım isteğe bağlıdır.

    ```csharp
    VirtualNetwork vnet = new VirtualNetwork(name: vnetName) 
       { 
            AddressSpace = new AddressSpace 
               { 
                    AddressPrefixes = new List<string> { "10.0.0.0/16" } 
               }, 
                    Subnets = new List<Subnet> 
                    { 
                        new Subnet(name: subnetName) 
                        { 
                            AddressPrefix = "10.0.0.0/24" 
                        } 
                    }, 
                    Location = m_location 
               }; 
    m_NrpClient.VirtualNetworks.CreateOrUpdate(resourceGroupName, “ContosoVNet”, vnet);
    ```

7. Genel IP adresi oluşturun ve genel IP adresinin DNS etiketi özelliğini ayarlayın. Cloud Services (genişletilmiş destek) yalnızca [temel] ( https://docs.microsoft.com/azure/virtual-network/public-ip-addresses#basic) SKU genel IP adreslerini destekler. Standart SKU genel IP 'Leri Cloud Services çalışmaz.
Statik IP kullanıyorsanız, bunu hizmet yapılandırma (. cscfg) dosyasında bir Ayrılmış IP olarak başvurmanız gerekir

    ```csharp
    PublicIPAddress publicIPAddressParams = new PublicIPAddress(name: “ContosIp”) 
       { 
            Location = m_location, 
            PublicIPAllocationMethod = IPAllocationMethod.Dynamic, 
            DnsSettings = new PublicIPAddressDnsSettings() 
               { 
                    DomainNameLabel = “contosoappdns” 
               } 
       }; 
    PublicIPAddress publicIpAddress = m_NrpClient.PublicIPAddresses.CreateOrUpdate(resourceGroupName, publicIPAddressName, publicIPAddressParams);
    ```

8. Bir ağ profili nesnesi oluşturun ve genel IP adresini yük dengeleyicinin ön ucunda ilişkilendirin. Azure platformu, bulut hizmeti kaynağıyla aynı abonelikte otomatik olarak bir ' klasik ' SKU yük dengeleyici kaynağı oluşturur. Yük dengeleyici kaynağı ARM 'de salt okuma kaynağıdır. Kaynaktaki tüm güncelleştirmeler yalnızca bulut hizmeti dağıtım dosyaları (. cscfg &. csdef) aracılığıyla desteklenir

    ```csharp
    LoadBalancerFrontendIPConfiguration feipConfiguration = new LoadBalancerFrontendIPConfiguration() 
        { 
            Name = “ContosoFe”, 
            Properties = new LoadBalancerFrontendIPConfigurationProperties() 
                { 
                PublicIPAddress = new CM.SubResource() 
                    { 
                        Id = $"/subscriptions/{m_subId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/publicIPAddresses/{publicIPAddressName}", 
                    } 
                } 
        }; 
    
    CloudServiceNetworkProfile cloudServiceNetworkProfile = new CloudServiceNetworkProfile() 
        { 
            LoadBalancerConfigurations = new List<LoadBalancerConfiguration>() 
                { 
                    new LoadBalancerConfiguration() 
                       { 
                        Name  = 'ContosoLB', 
                        Properties = new LoadBalancerConfigurationProperties() 
                            { 
                            FrontendIPConfigurations = new List<LoadBalancerFrontendIPConfiguration>() 
                                { 
                                feipConfig 
                                } 
                            } 
                        } 
                } 
        }; 
    
    ```

9. Anahtar kasası oluşturma. Bu Anahtar Kasası Cloud Services (genişletilmiş destek) rolleriyle ilişkili sertifikaları depolamak için kullanılacaktır. Anahtar Kasası Cloud Services (genişletilmiş destek) örneği ile aynı bölgede ve abonelikte yer almalıdır ve benzersiz bir ada sahip olmalıdır. Daha fazla bilgi için bkz. [Azure Cloud Services sertifikaları kullanma (genişletilmiş destek)](certificates-and-key-vault.md).

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosoOrg” -Location “East US”
    ```

10. Anahtar kasasının erişim ilkesini güncelleştirin ve Kullanıcı hesabınıza sertifika izinleri verin.

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosoOrg'      -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete
    ```

    Alternatif olarak, erişim ilkesini nesne KIMLIĞI (çalıştırarak alabileceğiniz) ile ayarlayın `Get-AzADUser` .

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -     ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates          create,get,list,delete
    ```

11. Bu örnekte, bir anahtar kasasına kendinden imzalı bir sertifika ekleyeceğiz. Sertifika parmak izinin, Cloud Services (genişletilmiş destek) rollerinde dağıtım için hizmet yapılandırma (. cscfg) dosyasına eklenmesi gerekir.

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -       SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -      CertificatePolicy $Policy
    ```

12. Bir işletim sistemi profili nesnesi oluşturun. İşletim sistemi profili Cloud Services (genişletilmiş destek) rolleriyle ilişkili sertifikaları belirtir. Bu, önceki adımda oluşturduğumuz aynı sertifikadır.

    ```csharp
    CloudServiceOsProfile cloudServiceOsProfile = 
        new CloudServiceOsProfile 
           { 
                Secrets = new List<CloudServiceVaultSecretGroup> 
                   { 
                        New CloudServiceVaultSecretGroup { 
                        SourceVault = <sourceVault>, 
                        VaultCertificates = <vaultCertificates> 
                        } 
                   } 
           };
    ```

13. Rol profili nesnesi oluşturun. Rol profili, bir SKU için ad, kapasite ve katman gibi role özgü özellikleri tanımlar. 

    Bu örnekte, iki rol tanımlayacağız: ContosoFrontend ve Contosoarka ucu. Rol profili bilgileri, hizmet yapılandırma (. cscfg) dosyasında ve hizmet tanımı (. csdef) dosyasında tanımlanan rol yapılandırmasıyla eşleşmelidir.

    ```csharp
    CloudServiceRoleProfile cloudServiceRoleProfile = new CloudServiceRoleProfile()
       {
            Roles = new List<CloudServiceRoleProfileProperties>();
            
                // foreach role in cloudService
                roles.Add(new CloudServiceRoleProfileProperties()
                    {
                        Name = 'ContosoFrontend',
                        Sku = new CloudServiceRoleSku
                        {
                            Name = 'Standard_D1_v2',
                            Capacity = 2,
                            Tier = 'Standard'
                        }
                    );
    
                roles.Add(new CloudServiceRoleProfileProperties()
                    {
                        Name = 'ContosoBackend',
                        Sku = new CloudServiceRoleSku
                        {
                            Name = 'Standard_D1_v2',
                            Capacity = 2,
                            Tier = 'Standard'
                        }
                    );
    
                    }
                    }
    ```

14. Seçim Cloud Services (genişletilmiş destek) örneğine eklemek istediğiniz bir uzantı profili nesnesi oluşturun. Bu örnekte, RDP uzantısı ekleyeceğiz.

    ```csharp
    string rdpExtensionPublicConfig = "<PublicConfig>" +
        "<UserName>adminRdpTest</UserName>" +
        "<Expiration>2021-10-27T23:59:59</Expiration>" +
        "</PublicConfig>";
        string rdpExtensionPrivateConfig = "<PrivateConfig>" +
        "<Password>VsmrdpTest!</Password>" +
        "</PrivateConfig>";
    
        Extension rdpExtension = new Extension
                {
                    Name = name,
                    Properties = new CloudServiceExtensionProperties
                    {
                        Publisher = "Microsoft.Windows.Azure.Extensions",
                        Type = "RDP",
                        TypeHandlerVersion = "1.2.1",,
                        AutoUpgradeMinorVersion = true,
                        Settings = rdpExtensionPublicConfig,
                        ProtectedSettings = rdpExtensionPrivateConfig,
                        RolesAppliedTo = [“*”],
                    }
                };
                        
    CloudServiceExtensionProfile cloudServiceExtensionProfile = new CloudServiceExtensionProfile
        {
            Extensions = rdpExtension
        };
    ```

15. Cloud Services (genişletilmiş destek) örneğinin dağıtımını oluşturun.

    ```csharp
    CloudService cloudService = new CloudService
        {
            Properties = new CloudServiceProperties
                {
                    RoleProfile = cloudServiceRoleProfile
                    Configuration = < Add Cscfg xml content here>,
                    // ConfigurationUrl = <Add your configuration URL here>,
                    PackageUrl = <Add cspkg SAS url here>,
                    ExtensionProfile = cloudServiceExtensionProfile,
                    OsProfile= cloudServiceOsProfile,
                    NetworkProfile = cloudServiceNetworkProfile,
                    UpgradeMode = 'Auto'
                },
                    Location = m_location
                };
    
    CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate(“ContosOrg”, “ContosoCS”, cloudService);
    ```

## <a name="next-steps"></a>Sonraki adımlar
- Cloud Services için [sık sorulan soruları](faq.md) gözden geçirin (genişletilmiş destek).
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), bir [şablon](deploy-template.md)veya [Visual Studio](deploy-visual-studio.md)kullanarak Cloud Services (genişletilmiş destek) dağıtın.
- [Cloud Services Için örnek deposunu ziyaret edin (genişletilmiş destek)](https://github.com/Azure-Samples/cloud-services-extended-support)
