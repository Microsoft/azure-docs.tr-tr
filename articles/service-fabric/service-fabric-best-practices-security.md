---
title: Azure Service Fabric en iyi güvenlik uygulamaları | Microsoft Docs
description: Azure Service Fabric güvenliği için en iyi uygulamalar.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 75edb385a86be849ec7c165759d3b451eab804f6
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828519"
---
# <a name="azure-service-fabric-security"></a>Azure Service Fabric güvenliği 

[Azure Güvenlik En Iyi uygulamaları](https://docs.microsoft.com/azure/security/)hakkında daha fazla bilgi için [Azure Service Fabric en iyi güvenlik uygulamalarını](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices) inceleyin

## <a name="key-vault"></a>Key Vault

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) , Azure Service Fabric uygulamalar ve kümeler için önerilen gizli dizi yönetim hizmetidir.
> [!NOTE]
> Bir Key Vault sertifikalar/gizlilikler sanal makine ölçek kümesi parolası olarak bir sanal makine ölçek kümesine dağıtılırsa, Key Vault ve sanal makine ölçek kümesinin birlikte bulunması gerekir.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>Sertifika yetkilisi tarafından verilen Service Fabric sertifikası oluştur

Bir Azure Key Vault sertifikası oluşturulabilir veya bir Key Vault içeri aktarılabilir. Bir Key Vault sertifikası oluşturulduğunda, özel anahtar Key Vault içinde oluşturulur ve sertifika sahibine hiçbir zaman gösterilmez. Key Vault bir sertifika oluşturmanın yolları aşağıda verilmiştir:

- Ortak özel anahtar çifti oluşturmak ve bir sertifikayla ilişkilendirmek için kendinden imzalı bir sertifika oluşturun. Sertifika kendi anahtarı tarafından imzalanacak. 
- Ortak özel anahtar çifti oluşturmak ve bir X. 509.440 sertifika imzalama isteği oluşturmak için el ile yeni bir sertifika oluşturun. İmzalama isteği, kayıt yetkiliniz veya sertifika yetkiliniz tarafından imzalanabilir. İmzalı x509 sertifikası, Key Vault KV sertifikasını tamamlayabilmeniz için bekleyen anahtar çiftiyle birleştirilebilir. Bu yöntem daha fazla adım gerektirse de, özel anahtar ' de oluşturulduğundan ve Key Vault kısıtlandığından daha fazla güvenlik sağlar. Bu, aşağıdaki diyagramda açıklanmaktadır. 

Daha fazla bilgi için [Azure Keykasasertifikası oluşturma yöntemlerini](https://docs.microsoft.com/azure/key-vault/create-certificate) gözden geçirin.

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Key Vault sertifikalarını Service Fabric kümesi sanal makine ölçek kümelerine dağıtma

Birlikte bulunan bir anahtar kasasından bir sanal makine ölçek kümesine sertifika dağıtmak için sanal makine ölçek kümesi [Osprofile](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)' i kullanın. Kaynak Yöneticisi Şablon özellikleri şunlardır:

```json
"secrets": [
   {
       "sourceVault": {
           "id": "[parameters('sourceVaultValue')]"
       },
       "vaultCertificates": [
          {
              "certificateStore": "[parameters('certificateStoreValue')]",
              "certificateUrl": "[parameters('certificateUrlValue')]"
          }
       ]
   }
]
```

> [!NOTE]
> Kaynak Yöneticisi Şablon dağıtımı için kasanın etkinleştirilmesi gerekir.

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Service Fabric kümeniz için sertifikanıza bir Access Control listesi (ACL) uygulayın

[Sanal makine ölçek kümesi uzantıları](https://docs.microsoft.com/cli/azure/vmss/extension?view=azure-cli-latest) yayımcısı Microsoft. Azure. servicefabric, düğümlerinizin güvenliğini yapılandırmak için kullanılır.
Service Fabric kümesi işlemleriniz için sertifikalarınızı bir ACL uygulamak için aşağıdaki Kaynak Yöneticisi şablonu özelliklerini kullanın:

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>Ortak ada göre Service Fabric kümesi sertifikasını güvenli hale getirme

Service Fabric kümenizi @no__t sertifikaya göre güvenli hale getirmek için, aşağıdaki gibi, [Certificatecommonnames](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames)Kaynak Yöneticisi şablon özelliğini kullanın:

```json
"certificateCommonNames": {
    "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
        }
    ],
    "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

> [!NOTE]
> Service Fabric kümeler, ana bilgisayarınızın sertifika deposunda bulduğu ilk geçerli sertifikayı kullanır. Bu, Windows 'ta, ortak ad ve veren parmak iziyle eşleşen son geçerlilik tarihi olan sertifika olacaktır.

\* @No__t-0SIZIN SUBDOMAIN\>.cloudapp.azure.com veya \<SIZIN SUBDOMAIN\>.trafficmanager.net gibi Azure etki alanları Microsoft 'a aittir. Sertifika yetkilileri, yetkisiz kullanıcılara etki alanları için sertifika yayınmayacak. Çoğu kullanıcının, bir sertifika yetkilisinin bu ortak ada sahip bir sertifika vermesi için bir kayıt grubundan bir etki alanı satın alması veya yetkili bir etki alanı yöneticisi olması gerekir.

DNS hizmetini, etki alanınızı bir Microsoft IP adresine çözümlemek üzere yapılandırma hakkında daha fazla bilgi için, [etki alanınızı barındırmak üzere Azure DNS](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)yapılandırma konusunu gözden geçirin.

> [!NOTE]
> Etki alanı adı sunucularınızın Azure DNS bölge adı sunucularınıza yetkisini aldıktan sonra, DNS bölgenize aşağıdaki iki kaydı ekleyin:
> - Özel etki alanınız çözülecek tüm IP adreslerine `Alias record set` olmayan etki alanı TEPESINDE için bir ' A ' kaydı.
> - Sağladığınız Microsoft Sub Domains için bir ' C ' kaydı, `Alias record set` DEĞILDIR. Örneğin, Traffic Manager veya Load Balancer DNS adını kullanabilirsiniz.

Portalınızı Service Fabric kümeniz için özel bir DNS adı görüntüleyecek şekilde güncelleştirmek üzere `"managementEndpoint"` ' ı Service Fabric kümeyi izle Kaynak Yöneticisi şablon özelliklerini güncelleştirin:

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>Service Fabric paketi gizli değerlerini şifreleme

Service Fabric paketlerinde şifrelenen ortak değerler Azure Container Registry (ACR) kimlik bilgileri, ortam değişkenleri, ayarlar ve Azure toplu eklenti depolama hesabı anahtarlarını içerir.

[Şifreleme sertifikası ayarlamak ve Windows kümelerinde gizli dizileri şifrelemek](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-windows)için:

Gizli dizinizi şifrelemek için otomatik olarak imzalanan bir sertifika oluşturun:

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

Service Fabric kümenizin sanal makine ölçek kümelerine Key Vault sertifikaları dağıtmak için [Service Fabric kümesi sanal makine ölçek kümelerine Key Vault sertifikaları dağıtma](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) ' daki yönergeleri kullanın.

Aşağıdaki PowerShell komutunu kullanarak gizli dizinizi şifreleyin ve sonra Service Fabric uygulama bildiriminizi şifreli değerle güncelleştirin:

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

[Bir şifreleme sertifikası ayarlamak ve Linux kümelerinde gizli dizileri şifrelemek](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-linux)için:

Gizli dizilerinizi şifrelemek için otomatik olarak imzalanan bir sertifika oluşturun:

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

Küme sanal makine ölçek kümelerine Service Fabric kümenizin sanal makine ölçek kümelerine [Service Fabric Key Vault sertifikaları dağıtma](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) ' daki yönergeleri kullanın.

Aşağıdaki komutları kullanarak gizli dizinizi şifreleyin ve sonra Service Fabric uygulama bildiriminizi şifreli değerle güncelleştirin:

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

Korumalı değerlerinizi şifrelemeden sonra, [Service Fabric uygulamasında şifrelenmiş gizli dizileri belirtin](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#specify-encrypted-secrets-in-an-application)ve [şifreli gizli dizileri hizmet kodundan çözün](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#decrypt-encrypted-secrets-from-service-code).

## <a name="include-certificate-in-service-fabric-applications"></a>Service Fabric uygulamalara sertifika ekleme

Uygulamanıza gizli dizi erişimi sağlamak için, uygulama bildirimine bir **Secretscercertificate** öğesi ekleyerek sertifikayı dahil edin.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>Yönetilen Hizmet Kimliği (MSI) kullanarak Azure kaynaklarında Service Fabric uygulamalarının kimliğini doğrulama

Azure kaynakları için Yönetilen kimlikler hakkında bilgi edinmek için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-does-it-work).
Azure Service Fabric kümeleri, [yönetilen hizmet kimliği](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-managed-identities-for-azure-resources)destekleyen sanal makine ölçek kümelerinde barındırılır.
Üzerinde kimlik doğrulaması yapmak için MSI tarafından kullanılabilecek hizmetlerin bir listesini almak için, bkz. [Azure Active Directory kimlik doğrulamasını destekleyen Azure hizmetleri](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-azure-ad-authentication).


Bir sanal makine ölçek kümesi veya var olan bir sanal makine ölçek kümesi oluşturma sırasında sistem tarafından atanan yönetilen kimliği etkinleştirmek için aşağıdaki `"Microsoft.Compute/virtualMachinesScaleSets"` özelliğini bildirin:

```json
"identity": { 
    "type": "SystemAssigned"
}
```
Daha fazla bilgi için bkz. [Azure kaynakları için Yönetilen kimlikler nedir?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss#system-assigned-managed-identity) .

[Kullanıcı tarafından atanan bir yönetilen kimlik](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm#create-a-user-assigned-managed-identity)oluşturduysanız, sanal makine ölçek kümesine atamak için şablonunuzda aşağıdaki kaynağı bildirin. @No__t-0 ' yı, oluşturduğunuz Kullanıcı tarafından atanan yönetilen kimliğin adıyla değiştirin:

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Service Fabric uygulamanızın yönetilen bir kimliği kullanabilmesi için, kimlik doğrulaması gereken Azure kaynaklarına izinler verilmelidir.
Aşağıdaki komutlar bir Azure kaynağına erişim izni verir:

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

Service Fabric uygulama kodunuzda, aşağıdakine benzer bir geri kalanı yaparak Azure Resource Manager için [bir erişim belirteci edinin](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token#get-a-token-using-http) :

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

Service Fabric uygulamanız daha sonra, Active Directory destekleyen Azure kaynaklarında kimlik doğrulaması yapmak için erişim belirtecini kullanabilir.
Aşağıdaki örnek, Cosmos DB kaynağı için bunun nasıl yapılacağını göstermektedir:

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```
## <a name="windows-security-baselines"></a>Windows güvenlik temelleri
[Bir taban çizgisi oluşturmak yerine, Microsoft güvenlik temelleri gibi yaygın olarak bilinen ve iyi test edilmiş sektör standardı bir yapılandırma uygulamanızı öneririz](https://docs.microsoft.com/windows/security/threat-protection/windows-security-baselines). bunları sanal makine ölçek kümelerinde sağlamaya yönelik bir seçenek, Azure Istenen durum yapılandırması (DSC) uzantı işleyicisini kullanarak VM 'Leri çevrimiçi olduklarında yapılandırmak için, üretim yazılımını çalıştırırlar.

## <a name="azure-firewall"></a>Azure Güvenlik Duvarı
[Azure Güvenlik Duvarı, Azure sanal ağ kaynaklarınızı koruyan, yönetilen, bulut tabanlı bir ağ güvenlik hizmetidir. Yerleşik yüksek kullanılabilirliğe sahip ve sınırsız sayıda bulut ölçeklenebilirliğe sahip bir hizmet olarak tam durum bilgisi olmayan bir güvenlik duvarıdır.](https://docs.microsoft.com/azure/firewall/overview); Bu, giden HTTP/S trafiğini, joker karakterler de dahil olmak üzere, tam etki alanı adları (FQDN) belirtilen bir listeyle sınırlandırmanızı sağlar. Bu özellik SSL sonlandırması gerektirmez. Windows güncelleştirmeleri için [Azure Güvenlik DUVARı FQDN etiketleri](https://docs.microsoft.com/azure/firewall/fqdn-tags) kullanmanız ve Microsoft Windows Update uç noktalarına ağ trafiğini etkinleştirmek için, güvenlik duvarınız üzerinden akabilir. [Şablon kullanarak Azure Güvenlik Duvarı dağıtmak](https://docs.microsoft.com/azure/firewall/deploy-template) , Microsoft. Network/azureFirewalls kaynak şablonu tanımı için bir örnek sağlar. Service Fabric uygulamalarda ortak olan güvenlik duvarı kuralları, kümeleriniz sanal ağınız için aşağıdakilere izin verdir:

- \* download.microsoft.com
- \* servicefabric.azure.com
- *. core.windows.net

Bu güvenlik duvarı kuralları, sanal ağınızdan izin verilen hedefler olarak ServiceFabric ve Storage dahil olmak üzere izin verilen giden ağ güvenlik gruplarınızı tamamlar.

## <a name="tls-12"></a>TLS 1,2
[TSG](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

## <a name="windows-defender"></a>Windows Defender 

Varsayılan olarak, Windows Defender virüsten koruma Windows Server 2016 ' ye yüklenir. Ayrıntılar için bkz. Windows [Server 'Da Windows Defender virüsten koruma 2016](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016). Kullanıcı arabirimi bazı SKU 'Larda varsayılan olarak yüklenir, ancak gerekli değildir. Windows Defender tarafından gerçekleşen tüm performans etkisi ve kaynak tüketimi yükünü azaltmak için ve güvenlik ilkeleriniz açık kaynaklı yazılım için işlem ve yolları dışlemenize izin verirseniz, aşağıdaki sanal makine ölçek kümesi uzantısı kaynağını bildirin Service Fabric kümenizi taramalardan hariç tutmak için yönetici şablonu özellikleri:


```json
 {
    "name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
    "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
            "AntimalwareEnabled": "true",
            "Exclusions": {
                "Paths": "[concat(parameters('svcFabData'), ';', parameters('svcFabLogs'), ';', parameters('svcFabRuntime'))]",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
            },
            "RealtimeProtectionEnabled": "true",
            "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
            }
        },
        "protectedSettings": null
    }
}
```

> [!NOTE]
> Windows Defender kullanmıyorsanız yapılandırma kuralları için kötü amaçlı yazılımdan koruma belgelerinize bakın. Windows Defender, Linux üzerinde desteklenmez.

## <a name="platform-isolation"></a>Platform yalıtımı
Varsayılan olarak, Service Fabric uygulamalar, kendisini farklı formlarda bildirimi olan Service Fabric çalışma zamanına sahiptir: [ortam değişkenleri](service-fabric-environment-variables-reference.md) , uygulama ve doku dosyalarına karşılık gelen konaktaki dosya yollarına işaret eden bir uygulamaya özgü istekleri kabul eden, işlem arası iletişim uç noktası ve yapının uygulamanın kimliğini doğrulamak için kullanmasını beklediği istemci sertifikası. Hizmetin güvenilmeyen kodu barındırdığı konusunda, açıkça gerekmedikçe SF çalışma zamanına erişimi devre dışı bırakmanız önerilir. Çalışma zamanına erişim, uygulama bildiriminin Ilkeler bölümünde aşağıdaki bildirim kullanılarak kaldırılır: 

```xml
<ServiceManifestImport>
    <Policies>
        <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
    </Policies>
</ServiceManifestImport>

```

## <a name="next-steps"></a>Sonraki adımlar

* Windows Server çalıştıran VM 'lerde veya bilgisayarlarda küme oluşturma: [Windows Server için Service Fabric kümesi oluşturma](service-fabric-cluster-creation-for-windows-server.md).
* VM 'lerde veya bilgisayarlarda Linux çalıştıran bir küme oluşturun: [Linux kümesi oluşturma](service-fabric-cluster-creation-via-portal.md).
* [Service Fabric destek seçenekleri](service-fabric-support.md)hakkında bilgi edinin.

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
