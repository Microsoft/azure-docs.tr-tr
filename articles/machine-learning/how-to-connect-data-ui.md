---
title: Azure 'da depolama hizmetleri 'nde verilere bağlanma
titleSuffix: Azure Machine Learning
description: Azure 'daki depolama hizmetleri 'ndeki verilere Azure Machine Learning Studio ile güvenli bir şekilde bağlanmak için veri depoları ve veri kümeleri oluşturun.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.custom: how-to, data4ml
ms.openlocfilehash: a0265984c4ae83b8869071017e2fb571a7ff548c
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107027661"
---
# <a name="connect-to-data-with-the-azure-machine-learning-studio"></a>Azure Machine Learning Studio ile verilere bağlanma

Bu makalede, [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md)ile verilerinize nasıl erişebileceğinizi öğrenin. Azure 'daki depolama hizmetlerinde verilerinize [Azure Machine Learning veri depoları](how-to-access-data.md)ile bağlanın ve ardından bu verileri ml iş akışlarınızda görevler için [Azure Machine Learning veri kümeleriyle](how-to-create-register-datasets.md)paketleyin.

Aşağıdaki tabloda, veri depolarının ve veri kümelerinin avantajları tanımlanmaktadır ve özetlenmektedir. 

|Nesne|Description| Avantajlar|   
|---|---|---|
|Veri depoları| Çalışma alanıyla ilişkili [Key Vault](https://azure.microsoft.com/services/key-vault/) abonelik kimliğiniz ve belirteç yetkilendirmesi gibi bağlantı bilgilerinizi depolayarak Azure 'da depolama hizmetinize güvenli bir şekilde bağlanın | Bilgileriniz güvenli bir şekilde depolandığından, <br><br> <li> &nbsp; &nbsp; Kimlik doğrulama &nbsp; kimlik bilgilerini &nbsp; veya &nbsp; özgün &nbsp; veri kaynaklarını riske yerleştirmeyin. <li> Artık betiklerinizde kalıcı olarak kod yazmanız gerekmez.
|Veri kümeleri| Bir veri kümesi oluşturduğunuzda ilgili veri kaynağı konumuna bir başvurunun yanı sıra meta verilerinin bir kopyasını oluşturmuş olursunuz. Veri kümeleri ile şunları yapabilirsiniz <br><br><li> Model eğitimi sırasında verilere erişin.<li> Veri paylaşma ve diğer kullanıcılarla işbirliği yapma.<li> Veri araştırması için Pandas gibi açık kaynak kitaplıklarından yararlanın. | Veri kümeleri geç olarak değerlendirildiğinden ve veriler mevcut konumunda kaldığı için <br><br><li>Depolamadaki verilerin tek bir kopyasını saklayın.<li> Ek depolama maliyeti yoktur <li> Özgün veri kaynaklarınızı yanlışlıkla değiştirmeyi riske eklemeyin.<li>ML iş akışı performans hızlarını geliştirir. 

Datamağazaların ve veri kümelerinin Azure Machine Learning genel veri erişimi iş akışına uygun olduğunu anlamak için, [güvenli erişim verileri](concept-data.md#data-workflow) makalesine bakın.

Kod ilk deneyimi için, [Azure Machine Learning Python SDK 'sını](/python/api/overview/azure/ml/) kullanmak için aşağıdaki makalelere bakın:
* [Veri depolarıyla Azure Storage Services 'A bağlanın](how-to-access-data.md). 
* [Azure Machine Learning veri kümeleri oluşturun](how-to-create-register-datasets.md). 

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree)deneyin.

- [Azure Machine Learning Studio](https://ml.azure.com/)'ya erişin.

- Azure Machine Learning çalışma alanı. [Azure Machine Learning çalışma alanı oluşturun](how-to-manage-workspace.md).

    -  Bir çalışma alanı oluşturduğunuzda, bir Azure Blob kapsayıcısı ve bir Azure dosya paylaşımının otomatik olarak çalışma alanına veri depoları olarak kaydedilir. Bunlar `workspaceblobstore` `workspacefilestore` sırasıyla ve olarak adlandırılır. Blob depolaması gereksinimleriniz için yeterliyse, `workspaceblobstore` varsayılan veri deposu olarak ayarlanır ve zaten kullanılmak üzere yapılandırılmıştır. Aksi takdirde, [desteklenen bir depolama türüyle](how-to-access-data.md#matrix)Azure 'da bir depolama hesabı gerekir.
    

## <a name="create-datastores"></a>Veri depoları oluşturma

[Bu Azure depolama çözümlerinden](how-to-access-data.md#matrix)veri depoları oluşturabilirsiniz. **Desteklenmeyen depolama çözümleri için** ve ml denemeleri sırasında veri çıkış maliyetini kaydetmek için, verilerinizi desteklenen bir Azure depolama çözümüne [taşımanız](how-to-access-data.md#move) gerekir. [Veri depoları hakkında daha fazla bilgi edinin](how-to-access-data.md). 

Azure Machine Learning Studio ile birkaç adımda yeni bir veri deposu oluşturun.

> [!IMPORTANT]
> Veri depolama hesabınız bir sanal ağda ise, Studio 'nun verilerinize erişebildiğinden emin olmak için ek yapılandırma adımları gereklidir. Uygun yapılandırma adımlarının uygulandığından emin olmak için bkz. [ağ yalıtımı & gizliliği](how-to-enable-studio-virtual-network.md) .

1. [Azure Machine Learning Studio](https://ml.azure.com/)'da oturum açın.
1. **Yönet** altında sol bölmedeki **veri depoları** ' nı seçin.
1. **+ Yeni veri deposu** seçin.
1. Yeni bir veri deposu oluşturmak ve kaydetmek için formu doldurun. Form, Azure depolama türü ve kimlik doğrulama türü seçimlerinize göre kendisini akıllıca güncelleştirir. Kimlik doğrulama kimlik bilgilerini nerede bulacağınızı öğrenmek için, bu formu doldurmanız gereken [depolama erişimi ve izinler bölümüne](#access-validation) bakın.

Aşağıdaki örnek, bir **Azure blob veri deposu** oluştururken formun nasıl göründüğünü göstermektedir:

![Yeni veri deposu için form](media/how-to-connect-data-ui/new-datastore-form.png)

## <a name="create-datasets"></a>Veri kümeleri oluşturma

Bir veri deposu oluşturduktan sonra verilerinizle etkileşimde bulunmak için bir veri kümesi oluşturun. Veri kümeleri, eğitim gibi makine öğrenimi görevleri için verilerinizi geç tarafından değerlendirilen bir tüketilebilir nesneye paketleyin. [Veri kümeleri hakkında daha fazla bilgi edinin](how-to-create-register-datasets.md).

Dosya kümesi ve TabularDataset olmak üzere iki tür veri kümesi vardır. 
[Filedataset](how-to-create-register-datasets.md#filedataset) 'ler, tek veya birden çok dosya veya genel URL başvuruları oluşturur. Öte yandan, [Tabulardataset](how-to-create-register-datasets.md#tabulardataset) verileri tablosal biçimde temsil eder. . Csv,. tsv,. Parquet,. jsonl dosyalarından ve SQL sorgu sonuçlarından Tabulardataset 'ler oluşturabilirsiniz.

Aşağıdaki adımlar ve animasyon, [Azure Machine Learning Studio](https://ml.azure.com)'da veri kümesi oluşturmayı gösterir.

> [!Note]
> Azure Machine Learning Studio aracılığıyla oluşturulan veri kümeleri otomatik olarak çalışma alanına kaydedilir.

![UI ile veri kümesi oluşturma](./media/how-to-connect-data-ui/create-dataset-ui.gif)

Studio 'da bir veri kümesi oluşturmak için:
1. [Azure Machine Learning Studio](https://ml.azure.com/)'da oturum açın.
1. Sol bölmedeki **varlıklar** bölümünde **veri kümeleri** ' ni seçin.
1. Veri kümenizin kaynağını seçmek için **veri kümesi oluştur** ' u seçin. Bu kaynak yerel dosyalar, bir veri deposu, genel URL veya [Azure açık veri kümeleri](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md)olabilir.
1. Veri kümesi türü için **tablo** veya **Dosya** seçin.
1. **Veri deposu ve dosya seçim** formunu açmak için **İleri ' yi** seçin. Bu formda, oluşturulduktan sonra veri kümenizin saklanacağı yeri ve veri kümeniz için kullanılacak veri dosyalarını seçin.
    1. Verileriniz bir sanal ağda ise doğrulamayı atlamayı etkinleştirin. [Sanal ağ yalıtımı ve gizliliği](how-to-enable-studio-virtual-network.md)hakkında daha fazla bilgi edinin.
    1. Tablo veri kümeleri için, veri kümenizde zaman ilişkili işlemleri etkinleştirmek üzere bir ' timeseries ' nitelik belirtebilirsiniz. [Veri kümenize zaman serisi nitelik eklemeyi](how-to-monitor-datasets.md#studio-dataset)öğrenin.
1. **Ayarları ve önizleme** ve **şema** formlarını doldurmak için **İleri ' yi** seçin; Bunlar dosya türüne göre akıllıca doldurulmuştur ve veri kümenizi bu formlarda oluşturmadan önce daha sonra yapılandırabilirsiniz. 
1. **Ayrıntıları Onayla** formunu gözden geçirmek için **İleri ' yi** seçin. Seçimlerinizi denetleyin ve veri kümeniz için isteğe bağlı bir veri profili oluşturun. [Veri profili oluşturma](#profile)hakkında daha fazla bilgi edinin.
1. Veri kümesi oluşturmayı gerçekleştirmek için **Oluştur** ' u seçin.

<a name="profile"></a>

### <a name="data-profile-and-preview"></a>Veri profili ve Önizleme

Veri kümenizi oluşturduktan sonra, aşağıdaki adımlarla ilgili olarak, aşağıdaki adımları izleyerek profili ve önizlemeyi görüntüleyebildiğinizi doğrulayın. 

1. [Azure Machine Learning Studio](https://ml.azure.com/) 'da oturum açın
1. Sol bölmedeki **varlıklar** bölümünde **veri kümeleri** ' ni seçin.
1. Görüntülemek istediğiniz veri kümesinin adını seçin. 
1. **Keşfet** sekmesini seçin. 
1. **Önizleme** veya **profil** sekmesini seçin. 

![Veri kümesi profilini ve önizlemeyi görüntüle](./media/how-to-connect-data-ui/dataset-preview-profile.gif)

Veri ayarlanmış olup olmadığını doğrulamak için veri kümesi genelinde çok sayıda Özet istatistiği alabilir. Sayısal olmayan sütunlarda yalnızca min, Max ve Error Count gibi temel istatistikler bulunur. Sayısal sütunlarda, istatistiksel ve tahmini quantiles değerlerini de gözden geçirebilirsiniz. 

Özellikle, Azure Machine Learning veri kümesinin veri profili şunları içerir:

>[!NOTE]
> İlgisiz türler içeren özellikler için boş girdiler görüntülenir.

|İstatistik|Description
|------|------
|Özellik| Özetlenen sütunun adı.
|Profil| Çıkarılan türe göre satır içi görselleştirme. Örneğin, dizeler, Boole değerleri ve tarihler değer sayılarına sahip olacaktır, ancak Ondalıklar (Numerics) de yaklaşık histogramlar olur. Bu, verilerin dağıtımını hızlı bir şekilde anlayabilmeniz için size izin verir.
|Tür dağılımı| Bir sütun içindeki türlerin satır içi değer sayısı. Null değerler kendi türlerdir, bu nedenle bu görselleştirme tek veya eksik değerleri algılamak için yararlıdır.
|Tür|Sütunun Çıkarsanan türü. Olası değerler şunlardır: dizeler, Boole değerleri, tarihler ve ondalıklar.
|Min| Sütunun minimum değeri. Boş girdiler, türü bir devralınan sıralamaya (örneğin, Boolean) sahip olmayan özellikler için görünür.
|En yüksek değer| Sütunun en büyük değeri. 
|Count| Sütundaki eksik ve eksik olmayan girdilerin toplam sayısı.
|Eksik sayı yok| Sütundaki eksik girdi sayısı. Boş dizeler ve hatalar değer olarak değerlendirilir ve bu nedenle "eksik sayısı" öğesine katkıda bulunmazlar.
|Dağılım Dilimleri| Verilerin dağıtılması hakkında bir fikir sağlamak için her bir satışla yaklaşık değerler.
|Ortalama| Sütunun aritmetik ortalaması veya ortalaması.
|Standart sapma| Bu sütun verisinin dağılım veya değişim miktarının ölçümü.
|Varyans| Bu sütunun verilerinin ne kadar yayıldığı, ortalama değerinden oluşan ölçüdür. 
|Eğrilikleri| Bu sütun verilerinin normal bir dağılıma göre ne kadar farklı olduğunu gösteren ölçü.
|Sıklık| Bu sütun verilerinin ne kadar süden bir normal dağıtımla karşılaştırıldığı ölçüdür.

## <a name="storage-access-and-permissions"></a>Depolama erişimi ve izinleri

Azure depolama hizmetinize güvenli bir şekilde bağlanabildiğinizden emin olmak için Azure Machine Learning, karşılık gelen veri depolamaya erişmek için izninizin olmasını gerektirir. Bu erişim, veri deposunu kaydetmek için kullanılan kimlik doğrulama kimlik bilgilerine bağlıdır.

### <a name="virtual-network"></a>Sanal ağ

Veri depolama hesabınız bir **Sanal ağda** ise Azure Machine Learning verilerinize erişiminin olduğundan emin olmak için ek yapılandırma adımları gerekir. Veri deposundan oluşturduğunuz ve kayıt yaptırdığınızda uygun yapılandırma adımlarının uygulandığından emin olmak için bkz. [ağ yalıtımı & gizliliği](how-to-enable-studio-virtual-network.md) .  

### <a name="access-validation"></a>Erişim doğrulaması

**İlk veri deposu oluşturma ve kayıt sürecinin bir parçası olarak**, Azure Machine Learning otomatik olarak temel alınan depolama hizmetinin olduğunu ve belirtilen kullanıcının (Kullanıcı adı, hizmet sorumlusu veya SAS belirteci) belirtilen depolama alanına erişimi olduğunu doğrular.

**Veri deposu oluşturulduktan sonra**, bu doğrulama yalnızca, veri deposu nesneleri her alınışında **değil** , temeldeki depolama kapsayıcısına erişim gerektiren yöntemler için gerçekleştirilir. Örneğin, veri deposundan dosyaları indirmek isterseniz doğrulama gerçekleşir; ancak yalnızca varsayılan veri deposundan değişiklik yapmak istiyorsanız doğrulama gerçekleşmez.

Temel depolama hizmetine erişiminizin kimliğini doğrulamak için, oluşturmak istediğiniz veri deposu türüne göre hesap anahtarınızı, paylaşılan erişim imzaları (SAS) belirteçlerini veya hizmet sorumlusunu sağlayabilirsiniz. [Depolama türü matrisi](how-to-access-data.md#matrix) , her veri deposu türüne karşılık gelen desteklenen kimlik doğrulama türlerini listeler.

Hesap anahtarını, SAS belirtecini ve hizmet sorumlusu bilgilerini [Azure Portal](https://portal.azure.com)bulabilirsiniz.

* Kimlik doğrulaması için bir hesap anahtarı veya SAS belirteci kullanmayı planlıyorsanız, sol bölmede **depolama hesapları** ' nı seçin ve kaydetmek istediğiniz depolama hesabını seçin.
  * **Genel bakış** sayfası hesap adı, kapsayıcı ve dosya paylaşma adı gibi bilgiler sağlar.
      1. Hesap anahtarları için, **Ayarlar** bölmesinde **erişim tuşları** ' na gidin.
      1. SAS belirteçleri için, **Ayarlar** bölmesinde **paylaşılan erişim imzaları** ' na gidin.

* Kimlik doğrulaması için bir [hizmet sorumlusu](../active-directory/develop/howto-create-service-principal-portal.md) kullanmayı planlıyorsanız, **uygulama kayıtları** gidin ve kullanmak istediğiniz uygulamayı seçin.
    * Kendisine karşılık gelen **genel bakış** sayfası, Kiracı kimliği ve istemci kimliği gibi gerekli bilgileri içerir.

> [!IMPORTANT]
> * Bir Azure depolama hesabı (hesap anahtarı veya SAS belirteci) için erişim anahtarlarınızı değiştirmeniz gerekiyorsa, yeni kimlik bilgilerini çalışma alanım ve ona bağlı veri depolarıyla eşitlediğinizden emin olun. [Güncelleştirilmiş kimlik bilgilerinizi eşitlemeyi](how-to-change-storage-access-key.md)öğrenin. <br> <br>
> * Aynı ada sahip bir veri deposunu silip yeniden kaydettirir ve başarısız olursa, çalışma alanınızın Azure Key Vault geçici silme etkin olmayabilir. Varsayılan olarak, çalışma alanınız tarafından oluşturulan Anahtar Kasası örneği için geçici silme etkindir, ancak var olan bir Anahtar Kasası kullandıysanız veya 2020 Ekim ' den önce oluşturulmuş bir çalışma alanına sahipseniz bu işlem etkinleştirilmemiş olabilir. Geçici silme özelliğini etkinleştirme hakkında daha fazla bilgi için bkz. [var olan bir Anahtar Kasası Için geçici silme özelliğini etkinleştirme]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault). "

### <a name="permissions"></a>İzinler

Azure Blob kapsayıcısı ve Azure Data Lake Gen 2 depolaması için, kimlik doğrulama kimlik bilgilerinizin **Depolama Blobu veri okuyucusu** erişimi olduğundan emin olun. [Depolama Blobu veri okuyucu](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)hakkında daha fazla bilgi edinin. Hesap SAS belirteci varsayılan olarak izin vermez. 
* Veri **okuma erişimi** için kimlik doğrulama kimlik bilgileriniz, kapsayıcılar ve nesneler için en az liste ve okuma izinlerine sahip olmalıdır. 

* Veri **yazma erişimi** için, yazma ve ekleme izinleri de gereklidir.

## <a name="train-with-datasets"></a>Veri kümeleriyle eğitme

ML modellerinizi eğitmek için Machine Learning denemeleri 'te veri kümelerinizi kullanın. [Veri kümeleriyle eğitme hakkında daha fazla bilgi edinin](how-to-train-with-datasets.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Tabulardataset ve otomatik makine öğrenimi ile eğitimlere yönelik adım adım bir örnek](tutorial-first-experiment-automated-ml.md).

* [Bir modeli eğitme](how-to-set-up-training-targets.md).

* Daha fazla veri kümesi eğitimi örnekleri için bkz. [örnek Not defterleri](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).
