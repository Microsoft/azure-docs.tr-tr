---
title: 'Hızlı başlangıç: portalda bir arama hizmeti oluşturma'
titleSuffix: Azure Cognitive Search
description: Azure portal bir Azurecognsisel arama kaynağı sağlayın. Kaynak grupları, bölgeler ve SKU veya fiyatlandırma katmanı seçin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 75a7cce55627a981d0d0b0f23a119fac6de920ef
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112109"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Hızlı başlangıç: portalda bir Azure Bilişsel Arama hizmeti oluşturma

Azure Bilişsel Arama, özel uygulamalara bir arama deneyimi eklemek için kullanılan tek başına kaynaktır. Azure Bilişsel Arama diğer Azure hizmetleriyle kolayca tümleştirilebilse de, bunu tek başına bir bileşen olarak kullanabilir veya ağ sunucularındaki uygulamalarla veya diğer bulut platformlarında çalışan yazılımlarla tümleştirebilirsiniz.

Bu makalede [Azure Portal](https://portal.azure.com/)kaynak oluşturmayı öğrenin.

[![animasyonlu GIF](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

PowerShell’i mi tercih ediyorsunuz? Azure Resource Manager [hizmet şablonunu](https://azure.microsoft.com/resources/templates/101-azure-search-create/) kullanın. Kullanmaya başlama hakkında yardım için bkz. [PowerShell Ile Azure bilişsel arama yönetme](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>Abone olma (ücretsiz veya ücretli)

[Ücretsiz bir Azure hesabı açın](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ve ücretli Azure hizmetlerini denemek için ücretsiz krediler kullanın. Krediler bittikten sonra hesabı tutun ve Web Siteleri gibi ücretsiz Azure hizmetlerini kullanmaya devam edin. Açıkça ayarlarınızı değiştirip ücretlendirme istemediğiniz sürece kredi kartınız asla ücretlendirilmez.

Alternatif olarak, [MSDN abone avantajlarınızı etkinleştirin](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). MSDN aboneliği size her ay ücretli Azure hizmetleri için kullanabileceğiniz krediler verir. 

## <a name="find-azure-cognitive-search"></a>Azure Bilişsel Arama bulun

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Sol üst köşedeki artı işaretine ("+ kaynak oluştur") tıklayın.
3. "Azure Bilişsel Arama" bulmak için arama çubuğunu kullanın veya **Web** > **Azure bilişsel arama**aracılığıyla kaynağa gidin.

![Portalda kaynak oluşturma](./media/search-create-service-portal/find-search3.png "Portalda kaynak oluşturma")

## <a name="choose-a-subscription"></a>Abonelik seçin

Abonelik KIMLIĞINI ve kaynak grubunu ayarlama ilk adımdır. Birden fazla aboneliğiniz varsa, veri veya dosya depolama hizmetleri de içeren bir abonelik seçin. Azure Bilişsel Arama, Azure Tablo ve BLOB depolama, SQL veritabanı ve Dizin [*oluşturucular*](search-indexer-overview.md)aracılığıyla Dizin oluşturma için Azure Cosmos DB, ancak yalnızca aynı abonelik kapsamındaki hizmetler için otomatik olarak açabilir.

## <a name="set-a-resource-group"></a>Kaynak grubu ayarlama

Kaynak grubu gereklidir ve maliyetler dahil olmak üzere kaynakları yönetmek için faydalıdır. Bir kaynak grubu, bir hizmetten veya birlikte kullanılan birden fazla hizmetten oluşabilir. Örneğin, Azure Cosmos DB bir veritabanının dizinini oluşturmak için Azure Bilişsel Arama kullanıyorsanız, her iki hizmeti de yönetim amaçlarıyla aynı kaynak grubunun parçası yapabilirsiniz. 

Kaynakları tek bir grupta birleştirmemişse veya mevcut kaynak grupları ilişkisiz çözümlerde kullanılan kaynaklarla doldurulduysa, yalnızca Azure Bilişsel Arama kaynağınız için yeni bir kaynak grubu oluşturun. 

![Yeni bir kaynak grubu oluşturun](./media/search-create-service-portal/new-resource-group.png "Yeni bir kaynak grubu oluşturma")

Zaman içinde, geçerli ve tahmini maliyetlerin tümünü izleyebilirsiniz (ekran görüntüsünde gösterildiği gibi) veya tek tek kaynakların ücretlerini görüntülemek için aşağı kaydırın. Aşağıdaki ekran görüntüsünde, en sonunda birden çok kaynağı tek bir grupta birleştirdiğinizde görmeyi bekletirebileceğiniz maliyet bilgilerinin türü gösterilmektedir.

![Kaynak grubu düzeyinde maliyetleri yönetin](./media/search-create-service-portal/resource-group-cost-management.png "Kaynak grubu düzeyinde maliyetleri yönetin")

> [!TIP]
> Kaynak grupları temizleme işlemini basitleştirir çünkü bir grup silindiğinde aynı zamanda içindeki hizmetler de silinir. Birden fazla hizmet kullanan prototip projeler için, tüm bunların aynı kaynak grubuna yerleştirilmesi, proje bittikten sonra temizleme işlemini kolaylaştırır.

## <a name="name-the-service"></a>Hizmeti adlandırın

Örnek ayrıntıları ' nda **URL** alanında bir hizmet adı belirtin. Ad, API çağrılarının verildiği URL uç noktasının bir parçasıdır: `https://your-service-name.search.windows.net`. Örneğin, uç noktanın `https://myservice.search.windows.net`istiyorsanız `myservice`girersiniz.

Hizmet adı gereksinimleri:

* search.windows.net ad alanı içinde benzersiz olmalıdır
* 2 ila 60 karakter uzunluğunda olmalıdır
* Küçük harfleri, rakamları veya kısa çizgileri ("-") kullanın
* İlk 2 karakter olarak veya sondaki tek karakter olarak tire ("-") kullanmaktan kaçının
* Hiçbir yerde art arda tire ("--") kullanmayın

> [!TIP]
> Birden çok hizmet kullandığınızı düşünüyorsanız, hizmet adına bölge (veya konum) bir adlandırma kuralı olarak dahil etmenizi öneririz. Aynı bölgedeki hizmetler ücretsiz olarak verileri değiş tokuş edebilir, bu nedenle Azure Bilişsel Arama Batı ABD ve aynı zamanda Batı ABD de başka hizmetleriniz varsa, `mysearchservice-westus` gibi bir ad, kaynakları birleştirme veya iliştirme konusunda karar verirken Özellikler sayfasına seyahat edebilir.

## <a name="choose-a-location"></a>Bir konum seçin

Bir Azure hizmeti olarak Azure Bilişsel Arama dünyanın dört bir yanındaki veri merkezlerinde barındırılabilir. Desteklenen bölgelerin listesi [fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/search/)bulunabilir. 

Birden fazla hizmet için aynı konumu seçerek bant genişliği ücretlerini en aza indirebilir veya önleyebilirsiniz. Örneğin, başka bir Azure hizmeti (Azure depolama, Azure Cosmos DB, Azure SQL veritabanı) tarafından sağlanmış olan verileri dizinlendirirken, Azure Bilişsel Arama hizmetinizi aynı bölgede oluşturmak bant genişliği ücretlenmesini önler (giden veriler için bir ücret alınmaz Hizmetler aynı bölgedeyse).

Ayrıca, AI zenginleştirme kullanıyorsanız, hizmetinizi bilişsel hizmetler ile aynı bölgede oluşturun. *Azure bilişsel arama ve bilişsel hizmetler 'in aynı bölgedeki birlikte bulunması, AI zenginleştirme için bir gereksinimdir*.

> [!Note]
> Orta Hindistan, yeni hizmetler için şu anda kullanılamıyor. Zaten Orta Hindistan olan hizmetler için, kısıtlama olmadan ölçeği ölçeklendirebilirsiniz ve hizmetiniz bu bölgede tam olarak desteklenmektedir. Bu bölgedeki kısıtlama geçicidir ve yalnızca yeni hizmetlerle sınırlıdır. Kısıtlama artık geçerli olmadığında bu notun kaldırılması gerekir.

## <a name="choose-a-pricing-tier-sku"></a>Fiyatlandırma Katmanı (SKU) seçin

[Azure bilişsel arama şu anda birden çok fiyatlandırma katmanlarında sunulmaktadır](https://azure.microsoft.com/pricing/details/search/): ücretsiz, temel veya standart. Her katmanın kendi [kapasitesi ve sınırları](search-limits-quotas-capacity.md) vardır. Yönergeler için [Fiyatlandırma katmanı veya SKU seçme](search-sku-tier.md) bölümüne bakın.

Temel ve standart, üretim iş yükleri için en yaygın seçimlerdir, ancak çoğu müşteri ücretsiz hizmetle başlar. Katmanlar arasındaki temel farklılıklar bölüm boyutu ve hız, oluşturabileceğiniz nesne sayısı için sınırlar olur.

Hizmet oluşturulduktan sonra bir fiyatlandırma katmanının değiştirilemeyeceğini unutmayın. Daha yüksek veya daha düşük bir katmana ihtiyacınız olursa, hizmeti yeniden oluşturmanız gerekir.

## <a name="create-your-service"></a>Sitenizi oluşturma

Gerekli girişleri sağladıktan sonra, devam edin ve hizmeti oluşturun. 

![Hizmeti gözden geçirin ve oluşturun](./media/search-create-service-portal/new-service3.png "Hizmeti gözden geçirin ve oluşturun")

Hizmetiniz, Azure bildirimleri aracılığıyla izleyebilmeniz için dakikalar içinde dağıtılır. Gelecekte kolay erişim sağlamak için hizmeti panonuza sabitlemeyi göz önünde bulundurun.

![Hizmeti izleme ve sabitleme](./media/search-create-service-portal/monitor-notifications.png "Hizmeti izleme ve sabitleme")

## <a name="get-a-key-and-url-endpoint"></a>Anahtar ve URL uç noktası al

Portalı kullanmıyorsanız, yeni hizmetinize programlı erişim, URL uç noktasını ve bir kimlik doğrulama API anahtarı sağlamanızı gerektirir.

1. **Genel bakış** sayfasında, sayfanın sağ tarafındaki URL uç noktasını bulup kopyalayın.

2. **Anahtarlar** sayfasında, yönetici anahtarlarından birini (eşdeğerdir) kopyalayın. Yönetim API 'si-hizmetinize nesneleri oluşturmak, güncelleştirmek ve silmek için anahtarlar gereklidir. Buna karşılık sorgu anahtarları, Dizin içeriğine okuma erişimi sağlar.

   ![URL uç noktası ile hizmete genel bakış sayfası](./media/search-create-service-portal/get-url-key.png "URL uç noktası ve diğer hizmet ayrıntıları")

Portal tabanlı görevler için bir uç nokta ve anahtar gerekli değildir. Portal, Azure Bilişsel Arama kaynağına yönetici haklarıyla zaten bağlı. Portal Kılavuzu için [hızlı başlangıç: portalda bir Azure bilişsel arama dizini oluşturun](search-get-started-portal.md).

## <a name="scale-your-service"></a>Hizmetinizi ölçeklendirme

Hizmetiniz sağlandıktan sonra ihtiyaçlarınızı karşılayacak şekilde ölçeklendirilebilir. Azure Bilişsel Arama hizmetiniz için standart katmanı seçerseniz, hizmetinizi iki boyutta ölçeklendirebilirsiniz: çoğaltmalar ve bölümler. Temel katmanı seçtiyseniz yalnızca çoğaltmalar ekleyebilirsiniz. Ücretsiz hizmeti sağladıysanız ölçek kullanılamaz.

***Bölümler***, hizmetinizin daha fazla belge depolamasına ve daha fazla belgede arama yapmasına olanak sağlar.

***Çoğaltmalar***, hizmetinizin daha yüksek arama sorgusu yükünü işlemesine olanak sağlar.

Kaynak eklemek aylık faturanız artırır. [Fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/), fatura konusunda kaynak eklemenin getirdiği sonuçları anlamanıza yardımcı olabilir. Kaynakları yüke göre ayarlayabildiğinizi unutmayın. Örneğin, tam bir ilk dizin oluşturmak için kaynakları artırabilir ve ardından artımlı dizin oluşturmak için daha uygun bir düzeye indirebilirsiniz.

> [!Important]
> Bir hizmetin [salt okunur SLA için 2 çoğaltması ve okuma/yazma SLA’sı için 3 çoğaltması](https://azure.microsoft.com/support/legal/sla/search/v1_0/) olmalıdır.

1. Azure portalında arama hizmeti sayfanıza gidin.
2. Sol gezinti bölmesinde **Ayarlar** > **Ölçek** seçeneklerini belirleyin.
3. Her iki türdeki kaynakları eklemek için kaydırma çubuğunu kullanın.

![Kapasite Ekle](./media/search-create-service-portal/settings-scale.png "Çoğaltmalar ve bölümler aracılığıyla kapasite ekleme")

> [!Note]
> Bölüm başına depolama ve hız daha yüksek katmanlarda artar. Daha fazla bilgi için bkz. [Kapasite ve sınırlar](search-limits-quotas-capacity.md).

## <a name="when-to-add-a-second-service"></a>Ne zaman ikinci bir hizmet eklenir?

Çoğu müşteri [, doğru kaynak dengesini](search-sku-tier.md)sağlayan bir katmanda sağlanan tek bir hizmeti kullanır. Bir hizmet, her bir dizinin diğerinden yalıtıldığı, [seçtiğiniz maksimum katman sınırlarına](search-capacity-planning.md) tabi olan birden fazla dizin barındırabilir. Azure Bilişsel Arama, istekler yalnızca bir dizine yönlendirilebilir ve aynı hizmette bulunan diğer dizinlerden yanlışlıkla veya kasıtlı olarak veri alma olasılığını en aza indirir.

Müşterilerin çoğu yalnızca bir hizmet kullansa da, işletim gereksinimleri arasında aşağıdakiler yer alıyorsa hizmet yedekliliği gerekebilir:

* Olağanüstü durum kurtarma (veri merkezi kesintisi). Azure Bilişsel Arama, kesinti durumunda anlık yük devretme sağlamaz. Öneriler ve kılavuz için bkz. [Hizmet yönetim](search-manage.md).
* Çok kiracılı modelleme araştırması, ek hizmetlerin optimum tasarım olduğunu belirlemiştir. Daha fazla bilgi için bkz. [Çoklu kiracı tasarımı](search-modeling-multitenant-saas-applications.md).
* Küresel olarak dağıtılan uygulamalarda, uygulamanızın Uluslararası trafiğinin gecikme süresini en aza indirmek için birden çok bölgede Azure Bilişsel Arama örneği gerekebilir.

> [!NOTE]
> Azure Bilişsel Arama, dizin oluşturma ve sorgulama işlemlerini ayırt edemez; Bu nedenle, ayrılmış iş yükleri için hiçbir şekilde birden çok hizmet oluşturmazsınız. Bir dizin her zaman oluşturulduğu hizmette sorgulanır (bir hizmette bir dizini oluşturup başka bir hizmete kopyalayamazsınız).

Yüksek düzeyde kullanılabilirlik için ikinci bir hizmet gerekmez. Aynı hizmette 2 veya daha fazla çoğaltma kullandığınızda sorguların yüksek kullanılabilirliği elde edilir. Çoğaltma güncelleştirmeleri sıralıdır, bu da hizmet güncelleştirmesi kullanıma alındığında en az bir işlem çalışır. Çalışma süresi hakkında daha fazla bilgi için bkz. [hizmet düzeyi sözleşmeleri](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Sonraki adımlar

Bir hizmet sağlamaktan sonra, ilk dizininizi oluşturmak için portalda devam edebilirsiniz.

> [!div class="nextstepaction"]
> [Hızlı başlangıç: portalda Azure Bilişsel Arama dizini oluşturma](search-get-started-portal.md)
