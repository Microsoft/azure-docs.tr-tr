---
title: Apache Spark için Python kitaplıklarını yönetme
description: Azure SYNAPSE Analytics 'te Apache Spark tarafından kullanılan Python kitaplıklarını ekleme ve yönetme hakkında bilgi edinin.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 1d64233fc477ec25f91bb73c744b10210571df41
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588352"
---
# <a name="manage-python-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te Apache Spark için Python kitaplıklarını yönetme

Kitaplıklar, programlarınıza veya projelerinize dahil etmek isteyebileceğiniz yeniden kullanılabilir kod sağlar. 

Çeşitli nedenlerle sunucusuz Apache Spark havuz ortamınızı güncelleştirmeniz gerekebilir. Örneğin, şunları görebilirsiniz:
- temel bağımlılıklarından biri yeni bir sürüm yayımlamıştır.
- Machine Learning modelinize eğitim veya verilerinizi hazırlama hakkında ek bir pakete ihtiyacınız vardır.
- daha iyi bir paket bulduk ve artık eski pakete ihtiyacınız yoktur.

Üçüncü tarafa veya yerel olarak oluşturulmuş bir kodu uygulamalarınız için kullanılabilir hale getirmek için, bir kitaplığı sunucusuz Apache Spark havuzlarınızdan veya Not defteri oturumlarından birine yükleyebilirsiniz. Bu makalede, daha az Apache Spark havuzunuzdaki Python kitaplıklarını nasıl yönetebileceğinizi ele göndereceğiz.

## <a name="default-installation"></a>Varsayılan yükleme
Azure SYNAPSE Analytics 'teki Apache Spark, ortak veri Mühendisliği, veri hazırlama, makine öğrenimi ve veri görselleştirme görevlerine yönelik eksiksiz bir kitaplık kümesine sahiptir. Tam kitaplıklar listesi, [Apache Spark sürüm desteği](apache-spark-version-support.md)' nde bulunabilir. 

Bir Spark örneği başlatıldığında, bu kitaplıklar otomatik olarak dahil edilir. Ek Python ve özel olarak oluşturulmuş paketler Spark havuzunda ve oturum düzeyinde eklenebilir.

## <a name="pool-libraries"></a>Havuz kitaplıkları
Spark uygulamanız için kullanmak istediğiniz Python kitaplıklarını tanımladıktan sonra bunları bir Spark havuzuna yükleyebilirsiniz. Havuz düzeyi kitaplıklar, havuzda çalışan tüm not defterleri ve işler için kullanılabilir.

Bir kümeye kitaplık yüklemenin iki birincil yolu vardır:
-  Çalışma alanı paketi olarak karşıya yüklenen bir çalışma alanı kitaplığı yükleme.
-  PyPI, Conda-Forge gibi depolardan paketleri yüklemek için bir *requirements.txt* veya *Conda Environment. yml* ortam belirtimi sağlayın.

> [!IMPORTANT]
> - Yüklemekte olduğunuz Paket büyükse veya yüklemesi uzun sürüyorsa, bu, Spark örneği başlangıç süresini etkiler.
> - PySpark, Python, Scala/Java, .NET veya Spark sürümünün değiştirilmesi desteklenmez.
> - PyPI, Conda-Forge gibi dış depolardan paket yükleme veya varsayılan Conda kanalları DEP özellikli çalışma alanları içinde desteklenmez.

### <a name="install-python-packages"></a>Python paketlerini yükler
Python paketleri, bir ortam belirtim dosyası sağlayarak PyPI ve Conda-Forge gibi depolardan yüklenebilir. 

#### <a name="environment-specification-formats"></a>Ortam belirtimi biçimleri

##### <a name="pip-requirementstxt"></a>PıP requirements.txt
Ortamı yükseltmek için bir *requirements.txt* dosyası ( `pip freeze` komuttan çıkış) kullanılabilir. Bir havuz güncelleştirilirken, bu dosyada listelenen paketler Pypı 'den indirilir. Daha sonra tüm bağımlılıklar önbelleğe alınır ve daha sonra havuzun yeniden kullanılması için kaydedilir. 

Aşağıdaki kod parçacığında gereksinimler dosyasının biçimi gösterilmektedir. Pypı paket adı, tam bir sürüm ile birlikte listelenir. Bu dosya, [PIP dondurma](https://pip.pypa.io/en/stable/reference/pip_freeze/) başvuru belgelerinde açıklanan biçimi izler. 

Bu örnek, belirli bir sürümü sabitler. 
```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```
##### <a name="yml-format-preview"></a>YıLML biçimi (Önizleme)
Ayrıca, havuz ortamını güncelleştirmek için bir *Environment. yıml* dosyası da sağlayabilirsiniz. Bu dosyada listelenen paketler varsayılan Conda kanallarından, Conda-Forge 'dan ve Pypı 'den indirilir. Yapılandırma seçeneklerini kullanarak diğer kanalları belirtebilir veya varsayılan kanalları kaldırabilirsiniz.

Bu örnek, kanalları ve Conda/PyPI bağımlılıklarını belirtir. 

```
name: stats2
channels:
- defaults
dependencies:
- bokeh
- numpy
- pip:
  - matplotlib
  - koalas==1.7.0
```
Bu ortamdan ortam oluşturma hakkında ayrıntılı bilgi için, bkz. [Environment. yıml dosyasından ortam oluşturma](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment
).

#### <a name="update-python-packages"></a>Python paketlerini güncelleştirme
Bir ortam belirtimi dosyasını veya Spark havuzunda yüklemek istediğiniz kitaplıkların kümesini tanımladıktan sonra, Azure SYNAPSE Studio veya Azure portal giderek Spark havuz kitaplıklarını güncelleştirebilirsiniz. Burada, ortam belirtimini sağlayabilir ve yüklenecek çalışma alanı kitaplıklarını seçebilirsiniz. 

Değişiklikler kaydedildikten sonra, bir Spark işi yüklemeyi çalıştırır ve elde edilen ortamı daha sonra yeniden kullanmak üzere önbelleğe alınır. İş tamamlandıktan sonra, yeni Spark işleri veya Not defteri oturumları güncelleştirilmiş havuz kitaplıklarını kullanacaktır. 

##### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Paketleri Azure SYNAPSE Studio veya Azure portal yönetme
Spark havuzu kitaplıkları Azure SYNAPSE Studio veya Azure portal yönetilebilir. 

Bir Spark havuzuna kitaplıkları güncelleştirmek veya eklemek için:
1. Azure portal Azure SYNAPSE Analytics çalışma alanınıza gidin.

    **Azure Portal** güncelleştiriyorsanız:

    - **SYNAPSE kaynakları** bölümünde, **Apache Spark havuzları** sekmesini seçin ve listeden bir Spark havuzu seçin.
     
    - Spark havuzunun **Ayarlar** bölümünden **paketleri** seçin.
  
    ![Ortam yapılandırma dosyasını karşıya yükle düğmesini vurgulayan ekran görüntüsü.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Python kitaplıklarını ekleme")
   
    **SYNAPSE Studio**'dan güncelleştiriyorsanız:
    - Ana Gezinti panelinden **Yönet** ' i seçin ve ardından **Apache Spark havuzları**' nı seçin.

    - Belirli bir Spark havuzu için **paketler** bölümünü seçin.
    ![Studio 'dan karşıya yükleme ortamı yapılandırma seçeneğini vurgulayan ekran görüntüsü.](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Studio 'dan Python kitaplıklarını ekleme")
   
2. Sayfanın  **paketler** bölümündeki dosya seçicisini kullanarak ortam yapılandırma dosyasını karşıya yükleyin.
3. Ayrıca, havuzunuza eklemek için ek **çalışma alanı paketleri** de seçebilirsiniz. 
4. Değişikliklerinizi kaydettikten sonra, belirtilen kitaplıkları yüklemek ve önbelleğe almak için bir sistem işi tetiklenecektir. Bu işlem, genel oturum başlatma süresini azaltmaya yardımcı olur. 
5. İş başarıyla tamamlandıktan sonra, tüm yeni oturumlar güncelleştirilmiş havuz kitaplıklarını seçer.

> [!IMPORTANT]
> **Yeni ayarları zorlama** seçeneğini belirleyerek seçili Spark havuzunun tüm geçerli oturumlarını sonlandıracaksınız. Oturumlar sona erdikten sonra, havuzun yeniden başlatılmasını beklemeniz gerekir. 
>
> Bu ayar işaretlenmezse, geçerli Spark oturumunun bitmesini beklemeniz veya el ile durdurmanız gerekir. Oturum sona erdikten sonra havuzun yeniden başlatılmasına izin vermek gerekir.


##### <a name="track-installation-progress-preview"></a>Yükleme ilerlemesini izleme (Önizleme)
Bir havuzun yeni bir kitaplık kümesiyle güncelleştirildiği her seferinde sistem tarafından ayrılmış Spark işi başlatılır. Bu Spark işi, kitaplık yüklemesinin durumunu izlemeye yardımcı olur. Yükleme, kitaplık çakışmaları veya diğer sorunlar nedeniyle başarısız olursa Spark havuzu önceki veya varsayılan durumuna geçer. 

Ayrıca, kullanıcılar bağımlılık çakışmalarını belirlemek için yükleme günlüklerini inceleyebilir veya havuz güncelleştirmesi sırasında hangi kitaplıkların yüklü olduğunu görebilirler.

Bu günlükleri görüntülemek için:
1. **İzleyici** sekmesinde Spark uygulamaları listesine gidin. 
2. Havuz güncelunuza karşılık gelen System Spark uygulama işini seçin. Bu sistem işleri *Systemreservedjob-LibraryManagement* başlığı altında çalışır.
   ![Sisteme ayrılan kitaplık işinin vurgualdığı ekran görüntüsü.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Sistem kitaplığı işini görüntüle")
3. **Sürücü** ve **stdout** günlüklerini görüntülemek için geçiş yapın. 
4. Sonuçlar içinde, bağımlılıklarınızın yüklenmesiyle ilgili günlükleri görürsünüz.
    ![Sisteme ayrılan kitaplık işi sonuçlarının vurgualdığı ekran görüntüsü.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Sistem kitaplığı işinin ilerlemesini görüntüleme")

## <a name="install-wheel-files"></a>Tekerlek dosyalarını yükler
Python tekerlek dosyaları, Python kitaplıklarının paketlenmesi için yaygın bir yoldur. Azure SYNAPSE Analytics 'te kullanıcılar, tekerlek dosyalarını Azure Data Lake Storage hesabını iyi bilinen bir konuma yükleyebilir veya Azure SYNAPSE çalışma alanı paketleri arabirimini kullanarak karşıya yükleyebilir.

### <a name="workspace-packages-preview"></a>Çalışma alanı paketleri (Önizleme)
Çalışma alanı paketleri özel veya özel tekerlek dosyaları olabilir. Bu paketleri çalışma alanınıza yükleyebilir ve daha sonra belirli bir Spark havuzuna atayabilirsiniz.

Çalışma alanı paketleri eklemek için:
1.   >  **Çalışma alanı paketlerini** Yönet sekmesine gidin.
2. Dosya seçiciyi kullanarak tekerlek dosyalarınızı karşıya yükleyin.
3. Dosyalar Azure SYNAPSE çalışma alanına yüklendikten sonra, bu paketleri belirli bir Apache Spark havuzuna ekleyebilirsiniz.

![Çalışma alanı paketlerini vurgulayan ekran görüntüsü.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Çalışma alanı paketlerini görüntüle")

>[!WARNING]
>- Azure SYNAPSE içinde, bir Apache Spark havuzu çalışma alanı paketleri olarak yüklenen veya iyi bilinen bir Azure Data Lake Storage yolu içinde karşıya yüklenen özel kitaplıkları kullanabilir. Ancak, bu seçeneklerin her ikisi de aynı Apache Spark havuzunda aynı anda kullanılamaz. Her iki yöntem kullanılarak paketler sağlanmışsa, yalnızca çalışma alanı paketleri listesinde belirtilen tekerlek dosyaları yüklenir. 
>
>- Belirli bir Apache Spark havuzuna paket yüklemek için çalışma alanı paketleri (Önizleme) kullanıldığında, artık aynı havuzdaki depolama hesabı yolunu kullanarak paketleri belirtmizin bir sınırlaması vardır.  

### <a name="storage-account"></a>Depolama hesabı
Özel yerleşik tekerlek paketleri, tüm tekerlek dosyalarını SYNAPSE çalışma alanıyla bağlantılı Azure Data Lake Storage (Gen2) hesabına yükleyerek Apache Spark havuza yüklenebilir. 

Dosyalar, depolama hesabının varsayılan kapsayıcısında aşağıdaki yola yüklenmelidir: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

>[!WARNING]
> Bazı durumlarda, zaten mevcut değilse yukarıdaki yapıya göre dosya yolunu oluşturmanız gerekebilir. Örneğin, klasör zaten yoksa klasörü içine eklemeniz gerekebilir ```python``` ```libraries``` .

> [!IMPORTANT]
> Azure DataLake Storage metodunu kullanarak özel kitaplıklar yüklemek için, Azure SYNAPSE Analytics çalışma alanına bağlı birincil Gen2 depolama hesabında **Depolama Blobu veri katılımcısı** veya **Depolama Blobu veri sahibi** izinlerine sahip olmanız gerekir.


## <a name="session-scoped-packages-preview"></a>Oturum kapsamlı paketler (Önizleme)
Havuz düzeyi paketlerine ek olarak, bir not defteri oturumunun başlangıcında oturum kapsamlı kitaplıklar da belirtebilirsiniz.  Oturum kapsamlı kitaplıklar, bir not defteri oturumunda özel Python ortamlarını belirtmenizi ve kullanmanızı sağlar. 

Oturum kapsamlı kitaplıklar kullanırken aşağıdaki noktaları göz önünde bulundurmanız önemlidir:
   - Oturum kapsamlı kitaplıklar yüklediğinizde, belirtilen kitaplıklara yalnızca geçerli not defteri erişimi vardır. 
   - Bu kitaplıklar, aynı Spark havuzunu kullanan diğer oturumları veya işleri etkilemez. 
   - Bu kitaplıklar temel çalışma zamanı ve havuz düzeyi kitaplıklarının üzerine yüklenir. 
   - Not defteri kitaplıkları en yüksek önceliğe sahip olur.

Oturum kapsamlı paketleri belirtmek için:
1.  Seçili Spark havuzuna gidin ve oturum düzeyi kitaplıklarını etkinleştirmiş olduğunuzdan emin olun.  Bu ayarı,   >  **Apache Spark havuzu**  >  **paketlerini** Yönet sekmesine giderek etkinleştirebilirsiniz. ![oturum paketlerini etkinleştirin.](./media/apache-spark-azure-portal-add-libraries/enable-session-packages.png "Oturum paketlerini etkinleştir")
2.  Ayar uygulandıktan sonra bir not defteri açabilir ve **oturum paketlerini Yapılandır**' ı seçebilirsiniz >  .
  ![Oturum paketlerini belirtin.](./media/apache-spark-azure-portal-add-libraries/update-session-notebook.png "Güncelleştirme oturumu yapılandırması")
3.  Burada, bir oturum içindeki paketleri yüklemek veya yükseltmek için Conda *Environment. yıml* dosyasını karşıya yükleyebilirsiniz. Oturumunuzu başlattığınızda, belirtilen kitaplıklar yüklenecek. Oturumunuz sona erdikten sonra bu kitaplıklar, oturumunuza özgü oldukları için artık kullanılabilir olmayacaktır.

## <a name="verify-installed-libraries"></a>Yüklü kitaplıkları doğrulama
Doğru kitaplıkların doğru sürümlerinin Pypı 'den yüklenip yüklenmediğini doğrulamak için aşağıdaki kodu çalıştırın:
```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
Bazı durumlarda, Conda paket sürümlerini görüntülemek için paket sürümünü tek tek incelemeniz gerekebilir.

## <a name="next-steps"></a>Sonraki adımlar
- Varsayılan kitaplıkları görüntüleme: [Apache Spark sürüm desteği](apache-spark-version-support.md)
- Kitaplık yükleme hatalarını giderme: [kitaplık hatalarında sorun giderme](apache-spark-troubleshoot-library-errors.md)
- Azure Data Lake Storage hesabınızı kullanarak özel bir Conda kanalı oluşturun: [Conda özel kanalları](./spark/../apache-spark-custom-conda-channel.md)
