---
title: Azure Machine Learning ile veri şifreleme
titleSuffix: Azure Machine Learning
description: Azure Machine Learning hesapladığı ve veri depolarının, bekleyen ve aktarım sırasında veri şifrelemesi nasıl sağladığını öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/09/2020
ms.openlocfilehash: 31b2a993267e6e8abbf7184d745889b74e587c50
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311779"
---
# <a name="data-encryption-with-azure-machine-learning"></a>Azure Machine Learning ile veri şifreleme

Azure Machine Learning, modellerdeki ve çıkarım gerçekleştirirken çeşitli Azure veri depolama hizmetleri ve işlem kaynakları kullanır. Bunların her biri, bekleyen ve aktarım sırasında veriler için şifreleme sağlamalarına ilişkin kendi hikayesine sahiptir. Bu makalede, senaryolarınız için en iyisi olan her biri hakkında bilgi edinin.

> [!IMPORTANT]
> __Eğitim__ sırasında üretim sınıfı şifrelemesi için, Microsoft Azure Machine Learning işlem kümesi kullanmayı önerir. Microsoft __, Microsoft__ Azure Kubernetes hizmetini kullanarak üretim sınıfı şifrelemesi için önerilir.
>
> Azure Machine Learning işlem örneği bir geliştirme/test ortamıdır. Bunu kullanırken, dosya paylaşımında Not defterleri ve betikler gibi dosyalarınızı depolamanızı öneririz. Verileriniz bir veri deposunda depolanmalıdır.

## <a name="encryption-at-rest"></a>Bekleme sırasında şifreleme

> [!IMPORTANT]
> Çalışma alanınız hassas veriler içeriyorsa, çalışma alanınızı oluştururken [hbi_workspace bayrağını](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) ayarlamayı öneririz. `hbi_workspace`Bayrak yalnızca bir çalışma alanı oluşturulduğunda ayarlanabilir. Mevcut bir çalışma alanı için değiştirilemez.

`hbi_workspace`Bayrak, [Microsoft 'un tanılama amacıyla topladığı veri](#microsoft-collected-data) miktarını denetler ve [Microsoft tarafından yönetilen ortamlarda ek şifrelemeye](../security/fundamentals/encryption-atrest.md)izin vermez. Ayrıca, aşağıdaki eylemleri sunar:

* Azure Machine Learning işlem kümenizde, bu abonelikte daha önceki kümelerin oluşturulmadığından, yerel çalışma diskini şifrelemeye başlar. Aksi takdirde, işlem kümelerinizin karalama diskinin şifrelenmesini etkinleştirmek için bir destek bileti yükseltmeniz gerekir 
* Çalıştırmalar arasında yerel karalama diskinizi temizler
* Anahtar kasanızı kullanarak depolama hesabınız, kapsayıcı kayıt defteriniz ve SSH hesabınız için kimlik bilgilerini yürütme katmanından işlem kümelerinize güvenli bir şekilde geçirir
* Temel alınan toplu iş havuzlarının AzureMachineLearningService dışında herhangi bir dış hizmet tarafından çağrılıp çağrılmaması için IP filtrelemeyi sağlar
* İşlem örnekleri HBı çalışma alanında destekleniyor

### <a name="azure-blob-storage"></a>Azure Blob depolama

Azure Machine Learning, Azure Machine Learning çalışma alanına ve aboneliğinize bağlı Azure Blob depolama hesabında anlık görüntüler, çıktılar ve Günlükler depolar. Azure Blob depolama alanında depolanan tüm veriler, Microsoft tarafından yönetilen anahtarlarla Rest 'te şifrelenir.

Azure Blob depolama alanında depolanan veriler için kendi anahtarlarınızı kullanma hakkında daha fazla bilgi için, bkz. [Azure Key Vault içindeki müşteri tarafından yönetilen anahtarlarla Azure depolama şifrelemesi](../storage/common/customer-managed-keys-configure-key-vault.md).

Eğitim verileri genellikle Azure Blob Storage 'da depolanır, böylece işlem hedeflerini eğitmek için erişilebilir. Bu depolama Azure Machine Learning tarafından yönetilmez ancak uzak bir dosya sistemi olarak işlem hedeflerine bağlanır.

Anahtarınızı __döndürmenize veya iptal__ etmeniz gerekiyorsa, bunu istediğiniz zaman yapabilirsiniz. Bir anahtar döndürürken depolama hesabı, bekleyen verileri şifrelemek için yeni anahtarı (en son sürüm) kullanmaya başlar. Bir anahtarı iptal ettiğinizde (devre dışı bırakırken), depolama hesabı başarısız istekleri üstlenir. Genellikle döndürme veya İptalin etkili olması için bir saat sürer.

Erişim anahtarlarını yeniden oluşturma hakkında daha fazla bilgi için bkz. [depolama erişim anahtarlarını yeniden üretme](how-to-change-storage-access-key.md).

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning meta verileri bir Azure Cosmos DB örneğine depolar. Bu örnek, Azure Machine Learning tarafından yönetilen bir Microsoft aboneliği ile ilişkilendirilir. Azure Cosmos DB depolanan tüm veriler, Microsoft tarafından yönetilen anahtarlarla birlikte geri kalanında şifrelenir.

Azure Cosmos DB örneğini şifrelemek için kendi (müşteri tarafından yönetilen) anahtarlarınızı kullanmak için, çalışma alanınız ile kullanmak üzere adanmış bir Cosmos DB örneği oluşturabilirsiniz. Çalışma geçmişi bilgilerini, Microsoft aboneliğimizde barındırılan çok kiracılı Cosmos DB örneğinin dışında depolamak istiyorsanız bu yaklaşımı öneririz. 

Abonelikinizde müşteri tarafından yönetilen anahtarlarla Cosmos DB bir örnek sağlamayı etkinleştirmek için aşağıdaki eylemleri gerçekleştirin:

* Henüz yapılmadıysanız Microsoft. Machinöğrenim ve Microsoft.DocumentDB kaynak sağlayıcılarını aboneliğinize kaydedin.

* Azure Machine Learning çalışma alanını oluştururken aşağıdaki parametreleri kullanın. Her iki parametre de zorunludur ve SDK, CLı, REST API 'Ler ve Kaynak Yöneticisi şablonlarda desteklenir.

    * `resource_cmk_uri`: Bu parametre, anahtarın [sürüm bilgileri](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)de dahil olmak üzere, anahtar kasasındaki müşterinin yönettiği anahtarın tam kaynak URI 'sidir. 

    * `cmk_keyvault`: Bu parametre, aboneliğinizdeki anahtar kasasının kaynak KIMLIĞIDIR. Bu anahtar kasasının, Azure Machine Learning çalışma alanı için kullanacağınız bölge ve abonelikte olması gerekir. 
    
        > [!NOTE]
        > Bu Anahtar Kasası örneği, çalışma alanını sağladığınızda Azure Machine Learning tarafından oluşturulan anahtar kasasından farklı olabilir. Çalışma alanı için aynı Anahtar Kasası örneğini kullanmak istiyorsanız, [key_vault parametresini](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)kullanarak çalışma alanını sağlarken aynı anahtar kasasını geçirin. 

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Anahtarınızı __döndürmenize veya iptal__ etmeniz gerekiyorsa, bunu istediğiniz zaman yapabilirsiniz. Bir anahtar döndürürken Cosmos DB, bekleyen verileri şifrelemek için yeni anahtarı (en son sürüm) kullanmaya başlar. Bir anahtarı iptal ettiğinizde (devre dışı bırakırken), Cosmos DB başarısız isteklerden yararlanır. Genellikle döndürme veya İptalin etkili olması için bir saat sürer.

Cosmos DB ile müşteri tarafından yönetilen anahtarlar hakkında daha fazla bilgi için bkz. [Azure Cosmos DB hesabınız için müşteri tarafından yönetilen anahtarları yapılandırma](../cosmos-db/how-to-setup-cmk.md).

### <a name="azure-container-registry"></a>Azure Container Registry

Kayıt defterinizde (Azure Container Registry) bulunan tüm kapsayıcı görüntüleri, bekleyen olarak şifrelenir. Azure, bir görüntüyü depolamadan önce otomatik olarak şifreler ve Azure Machine Learning görüntüyü aldığında şifresini çözer.

Azure Container Registry şifrelemek için kendi (müşteri tarafından yönetilen) anahtarlarınızı kullanmak için, kendi ACR 'nizi oluşturmanız ve çalışma alanını sağlarken veya çalışma alanı sağlama sırasında oluşturulan varsayılan örneği şifrelemeniz gerekir.

> [!IMPORTANT]
> Azure Machine Learning, yönetici hesabının Azure Container Registry etkinleştirilmesini gerektirir. Varsayılan olarak, bir kapsayıcı kayıt defteri oluşturduğunuzda bu ayar devre dışıdır. Yönetici hesabını etkinleştirme hakkında daha fazla bilgi için bkz. [yönetici hesabı](../container-registry/container-registry-authentication.md#admin-account).
>
> Bir çalışma alanı için Azure Container Registry oluşturulduktan sonra silmeyin. Bunu yapmak Azure Machine Learning çalışma alanınızı bozacaktır.

Mevcut bir Azure Container Registry kullanarak çalışma alanı oluşturma örneği için aşağıdaki makalelere bakın:

* [Azure CLI ile Azure Machine Learning için bir çalışma alanı oluşturun](how-to-manage-workspace-cli.md).
* [Python SDK ile bir çalışma alanı oluşturun](how-to-manage-workspace.md?tabs=python#create-a-workspace).
* [Azure Machine Learning için bir çalışma alanı oluşturmak üzere Azure Resource Manager şablonu kullanma](how-to-create-workspace-template.md)

### <a name="azure-container-instance"></a>Azure Container Örneği

Dağıtılan bir Azure Container Instance (acı) kaynağını, müşteri tarafından yönetilen anahtarları kullanarak şifreleyebilirsiniz. ACI için kullanılan müşteri tarafından yönetilen anahtar, çalışma alanınızın Azure Key Vault depolanabilir. Anahtar oluşturma hakkında bilgi için bkz. [müşteri tarafından yönetilen bir anahtarla verileri şifreleme](../container-instances/container-instances-encrypt-data.md#generate-a-new-key).

Azure Container Instance 'a model dağıttığınızda anahtarı kullanmak için kullanarak yeni bir dağıtım yapılandırması oluşturun `AciWebservice.deploy_configuration()` . Aşağıdaki parametreleri kullanarak anahtar bilgilerini sağlayın:

* `cmk_vault_base_url`: Anahtarı içeren anahtar kasasının URL 'SI.
* `cmk_key_name`: Anahtarın adı.
* `cmk_key_version`: Anahtarın sürümü.

Dağıtım yapılandırması oluşturma ve kullanma hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [AciWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-) başvurusu
* [Dağıtım nereye ve nasıl yapılır?](how-to-deploy-and-where.md)
* [Modeli Azure Container Instances’a dağıtma](how-to-deploy-azure-container-instance.md)

ACI ile müşteri tarafından yönetilen anahtar kullanma hakkında daha fazla bilgi için bkz. [müşteri tarafından yönetilen bir anahtarla verileri şifreleme](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key).

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Dağıtılan bir Azure Kubernetes hizmeti kaynağını, müşteri tarafından yönetilen anahtarları dilediğiniz zaman kullanarak şifreleyebilirsiniz. Daha fazla bilgi için bkz. [Azure Kubernetes hizmeti ile kendi anahtarlarınızı getirme](../aks/azure-disk-customer-managed-keys.md). 

Bu işlem, Kubernetes kümesindeki dağıtılan sanal makinelerin hem verilerini hem de işletim sistemi diskini şifrelemenizi sağlar.

> [!IMPORTANT]
> Bu işlem yalnızca AKS K8s sürüm 1,17 veya üzeri sürümlerle kullanılabilir. 13 Ocak 2020 ' de AKS 1,17 için destek eklendi Azure Machine Learning.

### <a name="machine-learning-compute"></a>Machine Learning İşlem

Azure depolama 'da depolanan her işlem düğümü için işletim sistemi diski, Azure Machine Learning depolama hesaplarında Microsoft tarafından yönetilen anahtarlarla şifrelenir. Bu işlem hedefi kısa ömürlü ve hiçbir çalışma sıraya alınmaz kümeler genellikle ölçeği aşağı ölçeklendirilir. Temel alınan sanal makine de sağlanmamıştır ve işletim sistemi diski silinir. Azure disk şifrelemesi, işletim sistemi diski için desteklenmez.

Her bir sanal makinenin işletim sistemi işlemleri için yerel bir geçici diski de vardır. İsterseniz eğitim verilerini hazırlamak için diski kullanabilirsiniz. Parametresi, parametresi olarak ayarlanan çalışma alanları için varsayılan olarak şifrelenir `hbi_workspace` `TRUE` . Bu ortam yalnızca çalıştırma süresince kısa ömürlü ve şifreleme desteği yalnızca sistem tarafından yönetilen anahtarlarla sınırlıdır.

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks, Azure Machine Learning işlem hatları içinde kullanılabilir. Varsayılan olarak, Azure Databricks tarafından kullanılan Databricks dosya sistemi (DBFS), Microsoft tarafından yönetilen bir anahtar kullanılarak şifrelenir. Azure Databricks, müşteri tarafından yönetilen anahtarları kullanacak şekilde yapılandırmak için, bkz. [varsayılan (root) için müşteri tarafından yönetilen anahtarları yapılandırma](/azure/databricks/security/customer-managed-keys-dbfs).

### <a name="microsoft-generated-data"></a>Microsoft tarafından oluşturulan veriler

Microsoft, otomatik Machine Learning gibi hizmetleri kullanırken, birden çok modeli eğitmek için geçici, önceden işlenmiş bir veri oluşturabilir. Bu veriler, çalışma alanınızdaki bir veri deposunda depolanır ve bu da erişim denetimlerini ve şifrelemeyi uygun şekilde zorlamanıza olanak sağlar.

[Dağıtılmış uç noktanıza kaydedilen tanılama bilgilerini](how-to-enable-app-insights.md) Azure Application Insights örneğinize şifrelemek da isteyebilirsiniz.

## <a name="encryption-in-transit"></a>Aktarım sırasında şifreleme

Azure Machine Learning, çeşitli Azure Machine Learning mikro hizmetler arasında iç iletişimin güvenliğini sağlamak için TLS kullanır. Tüm Azure depolama erişimi, güvenli bir kanal üzerinden de gerçekleşir.

Puanlama uç noktasına yapılan dış çağrıların güvenliğini sağlamak için Azure Machine Learning TLS kullanır. Daha fazla bilgi için bkz. [Azure Machine Learning aracılığıyla bir Web hizmetini güvenli hale getirmek IÇIN TLS kullanma](./how-to-secure-web-service.md).

## <a name="data-collection-and-handling"></a>Veri toplama ve işleme

### <a name="microsoft-collected-data"></a>Microsoft tarafından toplanan veriler

Microsoft, kaynak adları (örneğin, veri kümesi adı veya makine öğrenimi deneme adı) gibi kullanıcı olmayan tanımlama bilgilerini veya tanılama amacıyla iş ortamı değişkenlerini toplayabilir. Bu tür veriler, Microsoft 'un sahip olduğu aboneliklerde barındırılan depolamada Microsoft tarafından yönetilen anahtarlar kullanılarak depolanır ve [Microsoft 'un standart Gizlilik ilkesi ve veri işleme standartlarını](https://privacy.microsoft.com/privacystatement)izler.

Microsoft ayrıca, önemli bilgileri (örneğin, hesap anahtarı gizli dizileri) ortam değişkenlerine depolamamanızı da önerir. Ortam değişkenleri günlüğe kaydedilir, şifrelenir ve bizimle saklanır. Benzer şekilde [run_id](/python/api/azureml-core/azureml.core.run%28class%29)adlandırırken, Kullanıcı adları veya gizli proje adları gibi hassas bilgileri de eklemekten kaçının. Bu bilgiler, Microsoft Desteği mühendislerine erişilebilen telemetri günlüklerinde görünebilir.

`hbi_workspace`Çalışma alanını sağlarken parametresini olarak ayarlayarak, toplanan tanılama verilerinden bu verileri devre dışı kalabilirsiniz `TRUE` . Bu işlev, AzureML Python SDK 'Sı, CLı, REST API 'Leri veya Azure Resource Manager şablonları kullanılırken desteklenir.

## <a name="using-azure-key-vault"></a>Azure Key Vault kullanma

Azure Machine Learning, çeşitli türlerdeki kimlik bilgilerini depolamak için çalışma alanıyla ilişkili Azure Key Vault örneğini kullanır:

* İlişkili depolama hesabı bağlantı dizesi
* Azure Container Repository deposu örneklerine parolalar
* Veri depolarına yönelik bağlantı dizeleri

Azure HDInsight ve VM 'Ler gibi hedefleri hesaplamak için SSH parolaları ve anahtarları, Microsoft aboneliğiyle ilişkili ayrı bir anahtar kasasında depolanır. Azure Machine Learning, kullanıcılar tarafından sunulan herhangi bir parolayı veya anahtarı depolamaz. Bunun yerine, denemeleri çalıştırmak için VM 'Leri ve HDInsight 'a bağlanmak üzere kendi SSH anahtarlarını oluşturur, yetkilendirir ve depolar.

Her çalışma alanı, çalışma alanıyla aynı ada sahip ilişkili, sistem tarafından atanan bir yönetilen kimliğe sahiptir. Bu yönetilen kimliğin, anahtar kasasındaki tüm anahtar, gizli dizi ve sertifikalara erişimi vardır.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure depolamaya bağlanma](how-to-access-data.md)
* [Bir veri deposundan veri alma](how-to-create-register-datasets.md)
* [Verilere bağlanma](how-to-connect-data-ui.md)
* [Veri kümeleriyle eğitme](how-to-train-with-datasets.md)
