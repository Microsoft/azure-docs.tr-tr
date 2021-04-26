---
title: Maliyetleri planlama ve yönetme
titleSuffix: Azure Machine Learning
description: Azure portal maliyet analiziyle Azure Machine Learning maliyetlerini planlayın ve yönetin. ML modellerini oluştururken maliyetinizi düşürmek için daha fazla maliyet tasarrufu ipuçları edinin.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization, devx-track-azurecli
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: d8f74e438175758b1f09e1809b5eba15c1b26c3c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873640"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>Azure Machine Learning maliyetlerini planlayın ve yönetin

Bu makalede Azure Machine Learning maliyetlerinin nasıl planlanacağı ve yönetileceği açıklanmaktadır. İlk olarak, herhangi bir kaynak eklemeden önce maliyetleri planlamaya yardımcı olması için Azure Fiyatlandırma hesaplayıcısı ' nı kullanın. Daha sonra, Azure kaynaklarını eklerken Tahmini maliyetleri gözden geçirin. Son olarak, modelinize yönetilen Azure Machine Learning işlem kümeleriyle eğitede maliyet tasarrufu ipuçları kullanın.

Azure Machine Learning kaynaklarını kullanmaya başladıktan sonra, bütçe ayarlamak ve maliyetleri izlemek için maliyet yönetimi özelliklerini kullanın. Ayrıca tahmin edilen maliyetleri gözden geçirin ve hareket etmek isteyebileceğiniz bölgeleri belirlemek için harcama eğilimlerini belirler.

Azure Machine Learning maliyetlerinin yalnızca Azure faturanızda bulunan Aylık maliyetlerin yalnızca bir kısmı olduğunu anlayın. Diğer Azure hizmetlerini kullanıyorsanız, üçüncü taraf hizmetler de dahil olmak üzere Azure aboneliğinizde kullanılan tüm Azure hizmetleri ve kaynakları için faturalandırılırsınız. Bu makalede, Azure Machine Learning maliyetlerinin nasıl planlanacağı ve yönetileceği açıklanmaktadır. Azure Machine Learning maliyetlerini yönetme hakkında bilgi sahibi olduktan sonra, aboneliğinizde kullanılan tüm Azure hizmetlerinin maliyetlerini yönetmek için benzer yöntemler uygulayın.

Makine öğrenimi modellerinizi eğitedığınızda, daha fazla maliyet tasarrufu ipuçlarından yararlanmak için yönetilen Azure Machine Learning işlem kümelerini kullanın:

* Otomatik ölçeklendirme için eğitim kümelerinizi yapılandırma
* Aboneliğinizde ve çalışma alanlarınızda kotalar ayarlama
* Eğitim çalıştırmada sonlandırma ilkelerini ayarlama
* Düşük öncelikli sanal makineler (VM) kullanma
* Azure ayrılmış VM örneği kullanma

## <a name="prerequisites"></a>Ön koşullar

Maliyet analizi, farklı türdeki Azure hesaplarını destekler. Desteklenen hesap türlerinin tam listesini görüntülemek için, bkz. [Maliyet Yönetimi verilerini anlama](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Maliyet verilerini görüntülemek için Azure hesabınızda en azından okuma yetkisine sahip olmanız gerekir. 

Azure Maliyet Yönetimi verilerine erişim atama hakkında daha fazla bilgi için bkz. [Verilere erişim atama](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-machine-learning"></a>Azure Machine Learning kullanmadan önce maliyetleri tahmin etme

Azure Machine Learning hesapta kaynakları oluşturmadan önce maliyetleri tahmin etmek için [Azure Fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) ' nı kullanın. Sol tarafta **AI + Machine Learning** ve ardından başlamak için **Azure Machine Learning** öğesini seçin.  

Aşağıdaki ekran, hesaplayıcı kullanılarak maliyet tahmini gösterir:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Azure Hesaplayıcı 'da maliyet tahmini":::

Çalışma alanınıza yeni kaynaklar eklerken bu Hesaplayıcı 'a dönün ve maliyet tahminlerinizi güncelleştirmek için aynı kaynağı buraya ekleyin.

Daha fazla bilgi için bkz. [Azure Machine Learning fiyatlandırması](https://azure.microsoft.com/pricing/details/machine-learning?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="understand-the-full-billing-model-for-azure-machine-learning"></a>Azure Machine Learning için tam faturalandırma modelini anlayın

Azure Machine Learning, yeni kaynağı dağıtırken Azure Machine Learning ile birlikte maliyetleri tahakkuk eden Azure altyapısında çalışır. Ek altyapının maliyeti tahakkuk edebileceğini anlamak önemlidir. Dağıtılan kaynaklarda değişiklik yaptığınızda bu maliyeti yönetmeniz gerekir. 

### <a name="costs-that-typically-accrue-with-azure-machine-learning"></a>Genellikle Azure Machine Learning ile tahakkuk eden maliyetler

Bir Azure Machine Learning çalışma alanı için kaynak oluşturduğunuzda, diğer Azure hizmetlerine yönelik kaynaklar da oluşturulur. Bunlar:

* [Azure Container Registry](https://azure.microsoft.com/pricing/details/container-registry?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Temel hesap
* [Azure Blok Blob depolama](https://azure.microsoft.com/pricing/details/storage/blobs?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) (genel amaçlı v1)
* [Anahtar Kasası](https://azure.microsoft.com/pricing/details/key-vault?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Uygulama Bilgileri](https://azure.microsoft.com/en-us/pricing/details/monitor?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
 
### <a name="costs-might-accrue-after-resource-deletion"></a>Kaynak silme işleminin ardından maliyetler tahakkuk edebilir

Azure portal veya Azure CLı ile bir Azure Machine Learning çalışma alanını sildiğinizde, aşağıdaki kaynaklar mevcut olmaya devam eder. Bunlar silinene kadar maliyetleri tahakkuk ettirmeye devam eder.

* Azure Container Registry
* Azure Blok Blobu depolama
* Key Vault
* Application Insights

Çalışma alanını bu bağımlı kaynaklarla birlikte silmek için SDK 'Yı kullanın:

```python
ws.delete(delete_dependent_resources=True)
```

Çalışma alanınızda Azure Kubernetes hizmeti (AKS) oluşturursanız veya çalışma alanınıza herhangi bir işlem kaynağı eklerseniz, bunları [Azure Portal](https://portal.azure.com)ayrı olarak silmeniz gerekir.

### <a name="using-azure-prepayment-credit-with-azure-machine-learning"></a>Azure Machine Learning ile Azure ön ödeme kredisi kullanma

Azure ön ödeyiniz (daha önce parasal taahhüt olarak adlandırılır) krediyle Azure Machine Learning ücretleri için ödeme yapabilirsiniz. Bununla birlikte, Azure Marketi 'nden dahil olmak üzere üçüncü taraf ürün ve hizmetlerine yönelik ücretler için Azure ön ödemeli hizmetini kullanamazsınız.


## <a name="create-budgets"></a>Bütçe oluşturma

Maliyetleri yönetmek için [bütçeler](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oluşturabilir ve paydaşları harcama anormallikleri ve fazla harcama riskleri ile ilgili otomatik olarak bilgilendiren [uyarılar](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oluşturabilirsiniz. Uyarılar, bütçe ve maliyet eşiklerine kıyasla harcamaya göre belirlenir. Bütçeler ve uyarılar Azure abonelikleri ve kaynak grupları için oluşturulur, bu nedenle genel maliyet izleme stratejisinin bir parçası olarak faydalıdır. 

İzlemekte daha fazla ayrıntı düzeyi istiyorsanız, Azure 'daki belirli kaynaklara veya hizmetlere yönelik filtrelerle bütçeler oluşturulabilir. Filtreler, yanlışlıkla ek para maliyeti sağlayan yeni kaynaklar oluşturmamasını sağlamaya yardımcı olur. Bir bütçe oluştururken filtre seçenekleri hakkında daha fazla bilgi için bkz. [Grup ve filtre seçenekleri](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Maliyet verilerini dışarı aktarma

Ayrıca, maliyet verilerinizi bir depolama hesabına [dışarı aktarabilirsiniz](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) . Bu, maliyetler için ek veri analizi yapmanız gerektiğinde veya başkalarının yararlı olması durumunda faydalıdır. Örneğin, bir finans ekipleri, Excel veya Power BI kullanarak verileri analiz edebilir. Maliyetlerinizi günlük, haftalık veya aylık bir zamanlamaya göre dışa aktarabilir ve özel bir tarih aralığı ayarlayabilirsiniz. Maliyet verilerini dışarı aktarmak, maliyet veri kümelerini almak için önerilen yoldur.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-machine-learning"></a>Azure Machine Learning maliyetlerini yönetmenin ve azaltmanın diğer yolları

Machine Learning işlem kaynaklarınızın maliyetlerini içeren bu ipuçlarını kullanın.

### <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Azure Machine Learning işlem kümesi (AmlCompute) kullanın

Sürekli değişen veriler sayesinde, doğru modelleri sürdürmek için hızlı ve kolaylaştırılmış model eğitimi ve yeniden eğitimlere ihtiyacınız vardır. Ancak, özellikle GPU 'Larda derin öğrenme modelleri için sürekli eğitim bir maliyetle gelir. 

Azure Machine Learning kullanıcılar, AmlCompute olarak da adlandırılan yönetilen Azure Machine Learning işlem kümesini kullanabilir. AmlCompute, çeşitli GPU ve CPU seçeneklerini destekler. AmlCompute, Azure Machine Learning tarafından aboneliğiniz adına dahili olarak barındırılır. Azure IaaS bulut ölçeğinde aynı kurumsal sınıf güvenlik, uyumluluk ve idare sağlar.

Bu işlem havuzları Azure 'un IaaS altyapısının içinde olduğundan, altyapınızın geri kalanı ile aynı güvenlik ve uyumluluk gereksinimleriyle öğreticinizi dağıtabilir, ölçeklendirebilir ve yönetebilirsiniz.  Bu dağıtımlar aboneliğinizde oluşur ve idare kurallarınızı uyar. [Azure Machine Learning işlem](how-to-create-attach-compute-cluster.md)hakkında daha fazla bilgi edinin.

### <a name="configure-training-clusters-for-autoscaling"></a>Otomatik ölçeklendirme için eğitim kümelerini yapılandırma

İş yükünüzün gereksinimlerine göre otomatik ölçeklendirme kümeleri, yalnızca ihtiyacınız olanları kullanabilmeniz için maliyetlerinizi azaltmaya yardımcı olur.

AmlCompute kümeleri, iş yükünüze göre dinamik olarak ölçeklendirilecek şekilde tasarlanmıştır. Küme, yapılandırdığınız en fazla düğüm sayısına göre ölçeklendirilebilir. Her çalıştırma tamamlandığında, küme düğümleri serbest bırakabilir ve yapılandırılmış en düşük düğüm sayımına ölçeklendirecektir.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Ayrıca, düğümlerin ölçeğini ölçeklendirmadan önce boşta kaldığı süreyi de yapılandırabilirsiniz. Varsayılan olarak, ölçek azaltma öncesindeki boşta geçen süre 120 saniyeye ayarlanır.

+ Daha az yinelemeli deneme gerçekleştirirseniz, maliyetleri kaydetmek için bu süreyi azaltın.
+ Son derece yinelemeli geliştirme/test deneme gerçekleştirirseniz, eğitim betiğinizdeki veya ortamınızdaki her değişiklikten sonra sabit ölçekleme için ödeme yapmak zorunda kalmaması için süreyi artırmanız gerekebilir.

AmlCompute kümeleri, amlcompute [SDK sınıfı](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute)olan AMLCOMPUTE [CLI](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_amlcompute)ve [REST API 'leri](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable)ile birlikte Azure Portal değişen iş yükü gereksinimleriniz için yapılandırılabilir.

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

### <a name="set-quotas-on-resources"></a>Kaynaklarda kota ayarlama

AmlCompute bir [Kota (veya limit) yapılandırmasıyla](how-to-manage-quotas.md#azure-machine-learning-compute)gelir. Bu kota, VM ailesine göre (örneğin, Dv2 serisi, NCv3 serisi) ve her abonelik için bölgeye göre değişir. Abonelikler, sizin için küçük varsayılanlar ile başlar, ancak aboneliğinizde kullanılacak Amlcompute kaynaklarının miktarını denetlemek için bu ayarı kullanın. 

Ayrıca, bir abonelik içindeki her çalışma alanı için, [VM ailesine göre çalışma alanı düzeyi kotasını](how-to-manage-quotas.md#workspace-level-quotas)yapılandırın. Bunun yapılması, her bir çalışma alanının potansiyel olarak olası ve belirli VM ailelerini kısıtlayabileceği maliyetler üzerinde daha ayrıntılı denetime sahip olmasını sağlar. 

Çalışma alanı düzeyinde kotalar ayarlamak için [Azure Portal](https://portal.azure.com)başlatın.  Aboneliğinizdeki herhangi bir çalışma alanını seçin ve sol bölmedeki **kullanımlar + kotalar** ' ı seçin. Sonra kotaları görüntülemek için **kotaları Yapılandır** sekmesini seçin. Birden çok çalışma alanını etkileyen bir ayar olduğundan kotayı ayarlamak için abonelik kapsamında ayrıcalıklarınız olması gerekir.

### <a name="set-run-autotermination-policies"></a>Çalıştırma oto sonlandırma ilkelerini ayarla 

Bazı durumlarda, eğitim çalıştırmalarını süreleri sınırlamak veya erkenden sona erdirmek için yapılandırmanız gerekir. Örneğin, Azure Machine Learning yerleşik hiper parametre ayarlama veya otomatik makine öğrenimi kullanırken.

İşte kullanabileceğiniz birkaç seçenek:
* `max_run_duration_seconds`Bir çalıştırmanın seçtiğiniz işlem için (yerel veya uzak bulut işlem) üzerinde genişletileceği en uzun süreyi denetlemek Için RunConfiguration uygulamanızda çağrılan bir parametre tanımlayın.
* [Hiper parametre ayarlama](how-to-tune-hyperparameters.md#early-termination)için, bir bandıt ilkesinden erken sonlandırma Ilkesi, ortanca durdurma Ilkesi veya kesme seçim ilkesi tanımlayın. Hiper parametre sweeps 'yi daha fazla denetlemek için veya gibi parametreleri `max_total_runs` kullanın `max_duration_minutes` .
* [Otomatik makine öğrenimi](how-to-configure-auto-train.md#exit)için bayrağını kullanarak benzer sonlandırma ilkeleri ayarlayın `enable_early_stopping` . Ayrıca, `iteration_timeout_minutes` ve `experiment_timeout_minutes` bir çalıştırmanın en uzun süresini denetlemek için veya tüm denemeler için ve gibi özellikleri kullanın.

### <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> Düşük öncelikli VM 'Ler kullanma

Azure, sanal makine ölçek kümeleri, toplu Iş ve Machine Learning hizmeti arasında Low-Priority VM 'Ler olarak fazla unutilized kapasitesini kullanmanıza olanak tanır. Bu ayırmalar ön azaltıldı, ancak adanmış VM 'Lerle karşılaştırıldığında daha düşük bir fiyata gelir. Genel olarak, Batch iş yükleri için Low-Priority VM 'Leri kullanmanızı öneririz. Ayrıca, kesintileri resubmits aracılığıyla kurtarılabilir (toplu Iş için geçiş için) veya yeniden başlatmalar aracılığıyla (checksize ile derin öğrenme eğitimi için) de kullanmanız gerekir.

Low-Priority VM 'Ler, VM ailesi ile olan ayrılmış kota değerinden ayrı tek bir kota içermelidir. [AmlCompute kotaları hakkında daha fazla](how-to-manage-quotas.md)bilgi edinin.

 Low-Priority VM 'Ler, etkileşimli not defteri deneyimlerini desteklemesi gerektiğinden işlem örnekleri için çalışmaz.

### <a name="use-reserved-instances"></a>Ayrılmış örnekleri kullanma

İşlem kaynaklarına para kazanmanız için başka bir yöntem de Azure ayrılmış VM örneğidir. Bu teklifle, bir yıllık veya üç yıllık koşullara göre işlemeniz gerekir. Bu indirimler, Kullandıkça Öde fiyatlarının %72 ' ine kadar değişir ve doğrudan aylık Azure faturanızda uygulanır.

Azure Machine Learning Işlem, ayrılmış örnekleri kendiliğinden destekler. Bir yıllık veya üç yıllık ayrılmış örnek satın alırsanız, Azure Machine Learning yönetilen işlem için otomatik olarak indirim uygulayacağız.


## <a name="next-steps"></a>Sonraki adımlar

- [Azure maliyet yönetimi ile bulut yatırımınızın nasıl iyileştirileceği hakkında](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)bilgi edinin.
- [Maliyet Analizi](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)ile maliyetleri yönetme hakkında daha fazla bilgi edinin.
- [Beklenmeyen maliyetlerin nasıl önleneceği](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)hakkında bilgi edinin.
- [Maliyet yönetimi](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) destekli öğrenme kursuna katılın.
