---
title: Yedekleme ve geri yükleme Active Directory
description: Active Directory etki alanı denetleyicilerini yedeklemeyi ve geri yüklemeyi öğrenin.
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8db2dab605e90e4748b11a632d6651c23d631b6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98733562"
---
# <a name="back-up-and-restore-active-directory-domain-controllers"></a>Active Directory etki alanı denetleyicilerini yedekleme ve geri yükleme

Active Directory yedekleme ve bozulma durumlarında başarılı geri yükleme sağlamak ve Active Directory bakımın önemli bir parçasıdır.

Bu makalede, Azure sanal makineleri veya şirket içi sunucular gibi Azure Backup Active Directory etki alanı denetleyicilerini yedeklemeye ve geri yüklemeye yönelik doğru yordamlar özetlenmektedir. Tüm etki alanı denetleyicisini yedekleme sırasında durumuna geri yüklemeniz gereken bir senaryoyu ele alır. Hangi geri yükleme senaryosunun sizin için uygun olduğunu görmek için, [Bu makaleye](/windows-server/identity/ad-ds/manage/ad-forest-recovery-determine-how-to-recover)bakın.  

>[!NOTE]
> Bu makalede öğelerin [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)geri yüklenmesi anlatılmamaktadır. Azure Active Directory kullanıcıları geri yükleme hakkında daha fazla bilgi için [Bu makaleye](../active-directory/fundamentals/active-directory-users-restore.md)bakın.

## <a name="best-practices"></a>En iyi uygulamalar

- En az bir etki alanı denetleyicisinin yedeklendiğinizden emin olun. Birden çok etki alanı denetleyicisini yedeklemeniz durumunda, [FSMO (esnek tek yönetici işlemi) rollerini](/windows-server/identity/ad-ds/plan/planning-operations-master-role-placement) tutan tüm dosyaların yedeklendiğinizden emin olun.

- Active Directory sıklıkla yedekleyin. Ön kaldırılma ömrü "kaldırıldı" olduğundan ve artık geçerli kabul edilmeden, yedekleme, kaldırılma süresinden (varsayılan olarak 60 gün) hiç olmamalıdır.

- Etki alanı denetleyicilerinizi geri yükleme yönergelerini içeren net bir olağanüstü durum kurtarma planına sahiptir. Bir Active Directory ormanı geri yüklemeye hazırlanmak için [Active Directory orman kurtarma Kılavuzu](/windows-server/identity/ad-ds/manage/ad-forest-recovery-guide)' nu okuyun.

- Bir etki alanı denetleyicisini geri yüklemeniz ve etki alanında çalışan bir etki alanı denetleyiciniz olması gerekiyorsa, yedekten geri yükleme yerine yeni bir sunucu oluşturabilirsiniz. **Active Directory Domain Services** sunucusu rolünü, mevcut etki alanında bir etki alanı denetleyicisi yapmak için yeni sunucuya ekleyin. Active Directory veriler yeni sunucuya çoğaltılır. Önceki etki alanı denetleyicisini Active Directory kaldırmak için, meta veri temizleme işlemini gerçekleştirmek üzere [Bu makaledeki](/windows-server/identity/ad-ds/deploy/ad-ds-metadata-cleanup) adımları izleyin.

>[!NOTE]
>Azure Backup, Active Directory için öğe düzeyinde geri yükleme içermez. Silinen nesneleri geri yüklemek isterseniz ve bir etki alanı denetleyicisine erişebiliyorsa, [Active Directory geri dönüşüm kutusu](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#ad_recycle_bin_mgmt)'nu kullanın. Bu yöntem yoksa, [burada](https://support.microsoft.com/help/840001/how-to-restore-deleted-user-accounts-and-their-group-memberships-in-ac)açıklandığı gibi, silinen nesneleri **ntdsutil.exe** aracı ile geri yüklemek için etki alanı denetleyicisi yedeklemenizi kullanabilirsiniz.
>
>SYSVOL 'ün yetkili geri yüklemesini gerçekleştirme hakkında daha fazla bilgi için [Bu makaleye](/windows-server/identity/ad-ds/manage/ad-forest-recovery-authoritative-recovery-sysvol)bakın.

## <a name="backing-up-azure-vm-domain-controllers"></a>Azure VM etki alanı denetleyicilerini yedekleme

Etki alanı denetleyicisi bir Azure VM ise, [Azure VM yedeklemesi](backup-azure-vms-introduction.md)'ni kullanarak sunucuyu yedekleyebilirsiniz.

Azure VM etki alanı denetleyicilerinizin başarılı yedeklemeler (ve gelecekteki geri yüklemeler) sağlamak için, [sanallaştırılmış etki alanı denetleyicileri için işlemsel hususlar](/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v#operational-considerations-for-virtualized-domain-controllers) hakkında bilgi edinin.

## <a name="backing-up-on-premises-domain-controllers"></a>Şirket içi etki alanı denetleyicilerini yedekleme

Şirket içi etki alanı denetleyicisini yedeklemek için sunucunun sistem durumu verilerini yedeklemeniz gerekir.

- MARS kullanıyorsanız, [Bu yönergeleri](backup-azure-system-state.md)izleyin.
- MABS (Azure Backup Sunucusu) kullanıyorsanız, [Bu yönergeleri](backup-mabs-system-state-and-bmr.md)izleyin.

>[!NOTE]
> Şirket içi etki alanı denetleyicilerini (sistem durumundan veya VM 'lerden) Azure bulutuna geri yükleme desteklenmez. Şirket içi Active Directory ortamından Azure 'a yük devretme seçeneği istiyorsanız [Azure Site Recovery](../site-recovery/site-recovery-active-directory.md)kullanmayı göz önünde bulundurun.

## <a name="restoring-active-directory"></a>Active Directory geri yükleniyor

Active Directory veriler iki moddan birinde geri yüklenebilir: **yetkili** veya **yetkisiz**. Yetkili geri yükleme sırasında, geri yüklenen Active Directory verileri, ormandaki diğer etki alanı denetleyicilerinde bulunan verileri geçersiz kılar.

Ancak, bu senaryoda, mevcut bir etki alanında bir etki alanı denetleyicisi yeniden oluşturuluyor, bu nedenle **yetkisiz** bir geri yükleme gerçekleştirilmelidir.

Geri yükleme işlemi sırasında sunucu Dizin Hizmetleri geri yükleme modu 'nda (DSRM) başlatılır. Dizin Hizmetleri geri yükleme modu için yönetici parolasını sağlamanız gerekir.

>[!NOTE]
>DSRM parolası unutursa, [Bu yönergeleri](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc754363(v=ws.11))kullanarak sıfırlayabilirsiniz.

### <a name="restoring-azure-vm-domain-controllers"></a>Azure VM etki alanı denetleyicilerini geri yükleme

Azure VM etki alanı denetleyicisini geri yüklemek için bkz. [etki alanı denetleyicisi VM 'Lerini geri yükleme](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).

Tek bir etki alanı denetleyicisi sanal makinesini veya birden çok etki alanı denetleyicisi sanal makinesini tek bir etki alanına geri yüklüyorsanız, bunları diğer VM 'Ler gibi geri yükleyin. Dizin Hizmetleri geri yükleme modu (DSRM) da kullanılabilir, bu nedenle tüm Active Directory kurtarma senaryoları geçerlidir.

Birden çok etki alanı yapılandırmasında tek bir etki alanı denetleyicisi sanal makinesini geri yüklemeniz gerekiyorsa, diskleri geri yükleyin ve [PowerShell kullanarak](backup-azure-vms-automation.md#restore-the-disks)bir VM oluşturun.

Etki alanında kalan son etki alanı denetleyicisini geri yüklüyorsanız veya bir ormanda birden çok etki alanını geri yüklüyorsanız, bir [orman kurtarması](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)önerilir.

>[!NOTE]
> Windows 2012 ve sonraki sürümlerde sanallaştırılmış etki alanı denetleyicileri [sanallaştırma tabanlı korumalar](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100#virtualization-based-safeguards)kullanır. Bu korumalar sayesinde, Active Directory, geri yüklenen VM 'nin bir etki alanı denetleyicisi olup olmadığını anlamıştır ve Active Directory verilerini geri yüklemek için gerekli adımları gerçekleştirir.

### <a name="restoring-on-premises-domain-controllers"></a>Şirket içi etki alanı denetleyicilerini geri yükleme

Bir şirket içi etki alanı denetleyicisini geri yüklemek için, [bir etki alanı denetleyicisinde sistem durumu kurtarmaya yönelik özel noktalara](backup-azure-restore-system-state.md#special-considerations-for-system-state-recovery-on-a-domain-controller)yönelik yönergeleri kullanarak sistem durumunu Windows Server 'a geri yükleme yönergelerini izleyin.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Backup için destek matrisi](backup-support-matrix.md)