---
title: Azure 'da Red Hat Enterprise Linux Paceyapıcısı ayarlama | Microsoft Docs
description: Azure 'da Red Hat Enterprise Linux Paceyapıcısı ayarlama
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/17/2018
ms.author: sedusch
ms.openlocfilehash: 954ff23997e56249859dd8d35f124324432f2b22
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672993"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Azure 'da Red Hat Enterprise Linux Paceyapıcısı ayarlama

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


Önce aşağıdaki SAP notlarını ve kağıtları okuyun:

* SAP Note [1928533], şunları içerir:
  * SAP yazılımının dağıtımı için desteklenen Azure VM boyutlarının listesi.
  * Azure VM boyutları için önemli kapasite bilgileri.
  * Desteklenen SAP yazılımı ve işletim sistemi (OS) ve veritabanı birleşimleri.
  * Microsoft Azure üzerinde Windows ve Linux için gereken SAP çekirdek sürümü.
* SAP Note [2015553] , Azure 'da SAP tarafından desteklenen SAP yazılım dağıtımları için önkoşulları listeler.
* SAP Note [2002167] Red Hat Enterprise Linux için önerilen işletim sistemi ayarlarına sahiptir
* SAP Note [2009879] , Red Hat Enterprise Linux Için SAP HANA yönergelerine sahiptir
* SAP Note [2178632] , Azure 'da SAP için raporlanan tüm izleme ölçümleriyle ilgili ayrıntılı bilgiler içerir.
* SAP Note [2191498] , Azure 'da Linux IÇIN gereken SAP konak Aracısı sürümüne sahiptir.
* SAP Note [2243692] , Azure 'da LINUX üzerinde SAP lisanslama hakkında bilgi içerir.
* SAP Note [1999351] , SAP Için Azure Gelişmiş izleme uzantısı için ek sorun giderme bilgilerine sahiptir.
* [SAP COMMUNITY WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 'nin Linux için gereklı tüm sap notları vardır.
* [Linux 'ta SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [Linux 'ta SAP için Azure sanal makineleri dağıtımı (Bu makale)][deployment-guide]
* [Linux üzerinde SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
* [Pacemaker kümesinde sistem çoğaltmasını SAP HANA](https://access.redhat.com/articles/3004101)
* Genel RHEL belgeleri
  * [Yüksek kullanılabilirlik eklentisi genel bakış](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Yüksek kullanılabilirlik eklentisi Yönetimi](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Yüksek kullanılabilirlik eklentisi başvurusu](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [RHEL yüksek kullanılabilirlik kümeleri için destek Ilkeleri-SBD ve fence_sbd](https://access.redhat.com/articles/2800691)
* Azure 'a özgü RHEL belgeleri:
  * [RHEL yüksek kullanılabilirlik kümeleri için destek Ilkeleri-küme üyesi olarak Microsoft Azure Sanal Makineler](https://access.redhat.com/articles/3131341)
  * [Microsoft Azure üzerinde Red Hat Enterprise Linux 7,4 (ve üzeri) yüksek kullanılabilirlik kümesi yükleme ve yapılandırma](https://access.redhat.com/articles/3252491)
  * [RHEL 7,6 üzerinde pacemaker 'da tek başına sıraya alma sunucu 2 (ENSA2) ile SAP S/4HANA yoks/ERS yapılandırma](https://access.redhat.com/articles/3974941)

## <a name="cluster-installation"></a>Küme yükleme

![RHEL 'de pacemaker genel bakış](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

> [!NOTE]
> Red hat, yazılım ile öykünülmüş izleme 'yi desteklemez. Red hat, bulut platformlarında SBD 'yi desteklemez. Ayrıntılar için bkz. [RHEL yüksek kullanılabilirlik kümeleri Için destek ilkeleri-SBD ve fence_sbd](https://access.redhat.com/articles/2800691).
> Azure 'daki pacemaker Red Hat Enterprise Linux kümeleri için desteklenen tek dizin oluşturma mekanizması, Azure çit aracısıdır.  

Aşağıdaki öğeler ile önek **[A]** - tüm düğümler için geçerli **[1]** - düğüm 1 yalnızca uygulanabilir veya **[2]** - yalnızca düğüm 2 için geçerlidir.

1. **[A]** kayıt

   Sanal makinelerinizi kaydedin ve RHEL 7 için depoları içeren bir havuza bağlayın.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Bir Azure Marketi PAYG RHEL görüntüsüne bir havuz iliştirerek, RHEL kullanımınız için etkin bir şekilde çift faturalandırılacaksınız: PAYG görüntüsü için bir kez ve eklediğiniz havuzdaki RHEL yetkilendirmelerinin bir kez. Bunu azaltmak için, Azure artık KCG RHEL görüntüleri sağlamaktadır. Daha fazla bilgiye [buradan](https://aka.ms/rhel-byos)ulaşabilirsiniz.

1. **[A]** SAP depoları için RHEL 'yi etkinleştirme

   Gerekli paketleri yüklemek için aşağıdaki depoları etkinleştirin.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** RHEL ha eklentisini Install

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > Müşterilerin daha hızlı bir yük devretme zamanından faydalanmasını sağlamak için aşağıdaki Azure sınır Aracısı (veya üzeri) sürümlerini öneririz. bir kaynak durmazsa veya küme düğümleri bundan böyle iletişim kuramadıysanız:  
   > RHEL 7,6: Fence-Agents-4.2.1 -11. EL7 _ 6.8  
   > RHEL 7,5: Fence-Agents-4.0.11 -86. EL7 _ 5.8  
   > RHEL 7,4: Fence-Agents-4.0.11 -66. EL7 _ 4.12  
   > Daha fazla bilgi için, bkz. [Azure VM 'nin BIR RHEL yüksek kullanılabilirlik kümesi üyesi olarak çalışıyor olması çok uzun zaman alır veya VM kapatılmadan önce başarısız/zaman aşımına](https://access.redhat.com/solutions/3408711)uğrar.

   Azure çit aracısının sürümünü denetleyin. Gerekirse, yukarıda belirtilen değere eşit veya ondan daha yeni bir sürüme güncelleştirin.

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Azure sınır Aracısı 'nı güncelleştirmeniz gerekiyorsa ve özel rol kullanıyorsanız, özel rolü eylem **poweroff**içerecek şekilde güncelleştirdiğinizden emin olun. Ayrıntılar için bkz. sınır [Aracısı için özel rol oluşturma](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker#1-create-a-custom-role-for-the-fence-agent).  

1. **[A]**  Kurulum ana bilgisayar adı çözümlemesi

   Bir DNS sunucusu kullanabilir veya/etc/hosts tüm düğümlerde değiştirin. Bu örnek/Etc/Hosts dosyasının nasıl kullanılacağını gösterir.
   IP adresi ve aşağıdaki komutlarda bulunan ana bilgisayar adını değiştirin. / Etc/hosts kullanmanın avantajı, kümenizin bir tek hata noktası çok olabilir DNS bağımsız olur.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   / Etc/hosts aşağıdaki satırları ekleyin. IP adresi ve ana bilgisayar adını, ortamınızla eşleşecek şekilde değiştirin.

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]**  Aynı parolayı hacluster parolasını değiştirme

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** pacemaker için güvenlik duvarı kuralları ekleme

   Küme düğümleri arasındaki tüm küme iletişimine aşağıdaki güvenlik duvarı kurallarını ekleyin.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** temel küme hizmetlerini etkinleştirme

   Paceoluşturucu hizmetini etkinleştirmek ve başlatmak için aşağıdaki komutları çalıştırın.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** pacemaker kümesi oluşturma

   Düğümlerin kimliğini doğrulamak ve kümeyi oluşturmak için aşağıdaki komutları çalıştırın. Belleği Bakımı korumak için belirteci 30000 olarak ayarlayın. Daha fazla bilgi için [Linux için bu makaleye][virtual-machines-linux-maintenance]bakın.

   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all

   # Run the following command until the status of both nodes is online
   sudo pcs status

   # Cluster name: nw1-azr
   # WARNING: no stonith devices and stonith-enabled is not false
   # Stack: corosync
   # Current DC: <b>prod-cl1-1</b> (version 1.1.18-11.el7_5.3-2b07d5c5a9) - partition with quorum
   # Last updated: Fri Aug 17 09:18:24 2018
   # Last change: Fri Aug 17 09:17:46 2018 by hacluster via crmd on <b>prod-cl1-1</b>
   #
   # 2 nodes configured
   # 0 resources configured
   #
   # Online: [ <b>prod-cl1-0</b> <b>prod-cl1-1</b> ]
   #
   # No resources
   #
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]** beklenen oyları ayarla

   <pre><code>sudo pcs quorum expected-votes 2
   </code></pre>

## <a name="create-stonith-device"></a>STONITH cihazı oluşturma

STONITH cihaz, Microsoft Azure karşı korunmasına yetki vermek için bir hizmet sorumlusu kullanır. Bir hizmet sorumlusu oluşturmak için aşağıdaki adımları izleyin.

1. Şuraya gidin: <https://portal.azure.com>
1. Azure Active Directory dikey penceresini açın  
   Özellikler bölümüne gidin ve dizin kimliği yazma Bu **Kiracı kimliği**.
1. Uygulama kayıtları tıklayın
1. Yeni kayıt öğesine tıklayın
1. Bir ad girin, "yalnızca bu kuruluş dizinindeki hesaplar" ı seçin 
2. "Web" uygulama türünü seçin, bir oturum açma URL 'si girin (örneğin, http:\//localhost) ve Ekle ' ye tıklayın.  
   Oturum açma URL'si kullanılmaz ve geçerli bir URL olabilir
1. Sertifikalar ve gizlilikler ' ı seçin ve ardından yeni istemci parolası ' na tıklayın
1. Yeni anahtar için bir açıklama girin, "süresiz Expires" öğesini seçin ve Ekle ' ye tıklayın.
1. Değeri yazın. Olarak kullanılan **parola** için hizmet sorumlusu
1. Genel Bakış ' ı seçin. Uygulama Kimliği yazma Kullanıcı adı olarak kullanılır (**oturum açma kimliği** sonraki adımlarda), hizmet sorumlusu

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Sınır aracısı için özel bir rol oluşturun

Hizmet sorumlusu kullanarak Azure kaynaklarınızı varsayılan olarak erişim izni yok. Kümenin tüm sanal makinelerini başlatmak ve durdurmak (kapatmak) için hizmet sorumlusu izinleri vermeniz gerekir. Özel rol zaten oluşturmadıysanız, bunu kullanarak oluşturabilirsiniz [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) veya [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)

Giriş dosyası için aşağıdaki içeriği kullanın. İhtiyacınız olan içeriği için aboneliklerinizi uyum, c276fc76-9cd4-44c9-99a7-4fd71546436e ve e91d47c4-76f3-4271-a796-21b4ecfe3624 aboneliğinizin kimliği ile değiştirin. İkinci girdi, yalnızca bir aboneliğiniz varsa, ın AssignableScopes içinde kaldırın.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** hizmet sorumlusuna özel rol atama

Özel rol "Linux sınır aracısı hizmet sorumlusuna son bölümde oluşturduğunuz rolü" atayın. Sahip rolü artık kullanmayın!

1. Şuraya gidin: https://portal.azure.com
1. Tüm kaynaklar dikey penceresini açın
1. İlk küme düğümüne sanal makinesini seçin
1. Erişim denetimi (IAM)'ye tıklayın.
1. Ekle rol ataması
1. "Linux sınır Aracısı rolü" rolü seçin
1. Yukarıda oluşturduğunuz uygulamanın adını girin
1. Kaydet’e tıklayın.

İkinci küme düğümü için yukarıdaki adımları yineleyin.

### <a name="1-create-the-stonith-devices"></a>**[1]**  STONITH cihazları oluşturun

Sanal makineler için izinleri düzenleme sonra kümedeki STONITH cihazları yapılandırabilirsiniz.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

Çit cihazını yapılandırmak için aşağıdaki komutu kullanın.

> [!NOTE]
> ' Pcmk_host_map ' seçeneği yalnızca, RHEL ana bilgisayar adları ve Azure düğüm adları özdeş DEĞILSE komutta gereklidir. Komutun kalın bölümüne bakın.

<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> power_timeout=240 pcmk_reboot_timeout=900</code></pre>

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** bir STONITH cihazının kullanımını etkinleştirme

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

## <a name="next-steps"></a>Sonraki adımlar

* [SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [SAP için Azure sanal makineleri dağıtımı][deployment-guide]
* [SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
* Azure VM 'lerinde SAP HANA olağanüstü durum kurtarma için yüksek kullanılabilirlik ve plan planı oluşturma hakkında bilgi edinmek için bkz. [Azure sanal makinelerinde (VM) SAP HANA yüksek kullanılabilirliği][sap-hana-ha]
