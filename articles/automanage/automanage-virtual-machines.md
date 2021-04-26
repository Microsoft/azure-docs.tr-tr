---
title: Sanal makineler için Azure oto yönetimi
description: Sanal makineler için Azure oto yönetimi hakkında bilgi edinin.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 42b469963761ae1ffc736c719487ee93c3a1c149
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863128"
---
# <a name="azure-automanage-for-virtual-machines"></a>Sanal makineler için Azure oto yönetimi

Bu makalede, aşağıdaki avantajları içeren sanal makineler için Azure oto yönetimi hakkında bilgiler yer almaktadır:

- Sanal makineleri en iyi uygulamalar için Azure hizmetleri 'ni seçme konusunda akıllıca onpanolar
- Her Azure en iyi uygulama başına hizmeti otomatik olarak yapılandırır
- Değişikliklerini için izler ve algılandığında düzeltme için düzeltir
- Basit bir deneyim (işaret, tıklama, ayarlama, unutma) sağlar


## <a name="overview"></a>Genel Bakış

Sanal makineler için Azure oto yönetimi, Azure 'da sanal makinenize faydalanabilir belirli hizmetlerin nasıl ekleneceğini ve nasıl yapılandırılacağını öğrenmenizi sağlayan bir hizmettir. Bu hizmetler, Azure 'un en iyi yöntem Hizmetleri olduğu kabul edilir ve sanal makineler için güvenilirliği, güvenliği ve yönetimi geliştirmeye yardımcı olur. Örnek Hizmetler [Azure güncelleştirme yönetimi](../automation/update-management/overview.md) ve [Azure Backup](../backup/backup-overview.md)içerir.

Sanal makinelerinizi Azure otomatik Yönet 'e ekledikten sonra, her bir en iyi yöntem hizmeti önerilen ayarlara göre yapılandırılmıştır. En iyi uygulamalar, hizmetlerin her biri için farklıdır. En iyi uygulama, her gün bir kez sanal makineyi yedeklemek ve altı aylık bir bekletme dönemi varsa, bu örnek Azure Backup olabilir.

Ayrıca, Azure otomatik yönetimi de DRFT için otomatik olarak izler ve algılandığında düzeltme için düzeltir. Bunun anlamı, sanal makineniz Azure 'u yeniden yönetmek için eklendi, bunu yalnızca Azure 'un en iyi yöntemleri uyarınca yapılandıracağız, ancak kendi yaşam döngüsünün tamamında bu en iyi uygulamalarla uyumlu olmaya devam ettiğinden emin olmak için makinenizi izliyoruz. Sanal makineniz bu uygulamalardan daha fazla veya daha fazla olursa (örneğin, bir hizmet offboarded ise) bunu düzeltecektir ve makinenizi istenen duruma geri çekeceğiz.

## <a name="prerequisites"></a>Önkoşullar

Sanal makinelerinizde Azure oto yönetimi 'ni etkinleştirmeyi denemeden önce göz önünde bulundurmanız gereken birkaç önkoşul vardır.

- Desteklenen [Windows Server sürümleri](automanage-windows-server.md#supported-windows-server-versions) ve [Linux kaldırmalar](automanage-linux.md#supported-linux-distributions-and-versions)
- VM 'Ler desteklenen bir bölgede olmalıdır (aşağıya bakın)
- Kullanıcı doğru izinlere sahip olmalıdır (aşağıya bakın)
- Oto yönetimi şu anda korumalı alan aboneliklerini desteklemiyor

### <a name="supported-regions"></a>Desteklenen bölgeler
Oto yönetimi yalnızca aşağıdaki bölgelerde bulunan VM 'Leri destekler:
* West Europe
* Kuzey Avrupa
* Central US
* Doğu ABD
* Doğu ABD 2
* Batı ABD
* Batı ABD 2
* Orta Kanada
* Orta Batı ABD
* Orta Güney ABD
* Doğu Japonya
* Güney Birleşik Krallık
* AU Doğu
* AU Güneydoğu
* Güneydoğu Asya

### <a name="required-rbac-permissions"></a>Gerekli RBAC izinleri
Hesabınız, yeni bir oto Yönet hesabıyla, oto yönetimi etkinleştirip etkinleştirdiğinize bağlı olarak biraz farklı RBAC rolü gerektirir.

Yeni bir oto Yönet hesabıyla, oto Yönet 'i etkinleştirirseniz:
* VM 'lerinizi içeren abonelikler üzerinde **sahip** rolü _**veya**_
* VM 'lerinizi içeren **abonelikler ve** **Kullanıcı erişimi yönetici** rolleri

Var olan bir oto Yönet hesabıyla, oto Yönet 'i etkinleştirirseniz:
* VM 'lerinizi içeren kaynak grubundaki **katkıda bulunan** rolü

Oto Yönet hesabına, tekrar yönetilen makinelerde eylemler gerçekleştirmek için **katkıda bulunan** ve **kaynak ilkesi katılımcısı** izinleri verilecektir.

> [!NOTE]
> Farklı bir abonelikte bulunan bir çalışma alanına bağlı bir sanal makinede, oto yönetimi 'ni kullanmak istiyorsanız, her bir abonelikte yukarıda açıklanan izinlere sahip olmanız gerekir.

## <a name="participating-services"></a>Katılım Hizmetleri

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services-1.png" alt-text="Hizmetleri akıllıca ekleyin.":::

Katılımcı Azure hizmetlerinin yanı sıra desteklenen ortamları ve bunların tüm listesi için aşağıdakilere bakın:
- [Linux için oto Yönet](automanage-linux.md)
- [Windows Server için oto Yönet](automanage-windows-server.md)

 Sizi bu katılım hizmetlerine otomatik olarak ekleyeceğiz. Bunlar, [bulut benimseme çerçevesinden](/azure/cloud-adoption-framework/manage/azure-server-management)bulabileceğiniz en iyi yöntemler teknik incelemesi için önemlidir.

Bu hizmetlerin tümü için otomatik olarak kullanılacak, otomatik olarak yapılandıracağız, aracılık for drfor, ve değişikliklerini algılanırsa.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Azure portal VM 'Ler için oto yönetimi etkinleştiriliyor

Azure portal, var olan bir sanal makinede ya da yeni bir sanal makine oluştururken, oto yönetimini etkinleştirebilirsiniz. Bu işlemin kısa adımları için, [sanal makineler Için oto yönetimi hızlı](quick-create-virtual-machines-portal.md)başlangıcı ' na göz atın.

SANAL makineniz için Automanage 'u ilk kez etkinleştirdiğinizde, **automanage: Azure sanal makine en iyi yöntemleri** için Azure Portal araması yapabilirsiniz. **Mevcut VM 'de etkinleştir**' e tıklayın, eklemek Istediğiniz VM 'leri seçin, **Seç**' e tıklayın, **Etkinleştir**' e tıklayın ve işiniz bitti demektir.

Bu hizmetleri yönetmek için bu VM ile etkileşimde bulunabilmeniz gereken tek zaman, sanal makinenizin düzeltilmesi için denediğimiz olaydır, ancak bunu yapamadı. VM 'nizi başarılı bir şekilde düzeltmemiz durumunda sizi uyarmadan yine de uyumluluğa geri getirilecektir. Daha fazla ayrıntı için bkz. [VM 'Lerin durumu](#status-of-vms).

## <a name="enabling-automanage-for-vms-using-azure-policy"></a>Azure Ilkesi kullanarak VM 'Ler için oto yönetimini etkinleştirme
Ayrıca, yerleşik Azure Ilkesini kullanarak VM 'lerde ölçek üzerinde de oto yönetimi etkinleştirebilirsiniz. İlkede bir DeployIfNotExists efekti vardır. Bu, ilke kapsamındaki tüm uygun sanal makinelerin otomatik olarak VM En Iyi uygulamalarını otomatik olarak yönetmesi için eklendi.

İlkeye doğrudan bağlantı [burada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3)bulunur.

### <a name="how-to-apply-the-policy"></a>İlkeyi uygulama
1. İlke tanımını görüntülerken **ata** düğmesine tıklayın
1. İlkeyi uygulamak istediğiniz kapsamı seçin (Yönetim grubu, abonelik veya kaynak grubu olabilir)
1. **Parametreler** altında, oto Yönet hesabı, yapılandırma profili ve efekt için parametreleri belirtin (etki genellikle DeployIfNotExists olmalıdır)
    1. Bir oto Yönet hesabınız yoksa, [oluşturmanız](./automanage-account.md)gerekir.
1. **Düzeltme** altında "bir düzeltme görevini tıklatın" onay kutusunu işaretleyin. Bu, oto yönetimi için ekleme gerçekleştirecek.
1. **Gözden geçir + oluştur** ' a tıklayın ve tüm ayarların iyi görünmesini sağlayın.
1. **Oluştur**’a tıklayın.

## <a name="environment-configuration"></a>Ortam yapılandırması

Sanal makineniz için oto yönetimi etkinleştirirken bir ortam gerekir. Ortamlar bu hizmetin temelidir. Bunlar, makinelerinizi hangi hizmetlere sunduğumuz, bu hizmetlerin yapılandırmasının ne olacağı hakkında bir ölçüde tanımlar.

### <a name="default-environments"></a>Varsayılan ortamlar

Şu anda kullanılabilen iki ortam vardır.

- **Geliştirme** ve test ortamı geliştirme ve test makineleri için tasarlanmıştır.
- **Üretim ortamı üretime** yöneliktir.

Bu farklıın nedeni, çalışan iş yüküne bağlı olarak belirli hizmetlerin önerilmeidir. Örneğin, bir üretim makinesinde sizi Azure Backup için otomatik olarak ekleyeceğiz. Ancak, geliştirme/test makineleri genellikle daha düşük iş etkisi olduğundan, bir geliştirme ve test makinesi için bir yedekleme hizmeti gereksiz bir maliyet olacaktır.

### <a name="customizing-an-environment-using-preferences"></a>Tercihleri kullanarak bir ortamı özelleştirme

Size eklediğimiz standart hizmetlere ek olarak, belirli bir tercih alt kümesini yapılandırmanıza izin veririz. Bu tercihlere bir dizi yapılandırma seçeneği içinde izin verilir. Örneğin, Azure Backup olması durumunda yedeklemenin sıklığını ve haftanın hangi gününde olduğunu tanımlamanızı sağlayacak.

> [!NOTE]
> Geliştirme/test ortamında VM 'yi hiç yedekleyemeyecektir.

Tercihler aracılığıyla varsayılan bir ortamın ayarlarını yapabilirsiniz. [Burada](virtual-machines-custom-preferences.md)bir tercih oluşturma hakkında bilgi edinin.

> [!NOTE]
> Oto yönetimi etkinken sanal makinenizin enivonrment yapılandırmasını değiştiremezsiniz. Bu VM için, bu sanal makine için yeniden yönetme 'yi devre dışı bırakmanız ve ardından, istenen ortam ve tercihlerle tekrar yönetmeyi yeniden etkinleştirmeniz gerekir.

Katılan Azure hizmetlerinin tüm listesi ve tercihleri destekliyorsa, buraya bakın:
- [Linux için oto Yönet](automanage-windows-server.md)
- [Windows Server için oto Yönet](automanage-windows-server.md)


## <a name="automanage-account"></a>Hesabı oto Yönet

Otomatikmanage hesabı, güvenlik bağlamına veya otomatik işlemlerin oluştuğu kimliğe sahiptir. Genellikle, hesabı otomatik Yönet seçeneği gereksizdir, ancak kaynaklarınızın otomatik yönetimini bölmek istediğiniz bir yetkilendirme senaryosu varsa (Belki iki sistem yöneticisi arasında), etkinleştirme akışındaki hesabı otomatik Yönet seçeneği bu yöneticilerin her biri için bir Azure kimliği tanımlamanızı sağlar.

Oto Yönet hesabı ve bir hesap oluşturma hakkında daha fazla bilgi edinmek için, [hesabı oto Yönet belgesini](./automanage-account.md)ziyaret edin.

## <a name="status-of-vms"></a>VM 'lerin durumu

Azure portal, otomatik olarak yönetilen tüm sanal **makinelerinizi listeleyen otomatik Yönet – Azure sanal makine en iyi uygulamaları** sayfasına gidin. Burada her bir sanal makinenin genel durumunu görürsünüz.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="Yapılandırılmış sanal makinelerin listesi.":::

Listelenen her VM için şu ayrıntılar görüntülenir: ad, ortam, yapılandırma tercihi, durum, Işletim sistemi, hesap, abonelik ve kaynak grubu.

**Durum** sütununda aşağıdaki durumlar görüntülenebilir:
- *Devam eden* -VM yeni etkinleştirildi ve yapılandırılıyor
- *Yapılandırıldı* -VM yapılandırıldı ve bir DRT algılanmadı
- *Başarısız* -VM 'de düzeltebilecekler ve düzeltilemedi
- *Bekliyor* -VM Şu anda çalışmıyor ve bir sonraki çalıştırıldığında, yeniden Yönet işlemi VM 'yi ekleme veya düzeltme girişiminde bulunur

**Durumu** *başarısız* olarak görürseniz, sanal makinenizin bulunduğu kaynak grubu aracılığıyla dağıtımda sorun giderebilirsiniz. **Kaynak grupları**' na gidin, kaynak grubunuzu seçin, **dağıtımlar** ' a tıklayın ve hata ayrıntılarıyla birlikte *başarısız* durumuna bakın.


## <a name="disabling-automanage-for-vms"></a>VM 'Ler için oto yönetimini devre dışı bırakma

Belirli sanal makinelerde, tek bir güne kadar her gün tekrar yönetmeyi seçebilirsiniz. Örneğin, makineniz bazı süper duyarlı güvenli iş yükünü çalıştırıyor ve Azure 'dan daha da çok daha fazla işlem yapmanız gerekir. bu nedenle, makineyi Azure 'un en iyi yöntemleri dışında yapılandırmanız gerekir.

Azure portal bu işlemleri yapmak için otomatik olarak yönetilen tüm sanal **makinelerinizi listeleyen otomatik Yönet – Azure sanal makine en iyi uygulamaları** sayfasına gidin. Tekrar Yönet ' e tıklayarak devre dışı bırakmak istediğiniz sanal makinenin yanındaki onay kutusunu işaretleyin ve ardından yeniden **Aç düğmesine tıklayın** .

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Bir sanal makinede oto yönetimi devre dışı bırakılıyor.":::

Kabul etmiş önce **devre dışı bırakmak** için ortaya çıkan açılan pencerede iletiyi dikkatle okuyun.

> [!NOTE]
> Bir VM 'de oto yönetimini devre dışı bırakmak aşağıdaki davranışa neden olur:
>
> - VM 'nin yapılandırması ve bu hizmetin değişeklendi.
> - Bu hizmetler tarafından tahakkuk eden ücretler faturalandırılabilir ve tahakkuk olmaya devam eder.
> - Oto yönetimi, DRFT izlemeyi hemen durduruyor.


İlk ve daha önce, sanal makineyi eklendi ve yapılandırdığımız hizmetlerden herhangi birinden kurmayacak. Bu nedenle, bu hizmetler tarafından tahakkuk eden ücretler faturalandırılabilir olmaya devam edecektir. Gerekirse Pano 'ya ihtiyacınız olacaktır. Herhangi bir oto yönetimi davranışı hemen durdurulur. Örneğin, artık VM 'yi DRFT için izliyoruz.

## <a name="automanage-and-azure-disk-encryption"></a>Oto yönetimi ve Azure disk şifrelemesi
Oto yönetimi, Azure disk şifrelemesi (ADE) etkin olan VM 'lerle uyumludur.

Üretim ortamını kullanıyorsanız, Azure Backup için de eklendi olmanız gerekir. Başarılı bir şekilde ADE ve Azure Backup kullanımı için bir önkoşul vardır:
* ATA 'nın üretim ortamına, ADE özellikli VM 'nizi eklemeden önce, [Bu belgenin](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption#before-you-start) **başlamadan önce** bölümünde yer alan adımları izlediğinizden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, sanal makineler için oto yönetimi 'nin, en iyi uygulama Azure hizmetlerini bilmeniz, bu makaleye ekleme ve yapılandırma ihtiyacını ortadan kaldırabildiğiniz bir yol sağladığını öğrendiniz. Ayrıca, ortam kurulumundan Drifts sanal makineler için otomatik olarak yönetmek üzere eklendi bir makineniz varsa, otomatik olarak uyumluluk 'e geri getirilecektir.

Azure portal sanal makineler için oto yönetimini etkinleştirmeyi deneyin.

> [!div class="nextstepaction"]
> [Azure portal sanal makineler için oto yönetimini etkinleştir](quick-create-virtual-machines-portal.md)