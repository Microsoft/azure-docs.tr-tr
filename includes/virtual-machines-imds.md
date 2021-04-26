---
author: LittleBoxOfSunshine
manager: KumariSupriya
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: include
ms.date: 01/04/2021
ms.author: chhenk
ms.reviewer: azmetadatadev
ms.custom: references_regions
ms.openlocfilehash: 98866a4f06df0380d52d1aee3eede8aa2f70aaed
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588147"
---
Azure Instance Metadata Service (IMDS), çalışmakta olan sanal makine örnekleri hakkında bilgi sağlar. Bunu, sanal makinelerinizi yönetmek ve yapılandırmak için kullanabilirsiniz.
Bu bilgiler SKU, depolama, ağ yapılandırması ve yaklaşan bakım olaylarını içerir. Mevcut verilerin tüm listesi için bkz. [uç nokta kategorileri Özeti](#endpoint-categories).

IDS, sanal makine (VM) ve sanal makine ölçek kümesi örneklerinin örneklerini çalıştırmak için kullanılabilir. Tüm uç noktalar, [Azure Resource Manager](/rest/api/resources/)kullanılarak oluşturulan ve yönetilen VM 'leri destekler. Örnek kategorisinin yalnızca Atsınanan kategorisi ve ağ bölümü, klasik dağıtım modeli kullanılarak oluşturulan VM 'Leri destekler. Atsınanan uç nokta, bunu yalnızca sınırlı bir ölçüde yapar.

IDS, iyi bilinen, yönlendirilemeyen bir IP adresinde () bulunan bir REST API `169.254.169.254` . Yalnızca VM 'nin içinden erişebilirsiniz. VM ve ıDS arasındaki iletişim, Konağı hiçbir şekilde bırakmıyor.
ISE 'leri sorgularken, HTTP istemcilerinizi VM içindeki Web ara sunucusunu atlayıp `169.254.169.254` aynı şekilde değerlendirin [`168.63.129.16`](../articles/virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="usage"></a>Kullanım

### <a name="access-azure-instance-metadata-service"></a>Azure Instance Metadata Service erişme

IMDS 'ye erişmek için [Azure Resource Manager](/rest/api/resources/) veya [Azure Portal](https://portal.azure.com)bir VM oluşturun ve aşağıdaki örnekleri kullanın.
Daha fazla örnek için bkz. [Azure örnek meta verileri örnekleri](https://github.com/microsoft/azureimds).

Bir örnek için tüm meta verileri almak için örnek kod aşağıda verilmiştir. Belirli bir veri kaynağına erişmek için, kullanılabilir tüm özelliklere genel bir bakış için bkz. [uç nokta kategorileri](#endpoint-categories) .

**İstek**

> [!IMPORTANT]
> Bu örnek proxy 'leri atlar. IDS 'yi sorgularken proxy 'leri **atmalısınız** . Ek bilgi için bkz. [proxy 'ler](#proxies) .

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | jq
```

---

**Response**

> [!NOTE]
> Yanıt bir JSON dizesidir. Aşağıdaki örnek yanıt, okunabilirlik için oldukça yazdırılır.

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

## <a name="security-and-authentication"></a>Güvenlik ve kimlik doğrulaması

Instance Metadata Service, yalnızca yönlendirilemeyen bir IP adresi üzerinde çalışan bir sanal makine örneği içinden erişilebilir. VM 'Ler kendileriyle ilgili meta veriler/işlevlerle etkileşime sahip olacak şekilde sınırlandırılmıştır. API yalnızca HTTP 'dir ve ana bilgisayardan ayrılmayın.

İsteklerin doğrudan IMDS için amaçlandığından ve isteklerin istenmeden veya istenmeyen yeniden yönlendirilmesine engel olmak için istekleri:
- Üst **bilgisi içermelidir**`Metadata: true`
-  `X-Forwarded-For` Üst bilgi içermemelidir

Bu gereksinimlerin **her ikisini de** karşılamayan her türlü istek hizmet tarafından reddedilir.

> [!IMPORTANT]
> IDS gizli veriler için bir kanal **değildir** . API 'nin kimliği doğrulanmamış ve sanal makine üzerindeki tüm işlemlere açıktır. Bu hizmet aracılığıyla sunulan bilgiler, sanal makine içinde çalışan tüm uygulamalara paylaşılan bilgiler olarak düşünülmelidir.

## <a name="proxies"></a>Kullanıldığı

IDS 'lerin bir proxy arkasında kullanılması **amaçlanmamaktadır** ve bunun yapılması desteklenmez. Çoğu HTTP istemcisi isteklerinizin proxy 'lerini devre dışı bırakabilmeniz için bir seçenek sağlar ve bu işlevsellik, ıMDS ile iletişim kurulurken kullanılmalıdır. Ayrıntılar için istemcinizin belgelerine bakın.

> [!IMPORTANT]
> Ortamınızdaki herhangi bir ara sunucu yapılandırmasından haberdar olmasanız bile, **varsayılan istemci ara sunucu ayarlarını geçersiz kılmanız gerekir**. Proxy yapılandırmaları otomatik olarak bulunabilir ve bu tür yapılandırmaları atlamaması, makinenin yapılandırmasının gelecekte değiştirilmesi durumunda riskleri kesintiye karşı kesintiye neden olur.

## <a name="rate-limiting"></a>Hız sınırlaması

Genel olarak, ıDS istekleri saniyede 5 istek ile sınırlıdır. Bu eşiği aşan istekler 429 yanıt olarak reddedilir. [Yönetilen kimlik](#managed-identity) kategorisine yönelik istekler saniyede 20 istek ve 5 eşzamanlı istek ile sınırlıdır.

## <a name="http-verbs"></a>HTTP fiilleri

Aşağıdaki HTTP fiilleri Şu anda desteklenmektedir:

| Fiil | Description |
|------|-------------|
| `GET` | İstenen kaynağı alma

## <a name="parameters"></a>Parametreler

Uç noktalar, gerekli ve/veya isteğe bağlı parametreleri destekleyebilir. Ayrıntılar için bkz. ilgili son uç nokta için [şema](#schema) ve belgeler.

### <a name="query-parameters"></a>Sorgu parametreleri

IDS uç noktaları HTTP sorgu dizesi parametrelerini destekler. Örnek: 

```
http://169.254.169.254/metadata/instance/compute?api-version=2019-06-04&format=json
```

Parametreleri belirtir:

| Name | Değer |
|------|-------|
| `api-version` | `2019-06-04`
| `format` | `json`

Yinelenen sorgu parametresi adları olan istekler reddedilir.

### <a name="route-parameters"></a>Rota parametreleri

Daha büyük JSON blob 'ları döndüren bazı uç noktalar için, yanıtın bir alt kümesine filtre uygulamak üzere istek uç noktasına yol parametreleri eklemeyi destekliyoruz: 

```
http://169.254.169.254/metadata/<endpoint>/[<filter parameter>/...]?<query parameters>
```
Parametreler, ayrıştırılmış bir gösterimle etkileşim kurarken JSON nesnesinin ilerlemek için kullanılacak dizinlere/anahtarlara karşılık gelir.

Örneğin, `/metatadata/instance` JSON nesnesini döndürür:
```json
{
    "compute": { ... },
    "network": {
        "interface": [
            {
                "ipv4": {
                   "ipAddress": [{
                        "privateIpAddress": "10.144.133.132",
                        "publicIpAddress": ""
                    }],
                    "subnet": [{
                        "address": "10.144.133.128",
                        "prefix": "26"
                    }]
                },
                "ipv6": {
                    "ipAddress": [
                     ]
                },
                "macAddress": "0011AAFFBB22"
            },
            ...
        ]
    }
}
```

Yanıtı yalnızca işlem özelliğine göre filtrelemek istiyoruz, isteği şu şekilde göndereceğiz: 
```
http://169.254.169.254/metadata/instance/compute?api-version=<version>
```

Benzer şekilde, iç içe bir özelliğe veya belirli bir dizi öğesine filtre uygulamak istiyoruz anahtarlar eklemek istiyoruz: 
```
http://169.254.169.254/metadata/instance/network/interface/0?api-version=<version>
```
özellikten ilk öğeye filtre uygulayabilir `Network.interface` ve şunu döndürür:

```json
{
    "ipv4": {
       "ipAddress": [{
            "privateIpAddress": "10.144.133.132",
            "publicIpAddress": ""
        }],
        "subnet": [{
            "address": "10.144.133.128",
            "prefix": "26"
        }]
    },
    "ipv6": {
        "ipAddress": [
         ]
    },
    "macAddress": "0011AAFFBB22"
}
```

> [!NOTE]
> Bir yaprak düğümüne filtre uygulanırken `format=json` çalışmaz. `format=text`Varsayılan biçim JSON olduğundan bu sorguların açıkça belirtilmesi gerekir.

## <a name="schema"></a>Şema

### <a name="data-format"></a>Veri biçimi

Varsayılan olarak, ıDS verileri JSON biçiminde () döndürür `Content-Type: application/json` . Ancak, yanıt filtrelemeyi (bkz. [route parametreleri](#route-parameters)) destekleyen uç noktalar de biçimini destekler `text` .

Varsayılan olmayan bir yanıt biçimine erişmek için istenen biçimi istekte bir sorgu dizesi parametresi olarak belirtin. Örnek:

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

---

JSON yanıtlarında tüm temel öğeler türündedir `string` ve eksik ya da geçersiz değerler her zaman dahil edilir, ancak boş bir dizeye ayarlanır.

### <a name="versioning"></a>Sürüm Oluşturma

IDS 'nin sürümü oluşturulmuş ve HTTP isteğindeki API sürümünün belirtilmesi zorunludur. Bu gereksinimin tek istisnası, kullanılabilir API sürümlerini dinamik olarak almak için kullanılabilecek [sürümler](#versions) uç noktasıdır.

Daha yeni sürümler eklendikçe, betiklerinizin belirli veri biçimlerinde bağımlılıkları varsa, daha eski sürümlere uyumluluk için yine de erişilebilir.

Bir sürüm belirtmezseniz, en yeni Desteklenen sürümlerin listesini içeren bir hata alırsınız:
```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

#### <a name="supported-api-versions"></a>Desteklenen API sürümleri
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01
- 2020-12-01
- 2021-01-01

### <a name="swagger"></a>Swagger

IDS için tam Swagger tanımı şurada bulunabilir: https://github.com/Azure/azure-rest-api-specs/blob/master/specification/imds/data-plane/readme.md

## <a name="regional-availability"></a>Bölgesel kullanılabilirlik

Hizmet, tüm Azure bulutlarında **genel kullanıma sunulmuştur** .

## <a name="root-endpoint"></a>Kök uç noktası

Kök uç noktası `http://169.254.169.254/metadata` .

## <a name="endpoint-categories"></a>Uç nokta kategorileri

IMDS API 'SI, her biri bir veya daha fazla uç nokta içeren farklı veri kaynaklarını temsil eden birden çok uç nokta kategorisi içerir. Ayrıntılar için bkz. her kategori.

| Kategori kökü | Description | Sunulan sürüm |
|---------------|-------------|--------------------|
| `/metadata/attested` | Bkz. [Atsınanan veriler](#attested-data) | 2018-10-01
| `/metadata/identity` | Bkz. [yönetilen kimliği IMDS aracılığıyla](#managed-identity) | 2018-02-01
| `/metadata/instance` | Bkz. [örnek meta verileri](#instance-metadata) | 2017-04-02
| `/metadata/loadbalancer` | Bkz. [ıDS aracılığıyla Load Balancer meta verileri alma](#load-balancer-metadata) | 2020-10-01
| `/metadata/scheduledevents` | Bkz. [ıDS aracılığıyla zamanlanan olaylar](#scheduled-events) | 2017-08-01
| `/metadata/versions` | Bkz. [sürümler](#versions) | Yok

## <a name="versions"></a>Sürümler

> [!NOTE]
> Bu özellik, şu anda kullanıma alınmış olan ve her bölgede henüz kullanılamayan sürüm 2020-10-01 ' den sonra yayımlanmıştır.

### <a name="list-api-versions"></a>API sürümlerini Listele

Desteklenen API sürümleri kümesini döndürür.

```
GET /metadata/versions
```

#### <a name="parameters"></a>Parametreler

Hiçbiri (bu uç noktanın sürümü kaldırılıyor).

#### <a name="response"></a>Yanıt

```json
{
  "apiVersions": [
    "2017-03-01",
    "2017-04-02",
    ...
  ]
}
```

## <a name="instance-metadata"></a>Örnek meta verileri

### <a name="get-vm-metadata"></a>VM meta verilerini al

İşlem, ağ ve depolama dahil olmak üzere VM örneği için önemli meta verileri gösterir. 

```
GET /metadata/instance
```

#### <a name="parameters"></a>Parametreler

| Name | Gerekli/İsteğe Bağlı | Açıklama |
|------|-------------------|-------------|
| `api-version` | Gerekli | İsteğe hizmet vermek için kullanılan sürüm.
| `format` | Seçim | Yanıtın biçimi ( `json` veya `text` ). * Note: istek parametreleri kullanılırken gerekebilir

Bu uç nokta, [yol parametreleri](#route-parameters)aracılığıyla yanıt filtrelemeyi destekler.

#### <a name="response"></a>Yanıt

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

Şema dökümü:

**İşlem**

| Veriler | Description | Sunulan sürüm |
|------|-------------|--------------------|
| `azEnvironment` | VM 'nin çalıştığı Azure ortamı | 2018-10-01
| `customData` | Bu özellik kullanım dışıdır ve [ıMD 'de](#frequently-asked-questions)devre dışıdır. Yerine geçilen `userData` | 2019-02-01
| `evictionPolicy` | Bir [spot VM 'nin](../articles/virtual-machines/spot-vms.md) nasıl çıkarılacak olduğunu ayarlar. | 2020-12-01
| `isHostCompatibilityLayerVm` | VM 'nin konak uyumluluk katmanında çalışıp çalışmadığına göre tanımlar | 2020-06-01
| `licenseType` | [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit)için lisans türü. Bu yalnızca AHB özellikli VM 'Lerde mevcuttur | 2020-09-01
| `location` | VM 'nin çalıştığı Azure bölgesi | 2017-04-02
| `name` | VM adı | 2017-04-02
| `offer` | VM görüntüsü için teklif bilgileri ve yalnızca Azure görüntü Galerisi 'nden dağıtılan görüntülerde bulunur | 2017-04-02
| `osProfile.adminUsername` | Yönetici hesabının adını belirtir | 2020-07-15
| `osProfile.computerName` | Bilgisayarın adını belirtir | 2020-07-15
| `osProfile.disablePasswordAuthentication` | Parola kimlik doğrulamasının devre dışı bırakılıp bırakılmadığını belirtir. Bu yalnızca Linux sanal makinelerinde bulunur | 2020-10-01
| `osType` | Linux veya Windows | 2017-04-02
| `placementGroupId` | Sanal makine ölçek kümesinin [yerleştirme grubu](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) | 2017-08-01
| `plan` | Bir Azure Market görüntüsü ise VM için ad, ürün ve yayımcı içeren [plan planlayın](/rest/api/compute/virtualmachines/createorupdate#plan) | 2018-04-02
| `platformUpdateDomain` |  VM 'nin çalıştığı [etki alanını güncelleştirme](../articles/virtual-machines/availability.md) | 2017-04-02
| `platformFaultDomain` | VM 'nin çalıştığı [hata etki alanı](../articles/virtual-machines/availability.md) | 2017-04-02
| `priority` | VM önceliği. Daha fazla bilgi için [spot VM](../articles/virtual-machines/spot-vms.md) 'lere başvurun | 2020-12-01
| `provider` | VM sağlayıcısı | 2018-10-01
| `publicKeys` | VM ve yollara atanan [ortak anahtarların koleksiyonu](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) | 2018-04-02
| `publisher` | VM görüntüsünün yayımcısı | 2017-04-02
| `resourceGroupName` | Sanal makineniz için [kaynak grubu](../articles/azure-resource-manager/management/overview.md) | 2017-08-01
| `resourceId` | Kaynağın [tam](/rest/api/resources/resources/getbyid) kimliği | 2019-03-11
| `sku` | VM görüntüsü için belirli SKU | 2017-04-02
| `securityProfile.secureBootEnabled` | SANAL makinede UEFı güvenli önyükleme özelliğinin etkinleştirilip etkinleştirilmediğini belirler | 2020-06-01
| `securityProfile.virtualTpmEnabled` | Sanal Güvenilir Platform Modülü (TPM) VM 'de etkin olup olmadığını belirler | 2020-06-01
| `storageProfile` | Aşağıdaki depolama profilini inceleyin | 2019-06-01
| `subscriptionId` | Sanal makine için Azure aboneliği | 2017-08-01
| `tags` | Sanal makineniz için [Etiketler](../articles/azure-resource-manager/management/tag-resources.md)  | 2017-08-01
| `tagsList` | Daha kolay programlı ayrıştırma için JSON dizisi olarak biçimlendirilen Etiketler  | 2019-06-04
| `userData` | VM, sağlama sırasında veya sonrasında (Base64 kodlamalı) kullanım için oluşturulduğunda belirtilen veri kümesi  | 2021-01-01
| `version` | VM görüntüsünün sürümü | 2017-04-02
| `vmId` | VM için [benzersiz tanımlayıcı](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2017-04-02
| `vmScaleSetName` | Sanal makine ölçek kümesinin [sanal makine ölçek kümesi adı](../articles/virtual-machine-scale-sets/overview.md) | 2017-12-01
| `vmSize` | [VM boyutu](../articles/virtual-machines/sizes.md) | 2017-04-02
| `zone` | Sanal makinenizin [kullanılabilirlik bölgesi](../articles/availability-zones/az-overview.md) | 2017-12-01

**Depolama profili**

Bir sanal makinenin depolama profili üç kategoriye ayrılmıştır: görüntü başvurusu, işletim sistemi diski ve veri diskleri.

Görüntü başvurusu nesnesi, işletim sistemi görüntüsüyle ilgili aşağıdaki bilgileri içerir:

| Veriler | Description |
|------|-------------|
| `id` | Kaynak kimliği
| `offer` | Platform veya Market görüntüsü teklifi
| `publisher` | Görüntü yayımcısı
| `sku` | Görüntü SKU 'su
| `version` | Platform veya Market görüntüsünün sürümü

İşletim sistemi diski nesnesi, VM tarafından kullanılan işletim sistemi diski hakkında aşağıdaki bilgileri içerir:

| Veriler | Description |
|------|-------------|
| `caching` | Önbelleğe alma gereksinimleri
| `createOption` | VM 'nin nasıl oluşturulduğu hakkında bilgi
| `diffDiskSettings` | Kısa ömürlü disk ayarları
| `diskSizeGB` | Diskin GB cinsinden boyutu
| `image`   | Kaynak Kullanıcı görüntüsü sanal sabit diski
| `lun`     | Diskin mantıksal birim numarası
| `managedDisk` | Yönetilen disk parametreleri
| `name`    | Disk adı
| `vhd`     | Sanal sabit disk
| `writeAcceleratorEnabled` | Diskte writeAccelerator etkin olup olmadığı

Veri diskleri dizisi, VM 'ye bağlı veri disklerinin bir listesini içerir. Her veri diski nesnesi şu bilgileri içerir:

Veriler | Description |
-----|-------------|
| `caching` | Önbelleğe alma gereksinimleri
| `createOption` | VM 'nin nasıl oluşturulduğu hakkında bilgi
| `diffDiskSettings` | Kısa ömürlü disk ayarları
| `diskSizeGB` | Diskin GB cinsinden boyutu
| `encryptionSettings` | Disk için şifreleme ayarları
| `image` | Kaynak Kullanıcı görüntüsü sanal sabit diski
| `managedDisk` | Yönetilen disk parametreleri
| `name` | Disk adı
| `osType` | Diske dahil edilen işletim sisteminin türü
| `vhd` | Sanal sabit disk
| `writeAcceleratorEnabled` | Diskte writeAccelerator etkin olup olmadığı

**Ağ**

| Veriler | Description | Sunulan sürüm |
|------|-------------|--------------------|
| `ipv4.privateIpAddress` | VM 'nin yerel IPv4 adresi | 2017-04-02
| `ipv4.publicIpAddress` | VM 'nin genel IPv4 adresi | 2017-04-02
| `subnet.address` | VM 'nin alt ağ adresi | 2017-04-02
| `subnet.prefix` | Alt ağ ön eki, örnek 24 | 2017-04-02
| `ipv6.ipAddress` | VM 'nin yerel IPv6 adresi | 2017-04-02
| `macAddress` | VM MAC adresi | 2017-04-02

### <a name="get-user-data"></a>Kullanıcı verilerini al

Yeni bir VM oluştururken, VM sağlama sırasında veya sonrasında kullanılacak bir veri kümesi belirtebilir ve onları ıMDS aracılığıyla alabilirsiniz. Kullanıcı verilerini ayarlamak için [buradaki](https://aka.ms/ImdsUserDataArmTemplate)hızlı başlangıç şablonunu kullanın. Aşağıdaki örnek, bu verilerin ıMD 'ler aracılığıyla nasıl alınacağını gösterir.

> [!NOTE]
> Bu özellik sürümle birlikte yayımlanır `2021-01-01` ve Azure platformunda bir güncelleştirmeye bağlıdır ve şu anda kullanıma alınmış ve her bölgede henüz kullanılamayabilir.

> [!NOTE]
> Güvenlik Bildirimi: ıDS, sanal makinenin tüm uygulamalarına açıktır, gizli veriler kullanıcı verilerine yerleştirilmemelidir.


#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/userData?api-version=2021-01-01&format=text" | base64 --decode
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/userData?api-version=2021-01-01&format=text" | base64 --decode
```

---


#### <a name="sample-1-tracking-vm-running-on-azure"></a>Örnek 1: Azure 'da çalışan Izleme sanal makinesi

Hizmet sağlayıcı olarak, yazılımınızı çalıştıran VM 'lerin sayısını izlemeniz veya VM 'nin benzersizliği izlemesi gereken aracılardan sahip olmanız gerekebilir. Bir VM 'nin benzersiz KIMLIĞINI alabilmesi için `vmId` Instance Metadata Service alanı kullanın.

**İstek**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

---

**Response**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

#### <a name="sample-2-placement-of-different-data-replicas"></a>Örnek 2: farklı veri çoğaltmalarının yerleştirilmesi

Bazı senaryolarda, farklı veri çoğaltmalarının yerleştirilmesi önemli öneme sahiptir. Örneğin, bir [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) aracılığıyla yapılan [çoğaltma yerleşimi](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) veya KAPSAYıCı yerleştirmesi, `platformFaultDomain` ve `platformUpdateDomain` VM 'nin üzerinde çalışıp çalışmadığını bilmeniz gerekebilir.
Bu kararları vermek için örneklerin [kullanılabilirlik alanları](../articles/availability-zones/az-overview.md) de kullanabilirsiniz.
Bu verileri, doğrudan ıMDS aracılığıyla sorgulayabilirsiniz.

**İstek**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Response**

```
0
```

#### <a name="sample-3-get-vm-tags"></a>Örnek 3: VM etiketlerini al

VM etiketleri örnek/işlem/etiket uç noktası altına örnek API 'sini içerir.
Etiketler, bunları bir taksonomi halinde mantıksal olarak düzenlemek için Azure sanal makinenize uygulanmış olabilir. Bir VM 'ye atanan Etiketler aşağıdaki istek kullanılarak alınabilir.

**İstek**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Response**

```
Department:IT;ReferenceNumber:123456;TestStatus:Pending
```

`tags`Alan, etiketleri noktalı virgülle ayrılmış bir dizedir. Bu çıktı, etiketlerde noktalı virgül kullanılıyorsa bir sorun olabilir. Etiketleri programlı olarak ayıklamak için bir Ayrıştırıcı yazılmışsa, alanına güvenmelisiniz `tagsList` . `tagsList`Alan, sınırlandırıcı olmayan BIR JSON dizisidir ve sonuç olarak daha kolay ayrıştırılabilir. Bir VM 'ye atanan tagsList aşağıdaki istek kullanılarak alınabilir.

**İstek**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | jq
```

---

**Response**

#### <a name="windows"></a>[Windows](#tab/windows/)

```json
{
    "value":  [
                  {
                      "name":  "Department",
                      "value":  "IT"
                  },
                  {
                      "name":  "ReferenceNumber",
                      "value":  "123456"
                  },
                  {
                      "name":  "TestStatus",
                      "value":  "Pending"
                  }
              ],
    "Count":  3
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "ReferenceNumber",
    "value": "123456"
  },
  {
    "name": "TestStatus",
    "value": "Pending"
  }
]
```

---


#### <a name="sample-4-get-more-information-about-the-vm-during-support-case"></a>Örnek 4: destek talebi sırasında VM hakkında daha fazla bilgi alın

Hizmet sağlayıcı olarak, VM hakkında daha fazla bilgi edinmek istediğiniz bir destek çağrısı alabilirsiniz. Müşterinin işlem meta verilerinin paylaşılmasını sormak, destek uzmanı 'nın Azure 'daki sanal makine türü hakkında bilgi sahibi olmak için temel bilgiler sağlayabilir.

**İstek**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

---

**Response**

> [!NOTE]
> Yanıt bir JSON dizesidir. Aşağıdaki örnek yanıt, okunabilirlik için oldukça yazdırılır.

#### <a name="windows"></a>[Windows](#tab/windows/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "WindowsServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Windows",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "2019-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "WindowsServer",
            "publisher": "MicrosoftWindowsServer",
            "sku": "2019-Datacenter",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Windows",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "UbuntuServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "Canonical",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "18.04-LTS",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

---

#### <a name="sample-5-get-the-azure-environment-where-the-vm-is-running"></a>Örnek 5: VM 'nin çalıştığı Azure ortamını alın

Azure 'da [Azure Kamu](https://azure.microsoft.com/overview/clouds/government/)gibi çeşitli bağımsız bulutlar vardır. Bazen bazı çalışma zamanı kararları almak için Azure ortamına ihtiyacınız vardır. Aşağıdaki örnekte, bu davranışı nasıl sağlayabileceğiniz gösterilmektedir.

**İstek**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

---

**Response**

```
AzurePublicCloud
```

Azure ortamının bulutu ve değerleri burada listelenmiştir.

| Bulut | Azure ortamı |
|-------|-------------------|
| [Tüm genel kullanıma açık Azure bölgeleri](https://azure.microsoft.com/regions/) | AzurePublicCloud
| [Azure Devlet Kurumları](https://azure.microsoft.com/overview/clouds/government/) | AzureUSGovernmentCloud
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | AzureChinaCloud
| [Azure Almanya](https://azure.microsoft.com/overview/clouds/germany/) | AzureGermanCloud


#### <a name="sample-6-retrieve-network-information"></a>Örnek 6: ağ bilgilerini alma

**İstek**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01" | ConvertTo-Json  -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

---

**Response**

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}
```

#### <a name="sample-7-retrieve-public-ip-address"></a>Örnek 7: genel IP adresini alma

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

---

## <a name="attested-data"></a>Atsınanan veriler

### <a name="get-attested-data"></a>Atsınanan verileri al

IDS, belirtilen verilerin Azure 'dan geldiği garantisi sağlamaya yardımcı olur. Microsoft bu bilgilerin bir parçasını imzalar, böylece Azure Market 'teki bir görüntünün Azure 'da çalıştırdığınız bir görüntü olduğunu doğrulayabilirsiniz.

```
GET /metadata/attested/document
```

#### <a name="parameters"></a>Parametreler

| Name | Gerekli/İsteğe Bağlı | Açıklama |
|------|-------------------|-------------|
| `api-version` | Gerekli | İsteğe hizmet vermek için kullanılan sürüm.
| `nonce` | İsteğe Bağlı | Şifreleme nonce görevi gören 10 basamaklı bir dize. Değer sağlanmazsa, ıDS geçerli UTC zaman damgasını kullanır.

#### <a name="response"></a>Yanıt

```json
{
    "encoding":"pkcs7",
    "signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> [!NOTE]
> IDS 'nin önbelleğe alma mekanizması nedeniyle, önceden önbelleğe alınmış bir nonce değeri döndürülebilir.

İmza blobu, belgenin [PKCS7](https://aka.ms/pkcs7)imzalı bir sürümüdür. SANAL makineye özgü bazı ayrıntılarla birlikte imzalama için kullanılan sertifikayı içerir.

Azure Resource Manager kullanılarak oluşturulan VM 'ler için, belge,,,, `vmId` `sku` `nonce` `subscriptionId` `timeStamp` belgenin oluşturulması ve sona erme tarihi ve görüntüyle ilgili plan bilgileri içerir. Plan bilgileri yalnızca Azure Market görüntüleri için doldurulur.

Klasik dağıtım modeli kullanılarak oluşturulan VM 'Ler için yalnızca, `vmId` doldurulması garanti edilir. Yanıttan sertifikayı ayıklayabilir ve bu yanıtı kullanarak yanıtın geçerli olduğunu ve Azure 'dan geldiğini doğrulayabilirsiniz.

Kodu çözülen belge aşağıdaki alanları içerir:

| Veriler | Description | Sunulan sürüm |
|------|-------------|--------------------|
| `licenseType` | [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit)için lisans türü. Bu yalnızca AHB özellikli VM 'Ler için geçerlidir. | 2020-09-01
| `nonce` | İsteğe bağlı olarak istekle birlikte sağlanmış bir dize. Hayır `nonce` sağlanmazsa, geçerli Eşgüdümlü Evrensel Saat zaman damgası kullanılır. | 2018-10-01
| `plan` | [Azure Marketi görüntü planı](/rest/api/compute/virtualmachines/createorupdate#plan). Plan KIMLIĞINI (adı), ürün görüntüsünü veya teklifi (ürün) ve yayımcı KIMLIĞINI (yayımcı) içerir. | 2018-10-01
| `timestamp.createdOn` | İmzalanmış belgenin oluşturulduğu zamana ilişkin UTC zaman damgası | 2018-20-01
| `timestamp.expiresOn` | İmzalanan belgenin süresi dolduğu zaman için UTC zaman damgası | 2018-10-01
| `vmId` | VM için [benzersiz tanımlayıcı](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2018-10-01
| `subscriptionId` | Sanal makine için Azure aboneliği | 2019-04-30
| `sku` | VM görüntüsü için belirli SKU | 2019-11-01

> [!NOTE]
> Klasik (Azure Resource Manager olmayan) VM 'Ler için yalnızca VMID 'nin doldurulması garanti edilir.

Örnek belge:
```json
{
   "nonce":"20201130-211924",
   "plan":{
      "name":"planName",
      "product":"planProduct",
      "publisher":"planPublisher"
   },
   "sku":"Windows-Server-2012-R2-Datacenter",
   "subscriptionId":"8d10da13-8125-4ba9-a717-bf7490507b3d",
   "timeStamp":{
      "createdOn":"11/30/20 21:19:19 -0000",
      "expiresOn":"11/30/20 21:19:24 -0000"
   },
   "vmId":"02aab8a4-74ef-476e-8182-f6d2ba4166a6"
}
```

#### <a name="sample-1-validate-that-the-vm-is-running-in-azure"></a>Örnek 1: VM 'nin Azure 'da çalıştığını doğrulama

Azure Marketi 'ndeki satıcılar, yazılımlarının yalnızca Azure 'da çalışmak üzere lisanslanmasını sağlamak istiyor. Birisi VHD 'yi şirket içi bir ortama kopyarsa, satıcının bunu algılayabilmesi gerekir. IMDS aracılığıyla, bu satıcılar yalnızca Azure 'dan gelen yanıtı garanti eden imzalı verileri alabilir.

> [!NOTE]
> Bu örnek, JQ yardımcı programının yüklenmesini gerektirir.

**Doğrulama**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

İmzanın Microsoft Azure olduğunu doğrulayın ve hata için sertifika zincirini denetleyin.

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2020-09-01" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**Sonuçlar**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "licenseType": "Windows_Client",  
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

İmzanın Microsoft Azure olduğunu doğrulayın ve sertifika zincirini hatalara karşı denetleyin.

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

---

> [!NOTE]
> IDS 'nin önbelleğe alma mekanizması nedeniyle, önceden önbelleğe alınmış bir `nonce` değer döndürülebilir.

`nonce`Başlangıç isteğinde bir parametre sağladıysanız, imzalı belgedeki ' de karşılaştırılabilir `nonce` .

> [!NOTE]
> Genel bulut ve her bir sogeign bulutu için sertifika farklı olacaktır.

| Bulut | Sertifika |
|-------|-------------|
| [Tüm genel kullanıma açık Azure bölgeleri](https://azure.microsoft.com/regions/) | *. metadata.azure.com
| [Azure Devlet Kurumları](https://azure.microsoft.com/overview/clouds/government/) | *. metadata.azure.us
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | *. metadata.azure.cn
| [Azure Almanya](https://azure.microsoft.com/overview/clouds/germany/) | *. metadata.microsoftazure.de

> [!NOTE]
> Sertifikalar, genel bulut için tam olarak eşleşen bir eşleşmeyebilir `metadata.azure.com` . Bu nedenle, sertifika doğrulaması herhangi bir alt etki alanından ortak bir ada izin verilmelidir `.metadata.azure.com` .

Doğrulama sırasında ağ kısıtlamalarına bağlı olarak ara sertifikanın indirilebileceği durumlarda ara sertifikayı sabitleyebilir. Azure, standart PKI uygulaması olan sertifikaların üzerine kaydolur. Geçiş gerçekleştiğinde sabitlenmiş sertifikaları güncelleştirmeniz gerekir. Ara sertifikayı güncelleştirme değişikliği planlandığı zaman, Azure blogu güncelleştirilir ve Azure müşterileri bilgilendirilir. 

Ara sertifikaları [PKI deposunda](https://www.microsoft.com/pki/mscorp/cps/default.htm)bulabilirsiniz. Her bir bölgenin ara sertifikaları farklı olabilir.

> [!NOTE]
> Azure Çin 21Vianet için ara sertifika, balya 'ın yerine DigiCert genel kök CA 'dan olacaktır.
Kök zincir yetkilisinin bir parçası olarak Azure Çin ara sertifikalarını sabitlediğiniz takdirde, ara sertifikaların güncellenmesi gerekir.


## <a name="managed-identity"></a>Yönetilen kimlik

Sistem tarafından atanan yönetilen bir kimlik, sanal makinede etkinleştirilebilir. VM 'ye bir veya daha fazla kullanıcı tarafından atanan yönetilen kimlik de atayabilirsiniz.
Ardından, ıMDS 'den Yönetilen kimlikler için belirteçler isteyebilirsiniz. Azure Key Vault gibi diğer Azure hizmetleriyle kimlik doğrulaması yapmak için bu belirteçleri kullanın.

Bu özelliği etkinleştirmeye yönelik ayrıntılı adımlar için bkz. [erişim belirteci alma](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="load-balancer-metadata"></a>Load Balancer meta veri
Sanal makineyi veya sanal makine kümesi örneklerini bir Azure Standart Load Balancer arkasına yerleştirdiğinizde, yük dengeleyici ve örneklerle ilgili meta verileri almak için ıMDS 'yi kullanabilirsiniz. Daha fazla bilgi için bkz. [yük dengeleyici bilgilerini alma](../articles/load-balancer/instance-metadata-service-load-balancer.md).

## <a name="scheduled-events"></a>Zamanlanmış olaylar
Zamanlanan olayların durumunu ıMDS kullanarak elde edebilirsiniz. Ardından Kullanıcı, bu olaylar üzerinde çalıştırılacak bir eylemler kümesi belirtebilir. Daha fazla bilgi için bkz. Windows için Linux veya [Zamanlanmış olaylara](../articles/virtual-machines/windows/scheduled-events.md) [yönelik Zamanlanmış olaylar](../articles/virtual-machines/linux/scheduled-events.md) .


## <a name="sample-code-in-different-languages"></a>Farklı dillerdeki örnek kod

Aşağıdaki tabloda, sanal makine içinde farklı diller kullanılarak ıMDS çağırma örnekleri listelenmektedir:

| Dil | Örnek |
|----------|---------|
| Bash | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
| C# | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
| Başlayın | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
| Java | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
| NodeJS | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
| Perl | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
| PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
| Puppet | https://github.com/keirans/azuremetadata
| Python | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
| Ruby | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="errors-and-debugging"></a>Hatalar ve hata ayıklama

Veri öğesi bulunamadı veya hatalı oluşturulmuş bir istek varsa, Instance Metadata Service standart HTTP hataları döndürür. Örnek:

| HTTP durum kodu | Nedeni |
|------------------|--------|
| `200 OK` | İstek başarılı oldu.
| `400 Bad Request` | `Metadata: true`Yaprak düğüm sorgulanırken başlık veya eksik parametre eksik `format=json`
| `404 Not Found` | İstenen öğe yok
| `405 Method Not Allowed` | HTTP yöntemi (fiil) uç noktada desteklenmez.
| `410 Gone` | En fazla 70 saniye boyunca bir süre sonra yeniden deneyin
| `429 Too Many Requests` | API [hız sınırları](#rate-limiting) aşıldı
| `500 Service Error` | Bir süre sonra yeniden dene

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

- Hatayı alıyorum `400 Bad Request, Required metadata header not specified` . Bu ne anlama geliyor?
  - IDS, üstbilginin `Metadata: true` istek içine geçirilmesini gerektirir. Bu üstbilgiyi REST çağrısına geçirmek, ıMDS 'ye erişim sağlar.

- Neden Sanal makinem için işlem bilgilerini alamıyorum?
  - Şu anda, ıDS yalnızca Azure Resource Manager oluşturulan örnekleri destekler.

- Azure Resource Manager bir süre önce VM 'imi oluşturdum. İşlem meta veri bilgilerini neden göremiyorum?
  - VM 'nizi 2016 Eylül 'den sonra oluşturduysanız, işlem meta verilerini görmeye başlamak için bir [etiket](../articles/azure-resource-manager/management/tag-resources.md) ekleyin. VM 'nizi 2016 Eylül 'den önce oluşturduysanız meta verileri yenilemek için VM örneğine uzantıları veya veri diskleri ekleyin veya kaldırın.

- Kullanıcı verileri özel verilerle aynı mı?
  - Kullanıcı verileri, özel verilere benzer işlevselliği sunarak, kendi meta verilerinizi VM örneğine geçirmenize olanak sağlar. Aradaki fark, Kullanıcı verilerinin ıMDS aracılığıyla alınması ve sanal makine örneğinin kullanım ömrü boyunca kalıcıdır. Varolan özel veri özelliği, [Bu makalede](https://docs.microsoft.com/azure/virtual-machines/custom-data)açıklandığı gibi çalışmaya devam edecektir. Ancak, yerel sistem klasörü aracılığıyla, ıDS aracılığıyla değil, yalnızca özel veri alabilirsiniz.

- Neden yeni bir sürüm için doldurulmuş tüm verileri görmüyorum?
  - VM 'nizi 2016 Eylül 'den sonra oluşturduysanız, işlem meta verilerini görmeye başlamak için bir [etiket](../articles/azure-resource-manager/management/tag-resources.md) ekleyin. VM 'nizi 2016 Eylül 'den önce oluşturduysanız meta verileri yenilemek için VM örneğine uzantıları veya veri diskleri ekleyin veya kaldırın.

- Hatayı neden alıyorum `500 Internal Server Error` `410 Resource Gone` ?
  - İsteğinizi yeniden deneyin. Daha fazla bilgi için bkz. [geçici hata işleme](/azure/architecture/best-practices/transient-faults). Sorun devam ederse, VM için Azure portal bir destek sorunu oluşturun.

- Bu, sanal makine ölçek kümesi örnekleri için mi çalışıyor?
  - Evet, sanal makine ölçek kümesi örnekleri için ıMDS kullanılabilir.

- Sanal makine ölçek kümelerinde etiketlerimi güncelleştirdim, ancak örneklerde gözükmez (tek örnekli VM 'lerden farklı olarak). Sorun mu yaşıyorsunuz?
  - Şu anda sanal makine ölçek kümeleri için olan Etiketler yalnızca bir yeniden başlatma, yeniden görüntü veya örneğe disk değişikliği üzerinde VM 'yi gösterir.

- Ayrıntılar bölümünde Sanal makinem için SKU bilgilerini neden görmüyorum `instance/compute` ?
  - Azure platformu, Azure Marketi 'nden oluşturulan özel görüntüler için özel görüntü için SKU bilgilerini ve özel görüntüden oluşturulan tüm VM 'Lerin ayrıntılarını korumaz. Bu, tasarım ve bu nedenle VM ayrıntılarında ortaya çıkmış `instance/compute` .

- İstek, hizmete çağrımda neden zaman aşımına uğradı?
  - Meta veri çağrılarının, VM 'nin birincil ağ kartına atanan birincil IP adresinden yapılması gerekir. Ayrıca, rotalarınızı değiştirdiyseniz, sanal makinenizin yerel yönlendirme tablosunda 169.254.169.254/32 adresi için bir yol olmalıdır.

    ### <a name="windows"></a>[Windows](#tab/windows/)

    1. Yerel yönlendirme tablonuzu dökümünü alın ve ıMDS girişini bulun. Örnek:
        ```console
        > route print
        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
                127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
            168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
        169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
        ... (continues) ...
        ```
    1. İçin bir yolun mevcut olduğunu doğrulayın `169.254.169.254` ve ilgili ağ arabirimini (örneğin, `172.16.69.7` ) unutmayın.
    1. Arabirim yapılandırmasını dökümünü alın ve yönlendirme tablosunda başvurulan öğesine karşılık gelen arabirimi MAC (fiziksel) adresini belirterek bulun.
        ```console
        > ipconfig /all
        ... (continues) ...
        Ethernet adapter Ethernet:

        Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
        Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
        Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
        DHCP Enabled. . . . . . . . . . . : Yes
        Autoconfiguration Enabled . . . . : Yes
        Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
        IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
        Subnet Mask . . . . . . . . . . . : 255.255.255.0
        ... (continues) ...
        ```
    1. Arabirimin VM 'nin birincil NIC 'sine ve birincil IP 'ye karşılık geldiğini doğrulayın. Azure portal ağ yapılandırmasına bakarak veya Azure CLı ile arayarak birincil NIC ve IP 'yi bulabilirsiniz. Özel IP 'Leri (CLı kullanıyorsanız MAC adresini) göz önünde bulabilirsiniz. PowerShell CLı örneği aşağıda verilmiştir:
        ```powershell
        $ResourceGroup = '<Resource_Group>'
        $VmName = '<VM_Name>'
        $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
        foreach($NicName in $NicNames)
        {
            $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
            Write-Host $NicName, $Nic.primary, $Nic.macAddress
        }
        # Output: wintest767 True 00-0D-3A-E5-1C-C0
        ```
    1. Eşleşiyorlarsa, birincil NIC ve IP 'nin hedefleneceği şekilde yönlendirme tablosunu güncelleştirin.

    ### <a name="linux"></a>[Linux](#tab/linux/)

    1. Yerel yönlendirme tablonuzu gibi bir komutla dökümünü alın `netstat -r` ve ıMDS girişini (ör.) arayın:
        ```console
        ~$ netstat -r
        Kernel IP routing table
        Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
        default         _gateway        0.0.0.0         UG        0 0          0 eth0
        168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
        169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
        172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
        ```
    1. İçin bir yolun mevcut olduğunu doğrulayın `169.254.169.254` ve ilgili ağ arabirimine (ör.) göz önünde bulunduğunu unutmayın `eth0` .
    1. Yönlendirme tablosunda karşılık gelen arabirim için arabirim yapılandırması dökümünü alın (yapılandırma dosyasının tam adı değişebilir)
        ```console
        ~$ cat /etc/netplan/50-cloud-init.yaml
        network:
        ethernets:
            eth0:
                dhcp4: true
                dhcp4-overrides:
                    route-metric: 100
                dhcp6: false
                match:
                    macaddress: 00:0d:3a:e4:c7:2e
                set-name: eth0
        version: 2
        ```
    1. Dinamik IP kullanıyorsanız MAC adresini aklınızda edin. Statik IP kullanıyorsanız, listelenen IP 'leri ve/veya MAC adresini görebilirsiniz.
    1. Arabirimin VM 'nin birincil NIC 'sine ve birincil IP 'ye karşılık geldiğini doğrulayın. Azure portal ağ yapılandırmasına bakarak veya Azure CLı ile arayarak birincil NIC ve IP 'yi bulabilirsiniz. Özel IP 'Leri (CLı kullanıyorsanız MAC adresini) göz önünde bulabilirsiniz. PowerShell CLı örneği aşağıda verilmiştir:
        ```powershell
        $ResourceGroup = '<Resource_Group>'
        $VmName = '<VM_Name>'
        $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
        foreach($NicName in $NicNames)
        {
            $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
            Write-Host $NicName, $Nic.primary, $Nic.macAddress
        }
        # Output: ipexample606 True 00-0D-3A-E4-C7-2E
        ```
    1. Bunlar eşleşmiyorsa, yönlendirme tablosunu, birincil NIC/IP 'nin hedeflediği şekilde güncelleştirin.

    ---

- Windows Server 'da Yük Devretme Kümelemesi
  - Yük Devretme Kümelemesi ile ıMDS 'yi sorgularken, bazen yönlendirme tablosuna bir rota eklenmesi gerekir. Aşağıdaki adımları uygulayın:

    1. Yönetici ayrıcalıklarına sahip bir komut istemi açın.

    1. Aşağıdaki komutu çalıştırın ve IPv4 yol tablosundaki ağ hedefi () arabiriminin adresini göz önünde edin `0.0.0.0` .

    ```bat
    route print
    ```

    > [!NOTE]
    > Aşağıdaki örnek çıktı, yük devretme kümesi etkin bir Windows Server VM 'sinden yapılır. Kolaylık olması için, çıkış yalnızca IPv4 yol tablosunu içerir.

    ```
    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
            0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
            10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
            10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
            10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
            10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
    127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
        169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
        169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
    169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
            224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
    255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
    255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
    255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
    ```

    Aşağıdaki komutu çalıştırın ve `0.0.0.0` Bu örnekte () olan ağ hedefi () Için arabirimin adresini kullanın `10.0.1.10` .

    ```bat
    route add 169.254.169.254/32 10.0.1.10 metric 1 -p
    ```

## <a name="support"></a>Destek

Birden çok denemeden sonra meta veri yanıtı alamazsanız Azure portal bir destek sorunu oluşturabilirsiniz.

## <a name="product-feedback"></a>Ürün geri bildirimi

[Burada](https://feedback.azure.com/forums/216843-virtual-machines?category_id=394627) Instance Metadata Service sanal makineler altında Kullanıcı geri bildirim kanalımız ürün geri bildirimi ve fikirleri sağlayabilirsiniz >

## <a name="next-steps"></a>Sonraki adımlar

- [VM için bir erişim belirteci alma](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

- [Linux için Zamanlanmış olaylar](../articles/virtual-machines/linux/scheduled-events.md)

- [Windows için Zamanlanmış olaylar](../articles/virtual-machines/windows/scheduled-events.md)
