---
title: Azure SQL yönetilen örneği ve Azure SQL veritabanı 'na geçiş için SQL örneklerini değerlendirmek için öğretici
description: Azure geçişi 'nde Azure SQL için değerlendirme oluşturmayı öğrenin
author: rashi-ms
ms.author: rajosh
ms.topic: tutorial
ms.date: 02/07/2021
ms.openlocfilehash: d4078d1403df01475c6055dded2bd012e97af98e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557998"
---
# <a name="tutorial-assess-sql-instances-for-migration-to-azure-sql"></a>Öğretici: Azure SQL 'e geçiş için SQL örneklerini değerlendirme

Azure 'a geçiş sürecinizin bir parçası olarak, bulut hazırlığını ölçmek, riskleri belirlemek ve maliyetleri ve karmaşıklığı tahmin etmek için şirket içi iş yüklerinizi değerlendirmenizi sağlar.
Bu makalede, Azure geçişi: bulma ve değerlendirme aracı kullanılarak Azure SQL 'e geçiş hazırlığı sırasında keşfedilen SQL Server örnekleri veritabanlarının nasıl değerlendirireceğiniz gösterilmektedir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Sunucu yapılandırma ve performans verilerini temel alan bir değerlendirme çalıştırın.
> * Azure SQL değerlendirmesini gözden geçirme 

> [!NOTE]
> Öğreticiler senaryo denemek için en hızlı yolu gösterir ve mümkün olan yerlerde varsayılan seçenekleri kullanır. 


## <a name="prerequisites"></a>Ön koşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun.

- Azure SQL 'e geçiş için SQL Server örneklerinizi değerlendirmek üzere bu öğreticiyi izlemeden önce Azure geçişi gereci kullanarak değerlendirmek istediğiniz SQL örneklerini bulduğunuzdan emin olun, [Bu öğreticiyi izleyin](tutorial-discover-vmware.md)
- Bu özelliği mevcut bir projede denemek istiyorsanız, lütfen bu makaledeki [önkoşulları](how-to-discover-sql-existing-project.md) tamamladığınızdan emin olun.


## <a name="run-an-assessment"></a>Değerlendirme çalıştırma
Bir değerlendirmeyi aşağıdaki gibi çalıştırın:
1. **Windows, Linux ve SQL Server**> **genel bakış** sayfasında **sunucuları değerlendir ve geçir**' e tıklayın.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Azure geçişi için genel bakış sayfası":::
2. **Azure geçişi: bulma ve değerlendirme** üzerinde **değerlendir** ' e tıklayın ve değerlendirme türünü **Azure SQL** olarak seçin.
    :::image type="content" source="./media/tutorial-assess-sql/assess.png" alt-text="Değerlendirme türünü Azure SQL olarak seçmek için açılan menü":::
3. **Sunucuları değerlendir** ' de > değerlendirme türünü **Azure SQL** olarak önceden seçilmiş olarak görebilir ve bulma kaynağı **Azure geçişi gereci tarafından bulunan sunuculara** göre varsayılan olarak görünür.

4. Değerlendirme özelliklerini gözden geçirmek için **Düzenle** ' ye tıklayın.
     :::image type="content" source="./media/tutorial-assess-sql/assess-servers-sql.png" alt-text="Değerlendirme özelliklerinin özelleştirilebileceği düzenleme düğmesi":::
5. Değerlendirme özellikleri > **hedef özellikleri**:
    - **Hedef konum**' da, geçirmek istediğiniz Azure bölgesini belirtin. 
        - Azure SQL yapılandırması ve maliyet önerileri belirttiğiniz konuma göre yapılır. 
    - **Hedef dağıtım türü**' nde,
        - Azure geçişi 'nin Azure SQL MI ve Azure SQL DB 'ye geçiş için SQL örneklerinizin hazır olduğunu değerlendirmesini ve en uygun hedef dağıtım seçeneğini, hedef katmanını, Azure SQL yapılandırmasını ve aylık tahminleri önermesini istiyorsanız, **Önerilen**' i seçin. [Daha Fazla Bilgi](concepts-azure-sql-assessment-calculation.md)
        - SQL örneklerinizin yalnızca Azure SQL veritabanlarına geçirilmesi için hazır olduğunu değerlendirmek ve hedef katmanı, Azure SQL yapılandırması ve aylık tahminleri gözden geçirmek istiyorsanız **Azure SQL DB**' yi seçin.
        - SQL örneklerinizin yalnızca Azure SQL yönetilen örneğine geçirilmesi için hazır olduğunu değerlendirmek ve hedef katmanı, Azure SQL yapılandırması ve aylık tahminleri gözden geçirmek istiyorsanız **Azure SQL mı**' yi seçin.
    - **Ayrılmış kapasite**' de GEÇIŞTEN sonra SQL Server için ayrılmış kapasiteyi kullanmak isteyip istemediğinizi belirtin.
        - Ayrılmış kapasite seçeneğini belirlerseniz, "Indirim (%)" belirtemezsiniz.

6. Değerlendirme özellikleri > değerlendirme **ölçütü**:
    - Boyutlandırma ölçütleri, **performans tabanlı** olarak ayarlanır. Bu, Azure GEÇIŞI 'nin SQL örnekleriyle ilgili performans ölçümlerini ve en iyi boyuta sahip BIR Azure SQL veritabanı ve/veya Azure SQL yönetilen örnek yapılandırması önermek için BT tarafından yönetilen veritabanlarını toplayacağını gösterir. Şunları belirtebilirsiniz:
        - Değerlendirmeyi dayandırmak istediğiniz veri süresini belirtmek için **performans geçmişi** . (Varsayılan bir gün)
        - **Yüzdelik kullanım**, performans örneği için kullanmak istediğiniz yüzdebirlik değerini belirtir. (Varsayılan değer 95 yüzdebirlik)
    - **Rahatlık faktörüyle**, değerlendirme sırasında kullanmak istediğiniz arabelleği belirtin. Bu hesaplar, dönemsel kullanım, kısa performans geçmişi ve gelecekteki kullanımlarda olası artışlar gibi sorunlara yöneliktir. Örneğin, iki bir rahatlık faktörü kullanıyorsanız: 
        
        **Bileşen** | **Etkin kullanım** | **Rakip çarpanı ekleme (2,0)**
        --- | --- | ---
        Çekirdekler | 2  | 4
        Bellek | 8 GB | 16 GB
   
7. **Fiyatlandırma**:
    - **Teklif/lisanslama programında**, kaydolduysanız Azure teklifini belirtin. Şu anda yalnızca Kullandıkça öde ve kullandıkça öde geliştirme/test seçeneklerinden birini belirleyebilirsiniz. 
        - Ayrılmış kapasiteyi uygulayarak ve kullandıkça öde teklifinin üzerine Azure Hibrit Avantajı ek indirim sağlayabilirsiniz. 
        - Kullandıkça Öde geliştirme ve test için Azure Hibrit Avantajı uygulayabilirsiniz. Değerlendirme Şu anda, Kullandıkça Öde geliştirme ve test teklifinin üzerine ayrılmış kapasiteyi uygulamayı desteklemiyor.
    - **Hizmet katmanı**' nda, Azure SQL veritabanı ve/veya Azure SQL yönetilen örneği 'ne geçiş için iş ihtiyaçlarınızı karşılamak üzere en uygun hizmet katmanı seçeneğini belirleyin: 
        - Azure geçişi 'nin sunucularınız için en uygun hizmet katmanını önermesini istiyorsanız **önerilir** ' ı seçin. Bu genel amaçlı veya Iş açısından kritik olabilir. Daha Fazla Bilgi Edinin
        - Bütçe odaklı iş yükleri için tasarlanmış bir Azure SQL yapılandırması istiyorsanız **genel amaçlı** ' yi seçin.
        - Hatalara ve hızlı yük devretmeleri yüksek olan düşük gecikmeli iş yükleri için tasarlanmış bir Azure SQL yapılandırması istiyorsanız **iş açısından kritik** ' yi seçin.
    - **İndirim (%)** bölümünde, Azure teklifinin üzerine aldığınız aboneliğe özgü indirimleri ekleyin. Varsayılan ayar, %0’dır.
    - **Para birimi**' nde, hesabınız için faturalandırma para birimini seçin.
    - **Azure hibrit avantajı**' de, zaten bir SQL Server lisansına sahip olup olmadığını belirtin. Bunu yaptıysanız ve SQL Server aboneliklerinin etkin yazılım güvencesi kapsamında ele alındıklarında, lisansları Azure 'a getirdiğinizde Azure Hibrit Avantajı uygulayabilirsiniz.
    - Değişiklik yaparsanız Kaydet ' e tıklayın.
     :::image type="content" source="./media/tutorial-assess-sql/view-all.png" alt-text="Değerlendirme özelliklerine Kaydet düğmesi":::
8. **Sunucuları değerlendir** > ileri ' ye tıklayın.
9.  Değerlendirme **adını değerlendirmek için sunucuları seçin**  >   > değerlendirme için bir ad belirtin.
10. > **Grup Seç veya oluştur** bölümünde **Yeni oluştur** ' u seçin ve bir grup adı belirtin.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-add-servers.png" alt-text="Yeni Grup düğmesinin konumu":::
11. Gereç ' ı seçin ve gruba eklemek istediğiniz sunucuları seçin. Ardından İleri'ye tıklayın.
12. **İnceleme ve değerlendirme oluştur**' da, değerlendirme ayrıntılarını gözden geçirin ve grubu oluşturmak ve değerlendirmeyi çalıştırmak Için değerlendirme oluştur ' a tıklayın.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-create.png" alt-text="Inceleme ve değerlendirme oluşturma düğmesinin konumu.":::
13. Değerlendirme oluşturulduktan sonra **Windows, Linux ve SQL Server**  >  **Azure geçişi: bulma ve değerlendirme** Kutucuğu ' ne gidin > Azure SQL değerlendirmesi ' nin yanındaki sayıya tıklayın.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-navigation.png" alt-text="Oluşturulan değerlendirmeye gezinti":::
15. Görüntülemek istediğiniz değerlendirme adına tıklayın.

> [!NOTE]
> Azure SQL değerlendirmeleri performans tabanlı değerlendirmelerdir, değerlendirme oluşturmadan önce bulmayı başlattıktan sonra en az bir gün beklemeniz önerilir. Bu, daha yüksek güvenilirliğe sahip performans verilerinin toplanması için zaman sağlar. Bulma işlemi devam ediyorsa, SQL örneklerinizin hazır olma durumu **bilinmiyor** olarak işaretlenir. İdeal olarak, bulmayı başlattıktan sonra **belirttiğiniz performans süresini (gün/hafta/ay) bekleyin** ve daha yüksek güvenilirlikli bir derecelendirme değerlendirmesi oluşturun veya yeniden hesaplayın. 

## <a name="review-an-assessment"></a>Değerlendirmeyi gözden geçirme

**Bir değerlendirmeyi görüntülemek için**:

1. **Windows, Linux ve SQL Server**  >  **Azure geçişi: bulma ve değerlendirme** > Azure SQL değerlendirmesi ' nin yanındaki sayıya tıklayın.
2. Görüntülemek istediğiniz değerlendirme adına tıklayın. Örnek olarak (yalnızca Örneğin, tahminler ve maliyetler): :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-summary.png" alt-text="SQL değerlendirmesine genel bakış":::
3. Değerlendirme özetini gözden geçirin. Ayrıca değerlendirme özelliklerini düzenleyebilir veya değerlendirmeyi yeniden hesaplayabilirsiniz.

#### <a name="discovered-items"></a>Bulunan öğeler

Bu, bu değerlendirmede değerlendirilen SQL Server, örnek ve veritabanlarının sayısını belirtir.
    
#### <a name="azure-readiness"></a>Azure için hazır olma

Bu, değerlendirilen SQL örneklerinin dağılımını gösterir: 
    
**Hedef dağıtım türü (değerlendirme özelliklerinde)** | **Hazırlığı**   
--- | --- |
**Önerilen** |  Azure SQL veritabanı için hazır, Azure SQL yönetilen örneği için hazır, Azure VM için hazır, hazırlık bilinmiyor (bulma devam ediyor veya düzeltilmesi olası bazı bulma sorunları durumunda)
**Azure SQL veritabanı** veya **Azure SQL mı** | Azure SQL veritabanı veya Azure SQL yönetilen örneği için hazır, Azure SQL veritabanı veya Azure SQL yönetilen örneği için hazır değil, hazırlık bilinmiyor (bulma işlemi devam ederken veya düzeltilmesi için bazı bulma sorunları durumunda)
     
Azure SQL 'e geçişten önce düzeltebileceğiniz geçiş sorunları/uyarıları etrafında ayrıntıları anlamak için ayrıntıya gidebilirsiniz. [Daha fazla bilgi](concepts-azure-sql-assessment-calculation.md) Azure SQL veritabanlarına ve/veya yönetilen örneklere geçiş için önerilen Azure SQL yapılandırması ' nı da gözden geçirebilirsiniz.
    
#### <a name="azure-sql-database-and-managed-instance-cost-details"></a>Azure SQL veritabanı ve yönetilen örnek maliyet ayrıntıları

Aylık maliyet tahmini, önerilen Azure SQL veritabanı ve/veya Azure SQL yönetilen örnek dağıtım türüne karşılık gelen Azure SQL yapılandırmalarının işlem ve depolama maliyetlerini içerir. [Daha Fazla Bilgi](concepts-azure-sql-assessment-calculation.md#calculate-monthly-costs)


### <a name="review-readiness"></a>İnceleme hazırlığı

1. **Azure SQL hazırlığı**' ne tıklayın.
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-readiness.png" alt-text="Azure SQL hazırlığı ayrıntıları":::
1. Azure SQL hazırlığı ' nde, değerlendirilen SQL örnekleri için **Azure SQL DB hazırlığı** ve **Azure SQL mı hazırlığı** ' ni gözden geçirin:
    - **Hazırlanıyor**: örnek, hiçbir geçiş sorunu veya uyarısı olmadan Azure SQL DB/mı 'a geçirilmeye hazırlanıyor. 
        - Ready (köprü uygulanmış ve mavi bilgi simgesi): örnek, hiçbir geçiş sorunu olmadan Azure SQL DB/mı 'a geçirilmeye, ancak gözden geçirmeniz gereken bazı geçiş uyarılarına sahip olacak. Geçiş uyarılarını ve önerilen düzeltme kılavuzunu gözden geçirmek için köprüye tıklayabilirsiniz. :::image type="content" source="./media/tutorial-assess-sql/assess-ready.png" alt-text="hazırlık durumu Ile değerlendirme":::       
    - **Ready**: örnek Azure SQL DB/mı 'ye geçiş için bir veya daha fazla geçiş sorununa sahip. Köprüye tıklayıp geçiş sorunlarını ve önerilen düzeltme kılavuzunu gözden geçirebilirsiniz.
    - **Bilinmiyor**: bulma devam ettiğinden veya bulma sırasında bildirimler dikey penceresinden düzeltilmesi gereken sorunlar olduğundan, Azure geçişi hazırlık işlemini değerlendiremez. Sorun devam ederse, lütfen Microsoft Desteği'ne başvurun. 
1. Aşağıdaki matriste göre belirlenen SQL örneği için önerilen dağıtım türünü gözden geçirin:

    - **Hedef dağıtım türü** (değerlendirme özelliklerinde seçildiği gibi): **önerilir**

        **Azure SQL DB hazırlığı** | **Azure SQL MI hazırlığı** | **Önerilen dağıtım türü** | **Azure SQL yapılandırması ve maliyet tahminleri hesaplandı mı?**
         --- | --- | --- | --- |
        Hazır | Hazır | Azure SQL VERITABANı veya Azure SQL MI [daha fazla bilgi](concepts-azure-sql-assessment-calculation.md#recommended-deployment-type) | Yes
        Hazır | Ready veya Unknown | Azure SQL DB | Yes
        Ready veya Unknown | Hazır | Azure SQL MI | Yes
        Hazırlanma | Hazırlanma | Azure VM için hazırım [daha fazla bilgi](concepts-azure-sql-assessment-calculation.md#potentially-ready-for-azure-vm) | No
        Ready veya Unknown | Ready veya Unknown | Bilinmiyor | No
    
    - **Hedef dağıtım türü** (değerlendirme özelliklerinde seçildiği gibi): **Azure SQL DB**
    
        **Azure SQL DB hazırlığı** | **Azure SQL yapılandırması ve maliyet tahminleri hesaplandı mı?**
        --- | --- |
        Hazır | Yes
        Hazırlanma | Hayır
        Bilinmiyor | No
    
    - **Hedef dağıtım türü** (değerlendirme özelliklerinde seçildiği gibi): **Azure SQL mı**
    
        **Azure SQL MI hazırlığı** | **Azure SQL yapılandırması ve maliyet tahminleri hesaplandı mı?**
         --- | --- |
        Hazır | Yes
        Hazırlanma | Hayır
        Bilinmiyor | No

4. Kullanıcı veritabanlarının sayısını görmek için detaya git ' e tıklayın, örnek özellikleri, bilgi işlem (kapsamlı örnek) ve kaynak veritabanı depolama ayrıntıları dahil örnek ayrıntıları.
5. Veritabanlarının listesini ve bunların ayrıntılarını gözden geçirmek için kullanıcı veritabanlarının sayısına tıklayın. Örnek olarak (yalnızca Örneğin, tahminler ve maliyetler): :::image type="content" source="./media/tutorial-assess-sql/assessment-db.png" alt-text="SQL örneği ayrıntısı":::
5. Belirli bir hedef dağıtım türü için geçiş sorunlarını ve uyarılarını gözden geçirmek üzere geçiş sorunları sütunundaki ayrıntıları gözden geçir ' e tıklayın. 
    :::image type="content" source="./media/tutorial-assess-sql/assessment-db-issues.png" alt-text="DB geçiş sorunları ve uyarıları":::

### <a name="review-cost-estimates"></a>Tahmini maliyetleri gözden geçirme
Değerlendirme özeti, önerilen Azure SQL veritabanlarına ve/veya yönetilen örnek dağıtım türüne karşılık gelen Azure SQL yapılandırmalarının tahmini aylık işlem ve depolama maliyetlerini gösterir.

1. Aylık toplam maliyetleri gözden geçirin. Ücretler, değerlendirilen gruptaki tüm SQL örnekleri için toplanır.
    - Maliyet tahminleri, bir örnek için önerilen Azure SQL yapılandırmasına dayanır. 
    - İşlem ve depolama için aylık tahmini maliyetler gösterilir. Örnek olarak (yalnızca Örneğin, tahminler ve maliyetler):
    
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-cost.png" alt-text="Maliyet ayrıntıları":::

1. Örnek düzeyinde Azure SQL yapılandırması ve maliyet tahminleri görmek için bir örnek düzeyinde ayrıntıya gidebilirsiniz.  
1. Ayrıca, bir Azure SQL veritabanı yapılandırması önerildiği zaman veritabanı başına Azure SQL yapılandırmasını ve maliyet tahminlerini gözden geçirmek için veritabanı listesini ayrıntıya gidebilirsiniz.

### <a name="review-confidence-rating"></a>Güvenilirlik derecelendirmesini gözden geçirme
Azure geçişi, tüm Azure SQL değerlendirmelerine, değerlendirilen tüm SQL örnekleri ve veritabanlarının değerlendirmesini hesaplamak için gereken performans/kullanım veri noktalarının kullanılabilirliğine bağlı olarak bir güvenilirlik derecelendirmesi atar. Derecelendirme bir yıldız (en düşük) ile beş yıldız (en yüksek) arasında.
Güvenilirlik derecelendirmesi, değerlendirmede boyut önerilerinin güvenilirliğini tahmin etmenize yardımcı olur. Güven derecelendirmeleri aşağıdaki gibidir:

**Veri noktası kullanılabilirliği** | **Güvenilirlik derecelendirmesi**
--- | ---
%0-%20 | 1 yıldız
%21-%40 | 2 yıldız
%41-%60 | 3 yıldız
%61-%80 | 4 yıldız
%81-%100 | 5 yıldız

Güvenilirlikli derecelendirmeler hakkında [daha fazla bilgi edinin](concepts-azure-sql-assessment-calculation.md#confidence-ratings) .


## <a name="next-steps"></a>Sonraki adımlar

- Azure SQL değerlendirmelerinin nasıl hesaplandığı hakkında [daha fazla bilgi edinin](concepts-azure-sql-assessment-calculation.md) .
- [Azure veritabanı geçiş hizmeti](../dms/dms-overview.md)'NI kullanarak SQL örnekleri ve veritabanlarını geçirmeye başlayın.