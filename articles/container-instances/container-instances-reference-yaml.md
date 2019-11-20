---
title: YAML başvurusunu Azure Container Instances
description: Bir kapsayıcı grubunu yapılandırmak için Azure Container Instances tarafından desteklenen YAML dosyası başvurusu
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 08/12/2019
ms.author: danlep
ms.openlocfilehash: 2e6be18371cf3ff96d1ce91d4dde26ff1f14021b
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/22/2019
ms.locfileid: "71179910"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML başvurusu: Azure Container Instances

Bu makalede bir [kapsayıcı grubunu](container-instances-container-groups.md)yapılandırmak için Azure Container Instances tarafından desteklenen YAML dosyasının sözdizimi ve özellikleri ele alınmaktadır. Azure CLı 'de [az Container Create][az-container-create] komutuna grup yapılandırmasını girmek IÇIN BIR YAML dosyası kullanın. 

YAML dosyası, tekrarlanabilir dağıtımlar için bir kapsayıcı grubu yapılandırmanın kolay bir yoludur. Bir kapsayıcı grubu oluşturmak veya güncelleştirmek için bir [Kaynak Yöneticisi şablonu](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) veya Azure Container Instances SDK 'ları kullanmanın kısa bir alternatifidir.

> [!NOTE]
> Bu başvuru, Azure Container Instances REST API sürümüne `2018-10-01`yönelik YAML dosyaları için geçerlidir.

## <a name="schema"></a>Şema 

YAML dosyasının şeması, anahtar özelliklerini vurgulamak için yorumlar da dahil olmak üzere aşağıdaki gibidir. Bu şemadaki özelliklerin bir açıklaması için, bkz. [özellik değerleri](#property-values) bölümü.

```yml
name: string  # Name of the container group
apiVersion: '2018-10-01'
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
      ports: # Exposed ports on the instance
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
```

## <a name="property-values"></a>Özellik değerleri

Aşağıdaki tablolarda, şemada ayarlamanız gereken değerler açıklanır.

<a id="Microsoft.ContainerInstance/containerGroups" />

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Microsoft. Containerınstance/containerGroups nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  name | dize | Evet | Kapsayıcı grubunun adı. |
|  apiVersion | enum | Evet | 2018-10-01 |
|  location | dize | Hayır | Kaynak konumu. |
|  tags | object | Hayır | Kaynak etiketleri. |
|  identity | object | Hayır | Yapılandırılmışsa kapsayıcı grubunun kimliği. - [Containergroupıdentity nesnesi](#ContainerGroupIdentity) |
|  properties | object | Evet | [ContainerGroupProperties nesnesi](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>Containergroupıdentity nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  type | enum | Hayır | Kapsayıcı grubu için kullanılan kimlik türü. ' SystemAssigned, Useratandı ' türü hem örtük olarak oluşturulan bir kimliği hem de Kullanıcı tarafından atanan kimlikleri içerir. ' None ' türü, kapsayıcı grubundaki tüm kimlikleri kaldırır. -SystemAssigned, Useratandı, SystemAssigned, Useratandı, None |
|  Userassignedıdentities | object | Hayır | Kapsayıcı grubuyla ilişkili kullanıcı kimliklerinin listesi. Kullanıcı kimlik sözlüğü anahtar başvuruları Azure Resource Manager kaynak kimlikleri şu biçimde olacaktır: '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName }'. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>ContainerGroupProperties nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  containers | array | Evet | Kapsayıcı grubu içindeki kapsayıcılar. - [Kapsayıcı nesnesi](#Container) |
|  imageRegistryCredentials | array | Hayır | Kapsayıcı grubunun oluşturulduğu görüntü kayıt defteri kimlik bilgileri. - [ImageRegistryCredential nesnesi](#ImageRegistryCredential) |
|  restartPolicy | enum | Hayır | Kapsayıcı grubundaki tüm kapsayıcılar için ilkeyi yeniden başlatın. - `Always`Her zaman yeniden `OnFailure` Başlat- `Never` hata durumunda yeniden başlatma. -Always, OnFailure, hiçbir zaman |
|  Belirlenemiyor | object | Hayır | Kapsayıcı grubunun IP adresi türü. - [IPAddress nesnesi](#IpAddress) |
|  osType | enum | Evet | Kapsayıcı grubundaki kapsayıcılar için gereken işletim sistemi türü. -Windows veya Linux |
|  birim | array | Hayır | Bu kapsayıcı grubundaki kapsayıcılarla bağlankalebilecek birimlerin listesi. - [Birim nesnesi](#Volume) |
|  tanılama | object | Hayır | Bir kapsayıcı grubu için tanılama bilgileri. - [ContainerGroupDiagnostics nesnesi](#ContainerGroupDiagnostics) |
|  networkProfile | object | Hayır | Bir kapsayıcı grubu için ağ profili bilgileri. - [ContainerGroupNetworkProfile nesnesi](#ContainerGroupNetworkProfile) |
|  dnsConfig | object | Hayır | Bir kapsayıcı grubu için DNS yapılandırma bilgileri. - [DnsConfiguration nesnesi](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Kapsayıcı nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  name | dize | Evet | Kapsayıcı örneğinin Kullanıcı tarafından sağlanmış adı. |
|  properties | object | Evet | Kapsayıcı örneğinin özellikleri. - [ContainerProperties nesnesi](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>ImageRegistryCredential nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  server | dize | Evet | "Http" ve "https" gibi bir protokol olmadan Docker görüntü kayıt defteri sunucusu. |
|  kullanıcı adı | dize | Evet | Özel kayıt defteri için Kullanıcı adı. |
|  password | dize | Hayır | Özel kayıt defteri için parola. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>IPAddress nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  ports | array | Evet | Kapsayıcı grubunda kullanıma sunulan bağlantı noktalarının listesi. - [Bağlantı noktası nesnesi](#Port) |
|  type | enum | Evet | IP 'nin genel İnternet 'e veya özel VNET 'e açık olup olmadığını belirtir. -Public veya Private |
|  IP | dize | Hayır | Genel internet 'e sunulan IP. |
|  dnsNameLabel | dize | Hayır | IP için DNS ad etiketi. |


<a id="Volume" />

### <a name="volume-object"></a>Birim nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  name | dize | Evet | Birimin adı. |
|  AzureFile | object | Hayır | Azure dosya birimi. - [AzureFileVolume nesnesi](#AzureFileVolume) |
|  emptyDir | object | Hayır | Boş dizin birimi. |
|  secret | object | Hayır | Gizli birim. |
|  gitRepo | object | Hayır | Git depo birimi. - [GitRepoVolume nesnesi](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  Günlüğe kaydetme Analizi | object | Hayır | Kapsayıcı grubu Günlük Analizi bilgileri. - [LogAnalytics nesnesi](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>ContainerGroupNetworkProfile nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  id | dize | Evet | Bir ağ profili için tanımlayıcı. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>DnsConfiguration nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  Kullanır | array | Evet | Kapsayıcı grubu için DNS sunucuları. -dize |
|  searchDomains | dize | Hayır | Kapsayıcı grubunda ana bilgisayar adı araması için DNS arama etki alanları. |
|  options | dize | Hayır | Kapsayıcı grubu için DNS seçenekleri. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>ContainerProperties nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  image | dize | Evet | Kapsayıcı örneğini oluşturmak için kullanılan görüntünün adı. |
|  command | array | Hayır | Exec form 'da kapsayıcı örneği içinde yürütülecek komutlar. -dize |
|  ports | array | Hayır | Kapsayıcı örneğindeki açığa çıkarılan bağlantı noktaları. - [ContainerPort nesnesi](#ContainerPort) |
|  environmentVariables | array | Hayır | Kapsayıcı örneğinde ayarlanacak ortam değişkenleri. - [EnvironmentVariable nesnesi](#EnvironmentVariable) |
|  resources | object | Evet | Kapsayıcı örneğinin kaynak gereksinimleri. - [ResourceRequirements nesnesi](#ResourceRequirements) |
|  Birimsiz bağlama | array | Hayır | Kapsayıcı örneği için kullanılabilir birim bağlama. - [VolumeMount nesnesi](#VolumeMount) |
|  Livenessaraştırması | object | Hayır | Lizleştirme araştırması. - [Containeraraştırma nesnesi](#ContainerProbe) |
|  Readinessaraştırması | object | Hayır | Hazırlık araştırması. - [Containeraraştırma nesnesi](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Bağlantı noktası nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | Hayır | Bağlantı noktasıyla ilişkili protokol. -TCP veya UDP |
|  port | integer | Evet | Bağlantı noktası numarası. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>AzureFileVolume nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  madı | dize | Evet | Birim olarak takılacak Azure dosya paylaşımının adı. |
|  Özelliğinin | boolean | Hayır | Bir birim olarak bağlanmış Azure dosyasının salt okunurdur olup olmadığını belirten bayrak. |
|  storageAccountName | dize | Evet | Azure dosya paylaşımının bulunduğu depolama hesabının adı. |
|  storageAccountKey | dize | Hayır | Azure dosya paylaşımınıza erişmek için kullanılan depolama hesabı erişim anahtarı. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>GitRepoVolume nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  dizin | dize | Hayır | Hedef dizin adı. '.. ' İle içermemelidir veya başlamamalıdır.  '. ' Sağlanırsa, birim dizini git deposu olur.  Aksi halde, belirtilmişse, belirtilen ada sahip alt dizinde Git deposunu içerir. |
|  repository | dize | Evet | Depo URL 'SI |
|  Uncaya | dize | Hayır | Belirtilen düzeltme için karmayı işleyin. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>LogAnalytics nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  workspaceId | dize | Evet | Log Analytics için çalışma alanı kimliği |
|  workspaceKey | dize | Evet | Log Analytics için çalışma alanı anahtarı |
|  Günlüğe kaydetme türü | enum | Hayır | Kullanılacak günlük türü. -Containerınsights veya Containerınstancelogs |
|  meta veriler | object | Hayır | Log Analytics için meta veriler. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>ContainerPort nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | Hayır | Bağlantı noktasıyla ilişkili protokol. -TCP veya UDP |
|  port | integer | Evet | Kapsayıcı grubu içinde ortaya çıkarılan bağlantı noktası numarası. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>EnvironmentVariable nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  name | dize | Evet | Ortam değişkeninin adı. |
|  value | dize | Hayır | Ortam değişkeninin değeri. |
|  secureValue | dize | Hayır | Güvenli ortam değişkeninin değeri. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>ResourceRequirements nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  istekler | object | Evet | Bu kapsayıcı örneğinin kaynak istekleri. - [ResourceRequests nesnesi](#ResourceRequests) |
|  Değerleri | object | Hayır | Bu kapsayıcı örneğinin kaynak sınırları. - [Resourcelimit nesnesi](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>VolumeMount nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  name | dize | Evet | Birim bağlama adı. |
|  Bağlamayolu | dize | Evet | Birimin bağlanması gereken kapsayıcı içindeki yol. İki nokta (:) içermemelidir. |
|  Özelliğinin | boolean | Hayır | Birim bağlamamı salt okunurdur olduğunu belirten bayrak. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>Containeraraştırma nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  Exec | object | Hayır | Araştırma- [Containerexec nesnesine](#ContainerExec) yürütme komutu |
|  httpGet | object | Hayır | Http get ayarları araştırma- [Containerhttpget nesnesine](#ContainerHttpGet) |
|  ınitialdelayseconds | integer | Hayır | İlk gecikme süresi. |
|  periodSeconds | integer | Hayır | Süre saniyesi. |
|  failureThreshold | integer | Hayır | Hata eşiği. |
|  Başarılı eşik | integer | Hayır | Başarı eşiği. |
|  timeoutSeconds | integer | Hayır | Zaman aşımı süresi. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>ResourceRequests nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | numarası | Evet | Bu kapsayıcı örneğinin GB cinsinden bellek isteği. |
|  cpu | numarası | Evet | Bu kapsayıcı örneğinin CPU isteği. |
|  gpu | object | Hayır | Bu kapsayıcı örneğinin GPU isteği. - [GpuResource nesnesi](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>Resourcelimit nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | numarası | Hayır | Bu kapsayıcı örneğinin GB cinsinden bellek sınırı. |
|  cpu | numarası | Hayır | Bu kapsayıcı örneğinin CPU sınırı. |
|  gpu | object | Hayır | Bu kapsayıcı örneğinin GPU sınırı. - [GpuResource nesnesi](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>ContainerExec nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  command | array | Hayır | Kapsayıcı içinde yürütülecek komutlar. -dize |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>ContainerHttpGet nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  path | dize | Hayır | Araştırmanın yolu. |
|  port | integer | Evet | Araştırmanın bağlantı noktası numarası. |
|  düzen | enum | Hayır | Düzen. -http veya https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>GpuResource nesnesi

|  Name | Type | Gerekli | Value |
|  ---- | ---- | ---- | ---- |
|  count | integer | Evet | GPU kaynağının sayısı. |
|  sku | enum | Evet | GPU kaynağının SKU 'SU. -K80, P100, V100 |


## <a name="next-steps"></a>Sonraki adımlar

[YAML dosyası kullanarak çok kapsayıcılı bir grup dağıtma](container-instances-multi-container-yaml.md)öğreticisine bakın.

Bir [Sanal ağda](container-instances-vnet.md) kapsayıcı grupları dağıtmak veya [harici bir birimi bağlamak](container-instances-volume-azure-files.md)için bir YAML dosyası kullanma örneklerine bakın.

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

