---
title: Etki alanı bölge dosyasını içeri ve dışarı aktarma-Azure CLı
titleSuffix: Azure DNS
description: Azure CLı kullanarak Azure DNS bir DNS bölge dosyasını içeri ve dışarı aktarmayı öğrenin
services: dns
author: rohinkoul
ms.service: dns
ms.date: 7/30/2020
ms.author: rohink
ms.topic: how-to
ms.openlocfilehash: e2b998432f6c4417da0242d86347ed43acb5071a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94968239"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Azure CLI’yı kullanarak DNS bölge dosyasını içeri ve dışarı aktarma

Bu makalede, Azure CLı kullanarak Azure DNS için DNS bölge dosyalarını içeri ve dışarı aktarma işlemi adım adım açıklanmaktadır.

## <a name="introduction-to-dns-zone-migration"></a>DNS bölgesi geçişine giriş

DNS bölge dosyası, bölgedeki her etki alanı adı sistemi (DNS) kaydının ayrıntılarını içeren bir metin dosyasıdır. Standart bir biçimi izler ve bu, DNS kayıtlarını DNS sistemleri arasında aktarmaya uygun hale getirir. Bölge dosyası kullanmak, bir DNS bölgesini Azure DNS içine veya dışına aktarmaya yönelik hızlı, güvenilir ve kullanışlı bir yoldur.

Azure DNS, Azure komut satırı arabirimi 'ni (CLı) kullanarak bölge dosyalarını içeri ve dışarı aktarmayı destekler. Bölge dosyası içeri aktarma **Şu anda Azure PowerShell** veya Azure Portal aracılığıyla desteklenmiyor.

Azure CLı, Azure hizmetlerini yönetmek için kullanılan platformlar arası bir komut satırı aracıdır. [Azure İndirmeleri sayfasından](https://azure.microsoft.com/downloads/)Windows, Mac ve Linux platformları için kullanılabilir. Platformlar arası destek, bölge dosyalarını içeri ve dışarı aktarma için önemlidir, çünkü en yaygın ad sunucusu yazılımı, [bağlama](https://www.isc.org/downloads/bind/)genellikle Linux üzerinde çalışır.

## <a name="obtain-your-existing-dns-zone-file"></a>Mevcut DNS bölge dosyanızı edinin

Azure DNS bir DNS bölge dosyasını içeri aktarmadan önce, bölge dosyasının bir kopyasını edinmeniz gerekir. Bu dosyanın kaynağı, DNS bölgesinin Şu anda barındırıldığı yere bağlıdır.

* DNS bölgeniz bir iş ortağı hizmeti (örneğin, bir etki alanı kaydedici, adanmış DNS barındırma sağlayıcısı veya alternatif bulut sağlayıcısı) tarafından barındırılıyorsa, bu hizmet DNS bölge dosyasını indirme yeteneğini sağlamalıdır.
* DNS bölgeniz Windows DNS üzerinde barındırılıyorsa, bölge dosyaları için varsayılan klasör **%SystemRoot%\System32\Dns** olur. Her bölge dosyasının tam yolu, DNS konsolunun **genel** sekmesinde de görüntülenir.
* DNS bölgeniz BIND kullanılarak barındırılıyorsa, her bir bölgenin bölge dosyasının konumu **. conf ADLı** bağlama yapılandırma dosyasında belirtilir.

## <a name="import-a-dns-zone-file-into-azure-dns"></a>DNS bölge dosyasını Azure DNS içine aktarma

Bir bölge dosyasının içe aktarılması, zaten mevcut değilse Azure DNS yeni bir bölge oluşturur. Bölge zaten varsa, bölge dosyasındaki kayıt kümelerinin varolan kayıt kümeleriyle birleştirilmesi gerekir.

### <a name="merge-behavior"></a>Birleştirme davranışı

* Varsayılan olarak, mevcut ve yeni kayıt kümeleri birleştirilir. Birleştirilmiş bir kayıt kümesi içindeki özdeş kayıtlar de tekrarlanmış.
* Kayıt kümeleri birleştirildiğinde, önceden var olan kayıt kümelerinin yaşam süresi (TTL) kullanılır.
* Yetki başlangıcı (SOA) parametreleri (hariç `host` ), her zaman içeri aktarılan bölge dosyasından alınır. Benzer şekilde, bölge tepesinde için ayarlanan ad sunucusu kaydı için, TTL her zaman içeri aktarılan bölge dosyasından alınır.
* İçeri aktarılan bir CNAME kaydı, var olan bir CNAME kaydının aynı adla yerini almaz.  
* Bir CNAME kaydı ve aynı ada sahip başka bir kayıt arasında bir çakışma ortaya çıkarsa (hangisi var veya yeni olduğunda), varolan kayıt tutulur. 

### <a name="additional-information-about-importing"></a>Alma hakkında ek bilgiler

Aşağıdaki notlar bölge içeri aktarma işlemiyle ilgili ek teknik ayrıntılar sağlar.

* `$TTL`Yönergesi isteğe bağlıdır ve desteklenir. Hiçbir `$TTL` yönerge verilmezse, açık TTL 'si olmayan kayıtlar, varsayılan TTL olan 3600 saniyeye ayarlanır. Aynı kayıt kümesindeki iki kayıt farklı TTLs belirttiğinizde, alt değer kullanılır.
* `$ORIGIN`Yönergesi isteğe bağlıdır ve desteklenir. Hiçbir değer `$ORIGIN` ayarlandığında, kullanılan varsayılan değer, komut satırında belirtilen bölge adıdır (artı ".").
* `$INCLUDE`Ve `$GENERATE` yönergeleri desteklenmez.
* Bu kayıt türleri desteklenir: A, AAAA, CAA, CNAME, MX, NS, SOA, SRV ve TXT.
* SOA kaydı, bir bölge oluşturulduğunda Azure DNS tarafından otomatik olarak oluşturulur. Bir bölge dosyasını içeri aktardığınızda, tüm SOA parametreleri parametre *dışında* bölge dosyasından alınır `host` . Bu parametre Azure DNS tarafından belirtilen değeri kullanır. Bunun nedeni, bu parametrenin Azure DNS tarafından belirtilen birincil ad sunucusuna başvurması olması.
* Bölge tepesinde ' de ayarlanan ad sunucusu kaydı, bölge oluşturulduğunda Azure DNS tarafından otomatik olarak oluşturulur. Yalnızca bu kayıt kümesinin TTL değeri içeri aktarılır. Bu kayıtlar Azure DNS tarafından belirtilen ad sunucusu adlarını içerir. Kayıt verilerinin, içeri aktarılan bölge dosyasında bulunan değerlerle üzerine yazılmaz.
* Genel Önizleme sırasında Azure DNS yalnızca tek dizeli TXT kayıtlarını destekler. Çok dizeli TXT kayıtları, 255 karakter ile birleştirilir ve kesilir.

### <a name="cli-format-and-values"></a>CLı biçimi ve değerleri

DNS bölgesini içeri aktarmaya yönelik Azure CLı komutunun biçimi:

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Değerler:

* `<resource group>` Azure DNS içindeki bölge için kaynak grubunun adıdır.
* `<zone name>` , bölgenin adıdır.
* `<zone file name>` İçeri aktarılacak bölge dosyasının yolu/adı.

Kaynak grubunda bu ada sahip bir bölge yoksa, sizin için oluşturulur. Bölge zaten varsa, içeri aktarılan kayıt kümeleri varolan kayıt kümeleriyle birleştirilir. 

### <a name="step-1-import-a-zone-file"></a>Adım 1. Bölge dosyasını içeri aktarma

Bölge **contoso.com** için bir bölge dosyasını içeri aktarma.

1. Henüz bir tane yoksa, bir Kaynak Yöneticisi kaynak grubu oluşturmanız gerekir.

    ```azurecli
    az group create --resource-group myresourcegroup -l westeurope
    ```

2. **Contoso.com** bölgesini dosya **contoso.com.txt** **myresourcegroup** kaynak grubundaki yeni bir DNS bölgesine aktarmak için komutunu çalıştırın `az network dns zone import` .<BR>Bu komut, bölge dosyasını yükler ve ayrıştırır. Komutu, bölgeyi ve bölgedeki tüm kayıt kümelerini oluşturmak için Azure DNS hizmetinde bir dizi komut yürütür. Komut, ilerleme durumunu, hata veya uyarılarla birlikte konsol penceresinde bildirir. Kayıt kümeleri seri halinde oluşturulduğundan, büyük bir bölge dosyasının içe aktarılması birkaç dakika sürebilir.

    ```azurecli
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>Adım 2. Bölgeyi doğrulama

Dosyayı içeri aktardıktan sonra DNS bölgesini doğrulamak için aşağıdaki yöntemlerden birini kullanabilirsiniz:

* Aşağıdaki Azure CLı komutunu kullanarak kayıtları listeleyebilirsiniz:

    ```azurecli
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* Azure CLı komutunu kullanarak kayıtları listeleyebilirsiniz `az network dns record-set ns list` .
* `nslookup`Kayıtlar için ad çözümlemesini doğrulamak üzere ' i kullanabilirsiniz. Bölge henüz temsilci olmadığından, doğru Azure DNS ad sunucularını açıkça belirtmeniz gerekir. Aşağıdaki örnek, bölgeye atanan ad sunucusu adlarının nasıl alınacağını gösterir. Bu Ayrıca, kullanarak "www" kaydının nasıl sorgulanalınacağını gösterir `nslookup` .

    ```azurecli
    az network dns record-set ns list -g myresourcegroup -z contoso.com  --output json 
    ```

    ```json
    [
      {
       .......
       "name": "@",
        "nsRecords": [
          {
            "additionalProperties": {},
            "nsdname": "ns1-03.azure-dns.com."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns2-03.azure-dns.net."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns3-03.azure-dns.org."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns4-03.azure-dns.info."
          }
        ],
        "resourceGroup": "myresourcegroup",
        "ttl": 86400,
        "type": "Microsoft.Network/dnszones/NS"
      }
    ]
    ```

    ```cmd
    nslookup www.contoso.com ns1-03.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221
    ```

### <a name="step-3-update-dns-delegation"></a>3. Adım DNS temsilcisini Güncelleştir

Bölgenin doğru bir şekilde içeri aktarıldığını doğruladıktan sonra, DNS temsilcisini Azure DNS ad sunucularına işaret etmek üzere güncelleştirmeniz gerekir. Daha fazla bilgi için [DNS temsilcisini güncelleştirme](dns-domain-delegation.md)makalesine bakın.

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Azure DNS bir DNS bölge dosyasını dışarı aktarma

DNS bölgesini dışarı aktarmak için Azure CLı komutunun biçimi:

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Değerler:

* `<resource group>` Azure DNS içindeki bölge için kaynak grubunun adıdır.
* `<zone name>` , bölgenin adıdır.
* `<zone file name>` , aktarılacak bölge dosyasının yolu/adıdır.

Bölge içeri aktarırken olduğu gibi, önce oturum açmanız, aboneliğinizi seçmeniz ve Azure CLı 'yı Kaynak Yöneticisi modunu kullanacak şekilde yapılandırmanız gerekir.

### <a name="to-export-a-zone-file"></a>Bir bölge dosyasını dışarı aktarmak için

**Myresourcegroup** kaynak grubundaki mevcut Azure DNS Zone **contoso.com** öğesini dosya **contoso.com.txt** (geçerli klasörde) olarak dışa aktarmak için komutunu çalıştırın `azure network dns zone export` . Bu komut, bölgedeki kayıt kümelerini numaralandırmak ve sonuçları bağlama uyumlu bir bölge dosyasına aktarmak için Azure DNS hizmetini çağırır.

```azurecli
az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>Sonraki adımlar

* DNS bölgesindeki [kayıt kümelerini ve kayıtları yönetmeyi](./dns-getstarted-cli.md) öğrenin.

* [Azure DNS için etki alanınızı nasıl atayacağınızı](dns-domain-delegation.md)öğrenin.