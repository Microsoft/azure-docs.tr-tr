---
title: SQL için Azure Defender 'ı ayarlama
description: Azure Güvenlik Merkezi 'nin SQL planı için isteğe bağlı Azure Defender 'ı nasıl etkinleştirebileceğinizi öğrenin
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2021
ms.author: memildin
ms.openlocfilehash: a91329d3bd0247932614233ef5b1ec71bf4d2a6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103465472"
---
# <a name="enable-azure-defender-for-sql-servers-on-machines"></a>Makinelerde SQL Server 'lar için Azure Defender 'ı etkinleştirme 

Bu Azure Defender planı, veritabanlarına erişmek veya veritabanına yararlanmak için olağan dışı ve zararlı olabilecek girişimleri gösteren anormal etkinlikleri algılar.

Şüpheli veritabanı etkinlikleri, olası güvenlik açıkları veya SQL ekleme saldırıları ve anormal veritabanı erişimi ve sorgu desenleri olduğunda uyarılar görürsünüz.

## <a name="availability"></a>Kullanılabilirlik

|Görünüş|Ayrıntılar|
|----|:----|
|Yayın durumu:|Genel kullanılabilirlik (GA)|
|Fiyat|**MAKINELERDEKI SQL Server 'lar Için Azure Defender** , [Güvenlik Merkezi fiyatlandırması](https://azure.microsoft.com/pricing/details/security-center/) 'nda gösterildiği gibi faturalandırılır|
|Korumalı SQL sürümleri:|Azure SQL Server (Microsoft desteği kapsamında tüm sürümler)|
|Larının|![Yes](./media/icons/yes-icon.png) Ticari bulutlar<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Çin gov, diğer gov|
|||

## <a name="set-up-azure-defender-for-sql-servers-on-machines"></a>Makinelerde SQL Server 'lar için Azure Defender 'ı ayarlama

Bu planı etkinleştirmek için:

[1. adım. SQL sunucunuzun ana bilgisayarında Log Analytics aracısını sağlayın:](#step-1-provision-the-log-analytics-agent-on-your-sql-servers-host)

[2. adım. Güvenlik Merkezi 'nin fiyatlandırma ve Ayarlar sayfasında isteğe bağlı planı etkinleştirin:](#step-2-enable-the-optional-plan-in-security-centers-pricing-and-settings-page)


### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>Adım 1. SQL sunucunuzun ana bilgisayarında Log Analytics aracısını sağlayın:

- **Azure VM 'de SQL Server** -SQL makineniz BIR Azure VM üzerinde barındırılıyorsa, [Log Analytics aracısının <a name="auto-provision-mma"></a> Otomatik](security-center-enable-data-collection.md#auto-provision-mma)sağlanmasını sağlayabilirsiniz. Alternatif olarak, [Azure Stack hub sanal makinelerinizi](quickstart-onboard-machines.md?pivots=azure-portal#onboard-your-azure-stack-hub-vms)eklemek için el ile gerçekleştirilen prosedürü izleyebilirsiniz.
- **Azure arc SQL Server** -SQL Server [Azure Arc](../azure-arc/index.yml) etkin sunucuları tarafından yönetiliyorsa, güvenlik merkezi önerisi "Log Analytics Aracısı Windows tabanlı Azure yay makinelerinize (önizleme) yüklenmelidir" Log Analytics aracıyı dağıtabilirsiniz. Alternatif olarak, [Azure Arc belgelerinde](../azure-arc/servers/manage-vm-extensions.md)açıklanan yükleme yöntemlerini de izleyebilirsiniz.

- **Şirket içi SQL Server** -SQL Server Azure Arc olmadan şirket Içi bir Windows makinesinde barındırılıyorsa, Azure 'a bağlamak için iki seçeneğiniz vardır:
    
    - **Azure yayı dağıtma** -herhangi bir Windows makinesini Güvenlik Merkezi 'ne bağlayabilirsiniz. Bununla birlikte, Azure Arc *Tüm* Azure ortamlarınız genelinde daha derin tümleştirme sağlar. Azure yay ayarlarsanız, portalda **SQL Server – Azure yay** sayfasını görürsünüz ve güvenlik uyarılarınız ilgili sayfada özel bir **güvenlik** sekmesinde görüntülenir. Bu nedenle, ilk ve önerilen seçenek, [Azure Arc 'ı konakta ayarlamaya](../azure-arc/servers/onboard-portal.md#install-and-validate-the-agent-on-windows) ve yukarıdaki **azure Arc üzerinde SQL Server** yönergelerini izlemeye yöneliktir.
        
    - **Windows makinesini Azure Arc olmadan bağlama** -Windows makinesinde çalışan bir SQL Server Azure yay kullanmadan bağlamayı seçerseniz, [Windows makinelerini Azure izleyici 'ye bağlama](../azure-monitor/agents/agent-windows.md)bölümündeki yönergeleri izleyin.


### <a name="step-2-enable-the-optional-plan-in-security-centers-pricing-and-settings-page"></a>Adım 2. Güvenlik Merkezi 'nin fiyatlandırma ve Ayarlar sayfasında isteğe bağlı planı etkinleştirin:

1. Güvenlik Merkezi 'nin menüsünde, **fiyatlandırma & ayarları** sayfasını açın.

    - **Azure Güvenlik Merkezi 'nin varsayılan çalışma alanını** ("defaultworkspace-[abonelik kimliğiniz]-[Bölge]" olarak adlandırılan) kullanıyorsanız ilgili **aboneliği** seçin.

    - **Varsayılan olmayan bir çalışma alanı** kullanıyorsanız ilgili **çalışma alanını** seçin (gerekirse filtreye çalışma alanının adını girin):

        ![Varsayılan olmayan çalışma alanınızı başlığa göre bulma](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)

1. **MAKINELERDEKI SQL Server 'lar Için Azure Defender** seçeneğini **ayarlayın.** 

    :::image type="content" source="./media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png" alt-text="İsteğe bağlı planlar içeren güvenlik merkezi fiyatlandırma sayfası":::

    Plan, seçilen çalışma alanına bağlı tüm SQL Server 'lar üzerinde etkinleştirilecek. SQL Server örneğinin ilk yeniden başlatıldıktan sonra koruma tam olarak etkin olur.

    >[!TIP] 
    > Yeni bir çalışma alanı oluşturmak için [Log Analytics çalışma alanı oluşturma](../azure-monitor/logs/quick-create-workspace.md)bölümündeki yönergeleri izleyin.


1. İsteğe bağlı olarak, güvenlik uyarıları için e-posta bildirimini yapılandırın. 
    Güvenlik Merkezi uyarıları oluşturulduğunda, bir e-posta bildirimi almak için alıcıların listesini ayarlayabilirsiniz. E-posta, Azure Güvenlik Merkezi 'ndeki tüm ilgili ayrıntıların bulunduğu uyarıya doğrudan bir bağlantı içerir. Daha fazla bilgi için bkz. [güvenlik uyarıları için e-posta bildirimleri ayarlama](security-center-provide-security-contact-details.md).


## <a name="azure-defender-for-sql-alerts"></a>SQL için Azure Defender uyarıları
Uyarılar, SQL makinelerine erişmek veya bu makinelere yararlanmak için olağandışı ve olası zararlı saldırılara göre oluşturulur. Bu olaylar, [Uyarılar başvurusu sayfasında](alerts-reference.md#alerts-sql-db-and-warehouse)gösterilen uyarıları tetikleyebilirsiniz.

## <a name="explore-and-investigate-security-alerts"></a>Güvenlik uyarılarını araştır ve araştır

SQL uyarıları için Azure Defender, güvenlik merkezi 'nin Uyarılar sayfasında, kaynağın Güvenlik sekmesinde, [Azure Defender panosunda](azure-defender-dashboard.md)veya uyarı e-postalarında doğrudan bağlantı aracılığıyla kullanılabilir.

1. Uyarıları görüntülemek için Güvenlik Merkezi menüsünden **güvenlik uyarıları** ' nı seçin ve bir uyarı seçin.

1. Uyarılar, her birindeki ayrıntılı düzeltme adımları ve araştırma bilgileri ile kendine dahil olmak üzere tasarlanmıştır. Daha geniş bir görünüm için diğer Azure Güvenlik Merkezi ve Azure Sentinel özelliklerini kullanarak daha fazla araştırma yapabilirsiniz:

    * Daha fazla araştırmalar için SQL Server denetim özelliğini etkinleştirin. Azure Sentinel kullanıcısı kullanıyorsanız, Windows Güvenlik günlüğü etkinliklerinden SQL denetim günlüklerini Sentinel 'e yükleyebilir ve zengin araştırma deneyiminden yararlanabilirsiniz. [SQL Server denetimi hakkında daha fazla bilgi edinin](/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?preserve-view=true&view=sql-server-ver15).
    * Güvenlik duruşunuzu geliştirmek için, her uyarıda belirtilen ana makine için Güvenlik Merkezi 'nin önerilerini kullanın. Bu, gelecekteki saldırılardan riskleri azaltır. 

    [Uyarıları yönetme ve uyarıları yanıtlama hakkında daha fazla bilgi edinin](security-center-managing-and-responding-alerts.md).


## <a name="next-steps"></a>Sonraki adımlar

İlgili malzemeler için aşağıdaki makaleye bakın:

- [SQL veritabanı ve Azure SYNAPSE Analytics için güvenlik uyarıları](alerts-reference.md#alerts-sql-db-and-warehouse)
- [Güvenlik uyarıları için e-posta bildirimlerini ayarlama](security-center-provide-security-contact-details.md)
- [Azure Sentinel hakkında daha fazla bilgi edinin](../sentinel/index.yml)