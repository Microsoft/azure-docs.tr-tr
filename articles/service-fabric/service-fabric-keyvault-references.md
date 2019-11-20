---
title: Azure Service Fabric-Service Fabric uygulama Keykasası başvurularını kullanma | Microsoft Docs
description: Bu makalede, uygulama gizli dizileri için Service Fabric KeyVaultReference desteğinin nasıl kullanılacağı açıklanmaktadır.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 09/20/2019
ms.author: atsenthi
ms.openlocfilehash: b0f1a081727721ea0325276cf9edd52c6d71fb6b
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73243866"
---
#  <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>Service Fabric uygulamalar için KeyVaultReference desteği (Önizleme)

Bulut uygulamaları derlerken, uygulamanız için gereken gizli dizileri güvenli bir şekilde nasıl depolayabileceği yaygın bir zorluk vardır. Örneğin, kapsayıcı deposu kimlik bilgilerini anahtar kasasında depolamak ve uygulama bildiriminde buna başvurmak isteyebilirsiniz. Service Fabric KeyVaultReference, Service Fabric yönetilen kimliği kullanır ve keykasagizli dizileri başvurusunu kolaylaştırır. Bu makalenin geri kalanında, KeyVaultReference Service Fabric kullanımı ve bazı tipik kullanımlar yer almaktadır.

## <a name="prerequisites"></a>Önkoşullar

- Uygulama için yönetilen kimlik (MıT)
    
    Service Fabric KeyVaultReference desteği uygulamanın yönetilen kimliğini kullanır ve bu nedenle KeyVaultReferences 'ı kullanmak için uygulamalar yönetilen kimlik kullanmalıdır. Uygulamanız için yönetilen kimliği etkinleştirmek üzere bu [belgeyi](concepts-managed-identity.md) izleyin.

- Orta gizli dizi deposu (CSS).

    Merkezi gizli dizi (CSS), Service Fabric 'in şifreli yerel gizli dizileri, bir kez getirilen KeyVaultReference, CSS 'de önbelleğe alınır.

    KeyVaultReference desteği için gerekli tüm özellikleri etkinleştirmek üzere `fabricSettings` altındaki küme yapılandırmanıza aşağıda ekleyin.

    ```json
    "fabricSettings": 
    [
        ...
    {
        "parameters":  [
            "name":  "CentralSecretService"
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                }
                ],
            },
            {
                "name":  "ManagedIdentityTokenService",
                "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                }
                ]
            }
            ]
    ```

    > [!NOTE] 
    > CSS için ayrı bir şifreleme sertifikası kullanılması önerilir. Bunu "merkezde Secretservice" bölümünün altına ekleyebilirsiniz.

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```

- Anahtar Kasası 'na uygulamanın yönetilen kimlik erişim iznini verme

    Anahtar Kasası 'na yönetilen kimlik erişimi vermeyi öğrenmek için bu [belgeye](how-to-grant-access-other-resources.md) başvurun. Ayrıca, sistem tarafından atanan yönetilen kimlik kullanıyorsanız, yönetilen kimlik yalnızca uygulama dağıtımından sonra oluşturulur.

## <a name="keyvault-secret-as-application-parameter"></a>Uygulama parametresi olarak Anahtar Kasası gizli anahtarı
Uygulamanın, Anahtar Kasası 'nda depolanan arka uç veritabanı parolasını okuması gerektiğini, Service Fabric KeyVaultReference desteğinin de kolaylaştırdığını varsayalım. Aşağıdaki örnek, Service Fabric KeyVaultReference desteğini kullanarak keykasasından gizli dizi `DBPassword` okur.

- Settings. xml ' ye bir bölüm ekleyin

    Tür `KeyVaultReference` ve değer `<KeyVaultURL>` `DBPassword` parametresi tanımlayın

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- `<ConfigPackagePolicies>` 'daki ApplicationManifest. xml dosyasındaki yeni bölüme başvurun

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="ttkappuser" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- Uygulamanızda KeyVaultReference kullanma

    Hizmet örneklemesi Service Fabric, uygulamanın yönetilen kimliğini kullanarak KeyVaultReference parametresini çözer. `<Section  Name=dbsecrets>` altında listelenen her parametre, EnvironmentVariable SecretPath tarafından işaret edilen klasörün altında bulunan bir dosya olacaktır. Aşağıdaki C# kod parçacığı, uygulamanızda dbpassword 'ın nasıl okunacağını gösterir.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > Kapsayıcı senaryosunda, `secrets` nereye bağlanmayacağını denetlemek için bağlama noktasını kullanabilirsiniz.

## <a name="keyvault-secret-as-environment-variable"></a>Ortam değişkeni olarak Anahtar Kasası gizli anahtarı

Service Fabric ortam değişkenleri artık KeyVaultReference türünü destekliyor. örnek aşağıdaki örnekte, bir ortam değişkeninin Anahtar Kasası 'nda depolanan bir gizli dizi ile nasıl bağlanacağı gösterilmektedir.

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>Kapsayıcı deposu parolası olarak Anahtar Kasası gizli anahtarı
KeyVaultReference, kapsayıcı havuzunun kimlik bilgileri için desteklenen bir türdür, aşağıdaki örnek, bir Anahtar Kasası başvurusunun kapsayıcı deposu parolası olarak nasıl kullanılacağını göstermektedir.
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>SSS
- KeyVaultReference desteği için yönetilen kimliğin etkinleştirilmesi gerekir, anahtar Vaultreference yönetilen kimliği etkinleştirmeden kullanılırsa, uygulama etkinleştirme başarısız olur.

- Sistem tarafından atanan kimlik kullanıyorsanız, bu, yalnızca uygulama dağıtıldıktan sonra oluşturulur ve bu, döngüsel bir bağımlılık oluşturur. Uygulamanız dağıtıldıktan sonra, Anahtar Kasası 'na sisteme atanmış kimlik erişimi izni verebilirsiniz. Sistem tarafından atanan kimliği {Cluster}/{Application Name}/{ServiceName} adıyla bulabilirsiniz

- Keykasasının, Service Fabric kümeniz ile aynı abonelikte olması gerekir. 

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Keykasası belgeleri](https://docs.microsoft.com/azure/key-vault/)
