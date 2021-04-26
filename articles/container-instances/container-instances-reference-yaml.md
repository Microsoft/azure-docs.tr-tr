---
title: Kapsayıcı grubu için YAML başvurusu
description: Bir kapsayıcı grubunu yapılandırmak için Azure Container Instances tarafından desteklenen YAML dosyası başvurusu
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: efbea7b64ccdf685d4c82bd406f2aa09227e53e1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771146"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML başvurusu: Azure Container Instances

Bu makalede bir [kapsayıcı grubunu](container-instances-container-groups.md)yapılandırmak için Azure Container Instances tarafından desteklenen YAML dosyasının sözdizimi ve özellikleri ele alınmaktadır. Azure CLı 'de [az Container Create][az-container-create] komutuna grup yapılandırmasını girmek IÇIN BIR YAML dosyası kullanın. 

YAML dosyası, tekrarlanabilir dağıtımlar için bir kapsayıcı grubu yapılandırmanın kolay bir yoludur. Bir kapsayıcı grubu oluşturmak veya güncelleştirmek için bir [Kaynak Yöneticisi şablonu](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups) veya Azure Container Instances SDK 'ları kullanmanın kısa bir alternatifidir.

> [!NOTE]
> Bu başvuru, Azure Container Instances REST API sürümüne yönelik YAML dosyaları için geçerlidir `2019-12-01` .

## <a name="schema"></a>Şema 

YAML dosyasının şeması, anahtar özelliklerini vurgulamak için yorumlar da dahil olmak üzere aşağıdaki gibidir. Bu şemadaki özelliklerin bir açıklaması için, bkz. [özellik değerleri](#property-values) bölümü.

```yml
name: string  # Name of the container group
apiVersion: '2019-12-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # External-facing ports exposed on the instance, must also be set in group ipAddress property 
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
  sku: string # SKU for the container group
  encryptionProperties:
    vaultBaseUrl: string
    keyName: string
    keyVersion: string
  initContainers: # Array of init containers in the group
  - name: string
    properties:
      image: string
      command:
      - string
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      volumeMounts:
      - name: string
        mountPath: string
        readOnly: boolean
```

## <a name="property-values"></a>Özellik değerleri

Aşağıdaki tablolarda, şemada ayarlamanız gereken değerler açıklanır.



### <a name="microsoftcontainerinstancecontainergroups-object"></a>Microsoft. Containerınstance/containerGroups nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  name | string | Yes | Kapsayıcı grubunun adı. |
|  apiVersion | enum | Yes | 2018-10-01 |
|  location | dize | No | Kaynak konumu. |
|  etiketler | object | No | Kaynak etiketleri. |
|  identity | object | No | Yapılandırılmışsa kapsayıcı grubunun kimliği. - [Containergroupıdentity nesnesi](#containergroupidentity-object) |
|  properties | object | Yes | [ContainerGroupProperties nesnesi](#containergroupproperties-object) |




### <a name="containergroupidentity-object"></a>Containergroupıdentity nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  tür | enum | No | Kapsayıcı grubu için kullanılan kimlik türü. ' SystemAssigned, Useratandı ' türü hem örtük olarak oluşturulan bir kimliği hem de Kullanıcı tarafından atanan kimlikleri içerir. ' None ' türü, kapsayıcı grubundaki tüm kimlikleri kaldırır. -SystemAssigned, Useratandı, SystemAssigned, Useratandı, None |
|  Userassignedıdentities | object | No | Kapsayıcı grubuyla ilişkili kullanıcı kimliklerinin listesi. Kullanıcı kimlik sözlüğü anahtar başvuruları Azure Resource Manager kaynak kimlikleri şu biçimde olacaktır: '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName} '. |




### <a name="containergroupproperties-object"></a>ContainerGroupProperties nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  containers | array | Yes | Kapsayıcı grubu içindeki kapsayıcılar. - [Kapsayıcı nesnesi](#container-object) |
|  imageRegistryCredentials | array | No | Kapsayıcı grubunun oluşturulduğu görüntü kayıt defteri kimlik bilgileri. - [ImageRegistryCredential nesnesi](#imageregistrycredential-object) |
|  restartPolicy | enum | No | Kapsayıcı grubundaki tüm kapsayıcılar için ilkeyi yeniden başlatın. - `Always` Her zaman yeniden Başlat-hata durumunda yeniden başlatma `OnFailure` `Never` . -Always, OnFailure, hiçbir zaman |
|  Belirlenemiyor | object | No | Kapsayıcı grubunun IP adresi türü. - [IPAddress nesnesi](#ipaddress-object) |
|  osType | enum | Yes | Kapsayıcı grubundaki kapsayıcılar için gereken işletim sistemi türü. -Windows veya Linux |
|  volumes | array | No | Bu kapsayıcı grubundaki kapsayıcılarla bağlankalebilecek birimlerin listesi. - [Birim nesnesi](#volume-object) |
|  tanılama | object | No | Bir kapsayıcı grubu için tanılama bilgileri. - [ContainerGroupDiagnostics nesnesi](#containergroupdiagnostics-object) |
|  networkProfile | object | No | Bir kapsayıcı grubu için ağ profili bilgileri. - [ContainerGroupNetworkProfile nesnesi](#containergroupnetworkprofile-object) |
|  dnsConfig | object | No | Bir kapsayıcı grubu için DNS yapılandırma bilgileri. - [DnsConfiguration nesnesi](#dnsconfiguration-object) |
| isteyin | enum | No | Kapsayıcı grubu için SKU-standart veya adanmış |
| encryptionProperties | object | No | Bir kapsayıcı grubu için şifreleme özellikleri. - [EncryptionProperties nesnesi](#encryptionproperties-object) | 
| ınitcontainers | array | No | Bir kapsayıcı grubu için init kapsayıcıları. - [InitContainerDefinition nesnesi](#initcontainerdefinition-object) |




### <a name="container-object"></a>Kapsayıcı nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  name | string | Yes | Kapsayıcı örneğinin Kullanıcı tarafından sağlanmış adı. |
|  properties | object | Yes | Kapsayıcı örneğinin özellikleri. - [ContainerProperties nesnesi](#containerproperties-object) |




### <a name="imageregistrycredential-object"></a>ImageRegistryCredential nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  sunucu | string | Yes | "Http" ve "https" gibi bir protokol olmadan Docker görüntü kayıt defteri sunucusu. |
|  username | string | Yes | Özel kayıt defteri için Kullanıcı adı. |
|  password | dize | No | Özel kayıt defteri için parola. |




### <a name="ipaddress-object"></a>IPAddress nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  ports | array | Yes | Kapsayıcı grubunda kullanıma sunulan bağlantı noktalarının listesi. - [Bağlantı noktası nesnesi](#port-object) |
|  tür | enum | Yes | IP 'nin genel İnternet 'e veya özel VNET 'e açık olup olmadığını belirtir. -Public veya Private |
|  IP | dize | No | Genel internet 'e sunulan IP. |
|  dnsNameLabel | dize | No | IP için DNS ad etiketi. |




### <a name="volume-object"></a>Birim nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  name | string | Yes | Birimin adı. |
|  azureFile | object | No | Azure dosya birimi. - [AzureFileVolume nesnesi](#azurefilevolume-object) |
|  emptyDir | object | No | Boş dizin birimi. |
|  gizli dizi | object | No | Gizli birim. |
|  gitRepo | object | No | Git depo birimi. - [GitRepoVolume nesnesi](#gitrepovolume-object) |




### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  Günlüğe kaydetme Analizi | object | No | Kapsayıcı grubu Günlük Analizi bilgileri. - [LogAnalytics nesnesi](#loganalytics-object) |




### <a name="containergroupnetworkprofile-object"></a>ContainerGroupNetworkProfile nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  kimlik | string | Yes | Bir ağ profili için tanımlayıcı. |




### <a name="dnsconfiguration-object"></a>DnsConfiguration nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  Kullanır | array | Yes | Kapsayıcı grubu için DNS sunucuları. -dize |
|  searchDomains | dize | No | Kapsayıcı grubunda ana bilgisayar adı araması için DNS arama etki alanları. |
|  seçenekler | dize | No | Kapsayıcı grubu için DNS seçenekleri. |


### <a name="encryptionproperties-object"></a>EncryptionProperties nesnesi

| Ad  | Tür  | Gerekli  | Değer |
|  ---- | ---- | ---- | ---- |
| vaultBaseUrl 'Si  | string    | Yes   | Keykasası temel URL 'si. |
| Işareti   | string    | Yes   | Şifreleme anahtarı adı. |
| keyVersion    | string    | Yes   | Şifreleme anahtarı sürümü. |

### <a name="initcontainerdefinition-object"></a>InitContainerDefinition nesnesi

| Ad  | Tür  | Gerekli  | Değer |
|  ---- | ---- | ---- | ---- |
| name  | string |  Yes | İnit kapsayıcısının adı. |
| properties    | object    | Yes   | İnit kapsayıcısının özellikleri. - [InitContainerPropertiesDefinition nesnesi](#initcontainerpropertiesdefinition-object)


### <a name="containerproperties-object"></a>ContainerProperties nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  image | string | Yes | Kapsayıcı örneğini oluşturmak için kullanılan görüntünün adı. |
|  command | array | No | Exec form 'da kapsayıcı örneği içinde yürütülecek komutlar. -dize |
|  ports | array | No | Kapsayıcı örneğindeki açığa çıkarılan bağlantı noktaları. - [ContainerPort nesnesi](#containerport-object) |
|  environmentVariables | array | No | Kapsayıcı örneğinde ayarlanacak ortam değişkenleri. - [EnvironmentVariable nesnesi](#environmentvariable-object) |
|  kaynaklar | object | Yes | Kapsayıcı örneğinin kaynak gereksinimleri. - [ResourceRequirements nesnesi](#resourcerequirements-object) |
|  Birimsiz bağlama | array | No | Kapsayıcı örneği için kullanılabilir birim bağlama. - [VolumeMount nesnesi](#volumemount-object) |
|  Livenessaraştırması | object | No | Lizleştirme araştırması. - [Containeraraştırma nesnesi](#containerprobe-object) |
|  Readinessaraştırması | object | No | Hazırlık araştırması. - [Containeraraştırma nesnesi](#containerprobe-object) |




### <a name="port-object"></a>Bağlantı noktası nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  protokol | enum | No | Bağlantı noktasıyla ilişkili protokol. -TCP veya UDP |
|  port | tamsayı | Yes | Bağlantı noktası numarası. |




### <a name="azurefilevolume-object"></a>AzureFileVolume nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  madı | string | Yes | Birim olarak takılacak Azure dosya paylaşımının adı. |
|  Özelliğinin | boolean | No | Bir birim olarak bağlanmış Azure dosyasının salt okunurdur olup olmadığını belirten bayrak. |
|  storageAccountName | string | Yes | Azure dosya paylaşımının bulunduğu depolama hesabının adı. |
|  storageAccountKey | dize | No | Azure dosya paylaşımınıza erişmek için kullanılan depolama hesabı erişim anahtarı. |




### <a name="gitrepovolume-object"></a>GitRepoVolume nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  dizin | dize | No | Hedef dizin adı. '.. ' İle içermemelidir veya başlamamalıdır.  '. ' Sağlanırsa, birim dizini git deposu olur.  Aksi halde, belirtilmişse, belirtilen ada sahip alt dizinde Git deposunu içerir. |
|  depo | string | Yes | Depo URL 'SI |
|  revision | dize | No | Belirtilen düzeltme için karmayı işleyin. |



### <a name="loganalytics-object"></a>LogAnalytics nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  workspaceId | string | Yes | Log Analytics için çalışma alanı kimliği |
|  workspaceKey | string | Yes | Log Analytics için çalışma alanı anahtarı |
|  Günlüğe kaydetme türü | enum | No | Kullanılacak günlük türü. -Containerınsights veya Containerınstancelogs |
|  meta veriler | object | No | Log Analytics için meta veriler. |


### <a name="initcontainerpropertiesdefinition-object"></a>InitContainerPropertiesDefinition nesnesi

| Ad  | Tür  | Gerekli  | Değer |
|  ---- | ---- | ---- | ---- |
| image | dize    | No    | İnit kapsayıcısının görüntüsü. |
| command   | array | No    | Exec formundaki init kapsayıcısı içinde yürütülecek komut. -dize |
| environmentVariables | array  | No |İnit kapsayıcısında ayarlanacak ortam değişkenleri. - [EnvironmentVariable nesnesi](#environmentvariable-object)
| Birimsiz bağlama |array   | No    | Birim bağlama, init kapsayıcısına kullanılabilir. - [VolumeMount nesnesi](#volumemount-object)

### <a name="containerport-object"></a>ContainerPort nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  protokol | enum | No | Bağlantı noktasıyla ilişkili protokol. -TCP veya UDP |
|  port | tamsayı | Yes | Kapsayıcı grubu içinde ortaya çıkarılan bağlantı noktası numarası. |




### <a name="environmentvariable-object"></a>EnvironmentVariable nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  name | string | Yes | Ortam değişkeninin adı. |
|  değer | dize | No | Ortam değişkeninin değeri. |
|  secureValue | dize | No | Güvenli ortam değişkeninin değeri. |




### <a name="resourcerequirements-object"></a>ResourceRequirements nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  istekleri | object | Yes | Bu kapsayıcı örneğinin kaynak istekleri. - [ResourceRequests nesnesi](#resourcerequests-object) |
|  değerleri | object | No | Bu kapsayıcı örneğinin kaynak sınırları. - [Resourcelimit nesnesi](#resourcelimits-object) |




### <a name="volumemount-object"></a>VolumeMount nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  name | string | Yes | Birim bağlama adı. |
|  Bağlamayolu | string | Yes | Birimin bağlanması gereken kapsayıcı içindeki yol. İki nokta (:) içermemelidir. |
|  Özelliğinin | boolean | No | Birim bağlamamı salt okunurdur olduğunu belirten bayrak. |




### <a name="containerprobe-object"></a>Containeraraştırma nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  Exec | object | No | Araştırma- [Containerexec nesnesine](#containerexec-object) yürütme komutu |
|  httpGet | object | No | Http get ayarları araştırma- [Containerhttpget nesnesine](#containerhttpget-object) |
|  ınitialdelayseconds | tamsayı | No | İlk gecikme süresi. |
|  periodSeconds | tamsayı | No | Süre saniyesi. |
|  failureThreshold | tamsayı | No | Hata eşiği. |
|  Başarılı eşik | tamsayı | No | Başarı eşiği. |
|  timeoutSeconds | tamsayı | No | Zaman aşımı süresi. |




### <a name="resourcerequests-object"></a>ResourceRequests nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | sayı | Yes | Bu kapsayıcı örneğinin GB cinsinden bellek isteği. |
|  'suna | sayı | Yes | Bu kapsayıcı örneğinin CPU isteği. |
|  GPU | object | No | Bu kapsayıcı örneğinin GPU isteği. - [GpuResource nesnesi](#gpuresource-object) |




### <a name="resourcelimits-object"></a>Resourcelimit nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | sayı | No | Bu kapsayıcı örneğinin GB cinsinden bellek sınırı. |
|  'suna | sayı | No | Bu kapsayıcı örneğinin CPU sınırı. |
|  GPU | object | No | Bu kapsayıcı örneğinin GPU sınırı. - [GpuResource nesnesi](#gpuresource-object) |




### <a name="containerexec-object"></a>ContainerExec nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  command | array | No | Kapsayıcı içinde yürütülecek komutlar. -dize |




### <a name="containerhttpget-object"></a>ContainerHttpGet nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  path | dize | No | Araştırmanın yolu. |
|  port | tamsayı | Yes | Araştırmanın bağlantı noktası numarası. |
|  düzen | enum | No | Düzen. -http veya https |




### <a name="gpuresource-object"></a>GpuResource nesnesi

|  Ad | Tür | Gerekli | Değer |
|  ---- | ---- | ---- | ---- |
|  count | tamsayı | Yes | GPU kaynağının sayısı. |
|  isteyin | enum | Yes | GPU kaynağının SKU 'SU. -K80, P100, V100 |


## <a name="next-steps"></a>Sonraki adımlar

[YAML dosyası kullanarak çok kapsayıcılı bir grup dağıtma](container-instances-multi-container-yaml.md)öğreticisine bakın.

Bir [Sanal ağda](container-instances-vnet.md) kapsayıcı grupları dağıtmak veya [harici bir birimi bağlamak](container-instances-volume-azure-files.md)için bir YAML dosyası kullanma örneklerine bakın.

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
