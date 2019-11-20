---
title: Azure Güvenlik Merkezi 'nde IaaS için gelişmiş veri güvenliği | Microsoft Docs
description: " Azure Güvenlik Merkezi 'nde IaaS için gelişmiş veri güvenliğini nasıl etkinleştirebileceğinizi öğrenin. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: memildin
ms.openlocfilehash: 068fb9f61b7dcb3948e4f03c284ddfa680522c85
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907071"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Azure sanal makineler 'de SQL sunucuları için gelişmiş veri güvenliği (Önizleme)
Azure sanal makineler 'deki SQL sunucuları için gelişmiş veri güvenliği, gelişmiş SQL güvenlik özelliklerine yönelik Birleşik bir pakettir. Bu önizleme özelliği, olası veritabanı güvenlik açıklarını tanımlama ve azaltma ve veritabanınıza tehditler gösterebilen anormal etkinlikleri algılama işlevlerini içerir. 

Azure VM 'Leri için bu güvenlik teklifi SQL Server 'lar, [Azure SQL veritabanı gelişmiş veri güvenlik paketinde](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)kullanılan temel teknolojiyi temel alır.


## <a name="overview"></a>Genel Bakış

Gelişmiş veri güvenliği, güvenlik açığı değerlendirmesinden ve Gelişmiş tehdit korumasından oluşan gelişmiş bir SQL güvenlik özellikleri kümesi sağlar.

* [Güvenlik açığı değerlendirmesi](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) , olası veritabanı güvenlik açıklarını düzeltebileceğiniz, izleyebileceğiniz ve bu sorunları gidermenize yardımcı olabilecek bir hizmeti kolayca yapılandırabilir. Güvenlik durumlarınızın görünürlüğünü sağlar ve güvenlik sorunlarını giderme ve veritabanınızın Fortifications geliştirilmesine yönelik adımları içerir.
* [Gelişmiş tehdit koruması](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) , SQL Server 'a erişmeye veya yararlanmaya yönelik olağan dışı ve zararlı olabilecek girişimleri gösteren anormal etkinlikleri algılar. Veritabanınızı sürekli şüpheli etkinlikler için izler ve anormal veritabanı erişim desenlerinde eyleme dayalı güvenlik uyarıları sağlar. Bu uyarılar, tehdidi araştırmak ve azaltmak için şüpheli etkinlik ayrıntılarını ve önerilen eylemleri sağlar.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Azure VM 'lerinde SQL için gelişmiş veri güvenliği ile çalışmaya başlama

Aşağıdaki adımlar, Azure VM 'Leri genel önizleme üzerinde SQL için gelişmiş veri güvenliği ile çalışmaya başlamanızı ister.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Azure VM 'lerde SQL için gelişmiş veri güvenliğini ayarlama

Abonelik/çalışma alanı düzeyindeki sanal makinelerde SQL sunucuları için gelişmiş veri güvenliğini etkinleştirin:
 
1. Güvenlik Merkezi 'nin kenar çubuğundan **fiyatlandırma & ayarları** sayfasını açın.

1. Azure VM 'lerinde SQL için gelişmiş veri güvenliğini etkinleştirmek istediğiniz aboneliği veya çalışma alanını seçin.

1. **VM 'Deki SQL Server (Önizleme)** seçeneğini etkinleştirmek için değiştirin. 

    (Genişletilecek ekran görüntüsüne tıklayın)

    [Windows Yönetim Merkezi 'nde görülen Güvenlik Merkezi önerilerini ve uyarılarını ![](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    Seçili çalışma alanına veya seçili aboneliğin varsayılan çalışma alanına bağlı tüm SQL Server 'Lar üzerinde SQL Server için gelişmiş veri güvenliği etkinleştirilecek.

    >[!NOTE]
    > Çözüm, SQL Server ilk yeniden başlatıldıktan sonra tam olarak etkin olacaktır. 

Yeni bir çalışma alanı oluşturmak için [Log Analytics çalışma alanı oluşturma](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)bölümündeki yönergeleri izleyin.

SQL Server konağını bir çalışma alanına bağlamak için [Windows bilgisayarları Azure izleyici 'ye bağlama](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)bölümündeki yönergeleri izleyin.


## <a name="set-up-email-notification-for-atp-alerts"></a>ATP uyarıları için e-posta bildirimi ayarlama 

Güvenlik Merkezi uyarıları oluşturulduğunda, bir e-posta bildirimi almak için alıcıların listesini ayarlayabilirsiniz. E-posta, Azure Güvenlik Merkezi 'ndeki tüm ilgili ayrıntıların bulunduğu uyarıya doğrudan bir bağlantı içerir. 

1. **Güvenlik merkezi** > **fiyatlandırma & ayarlar** ' a gidin ve ilgili aboneliğe tıklayın

    ![Abonelik ayarları](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. Ayarlar menüsünde **e-posta bildirimleri**' ne tıklayın. 
1. **E-posta adresi** metin kutusuna bildirimleri alacak e-posta adreslerini girin. E-posta adreslerini virgülle ayırarak birden fazla e-posta adresi girebilirsiniz (,).  Örneğin admin1@mycompany.com,admin2@mycompany.com,admin3@mycompany.com

    ![E-posta Ayarları](./media/security-center-advanced-iaas-data/email-settings.png)

1. **E-posta bildirimi** ayarları ' nda, aşağıdaki seçenekleri ayarlayın:
  
    * **Yüksek önem derecesi uyarıları için e-posta bildirimi gönder**: tüm uyarılar için e-posta göndermek yerine yalnızca yüksek önem derecesi uyarıları gönderin.
    * **Ayrıca, abonelik sahiplerine e-posta bildirimleri gönderin**: abonelikler sahibine da bildirimler gönderin.

1. **E-posta bildirimleri** ekranının üst kısmında **Kaydet**' e tıklayın.

  > [!NOTE]
  > Pencereyi kapatmadan önce **Kaydet** ' i tıklattığınızdan emin olun veya yeni **e-posta bildirimi** ayarları kaydedilmez.

## <a name="explore-vulnerability-assessment-reports"></a>Güvenlik açığı değerlendirme raporlarını keşfet

Güvenlik açığı değerlendirme panosu, tüm veritabanlarınızdaki değerlendirme sonuçlarınıza genel bir bakış sağlar. Veritabanlarının SQL Server sürüme göre dağıtımını, veritabanlarının başarılı olup olmadığını ve risk dağıtımına göre başarısız olan denetimlerin genel özetini de görüntüleyebilirsiniz.

Güvenlik açığı değerlendirme sonuçlarını doğrudan güvenlik merkezi 'nden görüntüleyebilirsiniz.

1. Güvenlik Merkezi 'nin kenar çubuğundan kaynak GÜVENLIĞI HYGIENE altında **veri & depolama**' yı seçin.

1. **VM 'LERDEKI SQL veritabanlarınızdaki öneri güvenlik açıklarının düzeltilme (Önizleme)** seçeneğini belirleyin. Daha fazla bilgi için bkz. [Güvenlik Merkezi önerileri](security-center-recommendations.md). 

    [VM 'lerdeki SQL veritabanlarındaki ![* * güvenlik açıkları düzeltilmelidir (Önizleme) * * öneri](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    Bu öneriye ilişkin ayrıntılı görünüm görüntülenir.

    [VM 'lerdeki SQL veritabanlarındaki * * güvenlik açıkları için ayrıntılı görünüm ![düzeltilmelidir (Önizleme) * * öneri](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. Daha ayrıntılı bilgi edinmek için:

    * Taranan kaynaklara (veritabanlarına) ve test edilen güvenlik denetimlerinin listesine genel bakış için ilgilendiğiniz sunucuya tıklayın.
    [SQL Server tarafından gruplanmış ![güvenlik açıkları](media/security-center-advanced-iaas-data/single-server-view.png)](media/security-center-advanced-iaas-data/single-server-view.png#lightbox)

    * Belirli bir SQL veritabanı tarafından gruplanmış güvenlik açıklarına genel bir bakış için, ilgilendiğiniz veritabanına tıklayın.
    [SQL Server tarafından gruplanmış ![güvenlik açıkları](media/security-center-advanced-iaas-data/single-database-view.png)](media/security-center-advanced-iaas-data/single-database-view.png#lightbox)

    Her görünümde güvenlik denetimleri **önem derecesine**göre sıralanır. Bir **Açıklama**içeren ayrıntı bölmesini, bunu nasıl **düzeltebileceğiniz** ve **etki** veya **kıyaslama**gibi diğer ilgili bilgileri görmek için belirli bir güvenlik denetimine tıklayın.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Azure VM 'lerinde SQL sunucuları için Gelişmiş tehdit koruması
Uyarılar, SQL Server 'Lar için olağandışı ve olası zararlı saldırılara göre oluşturulur. Bu olaylar aşağıdaki uyarıları tetikleyebilir:

### <a name="anomalous-access-pattern-alerts-preview"></a>Anormal erişim deseninin uyarıları (Önizleme)

* **Olağan dışı konumdan erişim:** Bu uyarı, SQL Server 'a erişim modelinde bir değişiklik olduğunda tetiklenir ve bu, birinin SQL Server 'da olağan dışı bir coğrafi konumdan oturum açtığı yerdir. Olası nedenler:
    * Bir saldırgan veya eski bir kötü amaçlı, SQL Server erişti.
    * Yasal bir Kullanıcı yeni bir konumdan SQL Server erişti.
* **Zararlı olabilecek bir uygulamadan erişim**: Bu uyarı, zararlı olabilecek bir uygulama veritabanına erişmeye çalıştığında tetiklenir. Olası nedenler:
    * Bir saldırgan ortak saldırı araçlarını kullanarak SQL 'nizi ihlal etmek üzere çalışıyor.
    * Eylemde geçerli bir sızma testi.
* **Sıra dışı bir sorumludan erişim**: Bu uyarı, SQL sunucusunun erişim deseninde değişiklik olduğunda, bir kişi SQL sunucusuna sıra dışı bir sorumludan (SQL kullanıcısı) eriştiğinde tetiklenir. Olası nedenler:
    * Bir saldırgan veya eski bir kötü amaçlı, SQL Server erişti. 
    * Yasal bir Kullanıcı, SQL Server yeni bir sorumlu ile erişmiştir.
* **SQL kimlik bilgilerine deneme yanılma saldırısı**: Bu uyarı, farklı kimlik bilgileri kullanılarak sıra dışı sayıda başarısız oturum açma denemesi olduğunda tetiklenir. Olası nedenler:
    * Bir saldırgan, deneme yanılma kullanarak SQL 'nizi ihlal etmeye çalışan bir saldırgan.
    * Eylemde geçerli bir sızma testi.

### <a name="potential-sql-injection-attacks-supported-in-sql-server-2019"></a>Olası SQL ekleme saldırıları (SQL Server 2019 ' de desteklenir)

* **SQL ekleme güvenlik açığı**: Bu uyarı, bir uygulama veritabanında hatalı bir SQL açıklaması oluşturduğunda tetiklenir. Bu uyarı, SQL ekleme saldırılarına karşı olası bir güvenlik açığını gösterebilir. Olası nedenler:
    * Hatalı SQL deyimini oluşturan uygulama kodunda hata
    * Uygulama kodu veya depolanan yordamlar, hatalı SQL deyimi yapılandırılırken kullanıcı girişini temizlemez ve SQL Ekleme sırasında bu açıktan yararlanılabilir
* **Olası SQL ekleme**: Bu uyarı, SQL ekleme işlemine tanımlı uygulama güvenlik açığına karşı etkin bir açıktan yararlanma görüldüğünde tetiklenir. Bu, saldırganın güvenlik açığına sahip uygulama kodu veya depolanan yordamları kullanan kötü amaçlı SQL deyimleri eklemeye çalıştığı anlamına gelir.


### <a name="unsafe-command-supported-in-sql-server-2019"></a>Güvenli olmayan komut (SQL Server 2019 ' de desteklenir)

* **Güvensiz olabilecek eylem**: Bu uyarı, yüksek ayrıcalıklı ve güvensiz olabilecek bir komut yürütüldüğünde tetiklenir. Olası nedenler:
    * Daha iyi güvenlik sonrası için devre dışı bırakılması önerilen komut etkin.
    * Bir saldırgan, SQL Access 'ten yararlanmaya veya ayrıcalık almaya çalışan ayrıcalıklardır.   


## <a name="explore-and-investigate-security-alerts"></a>Güvenlik uyarılarını araştır ve araştır

Veri güvenliği uyarılarınız, güvenlik merkezi 'nin Uyarılar sayfasında, kaynağın Güvenlik sekmesinde veya uyarı e-postalarında doğrudan bağlantı aracılığıyla kullanılabilir.

1. Uyarıları görüntülemek için:

    * Güvenlik Merkezi 'nde, kenar çubuğundan **güvenlik uyarıları** ' na tıklayın ve bir uyarı seçin.
    * Kaynak kapsamı ' nda, ilgili kaynak sayfasını açın ve kenar çubuğundan **güvenlik**' e tıklayın. 

1. Uyarılar, her birindeki ayrıntılı düzeltme adımları ve araştırma bilgileri ile kendine dahil olmak üzere tasarlanmıştır. Daha geniş bir görünüm için diğer Azure Güvenlik Merkezi ve Azure Sentinel özelliklerini kullanarak daha fazla araştırma yapabilirsiniz:

    * Daha fazla araştırmalar için SQL Server denetim özelliğini etkinleştirin. Azure Sentinel kullanıcısı kullanıyorsanız, Windows Güvenlik günlüğü etkinliklerinden SQL denetim günlüklerini Sentinel 'e yükleyebilir ve zengin araştırma deneyiminden yararlanabilirsiniz.
    * Güvenlik duruşunuzu geliştirmek için, her uyarıda belirtilen ana makine için Güvenlik Merkezi 'nin önerilerini kullanın. Bu, gelecekteki saldırılardan riskleri azaltır. 


## <a name="next-steps"></a>Sonraki adımlar

İlgili malzemeler için aşağıdaki makaleye bakın:

- [Öneriler nasıl düzeltileceği](security-center-remediate-recommendations.md)