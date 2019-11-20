---
title: Azure Sentinel 'de not defterlerini kullanma özellikleri | Microsoft Docs
description: Bu makalede, Azure Sentinel arama özellikleri ile Not defterlerinin nasıl kullanılacağı açıklanır.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: rkarlin
ms.openlocfilehash: ba22cc3db0ca50a292ddef4d0d646f8578c15cd4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489181"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Güvenlik tehditleri aramak için Jupyıter not defterlerini kullanma

Azure Sentinel 'in temeli veri deposudur; yüksek performanslı sorgulamayı, dinamik şemayı birleştirir ve çok büyük veri birimlerine ölçeklendirir. Azure portal ve tüm Azure Sentinel araçları, bu veri deposuna erişmek için ortak bir API kullanır. Aynı API, [jupi](https://jupyter.org/) Not defterleri ve Python gibi dış araçlar için de kullanılabilir. Portalda birçok ortak görev gerçekleştirilebilirken, Jupyıter bu verilerle yapabileceklerinizi kapsamını genişletir. Makine öğrenimi, görselleştirme ve veri analizi için çok büyük bir kitaplık koleksiyonuyla tam programlama ile birleşir. Bu öznitelikler, Jupa 'nın güvenlik araştırması ve araştırma için etkileyici bir araç haline getirir.

![Örnek Not defteri](./media/notebooks/sentinel-notebooks-map.png)

Jupi deneyimini Azure portal ile tümleştirdik, böylece verilerinizi çözümlemek için not defterlerini oluşturup çalıştırabilirsiniz. *Ktomagic* kitaplığı, Azure Sentinel 'ten sorgular almanıza ve bunları doğrudan bir not defteri içinde çalıştırmanıza olanak tanıyan tutkalla 'yi sağlar. Sorgular [kusto sorgu dilini](https://kusto.azurewebsites.net/docs/query/index.html)kullanır. Microsoft 'un Güvenlik analistleri tarafından geliştirilen birkaç not defteri, Azure Sentinel ile paketlenmiştir. Bu not defterlerinden bazıları belirli bir senaryo için oluşturulmuştur ve olduğu gibi kullanılabilir. Diğerleri, kendi Not defterlerinizde kullanmak üzere kopyalayabileceğiniz veya bunları uyarlayabileceğiniz teknikleri ve özellikleri göstermek için örnek olarak tasarlanmıştır. Diğer Not defterleri de Azure Sentinel Community GitHub 'dan içeri aktarılabilir.

Tümleşik Jupyıter deneyimi, not defterlerini depolamak, paylaşmak ve yürütmek için [Azure Notebooks](https://notebooks.azure.com/) kullanır. Bilgisayarınızda bir Python ortamınız ve jupi varsa veya Azure Databricks gibi diğer JupterHub ortamlarında bu not defterlerini yerel olarak da çalıştırabilirsiniz.

Not defterlerinin iki bileşeni vardır:

- Sorgu ve kod girip çalıştırdığınız ve yürütme sonuçlarının görüntülendiği tarayıcı tabanlı arabirim.
- Kodu ayrıştırmaktan ve yürütmeden sorumlu bir *çekirdek* . 

Azure Notebooks, varsayılan olarak, bu çekirdek Azure *ücretsiz bulut işlem ve depolama*üzerinde çalışır. Not defterleriniz karmaşık makine öğrenimi modelleri veya görselleştirmeler içeriyorsa, [veri bilimi sanal makineleri](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (dsvm) gibi daha güçlü, ayrılmış işlem kaynakları kullanmayı düşünün. Hesabınızı paylaşmayı seçmediğiniz takdirde, hesabınızdaki Not defterleri özel tutulur.

Azure Sentinel Not defterleri, Pandas, Matplotlib, bokeh ve diğerleri gibi birçok popüler Python kitaplıklarını kullanır. Arasından seçim yapabileceğiniz çok sayıda başka Python paketi vardır:

- Görselleştirmeler ve grafikler
- Veri işleme ve analiz
- İstatistikler ve sayısal bilgi işlem
- Machine Learning ve derin öğrenme

Ayrıca, [msticter](https://github.com/Microsoft/msticpy/)adlı bir pakette bazı açık kaynaklı jupi güvenlik araçları da yayımladık. Bu paket, dahil edilen birçok Not defteri içinde kullanılır. Msticticizme araçları, araştırma ve araştırma için Not defteri oluşturma konusunda yardımcı olmak üzere tasarlanmıştır ve yeni özellikler ve geliştirmeler üzerinde etkin bir şekilde çalışıyoruz.

İlk not defterleri şunları içerir:

- **Kılavuzlu araştırma-Işlem uyarıları**: etkilenen konaktaki veya konaklardaki etkinlikleri çözümleyerek uyarıları hızlı bir şekilde önceliklendirmenize olanak tanır.
- **Kılavuzlu arama-Windows konak Gezgini**: hesap etkinliğini, işlem yürütmelerini, ağ etkinliğini ve bir konaktaki diğer olayları incelemenize olanak sağlar.
- **Kılavuzlu arama-Office365-keşfetme**: çok sayıda Office 365 veri kümesinde şüpheli Office 365 etkinliği için Hunt.

[Azure Sentinel Community GitHub deposu](https://github.com/Azure/Azure-Sentinel) , Microsoft tarafından oluşturulan veya topluluk tarafından katkıda bulunulan, gelecek Azure Sentinel Not defterlerinin konumudur.

Not defterlerini kullanmak için bir Azure Notebooks hesabınızın olması gerekir. Daha fazla bilgi için bkz. [hızlı başlangıç: oturum açın ve Azure Notebooks belgelerinden bir kullanıcı kimliği ayarlayın](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) . Bu hesabı oluşturmak için **Azure Sentinel-Not defterleri**' nde komut çubuğundan **Azure Notebooks için kaydolun** seçeneğini kullanabilirsiniz:

> [!div class="mx-imgBorder"]
>Azure Notebooks seçeneğe ![kaydolun](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

## <a name="view-available-notebooks-from-azure-sentinel"></a>Azure Sentinel 'de kullanılabilir not defterlerini görüntüleme
 
1. Azure portal Azure **sentinel** > **tehdit yönetimi** > **Not defterleri**' ne giderek Azure Sentinel 'in sağladığı not defterlerini görebilirsiniz. 

2. Açıklamalarını, gerekli veri türlerini ve veri kaynaklarını okumak için ayrı ayrı not defterlerini seçin. Örneğin:
    
    > [!div class="mx-imgBorder"]
    > ![başlatma Not defteri](./media/notebooks/sentinel-azure-notebooks-nolaunch.png)

3. [Azure Sentinel Community GitHub deposundaki](https://github.com/Azure/Azure-Sentinel)not defterlerine gitmek Için **Not defterini Başlat** ' ı seçin.

Şu anda bir not defterini doğrudan Azure Sentinel 'ten başlatamayemiyorum. Bunun yerine, GitHub 'daki not defterlerini bir Azure Notebooks projesine kopyalamak için aşağıdaki yordamı kullanın.

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>Azure Sentinel not defterlerini yeni bir Azure Notebooks projesine kopyalayın

Bu yordam, sizin için Azure Sentinel not defterlerini içeren bir Azure Notebooks projesi oluşturur. Ardından, not defterlerini olduğu gibi çalıştırabilir veya bunlarda değişiklikler yapabilir ve bunları çalıştırabilirsiniz.

1. Azure portal **Azure Sentinel** > **tehdit yönetimi** > **Not defterleri** ' ne gidin ve ardından komut çubuğundan **not defterlerini Kopyala** ' yı seçin:
  
    > [!div class="mx-imgBorder"]
    >![kopya Not defteri seçeneği](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. Aşağıdaki iletişim kutusu göründüğünde, GitHub deposunu Azure Notebooks projenize kopyalamak için **Içeri aktar** ' ı seçin. Mevcut bir Azure Notebooks hesabınız yoksa bir tane oluşturmanız ve oturum açmanız istenir.

   ![Not Defteri al](./media/notebooks/sentinel-notebooks-clone.png)

3. **GitHub deposunu karşıya yükle** iletişim kutusunda, bu seçenek bağlı GitHub depoları ' na başvurduğundan **özyinelemeli olarak Kopyala** ' yı seçmeyin. Proje adı için, varsayılan adı kullanın veya yeni bir ad yazın. Ardından **Içeri aktar** ' a tıklayarak GitHub içeriğini kopyalamaya başlayın, bu da tamamlanması birkaç dakika sürebilir.

   ![Not Defteri al](./media/notebooks/sentinel-create-project.png)

4. Yeni oluşturduğunuz projeyi açın ve not defterlerini görmek için **Not defterleri** klasörünü açın. Örneğin:

   ![Depoyu içeri aktar](./media/notebooks/sentinel-open-notebook1.png)

Daha sonra Azure Notebooks not defterlerini çalıştırabilirsiniz. Azure Sentinel 'deki bu not defterlerine geri dönmek için **Azure Sentinel-Not defterleri**' nde komut çubuğundan **not defterlerinize git** ' i seçin:

> [!div class="mx-imgBorder"]
>![Not defterleri seçeneğine gidin](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>Araya için not defterlerini kullanma

Her not defteri, bir Hunt veya araştırma gerçekleştirme adımlarında size yol gösterir. Kitaplıkları ve Not defteri için gereken diğer bağımlılıklar, Not defterinin kendisinden veya basit bir yapılandırma yordamıyla yüklenebilir. Not defteri projenizi Azure Sentinel aboneliğinize geri bağlayan yapılandırma, önceki adımlarda otomatik olarak sağlanır.

1. Zaten Azure Notebooks değilseniz, **Azure Sentinel-Not defterleri**' nde komut çubuğundan **not defterlerinize git** seçeneğini kullanabilirsiniz:
    
    > [!div class="mx-imgBorder"]
    >![Not defterleri seçeneğine gidin](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    Azure Notebooks, **Projelerim**' nı, ardından Azure Sentinel not defterlerini ve son olarak **Not defterleri** klasörünü içeren projeyi seçin.
    
2. Bir not defteri açmadan önce, not defterlerini çalıştırmak için varsayılan olarak ücretsiz Işlem seçilidir:
    
   ![Not defteri seçin](./media/notebooks/sentinel-open-notebook2.png)
    
    Giriş bölümünde açıklanacak şekilde kullanmak üzere bir veri bilimi sanal makinesi (DSVM) yapılandırdıysanız, ilk not defterini açmadan önce DSVM 'yi seçin ve kimlik doğrulaması yapın. 

3. Açmak için bir not defteri seçin.
    
    Bir not defterini ilk açışınızda bir çekirdek sürümü seçmeniz istenebilir. İstenirse çekirdek sürümünü **çekirdek** >  **değişiklik çekirdeğini**seçerek en az 3,6 olan sürümü seçebilirsiniz. Seçilen çekirdek sürümü, Not defteri penceresinin sağ üst kısmında görüntülenir:
    
   ![Not defteri seçin](./media/notebooks/sentinel-select-kernel.png)

4. İndirdiğiniz not defterinde herhangi bir değişiklik yapmadan önce, özgün not defterinin bir kopyasını oluşturmak ve kopyada çalışmak iyi bir fikirdir. Bunu yapmak için **dosya** > **bir kopya oluştur**' u seçin. Kopyaların üzerinde çalışma, verilerinizin üzerine yazmadan, daha sonraki Not defteri sürümlerine güvenle güncelleştirme yapmanızı sağlar.
    
    Artık seçili Not defterini çalıştırmaya veya düzenlemeye hazırsınız.

Öneri

- Azure Sentinel 'de verileri sorgulamaya yönelik hızlı bir giriş için ana **Not defterleri** klasöründeki [getstarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) not defterine bakın. 

- **Örnek Not defterleri** alt klasöründe ek örnek Not defterleri bulacaksınız. Bu örnek Not defterleri verilerle birlikte kaydedildiğinden, amaçlanan çıktıyı görmeniz daha kolay olur. Bu not defterlerini [nbviewer](https://nbviewer.jupyter.org/)'da görüntülemeniz önerilir. 

- **HOWTOs** klasörü, örnek olarak açıklandığı gibi not defterlerini içerir, örneğin: varsayılan Python sürümünüzü ayarlama, dsvm yapılandırma, bir not defterinden Azure Sentinel yer işaretlerini oluşturma ve diğer konular.

Sağlanan Not defterleri, kendi Not defterlerinizin geliştirilmesinde kullanabileceğiniz hem faydalı araçlar, hem de çizimler ve kod örnekleri olarak hazırlanmıştır.

Öneriler, özellikler için istekler, katkıda bulunulan Not defterleri, hata raporları ya da geliştirmeler ve mevcut not defterlerine ekler hakkında geri bildirimde bulunun. Bir sorun veya çatal oluşturmak için [Azure Sentinel Community GitHub](https://github.com/Azure/Azure-Sentinel) ' a gidin ve bir katkıyı karşıya yükleyin.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Sentinel 'de Jupyıter not defterlerini kullanmaya nasıl başlacağınız hakkında daha fazla öğrenirsiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Tehditler için proaktif araya](hunting.md)
- [Avlarken ilginç bilgileri kaydetmek için yer işaretlerini kullanın](bookmarks.md)
