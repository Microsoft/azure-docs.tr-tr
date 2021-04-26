---
title: Kitaplık yükleme hatalarını giderme
description: Bu öğretici, kitaplık yükleme hatalarını giderme hakkında genel bakış sağlar.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 006abf62c605c2ca34fd1adeadee8e29ae0fb8fb
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588318"
---
# <a name="troubleshoot-library-installation-errors"></a>Kitaplık yükleme hatalarını giderme 
Üçüncü tarafa veya yerel olarak oluşturulmuş bir kodu uygulamalarınız için kullanılabilir hale getirmek için, bir kitaplığı sunucusuz Apache Spark havuzlarınızdan birine yükleyebilirsiniz. requirements.txt dosyasında listelenen paketler, havuz başlatma sırasında PyPi 'den indirilir. Bu gereksinimler dosyası, bu Spark havuzundan bir Spark örneği oluşturulduğu her seferinde kullanılır. Spark havuzu için bir kitaplık yüklendikten sonra, aynı havuzu kullanan tüm oturumlarda kullanılabilir. 

Bazı durumlarda, Apache Spark havuzunuzdaki bir kitaplığın görünmediğinden emin olabilirsiniz. Bu durum genellikle, belirtilen requirements.txt veya belirtilen kitaplıklarda bir hata olduğunda meydana gelir. Kitaplık yükleme işleminde bir hata oluştuğunda, Apache Spark havuzu SYNAPSE temel çalışma zamanında belirtilen kitaplıklara geri döndürülür.

Bu belgenin amacı, yaygın sorunları sağlamaktır ve kitaplık yükleme hatalarını ayıklamanıza yardımcı olur.

## <a name="force-update-your-apache-spark-pool"></a>Apache Spark havuzunuzu güncelleştirmeye zorla
Apache Spark havuzunuzdaki kitaplıkları güncelleştirdiğinizde, havuz yeniden başlatıldıktan sonra bu değişiklikler alınacaktır. Etkin işleriniz varsa, bu işler Spark havuzunun orijinal sürümünde çalışmaya devam edecektir.

**Yeni ayarları zorlama** seçeneğini belirleyerek değişiklikleri uygulamaya zorlayabilirsiniz. Bu ayar, seçili Spark havuzunun tüm geçerli oturumlarını sonlandıracaktır. Oturumlar sona erdikten sonra, havuzun yeniden başlatılmasını beklemeniz gerekir. 

![Python kitaplıklarını ekleme](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Python kitaplıklarını ekleme")

## <a name="track-installation-progress"></a>Yükleme ilerlemesini izleme
Bir havuzun yeni bir kitaplık kümesiyle güncelleştirildiği her seferinde sistem tarafından ayrılmış Spark işi başlatılır. Bu Spark işi, kitaplık yüklemesinin durumunu izlemeye yardımcı olur. Yükleme, kitaplık çakışmaları veya diğer sorunlar nedeniyle başarısız olursa Spark havuzu önceki veya varsayılan durumuna geçer. 

Ayrıca, kullanıcılar bağımlılık çakışmalarını belirlemek için yükleme günlüklerini inceleyebilir veya havuz güncelleştirmesi sırasında hangi kitaplıkların yüklü olduğunu görebilirler.

Bu günlükleri görüntülemek için:
1. **İzleyici** sekmesinde Spark uygulamaları listesine gidin. 
2. Havuz güncelunuza karşılık gelen System Spark uygulama işini seçin. Bu sistem işleri *Systemreservedjob-LibraryManagement* başlığı altında çalışır.
   ![Sisteme ayrılan kitaplık işinin vurgualdığı ekran görüntüsü.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Sistem kitaplığı işini görüntüle")
3. **Sürücü** ve **stdout** günlüklerini görüntülemek için geçiş yapın. 
4. Sonuçlar içinde, paketlerinizin yüklenmesiyle ilgili günlükleri görürsünüz.
    ![Sisteme ayrılan kitaplık işi sonuçlarının vurgualdığı ekran görüntüsü.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Sistem kitaplığı işinin ilerlemesini görüntüleme")

## <a name="validate-your-permissions"></a>İzinlerinizi doğrulayın
Kitaplıkları yüklemek ve güncelleştirmek için, Azure SYNAPSE Analytics çalışma alanına bağlı birincil Azure Data Lake Storage 2. depolama hesabında **Depolama Blobu veri katılımcısı** veya **Depolama Blobu veri sahibi** izinlerine sahip olmanız gerekir.

Bu izinlere sahip olduğunu doğrulamak için aşağıdaki kodu çalıştırabilirsiniz:

```python
from pyspark.sql.types import StructType,StructField, StringType, IntegerType
data2 = [("James","Smith","Joe","4355","M",3000),
    ("Michael","Rose","Edward","40288","F",4000)
  ]

schema = StructType([ \
    StructField("firstname",StringType(),True), \
    StructField("middlename",StringType(),True), \
    StructField("lastname",StringType(),True), \
    StructField("id", StringType(), True), \
    StructField("gender", StringType(), True), \
    StructField("salary", IntegerType(), True) \
  ])
 
df = spark.createDataFrame(data=data2,schema=schema)

df.write.csv("abfss://<<ENTER NAME OF FILE SYSTEM>>@<<ENTER NAME OF PRIMARY STORAGE ACCOUNT>>.dfs.core.windows.net/validate_permissions.csv")

```
Bir hata alırsanız gerekli izinleriniz muhtemelen eksik olabilir. Gerekli izinleri alma hakkında bilgi edinmek için şu belgeyi ziyaret edin: [Depolama Blobu veri katılımcısı veya Depolama Blobu veri sahibi Izinleri atama](../../storage/common/storage-auth-aad-rbac-portal.md#assign-an-azure-built-in-role).

Ayrıca, bir işlem hattı çalıştırıyorsanız, çalışma alanı MSI, Depolama Blobu veri sahibine veya Depolama Blobu veri katılımcısı izinlerine de sahip olmalıdır. Çalışma alanı kimliğinizi bu izin olarak verme hakkında bilgi edinmek için, bkz. [çalışma alanı yönetilen kimliği için Izin verme](../security/how-to-grant-workspace-managed-identity-permissions.md).

## <a name="check-the-environment-configuration-file"></a>Ortam yapılandırma dosyasını denetleyin
Conda ortamını yükseltmek için bir ortam yapılandırma dosyası kullanılabilir. Python havuzu yönetimi için kabul edilebilir bu dosya biçimleri [burada](./apache-spark-manage-python-packages.md)listelenmiştir.

Aşağıdaki kısıtlamalara dikkat etmek önemlidir:
   -  Gereksinimler dosyasının içeriği ek boş satırlar veya karakterler içermemelidir. 
   -  [SYNAPSE çalışma zamanı](apache-spark-version-support.md) , her sunucusuz Apache Spark havuzuna önceden yüklenmiş bir kitaplıklar kümesi içerir. Temel çalışma zamanına önceden yüklenmiş olarak gelen paketler kaldırılamaz veya kaldırılamaz.
   -  PySpark, Python, Scala/Java, .NET veya Spark sürümünün değiştirilmesi desteklenmez.
   -  Python oturumu kapsamlı kitaplıklar yalnızca bir YıML uzantılı dosyaları kabul eder.

## <a name="validate-wheel-files"></a>Tekerlek dosyalarını doğrula
SYNAPSE sunucusuz Apache Spark havuzları, Linux dağıtımını temel alınır. Tekerlek dosyalarını doğrudan PyPI 'dan indirip yüklerken, Linux üzerinde oluşturulmuş ve Spark havuzuyla aynı Python sürümünde çalışan sürümü seçtiğinizden emin olun.

>[!IMPORTANT]
>Özel paketler, oturumlar arasında eklenebilir veya değiştirilebilir. Ancak, güncelleştirilmiş paketi görmek için havuzun ve oturumun yeniden başlatılmasını beklemeniz gerekir.

## <a name="check-for-dependency-conflicts"></a>Bağımlılık çakışmalarını denetle
 Genel olarak, Python bağımlılık çözümlemesi yönetilebilmesi için karmaşık olabilir. Bağımlılık çakışmalarından yerel olarak hata ayıklamanıza yardımcı olmak için SYNAPSE çalışma zamanını temel alarak kendi sanal ortamınızı oluşturabilir ve değişikliklerinizi doğrulayabilirsiniz.

Ortamı yeniden oluşturmak ve güncelleştirmelerinizi doğrulamak için:
 1. SYNAPSE çalışma zamanını yerel olarak yeniden oluşturmak için şablonu [indirin](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml) . Şablon ve gerçek SYNAPSE ortamı arasında hafif farklar olabilir.
   
 2. [Aşağıdaki yönergeleri](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment)kullanarak bir sanal ortam oluşturun. Bu ortam, belirtilen kitaplıklar listesiyle yalıtılmış bir Python yüklemesi oluşturmanıza olanak sağlar. 
    
    ```
    conda myenv create -f environment.yml
    conda activate myenv
    ```
   
 3. ``pip install -r <provide your req.txt file>``Sanal ortamı belirtilen paketleriniz ile güncelleştirmek için kullanın. Yükleme bir hatayla sonuçlandıysa, SYNAPSE temel çalışma zamanına önceden yüklenmiş olan ve belirtilen gereksinimler dosyasında belirtilen özellikler arasında bir çakışma olabilir. Bu bağımlılık çakışmalarının, sunucusuz Apache Spark havuzunuzdaki güncelleştirilmiş kitaplıkları almak için çözümlenmelidir.

>[!IMPORTANT]
>PIP ve Conda birlikte kullanırken sorunlar Arrise olabilir. PIP ve Conda birleştirilirken, [Önerilen en iyi yöntemleri](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment)izlemek en iyisidir.

## <a name="next-steps"></a>Sonraki adımlar
- Varsayılan kitaplıkları görüntüleme: [Apache Spark sürüm desteği](apache-spark-version-support.md)
