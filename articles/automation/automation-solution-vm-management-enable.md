---
title: Azure Otomasyonu VM'leri çalışma saatleri dışında başlat/durdur etkinleştirme
description: Bu makalede, Azure VM 'leriniz için VM'leri çalışma saatleri dışında başlat/durdur özelliğinin nasıl etkinleştirileceği açıklanır.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 36f885416c5e9cb656d01a65b9c503f8897d2f9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593893"
---
# <a name="enable-startstop-vms-during-off-hours"></a>VM'leri çalışma saatleri dışında başlat/durdur özelliğini etkinleştirme

Yeni veya mevcut bir Otomasyon hesabı ve bağlantılı Log Analytics çalışma alanı kullanarak VM 'Ler için VM'leri çalışma saatleri dışında başlat/durdur özelliğini etkinleştirmek üzere bu konudaki adımları uygulayın. Kurulum işlemini tamamladıktan sonra, özelliği özelleştirmek için değişkenleri yapılandırın.

>[!NOTE]
>Bu özelliği klasik VM 'lerle birlikte kullanmak için, varsayılan olarak oluşturulmamış bir klasik Farklı Çalıştır hesabına ihtiyacınız vardır. Bkz. [Klasik farklı çalıştır hesabı oluşturma](automation-create-standalone-account.md#create-a-classic-run-as-account).
>

## <a name="create-resources-for-the-feature"></a>Özellik için kaynaklar oluşturma

1. Azure [portalında](https://portal.azure.com)oturum açın.
2. **Otomasyon hesaplarını** arayın ve seçin.
3. Otomasyon hesapları sayfasında, listeden Otomasyon hesabınızı seçin.
4. Otomasyon hesabından **Ilgili kaynaklar** altında **VM Başlat/Durdur** ' u seçin. Buradan **daha fazla bilgi edinin ve çözümü etkinleştirin**. Özelliği zaten dağıttıysanız, **çözümü Yönet** ' e tıklayıp listeden bulabilirsiniz.

   ![Otomasyon hesabından etkinleştir](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Kaynak **Oluştur ' a** tıklayarak kaynağı Azure Portal herhangi bir yerden da oluşturabilirsiniz. Market sayfasında **Başlat** veya **Başlat/Durdur** gibi bir anahtar sözcük yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. Alternatif olarak, özelliğin tam adından bir veya daha fazla anahtar sözcük yazabilir ve ardından **ENTER** tuşuna basabilirsiniz. Arama sonuçlarından **VM'leri çalışma saatleri dışında Başlat/Durdur** seçin.

5. Seçili dağıtımın VM'leri çalışma saatleri dışında başlat/durdur sayfasında, Özet bilgilerini gözden geçirin ve ardından **Oluştur**' a tıklayın.

   ![Azure portalı](media/automation-solution-vm-management/azure-portal-01.png)

## <a name="configure-the-feature"></a>Özelliği yapılandırma

Kaynak oluşturulduğunda çözüm Ekle sayfası görüntülenir. Özelliği Otomasyon aboneliğinize aktarmadan önce yapılandırmanız istenir. Bkz. [Configure VM'leri çalışma saatleri dışında Başlat/Durdur](automation-solution-vm-management-config.md).

   ![VM yönetimi çözüm ekleme sayfası](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

## <a name="select-a-log-analytics-workspace"></a>Log Analytics çalışma alanı seçin

1. Çözüm Ekle sayfasında, **çalışma alanı**' nı seçin. Otomasyon hesabı tarafından kullanılan Azure aboneliğine bağlı bir Log Analytics çalışma alanı seçin. 

2. Çalışma alanınız yoksa **Yeni çalışma alanı oluştur**' u seçin. Log Analytics çalışma alanı sayfasında, aşağıdaki adımları uygulayın:

   - Yeni Log Analytics çalışma alanı için **Contosolaworkspace** gibi bir ad belirtin.
   - Varsayılan seçilen değer uygun değilse, açılan listeden seçerek bağlantı için bir **abonelik** seçin.
   - **Kaynak grubu** için, yeni bir kaynak grubu oluşturabilir veya var olan bir grup seçebilirsiniz.
   - Bir **konum** seçin.
   - Bir **Fiyatlandırma katmanı** seçin. **GB başına (tek başına)** seçeneğini belirleyin. Azure Izleyici günlükleri güncelleştirilmiş [fiyatlandırmaya](https://azure.microsoft.com/pricing/details/log-analytics/) sahıptır ve GB başına katman tek seçenektir.

   > [!NOTE]
   > Özellikleri etkinleştirirken, bir Log Analytics çalışma alanını ve bir Otomasyon hesabını bağlamak için yalnızca belirli bölgeler desteklenir. Desteklenen eşleme çiftlerinin bir listesi için bkz. [Otomasyon hesabı ve Log Analytics çalışma alanı Için bölge eşleme](how-to/region-mappings.md).

3. Log Analytics çalışma alanı sayfasında gerekli bilgileri sağladıktan sonra **Oluştur**' a tıklayın. Bu işlemi, tamamlandığında çözüm Ekle sayfasına geri getiren menüdeki **Bildirimler** bölümünden izleyebilirsiniz.

## <a name="add-automation-account"></a>Otomasyon hesabı ekle

Çözüm Ekle sayfasına yeniden erişin ve **Otomasyon hesabı**' nı seçin. Zaten bir Log Analytics çalışma alanına bağlı olmayan mevcut bir Otomasyon hesabı seçebilirsiniz. Yeni bir Log Analytics çalışma alanı oluşturuyorsanız, bununla ilişkilendirilecek yeni bir Otomasyon hesabı oluşturabilirsiniz. Var olan bir Otomasyon hesabı seçin veya **Otomasyon** hesabı Ekle ' ye tıklayın ve Otomasyon hesabı Ekle sayfasında, **ad** alanındaki Otomasyon hesabının adını belirtin.

Diğer tüm seçenekler, seçili Log Analytics çalışma alanına göre otomatik olarak doldurulur. Bu seçenekleri değiştiremezsiniz. Bir Azure farklı çalıştır hesabı, özelliğe dahil edilen runbook 'lar için varsayılan kimlik doğrulama yöntemidir. 

**Tamam**' a tıkladıktan sonra yapılandırma seçenekleri onaylanır ve Otomasyon hesabı oluşturulur. Bu işlemin ilerleme durumunu menüdeki **Bildirimler**’in altından izleyebilirsiniz.

## <a name="define-feature-parameters"></a>Özellik parametrelerini tanımlama

1. Çözüm Ekle sayfasında **yapılandırma**' yı seçin. Parametreler sayfası görüntülenir.

    ![Çözüm için parametreler sayfası](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

2. **Hedef ResourceGroup adları** alanı için bir değer belirtin. Alan, yönetilecek özelliğin VM 'Leri içeren grup adlarını tanımlar. Birden fazla ad girebilir ve adları virgüller kullanarak ayırabilirsiniz (değerler büyük/küçük harfe duyarlı değildir). Abonelikte yer alan tüm kaynak gruplarındaki sanal makineleri hedeflemek istiyorsanız, joker karakter kullanılması desteklenir. Değerler `External_Start_ResourceGroupNames` ve `External_Stop_ResourceGroupNames` değişkenlerinde saklanır.

    > [!IMPORTANT]
    > **Hedef ResourceGroup adları** için varsayılan değer bir **&ast;** . Bu ayar, bir abonelikteki tüm VM 'Leri hedefler. Özelliğin aboneliğinizdeki tüm VM 'Leri hedeflemesini istemiyorsanız, bir zamanlama seçmeden önce kaynak grubu adlarının bir listesini sağlamanız gerekir.
  
3. **VM dışlama listesi (dize)** alanı için bir değer belirtin. Bu değer, hedef kaynak grubundaki bir veya daha fazla sanal makinenin adıdır. Birden fazla ad girebilir ve adları virgüller kullanarak ayırabilirsiniz (değerler büyük/küçük harfe duyarlı değildir). Joker karakter kullanımı desteklenir. Bu değer, `External_ExcludeVMNames` değişkeninde depolanır.
  
4. Özelliği tarafından VM yönetimi için bir zamanlama seçmek üzere **zamanlama** alanını kullanın. Zamanlama için bir başlangıç tarihi ve saati seçerek, seçilen zamandan başlayarak yinelenen bir günlük zamanlama oluşturun. Farklı bir bölge seçme kullanılamıyor. Özelliği yapılandırdıktan sonra zamanlamayı belirli saat diliminize göre yapılandırmak için, bkz. [Başlangıç ve kapalı zamanlamaları değiştirme](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules).

5. Bir [eylem grubundan](../azure-monitor/alerts/action-groups.md)e-posta bildirimleri almak Için, **e-posta bildirimleri** alanında **Yes** varsayılan değerini kabul edin ve geçerli bir e-posta adresi sağlayın. **Hayır** ' ı seçerseniz, e-posta bildirimleri almak istediğiniz sonraki bir tarihte, virgülle ayrılmış geçerli e-posta adresleriyle oluşturulan eylem grubunu güncelleştirebilirsiniz. 

6. Aşağıdaki uyarı kurallarını etkinleştirin:

   - `AutoStop_VM_Child`
   - `Scheduled_StartStop_Parent`
   - `Sequenced_StartStop_Parent`

## <a name="create-alerts"></a>Uyarı oluşturma

VM'leri çalışma saatleri dışında başlat/durdur, önceden tanımlanmış bir uyarı kümesi içermez. DevOps veya işletimsel işlemlerinizi ve yordamlarınızı desteklemek üzere iş başarısız uyarıları oluşturmayı öğrenmek için [Azure izleyici ile günlük uyarıları oluşturma](../azure-monitor/alerts/alerts-log.md) ' yı gözden geçirin.

## <a name="deploy-the-feature"></a>Özelliği dağıtma

1. Özelliği için gereken başlangıç ayarlarını yapılandırdıktan sonra, parametreler sayfasını kapatmak için **Tamam** ' ı tıklatın.

2. **Oluştur**’a tıklayın. Tüm ayarlar doğrulandıktan sonra, özelliği aboneliğinize dağıtır. Bu işlemin tamamlanması birkaç saniye sürebilir ve ilerleme durumunu menüdeki **Bildirimler** ' in altından izleyebilirsiniz.

    > [!NOTE]
    > Azure bulut çözümü sağlayıcısı (Azure CSP) aboneliğiniz varsa, dağıtım tamamlandıktan sonra Otomasyon hesabınızda, **paylaşılan kaynaklar** altındaki **değişkenlere** gidin ve [External_EnableClassicVMs](automation-solution-vm-management.md#variables) değişkenini **false** olarak ayarlayın. Bu, çözümün klasik VM kaynaklarına bakmasını engeller.

## <a name="next-steps"></a>Sonraki adımlar

* Özelliği ayarlamak için, bkz. [çalışma saatleri dışında VM 'Leri durdurmayı/başlatmayı yapılandırma](automation-solution-vm-management-config.md).
* Özellik hatalarını çözümlemek için bkz. [sorun giderme VM'leri çalışma saatleri dışında Başlat/Durdur sorunları](troubleshoot/start-stop-vm.md).