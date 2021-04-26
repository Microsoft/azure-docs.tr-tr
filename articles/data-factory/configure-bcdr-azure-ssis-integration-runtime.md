---
title: İş sürekliliği ve olağanüstü durum kurtarma (BCDR) için Azure-SSIS tümleştirme çalışma zamanını yapılandırma
description: Bu makalede, iş sürekliliği ve olağanüstü durum kurtarma (BCDR) için Azure SQL veritabanı/yönetilen örnek yük devretme grubu ile Azure Data Factory Azure-SSIS tümleştirme çalışma zamanının nasıl yapılandırılacağı açıklanır.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/05/2021
ms.openlocfilehash: a426ee39ba3c0f50b9a6c1fb9c7de1ef8e7291b2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566362"
---
# <a name="configure-azure-ssis-integration-runtime-for-business-continuity-and-disaster-recovery-bcdr"></a>İş sürekliliği ve olağanüstü durum kurtarma (BCDR) için Azure-SSIS tümleştirme çalışma zamanını yapılandırma 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory (ADF) içindeki Azure SQL veritabanı/yönetilen örnek ve SQL Server Integration Services (SSIS), SQL Server geçiş için önerilen tüm hizmet olarak platform (PaaS) çözümü olarak birleştirilebilir. SSIS projelerinizi Azure SQL veritabanı/yönetilen örnek tarafından barındırılan SSIS Katalog veritabanına (SSSıSDB) dağıtabilir ve ADF 'de Azure SSIS Integration Runtime (IR) üzerinde SSIS paketlerinizi çalıştırabilirsiniz.

İş sürekliliği ve olağanüstü durum kurtarma (BCDR) için Azure SQL veritabanı/yönetilen örnek, [coğrafi çoğaltma/yük devretme grubuyla](../azure-sql/database/auto-failover-group-overview.md)yapılandırılabilir. burada, okuma/yazma erişimi olan birincil bir Azure bölgesindeki SSISDB, salt okuma erişimi (ikincil rol) ile bir ikincil bölgeye sürekli olarak çoğaltılır. Birincil bölgede bir olağanüstü durum oluştuğunda, birincil ve ikincil Ssisdb rolleri değiştirdikleri için bir yük devretme tetiklenecektir.

BCDR için, Azure SQL veritabanı/yönetilen örnek yük devretme grubuyla eşitlenmiş bir Azure SSIS IR çifti de yapılandırabilirsiniz. Bu, belirli bir zamanda Azure-SSIS IRS çalıştıran bir çiftiyle, paket yürütme günlükleri (birincil rol) yazmak ve yürütmek için birincil SSSıSDB 'ye erişebilir, diğeri ise yalnızca başka bir yerde dağıtılan paketler için (ikincil rol) aynı anda uygulanabilir. SSıSDB yük devretmesi gerçekleştiğinde, birincil ve ikincil Azure-SSIS IRS de rolleri takas eder ve her ikisi de çalışıyorsa neredeyse sıfır kapalı kalma süresi olacaktır.

Bu makalede, BCDR için Azure SQL veritabanı/yönetilen örnek yük devretme grubu ile Azure-SSIS IR nasıl yapılandırılacağı açıklanır.

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-database-failover-group"></a>Azure SQL veritabanı yük devretme grubu ile çift bekleme Azure-SSIS IR çifti yapılandırma

Azure SQL veritabanı yük devretme grubuyla eşitlenmiş bir çift bekleme Azure-SSIS IR çifti yapılandırmak için aşağıdaki adımları izleyin.

1. Azure portal/ADF Kullanıcı arabirimini kullanarak, birincil bölgede SSıSDB barındırmak için birincil Azure SQL veritabanı sunucunuz ile yeni bir Azure-SSIS IR oluşturabilirsiniz. Birincil Azure SQL veritabanı sunucunuz tarafından barındırılan SSıDB 'ye zaten bağlı olan bir Azure-SSIS IR varsa ve hala çalışıyorsa, yeniden yapılandırmak için önce durdurmanız gerekir. Bu, birincil Azure-SSIS IR olacaktır.

   **Tümleştirme çalışma zamanı kurulum** bölmesinin **dağıtım ayarları** sayfasında [SSISDB kullanmayı seçerken](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) , **sssısdb yük devretmesi ile çift bekleme Azure-SSIS Integration Runtime çiftini kullan** onay kutusunu da seçin. **Çift bekleme çifti adı** için, birincil ve ikincil Azure-SSIS IRS çiftini tanımlayacak bir ad girin. Birincil Azure-SSIS IR oluşturma işlemi tamamlandığında, başlatılır ve okuma/yazma erişimiyle sizin adınıza oluşturulacak birincil bir SSıSDB 'ye eklenir. Yalnızca yeniden yapılandırdıysanız, yeniden başlatmanız gerekir.

1. Azure portal kullanarak, birincil SSSıSDB 'nin birincil Azure SQL veritabanı sunucunuzun **genel bakış** sayfasında oluşturulup oluşturulmayacağını kontrol edebilirsiniz. Oluşturulduktan sonra, [birincil ve Ikincil Azure SQL veritabanı sunucularınız için bir yük devretme grubu oluşturabilir ve bu gruba](../azure-sql/database/failover-group-add-single-database-tutorial.md?tabs=azure-portal#2---create-the-failover-group) , **Yük devretme grupları** sayfasında SSISDB ekleyebilirsiniz. Yük devretme grubunuz oluşturulduktan sonra, birincil SSSıSDB 'nin ikincil Azure SQL veritabanı sunucunuzun **genel bakış** sayfasında salt okuma erişimiyle ikincil bir sunucuya çoğaltılıp çoğaltılmadığını kontrol edebilirsiniz.

1. Azure portal/ADF Kullanıcı arabirimini kullanarak, ikincil bölgede SSıSDB barındırmak için ikincil Azure SQL veritabanı sunucunuz ile başka bir Azure-SSIS IR oluşturabilirsiniz. Bu, ikincil Azure-SSIS IR olacaktır. Tüm BCDR için, bağımlı olduğu tüm kaynakların ikincil bölgede da oluşturulduğundan emin olun. Örneğin, özel kurulum betiği/dosyaları depolamak için Azure depolama, düzenleme/zamanlama paketi yürütmeleri için ADF, vb.

   **Tümleştirme çalışma zamanı kurulum** bölmesinin **dağıtım ayarları** sayfasında [SSISDB kullanmayı seçerken](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) , **sssısdb yük devretmesi ile çift bekleme Azure-SSIS Integration Runtime çiftini kullan** onay kutusunu da seçin. **Çift bekleme çifti adı** için, birincil ve ikincil Azure-SSIS IRS çiftini tanımlamak üzere aynı adı girin. İkincil Azure-SSIS IR oluşturma işlemi tamamlandığında, başlatılır ve ikincil SSSıSDB 'ye eklenir.

1. SSıSDB yük devretmesi gerçekleştiğinde neredeyse sıfır kapalı kalma süresine sahip olmak istiyorsanız, her ikisini de Azure-SSIS IRS 'nin çalışır durumda tutun. Yalnızca birincil Azure-SSIS IR, paket yürütme günlükleri yazmak ve yürütmek için birincil SSSıSDB 'ye erişebilir, ancak ikincil Azure-SSIS IR, örneğin Azure dosyalarında yalnızca başka bir yerde dağıtılan paketler için de aynı işlemi yapabilir.

   Çalışan maliyetinizi en aza indirmek isterseniz, ikincil Azure-SSIS IR oluşturulduktan sonra durdurabilirsiniz. SSıSDB yük devretmesi gerçekleştiğinde, birincil ve ikincil Azure-SSIS IRS, rolleri takas eder. Birincil Azure-SSIS IR durdurulmuşsa, yeniden başlatmanız gerekir. Bir sanal ağa ve ekleme yöntemine eklenmiş olmasına bağlı olarak, çalışması için 5 dakika veya yaklaşık 20-30 dakika içinde sürer.

1. [Düzenleme/zamanlama paket yürütmesi IÇIN ADF kullanıyorsanız](./how-to-invoke-ssis-package-ssis-activity.md), WSIS paketi etkinliklerini ve ilişkili TETIKLEYICILERLE ılgılı ADF işlem hatlarının tümünün, başlangıçta devre dışı bırakılan tetikleyicilerle ikincil ADF 'nize kopyalandığından emin olun. SSıSDB yük devretmesi gerçekleştiğinde, bunları etkinleştirmeniz gerekir.

1. Birincil ve ikincil Azure-SSIS IRS 'nın rolleri takas etmeksizin [Azure SQL veritabanı yük devretme grubunuzu test](../azure-sql/database/failover-group-add-single-database-tutorial.md?tabs=azure-portal#3---test-failover) edebılır ve [ADF portalındaki Azure-SSIS IR izleme sayfasını](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) kontrol edebilirsiniz. 

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-managed-instance-failover-group"></a>Azure SQL yönetilen örnek yük devretme grubu ile çift bekleme Azure-SSIS IR çifti yapılandırma

Azure SQL yönetilen örnek yük devretme grubuyla eşitlenmiş bir çift bekleme Azure-SSIS IR çifti yapılandırmak için aşağıdaki adımları izleyin.

1. Azure portal kullanarak birincil [ve Ikincil Azure SQL yönetilen örneklerinizin](../azure-sql/managed-instance/failover-group-add-instance-tutorial.md?tabs=azure-portal) BIRINCIL Azure SQL yönetilen örneğinizin **Yük devretme grupları** sayfasında bir yük devretme grubu oluşturabilirsiniz.

1. Azure portal/ADF Kullanıcı arabirimini kullanarak, birincil bölgede SSıSDB barındırmak üzere birincil Azure SQL yönetilen örneğiniz ile yeni bir Azure-SSIS IR oluşturabilirsiniz. Birincil Azure SQL yönetilen örneğiniz tarafından barındırılan SSıDB 'ye zaten çalışmakta olan bir Azure-SSIS IR varsa ve hala çalışıyorsa, yeniden yapılandırmak için önce durdurmanız gerekir. Bu, birincil Azure-SSIS IR olacaktır.

   **Tümleştirme çalışma zamanı kurulum** bölmesinin **dağıtım ayarları** sayfasında [SSISDB kullanmayı seçerken](./create-azure-ssis-integration-runtime.md#creating-ssisdb) , **sssısdb yük devretmesi ile çift bekleme Azure-SSIS Integration Runtime çiftini kullan** onay kutusunu da seçin. **Çift bekleme çifti adı** için, birincil ve ikincil Azure-SSIS IRS çiftini tanımlayacak bir ad girin. Birincil Azure-SSIS IR oluşturma işlemi tamamlandığında, başlatılır ve okuma/yazma erişimiyle sizin adınıza oluşturulacak birincil bir SSıSDB 'ye eklenir. Yalnızca yeniden yapılandırdıysanız, yeniden başlatmanız gerekir. Birincil SSSıSDB 'nin ikincil Azure SQL yönetilen örneğinizin **genel bakış** sayfasında salt okuma erişimi olan ikincil bir sunucuyla çoğaltılıp çoğaltılmadığını da denetleyebilirsiniz.

1. Azure portal/ADF Kullanıcı arabirimini kullanarak, ikincil bölgede SSıSDB barındırmak için ikincil Azure SQL yönetilen örneğiniz ile başka bir Azure-SSIS IR oluşturabilirsiniz. Bu, ikincil Azure-SSIS IR olacaktır. Tüm BCDR için, bağımlı olduğu tüm kaynakların ikincil bölgede da oluşturulduğundan emin olun. Örneğin, özel kurulum betiği/dosyaları depolamak için Azure depolama, düzenleme/zamanlama paketi yürütmeleri için ADF, vb.

   **Tümleştirme çalışma zamanı kurulum** bölmesinin **dağıtım ayarları** sayfasında [SSISDB kullanmayı seçerken](./create-azure-ssis-integration-runtime.md#creating-ssisdb) , **sssısdb yük devretmesi ile çift bekleme Azure-SSIS Integration Runtime çiftini kullan** onay kutusunu da seçin. **Çift bekleme çifti adı** için, birincil ve ikincil Azure-SSIS IRS çiftini tanımlamak üzere aynı adı girin. İkincil Azure-SSIS IR oluşturma işlemi tamamlandığında, başlatılır ve ikincil SSSıSDB 'ye eklenir.

1. Azure SQL yönetilen örneği, veritabanı ana anahtarını (DMK) kullanarak şifreleyerek SSSıSDB gibi veritabanlarındaki hassas verileri güvenli hale getirebilirsiniz. DMK, varsayılan olarak hizmet ana anahtarı (SMK) kullanılarak şifrelenir. Yazma sırasında, Azure SQL yönetilen örnek yük devretme grubu, SMK 'yi birincil Azure SQL yönetilen örneğinden çoğaltmaz, bu nedenle DMK ve içindeki SSSıSDB, yük devretme gerçekleştikten sonra ikincil Azure SQL yönetilen örneği üzerinde çözülemez. Bu sorunu geçici olarak çözmek için, DMK için ikincil Azure SQL yönetilen örneğinde Şifresi çözülecek bir parola şifrelemesi ekleyebilirsiniz. SSMS 'yi kullanarak aşağıdaki adımları izleyin.

   1. DMK şifrelemek için bir parola eklemek üzere birincil Azure SQL yönetilen Örneğinizde SSSıSDB için aşağıdaki komutu çalıştırın.

      ```sql
      ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'YourPassword'
      ```
   
   1. DMK şifresini çözmek için yeni parolayı eklemek üzere hem birincil hem de ikincil Azure SQL yönetilen örneklerinizin SSSıSDB için aşağıdaki komutu çalıştırın.

      ```sql
      EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB', @password = N'YourPassword', @action = N'add'
      ```

1. SSıSDB yük devretmesi gerçekleştiğinde neredeyse sıfır kapalı kalma süresine sahip olmak istiyorsanız, her ikisini de Azure-SSIS IRS 'nin çalışır durumda tutun. Yalnızca birincil Azure-SSIS IR, paket yürütme günlükleri yazmak ve yürütmek için birincil SSSıSDB 'ye erişebilir, ancak ikincil Azure-SSIS IR, örneğin Azure dosyalarında yalnızca başka bir yerde dağıtılan paketler için de aynı işlemi yapabilir.

   Çalışan maliyetinizi en aza indirmek isterseniz, ikincil Azure-SSIS IR oluşturulduktan sonra durdurabilirsiniz. SSıSDB yük devretmesi gerçekleştiğinde, birincil ve ikincil Azure-SSIS IRS, rolleri takas eder. Birincil Azure-SSIS IR durdurulmuşsa, yeniden başlatmanız gerekir. Bir sanal ağa ve ekleme yöntemine eklenmiş olmasına bağlı olarak, çalışması için 5 dakika veya yaklaşık 20-30 dakika içinde sürer.

1. [Düzenleme/zamanlama paket yürütmeleri Için Azure SQL yönetilen örnek Aracısı kullanırsanız](./how-to-invoke-ssis-package-managed-instance-agent.md), iş adımlarıyla ılgılı tüm SSIS işlerinin ve ilişkili zamanlamaların, başlangıçta devre dışı bırakılan zamanlamalarla IKINCIL Azure SQL yönetilen örneğine kopyalandığından emin olun. SSMS 'yi kullanarak aşağıdaki adımları izleyin.

   1. Her bir SSIS işi için, komut dosyasını oluşturmak üzere **komut dosyası işini**, **Oluştur**' a ve **Yeni sorgu Düzenleyicisi penceresi** açılır menü öğelerini seçin.

      ![SSIS iş betiği oluştur](media/configure-bcdr-azure-ssis-integration-runtime/generate-ssis-job-script.png)

   1. Oluşturulan her bir SSIS iş betiği için, saklı yordamı yürütme komutunu bulun `sp_add_job` ve değer atamasını `@owner_login_name` gerektiği gibi bağımsız değişkene değiştirin/kaldırın.

   1. Her güncelleştirilmiş SSIS iş betiği için, işi iş adımları ve ilişkili zamanlamalarla kopyalamak üzere ikincil Azure SQL yönetilen Örneğinizde çalıştırın.

   1. Aşağıdaki betiği kullanarak, birincil/ikincil SSıSDB rolüne bağlı olarak SSIS iş zamanlamalarını etkinleştirmek/devre dışı bırakmak için yeni bir T-SQL işi oluşturun ve hem birincil hem de ikincil Azure SQL yönetilen örneklerinde düzenli olarak çalıştırın. SSıSDB yük devretmesi gerçekleştiğinde, devre dışı bırakılmış SSIS iş zamanlamaları etkinleştirilir ve tam tersi de geçerlidir.

      ```sql
      IF (SELECT Top 1 role_desc FROM SSISDB.sys.dm_geo_replication_link_status WHERE partner_database = 'SSISDB') = 'PRIMARY'
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 0
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 1
         END
      ELSE
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 1
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 0
         END
      ```

1. [Düzenleme/zamanlama paket yürütmesi IÇIN ADF kullanıyorsanız](./how-to-invoke-ssis-package-ssis-activity.md), WSIS paketi etkinliklerini ve ilişkili TETIKLEYICILERLE ılgılı ADF işlem hatlarının tümünün, başlangıçta devre dışı bırakılan tetikleyicilerle ikincil ADF 'nize kopyalandığından emin olun. SSıSDB yük devretmesi gerçekleştiğinde, bunları etkinleştirmeniz gerekir.

1. [Azure SQL yönetilen örnek yük devretme grubunuzu test](../azure-sql/managed-instance/failover-group-add-instance-tutorial.md?tabs=azure-portal#test-failover) edebilir ve birincil ve ikincil Azure-SSIS IRS 'nin rolleri takas ETMEKSIZIN [ADF portalındaki Azure-SSIS IR izleme sayfasını](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) gözden geçirin. 

## <a name="attach-a-new-azure-ssis-ir-to-existing-ssisdb-hosted-by-azure-sql-databasemanaged-instance"></a>Azure SQL veritabanı/yönetilen örnek tarafından barındırılan var olan SSSıSDB 'ye yeni Azure-SSIS IR iliştirme

Bir olağanüstü durum oluşursa ve mevcut Azure-SSIS IR, ancak aynı bölgedeki Azure SQL veritabanı/yönetilen örneğini etkileiyorsa, bunu başka bir bölgedeki yeni bir bölgede değiştirebilirsiniz. Azure SQL veritabanı/yönetilen örneği tarafından barındırılan mevcut SSSıSDB 'nizi yeni bir Azure-SSIS IR eklemek için aşağıdaki adımları izleyin.

1. Mevcut Azure-SSIS IR hala çalışıyorsa, önce Azure portal/ADF Kullanıcı arabirimi veya Azure PowerShell kullanarak durdurmanız gerekir. Olağanüstü durum ADF 'yi aynı bölgede da etkirse, bu adımı atlayabilirsiniz.

1. SSMS 'yi kullanarak, yeni ADF/Azure-SSIS IR bağlantılarına izin verecek meta verileri güncelleştirmek için Azure SQL veritabanı/yönetilen örnekte SSSıSDB için aşağıdaki komutu çalıştırın.

   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name = 'YourNewADF', @integration_runtime_name = 'YourNewAzureSSISIR'
   ```

1. [Azure Portal/ADF Kullanıcı arabirimi](./create-azure-ssis-integration-runtime.md#use-the-azure-portal-to-create-an-integration-runtime) veya [Azure PowerShell](./create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime)kullanarak, başka bir bölgede sırasıyla, yeni ADF/Azure-SSIS IR *yournewadf* / *yournewazuressisar* adlı yeni ADF 'yi oluşturun. Azure portal/ADF Kullanıcı arabirimini kullanıyorsanız **Integration Runtime kurulum** bölmesinin **dağıtım ayarları** sayfasında bağlantı hatası ' nı yoksayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Azure-SSIS IR için bu diğer yapılandırma seçeneklerini göz önünde bulundurun:

- [Azure-SSIS IR için paket depolarını yapılandırma](./create-azure-ssis-integration-runtime.md#creating-azure-ssis-ir-package-stores)

- [Azure-SSIS IR için özel kurulumları yapılandırma](./how-to-configure-azure-ssis-ir-custom-setup.md)

- [Azure-SSIS IR için sanal ağ ekleme 'yi yapılandırma](./join-azure-ssis-integration-runtime-virtual-network.md)

- [Azure-SSIS IR için şirket içinde barındırılan IR 'yi bir ara sunucu olarak yapılandırma](./self-hosted-integration-runtime-proxy-ssis.md)