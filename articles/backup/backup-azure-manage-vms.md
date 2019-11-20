---
title: Azure VM yedeklemelerini yönetme ve izleme
description: Azure Backup hizmetini kullanarak Azure VM yedeklemelerini yönetme ve izleme hakkında bilgi edinin.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 3c4c4341286b7ee4f382192d291e08c83ff64994
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172967"
---
# <a name="manage-azure-vm-backups-with-azure-backup-service"></a>Azure Backup hizmeti ile Azure VM yedeklemelerini yönetme

Bu makalede, [Azure Backup hizmeti](backup-overview.md)kullanılarak yedeklenen Azure sanal makinelerinin (VM) nasıl yönetileceği açıklanmaktadır. Makale Ayrıca, kasa panosunda bulabileceğiniz yedekleme bilgilerini de özetler.

Azure portal, kurtarma hizmetleri Kasası panosu, aşağıdakiler de dahil olmak üzere kasa bilgilerine erişim sağlar:

* En son geri yükleme noktası olan en son yedekleme.
* Yedekleme ilkesi.
* Tüm yedekleme anlık görüntülerinin toplam boyutu.
* Yedeklemeler için etkinleştirilen sanal makinelerin sayısı.

, Panoyu kullanarak ve tek tek VM 'lere giderek, yedeklemeleri yönetebilirsiniz. Makine yedeklemelerini başlatmak için panoda kasayı açın.

![Kaydırıcı ile tam pano görünümü](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>Panodaki VM 'Leri görüntüleme

Kasa panosunda VM 'Leri görüntülemek için:

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Hub menüsünde, **Araştır**' ı seçin. Kaynak listesinde **Kurtarma Hizmetleri** yazın. Siz yazarken liste, giriş listenize göre filtrelenir. **Kurtarma Hizmetleri kasalarını**seçin.

    ![Kurtarma Hizmetleri kasası oluşturma](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. Kullanım kolaylığı için kasaya sağ tıklayın ve **panoya sabitle**' yi seçin.
4. Kasa panosunu açın.

    ![Kasa panosu ve ayarlar dikey penceresini açın](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. **Yedekleme öğeleri** kutucuğunda **Azure sanal makineler**' i seçin.

    ![Yedekleme öğeleri kutucuğunu aç](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. **Yedekleme öğeleri** dikey penceresinde korunan VM 'lerin listesini görüntüleyebilirsiniz. Bu örnekte, kasa bir sanal makineyi korur: demoyedekleme.  

    ![Yedekleme öğeleri dikey penceresini görüntüleyin](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. Kasa öğesinin panosundan, yedekleme ilkelerini değiştirin, isteğe bağlı bir yedekleme çalıştırın, VM korumasını durdurun veya sürdürülemez, yedekleme verilerini silin, geri yükleme noktalarını görüntüleyin ve bir geri yükleme çalıştırın.

    ![Yedekleme öğeleri panosu ve ayarlar dikey penceresi](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>VM için yedekleme ilkesini yönetme

Bir yedekleme ilkesini yönetmek için:

1. [Azure portalında](https://portal.azure.com/) oturum açın. Kasa panosunu açın.
2. **Yedekleme öğeleri** kutucuğunda **Azure sanal makineler**' i seçin.

    ![Yedekleme öğeleri kutucuğunu aç](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. **Yedekleme öğeleri** dikey penceresinde, korunan sanal makinelerin listesini ve en son geri yükleme noktası zamanına sahip son yedekleme durumunu görüntüleyebilirsiniz.

    ![Yedekleme öğeleri dikey penceresini görüntüleyin](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. Kasa öğesinin panosundan bir yedekleme ilkesi seçebilirsiniz.

   * İlkeleri değiştirmek için farklı bir ilke seçin ve ardından **Kaydet**' i seçin. Yeni ilke hemen kasaya uygulanır.

     ![Bir yedekleme ilkesi seçin](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>İsteğe bağlı yedekleme çalıştırma

Korumasını ayarladıktan sonra bir VM 'nin isteğe bağlı yedeklemesini çalıştırabilirsiniz. Bu ayrıntıları göz önünde bulundurun:

* İlk yedekleme beklendiğinde, isteğe bağlı yedekleme, kurtarma hizmetleri kasasında sanal makinenin tam bir kopyasını oluşturur.
* İlk yedekleme işlemi tamamlandıktan sonra, bir isteğe bağlı yedekleme, değişiklikleri yalnızca önceki anlık görüntüden kurtarma hizmetleri kasasına gönderir. Diğer bir deyişle, sonraki yedeklemeler her zaman artımlıdır.
* İsteğe bağlı yedekleme için bekletme aralığı, yedeklemeyi tetiklemeniz durumunda belirttiğiniz saklama değeridir.

İsteğe bağlı yedekleme tetiklemesi için:

1. [Kasa öğesi panosunda](#view-vms-on-the-dashboard), **korunan öğe**altında **yedekleme öğesi**' ni seçin.

    ![Şimdi Yedekle seçeneği](./media/backup-azure-manage-vms/backup-now-button.png)

2. **Yedekleme yönetimi türünden** **Azure sanal makinesi**' ni seçin. **Yedekleme öğesi (Azure sanal makine)** dikey penceresi görünür.
3. Bir VM seçin ve isteğe bağlı yedekleme oluşturmak için **Şimdi Yedekle** ' yi seçin. **Şimdi Yedekle** dikey penceresi görünür.
4. Yedek alana **sakla** alanında yedeklemenin korunması için bir tarih belirtin.

    ![Şimdi Yedekle takvimi](./media/backup-azure-manage-vms/backup-now-check.png)

5. Yedekleme işini çalıştırmak için **Tamam ' ı** seçin.

İşin ilerlemesini izlemek için kasa panosunda **yedekleme işleri** kutucuğunu seçin.

## <a name="stop-protecting-a-vm"></a>VM korumasını durdurma

Bir VM 'yi korumayı durdurmanın iki yolu vardır:

* **Korumayı durdurun ve yedekleme verilerini koruyun**. Bu seçenek, gelecekteki tüm yedekleme işlerinin VM 'nizi korumasını durdurur; Ancak, Azure Backup hizmet yedeklenen kurtarma noktalarını korur.  Kurtarma noktalarını kasada tutmak için ödeme yapmanız gerekir (Ayrıntılar için [Azure Backup fiyatlandırmasına](https://azure.microsoft.com/pricing/details/backup/) bakın). Gerekirse VM 'yi geri yükleyebileceksiniz. VM korumasını sürdürmeye karar verirseniz, *yedeklemeyi yeniden başlatma* seçeneğini kullanabilirsiniz.
* **Korumayı durdurun ve yedekleme verilerini silin**. Bu seçenek, gelecekteki tüm yedekleme işlerinin VM 'nizi korumasını durdurur ve tüm kurtarma noktalarını siler. VM 'yi geri yükleyemeyeceksiniz ve *yedeklemeyi* yeniden yükle seçeneğini kullanamazsınız.

>[!NOTE]
>Yedeklemeleri durdurmadan bir veri kaynağını silerseniz, yeni yedeklemeler başarısız olur. Eski kurtarma noktaları ilkeye göre sona erer, ancak yedeklemeleri durdurup verileri silene kadar bir son kurtarma noktası her zaman tutulur.
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

    ![Yedekleme verilerini silme](./media/backup-azure-manage-vms/delete-backup-data1.png)

## <a name="resume-protection-of-a-vm"></a>VM korumasını sürdürür

VM korumasını durdurma sırasında [Korumayı Durdur ve yedek verileri sakla](#stop-protection-and-retain-backup-data) seçeneğini belirlediyseniz **yedeklemeyi sürdürme**' yı kullanabilirsiniz. [Korumayı Durdur ve yedekleme verilerini sil](#stop-protection-and-delete-backup-data) seçeneğini belirlerseniz veya [yedekleme verilerini silerseniz](#delete-backup-data)Bu seçenek kullanılamaz.

Bir VM için korumayı sürdürmesini sağlamak için:

1. [Kasa öğesinin panosunda](#view-vms-on-the-dashboard) **yedeklemeyi yeniden başlatma**' yı seçin.

2. VM için ilkeyi atamak üzere [yedekleme Ilkelerini yönetme](#manage-backup-policy-for-a-vm) bölümündeki adımları izleyin. VM 'nin ilk koruma ilkesini seçmeniz gerekmez.
3. Yedekleme ilkesini sanal makineye uyguladıktan sonra aşağıdaki iletiyi görürsünüz:

    ![Başarıyla korunan bir VM belirten ileti](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Yedekleme verilerini silme

Bir sanal makinenin yedekleme verilerini silmenin iki yolu vardır:

* Kasa öğesi panosundan Yedeklemeyi Durdur ' u seçin ve [korumayı durdurma ve yedek verileri silme](#stop-protection-and-delete-backup-data) yönergelerini izleyin.

  ![Yedeklemeyi Durdur ' u seçin](./media/backup-azure-manage-vms/stop-backup-buttom.png)

* Kasa öğesi panosunda, yedekleme verilerini Sil ' i seçin. VM korumasını durdurma sırasında [korumayı durdurmayı ve yedekleme verilerini tutmayı](#stop-protection-and-retain-backup-data) seçtiyseniz bu seçenek etkinleştirilir

  ![Yedeklemeyi Sil ' i seçin](./media/backup-azure-manage-vms/delete-backup-buttom.png)

  * [Kasa öğesi panosunda](#view-vms-on-the-dashboard), **yedekleme verilerini sil**' i seçin.
  * Kurtarma noktalarını silmek istediğinizi onaylamak için yedekleme öğesinin adını yazın.

    ![Yedekleme verilerini silme](./media/backup-azure-manage-vms/delete-backup-data1.png)

  * Öğe için yedekleme verilerini silmek için **Sil**' i seçin. Bir bildirim iletisi, yedekleme verilerinin silindiğini bilmenizi sağlar.

  > [!NOTE]
  > Yedekleme verilerini sildiğinizde, ilişkili tüm kurtarma noktalarını silersiniz. Silmek için belirli kurtarma noktaları seçemezsiniz.

### <a name="backup-item-where-primary-data-source-no-longer-exists"></a>Birincil veri kaynağı artık mevcut olmayan yedekleme öğesi

* Azure Backup için yapılandırılmış Azure VM 'Ler korumayı durdurmadan silinir veya taşınırsa, hem zamanlanmış yedekleme işleri hem de isteğe bağlı (geçici) yedekleme işleri UserErrorVmNotFoundV2 hatasıyla başarısız olur. Yedekleme ön denetimi yalnızca başarısız olan isteğe bağlı yedekleme işleri için kritik olarak görünür (başarısız zamanlanan işler gösterilmez).
* Bu yedekleme öğeleri, Kullanıcı tarafından ayarlanan yedekleme ve bekletme ilkesine bağlı olarak, sistemde etkin kalır. Bu Azure VM 'lerinin yedeklenen verileri, bekletme ilkesine göre tutulacaktır. Süresi biten kurtarma noktaları (son kurtarma noktası dışında), yedekleme ilkesinde ayarlanan bekletme aralığına göre temizlenir.
* Tüm ek maliyetlerden kaçınmak için, birincil veri kaynağı artık mevcut olmayan yedekleme öğelerini silmeniz önerilir, çünkü silme kaynakları için yedekleme öğesi/verileri artık son kurtarma noktası sürekli tutulur ve Kullanıcı, geçerli yedekleme fiyatlandırmasına göre ücretlendirilir.

## <a name="next-steps"></a>Sonraki adımlar

* Azure VM 'lerini [VM ayarlarından nasıl yedekleyeceğinizi](backup-azure-vms-first-look-arm.md)öğrenin.
* [VM 'leri geri yüklemeyi](backup-azure-arm-restore-vms.md)öğrenin.
* [Azure VM yedeklemelerini izlemeyi](backup-azure-monitor-vms.md)öğrenin.
