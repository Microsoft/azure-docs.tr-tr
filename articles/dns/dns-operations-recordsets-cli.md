---
title: Azure CLı kullanarak Azure DNS DNS kayıtlarını yönetme
description: Etki alanınızı Azure DNS barındırırken Azure DNS DNS kayıt kümelerini ve kayıtlarını yönetme.
author: rohinkoul
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: how-to
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.workload: infrastructure-services
ms.date: 05/15/2018
ms.author: rohink
ms.openlocfilehash: 2d3989b3c477a35d602f1ccf3e45d6f597f5d78d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011582"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli"></a>Azure CLı kullanarak Azure DNS 'de DNS kayıtlarını ve kayıt kümelerini yönetme

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Bu makalede, Windows, Mac ve Linux 'ta kullanılabilen platformlar arası Azure CLı kullanarak DNS bölgeniz için DNS kayıtlarını yönetme işlemi gösterilmektedir. DNS kayıtlarınızı, [Azure PowerShell](dns-operations-recordsets.md) veya [Azure Portal](dns-operations-recordsets-portal.md)kullanarak da yönetebilirsiniz.

Bu makaledeki örneklerde [Azure CLI 'yi zaten yüklemiş, oturum açmış ve BIR DNS bölgesi oluşturduğunuz](dns-operations-dnszones-cli.md)varsayılmaktadır.

## <a name="introduction"></a>Giriş

Azure DNS’de DNS kayıtlarını oluşturmadan önce Azure DNS’nin DNS kayıtlarını DNS kayıt kümeleri şeklinde nasıl düzenlediğini kavramanız gerekir.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Azure DNS’deki DNS kayıtları hakkında daha fazla bilgi için bkz. [DNS bölgeleri ve kayıtları](dns-zones-records.md).

## <a name="create-a-dns-record"></a>DNS kaydı oluşturma

Bir DNS kaydı oluşturmak için `az network dns record-set <record-type> add-record` komutunu kullanın (burada `<record-type>` kayıt türüdür, yani a, SRV, txt vb.) Yardım için bkz `az network dns record-set --help` ..

Bir kayıt oluştururken kaynak grubu adını, bölge adını, kaynak kümesi adını, kaynak türünü ve oluşturulan kaynağın ayrıntılarını belirtmeniz gerekir. Verilen kayıt kümesi adı *göreli* bir ad olmalıdır, yani bölge adını hariç tutmalıdır.

Kayıt kümesi mevcut değilse bu komutla oluşturulur. Kayıt kümesi mevcutsa bu komut belirttiğiniz kaydı var olan kayıt kümesine ekler.

Yeni bir kayıt kümesi oluşturuluyorsa yaşam süresi (TTL) olarak varsayılan 3600 değeri kullanılır. Farklı TTLs kullanma hakkında yönergeler için bkz. [DNS kayıt kümesi oluşturma](#create-a-dns-record-set).

Aşağıdaki örnek *www* adlı A kaydını *contoso.com* bölgesinde ve *MyResourceGroup* kaynak grubu içinde oluşturur. A kaydının IP adresi *1.2.3.4* olarak belirtilmiştir.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Bölgenin tepesinde bir kayıt kümesi oluşturmak için (Bu durumda, "contoso.com"), \@ tırnak işaretleri dahil olmak üzere "" kayıt adını kullanın:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>DNS kayıt kümesi oluşturma

Yukarıdaki örneklerde, DNS kaydı var olan bir kayıt kümesine eklenmiştir ya da kayıt kümesi *örtük olarak* oluşturulmuştur. Kayıt kümesini kayıt eklemeden önce *açıkça* de oluşturabilirsiniz. Azure DNS, DNS kayıtları oluşturmadan önce DNS adını ayırmak için bir yer tutucu görevi gören ' Empty ' kayıt kümelerini destekler. Boş kayıt kümeleri Azure DNS Denetim düzlemine görünür, ancak Azure DNS ad sunucularında görünmez.

Kayıt kümeleri, komutu kullanılarak oluşturulur `az network dns record-set <record-type> create` . Yardım için bkz. `az network dns record-set <record-type> create --help`.

Kayıt kümesinin açıkça oluşturulması, [canlı yaşam süresi (TTL)](dns-zones-records.md#time-to-live) ve meta veriler gibi kayıt kümesi özelliklerini belirtmenize olanak tanır. [Kayıt kümesi meta verileri](dns-zones-records.md#tags-and-metadata) , uygulamaya özgü verileri her bir kayıt kümesiyle, anahtar-değer çiftleri olarak ilişkilendirmek için kullanılabilir.

Aşağıdaki örnek, `--ttl` parametresini kullanarak (kısa form) 60 saniyelik BIR TTL ile ' A ' türünde boş bir kayıt kümesi oluşturur `-l` :

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

Aşağıdaki örnek, parametresini kullanarak "Bölüm = Finans" ve "ortam = üretim" olmak üzere iki meta veri girişi olan bir kayıt kümesi oluşturur `--metadata` :

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

Boş bir kayıt kümesi oluşturulduktan sonra, kayıtlar `azure network dns record-set <record-type> add-record` [DNS kaydı oluşturma](#create-a-dns-record)bölümünde açıklandığı gibi kullanılarak eklenebilir.

## <a name="create-records-of-other-types"></a>Diğer türlerin kayıtlarını oluştur

' A ' kayıtlarının nasıl oluşturulacağı konusunda görüldüğü aşağıdaki örneklerde, Azure DNS tarafından desteklenen diğer kayıt türlerinin kaydının nasıl oluşturulacağı gösterilmektedir.

Kayıt verilerini belirtmek için kullanılan parametreler, kayıt türüne bağlı olarak değişiklik gösterir. Örneğin "A" türünde bir kayıt için IPv4 adresini `--ipv4-address <IPv4 address>` parametresiyle belirtirsiniz. Her bir kayıt türü için parametreler kullanılarak listelenebilir `az network dns record-set <record-type> add-record --help` .

Her durumda, tek bir kaydın nasıl oluşturulacağını göstereceğiz. Kayıt, varolan kayıt kümesine veya örtük olarak oluşturulan bir kayıt kümesine eklenir. Kayıt kümeleri oluşturma ve kayıt kümesi parametresini açıkça tanımlama hakkında daha fazla bilgi için bkz. [DNS kayıt kümesi oluşturma](#create-a-dns-record-set).

Her bir DNS bölgesi tarafından oluşturulup silindiği ve ayrı olarak oluşturulamadığı veya silinemediği için bir SOA kayıt kümesi oluşturma örneği vermeyiz. Ancak, [daha sonraki bir örnekte gösterildiği gıbı SOA de değiştirilebilir](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record"></a>AAAA kaydı oluşturma

```azurecli
az network dns record-set aaaa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-aaaa --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-an-caa-record"></a>CAA kaydı oluşturma

```azurecli
az network dns record-set caa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-caa --flags 0 --tag "issue" --value "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>CNAME kaydı oluşturma

> [!NOTE]
> DNS standartları, bir bölgenin () tepesinde CNAME kayıtlarına izin vermez `--Name "@"` veya birden fazla kayıt içeren kayıt kümelerine izin vermez.
> 
> Daha fazla bilgi için bkz. [CNAME kayıtları](dns-zones-records.md#cname-records).

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Bir MX kaydı oluşturma

Bu örnekte, \@ tepesinde BÖLGESINDE MX kaydı oluşturmak için "" kayıt kümesi adını kullanacağız (Bu durumda, "contoso.com").

```azurecli
az network dns record-set mx add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>NS kaydı oluşturma

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>PTR kaydı oluşturma

Bu durumda, ' my-arpa-zone.com ', IP aralığınızı temsil eden ARPA bölgesini temsil eder. Bu bölgedeki her PTR kaydı bu IP aralığındaki bir IP adresine karşılık gelir.  Kayıt adı ' 10 ' Bu IP aralığı içindeki IP adresinin bu kayıt tarafından temsil edildiği son sekizlisi.

```azurecli
az network dns record-set ptr add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>Bir SRV kaydı oluşturma

Bir [SRV kayıt kümesi](dns-zones-records.md#srv-records)oluştururken, kayıt kümesi adında *\_ hizmeti* ve *\_ Protokolü* belirtin. \@Tepesinde bölgesinde BIR SRV kayıt kümesi oluşturulurken kayıt kümesi adına "" eklemeniz gerekmez.

```azurecli
az network dns record-set srv add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>TXT kaydı oluşturma

Aşağıdaki örnek, bir TXT kaydının nasıl oluşturulacağını göstermektedir. TXT kayıtlarında desteklenen en fazla dize uzunluğu hakkında daha fazla bilgi için bkz. [txt kayıtları](dns-zones-records.md#txt-records).

```azurecli
az network dns record-set txt add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>Kayıt kümesi al

Varolan bir kayıt kümesini almak için kullanın `az network dns record-set <record-type> show` . Yardım için bkz. `az network dns record-set <record-type> show --help`.

Kayıt veya kayıt kümesi oluştururken, verilen kayıt kümesi adı *göreli* bir ad olmalıdır, yani bölge adını hariç tutmalıdır. Kayıt türünü, kayıt kümesini içeren bölgeyi ve bölgeyi içeren kaynak grubunu da belirtmeniz gerekir.

Aşağıdaki örnek, *Myresourcegroup* kaynak grubundaki bölge *contoso.com* From A türündeki *www* kaydını alır:

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>Kayıt kümelerini Listele

Komutunu kullanarak, bir DNS bölgesindeki tüm kayıtları listeleyebilirsiniz `az network dns record-set list` . Yardım için bkz. `az network dns record-set list --help`.

Bu örnek, ad veya kayıt türünden bağımsız olarak *Myresourcegroup* kaynak grubundaki *contoso.com* bölgesindeki tüm kayıt kümelerini döndürür:

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

Bu örnek, belirtilen kayıt türüyle eşleşen tüm kayıt kümelerini döndürür (Bu durumda, ' A ' kayıtları):

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>Mevcut bir kayıt kümesine kayıt ekleme

`az network dns record-set <record-type> add-record`Her ikisini de yeni bir kayıt kümesinde kayıt oluşturmak veya var olan bir kayıt kümesine kayıt eklemek için kullanabilirsiniz.

Daha fazla bilgi için bkz. [DNS kaydı oluşturma](#create-a-dns-record) ve yukarıdaki [diğer türlerin kayıtlarını oluşturma](#create-records-of-other-types) .

## <a name="remove-a-record-from-an-existing-record-set"></a>Varolan bir kayıt kümesinden bir kaydı kaldırın.

Varolan bir kayıt kümesinden bir DNS kaydını kaldırmak için öğesini kullanın `az network dns record-set <record-type> remove-record` . Yardım için bkz. `az network dns record-set <record-type> remove-record -h`.

Bu komut bir kayıt kümesinden bir DNS kaydını siler. Bir kayıt kümesindeki son kayıt silinirse, kayıt kümesi de silinir. Bunun yerine boş kayıt kümesini tutmak için `--keep-empty-record-set` seçeneğini kullanın.

Öğesini kullanarak bir kayıt oluştururken aynı parametreleri kullanarak, silinecek kaydı ve silinecek bölgeyi belirtmeniz gerekir `az network dns record-set <record-type> add-record` . Bu parametreler, [DNS kaydı oluşturma](#create-a-dns-record) ve yukarıdaki [diğer türlerin kayıtlarını oluşturma](#create-records-of-other-types) konularında açıklanmaktadır.

Aşağıdaki örnek, *Myresourcegroup* kaynak grubundaki *contoso.com* bölgesinde *www* adlı kayıt kümesinden ' 1.2.3.4 ' değerine sahip bir kaydı siler.

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>Varolan bir kayıt kümesini değiştirme

Her kayıt kümesi, [yaşam süresi (TTL)](dns-zones-records.md#time-to-live), [meta veri](dns-zones-records.md#tags-and-metadata)ve DNS kayıtları içerir. Aşağıdaki bölümlerde bu özelliklerden her birinin nasıl değiştirileceği açıklanmaktadır.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>A, AAAA, CAA, MX, NS, PTR, SRV veya TXT kayıtlarını değiştirmek için

A, AAAA, CAA, MX, NS, PTR, SRV veya TXT türünde varolan bir kaydı değiştirmek için, önce yeni bir kayıt eklemeli ve ardından mevcut kaydı silmelisiniz. Kayıtları silme ve ekleme hakkında ayrıntılı yönergeler için, bu makalenin önceki bölümlerine bakın.

Aşağıdaki örnek, bir ' A ' kaydının 1.2.3.4 IP adresi olan 5.6.7.8 IP adresine nasıl değiştirileceğini gösterir:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Bölge tepesinde ( `--Name "@"` tırnak işaretleri dahil) içinde otomatik olarak oluşturulan NS kayıt kümesine kayıt ekleyemez, bu kayıtları kaldıramazsınız veya değiştiremezsiniz. Bu kayıt kümesi için izin verilen tek değişiklikler, kayıt kümesi TTL ve meta verilerini değiştirmektir.

### <a name="to-modify-a-cname-record"></a>CNAME kaydını değiştirmek için

Diğer birçok kayıt türünün aksine, CNAME kayıt kümesi yalnızca tek bir kayıt içerebilir.  Bu nedenle, yeni bir kayıt ekleyerek ve var olan kaydı diğer kayıt türleri gibi kaldırarak geçerli değeri değiştiremezsiniz.

Bunun yerine, CNAME kaydını değiştirmek için kullanın `az network dns record-set cname set-record` . Yardım için bkz. `az network dns record-set cname set-record --help`

Örnek, *Myresourcegroup* kaynak grubundaki *contoso.com* *bölgesinde bulunan CNAME kayıt kümesini,* varolan değeri yerine ' www.fabrikam.net ' öğesine işaret etmek üzere değiştirir:

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Bir SOA kaydını değiştirmek için

Diğer birçok kayıt türünün aksine, CNAME kayıt kümesi yalnızca tek bir kayıt içerebilir.  Bu nedenle, yeni bir kayıt ekleyerek ve var olan kaydı diğer kayıt türleri gibi kaldırarak geçerli değeri değiştiremezsiniz.

Bunun yerine, SOA kaydını değiştirmek için kullanın `az network dns record-set soa update` . Yardım için bkz. `az network dns record-set soa update --help`.

Aşağıdaki örnekte, *Myresourcegroup* kaynak grubundaki *contoso.com* bölgesi için SOA kaydının ' email ' özelliğinin nasıl ayarlanacağı gösterilmektedir:

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Tepesinde bölgesinde NS kayıtlarını değiştirmek için

Tepesinde bölgesinde ayarlanan NS kaydı, her DNS bölgesiyle otomatik olarak oluşturulur. Bu, bölgeye atanan Azure DNS ad sunucularının adlarını içerir.

Birden fazla DNS sağlayıcısıyla ortak barındırma etki alanlarını desteklemek için bu NS kayıt kümesine ek ad sunucuları ekleyebilirsiniz. Bu kayıt kümesi için TTL ve meta verileri de değiştirebilirsiniz. Ancak, önceden doldurulmuş Azure DNS adı sunucularını kaldıramaz veya değiştiremezsiniz.

Bunun yalnızca tepesinde bölgesindeki NS kayıt kümesi için geçerli olduğunu unutmayın. Bölgesindeki diğer NS kayıt kümeleri (alt alanları temsilci atamak için kullanılan), kısıtlama olmadan değiştirilebilir.

Aşağıdaki örnek, tepesinde bölgesinde NS kayıt kümesine nasıl ek ad sunucusu ekleneceğini gösterir:

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Var olan bir kayıt kümesinin TTL 'sini değiştirmek için

Var olan bir kayıt kümesinin TTL 'sini değiştirmek için kullanın `azure network dns record-set <record-type> update` . Yardım için bkz. `azure network dns record-set <record-type> update --help`.

Aşağıdaki örnek, bu durumda 60 saniye olarak bir kayıt kümesi TTL 'sini nasıl değiştireceğiniz gösterilmektedir:

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Varolan bir kayıt kümesinin meta verilerini değiştirmek için

[Kayıt kümesi meta verileri](dns-zones-records.md#tags-and-metadata) , uygulamaya özgü verileri her bir kayıt kümesiyle, anahtar-değer çiftleri olarak ilişkilendirmek için kullanılabilir. Varolan bir kayıt kümesinin meta verilerini değiştirmek için kullanın `az network dns record-set <record-type> update` . Yardım için bkz. `az network dns record-set <record-type> update --help`.

Aşağıdaki örnek, "Bölüm = Finans" ve "ortam = üretim" olmak üzere iki meta veri girişi ile bir kayıt kümesinin nasıl değiştirileceğini gösterir. Varolan tüm meta verilerin, verilen değerlerle *değiştirildiğini* unutmayın.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>Kayıt kümesini silme

Kayıt kümeleri, komutu kullanılarak silinebilir `az network dns record-set <record-type> delete` . Yardım için bkz. `azure network dns record-set <record-type> delete --help`. Kayıt kümesi silindiğinde, kayıt kümesi içindeki tüm kayıtlar da silinir.

> [!NOTE]
> Tepesinde () bölgesinde SOA ve NS kayıt kümelerini silemezsiniz `--name "@"` .  Bunlar, bölge oluşturulduğunda otomatik olarak oluşturulur ve bölge silindiğinde otomatik olarak silinir.

Aşağıdaki örnek, *Myresourcegroup* kaynak grubundaki *contoso.com* alan içindeki *www* adlı kayıt kümesini siler:

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

Silme işlemini onaylamanız istenir. Bu istemi gizlemek için `--yes` anahtarını kullanın.

## <a name="next-steps"></a>Sonraki adımlar

[Azure DNS bölgeler ve kayıtlar](dns-zones-records.md)hakkında daha fazla bilgi edinin.
<br>
Azure DNS kullanırken [bölgelerinizi ve kayıtlarınızı nasıl koruyacağınızı](dns-protect-zones-recordsets.md) öğrenin.
