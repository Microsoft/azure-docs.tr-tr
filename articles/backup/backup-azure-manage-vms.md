---
title: Azure VM yedeklemelerini yönetme ve izleme
description: Azure Backup hizmetini kullanarak Azure VM yedeklemelerini yönetme ve izleme hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 08/02/2020
ms.openlocfilehash: 51ce88bb67d64ce129a3479d38db9a66dfe65d0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100635086"
---
# <a name="manage-azure-vm-backups-with-azure-backup-service"></a>Azure Backup hizmeti ile Azure VM yedeklemelerini yönetme

Bu makalede, [Azure Backup hizmetiyle](backup-overview.md)yedeklenen Azure sanal makinelerinin (VM) nasıl yönetileceği açıklanmaktadır. Makale Ayrıca, kasa panosunda bulabileceğiniz yedekleme bilgilerini de özetler.

Azure portal, kurtarma hizmetleri Kasası panosu, aşağıdakiler de dahil olmak üzere kasa bilgilerine erişim sağlar:

* En son geri yükleme noktası olan en son yedekleme.
* Yedekleme ilkesi.
* Tüm yedekleme anlık görüntülerinin toplam boyutu.
* Yedeklemeler için etkinleştirilen sanal makinelerin sayısı.

, Panoyu kullanarak ve tek tek VM 'lere giderek, yedeklemeleri yönetebilirsiniz. Makine yedeklemelerini başlatmak için panoda kasayı açın.

![Kaydırıcı ile tam pano görünümü](./media/backup-azure-manage-vms/bottom-slider.png)

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="view-vms-on-the-dashboard"></a>Panodaki VM 'Leri görüntüleme

Kasa panosunda VM 'Leri görüntülemek için:

1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. Sol taraftaki menüden **Tüm hizmetler**'i seçin.

    ![Tüm Hizmetler’i seçin](./media/backup-azure-manage-vms/select-all-services.png)

1. **Tüm hizmetler** iletişim kutusuna *Kurtarma Hizmetleri* yazın. Kaynak listesi, yazdıklarınıza göre filtrelenir. Kaynak listesinde **Kurtarma Hizmetleri kasaları**'nı seçin.

    ![Kurtarma Hizmetleri kasaları yazın ve seçin](./media/backup-azure-manage-vms/all-services.png)

    Abonelikteki Kurtarma Hizmetleri kasalarının listesi görünür.

1. Kullanım kolaylığı için, kasa adınızın yanındaki PIN simgesini seçin ve **panoya sabitle**' yi seçin.
1. Kasa panosunu açın.

    ![Kasa panosu ve ayarlar bölmesini açın](./media/backup-azure-manage-vms/full-view-rs-vault.png)

1. **Yedekleme öğeleri** kutucuğunda **Azure sanal makine**' yi seçin.

    ![Yedekleme öğeleri kutucuğunu aç](./media/backup-azure-manage-vms/azure-virtual-machine.png)

1. **Yedekleme öğeleri** bölmesinde, korunan VM 'lerin listesini görebilirsiniz. Bu örnekte, kasa bir sanal makineyi korur: *myVMR1*.  

    ![Yedekleme öğeleri bölmesini görüntüleme](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

1. Kasa öğesinin panosundan, yedekleme ilkelerini değiştirebilir, isteğe bağlı yedekleme çalıştırabilir, sanal makinelerin korumasını durdurabilir veya sürdürebilir, yedekleme verilerini silebilir, geri yükleme noktalarını görüntüleyebilir ve geri yükleme gerçekleştirebilirsiniz.

    ![Yedekleme öğeleri panosu ve ayarlar bölmesi](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>VM için yedekleme ilkesini yönetme

### <a name="modify-backup-policy"></a>Yedekleme ilkesini değiştirme

Var olan bir yedekleme ilkesini değiştirmek için:

1. [Azure portalında](https://portal.azure.com/) oturum açın. Kasa panosunu açın.
2. **> yedekleme Ilkelerini Yönet**' den **Azure sanal makinesi** türü için yedekleme ilkesini seçin.
3. **Değiştir** ' i seçin ve ayarları değiştirin.

### <a name="switch-backup-policy"></a>Yedekleme ilkesini Değiştir

Bir yedekleme ilkesini yönetmek için:

1. [Azure portalında](https://portal.azure.com/) oturum açın. Kasa panosunu açın.
2. **Yedekleme öğeleri** kutucuğunda **Azure sanal makine**' yi seçin.

    ![Yedekleme öğeleri kutucuğunu aç](./media/backup-azure-manage-vms/azure-virtual-machine.png)

3. **Yedekleme öğeleri** bölmesinde, korunan sanal makinelerin listesini ve en son geri yükleme noktası zamanına sahip son yedekleme durumunu görüntüleyebilirsiniz.

    ![Yedekleme öğeleri bölmesini görüntüleme](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. Kasa öğesinin panosundan bir yedekleme ilkesi seçebilirsiniz.

   * İlkeleri değiştirmek için farklı bir ilke seçin ve ardından **Kaydet**' i seçin. Yeni ilke hemen kasaya uygulanır.

     ![Yedekleme ilkesi seçme](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>İsteğe bağlı yedekleme çalıştırma

Korumasını ayarladıktan sonra bir VM 'nin isteğe bağlı yedeklemesini çalıştırabilirsiniz. Bu ayrıntıları göz önünde bulundurun:

* İlk yedekleme beklendiğinde, isteğe bağlı yedekleme, kurtarma hizmetleri kasasında sanal makinenin tam bir kopyasını oluşturur.
* İlk yedekleme işlemi tamamlandıktan sonra, bir isteğe bağlı yedekleme, değişiklikleri yalnızca önceki anlık görüntüden kurtarma hizmetleri kasasına gönderir. Diğer bir deyişle, sonraki yedeklemeler her zaman artımlıdır.
* İsteğe bağlı yedekleme için bekletme aralığı, yedeklemeyi tetiklemeniz durumunda belirttiğiniz saklama değeridir.

> [!NOTE]
> Azure Backup hizmeti, günde en fazla üç isteğe bağlı yedeklemeyi ve zamanlanan bir ek yedeklemeyi destekler.

İsteğe bağlı yedekleme tetiklemesi için:

1. [Kasa öğesi panosunda](#view-vms-on-the-dashboard), **korunan öğe** altında **yedekleme öğesi**' ni seçin.

    ![Şimdi Yedekle seçeneği](./media/backup-azure-manage-vms/backup-now-button.png)

2. **Yedekleme yönetimi türünden** **Azure sanal makinesi**' ni seçin. **Yedekleme öğesi (Azure sanal makine)** bölmesi görüntülenir.
3. Bir VM seçin ve isteğe bağlı yedekleme oluşturmak için **Şimdi Yedekle** ' yi seçin. **Şimdi Yedekle** bölmesi görüntülenir.
4. Yedek alana **sakla** alanında yedeklemenin korunması için bir tarih belirtin.

    ![Şimdi Yedekle takvimi](./media/backup-azure-manage-vms/backup-now-check.png)

5. Yedekleme işini çalıştırmak için **Tamam ' ı** seçin.

İşin ilerlemesini izlemek için kasa panosunda **yedekleme işleri** kutucuğunu seçin.

## <a name="stop-protecting-a-vm"></a>VM korumasını durdurma

Bir VM 'yi korumayı durdurmanın iki yolu vardır:

* **Korumayı durdurun ve yedekleme verilerini koruyun**. Bu seçenek, gelecekteki tüm yedekleme işlerinin VM 'nizi korumasını durduracak. Ancak, Azure Backup hizmet yedeklenen kurtarma noktalarını korur.  Kurtarma noktalarını kasada tutmak için ödeme yapmanız gerekir (Ayrıntılar için [Azure Backup fiyatlandırmasına](https://azure.microsoft.com/pricing/details/backup/) bakın). Gerekirse VM 'yi geri yükleyebileceksiniz. VM korumasını sürdürmeye karar verirseniz, *yedeklemeyi yeniden başlatma* seçeneğini kullanabilirsiniz.
* **Korumayı durdurun ve yedekleme verilerini silin**. Bu seçenek, gelecekteki tüm yedekleme işlerinin VM 'nizi korumasını durdurur ve tüm kurtarma noktalarını siler. VM 'yi geri yükleyemeyeceksiniz ve *yedeklemeyi* yeniden yükle seçeneğini kullanamazsınız.

>[!NOTE]
>Yedeklemeleri durdurmadan bir veri kaynağını silerseniz, yeni yedeklemeler başarısız olur. Eski kurtarma noktaları ilkeye göre sona erer, ancak yedeklemeleri durdurup verileri silene kadar en son kurtarma noktası her zaman tutulur.
>

### <a name="stop-protection-and-retain-backup-data"></a>Korumayı durdurun ve yedekleme verilerini koruyun

Korumayı durdurmak ve bir VM 'nin verilerini sürdürmek için:

1. [Kasa öğesinin panosunda](#view-vms-on-the-dashboard) **Yedeklemeyi Durdur**' u seçin.
2. **Yedekleme verilerini sakla**' yı seçin ve seçiminizi gerektiği şekilde onaylayın. İsterseniz bir açıklama ekleyin. Öğenin adından emin değilseniz, adı görüntülemek için ünlem işaretinin üzerine gelin.

    ![Yedekleme verilerini koruma](./media/backup-azure-manage-vms/retain-backup-data.png)

Bir bildirim, yedekleme işlerinin durdurulduğunu bilmenizi sağlar.

### <a name="stop-protection-and-delete-backup-data"></a>Korumayı Durdur ve yedekleme verilerini sil

Korumayı durdurmak ve bir VM 'nin verilerini silmek için:

1. [Kasa öğesinin panosunda](#view-vms-on-the-dashboard) **Yedeklemeyi Durdur**' u seçin.
2. **Yedekleme verilerini sil**' i seçin ve seçiminizi gerektiği şekilde onaylayın. Yedekleme öğesinin adını girin ve isterseniz bir açıklama ekleyin.

    ![Yedekleme verilerini silme](./media/backup-azure-manage-vms/delete-backup-data.png)

> [!NOTE]
> Silme işlemini tamamladıktan sonra, yedeklenen veriler [geçici olarak silinen durumunda](./soft-delete-virtual-machines.md)14 gün boyunca saklanacaktır. <br>Ayrıca, [geçici silme özelliğini etkinleştirebilir veya devre dışı](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)bırakabilirsiniz.

## <a name="resume-protection-of-a-vm"></a>VM korumasını sürdürür

VM korumasını durdurma sırasında [Korumayı Durdur ve yedek verileri sakla](#stop-protection-and-retain-backup-data) seçeneğini belirlediyseniz **yedeklemeyi devam ettirmeyi** kullanabilirsiniz. [Korumayı Durdur ve yedekleme verilerini sil](#stop-protection-and-delete-backup-data) seçeneğini belirlerseniz veya [yedekleme verilerini silerseniz](#delete-backup-data)Bu seçenek kullanılamaz.

Bir VM için korumayı sürdürmesini sağlamak için:

1. [Kasa öğesinin panosunda](#view-vms-on-the-dashboard) **yedeklemeyi yeniden başlatma**' yı seçin.

2. VM için ilkeyi atamak üzere [yedekleme Ilkelerini yönetme](#manage-backup-policy-for-a-vm) bölümündeki adımları izleyin. VM 'nin ilk koruma ilkesini seçmeniz gerekmez.
3. Yedekleme ilkesini sanal makineye uyguladıktan sonra aşağıdaki iletiyi görürsünüz:

    ![Başarıyla korunan bir VM belirten ileti](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Yedekleme verilerini silme

Bir sanal makinenin yedekleme verilerini silmenin iki yolu vardır:

* Kasa öğesi panosundan Yedeklemeyi Durdur ' u seçin ve [korumayı durdurma ve yedek verileri silme](#stop-protection-and-delete-backup-data) yönergelerini izleyin.

  ![Yedeklemeyi Durdur ' u seçin](./media/backup-azure-manage-vms/stop-backup-button.png)

* Kasa öğesi panosunda, yedekleme verilerini Sil ' i seçin. VM korumasını durdurma sırasında [korumayı durdurmayı ve yedekleme verilerini tutmayı](#stop-protection-and-retain-backup-data) seçtiyseniz bu seçenek etkinleştirilir.

  ![Yedeklemeyi Sil ' i seçin](./media/backup-azure-manage-vms/delete-backup-button.png)

  * [Kasa öğesi panosunda](#view-vms-on-the-dashboard), **yedekleme verilerini sil**' i seçin.
  * Kurtarma noktalarını silmek istediğinizi onaylamak için yedekleme öğesinin adını yazın.

    ![Yedekleme verilerini silme](./media/backup-azure-manage-vms/delete-backup-data.png)

  * Öğe için yedekleme verilerini silmek için **Sil**' i seçin. Bir bildirim iletisi, yedekleme verilerinin silindiğini bilmenizi sağlar.

Verilerinizi korumak için Azure Backup, geçici silme özelliğini içerir. Bir VM 'nin yedekleme (tüm kurtarma noktaları) silindikten sonra bile, geçici silme ile, yedekleme verileri 14 ek gün boyunca tutulur. Daha fazla bilgi için bkz. [geçici silme belgeleri](./backup-azure-security-feature-cloud.md).

  > [!NOTE]
  > Yedekleme verilerini sildiğinizde, ilişkili tüm kurtarma noktalarını silersiniz. Silmek için belirli kurtarma noktaları seçemezsiniz.

### <a name="backup-item-where-primary-data-source-no-longer-exists"></a>Birincil veri kaynağı artık mevcut olmayan yedekleme öğesi

* Azure Backup için yapılandırılmış Azure VM 'Ler korumayı durdurmadan silinir veya taşınırsa, hem zamanlanmış yedekleme işleri hem de isteğe bağlı (geçici) yedekleme işleri UserErrorVmNotFoundV2 hatasıyla başarısız olur. Yedekleme ön denetimi, yalnızca başarısız olan isteğe bağlı yedekleme işleri için kritik olarak görünür (başarısız zamanlanan işler gösterilmez).
* Bu yedekleme öğeleri, Kullanıcı tarafından ayarlanan yedekleme ve bekletme ilkesine bağlı olarak, sistemde etkin kalır. Bu Azure VM 'lerinin yedeklenen verileri, bekletme ilkesine göre tutulacaktır. Süresi geçmiş kurtarma noktaları (en son kurtarma noktası dışında), yedekleme ilkesinde ayarlanan bekletme aralığına göre temizlenir.
* Ek maliyetlerden kaçınmak için, birincil veri kaynağı artık mevcut olmayan yedekleme öğelerini silmenizi öneririz. Bu, en son kurtarma noktası süresiz olarak korunduğu ve ilgili yedekleme fiyatlandırmasına göre ücretlendirildiğiniz için silinen kaynakların yedekleme öğesinin/verilerinin artık gerekli olmadığı bir senaryodur.

## <a name="next-steps"></a>Sonraki adımlar

* Azure VM 'lerini [VM ayarlarından nasıl yedekleyeceğinizi](backup-azure-vms-first-look-arm.md)öğrenin.
* [VM 'leri geri yüklemeyi](backup-azure-arm-restore-vms.md)öğrenin.
* [Azure VM yedeklemelerini izlemeyi](./backup-azure-monitoring-built-in-monitor.md)öğrenin.
