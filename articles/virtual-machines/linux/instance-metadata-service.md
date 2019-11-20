---
title: Azure Instance Metadata Service
description: Linux VM 'nin işlem, ağ ve yaklaşan bakım olayları hakkında bilgi almak için yeniden oluşturma arabirimi.
services: virtual-machines-linux
documentationcenter: ''
author: KumariSupriya
manager: harijayms
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: d18822cf7d4827016a55fa1b1a1408a003e933ab
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035988"
---
# <a name="azure-instance-metadata-service"></a>Azure örnek meta veri hizmeti

Azure Instance Metadata Service, sanal makinelerinizi yönetmek ve yapılandırmak için kullanılabilecek çalışan sanal makine örnekleri hakkında bilgi sağlar.
Bu, SKU, ağ yapılandırması ve yaklaşan bakım olayları gibi bilgileri içerir. Hangi tür bilgilerin kullanılabildiği hakkında daha fazla bilgi için bkz. [metadata API 'leri](#metadata-apis).

Azure Instance Metadata Service, [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/)kullanılarak oluşturulan tüm IaaS VM 'lerine ERIŞILEBILEN bir REST uç noktasıdır.
Uç nokta, yalnızca VM içinden erişilebilen, iyi bilinen yönlendirilebilir olmayan IP adresinde (`169.254.169.254`) kullanılabilir.

> [!IMPORTANT]
> Bu hizmet, tüm Azure bölgelerinde **genel kullanıma sunulmuştur** .  Sanal makine örnekleri hakkında yeni bilgileri kullanıma sunmak için düzenli olarak güncelleştirmeleri alır. Bu sayfa, kullanılabilir olan güncel [meta veri API 'lerini](#metadata-apis) yansıtır.

## <a name="service-availability"></a>Hizmet kullanılabilirliği

Hizmet genel kullanıma sunulan Azure bölgelerinde kullanılabilir. Tüm API sürümleri tüm Azure bölgelerinde kullanılabilir olmayabilir.

Bölgeler                                        | Sonrası?                                 | Desteklenen Sürümler
-----------------------------------------------|-----------------------------------------------|-----------------
[Tüm genel kullanıma açık Azure bölgeleri](https://azure.microsoft.com/regions/)     | Genel Kullanıma Sunuldu | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04
[Azure Devlet Kurumları](https://azure.microsoft.com/overview/clouds/government/)              | Genel Kullanıma Sunuldu | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30
[Azure Çin](https://www.azure.cn/)                                                     | Genel Kullanıma Sunuldu | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30
[Azure Almanya](https://azure.microsoft.com/overview/clouds/germany/)                    | Genel Kullanıma Sunuldu | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30

Bu tablo, hizmet güncelleştirmeleri olduğunda ve veya desteklenen yeni sürümler varsa güncelleştirilir.

Instance Metadata Service denemek için yukarıdaki bölgelerde [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) veya [Azure Portal](https://portal.azure.com) bir VM oluşturun ve aşağıdaki örnekleri izleyin.

## <a name="usage"></a>Kullanım

### <a name="versioning"></a>Sürüm oluşturma

Instance Metadata Service sürümlenmiş ve HTTP isteğindeki API sürümünün belirtilmesi zorunludur.

Bu [kullanılabilirlik tablosunda](#service-availability)en yeni sürümlerin listelendiğini görebilirsiniz.

Daha yeni sürümler eklendikçe, betiklerinizin belirli veri biçimlerinde bağımlılıkları varsa, daha eski sürümlere uyumluluk için yine de erişilebilir.

Sürüm belirtilmediğinde, en yeni Desteklenen sürümlerin listesiyle birlikte bir hata döndürülür.

> [!NOTE]
> Yanıt bir JSON dizesidir. Aşağıdaki örnek yanıt, okunabilirlik için oldukça yazdırılır.

**İstek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance"
```

**Yanıt**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2018-10-01",
        "2018-04-02",
        "2018-02-01"
    ]
}
```

### <a name="using-headers"></a>Üst bilgileri kullanma

Instance Metadata Service sorgulayıp, isteğin istem dışı yönlendirilmediğinden emin olmak için üst bilgi `Metadata: true` sağlamanız gerekir.

### <a name="retrieving-metadata"></a>Meta veriler alınıyor

Örnek meta verileri, [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/)kullanılarak oluşturulan/yönetilen VM 'leri çalıştırmak için kullanılabilir. Aşağıdaki isteği kullanarak bir sanal makine örneği için tüm veri kategorilerine erişin:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE]
> Tüm örnek meta veri sorguları büyük/küçük harfe duyarlıdır.

### <a name="data-output"></a>Veri çıkışı

Varsayılan olarak, Instance Metadata Service JSON biçiminde (`Content-Type: application/json`) verileri döndürür. Ancak, farklı API 'Ler isteniyorsa verileri farklı biçimlerde döndürür.
Aşağıdaki tablo, API 'Lerin destekleyebileceği diğer veri biçimlerinin bir başvurusudur.

API | Varsayılan veri biçimi | Diğer biçimler
--------|---------------------|--------------
/Instance | nesnesinde | metin
/scheduledevents | nesnesinde | yok
/attested | nesnesinde | yok

Varsayılan olmayan bir yanıt biçimine erişmek için istenen biçimi istekte bir sorgu dizesi parametresi olarak belirtin. Örneğin:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> `format=json` çalışmıyor yaprak düğümler için. Varsayılan biçim JSON ise, bu sorgular için `format=text` açıkça belirtilmesi gerekir.

### <a name="security"></a>Güvenlik

Instance Metadata Service uç noktasına yalnızca, yönlendirilemeyen bir IP adresi üzerinde çalışan sanal makine örneği içinden erişilebilir. Ayrıca, `X-Forwarded-For` üst bilgisine sahip herhangi bir istek hizmet tarafından reddedilir.
Ayrıca, gerçek isteğin istenmeden yeniden yönlendirmenin bir parçası olmamasını sağlamak için istekler bir `Metadata: true` üst bilgisi içermelidir.

### <a name="error"></a>Hata

Veri öğesi bulunamadı veya hatalı oluşturulmuş bir istek varsa, Instance Metadata Service standart HTTP hataları döndürür. Örneğin:

HTTP durum kodu | Neden
----------------|-------
200 TAMAM |
400 Hatalı Istek | `Metadata: true` üstbilgisi eksik veya bir yaprak düğüm sorgulanırken biçim eksik
404 Bulunamadı | İstenen öğe yok
405 yöntemine Izin verilmiyor | Yalnızca `GET` ve `POST` istekleri destekleniyor
429 çok fazla Istek | API Şu anda saniyede en çok 5 sorgu destekliyor
500 hizmet hatası     | Bir süre sonra yeniden dene

### <a name="examples"></a>Örnekler

> [!NOTE]
> Tüm API yanıtları JSON dizeleridir. Aşağıdaki örnek yanıtların hepsi okunabilirlik için oldukça yazdırılır.

#### <a name="retrieving-network-information"></a>Ağ bilgileri alınıyor

**İstek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Yanıt**

> [!NOTE]
> Yanıt bir JSON dizesidir. Aşağıdaki örnek yanıt, okunabilirlik için oldukça yazdırılır.

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

#### <a name="retrieving-public-ip-address"></a>Genel IP adresi alınıyor

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Örnek için tüm meta verileri alma

**İstek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2019-03-11"
```

**Yanıt**

> [!NOTE]
> Yanıt bir JSON dizesidir. Aşağıdaki örnek yanıt, okunabilirlik için oldukça yazdırılır.

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Prod;Role:WorkerRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Windows sanal makinesinde meta veriler alınıyor

**İstek**

Örnek meta verileri, Windows 'da PowerShell yardımcı programı aracılığıyla alınabilir `curl`: 

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-03-11 | select -ExpandProperty Content
```

Veya `Invoke-RestMethod` cmdlet 'i aracılığıyla:

```powershell

Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2019-03-11 -Method get 
```

**Yanıt**

> [!NOTE]
> Yanıt bir JSON dizesidir. Aşağıdaki örnek yanıt, okunabilirlik için oldukça yazdırılır.

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="metadata-apis"></a>Meta veri API 'Leri

#### <a name="the-following-apis-are-available-through-the-metadata-endpoint"></a>Meta veri uç noktası aracılığıyla aşağıdaki API 'Ler mevcuttur:

Veriler | Açıklama | Sunulan sürüm
-----|-------------|-----------------------
kanıtlamak | Bkz. [Atsınanan veriler](#attested-data) | 2018-10-01
identity | Azure kaynakları için Yönetilen kimlikler. Bkz. [erişim belirteci alma](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
instance | Bkz. [örnek API 'si](#instance-api) | 2017-04-02
scheduledevents | Bkz. [zamanlanan olaylar](scheduled-events.md) | 2017-08-01

#### <a name="instance-api"></a>Örnek API 'SI
##### <a name="the-following-compute-categories-are-available-through-the-instance-api"></a>Aşağıdaki Işlem kategorileri örnek API aracılığıyla kullanılabilir:

> [!NOTE]
> Meta veri uç noktası aracılığıyla, örnek/işlem aracılığıyla aşağıdaki kategorilere erişilir

Veriler | Açıklama | Sunulan sürüm
-----|-------------|-----------------------
azEnvironment | VM 'nin çalıştığı Azure ortamı | 2018-10-01
customData | [Özel verileri](#custom-data) gör | 2019-02-01
location | VM 'nin çalıştığı Azure bölgesi | 2017-04-02
ad | VM adı | 2017-04-02
teklif | VM görüntüsü için teklif bilgileri ve yalnızca Azure görüntü Galerisi 'nden dağıtılan görüntülerde bulunur | 2017-04-02
osType | Linux veya Windows | 2017-04-02
placementGroupId | Sanal makine ölçek kümesinin [yerleştirme grubu](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) | 2017-08-01
plan | Bir Azure Market görüntüsü ise VM için ad, ürün ve yayımcı içeren bir [plan planlayın](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) | 2018-04-02
platformUpdateDomain |  VM 'nin çalıştığı [etki alanını güncelleştirme](manage-availability.md) | 2017-04-02
platformFaultDomain | VM 'nin çalıştığı [hata etki alanı](manage-availability.md) | 2017-04-02
sağlayıcısını | VM sağlayıcısı | 2018-10-01
publicKeys | VM ve yollara atanan [ortak anahtarların koleksiyonu](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) | 2018-04-02
publisher | VM görüntüsünün yayımcısı | 2017-04-02
resourceGroupName | Sanal makineniz için [kaynak grubu](../../azure-resource-manager/resource-group-overview.md) | 2017-08-01
resourceId | Kaynağın [tam](https://docs.microsoft.com/rest/api/resources/resources/getbyid) kimliği | 2019-03-11
isteyin | VM görüntüsü için belirli SKU | 2017-04-02
subscriptionId | Sanal makine için Azure aboneliği | 2017-08-01
etiketler | Sanal makineniz için [Etiketler](../../azure-resource-manager/resource-group-using-tags.md)  | 2017-08-01
tagsList | Daha kolay programlı ayrıştırma için JSON dizisi olarak biçimlendirilen Etiketler  | 2019-06-04
version | VM görüntüsünün sürümü | 2017-04-02
vmId | VM için [benzersiz tanımlayıcı](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2017-04-02
vmScaleSetName | Sanal makine ölçek kümesinin [sanal makine ölçek kümesi adı](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) | 2017-12-01
vmSize | [VM boyutu](sizes.md) | 2017-04-02
bölge | Sanal makinenizin [kullanılabilirlik bölgesi](../../availability-zones/az-overview.md) | 2017-12-01

##### <a name="the-following-network-categories-are-available-through-the-instance-api"></a>Aşağıdaki ağ kategorileri örnek API 'SI aracılığıyla kullanılabilir:

> [!NOTE]
> Meta veri uç noktası aracılığıyla, örnek/ağ/arabirim aracılığıyla aşağıdaki kategorilere erişilir

Veriler | Açıklama | Sunulan sürüm
-----|-------------|-----------------------
IPv4/Privateıpaddress | VM 'nin yerel IPv4 adresi | 2017-04-02
IPv4/Publicıpaddress | VM 'nin genel IPv4 adresi | 2017-04-02
alt ağ/adres | VM 'nin alt ağ adresi | 2017-04-02
alt ağ/ön ek | Alt ağ ön eki, örnek 24 | 2017-04-02
ipv6/ipAddress | VM 'nin yerel IPv6 adresi | 2017-04-02
macAddress | VM MAC adresi | 2017-04-02

## <a name="attested-data"></a>Atsınanan veriler

Örnek meta verileri 169.254.169.254 üzerinde http uç noktasında yanıt verir. Instance Metadata Service tarafından sunulan senaryonun bir kısmı, yanıt veren verilerin Azure 'dan geldiği garantisi sağlamaktır. Market görüntülerinin Azure 'da çalıştığından emin olmak için bu bilgilerin bir parçasını imzalıyoruz.

### <a name="example-attested-data"></a>Örnek Atsınanan veriler

> [!NOTE]
> Tüm API yanıtları JSON dizeleridir. Aşağıdaki örnek yanıtlar okunabilirlik için oldukça yazdırılır.

 **İstek**

 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

Api sürümü zorunlu bir alandır. Desteklenen API sürümleri için [hizmet kullanılabilirliği bölümüne](#service-availability) bakın.
Nonce, belirtilen isteğe bağlı 10 basamaklı bir dizedir. Nonce, isteği izlemek için kullanılabilir ve sağlanmazsa, yanıt kodlamalı dize ' de geçerli UTC zaman damgası döndürülür.

 **Yanıt**

> [!NOTE]
> Yanıt bir JSON dizesidir. Aşağıdaki örnek yanıt, okunabilirlik için oldukça yazdırılır.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> İmza blobu, belgenin [PKCS7](https://aka.ms/pkcs7) imzalı bir sürümüdür. Oturum açmak için kullanılan sertifikayı ve VMID, nonce, SubscriptionID, belgenin oluşturulması ve süre sonu için zaman damgası ve görüntüyle ilgili plan bilgileri gibi VM ayrıntılarını içerir. Plan bilgileri yalnızca Azure Market Place görüntüleri için doldurulur. Sertifika yanıttan ayıklanabilir ve yanıtın geçerli olduğunu ve Azure 'dan geldiğini doğrulamak için kullanılır.

#### <a name="retrieving-attested-metadata-in-windows-virtual-machine"></a>Windows sanal makinesinde atsınanan meta veriler alınıyor

 **İstek**

Örnek meta verileri, Windows 'da PowerShell yardımcı programı aracılığıyla alınabilir `curl`:

 ```bash
curl -H @{'Metadata'='true'} "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" | select -ExpandProperty Content
```

 Veya `Invoke-RestMethod` cmdlet 'i aracılığıyla:

 ```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" -Method get
```

Api sürümü zorunlu bir alandır. Desteklenen API sürümleri için hizmet kullanılabilirliği bölümüne bakın.
Nonce, belirtilen isteğe bağlı 10 basamaklı bir dizedir. Nonce, isteği izlemek için kullanılabilir ve sağlanmazsa, yanıt kodlamalı dize ' de geçerli UTC zaman damgası döndürülür.

 **Yanıt**

> [!NOTE]
> Yanıt bir JSON dizesidir. Aşağıdaki örnek yanıt, okunabilirlik için oldukça yazdırılır.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> İmza blobu, belgenin [PKCS7](https://aka.ms/pkcs7) imzalı bir sürümüdür. Oturum açmak için kullanılan sertifikayı ve VMID, nonce, SubscriptionID, belgenin oluşturulması ve süre sonu için zaman damgası ve görüntüyle ilgili plan bilgileri gibi VM ayrıntılarını içerir. Plan bilgileri yalnızca Azure Market Place görüntüleri için doldurulur. Sertifika yanıttan ayıklanabilir ve yanıtın geçerli olduğunu ve Azure 'dan geldiğini doğrulamak için kullanılır.


## <a name="example-scenarios-for-usage"></a>Kullanım için örnek senaryolar  

### <a name="tracking-vm-running-on-azure"></a>Azure üzerinde çalıştırılan VM'yi izleme

Hizmet sağlayıcı olarak, yazılımınızı çalıştıran VM 'lerin sayısını izlemeniz veya VM 'nin benzersizliği izlemesi gereken aracılardan sahip olmanız gerekebilir. Bir VM 'nin benzersiz KIMLIĞINI alabilmesi için Instance Metadata Service `vmId` alanı kullanın.

**İstek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Yanıt**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Hata/güncelleştirme etki alanı temelinde kapsayıcıların, veri bölümlerinin yerleşimi

Bazı senaryolarda, farklı veri çoğaltmalarının yerleştirilmesi önemli öneme sahiptir. Örneğin, bir [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) aracılığıyla yapılan [çoğaltma yerleştirmesi](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) veya kapsayıcı YERLEŞTIRMESI `platformFaultDomain` bilmeniz ve VM 'nin üzerinde çalıştığı `platformUpdateDomain` gerekir.
Bu kararları vermek için örneklerin [kullanılabilirlik alanları](../../availability-zones/az-overview.md) de kullanabilirsiniz.
Bu verileri doğrudan Instance Metadata Service aracılığıyla sorgulayabilirsiniz.

**İstek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Yanıt**

```text
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Destek olayı sırasında VM hakkında daha fazla bilgi alma

Hizmet sağlayıcı olarak, VM hakkında daha fazla bilgi edinmek istediğiniz bir destek çağrısı alabilirsiniz. Müşterinin işlem meta verilerinin paylaşılmasını sormak, destek uzmanı 'nın Azure 'daki sanal makine türü hakkında bilgi sahibi olmak için temel bilgiler sağlayabilir.

**İstek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-08-01"
```

**Yanıt**

> [!NOTE]
> Yanıt bir JSON dizesidir. Aşağıdaki örnek yanıt, okunabilirlik için oldukça yazdırılır.

```json
{
  "compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }
}
```

### <a name="getting-azure-environment-where-the-vm-is-running"></a>VM'nin çalıştırıldığı Azure Ortamını alma

Azure 'da [Azure Kamu](https://azure.microsoft.com/overview/clouds/government/)gibi çeşitli bağımsız bulutlar vardır. Bazen bazı çalışma zamanı kararları almak için Azure ortamına ihtiyacınız vardır. Aşağıdaki örnekte, bu davranışı nasıl sağlayabileceğiniz gösterilmektedir.

**İstek**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Yanıt**
```bash
AzurePublicCloud
```
Azure ortamının bulutu ve değerleri aşağıda listelenmiştir.

 Bulut   | Azure ortamı
---------|-----------------
[Tüm genel kullanıma açık Azure bölgeleri](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Devlet Kurumları](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure Çin](https://azure.microsoft.com/global-infrastructure/china/)                  | AzureChinaCloud
[Azure Almanya](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

### <a name="getting-the-tags-for-the-vm"></a>VM için Etiketler alınıyor

Etiketler, bunları bir taksonomi halinde mantıksal olarak düzenlemek için Azure sanal makinenize uygulanmış olabilir. Bir VM 'ye atanan Etiketler aşağıdaki istek kullanılarak alınabilir.

**İstek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Yanıt**

```text
Department:IT;Environment:Test;Role:WebRole
```

`tags` alan etiketleri noktalı virgülle ayırarak bir dizedir. Etiketlerde noktalı virgül kullanılırsa bu bir sorun olabilir. Etiketleri programlı olarak ayıklamak üzere yazılmışsa, sınırlandırıcı olmayan bir JSON dizisi olan `tagsList` alana ve sonuç olarak ayrıştırılmaya daha kolay.

**İstek**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04&format=text"
```

**Yanıt**

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "Environment",
    "value": "Test"
  },
  {
    "name": "Role",
    "value": "WebRole"
  }
]
```

### <a name="validating-that-the-vm-is-running-in-azure"></a>VM'nin Azure'da çalıştırıldığını doğrulama

Market satıcıları, yazılımlarının yalnızca Azure 'da çalışmak üzere lisanslanmasını sağlamak istiyor. Başka biri VHD 'yi şirket içine kopyalarsa, bu durumda bu, bunu tespit etme yeteneğine sahip olmalıdır. Instance Metadata Service çağırarak Market satıcıları yalnızca Azure 'dan gelen yanıtı garanti eden imzalı verileri alabilir.

> [!NOTE]
> JQ 'ın yüklenmesini gerektirir.

**İstek**

 ```bash
  # Get the signature
   curl  --silent -H Metadata:True http://169.254.169.254/metadata/attested/document?api-version=2019-04-30 | jq -r '.["signature"]' > signature
  # Decode the signature
  base64 -d signature > decodedsignature
  #Get PKCS7 format
  openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
  # Get Public key out of pkc7
  openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
  #Get the intermediate certificate
  wget -q -O intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
  openssl x509 -inform der -in intermediate.cer -out intermediate.pem
  #Verify the contents
  openssl smime -verify -in sign.pk7 -inform pem -noverify
 ```

 **Yanıt**

```json
Verification successful
{"nonce":"20181128-001617",
  "plan":
    {
     "name":"",
     "product":"",
     "publisher":""
    },
"timeStamp":
  {
    "createdOn":"11/28/18 00:16:17 -0000",
    "expiresOn":"11/28/18 06:16:17 -0000"
  },
"vmId":"d3e0e374-fda6-4649-bbc9-7f20dc379f34",
"subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"
}
```

Veriler | Açıklama
-----|------------
nonce | Kullanıcı istek ile isteğe bağlı dize sağladı. İstekte hiçbir nonce sağlanmazsa, geçerli UTC zaman damgası döndürülür
plan | Azure Market görüntüsündeki bir VM için [plan](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) yapın, ad, ürün ve yayımcı içerir
zaman damgası/createdOn | İlk imzalı belgenin oluşturulduğu zaman damgası
zaman damgası/expiresOn | İmzalanan belgenin süresi dolan zaman damgası
vmId |  VM için [benzersiz tanımlayıcı](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)
subscriptionId | `2019-04-30` tanıtılan Sanal makine için Azure aboneliği

#### <a name="verifying-the-signature"></a>İmza doğrulanıyor

Yukarıdaki imzayı aldıktan sonra, imzanın Microsoft 'tan olduğunu doğrulayabilirsiniz. Ayrıca ara sertifikayı ve sertifika zincirini doğrulayabilirsiniz. Son olarak, abonelik KIMLIĞININ doğru olduğunu doğrulayabilirsiniz.

> [!NOTE]
> Genel bulut ve bağımsız bulutu için sertifika farklı olacaktır.

 Bulut | Sertifika
---------|-----------------
[Tüm genel kullanıma açık Azure bölgeleri](https://azure.microsoft.com/regions/)     | metadata.azure.com
[Azure Devlet Kurumları](https://azure.microsoft.com/overview/clouds/government/)              | metadata.azure.us
[Azure Çin](https://azure.microsoft.com/global-infrastructure/china/)                  | metadata.azure.cn
[Azure Almanya](https://azure.microsoft.com/overview/clouds/germany/)                    | metadata.microsoftazure.de

```bash

# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

Doğrulama sırasında ağ kısıtlamalarına bağlı olarak ara sertifikanın indirilebileceği durumlarda ara sertifika sabitlenebilir. Ancak, Azure Standart PKI uygulamasına göre sertifikaların üzerine alınır. Sabitlenmiş sertifikaların, alma gerçekleşdiğinde güncellenmesi gerekir. Ara sertifikayı güncelleştirme değişikliği planlandığı zaman, Azure bloguna güncelleştirilecek ve Azure müşterileri bilgilendirilir. Ara Sertifikalar [burada](https://www.microsoft.com/pki/mscorp/cps/default.htm)bulunabilir. Her bir bölgenin ara sertifikaları farklı olabilir.

### <a name="failover-clustering-in-windows-server"></a>Windows Server 'da Yük Devretme Kümelemesi

Bazı senaryolarda, Yük Devretme Kümelemesi ile Instance Metadata Service sorgularken, yönlendirme tablosuna bir rota eklemek gereklidir.

1. Yönetici ayrıcalıklarıyla komut istemi 'ni açın.

2. Aşağıdaki komutu çalıştırın ve IPv4 yol tablosundaki ağ hedefi (`0.0.0.0`) arabiriminin adresini göz önünde edin.

```bat
route print
```

> [!NOTE] 
> Yük devretme kümesine sahip bir Windows Server VM 'sinden alınan aşağıdaki örnek çıktı, basitlik için yalnızca IPv4 yol tablosunu içerir.

```bat
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
        224.0.0.0        240.0.0.0         On-link         10.0.1.10    266
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

1. Aşağıdaki komutu çalıştırın ve bu örnekte (`10.0.1.10`) ağ hedefi için arabirimin adresini (`0.0.0.0`) kullanın.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

### <a name="custom-data"></a>Özel veriler
Instance Metadata Service, VM 'nin kendi özel verilerine erişimine sahip olmasını sağlar. İkili veriler 64 KB 'tan az olmalıdır ve Base64 kodlamalı biçimde VM 'ye sağlanmalıdır.

Azure özel verileri REST API 'Ler, PowerShell cmdlet 'Leri, Azure komut satırı arabirimi (CLı) veya ARM şablonu aracılığıyla sanal makineye eklenebilir.

Azure komut satırı arabirimi örneği için, bkz. [Microsoft Azure özel veriler ve Cloud-Init](https://azure.microsoft.com/blog/custom-data-and-cloud-init-on-windows-azure/).

ARM şablon örneği için bkz. [CustomData Ile sanal makine dağıtma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).

Özel veriler, VM 'de çalışan tüm işlemlerde kullanılabilir. Müşterilerin özel verilere gizli bilgiler eklemediğinden önerilir.

Şu anda, bir VM 'nin önyüklemesi sırasında özel verilerin kullanılabilir olduğu garanti edilir. VM 'de disk ekleme veya VM 'yi yeniden boyutlandırma gibi güncelleştirmeler yapılırsa Instance Metadata Service özel verileri sağlamacaktır. Instance Metadata Service üzerinden kalıcı olarak özel veri sağlama işlemi şu anda devam ediyor.

#### <a name="retrieving-custom-data-in-virtual-machine"></a>Sanal makinede özel veriler alınıyor
Instance Metadata Service, sanal makineye Base64 kodlamalı biçimde özel veriler sağlar. Aşağıdaki örnek Base64 kodlamalı dizesinin kodunu çözer.

> [!NOTE]
> Bu örnekteki özel veriler, "özel verilerimi" okuyan bir ASCII dizesi olarak yorumlanır.

**İstek**

```bash
curl -H "Metadata:true" "http://169.254.169.254/metadata/instance/compute/customData?api-version=2019-02-01&&format=text" | base64 --decode
```

**Yanıt**

```text
My custom data.
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>VM içinde farklı diller kullanarak meta veri hizmeti çağırma örnekleri

Dil | Örnek
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Başlayın  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata

## <a name="faq"></a>SSS

1. Hata `400 Bad Request, Required metadata header not specified`alıyorum. Bu ne anlama geliyor?
   * Instance Metadata Service, üst bilgi `Metadata: true` istekte geçirilmesini gerektirir. Bu üstbilginin REST çağrısına geçirilmesi Instance Metadata Service erişim sağlar.
2. Neden Sanal makinem için işlem bilgilerini alamıyorum?
   * Şu anda Instance Metadata Service yalnızca Azure Resource Manager oluşturulan örnekleri destekliyor. Gelecekte, bulut hizmeti VM 'Leri için destek eklenebilir.
3. Sanal makinmi Azure Resource Manager bir sırada oluşturdum. İşlem meta veri bilgilerini neden göremiyorum?
   * Sep 2016 ' den sonra oluşturulan tüm VM 'Ler için, işlem meta verilerini görmeye başlamak üzere bir [etiket](../../azure-resource-manager/resource-group-using-tags.md) ekleyin. Eski VM 'Ler için (2016. sürümden önce oluşturulan) meta verileri yenilemek için VM 'lere uzantıları veya veri diskleri ekleyin/kaldırın.
4. Yeni sürüm için doldurulmuş tüm verileri görmüyorum
   * Sep 2016 ' den sonra oluşturulan tüm VM 'Ler için, işlem meta verilerini görmeye başlamak üzere bir [etiket](../../azure-resource-manager/resource-group-using-tags.md) ekleyin. Eski VM 'Ler için (2016. sürümden önce oluşturulan) meta verileri yenilemek için VM 'lere uzantıları veya veri diskleri ekleyin/kaldırın.
5. Hata `500 Internal Server Error`neden alıyorum?
   * İsteğinizi üstel geri dönüş sistemine göre yeniden deneyin. Sorun devam ederse Azure desteği ile iletişime geçin.
6. Ek soruları/açıklamaları nerede paylaşabilirim?
   * https://feedback.azure.comyorumlarınızı gönderin.
7. Bu, sanal makine ölçek kümesi örneği için mi çalışıyor?
   * Evet meta veri hizmeti ölçek kümesi örnekleri için kullanılabilir.
8. Nasıl yaparım? hizmet için destek almak mı istiyorsunuz?
   * Hizmete yönelik destek almak için, uzun süreden sonra meta veri yanıtını geçirebileceğiniz VM için Azure portal bir destek sorunu oluşturun.
9. Hizmete yönelik çağrımın isteği zaman aşımına uğradı mı?
   * Meta veri çağrılarının, sanal makinenin ağ kartına atanan birincil IP adresinden olması gerekir. yönlendirlerinizi değiştirmenize ek olarak, ağ kartınızdan 169.254.0.0/16 adresi için bir yol olması gerekir.
10. Sanal makine ölçek kümesinde etiketlerimi güncelleştirdim, ancak VM 'lerden farklı olarak örneklerde gözükmez mi?
    * Şu anda ScaleSets etiketleri için yalnızca bir yeniden başlatma/yeniden görüntü/veya örneğe bir disk değişikliği olan sanal makineye gösterilir.

    ![Örnek meta veri desteği](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Zamanlanan olaylar](scheduled-events.md) hakkında daha fazla bilgi edinin
