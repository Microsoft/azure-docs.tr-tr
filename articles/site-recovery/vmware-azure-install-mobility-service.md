---
title: VMware VM 'lerini ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarmaya yönelik anında yükleme yoluyla Mobility hizmetini yüklemeye yönelik kaynak makineleri hazırlama | Microsoft Docs
description: Azure Site Recovery hizmetini kullanarak, VMware VM 'Leri ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarma için göndererek yükleme yoluyla sunucunuzu Mobility Aracısı 'nı yüklemeye nasıl hazırlayacağınızı öğrenin.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: f1a96302d180f3b4b179f42013232f3b48d4e2b0
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69016359"
---
# <a name="prepare-source-machine-for-push-installation-of-mobility-agent"></a>Mobility aracısının gönderim yüklemesi için kaynak makineyi hazırlama

[Azure Site Recovery](site-recovery-overview.md)kullanarak VMware VM 'leri ve fiziksel sunucular için olağanüstü durum kurtarmayı ayarlarken, her şirket içi VMware VM 'sine ve fiziksel sunucuya [Site Recovery Mobility hizmetini](vmware-physical-mobility-service-overview.md) yüklersiniz.  Mobility hizmeti makinede veri yazmaları yakalar ve bunları Site Recovery işlem sunucusuna iletir.

## <a name="install-on-windows-machine"></a>Windows makinesine yüklensin

Korumak istediğiniz her bir Windows makinesinde şunları yapın:

1. Makine ve işlem sunucusu arasında ağ bağlantısı olduğundan emin olun. Ayrı bir işlem sunucusu ayarlamadıysanız, varsayılan olarak yapılandırma sunucusunda çalışır.
1. İşlem sunucusunun bilgisayara erişmek için kullanabileceği bir hesap oluşturun. Hesabın yerel veya etki alanı yönetici haklarına sahip olması gerekir. Bu hesabı yalnızca anında yükleme ve aracı güncelleştirmeleri için kullanın.
2. Bir etki alanı hesabı kullanmıyorsanız, yerel bilgisayarda Uzak Kullanıcı erişim denetimini şu şekilde devre dışı bırakın:
    - HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System kayıt defteri anahtarı altında yeni bir DWORD ekleyin: **LocalAccountTokenFilterPolicy**. Değeri **1**olarak ayarlayın.
    -  Bunu bir komut isteminde yapmak için aşağıdaki komutu çalıştırın:  
   ' REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System/v LocalAccountTokenFilterPolicy/t REG_DWORD/d
3. Korumak istediğiniz makinedeki Windows Güvenlik Duvarı ' nda, **güvenlik duvarı aracılığıyla bir uygulamaya veya özelliğe Izin ver**' i seçin. **Dosya ve yazıcı paylaşımını** ve **Windows Yönetim Araçları (WMI)** etkinleştirin. Bir etki alanına ait bilgisayarlar için, güvenlik duvarı ayarlarını bir grup ilkesi nesnesi (GPO) kullanarak yapılandırabilirsiniz.

   ![Güvenlik Duvarı ayarları](./media/vmware-azure-install-mobility-service/mobility1.png)

4. Oluşturduğunuz hesabı CSPSConfigtool içine ekleyin. Bunu yapmak için yapılandırma sunucunuzda oturum açın.
5. **cspsconfigtool.exe** dosyasını açın. Bu, masaüstünde ve%ProgramData%\home\svsystems\bin klasöründe bir kısayol olarak kullanılabilir.
6. **Hesapları Yönet** sekmesinde **Hesap Ekle**' yi seçin.
7. Oluşturduğunuz hesabı ekleyin.
8. Bir bilgisayar için çoğaltmayı etkinleştirdiğinizde kullandığınız kimlik bilgilerini girin.

## <a name="install-on-linux-machine"></a>Linux makinesine yüklensin

Korumak istediğiniz her Linux makinesinde şunları yapın:

1. Linux makinesi ile işlem sunucusu arasında ağ bağlantısı olduğundan emin olun.
2. İşlem sunucusunun bilgisayara erişmek için kullanabileceği bir hesap oluşturun. Hesap, kaynak Linux sunucusu üzerindeki bir **kök** kullanıcı olmalıdır. Bu hesabı yalnızca anında yükleme ve güncelleştirmeler için kullanın.
3. Kaynak Linux sunucusundaki /etc/hosts dosyasında, yerel ana bilgisayar adını tüm ağ bağdaştırıcıları ile ilişkili IP adreslerine eşleyen girişler olduğunu denetleyin.
4. Çoğaltmak istediğiniz bilgisayara en son openssh, openssh-server, openssl paketlerini yükleyin.
5. Secure Shell’in (SSH) etkin olduğundan ve bağlantı noktası 22’de çalıştırıldığından emin olun.
4. Sshd_config dosyasında SFTP alt sistemi ve parola kimlik doğrulamasını etkinleştirin. Bunu yapmak için **kök**olarak oturum açın.
5. **/Etc/ssh/sshd_config** dosyasında, **passwordaduthentication**ile başlayan satırı bulun.
6. Satırın açıklamasını kaldırın ve değeri **Evet**olarak değiştirin.
7. **Alt sistem**ile başlayan satırı bulun ve satırın açıklamasını kaldırın.

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. **sshd** hizmetini yeniden başlatın.
9. Oluşturduğunuz hesabı CSPSConfigtool içine ekleyin. Bunu yapmak için yapılandırma sunucunuzda oturum açın.
10. **cspsconfigtool.exe** dosyasını açın. Bu, masaüstünde ve%ProgramData%\home\svsystems\bin klasöründe bir kısayol olarak kullanılabilir.
11. **Hesapları Yönet** sekmesinde **Hesap Ekle**' yi seçin.
12. Oluşturduğunuz hesabı ekleyin.
13. Bir bilgisayar için çoğaltmayı etkinleştirdiğinizde kullandığınız kimlik bilgilerini girin.

## <a name="anti-virus-on-replicated-machines"></a>Çoğaltılan makinelerde virüsten koruma

Çoğaltmak istediğiniz makinelerin etkin bir virüsten koruma yazılımı çalışıyorsa, Mobility hizmeti yükleme klasörünü virüsten koruma işlemlerinden (*C:\programdata\asr\agent*) dıştığınızdan emin olun. Bu, çoğaltmanın beklendiği gibi çalıştığından emin olmanızı sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Mobility hizmeti yüklendikten sonra, bu VM 'Leri korumaya başlamak için Azure portal **+ Çoğalt** ' ı seçin. [VMware VM 'leri](vmware-azure-enable-replication.md) ve [fiziksel sunucular](physical-azure-disaster-recovery.md#enable-replication)için çoğaltmayı etkinleştirme hakkında daha fazla bilgi edinin.


