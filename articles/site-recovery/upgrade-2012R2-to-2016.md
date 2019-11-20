---
title: Windows Server/System Center VMM 2012 R2 'yi Windows Server 2016 'ye yükseltme-Azure Site Recovery
description: Azure Site Recovery hizmetiyle Azure Stack VM 'Ler için Azure 'da olağanüstü durum kurtarmayı ayarlamayı öğrenin.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: rajanaki
ms.openlocfilehash: 1d94935db542a0e64754ab8769996fe906f88b46
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954416"
---
# <a name="upgrade-windows-server-serversystem-center-2012-r2-vmm-to-windows-servervmm-2016"></a>Windows Server Server/System Center 2012 R2 VMM 'yi Windows Server/VMM 2016 'ye yükseltme 

Bu makalede, Azure Site Recovery ile yapılandırılan Windows Server 2012 R2 konaklarının & SCVMM 2012 R2 'yi Windows Server 2016 & SCVMM 2016 ' e nasıl yükselteceğiniz gösterilmektedir

Site Recovery iş sürekliliği ve olağanüstü durum kurtarma (BCDR) stratejinize katkıda bulunur. Hizmet, sanal makine iş yüklerinizin beklenen durumlarda kullanılabilir kalmasını ve beklenmeyen kesintiler meydana gelmesini sağlar.

> [!IMPORTANT]
> Azure Site Recovery ile çoğaltma için zaten yapılandırılmış olan Windows Server 2012 R2 konaklarını yükselttiğinizde, bu belgede bahsedilen adımları izlemeniz gerekir. Yükseltme için seçilen alternatif yol, desteklenmeyen durumlara neden olabilir ve çoğaltma veya yük devretme gerçekleştirme yeteneğinin oluşmasına neden olabilir.


Bu makalede, ortamınızda aşağıdaki yapılandırmaların nasıl yükseltileceğini öğreneceksiniz:

> [!div class="checklist"]
> * **SCVMM tarafından yönetilmeyen Windows Server 2012 R2 Konakları** 
> * **Tek başına bir SCVMM 2012 R2 sunucusu tarafından yönetilen Windows Server 2012 R2 Konakları** 
> * **Yüksek oranda kullanılabilir SCVMM 2012 R2 Server tarafından yönetilen Windows Server 2012 R2 Konakları**


## <a name="prerequisites--factors-to-consider"></a>Dikkate alınması gereken & önkoşulları

Yükseltmeden önce şunları göz önünde bulabilirsiniz:-

- SCVMM tarafından yönetilmeyen Windows Server 2012 R2 Konakları varsa ve tek başına bir ortam kurulumu ise, yükseltmeyi gerçekleştirmeye çalışırsanız çoğaltma sırasında bir kesme olur.
- SCVMM 2012 R2 'yi ilk yere yüklerken "*anahtarlarımı dağıtılmış anahtar yönetimi altında Active Directory olarak depolamam*" seçeneğini belirlediyseniz, yükseltmeler başarıyla tamamlanmaz.

- System Center 2012 R2 VMM kullanıyorsanız, 

    - VMM 'deki veritabanı bilgilerini denetleyin: **VMM konsolu** -> **ayarları** -> **genel** -> **veritabanı bağlantısı**
    - System Center Virtual Machine Manager Aracısı hizmeti için kullanılan hizmet hesaplarını denetleme
    - VMM veritabanının yedeğine sahip olduğunuzdan emin olun.
    - Dahil edilen SCVMM sunucularının veritabanı adını aklınızda edin. Bu işlem, **VMM konsol** -> **ayarları** -> **genel** -> **veritabanı bağlantısı** ' na gidilerek yapılabilir
    - Hem 2012R2 birincil hem de kurtarma VMM sunucularının VMM KIMLIĞINI aklınızda edin. VMM KIMLIĞI, "HKLM: \ SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup" kayıt defterinden bulunabilir.
    - Kümeye eklediğiniz yeni SCN 'ler, daha önce olduğu gibi adlara sahip olduğundan emin olun. 

- Her iki tarafta da SCVMMs tarafından yönetilen sitelerinizden ikisi arasında çoğaltma yapıyorsanız, birincil tarafı yükseltmeden önce kurtarma yüzinizi yükseltdiğinizden emin olun.
  > [!WARNING]
  > SCVMM 2012 R2 'yi yükseltirken, dağıtılmış anahtar yönetimi altında, **şifreleme anahtarlarını Active Directory ' de depolamayı**seçin. Hizmet hesabı ve dağıtılmış anahtar yönetimi ayarlarını dikkatle seçin. Seçiminize bağlı olarak, şablonlardaki parolalar gibi şifrelenmiş veriler yükseltmeden sonra kullanılamayabilir ve Azure Site Recovery ile çoğaltmayı etkileyebilecek olabilir

> [!IMPORTANT]
> Lütfen [önkoşulların](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations) ayrıntılı SCVMM belgelerine bakın

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>SCVMM tarafından yönetilmeyen Windows Server 2012 R2 Konakları 
Aşağıda bahsedilen adımların listesi, bu [öğreticiyi](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial) izleyerek [Hyper-V konaklarındaki Azure 'a yönelik](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-architecture) Kullanıcı Yapılandırması için geçerlidir

> [!WARNING]
> Önkoşullardan bahsedildiği gibi, bu adımlar yalnızca, tek başına bir Hyper-V ana bilgisayar yapılandırmasında değil, kümelenmiş bir ortam senaryosu için geçerlidir.

1. [Kayan küme yükseltmesini](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) gerçekleştirmek için adımları izleyin. çalışırken küme yükseltme işlemini yürütmek için.
2. Kümede tanıtılan her yeni Windows Server 2016 ana bilgisayarı ile, [buradan] bahsedilen adımları izleyerek Azure Site Recovery bir Windows Server 2012 R2 ana bilgisayarının başvurusunu kaldırın. Bu, kümeden çıkarılması & boşaltmayı seçtiğiniz ana bilgisayar olmalıdır.
3. *Update-VMVersion* komutu tüm sanal makineler için yürütüldükten sonra, yükseltmeler tamamlanmıştır. 
4. Azure Site Recovery için yeni Windows Server 2016 konağını kaydetmek üzere [burada](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-the-source-environment) bahsedilen adımları kullanın. Lütfen Hyper-V sitesinin zaten etkin olduğunu ve yalnızca yeni konağı kümeye kaydetmeniz gerektiğini unutmayın. 
5.  Azure portal gidin ve Kurtarma Hizmetleri içindeki çoğaltılan sistem durumunu doğrulayın

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>Tek başına SCVMM 2012 R2 sunucusu tarafından yönetilen Windows Server 2012 R2 konaklarınızı yükseltin
Windows sever 2012 R2 konaklarınızı yükseltmeden önce, SCVMM 2012 R2 'yi SCVMM 2016 sürümüne yükseltmeniz gerekir. Aşağıdaki adımları izleyin:-

**Tek başına SCVMM 2012 R2 'yi SCVMM 2016 sürümüne yükseltin**

1.  Denetim Masası-> Programlar-> Programlar ve Özellikler-> Microsoft Azure Site Recovery ' a giderek ASR sağlayıcısını kaldırın ve Kaldır ' a tıklayın.
2. [SCVMM veritabanını koruma ve işletim sistemini yükseltme](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. **Program Ekle Kaldır**bölümünde **VMM** > **Kaldır**' ı seçin. b. **Özellikleri Kaldır**' ı seçin ve ardından V**mm YÖNETIM sunucusu ve VMM konsolu**' nu seçin. c. **Veritabanı seçenekleri**' nde **veritabanını sakla**' yı seçin. d. Özeti gözden geçirin ve **Kaldır**' a tıklayın.

4. [VMM 2016 'yi yükler](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016)
5. **Yapılar** sekmesinin altındaki her BIR konağın SCVMM 'yi başlatın ve durumunu denetleyin. en son durumu almak için **Yenile** 'ye tıklayın. Durumu "Ilgilenilmesi gerekiyor" olarak görmeniz gerekir. 
17. En son [Microsoft Azure Site Recovery SAĞLAYıCıSıNı](https://aka.ms/downloaddra) SCVMM 'ye yükler.
16. En son [Microsoft Azure kurtarma hizmeti (mars) aracısını](https://aka.ms/latestmarsagent) kümenin her bir konağına yükler. SCVMM 'nin Konakları başarıyla sorgulayabilmesini sağlamak için yenileyin.

**Windows Server 2012 R2 konaklarınızı Windows Server 2016 ' ye yükseltme**

1. Sıralı küme yükseltme işlemini yürütmek için [burada](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) bahsedilen adımları izleyin. 
2. Kümeye yeni ana bilgisayar eklendikten sonra, VMM aracısını bu güncelleştirilmiş konağa yüklemek için SCVMM konsolundan Konağı yenileyin.
3. Sanal makinelerin VM sürümlerini güncelleştirmek için *Update-VMVersion* komutunu yürütün. 
4.  Azure portal gidin ve kurtarma hizmetleri kasasının içindeki sanal makinelerin çoğaltılan sistem durumunu doğrulayın. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>Windows Server 2012 R2 Konakları yükseltme, yüksek oranda kullanılabilir SCVMM 2012 R2 sunucusu tarafından yönetilir
Windows sever 2012 R2 konaklarınızı yükseltmeden önce, SCVMM 2012 R2 'yi SCVMM 2016 sürümüne yükseltmeniz gerekir. Aşağıdaki yükseltme modları, ek VMM sunucularıyla karma mod & ek VMM sunucusu olmadan Azure Site Recovery karma mod ile yapılandırılan SCVMM 2012 R2 sunucuları yükseltilirken desteklenir.

**SCVMM 2012 R2 'yi SCVMM 2016 'ye yükseltme**

1.  Denetim Masası-> Programlar-> Programlar ve Özellikler-> Microsoft Azure Site Recovery ' a giderek ASR sağlayıcısını kaldırın ve Kaldır ' a tıklayın.
2. Yürütmek istediğiniz yükseltme moduna göre [burada](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server) bahsedilen adımları izleyin.
3. SCVMM konsolunu başlatın ve **yapılar** sekmesinin altındaki her bir konağın durumunu denetleyin. en son durumu almak için **Yenile** 'ye tıklayın. Durumu "Ilgilenilmesi gerekiyor" olarak görmeniz gerekir.
4. En son [Microsoft Azure Site Recovery SAĞLAYıCıSıNı](https://aka.ms/downloaddra) SCVMM 'ye yükler.
5. Kümenin her bir konağında en son [Microsoft Azure kurtarma hizmeti (mars) aracısını](https://aka.ms/latestmarsagent) güncelleştirin. SC VMM 'nin Konakları başarıyla sorgulayabilmesini sağlamak için yenileyin.


**Windows Server 2012 R2 konaklarınızı Windows Server 2016 ' ye yükseltme**

1. Sıralı küme yükseltme işlemini yürütmek için [burada](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) bahsedilen adımları izleyin.
2. Kümeye yeni ana bilgisayar eklendikten sonra, VMM aracısını bu güncelleştirilmiş konağa yüklemek için SCVMM konsolundan Konağı yenileyin.
3. Sanal makinelerin VM sürümlerini güncelleştirmek için *Update-VMVersion* komutunu yürütün. 
4.  Azure portal gidin ve kurtarma hizmetleri kasasının içindeki sanal makinelerin çoğaltılan sistem durumunu doğrulayın. 

## <a name="next-steps"></a>Sonraki adımlar
Konakları yükseltme işlemi gerçekleştirildikten sonra, çoğaltma ve olağanüstü durum kurtarma durumlarınızın sistem durumunu test etmek için bir [Yük devretme testi](tutorial-dr-drill-azure.md) gerçekleştirebilirsiniz.

