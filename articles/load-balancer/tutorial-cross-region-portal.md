---
title: 'Öğretici: Azure portal kullanarak çapraz bölge yük dengeleyici oluşturma'
titleSuffix: Azure Load Balancer
description: Azure portal bir çapraz bölge Azure Load Balancer dağıtmaya yönelik bu öğreticiyi kullanmaya başlayın.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/24/2021
ms.openlocfilehash: 16320021ede4a4e285c4e1973c166d2cdf643c4a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529539"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-the-azure-portal"></a>Öğretici: Azure portal kullanarak bir çapraz bölge Azure Load Balancer oluşturma

Çapraz bölge yük dengeleyici, bir hizmetin birden çok Azure bölgesinde küresel olarak kullanılabilmesini sağlar. Bir bölge başarısız olursa, trafik sonraki en iyi sağlıklı bölgesel yük dengeleyiciye yönlendirilir.  

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bölgeler arası yük dengeleyici oluşturun.
> * İki bölgesel yük dengeleyicileri içeren bir arka uç havuzu oluşturun.
> * Yük dengeleyici kuralı oluşturun.
> * Yük dengeleyiciyi test edin.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

> [!IMPORTANT]
> Çapraz bölge Azure Load Balancer Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği.
- İki farklı Azure bölgesinde dağıtılan arka uç havuzlarıyla iki **Standart** SKU Azure Yük dengeleyicileri.
    - Bir bölgesel standart yük dengeleyici ve arka uç havuzları için sanal makineler oluşturma hakkında bilgi için bkz. [hızlı başlangıç: Azure Portal kullanarak VM 'lerin yükünü dengelemek için genel yük dengeleyici oluşturma](quickstart-load-balancer-standard-public-portal.md).
        - **-R1** ve **-R2** ile her bir bölgedeki yük dengeleyiciler, sanal makineler ve diğer kaynakların adını ekleyin. 

## <a name="sign-in-to-azure-portal"></a>Azure portalda oturum açın

Azure portalında [oturum açın](https://portal.azure.com).

## <a name="create-cross-region-load-balancer"></a>Çapraz bölge yük dengeleyici oluştur

Bu bölümde, bir çapraz bölge yük dengeleyici ve genel IP adresi oluşturacaksınız.

1. **Kaynak oluştur**’u seçin. 
2. Arama kutusuna **yük dengeleyici** girin. Arama sonuçlarında **Yük dengeleyiciyi** seçin.
3. **Yük dengeleyici** sayfasında **Oluştur**' u seçin.
4. **Yük dengeleyici oluştur** sayfasının **temel bilgiler** sekmesinde, aşağıdaki bilgileri girin veya seçin: 

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | Abonelik               | Aboneliğinizi seçin.    |    
    | Kaynak grubu         | **Yeni oluştur** ' u seçin ve metin kutusuna **Createcrlböğreticisi-RG** girin.|
    | Name                   | **Myloadbalancer-CR** girin                                   |
    | Region         | **Batı ABD (ABD)** seçin.                                        |
    | Tür          | **Genel**’i seçin.                                        |
    | SKU           | Varsayılan değer olan **Standart**' i bırakın. |
    | Katman           | **Küresel** seçin |
    | Genel IP adresi | **Yeni oluştur**’u seçin.|
    | Genel IP adresi adı | Metin kutusuna **Mypublicıp-CR** yazın.|
    | Yönlendirme tercihi| **Microsoft ağı**' nı seçin. </br> Yönlendirme tercihi hakkında daha fazla bilgi için bkz. [yönlendirme tercihi (Önizleme) nedir?](../virtual-network/routing-preference-overview.md). |

    > [!NOTE]
    > Çapraz bölge yük dengeleyici yalnızca şu giriş bölgelerinde dağıtılabilir: **Doğu ABD 2, Batı ABD, Batı Avrupa, Güneydoğu Asya, Orta ABD, Kuzey Avrupa, Doğu Asya**. Daha fazla bilgi için bkz. **https://aka.ms/homeregionforglb**.


3. Geri kalan ayarlar için varsayılan değerleri kabul edin ve ardından **gözden geçir + oluştur**' u seçin.

4. **Gözden geçir + oluştur** sekmesinde **Oluştur**' u seçin.   

    :::image type="content" source="./media/tutorial-cross-region-portal/create-cross-region.png" alt-text="Çapraz bölge yük dengeleyici oluşturma" border="true":::

## <a name="create-backend-pool"></a>Arka uç havuzu oluşturma

Bu bölümde, çapraz bölge yük dengeleyicinin arka uç havuzuna iki bölgesel standart yük dengeleyici ekleyeceksiniz.

> [!IMPORTANT]
> Bu adımları tamamlayabilmeniz için, arka uç havuzlarıyla birlikte iki bölgesel yük dengeleyiciler aboneliğinizde dağıtılmış olduğundan emin olun.  Daha fazla bilgi için bkz. **[hızlı başlangıç: Azure Portal kullanarak VM 'lerin yükünü dengelemek için ortak yük dengeleyici oluşturma](quickstart-load-balancer-standard-public-portal.md)**.

Bölgesel standart yük dengeleyicileri dahil etmek için **Mybackendpool-CR** arka uç adres havuzunu oluşturun.

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynak LISTESINDEN **myloadbalancer-CR** ' yi seçin.

2. **Ayarlar** altında **arka uç havuzları**' nı seçin ve ardından **Ekle**' yi seçin.

3. **Arka uç Havuzu Ekle** sayfasında, ad Için **mybackendpool-CR** yazın.

4. **Add (Ekle)** seçeneğini belirleyin.

4. **Mybackendpool-CR** öğesini seçin.

5. **Yük dengeleyiciler** altında, **yük dengeleyici** altındaki açılır kutuyu seçin.

5. **Myloadbalancer-R1** veya bölge 1 ' de yük dengeleyicinizin adını seçin.

6. **Ön uç IP yapılandırması** altındaki açılır kutuyu seçin. **Loadbalancerön uç** öğesini seçin.

7. **Myloadbalancer-R2** eklemek için 4-6 arası adımları tekrarlayın.

8. **Add (Ekle)** seçeneğini belirleyin.

    :::image type="content" source="./media/tutorial-cross-region-portal/add-to-backendpool.png" alt-text="Backendpool 'a bölgesel yük dengeleyiciler ekleme" border="true":::

## <a name="create-a-load-balancer-rule"></a>Yük dengeleyici kuralı oluşturma

Bu bölümde, bir yük dengeleyici kuralı oluşturacaksınız:

* Adlandırılmış **Myhttprule**.
* **Loadbalancerön uç** adında.
* **80 numaralı bağlantı noktasını** dinliyor.
* Yük dengeli trafiği, **80 numaralı bağlantı noktasında** **Mybackendpool-CR** adlı arka uca yönlendirir.

    > [!NOTE]
    > Ön uç bağlantı noktası, arka uç bağlantı noktası ve arka uç havuzundaki bölgesel yük dengeleyiciler ön uç bağlantı noktası ile eşleşmelidir.

1. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynak LISTESINDEN **myloadbalancer-CR** ' yi seçin.

2. **Ayarlar** bölümünde **Yük Dengeleme kuralları**' nı ve ardından **Ekle**' yi seçin.

3. Yük Dengeleme kuralını yapılandırmak için şu değerleri kullanın:
    
    | Ayar | Değer |
    | ------- | ----- |
    | Ad | **Myhttprule** girin. |
    | IP sürümü | **IPv4** seçin |
    | Ön uç IP adresi | **Loadbalancerön uç** seçin |
    | Protokol | **TCP**’yi seçin. |
    | Bağlantı noktası | **80** girin.|
    | Arka uç bağlantı noktası | **80** girin. |
    | Arka uç havuzu | **Mybackendpool** öğesini seçin.|
    | Boşta kalma zaman aşımı (dakika) | Kaydırıcıyı **15**' e taşıyın. |
    | TCP sıfırlaması | **Etkin**'i seçin. |

4. Kalan varsayılan değerleri bırakın ve **Tamam**' ı seçin.

    :::image type="content" source="./media/tutorial-cross-region-portal/create-lb-rule.png" alt-text="Yük dengeleyici kuralı oluştur" border="true":::

## <a name="test-the-load-balancer"></a>Yük dengeleyiciyi test etme

Bu bölümde, bölgeler arası yük dengeleyiciyi test edersiniz. Genel IP adresine bir Web tarayıcısında bağlanırsınız.  Bölgesel yük dengeleyici arka uç havuzlarından birindeki sanal makineleri durdurarak yük devretmeyi gözlemleyeceksiniz.

1. **Genel Bakış** ekranında yük dengeleyici için genel IP adresini bulun. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından **mypublicıp-CR**' yi seçin.

2. Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın. IIS Web sunucusunun varsayılan sayfası, tarayıcıda görüntülenir.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-1.png" alt-text="Yük dengeleyiciyi sınama" border="true":::

3. Bölgesel yük dengeleyicileri birinin arka uç havuzundaki sanal makineleri durdurun.

4. Web tarayıcısını yenileyin ve diğer bölgesel yük dengeleyiciye bağlantının yük devretmesini gözlemleyin.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-2.png" alt-text="Yük devretme sonrasında yük dengeleyiciyi sına" border="true":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, yük dengeleyiciyi ve tüm ilgili kaynakları silin. 

Bunu yapmak için, kaynakları içeren **Createcrlböğreticisi-RG** kaynak grubunu seçin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

* Bir çapraz bölge yük dengeleyici oluşturuldu.
* Bölgesel yük dengeleyiciler çapraz bölge yük dengeleyicinin arka uç havuzuna eklendi.
* Yük Dengeleme kuralı oluşturuldu.
* Yük dengeleyici test edildi.

Bölgeler arası yük dengeleyici hakkında daha fazla bilgi için bkz.:
> [!div class="nextstepaction"]
> [Bölgeler arası yük dengeleyici (Önizleme)](cross-region-overview.md)
