---
title: Azure DNS 'de ters DNS arama bölgeleri barındırma
description: IP aralıklarınız için ters DNS arama bölgelerini barındırmak üzere Azure DNS nasıl kullanacağınızı öğrenin
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 0c85049d6c8921432a753bf08989cab473b7c734
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99525136"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Azure DNS 'de ters DNS arama bölgeleri barındırma

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu makalede, Azure DNS ' de atanan IP aralıkları için ters DNS arama bölgelerinin nasıl barındırılacağını açıklanmaktadır. Geriye doğru arama bölgeleri tarafından temsil edilen IP aralıkları, genellikle ISS 'niz tarafından şirketinize atanmalıdır.

Azure hizmetinize atanmış olan, Azure 'a ait bir IP adresi için ters DNS 'yi yapılandırmak üzere bkz. [Azure 'da barındırılan hizmetler için ters DNS yapılandırma](dns-reverse-dns-for-azure-services.md).

Bu makaleyi okuduktan sonra [Azure 'da ters DNS ve desteğe genel bakış](dns-reverse-dns-overview.md)konusunu bilmeniz gerekir.

Bu makale, Azure portal, Azure PowerShell, Azure klasik CLı veya Azure CLı kullanarak ilk geriye doğru arama DNS bölgenizi ve kaydınızı oluşturma adımlarında size kılavuzluk eder.

## <a name="create-a-reverse-lookup-dns-zone"></a>Geriye doğru arama DNS bölgesi oluşturma

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. **Hub** menüsünde **Yeni**  >  **ağ**' ı ve ardından **DNS bölgesi**' ni seçin.

   !["DNS bölgesi" seçimi](./media/dns-reverse-dns-hosting/figure1.png)

1. **DNS bölgesi oluştur** BÖLMESINDE, DNS bölgenizi adlandırın. Bölgenin adı, IPv4 ve IPv6 önekleri için farklı şekilde oluşturulur. Diliminizi adlandırmak için [IPv4](#ipv4) veya [IPv6](#ipv6) yönergelerini kullanın. İşiniz bittiğinde, bölgeyi oluşturmak için **Oluştur** ' u seçin.

### <a name="ipv4"></a>IPv4

IPv4 geriye doğru arama bölgesinin adı, temsil ettiği IP aralığını temel alır. Şu biçimde olmalıdır: `<IPv4 network prefix in reverse order>.in-addr.arpa` . Örnekler için bkz. [Azure 'da ters DNS ve desteğe genel bakış](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Azure DNS ' de sınıfsız ters DNS arama bölgeleri oluştururken, `-` bölge adında eğik çizgi () yerine kısa çizgi () kullanmanız gerekir `/` .
>
> Örneğin, 192.0.2.128/26 IP aralığı için `128-26.2.0.192.in-addr.arpa` yerine bölge adı olarak kullanmanız gerekir `128/26.2.0.192.in-addr.arpa` .
>
> DNS standartları her iki yöntemi de desteklese de, Azure DNS eğik çizgi () karakterini içeren DNS bölge adlarını desteklemez `/` .

Aşağıdaki örnek, Azure portal aracılığıyla Azure DNS adlı bir sınıf C ters DNS bölgesinin nasıl oluşturulacağını gösterir `2.0.192.in-addr.arpa` :

 ![Azure portal aracılığıyla Azure DNS içinde 2.0.192.in-addr. arpa adlı bir sınıf C ters DNS bölgesinin nasıl oluşturulacağını gösteren ekran görüntüsü.](./media/dns-reverse-dns-hosting/figure2.png)

**Kaynak grubu konumu** , kaynak grubunun konumunu tanımlar. DNS bölgesi üzerinde hiçbir etkisi yoktur. DNS bölgesinin konumu her zaman “genel” şeklindedir ve gösterilmez.

Aşağıdaki örneklerde Azure PowerShell ve Azure CLı kullanılarak bu görevin nasıl tamamlanacağı gösterilmektedir.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure klasik CLI

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI’si

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

IPv6 geriye doğru arama bölgesinin adı şu biçimde olmalıdır: `<IPv6 network prefix in reverse order>.ip6.arpa` .  Örnekler için bkz. [Azure 'da ters DNS ve desteğe genel bakış](dns-reverse-dns-overview.md#ipv6).


Aşağıdaki örnek, Azure portal aracılığıyla Azure DNS adlı bir IPv6 ters DNS arama bölgesinin nasıl oluşturulacağını gösterir `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` :

 !["DNS bölgesi oluşturma" bölmesi, kutular doldurulmuş](./media/dns-reverse-dns-hosting/figure3.png)

**Kaynak grubu konumu** , kaynak grubunun konumunu tanımlar. DNS bölgesi üzerinde hiçbir etkisi yoktur. DNS bölgesinin konumu her zaman “genel” şeklindedir ve gösterilmez.

Aşağıdaki örneklerde Azure PowerShell ve Azure CLı kullanılarak bu görevin nasıl tamamlanacağı gösterilmektedir.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure klasik CLI

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI’si

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Ters DNS arama bölgesi verme

Artık ters DNS arama bölgenizi oluşturduğunuza göre, bölgenin üst bölgeden temsilci atanmış olduğundan emin olmanız gerekir. DNS temsili, DNS ad çözümleme işleminin ters DNS arama diliminizi barındıran ad sunucularını bulmasına olanak sağlar. Bu ad sunucuları daha sonra adres aralığlarınızdaki IP adresleri için DNS ters sorgularına yanıt verebilir.

İleriye doğru arama bölgeleri için, DNS bölgesi temsilci seçme işlemi, [etki alanınızı Azure DNS Için temsilci](dns-delegate-domain-azure-dns.md)olarak açıklanmaktadır. Geriye doğru arama bölgelerinin temsili aynı şekilde çalışmaktadır. Tek fark, ad sunucularını etki alanı adı kaydediciniz yerine, IP aralığınızı sağlayan ISS ile yapılandırmanız gerektiğidir.

## <a name="create-a-dns-ptr-record"></a>DNS PTR kaydı oluşturma

### <a name="ipv4"></a>IPv4

Aşağıdaki örnek, Azure DNS bir ters DNS bölgesinde bir PTR kaydı oluşturma sürecinde size yol gösterir. Diğer kayıt türleri ve var olan kayıtların değiştirilmesi hakkında bilgi için bkz. [Azure portalı kullanarak DNS kayıtlarını ve kayıt kümelerini yönetme](dns-operations-recordsets-portal.md).

1. **DNS bölgesi** bölmesinin en üstünde **+ kayıt kümesi** ' ni seçerek **kayıt kümesi Ekle** bölmesini açın.

   ![+ Kayıt kümesi düğmesine işaret eden bir ok ile DNS bölgesi bölmesinin ekran görüntüsü.](./media/dns-reverse-dns-hosting/figure4.png)

1. Bir PTR kaydı için kayıt kümesinin adının ters sırada IPv4 adresinin geri kalanı olması gerekir. 

   Bu örnekte, ilk üç sekizli zaten bölge adının (. 2.0.192) bir parçası olarak doldurulmuştur. Bu nedenle, **ad** kutusunda yalnızca son sekizli sağlanır. Örneğin, IP adresi 192.0.2.15 olan bir kaynak için kayıt kümesine **15** adını yazabilirsiniz.  
1. **Tür** için **PTR**' yi seçin.  
1. **Etki alanı adı** IÇIN, IP 'yi kullanan kaynağın tam etki alanı adını (FQDN) girin.
1. DNS kaydını oluşturmak için bölmenin altındaki **Tamam ' ı** seçin.

   !["Kayıt kümesi Ekle" bölmesi, kutular doldurulmuş](./media/dns-reverse-dns-hosting/figure5.png)

Aşağıdaki örneklerde, PowerShell veya Azure CLı kullanarak bu görevin nasıl tamamlanacağı gösterilmektedir.

#### <a name="powershell&quot;></a>PowerShell

```powershell
New-AzDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname &quot;dc1.contoso.com")
```
#### <a name="azure-classic-cli"></a>Azure klasik CLI

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azure-cli"></a>Azure CLI’si

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

Aşağıdaki örnek, yeni PTR kaydı oluşturma sürecinde size yol gösterir. Diğer kayıt türleri ve var olan kayıtların değiştirilmesi hakkında bilgi için bkz. [Azure portalı kullanarak DNS kayıtlarını ve kayıt kümelerini yönetme](dns-operations-recordsets-portal.md).

1. **DNS bölgesi** bölmesinin en üstünde **+ kayıt kümesi** ' ni seçerek **kayıt kümesi Ekle** bölmesini açın.

   ![Kayıt kümesi oluşturmak için düğme](./media/dns-reverse-dns-hosting/figure6.png)

2. Bir PTR kaydı için kayıt kümesinin adının ters sırada IPv6 adresinin geri kalanı olması gerekir. Sıfır sıkıştırma içermemelidir. 

   Bu örnekte, IPv6 'nın ilk 64 biti zaten bölge adının bir parçası olarak doldurulmuştur (0.0.0.0. c. d. b. a. 8. b. d. 0.1.0.0.2. ip6. arpa). Bu nedenle, **ad** kutusunda yalnızca son 64 bitleri sağlanır. IP adresinin son 64 bitleri, her bir onaltılık sayı arasında sınırlayıcı olarak bir noktayla birlikte ters sırada girilir. Örneğin, IP adresi 2001:0db8: abdc: 0000: f524:10bc: 1af9:405e olan bir kaynak için kayıt ayarlamış **e. 5.0.4.9. f. a. 1. c. b. 0.1.4.2.5. f** adını yazabilirsiniz.  
3. **Tür** için **PTR**' yi seçin.  
4. **Etki alanı adı** için IP 'yi kullanan kaynağın FQDN 'sini girin.
5. DNS kaydını oluşturmak için bölmenin altındaki **Tamam ' ı** seçin.

![Tür alanındaki değere işaret eden bir ok ile içeren "kayıt kümesi Ekle" bölmesini gösteren ekran görüntüsü.](./media/dns-reverse-dns-hosting/figure7.png)

Aşağıdaki örneklerde, PowerShell veya Azure CLı kullanarak bu görevin nasıl tamamlanacağı gösterilmektedir.

#### <a name="powershell&quot;></a>PowerShell

```powershell
New-AzDnsRecordSet -Name &quot;e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f&quot; -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname &quot;dc2.contoso.com")
```

#### <a name="azure-classic-cli"></a>Azure klasik CLI

```azurecli
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli"></a>Azure CLI’si

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Kayıtları görüntüleme

Oluşturduğunuz kayıtları görüntülemek için Azure portal DNS bölgenize gidin. **DNS bölgesi** bölmesinin alt bölümünde DNS bölgesinin kayıtlarını görebilirsiniz. Varsayılan NS ve SOA kayıtlarının yanı sıra, oluşturduğunuz tüm yeni kayıtları görmeniz gerekir. NS ve SOA kayıtları her bölgede oluşturulur. 

### <a name="ipv4"></a>IPv4

**DNS bölgesi** bölmesi, IPv4 PTR kayıtlarını gösterir:

![IPv4 kayıtlarıyla "DNS bölgesi" bölmesi](./media/dns-reverse-dns-hosting/figure8.png)

Aşağıdaki örneklerde, PowerShell veya Azure CLı kullanarak PTR kayıtlarının nasıl görüntüleneceği gösterilmektedir.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure klasik CLI

```azurecli
azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI’si

```azurecli
az network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

**DNS bölgesi** BÖLMESI IPv6 PTR kayıtlarını gösterir:

![IPv6 kayıtlarıyla "DNS bölgesi" bölmesi](./media/dns-reverse-dns-hosting/figure9.png)

Aşağıdaki örneklerde, PowerShell veya Azure CLı kullanarak kayıtların nasıl görüntüleneceği gösterilmektedir.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure klasik CLI

```azurecli
azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI’si

```azurecli
az network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>SSS

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Azure DNS üzerinde ISS tarafından atanan IP blokları için ters DNS arama bölgelerini barındırabilirim miyim?

Evet. Azure DNS içindeki kendi IP aralıklarınız için ters arama (ARPA) bölgelerini barındırma, tam olarak desteklenmektedir.

Bu makalede açıklandığı gibi Azure DNS ters arama bölgesini oluşturun ve ardından [bölgeye temsilci seçmek](dns-domain-delegation.md)için ISS 'niz ile çalışın. Ardından, her ters arama için PTR kayıtlarını diğer kayıt türleriyle aynı şekilde yönetebilirsiniz.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Ters DNS arama bölgesi maliyetimi ne kadar çok barınıyor?

Azure DNS içindeki ISS tarafından atanan IP bloğunun ters DNS arama bölgesini barındırmak, [standart Azure DNS fiyatlarıyla](https://azure.microsoft.com/pricing/details/dns/)ücretlendirilir.

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Azure DNS içinde hem IPv4 hem de IPv6 adresleri için ters DNS arama bölgelerini barındırabilirim miyim?

Evet. Bu makalede, Azure DNS ' de hem IPv4 hem de IPv6 ters DNS arama bölgelerinin nasıl oluşturulacağı açıklanmaktadır.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Mevcut bir ters DNS arama bölgesini içeri aktarabilir miyim?

Evet. Azure CLı kullanarak mevcut DNS bölgelerini Azure DNS içine aktarabilirsiniz. Bu yöntem, ileriye doğru arama bölgeleri ve geriye doğru arama bölgeleri için geçerlidir.

Daha fazla bilgi için bkz. [Azure CLI kullanarak DNS bölge dosyasını içeri ve dışarı aktarma](dns-import-export.md).

## <a name="next-steps"></a>Sonraki adımlar

Ters DNS hakkında daha fazla bilgi için bkz. [Vikipde ters DNS araması](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
[Azure hizmetlerinize yönelik ters DNS kayıtlarını yönetmeyi](dns-reverse-dns-for-azure-services.md)öğrenin.
