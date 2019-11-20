---
title: .NET SDK kullanarak DNS bölgeleri ve kayıt kümeleri oluşturma
titleSuffix: Azure DNS
description: .NET SDK kullanarak Azure DNS ' de DNS bölgeleri ve kayıt kümeleri oluşturma.
services: dns
documentationcenter: na
author: asudbring
manager: kumudD
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: allensu
ms.openlocfilehash: b51dd4ea3b36a9d0420a60883ebc29276a7d6b8a
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076717"
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>.NET SDK kullanarak DNS bölgeleri ve kayıt kümeleri oluşturma

.NET DNS Yönetim Kitaplığı ile DNS SDK 'sını kullanarak DNS bölgelerini, kayıt kümelerini ve kayıtları oluşturma, silme veya güncelleştirme işlemlerini otomatik hale getirebilirsiniz. Tam bir Visual Studio projesi burada bulunabilir [.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Hizmet sorumlusu hesabı oluşturma

Genellikle, Azure kaynaklarına programlı erişim, kendi Kullanıcı kimlik bilgileriniz yerine adanmış bir hesap aracılığıyla verilir. Bu adanmış hesaplara ' hizmet sorumlusu ' hesapları denir. Azure DNS SDK örnek projesini kullanmak için, önce bir hizmet sorumlusu hesabı oluşturmanız ve doğru izinleri atamanız gerekir.

1. Bir hizmet sorumlusu hesabı oluşturmak için [Bu yönergeleri](../active-directory/develop/howto-authenticate-service-principal-powershell.md) izleyin (Azure DNS SDK örnek projesi parola tabanlı kimlik doğrulamasını varsayar.)
2. Bir kaynak grubu oluşturun ([burada](../azure-resource-manager/resource-group-template-deploy-portal.md)).
3. Kaynak grubuna hizmet sorumlusu hesabına ' DNS bölgesi katılımcısı ' izinleri vermek için Azure RBAC kullanın ([nasıl yapılır](../role-based-access-control/role-assignments-portal.md).)
4. Azure DNS SDK örnek projesi kullanıyorsanız, ' program. cs ' dosyasını aşağıdaki gibi düzenleyin:

   * `tenantId`, `clientId` (Hesap KIMLIĞI olarak da bilinir), `secret` (hizmet sorumlusu hesap parolası) ve `subscriptionId` adım 1 ' de kullanılan doğru değerleri ekleyin.
   * 2\. adımda seçilen kaynak grubu adını girin.
   * Tercih ettiğiniz bir DNS bölgesi adı girin.

## <a name="nuget-packages-and-namespace-declarations"></a>NuGet paketleri ve ad alanı bildirimleri

Azure DNS .NET SDK 'sını kullanmak için **Azure DNS Yönetim Kitaplığı** NuGet paketini ve diğer gerekli Azure paketlerini yüklemeniz gerekir.

1. **Visual Studio**'da bir proje veya yeni proje açın.
2. **NuGet paket yöneticisi** **>** **Araçlar** ' a gidin **>** **çözüm için NuGet paketlerini yönetin...** .
3. **Araştır**' a tıklayın, **ön sürümü dahil et** onay kutusunu etkinleştirin ve arama kutusuna **Microsoft. Azure. Management. DNS** yazın.
4. Paketi seçin ve Visual Studio projenize eklemek için **yüklemek** üzere tıklayın.
5. Aşağıdaki paketleri de yüklemek için yukarıdaki işlemi tekrarlayın: **Microsoft. Rest. ClientRuntime. Azure. Authentication** ve **Microsoft. Azure. Management. ResourceManager**.

## <a name="add-namespace-declarations"></a>Ad alanı bildirimleri ekleme

Aşağıdaki ad alanı bildirimlerini ekleyin

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>DNS yönetim istemcisini başlatma

`DnsManagementClient`, DNS bölgelerini ve kayıt kümelerini yönetmek için gereken yöntemleri ve özellikleri içerir.  Aşağıdaki kod, hizmet sorumlusu hesabına kaydedilir ve bir `DnsManagementClient` nesnesi oluşturur.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>DNS bölgesi oluşturma veya güncelleştirme

DNS bölgesi oluşturmak için ilk olarak DNS bölgesi parametrelerini içeren bir "bölge" nesnesi oluşturulur. DNS bölgeleri belirli bir bölgeye bağlı olmadığından, konum ' Global ' olarak ayarlanır. Bu örnekte, bölgesine bir [Azure Resource Manager ' Tag '](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) de eklenir.

Azure DNS bölgeyi gerçekten oluşturmak veya güncelleştirmek için, bölge parametrelerini içeren bölge nesnesi `DnsManagementClient.Zones.CreateOrUpdateAsyc` yöntemine geçirilir.

> [!NOTE]
> DnsManagementClient üç işlem modunu destekler: zaman uyumlu (' CreateOrUpdate '), zaman uyumsuz (' CreateOrUpdateAsync ') veya HTTP yanıtına erişimi olan zaman uyumsuz (' CreateOrUpdateWithHttpMessagesAsync ').  Uygulama gereksinimlerinize bağlı olarak bu modlardan herhangi birini seçebilirsiniz.

Azure DNS, [ETags](dns-getstarted-create-dnszone.md)adlı iyimser eşzamanlılığı destekler. Bu örnekte, ' If-None-Match ' üst bilgisi için "*" belirtildiğinde, bir tane yoksa bir DNS bölgesi oluşturmak Azure DNS söyler.  Verilen kaynak grubunda verilen ada sahip bir bölge zaten varsa, çağrı başarısız olur.

```cs
// Create zone parameters
var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"

// Create an Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
dnsZoneParams.Tags = new Dictionary<string, string>();
dnsZoneParams.Tags.Add("dept", "finance");

// Create the actual zone.
// Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
// Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
// Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");
```

## <a name="create-dns-record-sets-and-records"></a>DNS kayıt kümeleri ve kayıtları oluşturma

DNS kayıtları bir kayıt kümesi olarak yönetilir. Kayıt kümesi, bir bölge içindeki aynı ada ve kayıt türüne sahip bir kayıt kümesidir.  Kayıt kümesi adı, tam DNS adı değil, bölge adı ile ilişkilidir.

Bir kayıt kümesi oluşturmak veya güncelleştirmek için, bir "kayıt kümesi" parametreler nesnesi oluşturulup `DnsManagementClient.RecordSets.CreateOrUpdateAsync`geçirilir. DNS bölgelerinde olduğu gibi, üç işlem modu vardır: zaman uyumlu (' CreateOrUpdate '), zaman uyumsuz (' CreateOrUpdateAsync ') veya HTTP yanıtına erişimi olan zaman uyumsuz (' CreateOrUpdateWithHttpMessagesAsync ').

DNS bölgelerinde olduğu gibi, kayıt kümelerindeki işlemler iyimser eşzamanlılık desteğini içerir.  Bu örnekte, ' IF-Match ' veya ' If-None-Match ' belirtilmemişse, kayıt kümesi her zaman oluşturulur.  Bu çağrı, bu DNS bölgesindeki aynı ada ve kayıt türüne sahip olan tüm mevcut kayıt kümesini geçersiz kılar.

```cs
// Create record set parameters
var recordSetParams = new RecordSet();
recordSetParams.TTL = 3600;

// Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
recordSetParams.ARecords = new List<ARecord>();
recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

// Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
recordSetParams.Metadata = new Dictionary<string, string>();
recordSetParams.Metadata.Add("user", "Mary");

// Create the actual record set in Azure DNS
// Note: no ETAG checks specified, will overwrite existing record set if one exists
var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);
```

## <a name="get-zones-and-record-sets"></a>Bölgeleri ve kayıt kümelerini al

`DnsManagementClient.Zones.Get` ve `DnsManagementClient.RecordSets.Get` yöntemleri sırasıyla tek tek bölgeleri ve kayıt kümelerini alır. Kayıt kümeleri, türü, adı ve içerdikleri bölge ve kaynak grubu tarafından tanımlanır. Bölgeler, adı ve içerdikleri kaynak grubu tarafından tanımlanır.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Mevcut bir kayıt kümesini güncelleştirme

Var olan bir DNS kayıt kümesini güncelleştirmek için, önce kayıt kümesini alın, sonra kayıt kümesi içeriğini güncelleştirin ve ardından değişikliği iletin.  Bu örnekte, ' IF-Match ' parametresinde alınan kayıt kümesinden ' ETag ' öğesini belirttik. Eşzamanlı bir işlem, bu sırada kayıt kümesini değiştirdiyseniz çağrı başarısız olur.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Bölgeleri ve kayıt kümelerini listeleme

Bölgeleri listelemek için, belirli bir kaynak grubundaki tüm bölgeleri veya belirli bir Azure aboneliğindeki tüm bölgeleri (kaynak grupları arasında) listelemeyi destekleyen *Dnsmanagementclient. Zones. List...* yöntemlerini kullanın. Kayıt kümelerini listelemek için, belirli bir bölgedeki tüm kayıt kümelerini veya yalnızca belirli bir türdeki kayıt kümelerini listelemeyi destekleyen *Dnsmanagementclient. Recordset. List...* Methods öğesini kullanın.

Sonuçlar ve kayıt kümeleri listelendiğinde sonuçların sayfalandırılmış olabileceğini unutmayın.  Aşağıdaki örnek, sonuçların sayfalarında nasıl yineleme yapılacağını gösterir. (Yapay küçük sayfa boyutu ' 2 ', sayfalama zorlamak için kullanılır; uygulamada bu parametre atlanmalı ve varsayılan sayfa boyutu kullanılmalıdır.)

```cs
// Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
// In practice, to improve performance you would use a large page size or just use the system default
int recordSets = 0;
var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
recordSets += page.Count();

while (page.NextPageLink != null)
{
    page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
    recordSets += page.Count();
}
```

## <a name="next-steps"></a>Sonraki adımlar

Diğer DNS kayıt türlerine yönelik örnekler de dahil olmak üzere Azure DNS .NET SDK 'sını kullanma hakkında daha fazla örnek içeren [Azure DNS .NET SDK örnek projesini](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)indirin.
