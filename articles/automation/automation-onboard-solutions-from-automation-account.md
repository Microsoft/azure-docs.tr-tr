---
title: Azure Otomasyonu 'nda Güncelleştirme Yönetimi, Değişiklik İzleme ve envanter çözümlerini nasıl ekleyeceğinizi öğrenin
description: Azure Otomasyonu 'nun bir parçası olan Güncelleştirme Yönetimi, Değişiklik İzleme ve envanter çözümleriyle Azure sanal makinesini nasıl ekleyeceğinizi öğrenin
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 4/11/2019
ms.topic: conceptual
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: e117c6f8aa8526392678f37a05ec61b55983a1c7
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374433"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Güncelleştirme Yönetimi, Değişiklik İzleme ve envanter çözümlerini ekleme

Azure Otomasyonu, işletim sistemi güvenlik güncelleştirmelerini yönetmek, değişiklikleri izlemek ve bilgisayarlarınıza nelerin yüklü olduğunu envantere almak için çözümler sağlar. Makineleri eklemek için birçok yol vardır, çözümü [bir sanal makineden](automation-onboard-solutions-from-vm.md), [birden çok makineye göz atarak](automation-onboard-solutions-from-browse.md), Otomasyon hesabınızdan veya [runbook](automation-onboard-solutions.md)'a ekleyebilirsiniz. Bu makalede Otomasyon hesabınızdan bu çözümlerin eklenmesi ele alınmaktadır.

## <a name="sign-in-to-azure"></a>Azure'da oturum açın

https://portal.azure.com adresinden Azure'da oturum açın

## <a name="enable-solutions"></a>Çözümleri etkinleştirme

Otomasyon hesabınıza gidin ve **yapılandırma yönetimi**altında **Envanter** veya **değişiklik izleme** ' yi seçin.

Log Analytics çalışma alanını ve otomasyon hesabını seçin ve çözümü etkinleştirmek için **Etkinleştir** ' e tıklayın. Çözümün etkinleştirilmesi 15 dakika sürer.

![Envanter çözümü ekleme](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

> [!NOTE]
> Çözümleri etkinleştirirken Log Analytics çalışma alanı ile Otomasyon Hesabı arasında bağlantı kurma seçeneği yalnızca belirli bölgelerde desteklenmektedir.
>
> Desteklenen eşleme çiftlerinin bir listesi için bkz. [Otomasyon hesabı ve Log Analytics çalışma alanı Için bölge eşleme](how-to/region-mappings.md).

Değişiklik İzleme ve Sayım çözümü, sanal makinelerinizde [değişiklikleri izleme](automation-vm-change-tracking.md) ve [sayım](automation-vm-inventory.md) olanağı sağlar. Bu adımda çözümü bir sanal makine üzerinde etkinleştirirsiniz.

Değişiklik izleme ve sayım çözümü ekleme bildirimi tamamlandığında **YAPILANDIRMA YÖNETİMİ** bölümünde **Güncelleştirme Yönetimi**’ne tıklayın.

Güncelleştirme Yönetimi çözümü, Azure Windows VM’leriniz için güncelleştirmeleri ve yamaları yönetmenizi sağlar. Kullanılabilir güncelleştirmelerin durumunu değerlendirebilir, gerekli güncelleştirmelerin yüklemesini zamanlayabilir ve güncelleştirmelerin VM’ye başarıyla uygulandığını doğrulamak için dağıtım sonuçlarını gözden geçirebilirsiniz. Bu eylem, VM 'niz için çözümü etkinleştirdi.

**Güncelleştirme**yönetimi altında **güncelleştirme yönetimi** ' ni seçin. Seçilen Log Analytics çalışma alanı, önceki adımda kullanılan çalışma alanıdır. Güncelleştirme yönetimi çözümünü eklemek için **Etkinleştir**’e tıklayın. Çözümün etkinleştirilmesi 15 dakika sürer.

![Güncelleştirme çözümünü ekleme](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Kapsam yapılandırması

Her çözüm, çözümü alan bilgisayarları hedeflemek için çalışma alanında bir kapsam yapılandırması kullanır. Kapsam yapılandırması, çözümün kapsamını belirli bilgisayarlarla sınırlandırmak için kullanılan bir veya daha fazla kayıtlı arama grubudur. Kapsam yapılandırmalarına erişmek için, Otomasyon hesabınızda **ılgılı kaynaklar**altında **çalışma alanı**' nı seçin. Sonra çalışma alanı **VERI kaynakları**altındaki çalışma alanında **kapsam yapılandırması**' nı seçin.

Seçilen çalışma alanında henüz Güncelleştirme Yönetimi veya Değişiklik İzleme Çözümleri yoksa, aşağıdaki kapsam konfigürasyonları oluşturulur:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Seçilen çalışma alanında çözüm zaten varsa, çözüm yeniden dağıtılır ve kapsam yapılandırması buna eklenmez.

## <a name="saved-searches"></a>Kayıtlı aramalar

Bir bilgisayar Güncelleştirme Yönetimi veya Değişiklik İzleme ve envanter çözümlerine eklendiğinde, çalışma alanınızda iki kayıtlı aramadan birine eklenirler. Bu kayıtlı aramalar, bu çözümler için hedeflenen bilgisayarları içeren sorgulardır.

Otomasyon hesabınıza gidin ve **genel**altında **kaydedilmiş aramalar** ' ı seçin. Bu çözümler tarafından kullanılan iki kayıtlı arama aşağıdaki tabloda görülebilir:

|Adı     |Kategori  |Diğer ad  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  Değişiklik izleme dosyanız       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Güncellemeler        | Updates__MicrosoftDefaultComputerGroup         |

Grubu doldurmak için kullanılan sorguyu görüntülemek için kayıtlı arama ' yı seçin. Aşağıdaki görüntüde sorgu ve sonuçları gösterilmektedir:

![Kayıtlı aramalar](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Azure VM 'Leri ekleme

Otomasyon hesabınızdan, **yapılandırma**yönetimi altında **Envanter** veya **değişiklik izleme** ' yi veya **güncelleştirme**yönetimi altında **güncelleştirme yönetimi** ' ni seçin.

**+ Azure VM 'Leri Ekle**' ye tıklayın, listeden bir veya daha fazla VM seçin. Etkinleştirilmemiş sanal makineler kullanılamıyor ve seçilemiyor. Azure VM 'Ler, Otomasyon hesabınızın konumu ne olduğuna bakılmaksızın herhangi bir bölgede bulunabilir. **Etkinleştir güncelleştirme yönetimi** sayfasında **Etkinleştir**' e tıklayın. Bu eylem, seçilen VM 'Leri çözüm için bilgisayar grubu kayıtlı aramasına ekler.

![Azure VM 'lerini etkinleştirin](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Azure olmayan bir makine ekleme

Azure 'da bulunmayan makinelerin el ile eklenmesi gerekir. Otomasyon hesabınızdan, **yapılandırma**yönetimi altında **Envanter** veya **değişiklik izleme** ' yi veya **güncelleştirme**yönetimi altında **güncelleştirme yönetimi** ' ni seçin.

**Azure dışı makine Ekle**' ye tıklayın. Bu eylem, makinenin çözüme raporlamaya başlayabilmesi [için makineye Microsoft Monitoring Agent yükleyip yapılandırma yönergeleriyle](../azure-monitor/platform/log-analytics-agent.md) birlikte yeni bir tarayıcı penceresi açar. Şu anda System Center Operations Manager tarafından yönetilen bir makine oluşturuyorsanız, yeni bir aracı gerekli değildir, çalışma alanı bilgileri mevcut aracıya girilir.

## <a name="onboard-machines-in-the-workspace"></a>Çalışma alanındaki makineleri ekleme

El ile yüklenen makineler veya makineler, çözümün etkinleştirilmesi için Azure Otomasyonu 'na eklenmiş olmalıdır. Otomasyon hesabınızdan, **yapılandırma**yönetimi altında **Envanter** veya **değişiklik izleme** ' yi veya **güncelleştirme**yönetimi altında **güncelleştirme yönetimi** ' ni seçin.

**Makineleri Yönet**' i seçin. Bu eylem, **makineleri Yönet** sayfasını açar. Bu sayfa, çözümü bir dizi makinede, tüm kullanılabilir makinelerde etkinleştirmenizi veya tüm geçerli makineler için çözümü etkinleştirmenizi ve gelecekteki tüm makinelerde etkinleştirmenizi sağlar. Daha önce **tüm mevcut ve gelecekteki makinelerde etkinleştir**seçeneğini belirlediyseniz **makineleri Yönet** düğmesi gri olabilir.

![Kayıtlı aramalar](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Tüm kullanılabilir makineler

Çözümü tüm kullanılabilir makineler için etkinleştirmek üzere **kullanılabilir tüm makinelerde etkinleştir**' i seçin. Bu eylem, tek tek makineleri eklemek için denetimi devre dışı bırakır. Bu görev, raporlayan makinelerin tüm adlarını çalışma alanına kayıtlı bilgisayar grubu arama sorgusuna ekler. Seçildiğinde, bu eylem **makineleri Yönet** düğmesini devre dışı bırakır.

### <a name="all-available-and-future-machines"></a>Tüm kullanılabilir ve gelecekteki makineler

Çözümü tüm kullanılabilir makineler ve gelecekteki makineler için etkinleştirmek üzere **tüm kullanılabilir ve gelecekteki makinelerde etkinleştir**' i seçin. Bu seçenek, kaydedilmiş aramaları ve kapsam yapılandırmasını çalışma alanından siler. Bu eylem, çözümü çalışma alanına raporlama yapan tüm Azure ve Azure dışı makinelere açar. Seçildiğinde, bu eylem, hiçbir kapsam yapılandırması bulunmadığından **makineleri Yönet** düğmesini kalıcı olarak devre dışı bırakır.

İlk kaydedilen aramaları geri ekleyerek kapsam yapılandırma işlemleri geri eklenebilir. Daha fazla bilgi için bkz. [kayıtlı aramalar](#saved-searches).

### <a name="selected-machines"></a>Seçilen makineler

Çözümü bir veya daha fazla makineye etkinleştirmek için **Seçili makinelerde etkinleştir** ' i seçin ve çözüme eklemek istediğiniz her makinenin yanındaki **Ekle** ' ye tıklayın. Bu görev, seçilen makine adlarını çözüme ait kayıtlı bilgisayar grubu arama sorgusuna ekler.

## <a name="unlink-workspace"></a>Çalışma alanının bağlantısını kaldır

Aşağıdaki çözümler bir Log Analytics çalışma alanına bağımlıdır:

* [Güncelleştirme yönetimi](automation-update-management.md)
* [Değişiklik İzleme](automation-change-tracking.md)
* [VM'leri çalışma saatleri dışında başlat/durdur](automation-solution-vm-management.md)

Artık Otomasyon hesabınızı bir Log Analytics çalışma alanıyla tümleştirmenize karar verirseniz, Hesabınızın bağlantısını doğrudan Azure portal kaldırabilirsiniz.  Devam etmeden önce önce bahsedilen çözümleri kaldırmanız gerekir, aksi takdirde bu işlemin devam etmesini engellenemez. Kaldırmak için gereken adımları anlamak için, içeri aktardığınız belirli çözüme yönelik makaleyi gözden geçirin.

Bu çözümleri kaldırdıktan sonra, Otomasyon Hesabınızın bağlantısını kaldırmak için aşağıdaki adımları tamamlayabilirsiniz.

> [!NOTE]
> Azure SQL izleme çözümünün önceki sürümleri de dahil olmak üzere bazı çözümler Otomasyon varlıkları oluşturmuş olabilir ve çalışma alanının bağlantısı kaldırılmadan önce de kaldırılması gerekebilir.

1. Azure portal, Otomasyon hesabınızı açın ve Otomasyon hesabı sayfasında, soldaki **Ilgili kaynaklar** etiketli bölümünde **bağlantılı çalışma alanı** ' nı seçin.

2. Çalışma alanının bağlantısını Kaldır sayfasında, **çalışma alanının bağlantısını kaldır**' a tıklayın.

   ![Çalışma alanının bağlantısını Kaldır sayfası](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   Devam etmek istediğinizi doğrulayan bir istem alacaksınız.

3. Azure Otomasyonu hesap Log Analytics çalışma alanınızın bağlantısını kaldırmayı denediğinde, ilerleme durumunu menüdeki **Bildirimler** bölümünden izleyebilirsiniz.

Güncelleştirme Yönetimi çözümünü kullandıysanız, isteğe bağlı olarak, çözümü kaldırdıktan sonra artık gerekli olmayan aşağıdaki öğeleri kaldırmak isteyebilirsiniz.

* Zamanlamayı Güncelleştir-her birinin, oluşturduğunuz güncelleştirme dağıtımlarıyla eşleşen adları olur)

* Çözüm için oluşturulan karma çalışan grupları-her biri, machine1. contoso. com _9ceb8108-26c9-4051-B6B3-227600d715c8 ' e benzer şekilde adlandırılır.

VM 'Leri çalışma saatleri dışında Başlat ve Durdur çözümünü kullandıysanız, isteğe bağlı olarak, çözümü kaldırdıktan sonra artık gerekli olmayan aşağıdaki öğeleri kaldırmak isteyebilirsiniz.

* VM runbook zamanlamalarını başlatma ve durdurma
* VM runbook 'larını başlatma ve durdurma
* Değişkenler

Alternatif olarak, Log Analytics çalışma alanınızdan Otomasyon hesabınızdan çalışma alanınızın bağlantısını da kaldırabilirsiniz. Çalışma alanınızda **Ilgili kaynaklar**altında **Otomasyon hesabı** ' nı seçin. Otomasyon hesabı sayfasında **Hesap bağlantısını kaldır**' ı seçin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sanal makineyi Güncelleştirme Yönetimi kaldırmak için:

* Log Analytics çalışma alanınızda, kapsam yapılandırması için kayıtlı aramadan VM 'yi kaldırın `MicrosoftDefaultScopeConfig-Updates`. Kayıtlı aramalar, çalışma alanınızda **genel** altında bulunabilir.
* Linux için [Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) 'ı veya [Log Analytics aracısını](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources)kaldırın.

## <a name="next-steps"></a>Sonraki adımlar

Nasıl kullanacağınızı öğrenmek için çözümlerle ilgili öğreticilere geçin.

* [Öğretici-VM 'niz için güncelleştirmeleri yönetme](automation-tutorial-update-management.md)

* [Öğretici-bir VM 'de yazılım tanımla](automation-tutorial-installed-software.md)

* [Öğretici-VM 'deki değişikliklerle Ilgili sorunları giderme](automation-tutorial-troubleshoot-changes.md)
