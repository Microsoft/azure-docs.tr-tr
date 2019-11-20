---
title: Azure 'da Azure Site Recovery ile VMware olağanüstü durum kurtarma için kaynak ayarları ayarlama
description: Bu makalede, Azure Site Recovery ile Azure 'a VMware VM 'lerini çoğaltmak için şirket içi ortamınızın nasıl ayarlanacağı açıklanır.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: ff01aed92669acb193ff149ea9298550134f42a3
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622202"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>VMware 'den Azure 'a çoğaltma için kaynak ortamı ayarlama

Bu makalede, VMware VM 'lerini Azure 'a çoğaltmak için kaynak şirket içi ortamınızı ayarlama açıklanmaktadır. Makale, çoğaltma senaryonuzu seçme, Site Recovery yapılandırma sunucusu olarak şirket içi makine ayarlama ve şirket içi VM 'Leri otomatik olarak bulma adımlarını içerir.

## <a name="prerequisites"></a>Ön koşullar

Makalesinde zaten sahip olduğunuz varsayılır:

- [Azure Site Recovery dağıtım planlayıcısı](site-recovery-deployment-planner.md)yardımıyla dağıtımınız planlanmaktadır. Bu, istenen kurtarma noktası hedefini (RPO) karşılayacak günlük veri değişikliği hızınıza göre yeterli bant genişliği ayırmanıza yardımcı olur.
- [Azure Portal](https://portal.azure.com) [kaynakları ayarlayın](tutorial-prepare-azure.md) .
- Otomatik bulma için adanmış bir hesap dahil olmak üzere [Şirket Içi VMware 'Yi ayarlayın](vmware-azure-tutorial-prepare-on-premises.md).

## <a name="choose-your-protection-goals"></a>Koruma hedeflerinizi seçin

1. **Kurtarma Hizmetleri kasaları** bölümünde kasa adını seçin. Bu senaryo için**ContosoVMVault**’u kullanıyoruz.
2. **Başlarken** bölümünde Site Recovery’yi seçin. Daha sonra **Altyapıyı Hazırlama**’yı seçin.
3. **Koruma hedefi** > **Makineleriniz nerede** bölümünde **Şirket içi** seçeneğini belirleyin.
4. **Makinelerinizi nereye çoğaltmak istiyorsunuz** bölümünde **Azure’a** seçeneğini belirleyin.
5. **Makineleriniz sanallaştırıldı mı** bölümünde **Evet, VMware vSphere Hypervisor ile** seçeneğini belirleyin. Sonra **Tamam**’ı seçin.

## <a name="set-up-the-configuration-server"></a>Yapılandırma sunucusunu ayarlama

Yapılandırma sunucusunu, açık bir sanallaştırma uygulaması (OVA) şablonu aracılığıyla şirket içi VMware VM 'si olarak ayarlayabilirsiniz. VMware VM 'de yüklenecek bileşenler hakkında [daha fazla bilgi edinin](concepts-vmware-to-azure-architecture.md) .

1. Yapılandırma sunucusu dağıtımı [önkoşulları](vmware-azure-deploy-configuration-server.md#prerequisites) hakkında bilgi edinin.
2. Dağıtım için [Kapasite numaralarını denetleyin](vmware-azure-deploy-configuration-server.md#sizing-and-capacity-requirements) .
3. Yapılandırma sunucusunu çalıştıran bir şirket içi VMware VM 'yi ayarlamak için OVA şablonunu [indirin](vmware-azure-deploy-configuration-server.md#download-the-template) ve [içeri aktarın](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) . Şablonla birlikte sunulan lisans bir değerlendirme lisansındır ve 180 gün için geçerlidir. Bu dönemde, müşterinin Windows 'u bir temin lisansıyla etkinleştirmesi gerekir.
4. VMware VM 'yi açın ve kurtarma hizmetleri kasasına [kaydedin](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) .

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Virüsten koruma programından klasör dışlamalarını Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Virüsten koruma yazılımı kaynak makinede etkin ise

Kaynak makinede etkin bir virüsten koruma yazılımı varsa, yükleme klasörü dışlanmalıdır. Bu nedenle, sorunsuz çoğaltma için *C:\programdata\asr\agent* klasörünü hariç tutun.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Yapılandırma sunucusunda virüsten koruma yazılımı etkinse

Kesintisiz çoğaltma için aşağıdaki klasörleri virüsten koruma yazılımından hariç tutun ve bağlantı sorunlarını önleyin

- C:\Program Files\Microsoft Azure kurtarma hizmetleri Aracısı.
- C:\Program Files\Microsoft Azure Site Recovery sağlayıcısı
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manager 
- C:\Program Files\Microsoft Azure Site Recovery hata toplama aracı 
  - C:\üçüncüpartisi
  - C:\Temp
  - C:\straileraz
  - C:\ProgramData\MySQL
  - C:\Program Files (x86) \MySQL
  - C:\ProgramData\ASR
  - C:\ProgramData\Microsoft Azure Site Recovery
  - C:\ProgramData\ASRLogs
  - C:\ProgramData\ASRSetupLogs
  - C:\ProgramData\LogUploadServiceLogs
  - C:\Inetpub
  - Sunucu yükleme dizinini Site Recovery. Örneğin: E:\Program Files (x86) \Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Genişleme Işlem sunucusu/ana hedef üzerinde virüsten koruma yazılımı etkinse

Aşağıdaki klasörleri virüsten koruma yazılımından çıkar

1. C:\Program Files\Microsoft Azure kurtarma hizmetleri Aracısı
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. Yük dengeli işlem sunucusu yükleme dizinini Azure Site Recovery, örnek: C:\Program Files (x86) \Microsoft Azure Site Recovery


## <a name="next-steps"></a>Sonraki adımlar
[Hedef ortamınızı ayarlama](./vmware-azure-set-up-target.md) 
