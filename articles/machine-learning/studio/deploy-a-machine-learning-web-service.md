---
title: Bir web hizmetini dağıtma
titleSuffix: ML Studio (classic) - Azure
description: Bir eğitim denemesini tahmine dayalı bir deneyiye dönüştürme, dağıtıma hazırlama ve Azure Machine Learning Studio (klasik) Web hizmeti olarak dağıtma.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 4324d33a9da66c5f49645e1c8014bd5f57d37bd9
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837739"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Azure Machine Learning Studio (klasik) Web hizmeti dağıtma

Azure Machine Learning Studio (klasik), tahmine dayalı analitik çözüm oluşturmanıza ve test etmenize olanak sağlar. Daha sonra çözümü bir Web hizmeti olarak dağıtabilirsiniz.

Machine Learning Studio (klasik) Web Hizmetleri, bir uygulama ile Machine Learning Studio (klasik) iş akışı Puanlama modeli arasında bir arabirim sağlar. Dış uygulama, Machine Learning Studio (klasik) iş akışı Puanlama modeliyle gerçek zamanlı olarak iletişim kurabilir. Machine Learning Studio (klasik) Web hizmetine yapılan bir çağrı, bir dış uygulamaya tahmin sonuçları döndürür. Bir web hizmetine çağrı yapmak için web hizmeti dağıtılırken oluşturulan bir API anahtarını geçirirsiniz. Machine Learning Studio (klasik) Web hizmeti, Web programlama projeleri için popüler bir mimari seçimi olan REST 'i temel alır.

Azure Machine Learning Studio klasik sürümü iki tür Web hizmetine sahiptir:

* İstek-yanıt hizmeti (RR): tek bir veri kaydına puan veren düşük gecikmeli, yüksek düzeyde ölçeklenebilir bir hizmet.
* Toplu yürütme hizmeti (BES): veri kayıtlarını toplu olarak alan bir zaman uyumsuz hizmettir.

BES girişi, RRS’nin kullandığı veri girişi gibidir. Aralarındaki temel fark, BES'nin Azure Blob depolama, Azure Tablo depolama, Azure SQL Veritabanı, HDInsight (hive sorgusu) ve HTTP kaynakları gibi çeşitli kaynaklardan bir kayıt bloğunu okumasıdır.

Üst düzey bir görünümden, modelinizi üç adımda dağıtırsınız:

* **[Eğitim denemesi oluşturma]** -klasik Studio sürümünde, büyük bir yerleşik makine öğrenimi algoritması kümesi kullanarak, sağladığınız eğitim verilerini kullanarak tahmine dayalı analiz modelini eğitebilirsiniz ve test edebilirsiniz.
* **[Tahmine dayalı bir deneyiye Dönüştür]** . modelinizin mevcut verilerle eğitilirken ve yeni veri puanlaması için kullanmaya hazırsanız, tahminlere yönelik denemenizi hazırlayın ve kolaylaştırın.
* **[Yeni Web hizmeti]** veya **[Klasik Web hizmeti]** olarak **dağıtma** -tahmine dayalı denemenizi bir Azure Web hizmeti olarak dağıttığınızda, kullanıcılar modelinize veri gönderebilir ve modelinizin tahminlerinizi alabilir.

## <a name="create-a-training-experiment"></a>Eğitim denemesi oluşturma

Tahmine dayalı analiz modelini eğmek için Azure Machine Learning Studio klasik sürümünü kullanarak eğitim verilerini yükleme, verileri gereken şekilde hazırlama, makine öğrenimi algoritmaları uygulama ve Sonuçları değerlendirin. Bir deneme üzerinde yineleyebilir ve sonuçları karşılaştırmak ve değerlendirmek için farklı makine öğrenimi algoritmaları deneyebilirsiniz.

Eğitim denemeleri oluşturma ve yönetme işlemi daha kapsamlı bir yerde ele alınmıştır. Daha fazla bilgi için şu makalelere bakın:

* [Azure Machine Learning Studio basit bir deneme oluşturma (klasik)](create-experiment.md)
* [Azure Machine Learning Studio (klasik) ile tahmine dayalı bir çözüm geliştirin](tutorial-part1-credit-risk.md)
* [Eğitim verilerinizi Azure Machine Learning Studio içe aktarın (klasik)](import-data.md)
* [Azure Machine Learning Studio deneme yinelemelerini yönetme (klasik)](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Eğitim denemesini tahmine dayalı bir deneyle Dönüştür

Modelinizi eğittikten sonra, yeni verileri almak için eğitim denemenizi tahmine dayalı bir deneyle dönüştürmeye hazırsınız demektir.

Tahmine dayalı bir deneye dönüştürerek, eğitilen modelinizi bir Puanlama Web hizmeti olarak dağıtılmaya hazırsınız demektir. Web hizmetinin kullanıcıları, modelinize giriş verileri gönderebilir ve modeliniz tahmin sonuçlarını geri gönderir. Tahmine dayalı bir deneyiye dönüştürme yaparken, modelinizin başkaları tarafından kullanılmasını nasıl beklediğinizi göz önünde bulundurun.

Eğitim denemenizi bir tahmine dayalı denemeye dönüştürmek için, deneme tuvalinin alt kısmındaki **Çalıştır** ' a tıklayın, **Web hizmeti ayarla**' yı tıklatın ve tahmine **dayalı Web hizmeti**' ni seçin.

![Puanlama denemesine Dönüştür](./media/publish-a-machine-learning-web-service/figure-1.png)

Bu dönüştürmeyi gerçekleştirme hakkında daha fazla bilgi için bkz. [Azure Machine Learning Studio dağıtım için modelinizi hazırlama (klasik)](convert-training-experiment-to-scoring-experiment.md).

Aşağıdaki adımlarda, tahmine dayalı bir denemeyi yeni bir Web hizmeti olarak dağıtma açıklanır. Denemeyi klasik Web hizmeti olarak da dağıtabilirsiniz.

## <a name="deploy-it-as-a-new-web-service"></a>Yeni bir Web hizmeti olarak dağıtın

Tahmine dayalı denemenin hazırlandığına göre, bunu yeni (Kaynak Yöneticisi tabanlı) bir Azure Web hizmeti olarak dağıtabilirsiniz. Kullanıcılar, Web hizmetini kullanarak modelinize veri gönderebilir ve model tahmine dayalı olarak döndürülür.

Tahmine dayalı denemenize dağıtmak için deneme tuvalinin alt kısmındaki **Çalıştır** ' a tıklayın. Deneme çalışmayı tamamladıktan sonra **Web Hizmeti Dağıt** ' a tıklayın ve **Web Hizmeti Dağıt [Yeni]** ' yi seçin.  Machine Learning Studio (klasik) Web hizmeti portalının dağıtım sayfası açılır.

> [!NOTE] 
> Yeni bir Web hizmeti dağıtmak için, Web hizmetini dağıttığınız abonelikte yeterli izinlere sahip olmanız gerekir. Daha fazla bilgi için, [Azure Machine Learning Web Hizmetleri portalını kullanarak bir Web hizmetini yönetme](manage-new-webservice.md)makalesine bakın. 

### <a name="web-service-portal-deploy-experiment-page"></a>Web Service Portal dağıtımı deneme sayfası

Deneme dağıtımı sayfasında, Web hizmeti için bir ad girin.
Fiyatlandırma planı seçin. Mevcut bir fiyatlandırma planınız varsa, bunu seçebilirsiniz; Aksi takdirde hizmet için yeni bir fiyat planı oluşturmanız gerekir.

1. **Fiyat planı** açılan penceresinde, mevcut bir planı seçin veya **Yeni plan Seç** seçeneğini belirleyin.
2. **Plan adı**' nda, faturanızda planı tanımlayacak bir ad yazın.
3. **Aylık plan katmanlarından**birini seçin. Varsayılan bölgeniz ve Web hizmetiniz için plan katmanları varsayılan olarak bu bölgeye dağıtılır.

**Dağıt** ' a tıklayın ve Web hizmetiniz Için **hızlı başlangıç** sayfası açılır.

Web hizmeti hızlı başlangıç sayfası, Web hizmeti oluşturduktan sonra gerçekleştirdiğiniz en yaygın görevlere erişmenizi ve size rehberlik sağlar. Buradan, test sayfası ve kullanma sayfasına kolayca erişebilirsiniz.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Yeni Web hizmetinizi test etme

Yeni Web hizmetinizi test etmek için ortak görevler altında **Web hizmeti testi** ' ne tıklayın. Test sayfasında, Web hizmetinizi bir Istek-yanıt hizmeti (RR) veya toplu yürütme hizmeti (BES) olarak test edebilirsiniz.

RR 'ler test sayfası, deneme için tanımladığınız girdileri, çıkışları ve genel parametreleri görüntüler. Web hizmetini test etmek için, girişler için uygun değerleri el ile girebilir veya test değerlerini içeren bir virgülle ayrılmış değer (CSV) biçimli dosya sağlayabilirsiniz.

RR 'leri kullanarak test etmek için liste görünümü modundan, girişler için uygun değerleri girin ve **Test isteği-yanıtı**' na tıklayın. Tahmin sonuçlarınız, sol taraftaki çıktı sütununda görüntülenir.

![Web hizmetinizi test etmek için uygun değerleri girin](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

BES 'yı test etmek için **Batch**' e tıklayın. Toplu Işlem testi sayfasında, girişte bulunan Araştır ' a tıklayın ve uygun örnek değerleri içeren bir CSV dosyası seçin. Bir CSV dosyanız yoksa ve Machine Learning Studio klasik sürümünü kullanarak tahmine dayalı denemeniz oluşturduysanız, tahmine dayalı denemenize yönelik veri kümesini indirebilir ve kullanabilirsiniz.

Veri kümesini indirmek için Machine Learning Studio klasik sürümünü açın. Tahmine dayalı denemenizi açın ve denemenizin girişine sağ tıklayın. Bağlam menüsünden **veri kümesi** ' ni seçin ve ardından **İndir**' i seçin.

![Veri kümesini Studio (klasik) tuvalinden indirin](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

**Test**' e tıklayın. Toplu yürütme işinin durumu, **Test toplu işleri**altında sağ tarafta görüntülenir.

![Web hizmeti portalı ile Batch yürütme işinizi test etme](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

**Yapılandırma** sayfasında, açıklamayı, başlığı değiştirebilir, depolama hesabı anahtarını güncelleştirebilir ve Web hizmetiniz için örnek verileri etkinleştirebilirsiniz.

![Web hizmetinizi yapılandırma](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Yeni Web hizmetinize erişin

Web hizmetinizi klasik Machine Learning Studio sürümünden dağıttıktan sonra, hizmete veri gönderebilir ve yanıtları programlı bir şekilde alabilirsiniz.

Kullanım **sayfası,** Web hizmetinize erişmek için gereken tüm bilgileri sağlar. Örneğin, hizmet için yetkilendirilmiş erişime izin vermek üzere API anahtarı sağlanır.

Machine Learning Studio (klasik) Web hizmetine erişme hakkında daha fazla bilgi için bkz. [Azure Machine Learning Studio (klasik) Web hizmeti kullanma](consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Yeni Web hizmetinizi yönetin

Yeni Web Hizmetleri Machine Learning Studio (klasik) Web Hizmetleri portalınızı yönetebilirsiniz. [Ana portal sayfasında](https://services.azureml-test.net/), **Web Hizmetleri**' ne tıklayın. Web Hizmetleri sayfasından bir hizmeti silebilir veya kopyalayabilirsiniz. Belirli bir hizmeti izlemek için, hizmete tıklayın ve ardından **Pano**' ya tıklayın. Web hizmetiyle ilişkili toplu işleri izlemek için **Batch Istek günlüğü**' ne tıklayın.

### <a id="multi-region"></a>Yeni Web hizmetinizi birden çok bölgeye dağıtma

Birden çok aboneliğe veya çalışma alanına gerek duymadan, yeni bir Web hizmetini kolayca birden çok bölgeye dağıtabilirsiniz.

Fiyatlandırma bölgeye özeldir, bu nedenle Web hizmetini dağıtacağınız her bölge için bir faturalandırma planı tanımlamanız gerekir.

#### <a name="create-a-plan-in-another-region"></a>Başka bir bölgede plan oluşturma

1. [Microsoft Azure Machine Learning Web hizmetlerinde](https://services.azureml.net/)oturum açın.
2. **Planlar** menü seçeneğine tıklayın.
3. Görünüm üzerinden planlar sayfasında **Yeni**' ye tıklayın.
4. **Abonelik** açılan menüsünde, yeni planın bulunacağı aboneliği seçin.
5. **Bölge** açılan listesinden yeni plan için bir bölge seçin. Seçilen bölge için plan seçenekleri, sayfanın **plan seçenekleri** bölümünde görüntülenecektir.
6. **Kaynak grubu** açılır listesinden plan için bir kaynak grubu seçin. Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure Resource Manager genel bakış](../../azure-resource-manager/resource-group-overview.md).
7. **Plan adı** ' nda planın adını yazın.
8. **Plan seçenekleri**altında, yeni plan için faturalandırma düzeyine tıklayın.
9. **Oluştur**'a tıklayın.

#### <a name="deploy-the-web-service-to-another-region"></a>Web hizmetini başka bir bölgeye dağıtma

1. Microsoft Azure Machine Learning Web Hizmetleri sayfasında, **Web Hizmetleri** menü seçeneğine tıklayın.
2. Yeni bir bölgeye dağıttığınız Web hizmetini seçin.
3. **Kopyala**' ya tıklayın.
4. **Web hizmeti adı**' nda, Web hizmeti için yeni bir ad yazın.
5. **Web hizmeti açıklamasında**, Web hizmeti için bir açıklama yazın.
6. **Abonelik** açılan menüsünde, yeni Web hizmetinin bulunacağı aboneliği seçin.
7. **Kaynak grubu** açılır listesinden Web hizmeti için bir kaynak grubu seçin. Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure Resource Manager genel bakış](../../azure-resource-manager/resource-group-overview.md).
8. **Bölge** açılan listesinden, Web hizmetinin dağıtılacağı bölgeyi seçin.
9. **Depolama hesabı** açılan menüsünde, Web hizmetinin kaydedileceği bir depolama hesabı seçin.
10. **Fiyat planı** açılan menüsünde, adım 8 ' de seçtiğiniz bölgede bir plan seçin.
11. **Kopyala**' ya tıklayın.

## <a name="deploy-it-as-a-classic-web-service"></a>Bunu klasik Web hizmeti olarak dağıtma

Tahmine dayalı deneme yeterli şekilde hazırlandığına göre, bunu klasik bir Azure Web hizmeti olarak dağıtabilirsiniz. Kullanıcılar, Web hizmetini kullanarak modelinize veri gönderebilir ve model tahmine dayalı olarak döndürülür.

Tahmine dayalı denemenize dağıtmak için deneme tuvalinin alt kısmındaki **Çalıştır** ' a tıklayın ve ardından **Web Hizmeti Dağıt**' a tıklayın. Web hizmeti ayarlanır ve Web hizmeti panosuna yerleştirdiniz.

![Web hizmetinizi Studio 'dan dağıtma (klasik)](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>Klasik Web hizmetinizi test etme

Web hizmetini Machine Learning Studio (klasik) Web Hizmetleri portalında veya Machine Learning Studio (klasik) test edebilirsiniz.

Istek yanıtı Web hizmetini test etmek için Web hizmeti panosundaki **Test** düğmesine tıklayın. Bir iletişim kutusu açılır ve hizmet için giriş verilerini ister. Bunlar, Puanlama denemesine göre beklenen sütunlardır. Bir veri kümesi girin ve ardından **Tamam**' a tıklayın. Web hizmeti tarafından oluşturulan sonuçlar panonun en altında görüntülenir.

Daha önce yeni Web hizmeti bölümünde gösterildiği gibi, Azure Machine Learning Studio Web Hizmetleri portalının klasik sürümünde hizmetinizi test etmek için **Test** önizleme bağlantısına tıklayabilirsiniz.

Toplu yürütme hizmetini test etmek için **Test** önizleme bağlantısı ' na tıklayın. Toplu Işlem testi sayfasında, girişte bulunan Araştır ' a tıklayın ve uygun örnek değerleri içeren bir CSV dosyası seçin. Bir CSV dosyanız yoksa ve Machine Learning Studio klasik sürümünü kullanarak tahmine dayalı denemeniz oluşturduysanız, tahmine dayalı denemenize yönelik veri kümesini indirebilir ve kullanabilirsiniz.

![Web hizmetini test etme](./media/publish-a-machine-learning-web-service/figure-3.png)

**Yapılandırma** sayfasında, hizmetin görünen adını değiştirebilir ve bir açıklama verebilirsiniz. Ad ve açıklama, Web hizmetlerinizi yönettiğiniz [Azure Portal](https://portal.azure.com/) görüntülenir.

Giriş **şeması**, **Çıkış ŞEMASı**ve **Web hizmeti parametresi**altındaki her bir sütun için bir dize girerek giriş verileriniz, çıkış verileriniz ve Web hizmeti parametreleriniz için bir açıklama sağlayabilirsiniz. Bu açıklamalar, Web hizmeti için sunulan örnek kod belgelerinde kullanılır.

Web hizmetinize erişildiğinde gördüğünüz herhangi bir başarısızlığı tanılamak için günlüğe kaydetmeyi etkinleştirebilirsiniz. Daha fazla bilgi için bkz. [Machine Learning Studio (klasik) Web Hizmetleri için günlüğü etkinleştirme](web-services-logging.md).

![Web Hizmetleri portalında günlüğü etkinleştir](./media/publish-a-machine-learning-web-service/figure-4.png)

Web hizmeti için uç noktalarını, daha önce yeni Web hizmeti bölümünde gösterilen yordama benzer Azure Machine Learning Web Hizmetleri portalında da yapılandırabilirsiniz. Seçenekler farklıdır, hizmet açıklaması ekleyebilir veya değiştirebilir, günlüğe kaydetmeyi etkinleştirebilir ve test için örnek verileri etkinleştirebilirsiniz.

### <a name="access-your-classic-web-service"></a>Klasik Web hizmetinize erişin

Web hizmetinizi klasik Machine Learning Studio sürümünden dağıttıktan sonra, hizmete veri gönderebilir ve yanıtları programlı bir şekilde alabilirsiniz.

Pano, Web hizmetinize erişmek için gereken tüm bilgileri sağlar. Örneğin, API anahtarı hizmete yetkisiz erişime izin vermek için sağlanır ve kodunuzu yazmaya başlamanıza yardımcı olmak için API Yardım sayfaları sağlanır.

Machine Learning Studio (klasik) Web hizmetine erişme hakkında daha fazla bilgi için bkz. [Azure Machine Learning Studio (klasik) Web hizmeti kullanma](consume-web-services.md).

### <a name="manage-your-classic-web-service"></a>Klasik Web hizmetinizi yönetme

Bir Web hizmetini izlemek için gerçekleştirebileceğiniz çeşitli eylemler vardır. Güncelleştirebilir ve silebilirsiniz. Ayrıca, dağıtırken oluşturulan varsayılan uç noktaya ek olarak klasik bir Web hizmetine ek uç noktalar ekleyebilirsiniz.

Daha fazla bilgi için, bkz. [Azure Machine Learning Studio (klasik) çalışma alanını yönetme](manage-workspace.md) ve [Azure Machine Learning Studio (klasik) Web Hizmetleri portalını kullanarak bir Web hizmetini yönetme](manage-new-webservice.md).

## <a name="update-the-web-service"></a>Web hizmetini güncelleştirme
Web hizmetinizde, modeli ek eğitim verileriyle güncelleştirmek ve özgün Web hizmetinin üzerine yazarak yeniden dağıtmak gibi değişiklikler yapabilirsiniz.

Web hizmetini güncelleştirmek için, Web hizmetini dağıtmak için kullandığınız özgün tahmine dayalı denemeyi açın ve **farklı kaydet**' e tıklayarak düzenlenebilir bir kopya oluşturun. Değişikliklerinizi yapın ve ardından **Web Hizmeti Dağıt**' a tıklayın.

Bu denemeyi daha önce dağıttığınıza göre, mevcut hizmeti (Klasik Web hizmeti) veya güncelleştirme (yeni Web hizmeti) üzerine yazmak isteyip istemediğiniz sorulur. **Evet** veya **Güncelleştir** ' i tıklatmak, mevcut Web hizmetini sonlandırır ve yeni tahmine dayalı denemeyi dağıttığı yerde dağıtılır.

> [!NOTE]
> Özgün Web hizmetinde yapılandırma değişiklikleri yaptıysanız (örneğin, yeni bir görünen ad veya açıklama girdiğinizde), bu değerleri yeniden girmeniz gerekir.

Web hizmetinizi güncelleştirmek için bir seçenek, modeli programlı bir şekilde yeniden eğitmenize yöneliktir. Daha fazla bilgi için bkz. [Machine Learning Studio (klasik) modellerini programlı olarak yeniden eğitme](/azure/machine-learning/studio/retrain-machine-learning-model).

## <a name="next-steps"></a>Sonraki adımlar

* Dağıtımın nasıl çalıştığı hakkında daha fazla teknik bilgi için, bkz. [Machine Learning Studio (klasik) modelin bir deneyden bir çalışılan Web hizmetine nasıl ilerlediği](model-progression-experiment-to-web-service.md).

* Modelinizi dağıtıma nasıl hazırlayacağınız hakkında ayrıntılı bilgi için, bkz. [Azure Machine Learning Studio (klasik) dağıtım için modelinizi hazırlama](convert-training-experiment-to-scoring-experiment.md).

* REST API’sini kullanmanın ve web hizmetine erişmenin birkaç yolu vardır. Bkz. [Azure Machine Learning Studio (klasik) Web hizmetini kullanma](consume-web-services.md).

<!-- internal links -->
[Eğitim denemesi oluşturma]: #create-a-training-experiment
[Tahmine dayalı bir deneyiye Dönüştür]: #convert-the-training-experiment-to-a-predictive-experiment
[Yeni Web hizmeti]: #deploy-it-as-a-new-web-service
[Klasik Web hizmeti]: #deploy-it-as-a-classic-web-service
[Yeni]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
