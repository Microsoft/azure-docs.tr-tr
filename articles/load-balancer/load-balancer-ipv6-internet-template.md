---
title: IPv6-Azure şablonuyla Internet 'e yönelik yük dengeleyici dağıtma
titleSuffix: Azure Load Balancer
description: Azure şablonu kullanarak Azure Load Balancer ve yük dengeli VM 'Ler için IPv6 desteğinin nasıl dağıtılacağını öğrenin.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, azure yük dengeleyici, ikili yığın, genel IP, yerel IPv6, mobil veya IOT
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: f92011a6955398ef85e44fae1ce8f782cc9cc12c
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075995"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Bir şablon kullanarak IPv6 ile Internet 'e yönelik yük dengeleyici çözümü dağıtma

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [Şablon](load-balancer-ipv6-internet-template.md)


>[! NOTE: IPv6 için En Iyi uygulamada değişiklik yapın] Bu makalede temel yük dengeleyiciler hem IPv4 hem de IPv6 bağlantısı sağlamasına izin veren bir giriş IPv6 özelliği açıklanır.  Daha kapsamlı IPv6 bağlantısı artık, sanal ağlarınızla IPv6 bağlantısını tümleştiren ve IPv6 ağ güvenlik grubu kuralları, IPv6 Kullanıcı tanımlı yönlendirme, IPv6 temel ve standart yük dengeleme gibi önemli özellikleri ve daha fazlasını içeren [Azure VNET 'ler Için IPv6](../virtual-network/ipv6-overview.md) ile kullanılabilir.  Azure sanal ağları için IPv6, Azure 'daki IPv6 uygulamaları için önerilen en iyi uygulamadır. 
>Lütfen bkz. [Azure VNET şablon dağıtımı Için IPv6](../virtual-network/ipv6-configure-standard-load-balancer-template-json.md)

Azure Load Balancer bir Katman 4 (TCP, UDP) yük dengeleyicidir. Yük dengeleyici, gelen trafiği bulut hizmetlerindeki sağlıklı hizmet örnekleri veya bir yük dengeleyici kümesindeki sanal makineler arasında dağıtarak yüksek kullanılabilirlik sağlar. Ayrıca, Azure Load Balancer bu hizmetleri birden çok bağlantı noktasında, birden çok IP adresinde ya da her ikisinde birden sağlayabilir.

## <a name="example-deployment-scenario"></a>Örnek dağıtım senaryosu

Aşağıdaki diyagramda, bu makalede açıklanan örnek şablon kullanılarak dağıtılan yük dengeleme çözümü gösterilmektedir.

![Yük dengeleyici senaryosu](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

Bu senaryoda, aşağıdaki Azure kaynaklarını oluşturacaksınız:

* Her VM için hem IPv4 hem de IPv6 adresi atanmış bir sanal ağ arabirimi
* IPv4 ve IPv6 genel IP adresi ile Internet 'e yönelik Load Balancer
* Genel VIP 'leri özel uç noktalarla eşlemek için iki yük dengeleme kuralı
* İki VM 'yi içeren bir kullanılabilirlik kümesi
* iki sanal makine (VM)

## <a name="deploying-the-template-using-the-azure-portal"></a>Azure portal kullanarak şablonu dağıtma

Bu makale, [Azure hızlı başlangıç şablonları](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) galerisinde yayınlanan bir şablona başvurur. Şablonu Galeriden indirebilir veya Azure 'daki dağıtımı doğrudan Galeriden başlatabilirsiniz. Bu makalede, şablonu yerel bilgisayarınıza indirdiğiniz varsayılmaktadır.

1. Azure portal açın ve bir Azure aboneliği içinde VM 'Ler ve ağ kaynakları oluşturma izinlerine sahip bir hesapla oturum açın. Ayrıca, mevcut kaynakları kullanmıyorsanız, hesap için bir kaynak grubu ve depolama hesabı oluşturma izni gerekir.
2. Menüden "+ yeni" ye tıklayın, sonra arama kutusuna "şablon" yazın. Arama sonuçlarından "Şablon dağıtımı" seçeneğini belirleyin.

    ![lb-ipv6-portal-step2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. Her şey dikey penceresinde "Şablon dağıtımı" a tıklayın.

    ![lb-ipv6-portal-step3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. "Oluştur" a tıklayın.

    ![lb-ipv6-portal-step4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. "Şablonu Düzenle" ye tıklayın. Mevcut içeriği silin ve şablon dosyasının tüm içeriğini Kopyala/Yapıştır (başlangıç ve bitiş {} dahil etmek için), ardından "Kaydet" e tıklayın.

    > [!NOTE]
    > Microsoft Internet Explorer kullanıyorsanız, yapıştırdığınızda Windows panosuna erişime izin vermenizi isteyen bir iletişim kutusu alırsınız. "Erişime Izin ver" e tıklayın.

    ![lb-ipv6-portal-step5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. "Parametreleri Düzenle" seçeneğine tıklayın. Parametreler dikey penceresinde, şablon parametreleri bölümünde rehberlik başına değerleri belirtin ve sonra parametreler dikey penceresini kapatmak için "Kaydet" e tıklayın. Özel dağıtım dikey penceresinde, aboneliğinizi, var olan bir kaynak grubunu seçin ya da bir tane oluşturun. Bir kaynak grubu oluşturuyorsanız, kaynak grubu için bir konum seçin. Ardından **yasal koşullar**' a ve ardından yasal koşullar Için **satın al** ' a tıklayın. Azure, kaynakları dağıtmaya başlar. Tüm kaynakların dağıtılması birkaç dakika sürer.

    ![lb-ipv6-portal-step6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Bu parametreler hakkında daha fazla bilgi için bu makalenin ilerleyen kısımlarında yer alarak [şablon parametreleri ve değişkenleri](#template-parameters-and-variables) bölümüne bakın.

7. Şablon tarafından oluşturulan kaynakları görmek için, Araştır ' a tıklayın, "kaynak grupları" görene kadar listeyi aşağı kaydırın ve ardından tıklatın.

    ![lb-ipv6-portal-step7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. Kaynak grupları dikey penceresinde, adım 6 ' da belirttiğiniz kaynak grubunun adına tıklayın. Dağıtılan tüm kaynakların bir listesini görürsünüz. Her şey iyi olursa, "son dağıtım" altında "başarılı" deyin. Aksi takdirde, kullanmakta olduğunuz hesabın gerekli kaynakları oluşturmak için izinlere sahip olduğundan emin olun.

    ![lb-ipv6-portal-step8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Adım 6 ' yı tamamladıktan hemen sonra kaynak gruplarınıza gözatıyorsanız, "son dağıtım", kaynaklar dağıtılırken "dağıtım" durumunu görüntüler.

9. Kaynak listesinde "myIPv6PublicIP" düğmesine tıklayın. IP adresi altında bir IPv6 adresine sahip olduğunu ve bu sunucunun DNS adının 6. adımdaki dnsNameforIPv6LbIP parametresi için belirttiğiniz değer olduğunu görürsünüz. Bu kaynak, Internet istemcileri tarafından erişilebilen genel IPv6 adresi ve ana bilgisayar adıdır.

    ![lb-ipv6-portal-step9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Bağlantıyı doğrula

Şablon başarıyla dağıtıldığında, aşağıdaki görevleri tamamlayarak bağlantıyı doğrulayabilirsiniz:

1. Azure portal oturum açın ve şablon dağıtımı tarafından oluşturulan VM 'lerin her birine bağlanın. Bir Windows Server sanal makinesi dağıttıysanız, komut isteminden ipconfig/all komutunu çalıştırın. VM 'Lerin hem IPv4 hem de IPv6 adreslerine sahip olduğunu görürsünüz. Linux VM 'Leri dağıttıysanız, Linux dağıtım için sunulan yönergeleri kullanarak Linux işletim sistemini dinamik IPv6 adresleri alacak şekilde yapılandırmanız gerekir.
2. IPv6 Internet 'e bağlı bir istemciden, yük dengeleyicinin genel IPv6 adresiyle bir bağlantı başlatın. Yük dengeleyicinin iki VM arasında dengelenmesini onaylamak için, VM 'lerin her birine Microsoft Internet Information Services (IIS) gibi bir Web sunucusu yükleyebilirsiniz. Her bir sunucuda varsayılan Web sayfası, benzersiz bir şekilde tanımlamak için "Server0" veya "Sunucu1" metnini içerebilir. Daha sonra, bir Internet tarayıcısını IPv6 Internet 'e bağlı bir istemcide açın ve her bir VM 'ye uçtan uca IPv6 bağlantısını onaylamak için yük dengeleyicinin dnsNameforIPv6LbIP parametresi için belirttiğiniz ana bilgisayar adına gidin. Yalnızca bir sunucudan Web sayfasını görürseniz, tarayıcı önbelleğinizi temizlemeniz gerekebilir. Birden çok özel gözatma oturumu açın. Her sunucudan bir yanıt görmeniz gerekir.
3. IPv4 Internet 'e bağlı bir istemciden, yük dengeleyicinin genel IPv4 adresine bir bağlantı başlatın. Yük dengeleyicinin iki VM 'nin yük dengelemesinin olduğunu doğrulamak için, adım 2 ' de açıklandığı gibi IIS kullanarak test edebilirsiniz.
4. Her VM 'den bir IPv6 veya IPv4 bağlantılı Internet cihazına giden bir bağlantı başlatın. Her iki durumda da, hedef cihaz tarafından görülen kaynak IP, yük dengeleyicinin genel IPv4 veya IPv6 adresidir.

> [!NOTE]
> Azure ağında hem IPv4 hem de IPv6 için ıCMP engellenir. Sonuç olarak, ping gibi ıCMP araçları her zaman başarısız olur. Bağlantıyı sınamak için, TCPing veya PowerShell test-NetConnection cmdlet 'i gibi bir TCP alternatifi kullanın. Diyagramda gösterilen IP adreslerinin görebileceğiniz değer örnekleri olduğunu unutmayın. IPv6 adresleri dinamik olarak atandığından, aldığınız adresler farklı olur ve bölgeye göre farklılık gösterebilir. Ayrıca, yük dengeleyicideki genel IPv6 adresinin arka uç havuzundaki özel IPv6 adreslerinden farklı bir önekle başlaması yaygındır.

## <a name="template-parameters-and-variables"></a>Şablon parametreleri ve değişkenleri

Azure Resource Manager şablon, gereksinimlerinize göre özelleştirebileceğiniz birden çok değişken ve parametre içerir. Değişkenler, bir kullanıcının değiştirmesini istemediğiniz sabit değerler için kullanılır. Parametreler, bir kullanıcının şablonu dağıtmada sağlamasını istediğiniz değerler için kullanılır. Örnek şablon, bu makalede açıklanan senaryo için yapılandırılır. Bunu ortamınızın ihtiyaçlarına göre özelleştirebilirsiniz.

Bu makalede kullanılan örnek şablon aşağıdaki değişkenleri ve parametreleri içerir:

| Parametre/değişken | Notlar |
| --- | --- |
| adminUsername |Sanal makinelerde oturum açmak için kullanılan yönetici hesabının adını belirtin. |
| adminPassword |Sanal makinelerde oturum açmak için kullanılan yönetici hesabının parolasını belirtin. |
| dnsNameforIPv4LbIP |Yük dengeleyicinin ortak adı olarak atamak istediğiniz DNS ana bilgisayar adını belirtin. Bu ad, yük dengeleyicinin genel IPv4 adresine çözümlenir. Ad küçük harfle yazılmalıdır ve Regex ile eşleşmelidir: ^ [a-z] [a-Z0-9-]{1,61}[a-Z0-9] $. |
| dnsNameforIPv6LbIP |Yük dengeleyicinin ortak adı olarak atamak istediğiniz DNS ana bilgisayar adını belirtin. Bu ad, yük dengeleyicinin genel IPv6 adresine çözümlenir. Ad küçük harfle yazılmalıdır ve Regex ile eşleşmelidir: ^ [a-z] [a-Z0-9-]{1,61}[a-Z0-9] $. Bu, IPv4 adresiyle aynı ada sahip olabilir. İstemci bu ad için bir DNS sorgusu gönderdiğinde, ad paylaşıldığında Azure hem A hem de AAAA kayıtlarını döndürür. |
| vmNamePrefix |VM adı önekini belirtin. Şablon, VM 'Ler oluşturulduğunda ada bir sayı (0, 1, vb.) ekler. |
| nicNamePrefix |Ağ arabirimi adı ön ekini belirtin. Şablon, ağ arabirimleri oluşturulduğunda ada (0, 1, vb.) bir sayı ekler. |
| storageAccountName |Var olan bir depolama hesabının adını girin veya şablon tarafından oluşturulacak yeni bir ad belirtin. |
| availabilitySetName |VM 'lerle kullanılacak kullanılabilirlik kümesinin adını girin |
| addressPrefix |Sanal ağın adres aralığını tanımlamak için kullanılan adres ön eki |
| subnetName |VNet için oluşturulan alt ağın adı |
| subnetPrefix |Alt ağın adres aralığını tanımlamak için kullanılan adres ön eki |
| vnetName |VM 'Ler tarafından kullanılan VNet 'in adını belirtin. |
| ipv4PrivateIPAddressType |Özel IP adresi (statik veya dinamik) için kullanılan ayırma yöntemi |
| ipv6PrivateIPAddressType |Özel IP adresi (dinamik) için kullanılan ayırma yöntemi. IPv6 yalnızca dinamik ayırmayı destekler. |
| Numberofınstances |Şablon tarafından dağıtılan yük dengeli örnek sayısı |
| ipv4PublicIPAddressName |Yük dengeleyicinin ortak IPv4 adresi ile iletişim kurmak için kullanmak istediğiniz DNS adını belirtin. |
| ipv4PublicIPAddressType |Genel IP adresi (statik veya dinamik) için kullanılan ayırma yöntemi |
| Ipv6PublicIPAddressName |Yük dengeleyicinin genel IPv6 adresiyle iletişim kurmak için kullanmak istediğiniz DNS adını belirtin. |
| ipv6PublicIPAddressType |Genel IP adresi (dinamik) için kullanılan ayırma yöntemi. IPv6 yalnızca dinamik ayırmayı destekler. |
| lbName |Yük dengeleyicinin adını belirtin. Bu ad portalda görüntülenir veya CLı ya da PowerShell komutuyla başvuru yaparken kullanılır. |

Şablondaki kalan değişkenler, Azure kaynakları oluşturduğunda atanan türetilmiş değerleri içerir. Bu değişkenleri değiştirmeyin.

## <a name="next-steps"></a>Sonraki adımlar

Bir şablondaki yük dengeleyicinin JSON sözdizimi ve özellikleri için bkz. [Microsoft. Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers).
