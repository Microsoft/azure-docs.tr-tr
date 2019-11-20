---
title: Service Fabric gizli dizileri | Microsoft Docs
description: Bu makalede Service Fabric gizli dizi deposunun nasıl kullanılacağı açıklanır.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: 5315a8806f45e40204e8500e97c3440bfa9ab8b2
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077355"
---
#  <a name="service-fabric-secrets-store"></a>Service Fabric gizli dizi deposu
Bu makalede Service Fabric gizli dizileri deposu (CSS) kullanılarak Service Fabric uygulamalarda parolaların nasıl oluşturulduğu ve kullanılacağı açıklanmaktadır. CSS, bir parola, belirteç ve anahtarlar gibi hassas verileri bellekte tutmak için kullanılan bir yerel gizli dizi depolama önbelleğidir.

## <a name="enabling-secrets-store"></a>Gizli dizi depolaması etkinleştiriliyor
 CSS 'yi etkinleştirmek için `fabricSettings` altındaki küme yapılandırmanıza aşağıda ekleyin. CSS için küme sertifikasından farklı bir sertifika kullanmanız önerilir. Şifreleme sertifikasının tüm düğümlerde yüklü olduğundan ve `NetworkService` sertifikanın özel anahtarı için okuma iznine sahip olduğundan emin olun.
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
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
```
## <a name="declare-secret-resource"></a>Gizli kaynağı bildirme
Kaynak Yöneticisi şablonunu kullanarak ya da REST API kullanarak gizli bir kaynak oluşturabilirsiniz.

* Resource Manager şablonunu kullanma
```json
   "resources": [
      {
        "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
          }
        }
      ]
```
Yukarıdaki şablon `supersecret` gizli kaynak oluşturuyor, ancak gizli kaynak için henüz bir değer ayarlanmadı.

* REST API’sini kullanma

Gizli kaynak oluşturmak için, `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`için bir PUT isteği yapın `supersecret`. Gizli dizi oluşturmak için küme sertifikası veya yönetici istemci sertifikasına ihtiyacınız vardır.

```powershell
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint>
```

## <a name="set-secret-value"></a>Gizli değeri ayarla
* Resource Manager şablonunu kullanma

Aşağıdaki Kaynak Yöneticisi şablonu, sürüm `ver1`ile gizli `supersecret` için değer oluşturur ve ayarlar.
```json
  {
  "parameters": {
  "supersecret": {
      "type": "string",
      "metadata": {
        "description": "supersecret value"
      }
   }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
        }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "supersecret/ver1",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/supersecret"
      ],
      "properties": {
        "value": "[parameters('supersecret')]"
      }
    }
  ],
  ```
* REST API’sini kullanma

```powershell
$Params = @{"properties": {"value": "mysecretpassword"}}
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
## <a name="using-the-secret-in-your-application"></a>Uygulamanızda gizli dizi kullanma

1.  Settings. xml dosyasına aşağıdaki içeriğe sahip bir bölüm ekleyin. Buraya değer {`secretname:version`} biçimindedir

```xml
  <Section Name="testsecrets">
   <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
  </Section>
```
2. Şimdi ApplicationManifest. xml dosyasındaki bölümü içeri aktarın
```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ConfigPackagePolicies CodePackageRef="Code">
        <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
        </ConfigPackagePolicies>
    </Policies>
  </ServiceManifestImport>
```

' SecretPath ' ortam değişkeni, tüm parolaların depolandığı dizine işaret edecektir. Bölüm `testsecrets` altında listelenen her bir parametre ayrı bir dosyada depolanır. Uygulama artık aşağıda gösterildiği gibi gizli dizi kullanabilir
```C#
secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
```
3. Bir kapsayıcıya gizli diziler bağlama

Yalnızca, kapsayıcının içinde kullanılabilir gizli dizileri oluşturmak için gereken değişiklik, `<ConfigPackage>`bir bağlama noktası belirtmektir.
Burada değiştirilen ApplicationManifest. xml dosyası  

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ConfigPackagePolicies CodePackageRef="Code">
        <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
        <!-- Linux Container
         <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
        -->
      </ConfigPackagePolicies>
    </Policies>
  </ServiceManifestImport>
```
Gizlilikler, kapsayıcının içindeki bağlama noktası altında kullanılabilir.

4. Bir ortam değişkenine gizli dizi bağlama 

Type = ' SecretsStoreRef ' belirterek, bir işlem ortamı değişkenine gizli dizi bağlayabilirsiniz. ServiceManifest. xml ' de `MySuperSecret` ortam değişkenine `supersecret` sürüm `ver1` bağlama örneği aşağıda verilmiştir.

```xml
<EnvironmentVariables>
  <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
</EnvironmentVariables>
```
## <a name="next-steps"></a>Sonraki adımlar
[Uygulama ve hizmet güvenliği](service-fabric-application-and-service-security.md) hakkında daha fazla bilgi edinin