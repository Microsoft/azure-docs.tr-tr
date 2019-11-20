---
title: Birden çok IP yapılandırmasında Yük Dengeleme-Azure portal
titleSuffix: Azure Load Balancer
description: Bu makalede, Azure portal kullanarak birincil ve ikincil IP yapılandırmalarında yük dengeleme hakkında bilgi edinin.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: se0dec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 4bf74986462ecb2659505f8a1261b9b24aba3fee
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076998"
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Azure portal kullanarak birden çok IP yapılandırmasında Yük Dengeleme

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [CLI](load-balancer-multiple-ip-cli.md)


Bu makalede, bir ikincil ağ arabirimi denetleyicisinde (NIC) birden çok IP adresi ile Azure Load Balancer kullanmayı göstereceğiz. Aşağıdaki diyagramda senaryonuzu gösterilmektedir:

![Yük dengeleyici senaryosu](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

Senaryolarımızda aşağıdaki yapılandırmayı kullanıyoruz:

- Windows çalıştıran iki sanal makine (VM).
- Her VM 'nin birincil ve ikincil NIC 'si vardır.
- Her ikincil NIC 'in iki IP yapılandırması vardır.
- Her VM iki Web sitesi barındırır: contoso.com ve fabrikam.com.
- Her Web sitesi, ikincil NIC 'deki bir IP yapılandırmasına bağlıdır.
- Azure Load Balancer, her bir Web sitesi için bir tane olmak üzere iki ön uç IP adresini göstermek için kullanılır. Ön uç adresleri, her Web sitesi için ilgili IP yapılandırmasına trafik dağıtmak için kullanılır.
- Aynı bağlantı noktası numarası hem ön uç IP adresleri hem de arka uç havuzu IP adresleri için kullanılır.

## <a name="prerequisites"></a>Önkoşullar

Senaryo örneğimiz, aşağıdaki gibi yapılandırılmış **contosofabrikam** adlı bir kaynak grubunuz olduğunu varsayar:

- Kaynak grubu, **Myvnet**adlı bir sanal ağ içerir.
- **Myvnet** ağı **VM1** ve **VM2**adlı iki VM 'yi içerir.
- VM1 ve VM2, **myAvailset**adlı aynı Kullanılabilirlik kümesinde bulunur. 
- VM1 ve VM2 her biri sırasıyla **VM1NIC1** ve **VM2NIC1**adlı bir birincil NIC 'ye sahiptir. 
- VM1 ve VM2 her biri sırasıyla **VM1NIC2** ve **VM2NIC2**adlı bir ikincil NIC 'ye sahiptir.

Birden çok NIC ile VM oluşturma hakkında daha fazla bilgi için bkz. [PowerShell kullanarak birden çok NIC Ile VM oluşturma](../virtual-machines/windows/multiple-nics.md).

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Birden çok IP yapılandırmasında yük dengelemeyi gerçekleştirme

Bu makalede özetlenen senaryoya ulaşmak için aşağıdaki adımları izleyin.

### <a name="step-1-configure-the-secondary-nics"></a>1\. Adım: ikincil NIC 'Leri yapılandırma

Sanal ağınızdaki her VM için, ikincil NIC için IP yapılandırmasını ekleyin:  

1. Azure portal gidin: https://portal.azure.com. Azure hesabınızla oturum açın.

2. Ekranın sol üst kısmında **kaynak grubu** simgesini seçin. Ardından sanal makinelerinizin bulunduğu kaynak grubunu seçin (örneğin, **contosofabrikam**). **Kaynak grupları** bölmesi VM 'ler için tüm kaynakları ve NIC 'leri görüntüler.

3. Her VM 'nin ikincil NIC 'i için IP yapılandırmasını ekleyin:

    1. Yapılandırmak istediğiniz ikincil NIC 'ı seçin.
    
    2. **IP yapılandırması**' nı seçin. Sonraki bölmede, en üstteki, **Ekle**' yi seçin.

    3. **IP Yapılandırması Ekle**altında, NIC 'ye ıkıncı bir IP yapılandırması ekleyin: 

        1. İkincil IP yapılandırması için bir ad girin. (Örneğin, VM1 ve VM2 için sırasıyla IP yapılandırmasını **VM1NIC2-ipconfig2** ve **VM2NIC2-ipconfig2**olarak adlandırın.)

        2. **Özel IP adresi**, **ayırma** ayarı için **statik**' ı seçin.

        3. **Tamam**’ı seçin.

İkincil NIC için ikinci IP yapılandırması tamamlandıktan sonra, söz konusu NIC için **IP yapılandırmaları** ayarları altında görüntülenir.

### <a name="step-2-create-the-load-balancer"></a>2\. Adım: Yük dengeleyiciyi oluşturun

Yapılandırma için yük dengeleyiciyi oluşturun:

1. Azure portal gidin: https://portal.azure.com. Azure hesabınızla oturum açın.

2. Ekranın sol üst kısmında, **ağ** > **Load Balancer** > **kaynak oluştur** ' u seçin. Sonra **Oluştur**' u seçin.

3. **Yük dengeleyici oluştur**altında yük dengeleyiciniz için bir ad yazın. Bu senaryoda, **mylb**adını kullanacağız.

4. **Genel IP adresi**altında, **PublicIP1**ADLı yeni bir genel IP oluşturun.

5. **Kaynak grubu**altında sanal makinelerinize ait mevcut kaynak grubunu seçin (örneğin, **contosofabrikam**). Yük dengeleyicinizin dağıtılacağı konumu seçin ve ardından **Tamam**' ı seçin.

Yük dengeleyici dağıtmaya başlar. Dağıtımın başarıyla tamamlanması birkaç dakika sürebilir. Dağıtım tamamlandıktan sonra, yük dengeleyici kaynak grubunuzda bir kaynak olarak görüntülenir.

### <a name="step-3-configure-the-front-end-ip-pool"></a>3\. Adım: ön uç IP havuzunu yapılandırma

Her Web sitesi için (contoso.com ve fabrikam.com), yük dengeleyicinizdeki ön uç IP havuzunu yapılandırın:

1. Portalda **diğer hizmetler**' i seçin. Filtre kutusuna **genel IP adresi** yazın ve ardından **genel IP adresleri**' ni seçin. Sonraki bölmede, en üstteki, **Ekle**' yi seçin.

2. İki Web sitesi (contoso.com ve fabrikam.com) için iki genel IP adresi (**PublicIP1** ve **PublicIP2**) yapılandırın:

   1. Ön uç IP adresiniz için bir ad yazın.

   2. **Kaynak grubu**Için, VM 'niz için mevcut kaynak grubunu seçin (örneğin, **contosofabrikam**).

   3. **Konum**Için VM 'lerle aynı konumu seçin.

   4. **Tamam**’ı seçin.

      Genel IP adresleri oluşturulduktan sonra, bunlar **genel IP** adresleri altında görüntülenir.

3. <a name="step3-3"></a>Portalda **diğer hizmetler**' i seçin. Filtre kutusunda **yük dengeleyici** yazın ve **Load Balancer**' ı seçin. 

4. Ön uç IP havuzu eklemek istediğiniz yük dengeleyiciyi (**mylb**) seçin.

5. **Ayarlar**altında **ön uç IP yapılandırması**' nı seçin. Sonraki bölmede, en üstteki, **Ekle**' yi seçin.

6. Ön uç IP adresiniz için bir ad yazın (örneğin, **contosofe** veya **fabrikamfe**).

7. <a name="step3-7"></a>**IP adresi**seçin. **Ortak IP adresi Seç**' in altında, ön UÇAĞıNıZıN IP adreslerini seçin (**PublicIP1** veya **PublicIP2**).

8. İkinci ön uç IP adresini, <a href="#step3-3">Adım 3</a> ile bu bölümde adım <a href="#step3-7">7 ' yi</a> yineleyerek oluşturun.

Ön uç havuzu yapılandırıldıktan sonra, IP adresleri yük dengeleyici **ön uç IP yapılandırması** ayarlarının altında görüntülenir. 
    
### <a name="step-4-configure-the-back-end-pool"></a>4\. Adım: arka uç havuzunu yapılandırma

Her Web sitesi için (contoso.com ve fabrikam.com), yük dengeleyicinizdeki arka uç adres havuzunu yapılandırın:
        
1. Portalda **diğer hizmetler**' i seçin. Filtre kutusunda **yük dengeleyici** yazın ve **Load Balancer**' ı seçin.

2. Arka uç havuzunu eklemek istediğiniz yük dengeleyiciyi (**mylb**) seçin.

3. **Ayarlar**altında **arka uç havuzları**' nı seçin. Arka uç havuzunuz için bir ad yazın (örneğin, **contosopool** veya **fabrikampool**). Sonraki bölmede, en üstteki, **Ekle**' yi seçin. 

4. **İlişkili**için **kullanılabilirlik kümesi**' ni seçin.

5. **Kullanılabilirlik kümesi**için **myAvailset**öğesini seçin.

6. Her iki VM için de hedef ağ IP yapılandırmasını ekleyin: 

    ![Yük Dengeleyici için arka uç havuzlarını yapılandırma](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. **Hedef sanal makine**için, arka uç havuzuna eklemek istediğiniz VM 'yi seçin (örneğin, **VM1** veya **VM2**).

    2. **Ağ IP yapılandırması**için, önceki ADıMDA seçtiğiniz VM IÇIN ikincil NIC 'nin IP yapılandırmasını seçin (örneğin, **VM1NIC2-ipconfig2** veya **VM2NIC2-ipconfig2**).

7. **Tamam**’ı seçin.

Arka uç havuzu yapılandırıldıktan sonra, adresler yük dengeleyici **arka uç havuzu** ayarlarınızın altında görüntülenir.

### <a name="step-5-configure-the-health-probe"></a>5\. Adım: sistem durumu araştırmasını yapılandırma

Yük dengeleyiciniz için bir sistem durumu araştırması yapılandırın:

1. Portalda **diğer hizmetler**' i seçin. Filtre kutusunda **yük dengeleyici** yazın ve **Load Balancer**' ı seçin.

2. Sistem durumu araştırmasını eklemek istediğiniz yük dengeleyiciyi (**mylb**) seçin.

3. **Ayarlar**altında **sistem durumu araştırması**' ni seçin. Sonraki bölmede, en üstteki, **Ekle**' yi seçin. 

4. Durum araştırması için bir ad yazın (örneğin, **http**). **Tamam**’ı seçin.

### <a name="step-6-configure-load-balancing-rules"></a>6\. Adım: Yük Dengeleme kurallarını yapılandırma

Her Web sitesi için (contoso.com ve fabrikam.com), Yük Dengeleme kurallarını yapılandırın:
    
1. <a name="step6-1"></a>**Ayarlar**bölümünde **Yük Dengeleme kuralları**' nı seçin. Sonraki bölmede, en üstteki, **Ekle**' yi seçin. 

2. **Ad**için, Yük Dengeleme kuralı için bir ad yazın (örneğin, contoso.com Için **httpc** veya fabrikam.com için **httpf** ).

3. **Ön uç IP adresi**için, daha önce oluşturduğunuz ön uç IP adresini seçin (örneğin, **contosofe** veya **fabrikamfe**).

4. **Bağlantı noktası** ve **arka uç bağlantı noktası**için varsayılan **80**değerini koruyun.

5. **Kayan IP (doğrudan sunucu dönüşü)** Için **devre dışı**öğesini seçin.

6. <a name="step6-6"></a>**Tamam ' ı**seçin.

7. İkinci yük dengeleyici kuralını, bu bölümde adım <a href="#step6-6">6</a> ' dan 5. adım ' <a href="#step6-1">i</a> yineleyerek oluşturun.

Kurallar yapılandırıldıktan sonra, yük dengeleyici **Yük Dengeleme kuralları** ayarları altında görüntülenir.

### <a name="step-7-configure-dns-records"></a>7\. Adım: DNS kayıtlarını yapılandırma

Son adım olarak, DNS kaynak kayıtlarınızı yük dengeleyiciniz için ilgili ön uç IP adreslerini işaret etmek üzere yapılandırın. Etki alanlarınızı Azure DNS ' de barındırabilirsiniz. Load Balancer ile Azure DNS kullanma hakkında daha fazla bilgi için bkz. [diğer Azure hizmetleriyle Azure DNS kullanma](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Sonraki adımlar
- Azure 'da yük [Dengeleme hizmetlerini kullanarak](../traffic-manager/traffic-manager-load-balancing-azure.md)Yük Dengeleme hizmetlerini Azure 'da birleştirme hakkında daha fazla bilgi edinin.
- [Azure Load Balancer Için Azure izleyici günlüklerinde](../load-balancer/load-balancer-monitor-log.md)yük dengeleyiciyi yönetmek ve sorunlarını gidermek için farklı türlerdeki günlükleri nasıl kullanabileceğinizi öğrenin.
