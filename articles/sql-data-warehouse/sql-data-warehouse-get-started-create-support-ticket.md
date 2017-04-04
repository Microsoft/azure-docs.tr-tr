---
title: "SQL Veri Ambarı için destek bileti oluşturma | Microsoft Belgeleri"
description: "Azure SQL Data Warehouse&quot;da destek bileti oluşturma"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: a91d1f53-03cb-464b-9d5b-4a9c1a194ed3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 889776269aa3d722115ea0632b7c3db2775924eb
ms.openlocfilehash: cc3bc8a0463842ba11b9cdcc996319e2d811ce8a
ms.lasthandoff: 01/18/2017


---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>SQL Data Warehouse için destek bileti oluşturma
SQL Veri Ambarı’nız ile ilgili herhangi bir sorun yaşıyorsanız lütfen mühendislik ekibimizin size yardımcı olabilmesi için bir destek bileti oluşturun.

> [!NOTE] 
> 20.12.2016 itibarıyla Azure portalındaki kaynak durumu denetimi doğru değildir. Bu sorunu düzeltmek için etkin bir şekilde çalışıyoruz. 


## <a name="create-a-support-ticket"></a>Destek bileti oluşturma
1. [Azure portalını][Azure portal] açın.
2. Giriş ekranında **Yardım + destek** kutucuğuna tıklayın.
   
    ![Yardım + destek](./media/sql-data-warehouse-get-started-create-support-ticket/help-support.png)
3. Yardım + Destek dikey penceresinde **Yeni destek isteği**'ne tıklayın
   
    ![Yeni destek isteği](./media/sql-data-warehouse-get-started-create-support-ticket/create-support-request.png)
   
    <a name="request-quota-change"></a> 
4. **İstek Türü**'nü seçin.
   
    ![İstek türü](./media/sql-data-warehouse-get-started-create-support-ticket/request-type.png)
   
   > [!NOTE]
   > Varsayılan olarak her SQL sunucusu (örn. myserver.database.windows.net) 45.000’lik **DTU Kotası**’na sahiptir. Bu kota yalnızca bir güvenlik sınırıdır. Bir destek bileti oluşturarak ve istek türü olarak *Kota* ’ yı seçerek kotanızı artırabilirsiniz. DTU gereksinimlerinizi hesaplamak için gereken toplam [DWU][DWU] değerini 7,5 ile çarpın. Örneğin, tek bir SQL sunucusu üzerinde iki DW6000 barındırmak istiyorsanız, 90.000’lik DTU kotası istemeniz gerekir.  Geçerli DTU tüketiminizi portaldaki SQL server dikey penceresinden görüntüleyebilirsiniz. DTU kotasında hem duraklatılmış hem de duraklatılmamış veritabanları sayılır. 
   > 
   > 
5. Bildirdiğiniz sorunun yaşandığı veritabanını barındıran **Aboneliği** seçin.
   
    ![Abonelik](./media/sql-data-warehouse-get-started-create-support-ticket/subscription.png)
6. Kaynak olarak **SQL Data Warehouse**'u seçin.
   
    ![Kaynak](./media/sql-data-warehouse-get-started-create-support-ticket/resource.png)
7. [Azure destek planınızı][Azure support plan] seçin.
   
   * Tüm destek düzeylerinde **faturalama, kota ve abonelik yönetimi** desteği sunulmaktadır.
   * **Onarım** desteği [Geliştirici][Developer], [Standart][Standard], [Profesyonel Doğrudan][Professional Direct] veya [Premier][Premier] destek aracılığıyla sağlanır. Onarım sorunları, müşterilerin Azure'ı kullandığı sırada karşılaştıkları ve ilgili sorunun Microsoft'tan kaynaklandığına ilişkin makul bir olasılığın bulunduğu sorunlardır.
   * **Geliştirici rehberliği** ve **danışmanlık hizmetleri**, [Profesyonel Doğrudan][Professional Direct] ve [Premier][Premier] destek düzeylerinde kullanılabilir. 
     
     Bir Premier destek planınız varsa SQL Veri Ambarı ile ilgili sorunları [Microsoft Premier çevrimiçi portalı][Microsoft Premier online portal] üzerinden de bildirebilirsiniz.  Kapsam,yanıt süreleri ve fiyatlandırma dahil olmak üzere çeşitli destek planları hakkında daha fazla bilgi edinmek için bkz. [Azure destek planları][Azure support plan].  Azure desteği ile ilgili sık sorulan sorular için bkz. [Azure desteği ile ilgili SSS][Azure support FAQs].  
     
     ![Destek planı](./media/sql-data-warehouse-get-started-create-support-ticket/support-plan.png)
8. **Sorun Türü**'nü ve **Kategori**'yi seçin. Bu örnekte Sorun türü olarak "Araçlar", kategori olarak ise "İstemci araçları" seçimini yaptık. 
   
    ![Sorun türü kategori](./media/sql-data-warehouse-get-started-create-support-ticket/problem-type-category.png)
9. Sorunu açıklayın ve iş etkisi düzeyini seçin.
   
    ![Sorun açıklaması](./media/sql-data-warehouse-get-started-create-support-ticket/problem-description.png)
10. Bu destek bileti için **iletişim bilgileriniz** önceden doldurulur. Gerekli gördüğünüz halde bilgilerinizi güncelleştirin.
    
    ![İletişim bilgileri](./media/sql-data-warehouse-get-started-create-support-ticket/contact-info.png)
11. Destek isteğini göndermek için **Oluştur**'a tıklayın.

## <a name="monitor-a-support-ticket"></a>Destek biletini izleme
Destek isteğini gönderdikten sonra Azure destek ekibi sizinle iletişime geçer. İstek durumunuzu ve ayrıntılarını kontrol etmek için panoda bulunan **Destek isteklerini yönetin** kutucuğuna tıklayın.

![Durumu kontrol etme](./media/sql-data-warehouse-get-started-create-support-ticket/check-status.png)

## <a name="other-resources"></a>Diğer Kaynaklar
Ayrıca, [Stack Overflow][Stack Overflow] veya [Azure SQL Veri Ambarı MSDN forumu][Azure SQL Data Warehouse MSDN forum] üzerinden SQL Veri Ambarı topluluğuna bağlanabilirsiniz.

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md

<!--MSDN references--> 

<!--Other web references--> 
[Azure portal]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Developer]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Azure support FAQs]: https://azure.microsoft.com/support/faq/
[Microsoft Premier online portal]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/


