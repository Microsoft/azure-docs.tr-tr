---
title: SSIS paketlerini SQL tek veritabanına yeniden dağıtma
titleSuffix: Azure Database Migration Service
description: Azure veritabanı geçiş hizmeti ve Data Migration Yardımcısı kullanarak SQL Server Integration Services paketleri ve projeleri Azure SQL veritabanı tek veritabanına geçirme veya yeniden dağıtma hakkında bilgi edinin.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: e42b5ab2bbdebb2584b38531bd6458a98563ef42
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643741"
---
# <a name="redeploy-ssis-packages-to-azure-sql-database-with-azure-database-migration-service"></a>SSIS paketlerini Azure veritabanı geçiş hizmeti ile Azure SQL veritabanı 'na yeniden dağıtma

SQL Server Integration Services (SSIS) kullanıyorsanız ve SSIS projelerinizi/paketlerinizi SQL Server tarafından barındırılan kaynak SSSıSDB 'den Azure SQL veritabanı tarafından barındırılan hedef SSıSDB 'ye geçirmek istiyorsanız, Tümleştirme Hizmetleri dağıtım Sihirbazı 'nı kullanarak bunları yeniden dağıtabilirsiniz. Sihirbazı SQL Server Management Studio (SSMS) içinden başlatabilirsiniz.

SSIS 'nin kullandığınız sürümü 2012 'den daha eski ise, SSIS projelerinizi/paketlerinizi proje dağıtım modeline yeniden dağıtmadan önce, SSIS 'den de başlatılabilen Tümleştirme Hizmetleri proje Dönüştürme Sihirbazı 'Nı kullanarak bunları dönüştürmeniz gerekir. Daha fazla bilgi için [projeleri proje dağıtım modeline dönüştürme](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#convert)makalesine bakın.

> [!NOTE]
> Azure veritabanı geçiş hizmeti (DMS) Şu anda bir kaynak SSSıSDB 'nin Azure SQL veritabanı 'na geçişini desteklemez, ancak aşağıdaki işlemi kullanarak SSIS projelerinizi/paketlerinizi yeniden dağıtabilirsiniz.

Bu makalede şunları öğreneceksiniz:
> [!div class="checklist"]
>
> * Kaynak SSIS projelerini/paketlerini değerlendirin.
> * SSIS projelerini/paketlerini Azure 'a geçirin.

## <a name="prerequisites"></a>Önkoşullar

Bu adımları tamamlayabilmeniz için şunlar gerekir:

* SSMS sürüm 17,2 veya üzeri.
* SSıSDB barındırmak için hedef veritabanı sunucunuzun bir örneği. Henüz yoksa, SQL Server (yalnızca mantıksal sunucu) [formuna](https://ms.portal.azure.com/#create/Microsoft.SQLServer)giderek Azure Portal kullanarak BIR [mantıksal SQL Server](../azure-sql/database/logical-servers.md) (veritabanı olmadan) oluşturun.
* SSIS, SQL veritabanı tarafından barındırılan hedef SSSıSDB ile Azure-SSIS Integration Runtime (IR) içeren Azure Data Factory (ADF) ( [Azure Data Factory 'de Azure-SSIS Integration Runtime sağlama](../data-factory/tutorial-deploy-ssis-packages-azure.md)makalesinde açıklandığı gibi) sağlanmalıdır.

## <a name="assess-source-ssis-projectspackages"></a>Kaynak SSIS projelerini/paketlerini değerlendir

Kaynak SSSıSDB değerlendirmesi, veritabanı Geçiş Yardımcısı (DMA) veya Azure veritabanı geçiş hizmeti (DMS) ile henüz tümleştirilmediğinde, SSIS projeleriniz/paketleriniz Azure SQL veritabanı tarafından barındırılan hedef SSıSDB 'ye yeniden dağıtıldıklarında değerlendirilecek/doğrulanacak.

## <a name="migrate-ssis-projectspackages"></a>SSIS projelerini/paketlerini geçirme

SSIS projelerini/paketlerini Azure SQL veritabanı 'na geçirmek için aşağıdaki adımları uygulayın.

1. SSMS 'yi açın ve sonra **sunucuya Bağlan** iletişim kutusunu göstermek için **Seçenekler** ' i seçin.

2. **Oturum açma** sekmesinde, hedef sssısdb barındıracak sunucuya bağlanmak için gereken bilgileri belirtin.

    ![SSIS oturum açma sekmesi](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. **Bağlantı özellikleri** sekmesindeki **veritabanına Bağlan** metin kutusunda **sssısdb**' ı seçin veya girin ve sonra **Bağlan**' ı seçin.

    ![SSIS Bağlantı özellikleri sekmesi](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. SSMS Nesne Gezgini **Tümleştirme Hizmetleri katalogları** düğümünü genişletin, **sssısdb**' yi genişletin ve var olan klasörler yoksa, **SSISDB** ' ye sağ tıklayıp yeni bir klasör oluşturun.

5. **Sssısdb** altında herhangi bir klasörü genişletin, **Projeler**' e sağ tıklayın ve ardından **projeyi dağıt**' ı seçin.

    ![SSIS SSıSDB düğümü genişletildi](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. Tümleştirme Hizmetleri dağıtım sihirbazında, **giriş** sayfasında, bilgileri gözden geçirin ve ardından **İleri**' yi seçin.

    ![Dağıtım sihirbazına giriş sayfası](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. **Kaynak Seç** sayfasında, dağıtmak istediğiniz var olan SSIS projesini belirtin.

    SSMS, kaynak SSSıSDB 'yi barındıran SQL Server de bağlandıysa, **Tümleştirme Hizmetleri Kataloğu**' nu seçin ve ardından projenize sunucu adını ve proje yolunu girerek projenizi doğrudan dağıtın.

    Alternatif olarak, **Proje dağıtım dosyası**' nı seçin ve ardından projenizi dağıtmak için mevcut bir proje dağıtım dosyasının (. ıspac) yolunu belirtin.

    ![Dağıtım Sihirbazı kaynak sayfasını seçin](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. **İleri**’yi seçin.
9. **Hedef Seç** sayfasında, projenizin hedefini belirtin.

    a. Sunucu adı metin kutusuna tam sunucu adını (<server_name>. database.windows.net) girin.

    b. Kimlik doğrulama bilgilerini sağlayın ve sonra **Bağlan**' ı seçin.

    ![Dağıtım Sihirbazı hedef sayfayı seçin](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. SSSıSDB 'de hedef klasörü belirtmek için, **Git**' i seçin ve ardından İleri ' **yi seçin.**

    > [!NOTE]
    > **İleri** düğmesi yalnızca **Bağlan**'ı seçtikten sonra etkinleştirilir.

10. **Doğrula** sayfasında, hataları/uyarıları görüntüleyin ve gerekirse paketlerinizi uygun şekilde değiştirin.

    ![Dağıtım Sihirbazı sayfayı doğrula](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. **İleri**’yi seçin.

12. **İnceleme** sayfasında, dağıtım ayarlarınızı gözden geçirin.

    > [!NOTE]
    > Ayarlarınızı, **önceki** ' i seçerek veya sol bölmedeki adım bağlantılarından birini seçerek değiştirebilirsiniz.

13. Dağıtım işlemini başlatmak için **Dağıt** ' ı seçin.

14. Dağıtım işlemi tamamlandıktan sonra, her dağıtım eyleminin başarısını veya başarısızlığını gösteren sonuçlar sayfasını görüntüleyebilirsiniz.
    a. Herhangi bir eylem başarısız olursa, **sonuç** sütununda hatanın açıklamasını göstermek için **başarısız** ' ı seçin.
    b. İsteğe bağlı olarak, sonuçları bir XML dosyasına kaydetmek için **raporu kaydet** ' i seçin.

15. Tümleştirme Hizmetleri dağıtım sihirbazından çıkmak için **Kapat** ' ı seçin.

Projenizin dağıtımı hata olmadan başarılı olursa, içerdiği tüm paketleri Azure-SSIS IR çalıştırmak için seçebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* Microsoft [veritabanı geçiş kılavuzu](https://datamigration.microsoft.com/)' nda geçiş kılavuzunu gözden geçirin.