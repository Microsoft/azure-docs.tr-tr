---
title: Portal kullanarak Internet çözümleyici testi oluşturma | Microsoft Docs
description: Bu makalede, ilk Internet Çözümleyicisi testinizi oluşturmayı öğrenin.
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 64bbed9b558d4c20889b28a5247e2113d20daa77
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501774"
---
# <a name="create-an-internet-analyzer-test-using-portal-preview"></a>Portal kullanarak Internet çözümleyici testi oluşturma (Önizleme)

Azure portal veya [CLI](internet-analyzer-cli.md)kullanarak Internet çözümleyici kaynağı oluşturmanın iki yolu vardır. Bu bölüm, Portal deneyimimizi kullanarak yeni bir Azure Internet çözümleyici kaynağı oluşturmanıza yardımcı olur.

> [!IMPORTANT]
> Bu genel önizleme bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılmamalıdır. Belirli özellikler desteklenmiyor olabilir, kısıtlı yeteneklere sahip olabilir veya tüm Azure konumlarında mevcut olmayabilir. Ayrıntılar için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Başlamadan önce

Genel Önizleme Genel kullanım için kullanılabilir; Ancak, veri depolama önizleme sırasında *ABD Batı 2* sınırlıdır.

## <a name="basics"></a>Temel Bilgiler

1. **Önizlemeye katılmak nasıl yaparım?** , [Azure Internet Çözümleyicisi hakkında SSS](internet-analyzer-faq.md)bölümündeki yönergeleri izleyerek Internet Çözümleyicisi önizleme erişimi alın.
2. [Azure Portal](https://preview.portal.azure.com)giriş sayfasından **+ kaynak oluştur ' a**tıklayın. Internet Çözümleyicisi şu anda yalnızca Azure portal önizleme sürümünde kullanılabilir.
3. **Yeni** sayfada *Market 'te arama* yapın alanında "Internet Analyzer" ifadesini aratın.
4. **Internet Çözümleyicisi (Önizleme)** seçeneğine tıklayın. Yayımcının *Microsoft* olduğundan ve kategorinin *ağ*olduğundan emin olun.
5. Internet Çözümleyicisi **(Önizleme)** sayfasında, **Oluştur** ' a tıklayarak **Internet Çözümleyicisi oluştur** sayfasını açın.
6. Internet Çözümleyicisi kaynağınız için aşağıdaki yapılandırma ayarlarını belirtin:

    * **Abonelik:** Yeni Internet çözümleyici kaynağını barındırmak için Azure aboneliği. ***Önizleme erişimi istemek için kullanılan abonelik KIMLIĞINI kullanın.***
    * **Kaynak grubu:** Yeni Internet Çözümleyicisi kaynağının oluşturulacağı Azure Kaynak grubu. Mevcut bir kaynak grubunuz yoksa yeni bir tane oluşturabilirsiniz.
    * **Ad:** Yeni Internet Çözümleyicisi kaynak profilinin adı.
    * **Bölge:** Kaynağın oluşturulacağı Azure ortak bölgesi. Önizleme süresince yalnızca *ABD Batı 2* kullanılabilir.

7. Profil ayarlarınızı belirtmeyi tamamladığınızda, **gözden geçir + oluştur**' a tıklayın.

## <a name="configuration"></a>Yapılandırma

Temel adımları tamamlamak, bir testi yapılandırma ve JavaScript istemcisini katıştırma için bir önkoşuldur. Bir profil oluşturduktan sonra, ilk testinizi yapılandırmak için **ayarlar > yapılandırma** bölümüne gidin.

1. Test **adı** kutusuna testinize bir ad verin.
2. **Açıklama** alanına testiniz için bir açıklama ekleyin.
3. **Uç noktayı Yapılandır** ' a tıklayın, sağ taraftaki bir sekme görünür. Yapılandırmak istediğiniz uç nokta türünü seçin-tek bir Azure bölgesi, birden çok Azure bölgesi veya özel uç nokta.

    >
    ***Önceden yapılandırılmış uç noktalar: tek ve birden çok Azure bölge birleşimleri***
    * [Önceden yapılandırılmış bir Azure uç noktası listesinden](internet-analyzer-faq.md)bölge veya bölge kümesi seçin.
    * Ardından, değerlendirmek istediğiniz uygulama veya içerik teslimi mimarisinin türünü seçin.
        * Tek Azure bölgesi: site hızlandırma ([Azure ön kapısı](https://azure.microsoft.com/services/frontdoor/)), statik içerik önbelleği ([Microsoft için Azure CDN](https://azure.microsoft.com/services/cdn/)) veya hiçbiri.
        * Birden çok Azure bölgesi: site hızlandırma ([Azure ön kapısı](https://azure.microsoft.com/services/frontdoor/)), DNS ([Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/))  

    ***Özel uç noktalar***
    * [Özel uç nokta oluştur](internet-analyzer-custom-endpoint.md) sayfasındaki yönergeleri izleyin.
    * Portalda tek piksellik görüntünün HTTPS URL konumunu yapıştırın.
    >

4. Son noktayı testinize eklemek için **Ekle** ' ye tıklayın.
5. İkinci uç noktanızı yapılandırmak için 1-4 arasındaki adımları yineleyin. Uç nokta B her zaman uç noktaya göre ölçülür-uç noktaları yapılandırırken, hangi uç noktanın test denetiminiz olması gerektiğini göz önünde bulundurun.
6. Testinizi kaydetmek için **Kaydet** düğmesine tıklayın. Bir testi kaydettikten sonra, belirli bir testin uç noktalarını artık düzenleyemezsiniz.
7. Başlatmak istediğiniz test (ler) i seçin ve **teste başla**' ya tıklayın. Bu, testinizin ***durumunu*** ***çalışır***olarak değiştirecek. Testleri dilediğiniz zaman başlatabilirsiniz, ancak testin ölçümleri toplamaya başlaması için JavaScript istemcisinin katıştırılması gerekir.
8. Herhangi bir noktada daha fazla test ekleyin. Benzersiz JavaScript istemcisinin bir test oluşturuluncaya kadar oluşturulmadığını unutmayın.

## <a name="embed-client"></a>Istemciyi Ekle

Herhangi bir teste başlamak için JavaScript istemcisinin Web uygulamanıza katıştırılması gerekir. En az bir testi yapılandırdıktan sonra, **gözden geçir + oluştur**' a tıklayın, **Ayarlar > yapılandırma**' ya gidin ve JavaScript istemcisini kopyalayın. Özel yönergeler [Internet Çözümleyicisi Istemci ekleme](internet-analyzer-embed-client.md) sayfasında bulunabilir.  

## <a name="next-steps"></a>Sonraki adımlar

* [Internet ÇÖZÜMLEYICISI SSS](internet-analyzer-faq.md) makalesini okuyun
* [Internet Çözümleyicisi istemcisini](internet-analyzer-embed-client.md) katıştırma ve [özel bir uç nokta](internet-analyzer-custom-endpoint.md)oluşturma hakkında daha fazla bilgi edinin.
