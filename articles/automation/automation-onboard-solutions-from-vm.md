---
title: Azure VM 'den Güncelleştirme Yönetimi, Değişiklik İzleme ve envanter çözümlerini ekleme
description: Azure Otomasyonu 'nun bir parçası olan Güncelleştirme Yönetimi, Değişiklik İzleme ve envanter çözümleriyle Azure sanal makinesini nasıl ekleyeceğinizi öğrenin.
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 0069d2e8ccd3b4f65ced8b6e18ce568689f81e14
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374418"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Azure sanal makinesinden Güncelleştirme Yönetimi, Değişiklik İzleme ve envanter çözümlerini ekleme

Azure Otomasyonu, işletim sistemi güvenlik güncelleştirmelerini yönetmenize, değişiklikleri izlemenize ve bilgisayarlarınıza nelerin yüklü olduğunu izlemenize yardımcı olan çözümler sunar. Makineleri eklemek için birden çok yol vardır. Çözümü bir sanal makineden, [Otomasyon hesabınızdan](automation-onboard-solutions-from-automation-account.md), [birden çok makineye göz atarak](automation-onboard-solutions-from-browse.md)veya bir [runbook](automation-onboard-solutions.md)kullanarak ekleyebilirsiniz. Bu makalede, Azure sanal makinesinden bu çözümlerin eklenmesi ele alınmaktadır.

## <a name="sign-in-to-azure"></a>Azure'da oturum açın

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="enable-the-solutions"></a>Çözümleri etkinleştirme

Var olan bir sanal makineye gidin. **İşlemler**altında **güncelleştirme yönetimi**, **Envanter**veya **değişiklik izleme**' yi seçin. Otomasyon hesabınızın konumu ne olduğuna bakılmaksızın, sanal makine herhangi bir bölgede bulunabilir. Bir VM 'den bir çözüm eklerken, sanal makinenin bir çalışma alanına eklendi olup olmadığını anlamak için `Microsoft.OperationalInsights/workspaces/read` izninizin olması gerekir. Genel olarak gereken ek izinler hakkında daha fazla bilgi edinmek için bkz. [makineler eklemek için gereken izinler](automation-role-based-access-control.md#onboarding).

Yalnızca VM için çözümü etkinleştirmek üzere **Bu VM Için etkinleştir** ' in seçili olduğundan emin olun. Çözüme birden çok makine eklemek için **Bu abonelikteki VM 'ler Için etkinleştir**' i seçin ve ardından **etkinleştirilecek makineleri seçmek Için tıklayın ' ı**seçin. Aynı anda birden çok makine ekleme hakkında bilgi edinmek için bkz. [güncelleştirme yönetimi, değişiklik izleme ve envanter çözümlerini](automation-onboard-solutions-from-automation-account.md)ekleme.

Azure Log Analytics çalışma alanını ve otomasyon hesabını seçin ve ardından çözümü etkinleştirmek için **Etkinleştir** ' i seçin. Çözümün etkinleştirilmesi 15 dakika sürer.

![Güncelleştirme Yönetimi çözümü ekleme](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Diğer çözümlere gidin ve **Etkinleştir**' i seçin. Log Analytics çalışma alanı ve Otomasyon hesabı açılır listeleri devre dışıdır çünkü bu çözümler, önceden etkinleştirilmiş çözümle aynı çalışma alanını ve otomasyon hesabını kullanır.

> [!NOTE]
> **Değişiklik izleme** ve **Stok** aynı çözümü kullanır. Bu çözümlerden biri etkinleştirildiğinde, diğeri de etkinleştirilir.

## <a name="scope-configuration"></a>Kapsam yapılandırması

Her çözüm, çözümü alan bilgisayarları hedeflemek için çalışma alanında bir kapsam yapılandırması kullanır. Kapsam yapılandırması, çözümün kapsamını belirli bilgisayarlarla sınırlandırmak için kullanılan bir veya daha fazla kayıtlı arama grubudur. Kapsam yapılandırmalarına erişmek için, Otomasyon hesabınızda, **ılgılı kaynaklar**altında, **çalışma alanı**' nı seçin. Çalışma alanında, **çalışma alanı VERI kaynakları**altında **kapsam yapılandırması**' nı seçin.

Seçilen çalışma alanında Güncelleştirme Yönetimi veya Değişiklik İzleme Çözümleri yoksa, aşağıdaki kapsam konfigürasyonları oluşturulur:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Seçilen çalışma alanında çözüm zaten varsa, çözüm yeniden dağıtılır ve kapsam yapılandırması eklenmez.

Yapılandırmaların herhangi birinde üç nokta ( **...** ) simgesini seçin ve ardından **Düzenle**' yi seçin. **Kapsam yapılandırmasını düzenle** bölmesinde **bilgisayar gruplarını Seç**' i seçin. **Bilgisayar grupları** bölmesi, kapsam yapılandırmasını oluşturmak için kullanılan kaydedilmiş aramaları gösterir.

## <a name="saved-searches"></a>Kayıtlı aramalar

Güncelleştirme Yönetimi, Değişiklik İzleme veya envanter çözümlerine bir bilgisayar eklendiğinde, bilgisayar çalışma alanınızdaki iki kayıtlı aramadan birine eklenir. Kayıtlı aramalar, bu çözümler için hedeflenen bilgisayarları içeren sorgulardır.

Çalışma alanınıza gidin. **Genel**altında **kaydedilmiş aramalar**' ı seçin. Bu çözümler tarafından kullanılan iki kayıtlı arama aşağıdaki tabloda gösterilmiştir:

|Adı     |Kategori  |Diğer ad  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  Değişiklik izleme dosyanız       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Güncellemeler        | Updates__MicrosoftDefaultComputerGroup         |

Grubu doldurmak için kullanılan sorguyu görüntülemek için kayıtlı aramalardan birini seçin. Aşağıdaki görüntüde sorgu ve sonuçları gösterilmektedir:

![Kayıtlı aramalar](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Çalışma alanının bağlantısını kaldır

Aşağıdaki çözümler bir Log Analytics çalışma alanına bağımlıdır:

* [Güncelleştirme yönetimi](automation-update-management.md)
* [Değişiklik İzleme](automation-change-tracking.md)
* [VM'leri çalışma saatleri dışında başlat/durdur](automation-solution-vm-management.md)

Artık Otomasyon hesabınızı bir Log Analytics çalışma alanıyla tümleştirmenize karar verirseniz, Hesabınızın bağlantısını doğrudan Azure portal kaldırabilirsiniz.  Devam etmeden önce önce bahsedilen çözümleri kaldırmanız gerekir, aksi takdirde bu işlemin devam etmesini engellenemez. Kaldırmak için gereken adımları anlamak için, içeri aktardığınız belirli çözüme yönelik makaleyi gözden geçirin.

Bu çözümleri kaldırdıktan sonra, Otomasyon Hesabınızın bağlantısını kaldırmak için aşağıdaki adımları uygulayabilirsiniz.

> [!NOTE]
> Azure SQL izleme çözümünün önceki sürümleri de dahil olmak üzere bazı çözümler Otomasyon varlıkları oluşturmuş olabilir ve çalışma alanının bağlantısı kaldırılmadan önce de kaldırılması gerekebilir.

1. Azure portal Otomasyon hesabınızı açın ve Otomasyon hesabı sayfasında, sol taraftaki **Ilgili kaynaklar** bölümünde **bağlantılı çalışma alanı** ' nı seçin.

2. Çalışma alanının bağlantısını Kaldır sayfasında, **çalışma alanının bağlantısını kaldır**' a tıklayın.

   ![Çalışma alanının bağlantısını Kaldır sayfası](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   Devam etmek istediğinizi doğrulayan bir ileti alacaksınız.

3. Azure Otomasyonu, Log Analytics çalışma alanınızdaki hesabın bağlantısını kaldırmayı denediğinde, ilerleme durumunu menüdeki **Bildirimler** bölümünden izleyebilirsiniz.

Güncelleştirme Yönetimi çözümünü kullandıysanız, isteğe bağlı olarak, çözümü kaldırdıktan sonra artık gerekli olmayan aşağıdaki öğeleri kaldırmak isteyebilirsiniz.

* Zamanlamayı Güncelleştir-her birinin, oluşturduğunuz güncelleştirme dağıtımlarıyla eşleşen adları olur)

* Çözüm için oluşturulan karma çalışan grupları-her biri, machine1. contoso. com _9ceb8108-26c9-4051-B6B3-227600d715c8 ' e benzer şekilde adlandırılır.

VM'leri çalışma saatleri dışında başlat/durdur çözümünü kullandıysanız, isteğe bağlı olarak, çözümü kaldırdıktan sonra artık gerekli olmayan aşağıdaki öğeleri kaldırmak isteyebilirsiniz.

* VM runbook zamanlamalarını başlatma ve durdurma
* VM runbook 'larını başlatma ve durdurma
* Değişkenler

Alternatif olarak, Log Analytics çalışma alanınızdan Otomasyon hesabınızdan çalışma alanınızın bağlantısını da kaldırabilirsiniz. Çalışma alanınızda **Ilgili kaynaklar**altında **Otomasyon hesabı** ' nı seçin. Otomasyon hesabı sayfasında **Hesap bağlantısını kaldır**' ı seçin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sanal makineyi Güncelleştirme Yönetimi kaldırmak için:

* Log Analytics çalışma alanınızda, kapsam yapılandırması için kayıtlı aramadan VM 'yi kaldırın `MicrosoftDefaultScopeConfig-Updates`. Kayıtlı aramalar, çalışma alanınızda **genel** altında bulunabilir.
* Linux için [Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) 'ı veya [Log Analytics aracısını](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources)kaldırın.

## <a name="next-steps"></a>Sonraki adımlar

Bunları nasıl kullanacağınızı öğrenmek için çözümler için öğreticilere geçin:

* [Öğretici-VM 'niz için güncelleştirmeleri yönetme](automation-tutorial-update-management.md)
* [Öğretici-bir VM 'de yazılım tanımla](automation-tutorial-installed-software.md)
* [Öğretici-VM 'deki değişikliklerle Ilgili sorunları giderme](automation-tutorial-troubleshoot-changes.md)
