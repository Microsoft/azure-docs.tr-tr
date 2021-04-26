---
title: İstek kotası artar ve destek alın
description: Azure SYNAPSE Analytics için Azure portal bir destek isteği oluşturma. İstek kotası artar veya sorun çözümleme desteğini alır.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/10/2020
author: julieMSFT
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 5256c6d25a9c7acfc45cdffee05c95fb3407c24a
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568327"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>İstek kotası artar ve Azure SYNAPSE Analytics desteği alınır

Bu makalede, Azure SYNAPSE Analytics için Azure portal bir destek bileti gönderme açıklanır. Bu işlem, bir kota artışı talep etmenizi veya mühendislik desteği ekibine yönelik teknik destek talebi göndermenizi sağlar.

## <a name="create-a-support-ticket"></a>Destek bileti oluşturma

Azure SYNAPSE Analytics için Azure portal yeni bir destek isteği oluşturmak için aşağıdaki adımları kullanın.

1. [Azure Portal](https://portal.azure.com) menüsünde **Yardım + Destek**' i seçin.

   ![Yardım + destek bağlantısı](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. **Yardım + Destek** bölümünde **Yeni destek isteği**' ni seçin.

    ![Yeni bir destek isteği oluşturun](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. [Azure destek planınızı](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)gözden geçirin.

   * Tüm destek düzeylerinde **faturalama, kota ve abonelik yönetimi** desteği sunulmaktadır.
   * **Onarım** desteği [Geliştirici](https://azure.microsoft.com/support/plans/developer/), [Standart](https://azure.microsoft.com/support/plans/standard/), [profesyonel doğrudan](https://azure.microsoft.com/support/plans/prodirect/)veya [Premier](https://azure.microsoft.com/support/plans/premier/) destek aracılığıyla sağlanır. Onarım sorunları, müşterilerin Azure'ı kullandığı sırada karşılaştıkları ve ilgili sorunun Microsoft'tan kaynaklandığına ilişkin makul bir olasılığın bulunduğu sorunlardır.
   * **Geliştirici rehberliği** ve **danışmanlık hizmetleri**, [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) ve [Premier](https://azure.microsoft.com/support/plans/premier/) destek düzeylerinde kullanılabilir.

   Premier Destek planınız varsa, [Microsoft Premier çevrimiçi portalındaki](https://premier.microsoft.com/)Azure SYNAPSE Analytics sorunlarını da bildirebilirsiniz. Kapsam, yanıt süreleri, fiyatlandırma vb. dahil olmak üzere çeşitli destek planları hakkında daha fazla bilgi edinmek için bkz. [Azure destek planları](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) .  Azure desteği hakkında sık sorulan sorular için bkz. [Azure desteği SSS](https://azure.microsoft.com/support/faq/).

1. **Sorun türü** için uygun sorun türünü seçin. Onarım sorunları için **Teknik**' i seçin. Kota artışı istekleri için **hizmet ve abonelik sınırları (kotalar)** öğesini seçin.

   ![Sorun türü seçin](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > Bu makalenin geri kalanı, kota artışı istekleri üzerine odaklandırırsınız. Ancak sorun çözümü destek istekleri için burada **Teknik** ' i de seçebilirsiniz. **Teknik**' i seçerseniz, bir Özet girmeniz ve ardından **sorun türünü seç**' i seçerek bir sorun türü belirlemeniz istenir. Sorununuzu gidermeye yardımcı olabilecek çözümler görebilirsiniz. Sunulan çözümler sorununuzu gidermezse, **İleri** ' yi seçin ve destek bileti göndermek için formu doldurun.

1. Kota artışı istekleri için **Kota türü** Için **Azure SYNAPSE Analytics** ' i seçin. Ardından Ileri ' yi seçin **: çözümler >>**.

   ![Kota türü seçin](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. **Ayrıntılar** penceresinde, ek bilgi girmek Için **ayrıntıları girin** ' i seçin.

   !["Ayrıntıları sağla" bağlantısı](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Kota istek türleri

**Ayrıntıları gir** ' i seçtiğinizde, ek bilgi eklemenize olanak tanıyan **Kota ayrıntıları** penceresi görüntülenir. Aşağıdaki bölümlerde, Azure SYNAPSE Analytics için kullanılabilen farklı kota istekleri açıklanır.

### <a name="synapse-sql-pool-data-warehouse-units-dwus-per-server"></a>Sunucu başına SYNAPSE SQL havuzu veri ambarı birimleri (DWUs)

Sunucu başına DWU 'da artış istemek için aşağıdaki adımları kullanın.

1. **Sunucu başına SYNAPSE SQL havuzunu DWUs** kota türünü seçin.

1. Açılan listeyi kullanarak kota artışını uygulamak istediğiniz **kaynağı** seçin.

1. Yeni kotayı **istek kotası** bölümüne girin.

1. **Kaydet ve devam et**’i seçin.

   ![DWU kota ayrıntıları](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)


### <a name="servers-per-subscription"></a>Abonelik başına sunucu sayısı

Abonelik başına sunucu sayısında artış istemek için aşağıdaki adımları gerçekleştirmeniz gerekir:

1. Kota türü olarak **abonelik başına SQL sunucularını** seçin.

1. **Konum** listesinde, kullanılacak Azure bölgesini seçin. Kota her bölgede abonelik başına olur.

1. **İstek kotası** alanına, bu bölgedeki en fazla sunucu sayısı için isteğinizi girin.

   ![Sunucu kotası ayrıntıları](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)



1. **Kaydet ve devam et**’i seçin.

Bazı teklif türleri her bölgede kullanılamaz. Aşağıdaki hatayı görebilirsiniz:

![Bölge erişimi hatası](./media/sql-data-warehouse-get-started-create-support-ticket/region-access-error.png)

### <a name="enable-subscription-access-to-a-region"></a>Bir bölgeye abonelik erişimini etkinleştirme

Bir abonelik için bölge erişimini etkinleştirmek üzere aşağıdaki adımları gerçekleştirmeniz gerekir:  

1. **SYNAPSE SQL Havuzu (veri ambarı) bölgesine erişim** kotası türünü seçin.

1. Açılan listeden bir **konum** seçerek bölgeyi seçin.

1. DWU performansı gereksinimini **DWU gerekli** bölümünde belirtin.

1. **İş gereksinimlerinin açıklamasını** girin. 

1. **Kaydet ve devam et**’i seçin.

![Bölge erişimi](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-region.png)


### <a name="for-other-quota-requests"></a>Diğer kota istekleri için

Diğer kota istek türleri için kota türü açılan menüsünden **diğer kota isteği** ' ni seçin:

![Diğer kota ayrıntıları](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details.png)

## <a name="submit-your-request"></a>İsteğinizi gönderin

Son adım, SQL veritabanı destek isteğinizin geri kalan ayrıntılarını doldurmasıdır. Ardından Ileri ' yi seçin **: gözden geçir + oluştur>>**.

![İnceleme ayrıntılarını gözden geçirin](./media/sql-data-warehouse-get-started-create-support-ticket/review-create-details.png)

İstek ayrıntılarını inceledikten sonra, isteği göndermek için **Oluştur** ' u seçin.

![Bilet oluştur](./media/sql-data-warehouse-get-started-create-support-ticket/create-ticket.png)

## <a name="monitor-a-support-ticket"></a>Destek biletini izleme

Destek isteğini gönderdikten sonra, Azure destek ekibi sizinle iletişim kuracaktır. İstek durumunuzu ve ayrıntılarını denetlemek için panoda **tüm destek istekleri** ' ni seçin.

![Durumu kontrol etme](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Diğer kaynaklar

Azure SYNAPSE Analytics Community ile [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) veya [Azure SYNAPSE Analytics için Microsoft Q&soru sayfası](/answers/topics/azure-synapse-analytics.html)aracılığıyla da bağlanabilirsiniz.