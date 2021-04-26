---
title: Azure özel DNS için bir etki alanı bölge dosyasını içeri ve dışarı aktarma-Azure CLı
titleSuffix: Azure DNS
description: Azure CLı kullanarak bir DNS bölge dosyasını Azure özel DNS 'e aktarmayı ve vermeyi öğrenin
services: dns
author: duongau
ms.service: dns
ms.date: 03/16/2021
ms.author: duau
ms.topic: how-to
ms.openlocfilehash: 2e5babb85dbf4aa7da707e22be5eeaf3ae89972d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451180"
---
# <a name="import-and-export-a-private-dns-zone-file-for-azure-private-dns"></a>Azure özel DNS için özel DNS bölge dosyasını içeri ve dışarı aktarma

Bu makalede, Azure CLı kullanarak Azure DNS için DNS bölge dosyalarını içeri ve dışarı aktarma işlemi adım adım açıklanmaktadır.

## <a name="introduction-to-dns-zone-migration"></a>DNS bölgesi geçişine giriş

DNS bölge dosyası, bölgedeki her etki alanı adı sistemi (DNS) kaydının ayrıntılarını içeren bir metin dosyasıdır. Standart bir biçimi izler ve bu, DNS kayıtlarını DNS sistemleri arasında aktarmaya uygun hale getirir. Bölge dosyası kullanmak, bir DNS bölgesini Azure DNS içine veya dışına aktarmaya yönelik hızlı, güvenilir ve kullanışlı bir yoldur.

Azure özel DNS, Azure komut satırı arabirimi 'ni (CLı) kullanarak bölge dosyalarını içeri ve dışarı aktarmayı destekler. Bölge dosyası içeri aktarma **Şu anda Azure PowerShell** veya Azure Portal aracılığıyla desteklenmiyor.

Azure CLı, Azure hizmetlerini yönetmek için kullanılan platformlar arası bir komut satırı aracıdır. [Azure İndirmeleri sayfasından](https://azure.microsoft.com/downloads/)Windows, Mac ve Linux platformları için kullanılabilir. Platformlar arası destek, bölge dosyalarını içeri ve dışarı aktarma için önemlidir, çünkü en yaygın ad sunucusu yazılımı, [bağlama](https://www.isc.org/downloads/bind/)genellikle Linux üzerinde çalışır.

## <a name="obtain-your-existing-dns-zone-file"></a>Mevcut DNS bölge dosyanızı edinin

Azure DNS bir DNS bölge dosyasını içeri aktarmadan önce, bölge dosyasının bir kopyasını edinmeniz gerekir. Bu dosyanın kaynağı, DNS bölgesinin Şu anda barındırıldığı yere bağlıdır.

* DNS bölgeniz bir iş ortağı hizmeti (örneğin, bir etki alanı kaydedici, adanmış DNS barındırma sağlayıcısı veya alternatif bulut sağlayıcısı) tarafından barındırılıyorsa, bu hizmet DNS bölge dosyasını indirme yeteneğini sağlamalıdır.
* DNS bölgeniz Windows DNS üzerinde barındırılıyorsa, bölge dosyaları için varsayılan klasör **%SystemRoot%\System32\Dns** olur. Her bölge dosyasının tam yolu, DNS konsolunun **genel** sekmesinde de görüntülenir.
* DNS bölgeniz BIND kullanılarak barındırılıyorsa, her bir bölgenin bölge dosyasının konumu **. conf ADLı** bağlama yapılandırma dosyasında belirtilir.

## <a name="import-a-dns-zone-file-into-azure-private-dns"></a>DNS bölge dosyasını Azure özel DNS 'e aktarma

Bir bölge dosyasının içe aktarılması, zaten mevcut değilse Azure özel DNS 'de yeni bir bölge oluşturur. Bölge zaten varsa, bölge dosyasındaki kayıt kümelerinin varolan kayıt kümeleriyle birleştirilmesi gerekir.

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
az network private-dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Değerler:

* `<resource group>` Azure DNS içindeki bölge için kaynak grubunun adıdır.
* `<zone name>` , bölgenin adıdır.
* `<zone file name>` İçeri aktarılacak bölge dosyasının yolu/adı.

Kaynak grubunda bu ada sahip bir bölge yoksa, sizin için oluşturulur. Bölge zaten varsa, içeri aktarılan kayıt kümeleri varolan kayıt kümeleriyle birleştirilir.

### <a name="import-a-zone-file"></a>Bölge dosyasını içeri aktarma

Bölge **contoso.com** için bir bölge dosyasını içeri aktarma.

1. Henüz bir tane yoksa, bir Kaynak Yöneticisi kaynak grubu oluşturmanız gerekir.

    ```azurecli
    az group create --resource-group myresourcegroup -l westeurope
    ```

2. **Contoso.com** bölgesini dosya **contoso.com.txt** **myresourcegroup** kaynak grubundaki yeni bir DNS bölgesine aktarmak için komutunu çalıştırın `az network private-dns zone import` .<BR>Bu komut, bölge dosyasını yükler ve ayrıştırır. Komutu, bölgeyi ve bölgedeki tüm kayıt kümelerini oluşturmak için Azure DNS hizmetinde bir dizi komut yürütür. Komut, ilerleme durumunu, hata veya uyarılarla birlikte konsol penceresinde bildirir. Kayıt kümeleri seri halinde oluşturulduğundan, büyük bir bölge dosyasının içe aktarılması birkaç dakika sürebilir.

    ```azurecli
    az network private-dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="verify-the-zone"></a>Bölgeyi doğrulama

Dosyayı içeri aktardıktan sonra DNS bölgesini doğrulamak için aşağıdaki yöntemlerden birini kullanabilirsiniz:

* Aşağıdaki Azure CLı komutunu kullanarak kayıtları listeleyebilirsiniz:

    ```azurecli
    az network private-dns record-set list -g myresourcegroup -z contoso.com
    ```

* `nslookup`Kayıtlar için ad çözümlemesini doğrulamak üzere ' i kullanabilirsiniz. Bölge henüz temsilci olmadığından, doğru Azure DNS ad sunucularını açıkça belirtmeniz gerekir. Aşağıdaki örnek, bölgeye atanan ad sunucusu adlarının nasıl alınacağını gösterir. Bu Ayrıca, kullanarak "www" kaydının nasıl sorgulanalınacağını gösterir `nslookup` .

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Azure DNS bir DNS bölge dosyasını dışarı aktarma

DNS bölgesini dışarı aktarmak için Azure CLı komutunun biçimi:

```azurecli
az network private-dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Değerler:

* `<resource group>` Azure DNS içindeki bölge için kaynak grubunun adıdır.
* `<zone name>` , bölgenin adıdır.
* `<zone file name>` , aktarılacak bölge dosyasının yolu/adıdır.

Bölge içeri aktarırken olduğu gibi, önce oturum açmanız, aboneliğinizi seçmeniz ve Azure CLı 'yı Kaynak Yöneticisi modunu kullanacak şekilde yapılandırmanız gerekir.

### <a name="to-export-a-zone-file"></a>Bir bölge dosyasını dışarı aktarmak için

**Myresourcegroup** kaynak grubundaki mevcut Azure DNS Zone **contoso.com** öğesini dosya **contoso.com.txt** (geçerli klasörde) olarak dışa aktarmak için komutunu çalıştırın `azure network private-dns zone export` . Bu komut, bölgedeki kayıt kümelerini numaralandırmak ve sonuçları bağlama uyumlu bir bölge dosyasına aktarmak için Azure DNS hizmetini çağırır.

```azurecli
az network private-dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>Sonraki adımlar

* DNS bölgesindeki [kayıt kümelerini ve kayıtları yönetmeyi](./private-dns-getstarted-cli.md) öğrenin.
