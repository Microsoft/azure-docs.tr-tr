---
title: include dosyası
description: include dosyası
services: virtual-machines
author: barclayn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/13/2019
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: d0ede337f2ae17f28cd02df7ed35919bd9955e7a
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048558"
---
Bu makale, Azure sanal makineler için güvenlik önerileri içerir. Paylaşılan sorumluluk için modelimizde açıklanan güvenlik yükümlülüklerinizi karşılamak üzere bu önerileri izleyin. Öneriler, Web uygulaması çözümleriniz için genel güvenliği iyileştirmenize de yardımcı olur. Hizmet sağlayıcı sorumluluklarını Microsoft 'un karşılama hakkında daha fazla bilgi için bkz. [bulut bilgi işlem Için paylaşılan sorumluluklar](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Bu makalenin bazı önerilerini Azure Güvenlik Merkezi tarafından otomatik olarak çözülebilir. Azure Güvenlik Merkezi, Azure 'daki kaynaklarınız için ilk savunma hattınızdır. Olası güvenlik açıklarını belirlemek için Azure kaynaklarınızın güvenlik durumunu düzenli olarak analiz eder. Daha sonra bu güvenlik açıklarının nasıl ele alınacağını önerir. Daha fazla bilgi için bkz. [Azure Güvenlik Merkezi 'Nde güvenlik önerileri](../articles/security-center/security-center-recommendations.md).

Azure Güvenlik Merkezi hakkında genel bilgi için bkz. [Azure Güvenlik Merkezi nedir?](../articles/security-center/security-center-intro.md).

## <a name="general"></a>Genel

| Öneri | Yorumlar | Güvenlik Merkezi |
|-|----|--|
| Özel VM görüntüleri oluştururken, en son güncelleştirmeleri uygulayın. | Görüntü oluşturmadan önce, işletim sistemi ve görüntünüzün bir parçası olacak tüm uygulamalar için en son güncelleştirmeleri yükleyebilirsiniz.  | - |
| VM 'lerinizi güncel tutun. | Azure 'daki Windows ve Linux bilgisayarlarınıza yönelik işletim sistemi güncelleştirmelerini yönetmek için Azure Otomasyonu 'nda [güncelleştirme yönetimi](../articles/automation/automation-update-management.md) çözümünü kullanabilirsiniz. | [Evet](../articles/security-center/security-center-apply-system-updates.md) |
| VM 'lerinizi yedekleyin. | [Azure Backup](../articles/backup/backup-overview.md) , uygulama verilerinizin korunmasına yardımcı olur ve en düşük işletim maliyetlerine sahiptir. Uygulama hataları verilerinizi bozabilir ve insan hataları, uygulamalarınızda hatalar oluşturabilir. Windows ve Linux çalıştıran sanal makinelerinizi korur Azure Backup. | - |
| Daha büyük esnekliği ve kullanılabilirlik için birden fazla VM kullanın. | VM 'niz yüksek oranda kullanılabilir olması gereken uygulamalar çalıştırıyorsa, birden çok VM veya [kullanılabilirlik kümesi](../articles/virtual-machines/windows/manage-availability.md)kullanın. | - |
| Bir iş sürekliliği ve olağanüstü durum kurtarma (BCDR) stratejisini benimseyin. | Azure Site Recovery, iş sürekliliğini desteklemek için tasarlanan farklı seçenekler arasından seçim yapmanıza olanak tanır. Farklı çoğaltma ve yük devretme senaryolarını destekler. Daha fazla bilgi için bkz. [Site Recovery](../articles/site-recovery/site-recovery-overview.md). | - |

## <a name="data-security"></a>Veri güvenliği

| Öneri | Yorumlar | Güvenlik Merkezi |
|-|----|--|
| İşletim sistemi disklerini şifreleyin. | [Azure disk şifrelemesi](../articles/security/azure-security-disk-encryption-overview.md) , Windows ve Linux ıAAS VM disklerini şifrelemenize yardımcı olur. Gerekli anahtarlar olmadan, şifrelenmiş disklerin içerikleri okunamaz. Disk şifrelemesi, depolanan verileri, aksi halde disk kopyalanırsa mümkün olmayan yetkisiz erişimlerden korur.| [Evet](../articles/security-center/security-center-apply-disk-encryption.md) |
| Veri disklerini şifreleyin. | [Azure disk şifrelemesi](../articles/security/azure-security-disk-encryption-overview.md) , Windows ve Linux ıAAS VM disklerini şifrelemenize yardımcı olur. Gerekli anahtarlar olmadan, şifrelenmiş disklerin içerikleri okunamaz. Disk şifrelemesi, depolanan verileri, aksi halde disk kopyalanırsa mümkün olmayan yetkisiz erişimlerden korur.| -  |
| Yüklü yazılımı sınırlayın. | Çözümünüzü başarıyla uygulamak için, yüklü yazılımları gerekli olan ile sınırlayın. Bu kılavuz, çözümünüzün saldırı yüzeyini azaltmaya yardımcı olur. | - |
| Virüsten koruma veya kötü amaçlı yazılımdan koruma kullanın. | Azure 'da, Microsoft, Symantec, Trend Micro ve Kaspersky gibi güvenlik satıcılarından kötü amaçlı yazılımdan koruma yazılımı kullanabilirsiniz. Bu yazılım, sanal makinelerinizi kötü amaçlı dosyalardan, reklam yazılımlarından ve diğer tehditlerden korumanıza yardımcı olur. Microsoft kötü amaçlı yazılımdan koruma uygulamasını uygulama iş yüklerinize göre dağıtabilirsiniz. Temel güvenli ya da gelişmiş özel yapılandırma kullanın. Daha fazla bilgi için bkz. [Azure Cloud Services Için Microsoft kötü amaçlı yazılımdan koruma ve sanal makineler](../articles/security/azure-security-antimalware.md). | - |
| Anahtarları ve gizli dizileri güvenli bir şekilde depolayın. | Güvenli ve merkezi olarak yönetilen bir seçeneğe sahip uygulama sahiplerinizi sağlayarak gizli dizi ve anahtarlarınızın yönetimini kolaylaştırın. Bu yönetim, yanlışlıkla bir uzlaşma veya sızıntı riskini azaltır. Azure Key Vault, anahtarlarınızı FIPS 140-2 düzey 2 ' ye sertifikalı donanım güvenlik modüllerinde (HSM 'ler) güvenli bir şekilde saklayabilir. Anahtarlarınızı ve sırlarınızı depolamak için FIPS 140,2 düzey 3 ' ü kullanmanız gerekiyorsa [Azure ayrılmış HSM](../articles/dedicated-hsm/overview.md)'yi kullanabilirsiniz. | - |

## <a name="identity-and-access-management"></a>Kimlik ve erişim yönetimi 

| Öneri | Yorumlar | Güvenlik Merkezi |
|-|----|--|
| VM kimlik doğrulamasını merkezileştirme. | [Azure Active Directory kimlik doğrulaması](../articles/active-directory/develop/authentication-scenarios.md)kullanarak Windows ve Linux sanal makinelerinizin kimlik doğrulamasını merkezileştirebilirsiniz. | - |

## <a name="monitoring"></a>İzleme

| Öneri | Yorumlar | Güvenlik Merkezi |
|-|----|--|
| VM 'lerinizi izleyin. | Azure VM 'lerinizin ve sanal makine ölçek kümelerinin durumunu izlemek için [VM'ler için Azure izleyici](../articles/azure-monitor/insights/vminsights-overview.md) kullanabilirsiniz. Bir VM ile ilgili performans sorunları, hizmetin kesintiye uğramasına neden olabilir ve bu da kullanılabilirliği güvenlik ilkesini ihlal eder. | - |

## <a name="networking"></a>Ağ

| Öneri | Yorumlar | Güvenlik Merkezi |
|-|----|--|
| Yönetim bağlantı noktalarına erişimi kısıtlayın. | Saldırganlar açık yönetim bağlantı noktaları için genel bulut IP aralıklarını tarar ve ortak parolalar ve bilinen düzeltme eki yüklenmemiş güvenlik açıkları gibi "kolay" saldırıları dener. [Tam zamanında (JIT) VM erişimi](../articles/security-center/security-center-just-in-time.md) 'Ni kullanarak Azure VM 'lerinize gelen trafiği kilitleyebilir, ancak gerektiğinde sanal makinelere kolay bağlantı sağlarken saldırılara maruz kalmayı azaltabilirsiniz. | - |
| Ağ erişimini sınırlayın. | Ağ güvenlik grupları ağ erişimini kısıtlayıp açığa çıkarılan uç noktaların sayısını denetlemenizi sağlar. Daha fazla bilgi için bkz. [ağ güvenlik grubu oluşturma, değiştirme veya silme](../articles/virtual-network/manage-network-security-group.md). | - |

## <a name="next-steps"></a>Sonraki adımlar

Ek güvenlik gereksinimleri hakkında bilgi edinmek için uygulama sağlayıcınıza danışın. Güvenli uygulamalar geliştirme hakkında daha fazla bilgi için bkz. [Güvenli Geliştirme belgeleri](../articles/security/fundamentals/abstract-develop-secure-apps.md).
