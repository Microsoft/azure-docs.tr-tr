---
title: Windows sanal masaüstü dosya paylaşımının kurulum dosyası-Azure Uygulama iliştirme-Azure
description: Windows sanal masaüstü için MSIX uygulama iliştirme için dosya paylaşımının nasıl ayarlanacağı.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a2d4ebee02d85d10d5db8ec2de0bb1be334770dc
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717666"
---
# <a name="set-up-a-file-share-for-msix-app-attach"></a>MSIX uygulama iliştirme için dosya paylaşma ayarlama

Tüm MSIX görüntülerinin, salt okuma izinlerine sahip bir konak havuzundaki kullanıcılar tarafından erişilebilen bir ağ paylaşımında depolanması gerekir.

MSIX uygulama iliştirme, dosya paylaşımının kullandığı depolama dokusunun türü üzerinde hiçbir bağımlılığı içermez. MSIX uygulama iliştirme paylaşımıyla ilgili konular bir FSLogix paylaşımıyla aynı olanlardır. Depolama gereksinimleri hakkında daha fazla bilgi edinmek için bkz. [Windows sanal masaüstü 'Nde FSLogix profil kapsayıcıları Için depolama seçenekleri](store-fslogix-profile.md).

## <a name="performance-requirements"></a>Performans gereksinimleri

Sisteminiz için MSIX uygulama iliştirme görüntü boyutu sınırları, VHD veya VHDx dosyalarını depolamak için kullandığınız depolama türüne ve VHD, VHSD veya CıM dosyalarının ve dosya sisteminin boyut kısıtlamalarına bağlıdır.

Aşağıdaki tabloda, her bir VM için ihtiyaç duyduğu bir MALTıDAN oluşan tek 1 GB 'lık bir görüntünün kaç kaynağa ait olduğunu gösteren bir örnek verilmiştir:

| Kaynak             | Gereksinimler |
|----------------------|--------------|
| Sabit durum IOPS 'si    | 1 IOPS       |
| Makine önyüklemesi oturum açma | 10 IOPS      |
| Gecikme süresi              | 400 MS       |

Gereksinimler, MSIX 'nin kaç tane üzerinde paketlendiğine göre büyük ölçüde farklılık gösterebilir. Daha büyük MSIX görüntüleri için daha fazla bant genişliği ayırmanız gerekir.

### <a name="storage-recommendations"></a>Depolama önerileri

Azure, MISX uygulama iliştirme için kullanılabilen birden çok depolama seçeneği sunar. Bu seçenekler maliyet ve yönetim yükü arasında en iyi değeri sunan Azure dosyaları veya Azure NetApp Files kullanmanızı öneririz. [Windows sanal masaüstündeki FSLogix profil kapsayıcılarına yönelik makale depolama seçenekleri](store-fslogix-profile.md) , Azure 'un sunduğu farklı yönetilen depolama çözümlerini Windows sanal masaüstü bağlamında karşılaştırır.

### <a name="optimize-msix-app-attach-performance"></a>MALTı uygulama iliştirme performansını iyileştir

Aşağıda, MSIX uygulama iliştirme performansını iyileştirmek için yapmanız önerilen bazı diğer şeyler verilmiştir:

- MSIX uygulama iliştirme için kullandığınız depolama çözümü, oturum ana bilgisayarları ile aynı veri merkezi konumunda olmalıdır.
- Performans sorunlarını önlemek için, aşağıdaki VHD, VHDX ve CıM dosyalarını virüsten koruma taramalarından hariç tutun:
   
    - <MSIXAppAttachFileShare \> \* . SAHIP
    - <MSIXAppAttachFileShare \> \* . IÇERMEDIĞI
    - \\\\storageaccount.file.core.windows.net \\ paylaşma \* \* . SAHIP
    - \\\\storageaccount.file.core.windows.net \\ paylaşma \* \* . IÇERMEDIĞI
    - <MSIXAppAttachFileShare>. CıM
    - \\\\storageaccount.file.core.windows.net \\ paylaşma \* \* . CıM

- FSLogix profil kapsayıcılarından MSIX uygulama iliştirme için depolama dokusunu ayırın.
- Tüm VM sistem hesaplarının ve Kullanıcı hesaplarının dosya paylaşımında erişim için salt okuma izinlerine sahip olması gerekir.
- Windows sanal masaüstü için herhangi bir olağanüstü durum kurtarma planı, ikincil yük devretme konumunuzda MSIX uygulama iliştirme dosya paylaşımının çoğaltılmasını içermelidir. Olağanüstü durum kurtarma hakkında daha fazla bilgi edinmek için bkz. [iş sürekliliği ve olağanüstü durum kurtarma planı ayarlama](disaster-recovery.md).

## <a name="how-to-set-up-the-file-share"></a>Dosya paylaşımının nasıl ayarlanacağı

MSIX uygulama iliştirme dosya paylaşımı için kurulum işlemi, büyük ölçüde [FSLogix profil dosya paylaşımları için kurulum sürecidir](create-host-pools-user-profile.md). Ancak kullanıcılara farklı izinler atamanız gerekir. MSIX uygulama iliştirme dosya paylaşımında erişim için salt okuma izinleri gerektirir.

MSIX uygulamalarınızı Azure dosyalarında depoluyorsanız, oturum ana bilgisayarları için, her oturum ana bilgisayar VM 'lerini, hem depolama hesabı rol tabanlı erişim denetimi (RBAC) hem de dosya paylaşma yeni teknoloji dosya sistemi (NTFS) izinleri için atamanız gerekir.

| Azure nesnesi                      | Gerekli rol                                     | Rol işlevi                                  |
|-----------------------------------|--------------------------------------------------|-----------------------------------------------|
| Oturum Ana Bilgisayarı (VM bilgisayar nesneleri)| Depolama Dosyası Verileri SMB Paylaşımı Katkıda Bulunanı          | Klasör içeriğini okuma ve yürütme, okuma, listeleme  |
| Dosya paylaşımındaki Yöneticiler              | Depolama Dosyası Verileri SMB Paylaşımı Yükseltilmiş Katkıda Bulunanı | Tam denetim                                  |
| Dosya paylaşımındaki kullanıcılar               | Depolama Dosyası Verileri SMB Paylaşımı Katkıda Bulunanı          | Klasör içeriğini okuma ve yürütme, okuma, listeleme  |

Depolama hesabı ve dosya paylaşımında oturum ana bilgisayar VM 'Leri atamak için:

1. Active Directory Domain Services (AD DS) bir güvenlik grubu oluşturun.

2. Tüm oturum ana bilgisayarları için bilgisayar hesaplarını grubun üyesi olarak ekleyin.

3. AD DS grubunu Azure Active Directory (Azure AD) ile eşitleyin.

4. Depolama hesabı oluşturma.

5. [Azure dosya paylaşma oluşturma](../storage/files/storage-how-to-create-file-share.md#create-a-file-share)bölümündeki yönergeleri izleyerek depolama hesabı altında bir dosya paylaşma oluşturun.

6. Birinci bölüm içindeki yönergeleri izleyerek AD DS depolama hesabına katılarak [Azure dosya paylaşımlarınız için AD DS kimlik doğrulamasını etkinleştirin](../storage/files/storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module).

7. Eşitlenmiş AD DS grubunu Azure AD 'ye atayın ve depolama hesabı depolama dosyası veri SMB paylaşımının katkıda bulunan rolünü atayın.

8. İki bölümden oluşan yönergeleri izleyerek dosya paylaşımının herhangi bir oturum konağına bağlanması [: bir kimliğe Share-Level izinleri atama](../storage/files/storage-files-identity-ad-ds-assign-permissions.md).

9. Dosya paylaşımında AD DS grubuna NTFS izinleri verin.

10. Kullanıcı hesapları için NTFS izinlerini ayarlayın. VM 'deki hesapların ait olduğu AD DS kaynak bir işletim birimi (OU) olması gerekir.

Kimliği depolamaya atadıktan sonra, VM 'lere atadığınız kimliğe diğer gerekli izinleri vermek için [sonraki adımlarda](#next-steps) bulunan makalelerdeki yönergeleri izleyin.

Ayrıca, oturum ana bilgisayar sanal makinelerinizin yeni teknoloji dosya sistemi (NTFS) izinlerine sahip olduğundan emin olmanız gerekir. Active Directory Domain Services (AD DS) kaynaklı bir işlemsel birim kapsayıcısına sahip olmanız ve kullanıcılarınızın bu izinleri kullanmak için bu işletimsel birimin üyesi olması gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Dosya paylaşımından sonra yapmanız gereken diğer şeyler şunlardır:

- [Azure dosyaları ve AD DS profil kapsayıcısı oluşturma](create-file-share.md)konusunda Azure Active Directory Domain Services (AD DS) ayarlamayı öğrenin.
- Azure dosyaları ve Azure AD DS oluşturma hakkında bilgi edinmek için bkz. Azure dosyaları ve Azure [AD DS ile profil kapsayıcısı oluşturma](create-profile-container-adds.md).
- [Azure NetApp Files ve AD DS ile bir profil kapsayıcısı oluşturma](create-fslogix-profile-container.md)konusunda msix uygulama iliştirme için Azure NetApp Files ayarlamayı öğrenin.
- Bir [dosya paylaşımının kullanıldığı konak havuzu için profil kapsayıcısı oluşturma](create-host-pools-user-profile.md)sırasında sanal makine tabanlı dosya paylaşımının nasıl kullanılacağını öğrenin.

İşiniz bittiğinde, yararlı bulabileceğiniz bazı kaynaklar aşağıda verilmiştir:

- Bu özellikle ilgili topluluk sorularımıza [Windows sanal masaüstü TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)' de sorun.
- Windows sanal masaüstü [Geri Bildirim Hub 'ında](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)Windows sanal masaüstü geri bildirimini de bırakabilirsiniz.
- [MSIX uygulama iliştirme sözlüğü](app-attach-glossary.md)
- [MSIX uygulama iliştirme SSS](app-attach-faq.md)
