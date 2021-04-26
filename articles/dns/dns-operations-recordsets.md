---
title: Azure PowerShell kullanarak Azure DNS DNS kayıtlarını yönetme | Microsoft Docs
description: Etki alanınızı Azure DNS barındırırken Azure DNS DNS kayıt kümelerini ve kayıtlarını yönetme. Kayıt kümeleri ve kayıtları üzerinde işlemler için tüm PowerShell komutları.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: rohink
ms.openlocfilehash: f4e713f54ab4702b21763dc9fc6c7b606f94a945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011600"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Azure DNS Azure PowerShell kullanarak DNS kayıtlarını ve kayıt kümelerini yönetme

> [!div class="op_single_selector"]
> * [Azure Portalı](dns-operations-recordsets-portal.md)
> * [Azure klasik CLı](./dns-operations-recordsets-cli.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Bu makalede, DNS bölgenizin DNS kayıtlarını Azure PowerShell kullanarak nasıl yöneteceğiniz gösterilmektedir. DNS kayıtları, platformlar arası [Azure CLI](dns-operations-recordsets-cli.md) veya [Azure Portal](dns-operations-recordsets-portal.md)kullanılarak da yönetilebilir.

Bu makaledeki örneklerde [, zaten Azure PowerShell, oturum açmış ve BIR DNS bölgesi oluşturduğunuz](dns-operations-dnszones.md)varsayılmaktadır.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="introduction"></a>Giriş

Azure DNS’de DNS kayıtlarını oluşturmadan önce Azure DNS’nin DNS kayıtlarını DNS kayıt kümeleri şeklinde nasıl düzenlediğini kavramanız gerekir.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Azure DNS’deki DNS kayıtları hakkında daha fazla bilgi için bkz. [DNS bölgeleri ve kayıtları](dns-zones-records.md).


## <a name="create-a-new-dns-record"></a>Yeni bir DNS kaydı oluştur

Yeni kaydınız mevcut bir kayıtla aynı ada ve türe sahipse, [mevcut kayıt kümesine eklemeniz](#add-a-record-to-an-existing-record-set)gerekir. Yeni kaydınız farklı bir ada sahipse ve tüm mevcut kayıtlara yazarsanız, yeni bir kayıt kümesi oluşturmanız gerekir. 

### <a name="create-a-records-in-a-new-record-set"></a>Yeni bir kayıt kümesinde ' A ' kayıtlarını oluşturma

`New-AzDnsRecordSet` cmdlet’ini kullanarak kayıt kümeleri oluşturabilirsiniz. Bir kayıt kümesi oluştururken, kayıt kümesi adını, bölgeyi, yaşam süresi (TTL), kayıt türünü ve oluşturulacak kayıtları belirtmeniz gerekir.

Bir kayıt kümesine kayıt eklemeye yönelik parametreler, kayıt kümesinin türüne bağlı olarak farklılık gösterir. Örneğin, ' A ' türünde bir kayıt kümesi kullanırken, parametresini kullanarak IP adresini belirtmeniz gerekir `-IPv4Address` . Diğer parametreler diğer kayıt türleri için kullanılır. Ayrıntılar için ek kayıt türü örneklerine bakın.

Aşağıdaki örnek, ' contoso.com ' DNS bölgesinde göreli adı ' www ' olan bir kayıt kümesi oluşturur. Kayıt kümesinin tam adı ' www.contoso.com '. Kayıt türü ' A ', TTL 3600 saniyedir. Kayıt kümesi, IP adresi ' 1.2.3.4 ' olan tek bir kayıt içeriyor.

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Bir bölgenin ' tepesinde ' konumunda (Bu durumda, ' contoso.com ') bir kayıt kümesi oluşturmak için, ' ' kayıt kümesi adını kullanın \@ (tırnak işaretleri hariç):

```powershell
New-AzDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Birden fazla kayıt içeren bir kayıt kümesi oluşturmanız gerekiyorsa, önce bir yerel dizi oluşturun ve kayıtları ekleyin, sonra diziyi `New-AzDnsRecordSet` aşağıdaki gibi geçirin:

```powershell
$aRecords = @()
$aRecords += New-AzDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[Kayıt kümesi meta verileri](dns-zones-records.md#tags-and-metadata) , uygulamaya özgü verileri her bir kayıt kümesiyle, anahtar-değer çiftleri olarak ilişkilendirmek için kullanılabilir. Aşağıdaki örnek, ' bölüm = Finans ' ve ' Environment = production ' olmak üzere iki meta veri girişi ile bir kayıt kümesinin nasıl oluşturulacağını gösterir.

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

Azure DNS Ayrıca, DNS kayıtları oluşturmadan önce DNS adını ayırmak için bir yer tutucu görevi gören ' boş ' kayıt kümelerini destekler. Boş kayıt kümeleri Azure DNS Denetim düzlemine görünür, ancak Azure DNS ad sunucularında görünür. Aşağıdaki örnek boş bir kayıt kümesi oluşturur:

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Diğer türlerin kayıtlarını oluştur

' A ' kayıtlarının nasıl oluşturulacağı konusunda görüldüğü aşağıdaki örneklerde, Azure DNS tarafından desteklenen diğer kayıt türlerinin kayıtlarının nasıl oluşturulacağı gösterilmektedir.

Her durumda, tek bir kayıt içeren bir kayıt kümesi oluşturmayı gösteririz. ' A ' kayıtları için önceki örnekler, birden çok kayıt içeren diğer türlerin kayıt kümelerini oluşturmak için veya meta verilerle birlikte boş kayıt kümeleri oluşturmak için uyarlanmıştır.

Her bir DNS bölgesi tarafından oluşturulup silindiği ve ayrı olarak oluşturulamadığı veya silinemediği için bir SOA kayıt kümesi oluşturma örneği vermeyiz. Ancak, [daha sonraki bir örnekte gösterildiği gıbı SOA de değiştirilebilir](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Tek kayıtlı bir AAAA kayıt kümesi oluşturma

```powershell
New-AzDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Tek kayıtlı bir CAA kayıt kümesi oluşturma

```powershell
New-AzDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Tek kayıtlı bir CNAME kayıt kümesi oluşturma

> [!NOTE]
> DNS standartları, bir bölgenin () tepesinde CNAME kayıtlarına izin vermez `-Name '@'` veya birden fazla kayıt içeren kayıt kümelerine izin vermez.
> 
> Daha fazla bilgi için bkz. [CNAME kayıtları](dns-zones-records.md#cname-records).


```powershell
New-AzDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Tek kayıtlı bir MX kayıt kümesi oluşturma

Bu örnekte, \@ tepesinde bölgesinde (Bu durumda, ' contoso.com ') BIR MX kaydı oluşturmak için ' ' kayıt kümesi adını kullanacağız.


```powershell
New-AzDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Tek kayıtlı bir NS kayıt kümesi oluşturma

```powershell
New-AzDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Tek kayıtlı bir PTR kaydı kümesi oluşturma

Bu durumda, ' my-arpa-zone.com ', IP aralığınızı temsil eden ARPA geriye doğru arama bölgesini temsil eder. Bu bölgedeki her PTR kaydı bu IP aralığındaki bir IP adresine karşılık gelir. Kayıt adı ' 10 ' Bu IP aralığı içindeki IP adresinin bu kayıt tarafından temsil edildiği son sekizlisi.

```powershell
New-AzDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Tek kayıtlı bir SRV kayıt kümesi oluşturma

Bir [SRV kayıt kümesi](dns-zones-records.md#srv-records)oluştururken, kayıt kümesi adında *\_ hizmeti* ve *\_ Protokolü* belirtin. \@Tepesinde bölgesinde BIR SRV kayıt kümesi oluştururken, kayıt kümesi adına ' ' eklemeniz gerekmez.

```powershell
New-AzDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Tek bir kayıtla bir TXT kayıt kümesi oluşturma

Aşağıdaki örnek, bir TXT kaydının nasıl oluşturulacağını göstermektedir. TXT kayıtlarında desteklenen en fazla dize uzunluğu hakkında daha fazla bilgi için bkz. [txt kayıtları](dns-zones-records.md#txt-records).

```powershell
New-AzDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Kayıt kümesi al

Varolan bir kayıt kümesini almak için kullanın `Get-AzDnsRecordSet` . Bu cmdlet Azure DNS içindeki kayıt kümesini temsil eden yerel bir nesne döndürür.

İle olduğu gibi `New-AzDnsRecordSet` , verilen kayıt kümesi adı *göreli* bir ad olmalıdır, yani bölge adını hariç tutmalıdır. Kayıt türünü ve kayıt kümesini içeren bölgeyi de belirtmeniz gerekir.

Aşağıdaki örnek, bir kayıt kümesinin nasıl alınacağını gösterir. Bu örnekte, bölgesi `-ZoneName` ve parametreleri kullanılarak belirtilir `-ResourceGroupName` .

```powershell
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternatif olarak, bir bölge nesnesi kullanarak, parametresini kullanarak da bölgeyi belirtebilirsiniz `-Zone` .

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Kayıt kümelerini Listele

Ayrıca, `Get-AzDnsZone` `-Name` ve/veya parametrelerini atlayarak, bir bölgedeki kayıt kümelerini listelemek için ' yi kullanabilirsiniz `-RecordType` .

Aşağıdaki örnek, bölgesindeki tüm kayıt kümelerini döndürür:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Aşağıdaki örnek, kayıt kümesi adı hariç, kayıt türü belirtilerek belirli bir türün tüm kayıt kümelerinin nasıl alınamayacağını gösterir:

```powershell
$recordsets = Get-AzDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Belirli bir ada sahip tüm kayıt kümelerini kayıt türleri arasında almak için, tüm kayıt kümelerini almanız ve ardından sonuçları filtrelemeniz gerekir:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

Yukarıdaki örneklerde, bölge `-ZoneName` ve `-ResourceGroupName` parametreleri kullanılarak (gösterildiği gibi) veya bir bölge nesnesi belirtilerek belirtilebilir:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Mevcut bir kayıt kümesine kayıt ekleme

Varolan bir kayıt kümesine kayıt eklemek için aşağıdaki üç adımı izleyin:

1. Mevcut kayıt kümesini al

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Yeni kaydı yerel kayıt kümesine ekleyin. Bu, çevrimdışı bir işlemdir.

    ```powershell
    Add-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Değişikliği Azure DNS hizmetine geri yürütün. 

    ```powershell
    Set-AzDnsRecordSet -RecordSet $rs
    ```

Kullanmak, `Set-AzDnsRecordSet` Azure DNS (ve içerdiği tüm kayıtlar) içindeki mevcut kayıt kümesini belirtilen kayıt kümesiyle *değiştirir* . [ETag denetimleri](dns-zones-records.md#etags) , eşzamanlı değişikliklerin üzerine yazılmadığından emin olmak için kullanılır. `-Overwrite`Bu denetimleri bastırmak için isteğe bağlı anahtarı kullanabilirsiniz.

Bu işlem dizisi de *Ayrıca, bir parametre olarak geçirilmesi yerine* kanal kullanarak kayıt kümesi nesnesini geçirdiğiniz anlamına gelir.

```powershell
Get-AzDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Yukarıdaki örneklerde, ' a ' türünde varolan bir kayıt kümesine ' A ' kaydının nasıl ekleneceği gösterilmektedir. Başka türdeki kayıt kümelerine kayıt eklemek için benzer bir işlem dizisi kullanılır ve `-Ipv4Address` parametresi `Add-AzDnsRecordConfig` her bir kayıt türüne özgü diğer parametrelerle birlikte bulunur. Her bir kayıt türü için parametreler, `New-AzDnsRecordConfig` Yukarıdaki ek kayıt türü örneklerinde gösterildiği gibi cmdlet 'i ile aynıdır.

' CNAME ' veya ' SOA ' türündeki kayıt kümeleri birden fazla kayıt içeremez. Bu kısıtlama, DNS standartlarından oluşur. Azure DNS sınırlaması değildir.

## <a name="remove-a-record-from-an-existing-record-set"></a>Varolan bir kayıt kümesinden kayıt kaldırma

Kayıt kümesinden bir kaydı kaldırma işlemi, var olan bir kayıt kümesine kayıt eklemek için işleme benzerdir:

1. Mevcut kayıt kümesini al

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Kaydı yerel kayıt kümesi nesnesinden kaldırın. Bu, çevrimdışı bir işlemdir. Kaldırılmakta olan kayıt tüm parametrelerde mevcut bir kayıtla tam eşleşme olmalıdır.

    ```powershell
    Remove-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Değişikliği Azure DNS hizmetine geri yürütün. `-Overwrite`Eş zamanlı değişikliklere yönelik [ETag denetimlerini](dns-zones-records.md#etags) gizlemek için isteğe bağlı anahtarı kullanın.

    ```powershell
    Set-AzDnsRecordSet -RecordSet $Rs
    ```

Bir kayıt kümesinden son kaydı kaldırmak için yukarıdaki sıranın kullanılması, kayıt kümesini silmez, bunun yerine boş bir kayıt kümesi bırakır. Kayıt kümesini tamamen kaldırmak için bkz. [kayıt kümesini silme](#delete-a-record-set).

Kayıt kümesine kayıt eklemek için benzer şekilde, bir kayıt kümesini kaldırmak için işlem sırası da çıkartılmış olabilir:

```powershell
Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Farklı kayıt türleri, uygun türe özgü parametreleri öğesine geçirerek desteklenir `Remove-AzDnsRecordSet` . Her bir kayıt türü için parametreler, `New-AzDnsRecordConfig` Yukarıdaki ek kayıt türü örneklerinde gösterildiği gibi cmdlet 'i ile aynıdır.


## <a name="modify-an-existing-record-set"></a>Varolan bir kayıt kümesini değiştirme

Varolan bir kayıt kümesini değiştirme adımları, kayıt kümesinden kayıtları eklerken veya kaldırırken aldığınız adımlara benzerdir:

1. Kullanarak varolan kayıt kümesini alın `Get-AzDnsRecordSet` .
2. Yerel kayıt kümesi nesnesini şu şekilde değiştirin:
    * Kayıt ekleme veya kaldırma
    * Mevcut kayıtların parametrelerini değiştirme
    * Kayıt kümesi meta verilerini ve yaşam süresini (TTL) değiştirme
3. Cmdlet 'ini kullanarak yaptığınız değişiklikleri işleyin `Set-AzDnsRecordSet` . Bu, Azure DNS içindeki mevcut kayıt kümesini belirtilen kayıt kümesiyle *değiştirir* .

Kullanırken `Set-AzDnsRecordSet` , eş zamanlı değişikliklerin üzerine yazılmadığından emin olmak için [ETag denetimleri](dns-zones-records.md#etags) kullanılır. `-Overwrite`Bu denetimleri bastırmak için isteğe bağlı anahtarı kullanabilirsiniz.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Var olan bir kayıt kümesindeki bir kaydı güncelleştirmek için

Bu örnekte, var olan bir ' A ' kaydının IP adresini değiştiririz:

```powershell
$rs = Get-AzDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Bir SOA kaydını değiştirmek için

Tepesinde bölgesinde ( `-Name "@"` tırnak işaretleri dahil) otomatik olarak oluşturulan SOA kayıt kümesine kayıt ekleyemez veya buradan kayıt kaldıramazsınız. Bununla birlikte, SOA kaydındaki parametrelerden herhangi birini ("ana bilgisayar" hariç) ve kayıt kümesi TTL 'sini de değiştirebilirsiniz.

Aşağıdaki örnekte, SOA kaydının *e-posta* özelliğinin nasıl değiştirileceği gösterilmektedir:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Tepesinde bölgesinde NS kayıtlarını değiştirmek için

Tepesinde bölgesinde ayarlanan NS kaydı, her DNS bölgesiyle otomatik olarak oluşturulur. Bu, bölgeye atanan Azure DNS ad sunucularının adlarını içerir.

Birden fazla DNS sağlayıcısıyla ortak barındırma etki alanlarını desteklemek için bu NS kayıt kümesine ek ad sunucuları ekleyebilirsiniz. Bu kayıt kümesi için TTL ve meta verileri de değiştirebilirsiniz. Ancak, önceden doldurulmuş Azure DNS adı sunucularını kaldıramaz veya değiştiremezsiniz.

Bunun yalnızca tepesinde bölgesindeki NS kayıt kümesi için geçerli olduğunu unutmayın. Bölgesindeki diğer NS kayıt kümeleri (alt alanları temsilci atamak için kullanılan), kısıtlama olmadan değiştirilebilir.

Aşağıdaki örnek, tepesinde bölgesinde NS kayıt kümesine nasıl ek ad sunucusu ekleneceğini gösterir:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Kayıt kümesi meta verilerini değiştirmek için

[Kayıt kümesi meta verileri](dns-zones-records.md#tags-and-metadata) , uygulamaya özgü verileri her bir kayıt kümesiyle, anahtar-değer çiftleri olarak ilişkilendirmek için kullanılabilir.

Aşağıdaki örnek, var olan bir kayıt kümesinin meta verilerinin nasıl değiştirileceğini göstermektedir:

```powershell
# Get the record set
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"

# Add 'dept=finance' name-value pair
$rs.Metadata.Add('dept', 'finance') 

# Remove metadata item named 'environment'
$rs.Metadata.Remove('environment')  

# Commit changes
Set-AzDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>Kayıt kümesini silme

Kayıt kümeleri cmdlet kullanılarak silinebilir `Remove-AzDnsRecordSet` . Kayıt kümesi silindiğinde, kayıt kümesi içindeki tüm kayıtlar da silinir.

> [!NOTE]
> Tepesinde () bölgesinde SOA ve NS kayıt kümelerini silemezsiniz `-Name '@'` .  Azure DNS bölge oluşturulduğunda bu otomatik olarak oluşturulur ve bölge silindiğinde onları otomatik olarak siler.

Aşağıdaki örnek, bir kayıt kümesinin nasıl silineceğini gösterir. Bu örnekte, kayıt kümesi adı, kayıt kümesi türü, bölge adı ve kaynak grubu her ikisi açıkça belirtilmiştir.

```powershell
Remove-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternatif olarak, kayıt kümesi ad ve tür ile belirtilebilir ve bir nesnesi kullanılarak belirtilen bölge:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Üçüncü bir seçenek olarak, kayıt kümesi kendisi bir kayıt kümesi nesnesi kullanılarak belirtilebilir:

```powershell
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -RecordSet $rs
```

Kayıt kümesi nesnesi kullanılarak silinecek kayıt kümesini belirttiğinizde, eşzamanlı değişikliklerin silinmediğinden emin olmak için [ETag denetimleri](dns-zones-records.md#etags) kullanılır. `-Overwrite`Bu denetimleri bastırmak için isteğe bağlı anahtarı kullanabilirsiniz.

Kayıt kümesi nesnesi de bir parametre olarak geçirilmesi yerine, bir de olabilir:

```powershell
Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzDnsRecordSet
```

## <a name="confirmation-prompts"></a>Onay istemleri

`New-AzDnsRecordSet`, `Set-AzDnsRecordSet` ve `Remove-AzDnsRecordSet` cmdlet’lerinin tamamı onay istemlerini destekler.

`$ConfirmPreference`PowerShell tercih değişkeninin değeri veya daha düşükse, her cmdlet onay ister `Medium` . Varsayılan değeri `$ConfirmPreference` olduğundan `High` , varsayılan PowerShell ayarları kullanılırken bu istemler verilmez.

`-Confirm` parametresini kullanarak geçerli `$ConfirmPreference` ayarını geçersiz kılabilirsiniz. `-Confirm` veya `-Confirm:$True` belirtirseniz cmdlet, çalıştırılmadan önce size onay istemi görüntüler. `-Confirm:$False` belirtirseniz cmdlet size onay istemi görüntülemez. 

`-Confirm` ve `$ConfirmPreference` hakkında daha fazla bilgi için bkz. [Tercih Değişkenleri Hakkında](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Sonraki adımlar

[Azure DNS bölgeler ve kayıtlar](dns-zones-records.md)hakkında daha fazla bilgi edinin.
<br>
Azure DNS kullanırken [bölgelerinizi ve kayıtlarınızı nasıl koruyacağınızı](dns-protect-zones-recordsets.md) öğrenin.
<br>
[PowerShell başvuru belgelerini Azure DNS](/powershell/module/az.dns)inceleyin.