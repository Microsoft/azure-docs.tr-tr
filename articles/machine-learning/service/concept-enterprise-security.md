---
title: Kurumsal güvenlik
titleSuffix: Azure Machine Learning
description: 'Azure Machine Learning güvenli bir şekilde kullanın: kimlik doğrulama, yetkilendirme, ağ güvenliği, veri şifreleme ve izleme.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/04/2019
ms.openlocfilehash: e834c55ec35195ff627176603c7611abbf6adf1c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497506"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Azure Machine Learning için Kuruluş Güvenliği

Bu makalede Azure Machine Learning için kullanılabilen güvenlik özellikleri hakkında bilgi edineceksiniz.

Bir bulut hizmeti kullandığınızda, erişimi yalnızca ihtiyacı olan kullanıcılarla kısıtlayabilmeniz en iyi uygulamadır. Hizmetini, hizmet tarafından kullanılan kimlik doğrulama ve yetkilendirme modelini inceleyerek başlatın. Ayrıca, ağ erişimini kısıtlamak veya şirket içi ağınızdaki kaynakları güvenli bir şekilde buluta katmak isteyebilirsiniz. Veri şifreleme Ayrıca, hem bekleyen hem de veriler hizmetler arasında taşınıyor. Son olarak, hizmeti izleyebilmeniz ve tüm etkinliklerin denetim günlüğünü üretmeniz gerekir.

## <a name="authentication"></a>Kimlik Doğrulaması

Azure Active Directory (Azure AD) kullanmak üzere yapılandırılmışsa Multi-Factor Authentication desteklenir. Kimlik doğrulama işlemi şu şekildedir:

1. İstemci Azure AD 'de oturum açar ve bir Azure Resource Manager belirteci alır.  Kullanıcılar ve hizmet sorumluları tam olarak desteklenmektedir.
1. İstemci belirteci Azure Resource Manager ve tüm Azure Machine Learning gösterir.
1. Machine Learning hizmeti, Kullanıcı işlem hedefine bir Machine Learning hizmet belirteci sağlar (örneğin, Machine Learning İşlem). Bu belirteç, Kullanıcı işlem hedefi tarafından, çalıştırma tamamlandıktan sonra Machine Learning hizmetine geri çağrı yapmak için kullanılır. Kapsam, çalışma alanıyla sınırlıdır.

[Azure Machine Learning ![kimlik doğrulaması](./media/enterprise-readiness/authentication.png)](./media/enterprise-readiness/authentication-expanded.png)

### <a name="authentication-for-web-service-deployment"></a>Web hizmeti dağıtımı için kimlik doğrulaması

Azure Machine Learning, Web Hizmetleri için iki kimlik doğrulama biçimini destekler: anahtar ve belirteç. Her Web hizmeti tek seferde yalnızca bir kimlik doğrulama biçimi etkinleştirebilir.

|Kimlik doğrulama Yöntemi|Azure Container Instances|AKS|
|---|---|---|
|Anahtar|Varsayılan olarak devre dışı| Varsayılan olarak etkin|
|Belirteç| Kullanılamaz| Varsayılan olarak devre dışı |

#### <a name="authentication-with-keys"></a>Anahtarlar ile kimlik doğrulama

Bir dağıtım için anahtar kimlik doğrulamasını etkinleştirdiğinizde, otomatik olarak kimlik doğrulama anahtarları oluşturursunuz.

* Azure Kubernetes Service 'e (AKS) dağıtırken kimlik doğrulaması varsayılan olarak etkindir.
* Azure Container Instances ' a dağıtırken kimlik doğrulaması varsayılan olarak devre dışıdır.

Anahtar kimlik doğrulamasını etkinleştirmek için, bir dağıtım oluştururken veya güncelleştirdiğinizde `auth_enabled` parametresini kullanın.

Anahtar kimlik doğrulaması etkinleştirilirse, birincil ve ikincil kimlik doğrulama anahtarını almak için `get_keys` yöntemini kullanabilirsiniz:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Bir anahtarı yeniden oluşturmanız gerekiyorsa [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)kullanın.

#### <a name="authentication-with-tokens"></a>Belirteçlerle kimlik doğrulama

Bir Web hizmeti için belirteç kimlik doğrulamasını etkinleştirdiğinizde, kullanıcılar, Web hizmetine erişmek için bir Azure Machine Learning JSON Web Token sunmalıdır.

* Belirteç kimlik doğrulaması, Azure Kubernetes hizmetine dağıtırken varsayılan olarak devre dışıdır.
* Azure Container Instances ' a dağıtırken belirteç kimlik doğrulaması desteklenmez.

Belirteç kimlik doğrulamasını denetlemek için, bir dağıtım oluştururken veya güncelleştirdiğinizde `token_auth_enabled` parametresini kullanın.

Belirteç kimlik doğrulaması etkinleştirilirse, bir JSON Web Token (JWT) almak için `get_token` yöntemini ve belirtecin sona erme süresini kullanabilirsiniz:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Belirtecin `refresh_by` zamanından sonra yeni bir belirteç istemeniz gerekir.
>
> Azure Machine Learning çalışma alanınızı Azure Kubernetes hizmet kümeniz ile aynı bölgede oluşturmanızı önemle öneririz. 
>
> Bir belirteçle kimlik doğrulaması yapmak için Web hizmeti, Azure Machine Learning çalışma alanınızın oluşturulduğu bölgeye bir çağrı yapar. Çalışma alanınızın bölgesi kullanılamıyorsa, kümeniz çalışma alanınızdan farklı bir bölgede olsa bile, Web hizmetiniz için bir belirteç getirimeyeceksiniz. Sonuç olarak, çalışma alanınızın bölgesi yeniden kullanılabilir olana kadar Azure AD kimlik doğrulaması kullanılamaz. 
>
> Ayrıca, kümenizin bölgesi ve çalışma alanınızın bölgesi arasındaki mesafe arttıkça bir belirteç getirmek için o kadar sürer.

## <a name="authorization"></a>Yetkilendirme

Birden çok çalışma alanı oluşturabilirsiniz ve her çalışma alanı birden çok kişi tarafından paylaşılabilir. Bir çalışma alanını paylaştığınızda, kullanıcılara bu rolleri atayarak erişimi denetleyebilirsiniz:

* Sahip
* Katılımcı
* Okuyucu

Aşağıdaki tabloda, bazı önemli Azure Machine Learning işlemleri ve bunları gerçekleştirebilen roller listelenmektedir:

| Azure Machine Learning işlemi | Sahip | Katılımcı | Okuyucu |
| ---- |:----:|:----:|:----:|
| Çalışma alanı oluşturma | ✓ | ✓ | |
| Çalışma alanını paylaşma | ✓ | |  |
| Çalışma alanını Enterprise Edition 'a yükselt | ✓ | |
| İşlem hedefi oluştur | ✓ | ✓ | |
| İşlem hedefi Ekle | ✓ | ✓ | |
| Veri depoları Ekle | ✓ | ✓ | |
| Deneme Çalıştır | ✓ | ✓ | |
| Çalıştırmaları/ölçümleri görüntüle | ✓ | ✓ | ✓ |
| Modeli kaydetme | ✓ | ✓ | |
| Görüntü oluştur | ✓ | ✓ | |
| Web hizmetini dağıtma | ✓ | ✓ | |
| Modelleri/görüntüleri görüntüleme | ✓ | ✓ | ✓ |
| Web hizmetini çağır | ✓ | ✓ | ✓ |

Yerleşik roller ihtiyaçlarınızı karşılamıyorsa, özel roller oluşturabilirsiniz. Özel roller yalnızca çalışma alanındaki işlemler için desteklenir ve Machine Learning İşlem. Özel roller çalışma alanında ve bu çalışma alanındaki işlem kaynağında okuma, yazma veya silme izinlerine sahip olabilir. Rolü belirli bir çalışma alanı düzeyinde, belirli bir kaynak grubu düzeyinde veya belirli bir abonelik düzeyinde kullanılabilir hale getirebilirsiniz. Daha fazla bilgi için bkz. [Azure Machine Learning çalışma alanında kullanıcıları ve rolleri yönetme](how-to-assign-roles.md).

### <a name="securing-compute-targets-and-data"></a>İşlem hedeflerinin ve verilerin güvenliğini sağlama

Sahipler ve katkıda bulunanlar, çalışma alanına bağlı tüm işlem hedeflerini ve veri depolarını kullanabilir.  

Her çalışma alanı Ayrıca, çalışma alanıyla aynı ada sahip ilişkili bir sistem tarafından atanan yönetilen kimliğe sahiptir. Yönetilen kimliğin, çalışma alanında kullanılan bağlı kaynaklar üzerinde aşağıdaki izinleri vardır.

Yönetilen kimlikler hakkında daha fazla bilgi için bkz. [Azure kaynakları Için Yönetilen kimlikler](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

| Kaynak | İzinler |
| ----- | ----- |
| Çalışma alanı | Katılımcı |
| Depolama hesabı | Depolama Blobu veri Katılımcısı |
| Key Vault | Tüm anahtarlar, gizlilikler, sertifikalara erişim |
| Azure Container Registry | Katılımcı |
| Çalışma alanını içeren kaynak grubu | Katılımcı |
| Anahtar kasasını içeren kaynak grubu (çalışma alanını içeren bunlardan farklıysa) | Katılımcı |

Yöneticilerin yönetilen kimliğin önceki tabloda bahsedilen kaynaklara erişimini iptal etmemenizi önermiyoruz. Yeniden eşitleme anahtarları işlemini kullanarak erişimi geri yükleyebilirsiniz.

Azure Machine Learning, her çalışma alanı bölgesi için aboneliğinizde katkıda bulunan düzeyinde erişim ile ek bir uygulama (ad `aml-` veya `Microsoft-AzureML-Support-App-`ile başlar) oluşturur. Örneğin, Doğu ABD içinde bir çalışma alanınız ve aynı abonelikte Kuzey Avrupa başka bir çalışma alanı varsa, bu uygulamalardan ikisini de görürsünüz. Bu uygulamalar, işlem kaynaklarını yönetmenize yardımcı olmak için Azure Machine Learning sağlar.

## <a name="network-security"></a>Ağ güvenliği

Azure Machine Learning, işlem kaynakları için diğer Azure hizmetlerini kullanır. İşlem kaynakları (işlem hedefleri) modelleri eğitmek ve dağıtmak için kullanılır. Bu işlem hedeflerini bir sanal ağda oluşturabilirsiniz. Örneğin, bir modeli eğitebilmek ve sonra modeli AKS 'e dağıtmak için Azure Veri Bilimi Sanal Makinesi kullanabilirsiniz.  

Daha fazla bilgi için bkz. [Sanal ağda denemeleri ve çıkarımı çalıştırma](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Veri şifrelemesi

### <a name="encryption-at-rest"></a>Bekleme sırasında şifreleme

#### <a name="azure-blob-storage"></a>Azure Blob depolama

Azure Machine Learning, Azure Machine Learning çalışma alanına ve aboneliğinize bağlı Azure Blob depolama hesabında anlık görüntüler, çıktılar ve Günlükler depolar. Azure Blob depolama alanında depolanan tüm veriler, Microsoft tarafından yönetilen anahtarlarla Rest 'te şifrelenir.

Azure Blob depolama alanında depolanan veriler için kendi anahtarlarınızı kullanma hakkında daha fazla bilgi için, bkz. [Azure Key Vault içindeki müşteri tarafından yönetilen anahtarlarla Azure depolama şifrelemesi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

Eğitim verileri genellikle Azure Blob Storage 'da depolanır, böylece işlem hedeflerini eğitmek için erişilebilir. Bu depolama Azure Machine Learning tarafından yönetilmez ancak uzak bir dosya sistemi olarak işlem hedeflerine bağlanır.

Çalışma alanınızda kullanılan Azure depolama hesapları için erişim anahtarlarını yeniden oluşturma hakkında bilgi için bkz. [depolama erişim anahtarlarını yeniden üretme](how-to-change-storage-access-key.md).

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning ölçümleri ve meta verileri Azure Machine Learning tarafından yönetilen bir Microsoft aboneliğiyle ilişkili Azure Cosmos DB örneğinde depolar. Azure Cosmos DB depolanan tüm veriler, Microsoft tarafından yönetilen anahtarlarla birlikte geri kalanında şifrelenir.

#### <a name="azure-container-registry"></a>Azure Container Registry

Kayıt defterinizde (Azure Container Registry) bulunan tüm kapsayıcı görüntüleri, bekleyen olarak şifrelenir. Azure, bir görüntüyü depolamadan önce otomatik olarak şifreler ve Azure Machine Learning görüntüyü çeker.

#### <a name="machine-learning-compute"></a>Machine Learning İşlem

Azure depolama 'da depolanan her işlem düğümü için işletim sistemi diski, Azure Machine Learning depolama hesaplarında Microsoft tarafından yönetilen anahtarlarla şifrelenir. Bu işlem hedefi kısa ömürlü ve hiçbir çalışma sıraya alınmaz kümeler genellikle ölçeği aşağı ölçeklendirilir. Temel alınan sanal makine de sağlanmamıştır ve işletim sistemi diski silinir. Azure disk şifrelemesi, işletim sistemi diski için desteklenmez.

Her bir sanal makinenin işletim sistemi işlemleri için yerel bir geçici diski de vardır. İsterseniz eğitim verilerini hazırlamak için diski kullanabilirsiniz. Disk şifrelenmedi.
Azure 'da bekleyen şifrelemenin nasıl çalıştığı hakkında daha fazla bilgi için bkz. [rest 'de Azure veri şifrelemesi](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest).

### <a name="encryption-in-transit"></a>Aktarım sırasında şifreleme

Azure Machine Learning mikro hizmetler arasındaki iç iletişimin güvenliğini sağlamak ve Puanlama uç noktasına yönelik dış çağrıların güvenliğini sağlamak için SSL kullanabilirsiniz. Tüm Azure depolama erişimi, güvenli bir kanal üzerinden de gerçekleşir.

Daha fazla bilgi için bkz. [bir Web hizmetini Azure Machine Learning aracılığıyla güvenli hale getirmek IÇIN SSL kullanma](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Azure Key Vault kullanma

Azure Machine Learning, çeşitli türlerdeki kimlik bilgilerini depolamak için çalışma alanıyla ilişkili Azure Key Vault örneğini kullanır:

* İlişkili depolama hesabı bağlantı dizesi
* Azure Container Repository deposu örneklerine parolalar
* Veri depolarına yönelik bağlantı dizeleri

Azure HDInsight ve VM 'Ler gibi hedefleri hesaplamak için SSH parolaları ve anahtarları, Microsoft aboneliğiyle ilişkili ayrı bir anahtar kasasında depolanır. Azure Machine Learning, kullanıcılar tarafından sunulan herhangi bir parolayı veya anahtarı depolamaz. Bunun yerine, denemeleri çalıştırmak için VM 'Leri ve HDInsight 'a bağlanmak üzere kendi SSH anahtarlarını oluşturur, yetkilendirir ve depolar.

Her çalışma alanı, çalışma alanıyla aynı ada sahip ilişkili, sistem tarafından atanan bir yönetilen kimliğe sahiptir. Bu yönetilen kimliğin, anahtar kasasındaki tüm anahtar, gizli dizi ve sertifikalara erişimi vardır.

## <a name="monitoring"></a>İzleme

### <a name="metrics"></a>Ölçümler

Azure Machine Learning çalışma alanınızın ölçümlerini görüntülemek ve izlemek için Azure Izleyici ölçümlerini kullanabilirsiniz. [Azure Portal](https://portal.azure.com), çalışma alanınızı seçin ve ardından **ölçümler**' i seçin:

[bir çalışma alanı için örnek ölçümleri gösteren ekran görüntüsü ![](./media/enterprise-readiness/workspace-metrics.png)](./media/enterprise-readiness/workspace-metrics-expanded.png)

Ölçümler, çalıştırmalar, dağıtımlar ve kayıtlar hakkındaki bilgileri içerir.

Daha fazla bilgi için bkz. [Azure izleyici 'de ölçümler](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Etkinlik günlüğü

Çalışma alanında gerçekleştirilen çeşitli işlemleri görmek için bir çalışma alanının etkinlik günlüğünü görüntüleyebilirsiniz. Günlük, işlem adı, olay başlatıcısı ve zaman damgası gibi temel bilgileri içerir.

Bu ekran görüntüsünde bir çalışma alanının etkinlik günlüğü gösterilmektedir:

[bir çalışma alanının etkinlik günlüğünü gösteren ekran görüntüsü ![](./media/enterprise-readiness/workspace-activity-log.png)](./media/enterprise-readiness/workspace-activity-log-expanded.png)

Puanlama isteği ayrıntıları Application Insights depolanır. Application Insights, bir çalışma alanı oluşturduğunuzda aboneliğinizde oluşturulur. Günlüğe kaydedilen bilgiler HTTPMethod, UserAgent, ComputeType, RequestUrl, StatusCode, RequestId ve Duration gibi alanları içerir.

> [!IMPORTANT]
> Azure Machine Learning çalışma alanındaki bazı eylemler etkinlik günlüğüne bilgileri günlüğe vermez. Örneğin, bir eğitimin başlangıcı ve bir modelin kaydı günlüğe kaydedilmez.
>
> Bu eylemlerden bazıları, çalışma alanınızın **Etkinlikler** alanında görünür ancak bu bildirimler etkinliği kimin başlattığını göstermez.

## <a name="data-flow-diagrams"></a>Veri akışı diyagramları

### <a name="create-workspace"></a>Çalışma alanı oluşturma

Aşağıdaki diyagramda, çalışma alanı oluşturma iş akışı gösterilmektedir.

* Kullanıcı, desteklenen Azure Machine Learning istemcilerinden (Azure CLı, Python SDK, Azure portal) birinden Azure AD 'ye oturum açar ve uygun Azure Resource Manager belirtecini ister.
* Kullanıcı, çalışma alanını oluşturmak için Azure Resource Manager çağırır. 
* Azure Resource Manager, çalışma alanını sağlamak için Azure Machine Learning kaynak sağlayıcısıyla iletişim kurar.

Çalışma alanı oluşturma sırasında kullanıcının aboneliğinde ek kaynaklar oluşturulur:

* Key Vault (gizli dizileri depolamak için)
* Bir Azure depolama hesabı (blob ve dosya paylaşma dahil)
* Azure Container Registry (çıkarım/Puanlama ve deneme için Docker görüntülerini depolamak için)
* Application Insights (Telemetriyi depolamak için)

Kullanıcı, gerektiğinde bir çalışma alanına (Azure Kubernetes hizmeti veya VM 'Ler gibi) bağlı diğer işlem hedeflerini de temin edebilir.

[![çalışma alanı iş akışı oluşturma](./media/enterprise-readiness/create-workspace.png)](./media/enterprise-readiness/create-workspace-expanded.png)

### <a name="save-source-code-training-scripts"></a>Kaynak kodunu kaydet (eğitim betikleri)

Aşağıdaki diyagramda, kod anlık görüntüsü iş akışı gösterilmektedir.

Bir Azure Machine Learning çalışma alanıyla ilişkili, kaynak kodu (eğitim betikleri) içeren dizinlerdir (denemeleri). Bu betikler yerel makinenizde ve bulutta depolanır (aboneliğiniz için Azure Blob depolama alanında). Kod anlık görüntüleri, geçmiş denetimi için yürütme veya denetleme için kullanılır.

[![Code Snapshot iş akışı](./media/enterprise-readiness/code-snapshot.png)](./media/enterprise-readiness/code-snapshot-expanded.png)

### <a name="training"></a>Eğitim

Aşağıdaki diyagramda eğitim iş akışı gösterilmektedir.

* Azure Machine Learning, önceki bölümde kaydedilen kod anlık görüntüsünün anlık görüntü KIMLIĞIYLE çağırılır.
* Azure Machine Learning, bir çalıştırma KIMLIĞI (isteğe bağlı) ve Machine Learning bir hizmet belirteci oluşturur. Bu, daha sonra Machine Learning hizmetiyle iletişim kurmak için Machine Learning İşlem/VM gibi işlem hedefleri tarafından daha sonra kullanılır.
* Eğitim işlerinizi çalıştırmak için yönetilen bir işlem hedefi (Machine Learning İşlem gibi) veya yönetilmeyen bir işlem hedefi (VM 'Ler gibi) seçebilirsiniz. Her iki senaryo için de veri akışları aşağıda verilmiştir:
   * Microsoft aboneliğindeki bir anahtar kasasında SSH kimlik bilgileri tarafından erişilen VM 'Ler/HDInsight. Azure Machine Learning, işlem hedefinde yönetim kodu çalıştırır:

   1. Ortamı hazırlar. (Docker, VM 'Ler ve yerel bilgisayarlar için bir seçenektir. Docker kapsayıcılarındaki denemeleri 'ın nasıl çalıştığını anlamak için Machine Learning İşlem için aşağıdaki adımlara bakın.)
   1. Kodu indirir.
   1. Ortam değişkenlerini ve konfigürasyonları ayarlar.
   1. Kullanıcı komut dosyalarını çalıştırır (önceki bölümde bahsedilen kod anlık görüntüsü).

   * Machine Learning İşlem, çalışma alanı tarafından yönetilen bir kimlikle erişilir.
Machine Learning İşlem yönetilen bir işlem hedefi olduğundan (Microsoft tarafından yönetilen), Microsoft Aboneliğiniz kapsamında çalışır.

   1. Gerekirse uzak Docker oluşturma kapalıdır.
   1. Yönetim kodu kullanıcının Azure dosya paylaşımında yazılır.
   1. Kapsayıcı bir başlangıç komutuyla başlatılır. Diğer bir deyişle, önceki adımda açıklandığı gibi yönetim kodu.

#### <a name="querying-runs-and-metrics"></a>Çalıştırmaları ve ölçümleri sorgulama

Aşağıdaki akış diyagramında, bu adım, eğitim işlem hedefi Cosmos DB veritabanındaki depolamadan Azure Machine Learning için yeniden çalıştırma ölçümleri yazdığında meydana gelir. İstemciler, Azure Machine Learning çağırabilir. Machine Learning, Cosmos DB veritabanından çekme ölçümlerini açıp istemciye geri döndürmeyecektir.

[Eğitim iş akışı ![](./media/enterprise-readiness/training-and-metrics.png)](./media/enterprise-readiness/training-and-metrics-expanded.png)

### <a name="creating-web-services"></a>Web Hizmetleri oluşturma

Aşağıdaki diyagramda çıkarım iş akışı gösterilmektedir. Çıkarım veya model Puanlama, dağıtılan modelin, en yaygın olarak üretim verileri üzerinde tahmin için kullanıldığı aşamadır.

Ayrıntılar aşağıda verilmiştir:

* Kullanıcı, Azure Machine Learning SDK gibi bir istemciyi kullanarak bir modeli kaydeder.
* Kullanıcı bir model, bir puan dosyası ve diğer model bağımlılıklarını kullanarak bir görüntü oluşturur.
* Docker görüntüsü oluşturulup Azure Container Registry depolanır.
* Web hizmeti, önceki adımda oluşturulan görüntüyü kullanarak işlem hedefine (Container Instances/AKS) dağıtılır.
* Puanlama isteği ayrıntıları, Kullanıcı aboneliğindeki Application Insights depolanır.
* Telemetri ayrıca Microsoft/Azure aboneliğine de gönderilir.

[![çıkarım iş akışı](./media/enterprise-readiness/inferencing.png)](./media/enterprise-readiness/inferencing-expanded.png)

## <a name="next-steps"></a>Sonraki adımlar

* [SSL ile güvenli Azure Machine Learning Web Hizmetleri](how-to-secure-web-service.md)
* [Web hizmeti olarak dağıtılan bir Machine Learning modeli kullanma](how-to-consume-web-service.md)
* [Toplu tahminleri çalıştırma](how-to-run-batch-predictions.md)
* [Application Insights Azure Machine Learning modellerinizi izleyin](how-to-enable-app-insights.md)
* [Üretimde modeller için veri toplama](how-to-enable-data-collection.md)
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Azure sanal ağ ile Azure Machine Learning kullanma](how-to-enable-virtual-network.md)
* [Öneri sistemleri oluşturmak için en iyi uygulamalar](https://github.com/Microsoft/Recommenders)
* [Azure 'da gerçek zamanlı bir öneri API 'SI oluşturun](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
