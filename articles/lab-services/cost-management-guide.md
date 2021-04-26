---
title: Azure Lab Services için maliyet yönetimi Kılavuzu
description: Laboratuvar Hizmetleri için maliyetleri görüntülemenin farklı yollarını anlayın.
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: article
ms.openlocfilehash: 29f6be5319c5a142ad3ea0d73deb2f95d8cb0d7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659735"
---
# <a name="cost-management-for-azure-lab-services"></a>Azure Lab Services için maliyet yönetimi

Azure Lab Services için maliyet yönetimi iki farklı alana ayrılabilir: maliyet tahmini ve maliyet analizi. Maliyet tahmini, laboratuvarın ilk yapısının beklenen bütçeye sığmasını sağlamak üzere Laboratuvarı ayarlarken oluşur. Maliyet Analizi genellikle ayın sonunda, sonraki ay için gerekli eylemleri belirlemede oluşur.

## <a name="estimate-the-lab-costs"></a>Laboratuvar maliyetlerini tahmin etme

Her laboratuvar panosunda, laboratuvarın ay için maliyeti olan kabaca bir tahmin sunan bir **maliyetler & faturalandırma** bölümü vardır. Maliyet tahmini, en fazla kullanıcı sayısı ile saat başına tahmini maliyet kadar olan saat kullanımını özetler. En doğru tahmini sağlamak için, [zamanlama](how-to-create-schedules.md)dahil olmak üzere Laboratuvarı ayarlayın. Pano tahmini maliyeti yansıtır. 

Bu tahmin, olası tüm maliyetleri gösteremeyebilir. Birkaç kaynak dahil değildir:

- Şablon hazırlama maliyeti. Şablonu oluşturmak için gereken süre miktarının önemli ölçüde farklılık gösterebilir. Şablonu çalıştırma maliyeti, saat başına genel laboratuvar maliyetiyle aynıdır. 
- Bir galeri birden çok laboratuvar arasında paylaşılabilmesi için tüm [paylaşılan görüntü Galerisi](how-to-use-shared-image-gallery.md) maliyetleri. 
- Laboratuvar oluşturucusunun bir sanal makineyi (VM) başlattığında oluşan saatler.

> [!div class="mx-imgBorder"]
> ![Pano maliyeti tahminini gösteren ekran görüntüsü.](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-the-previous-months-usage"></a>Önceki ayın kullanımını çözümle

Maliyet analizi, laboratuvara ilgili herhangi bir ayarlamayı belirlemenize yardımcı olmak için önceki ayın kullanımını gözden geçirmeye yöneliktir. Önceki maliyetlerin dökümünü [abonelik maliyeti analizinde](../cost-management-billing/costs/quick-acm-cost-analysis.md)bulabilirsiniz. Azure portal, arama kutusuna **abonelikler** girebilir ve ardından **abonelikler** seçeneğini belirleyebilirsiniz. 

> [!div class="mx-imgBorder"]
> ![Arama kutusunu ve abonelikler seçeneğini gösteren ekran görüntüsü.](./media/cost-management-guide/subscription-search.png)

Gözden geçirmek istediğiniz belirli bir abonelik seçin.

> [!div class="mx-imgBorder"]
> ![Abonelik seçimini gösteren ekran görüntüsü.](./media/cost-management-guide/subscription-select.png)

Maliyet **yönetimi** altında sol bölmede **Maliyet Analizi** ' ni seçin.

> [!div class="mx-imgBorder"]
> ![Bir grafikteki abonelik maliyeti analizini gösteren ekran görüntüsü.](./media/cost-management-guide/subscription-cost-analysis.png)

Bu Pano, bir zamanlamaya göre farklı dosya türlerine dışarı aktarma özelliği de dahil olmak üzere, derinlemesine maliyet analizine izin verir. Daha fazla bilgi için bkz. [maliyet yönetimi + faturalandırma genel bakış](../cost-management-billing/cost-management-billing-overview.md).

Kaynak türüne göre filtre uygulayabilirsiniz. Kullanmak `microsoft.labservices/labaccounts` , yalnızca laboratuvar hizmetleriyle ilişkili maliyeti gösterir.

## <a name="understand-the-usage"></a>Kullanımı anlayın

Aşağıdaki ekran görüntüsü, maliyet analizine bir örnektir.

> [!div class="mx-imgBorder"]
> ![Bir abonelik için örnek maliyet analizini gösteren ekran görüntüsü.](./media/cost-management-guide/cost-analysis.png)

Varsayılan olarak, altı sütun vardır: **kaynak**, **kaynak türü**, **konum**, **kaynak grubu adı**, **Etiketler** ve **Maliyet**. **Kaynak** sütunu, laboratuvar hesabı, laboratuvar adı ve VM hakkındaki bilgileri içerir. Laboratuvar hesabı, laboratuvar adı ve varsayılan (ikinci ve üçüncü satırlar) gösteren satırlar, laboratuvarın maliyetlidir. Kullanılan VM 'Ler, laboratuvar hesabı, laboratuvar adı, varsayılan ve VM adını gösteren satırlar için görebileceğiniz bir maliyettir. 

Bu örnekte, ilk ve ikinci satırları ekleme (her ikisi de **aaalab/dockerlab** ile başlar), "aaalab" Laboratuvar hesabında "dockerlab" Laboratuvarı için toplam maliyet sağlar.

Görüntü galerisinin genel maliyetini almak için kaynak türünü olarak değiştirin `Microsoft.Compute/Galleries` . Bir paylaşılan görüntü Galerisi, galerinin nerede depolandığına bağlı olarak maliyetlerde görünmeyebilir.

> [!NOTE]
> Bir paylaşılan görüntü Galerisi laboratuvar hesabına bağlıdır. Bu, birden çok laboratuvarın aynı görüntüyü kullanabileceği anlamına gelir.

## <a name="separate-the-costs"></a>Maliyetleri ayırın

Bazı üniversiteler, bir laboratuvar hesabı ve kaynak grubunu, sınıfları ayırmanın yolları olarak kullandı. Her sınıfın kendi laboratuvar hesabı ve kaynak grubu vardır. 

Maliyet Analizi bölmesinde, kaynak grubu adına temel alan, sınıf için uygun kaynak grubu adıyla bir filtre ekleyin. Daha sonra, yalnızca bu sınıfa ait maliyetler görünür olur. Bu, maliyetleri görüntülerken sınıflar arasında daha net bir geri alma olanağı sağlar. Her sınıfın maliyetlerini ayrı dosyalarda indirmek için maliyet analizinin [zamanlanan dışa aktarma](../cost-management-billing/costs/tutorial-export-acm-data.md) özelliğini kullanabilirsiniz.

## <a name="manage-costs"></a>Maliyetleri yönetme

Sınıfın türüne bağlı olarak, bir öğrenci olmadan çalışan sanal makinelerin örneklerini azaltmak için maliyetleri yönetmenin yolları vardır.

### <a name="automatic-shutdown-settings-for-cost-control"></a>Maliyet denetimi için otomatik kapatılma ayarları

Otomatik olarak kapalı özellikler, laboratuvarlarda harcanan VM kullanım saatlerini engellemenizi sağlar. Aşağıdaki ayarlar, kullanıcıların sanal makinelerini yanlışlıkla terk ettiği durumların çoğunu yakalar:

> [!div class="mx-imgBorder"]
> ![Üç otomatik kapatılma ayarını gösteren ekran görüntüsü.](./media/cost-management-guide/auto-shutdown-disconnect.png)

Bu ayarları hem laboratuvar hesabı düzeyinde hem de laboratuvar düzeyinde yapılandırabilirsiniz. Bunları laboratuvar hesabı düzeyinde etkinleştirirseniz laboratuvar hesabı içindeki tüm laboratuvarlara uygulanır. Tüm yeni laboratuvar hesapları için bu ayarlar varsayılan olarak açıktır. 

#### <a name="automatically-disconnect-users-from-virtual-machines-that-the-os-deems-idle"></a>İşletim sisteminin boşta kaldığı sanal makinelerle kullanıcıların bağlantısını otomatik olarak keser

> [!NOTE]
> Bu ayar yalnızca Windows sanal makineler için kullanılabilir.

**Sanal makineler boşta kaldığında kullanıcıların bağlantısını kes** ayarı açık olduğunda, Windows işletim sistemi oturumun (şablon sanal makineler dahil) boşta kalması durumunda kullanıcının laboratuvardaki makinelerle bağlantısı kesilir. [Boştaki Windows işletim sistemi tanımı](/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state) iki ölçüt kullanır: 

* Kullanıcı devamsızlığı: klavye veya fare girişi yok.
* Kaynak tüketiminin bulunmaması: tüm işlemciler ve tüm diskler belirli bir süre için boşta değildi.

Kullanıcılar, bağlanmadan önce VM 'de şöyle bir ileti görür: 

> [!div class="mx-imgBorder"]
> ![Bir oturumun zaman sınırının üzerinde boşta olduğunu ve bağlantısının kesileceğini belirten bir uyarı iletisi gösteren ekran görüntüsü.](./media/cost-management-guide/idle-timer-expired.png)
 
Kullanıcının bağlantısı kesildiğinde sanal makine çalışmaya devam eder. Kullanıcı oturum açarken sanal makineye yeniden bağlanırsa, Windows veya açık olan veya bağlantısı kesilmeye başlamadan önce kaydedilmemiş olan dosyalar burada kalır. Bu durumda, sanal makine çalıştığından, hala etkin ve tahakkuk eden maliyet olarak sayılır. 
 
Bağlantısı kesilen boşta Windows sanal makinelerini otomatik olarak kapatmak için, **sanal makineler boşta kaldığında kullanıcıların bağlantısını kesme** bileşimini kullanın ve **kullanıcıların ayarları kesildiğinde sanal makineleri kapatın** .

Örneğin, ayarları aşağıdaki gibi yapılandırırsanız:
 
* **Sanal makineler boşta kaldığında kullanıcıların bağlantısını kesin**: boşta durumu algılandıktan 15 dakika sonra.
* **Kullanıcıların bağlantısı kesildiğinde sanal makineleri kapat**: kullanıcının bağlantısı kesildikten 5 dakika sonra.
 
Windows sanal makineleri, Kullanıcı bunları kullanmayı durdurduktan sonra 20 dakika sonra otomatik olarak kapatılır. 
 
> [!div class="mx-imgBorder"]
> ![Otomatik VM kapatmasıyla sonuçlanan ayarların birleşimini gösteren diyagram.](./media/cost-management-guide/vm-idle-diagram.png)

#### <a name="automatically-shut-down-virtual-machines-when-users-disconnect"></a>Kullanıcıların bağlantısı kesildiğinde sanal makineleri otomatik olarak kapat
 
**Kullanıcıların bağlantısı kesildiğinde sanal makineleri kapat** ayarı hem Windows hem de Linux sanal makinelerini destekler. Bu ayar açık olduğunda, otomatik olarak kapatmadan önce Şu durumlarda gerçekleşir:
 
* Windows için Uzak Masaüstü (RDP) bağlantısının bağlantısı kesilir.
* Linux için bir SSH bağlantısının bağlantısı kesilir.
 
> [!NOTE]
> Yalnızca [belirli bir Linux dağıtımları ve Linux sürümleri](../virtual-machines/extensions/diagnostics-linux.md#supported-linux-distributions) desteklenir.
 
Sanal makinelerin otomatik olarak kapanmadan önce kullanıcının yeniden bağlanmasına ne kadar süre beklemesi gerektiğini belirtebilirsiniz. 

#### <a name="automatically-shut-down-virtual-machines-that-are-started-but-users-dont-connect"></a>Başlatılan ancak kullanıcılar bağlanmadığında sanal makineleri otomatik olarak kapat
 
Bir laboratuvarda Kullanıcı bir sanal makine başlatabilir, ancak bu sunucuya hiçbir şekilde bağlanmayabilir. Örnek:
 
* Laboratuvardaki bir zamanlama, bir sınıf oturumu için tüm sanal makineleri başlatır, ancak bazı öğrenciler makinelere bağlanmayın ve bunlara bağlanmayın. 
* Bir Kullanıcı bir sanal makine başlatır ancak bağlanmayı unutur. 
 
**Kullanıcılar bağlanmayan sanal makineleri kapat** ayarı bu durumları yakalar ve sanal makineleri otomatik olarak kapatır. 
 
Bağlantı kesildiğinde VM 'lerin otomatik olarak kapatılmasını yapılandırma ve etkinleştirme hakkında bilgi için şu makalelere bakın:

* [Laboratuvar hesabı için sanal makinelerin otomatik olarak kapatılmasını yapılandırın](how-to-configure-lab-accounts.md)
* [Laboratuvar için sanal makinelerin otomatik olarak kapatılmasını yapılandırma](how-to-enable-shutdown-disconnect.md)

### <a name="scheduled-time-vs-quota-time"></a>Zamanlanan saat ile kota süresi

[Zamanlanan saat](classroom-labs-concepts.md#schedules) ve [Kota süresini](classroom-labs-concepts.md#quota) anlamak, bir laboratuvarı, Mesleme ve öğrencilerin ihtiyaçlarını daha iyi karşılayacak şekilde yapılandırmanıza yardımcı olur. 

Zamanlanan saat, tüm öğrenci VM 'lerinin başlatıldığı ve bağlantı için kullanılabildiği bir zaman kümesidir. Zamanlanan saat genellikle tüm öğrenciler kendi sanal makinelerine sahip olduğunda ve gün içinde bir ayarlanan süre (sınıf saatleri gibi) için professlı 'in yönlerini izlediğinin yaygın olarak kullanılır. Bu durumda, bir öğrenciye bir VM 'de oturum açmasa bile, tüm öğrenci VM 'lerinin başlatıldığı ve ücretden tahakkuk etmemelerdir. 

Kota süresi her öğrenciye kullanım için ayrılır ve genellikle bağımsız bir studying için kullanılır. Sanal makineler, öğrenciye başlamadan önce başlatılmaz. 

Laboratuvar, kota süresi veya zamanlanan zaman ya da her ikisinin birleşimini kullanabilir. Bir sınıfa zamanlanan süre gerekmiyorsa, VM 'lerin en etkili kullanımı için yalnızca kota süresi kullanın.

### <a name="scheduled-event-stop-only"></a>Zamanlanan olay: yalnızca durdur

Zamanlamada, belirli bir zamanda tüm makineleri durduracak bir yalnızca durdurma olay türü ekleyebilirsiniz. Bazı laboratuvar sahipleri, bir öğrenci kullanmakta oldukları VM 'yi kapatmayı unutursa maliyet ve kota kullanımını azaltmak için her gün için yalnızca bir durdurma olayı ayarladı. Bu olay türünün dezavantajı, bir öğrenci VM kullanıyor olsa bile tüm VM 'Lerin kapatılmasını sağlar.

### <a name="other-costs-related-to-labs"></a>Laboratuvarlarla ilgili diğer maliyetler 

Bazı maliyetler laboratuvar hizmetlerine alınmaz, ancak bir laboratuvar hizmetine bağlanabilir. Paylaşılan bir görüntü galerisini laboratuvara bağlayabilirsiniz, ancak Laboratuvar Hizmetleri maliyetlerinde gösterilmez ve maliyetleri vardır. Genel maliyetleri daha düşük tutmaya yardımcı olmak için, görüntülerin devralınan bir depolama maliyeti olduğundan, kullanılmayan görüntüleri Galeriden kaldırmanız gerekir. 

Laboratuvarların bir sanal ağ üzerinden diğer Azure kaynaklarıyla bağlantıları olabilir. Bir laboratuvar kaldırıldığında, sanal ağı ve diğer kaynakları kaldırmanız gerekir.

## <a name="conclusion"></a>Sonuç

Bu makaledeki bilgiler, kullanım maliyetlerini azaltmanıza yardımcı olabilecek araçlar hakkında daha iyi bir anlayışa vermiş.