---
title: Azure 'da RHEL üzerinde pacemaker ayarlama | Microsoft Docs
description: Azure 'da Red Hat Enterprise Linux Paceyapıcısı ayarlama
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2021
ms.author: radeltch
ms.openlocfilehash: af8523486b42af8c0722a56bdd813d6449692c14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676894"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Azure 'da Red Hat Enterprise Linux Paceyapıcısı ayarlama

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


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
  * [Yüksek kullanılabilirlik Add-On genel bakış](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Yüksek kullanılabilirlik Add-On yönetimi](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Yüksek kullanılabilirlik Add-On başvurusu](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [RHEL yüksek kullanılabilirlik kümeleri için destek Ilkeleri-SBD ve fence_sbd](https://access.redhat.com/articles/2800691)
* Azure 'a özgü RHEL belgeleri:
  * [RHEL yüksek kullanılabilirlik kümeleri için destek Ilkeleri-küme üyesi olarak Microsoft Azure Sanal Makineler](https://access.redhat.com/articles/3131341)
  * [Microsoft Azure Red Hat Enterprise Linux 7,4 (ve üzeri) High-Availability kümesini yükleme ve yapılandırma](https://access.redhat.com/articles/3252491)
  * [RHEL 8 ' i benimseme ile ilgili konular-yüksek kullanılabilirlik ve kümeler](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/considerations_in_adopting_rhel_8/high-availability-and-clusters_considerations-in-adopting-rhel-8)
  * [RHEL 7,6 üzerinde pacemaker 'da tek başına sıraya alma sunucu 2 (ENSA2) ile SAP S/4HANA yoks/ERS yapılandırma](https://access.redhat.com/articles/3974941)
  * [Azure 'da SAP teklifleri için RHEL](https://access.redhat.com/articles/5456301)

## <a name="cluster-installation"></a>Küme yüklemesi

![RHEL 'de pacemaker genel bakış](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

> [!NOTE]
> Red hat, yazılım ile öykünülmüş izleme 'yi desteklemez. Red hat, bulut platformlarında SBD 'yi desteklemez. Ayrıntılar için bkz. [RHEL yüksek kullanılabilirlik kümeleri Için destek ilkeleri-SBD ve fence_sbd](https://access.redhat.com/articles/2800691).
> Azure 'daki pacemaker Red Hat Enterprise Linux kümeleri için desteklenen tek dizin oluşturma mekanizması, Azure çit aracısıdır.  

Şu öğeler, **[A]** ön eki olan tüm düğümlere uygulanabilir, **[1]** -yalnızca düğüm 1 veya **[2]** için geçerlidir-yalnızca node 2 için geçerlidir.

1. **[A]** kayıt. RHEL SAP HA özellikli görüntüler kullanılıyorsa bu adım gerekli değildir.  

   Sanal makinelerinizi kaydedin ve RHEL 7 için depoları içeren bir havuza bağlayın.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Bir Azure Marketi PAYG RHEL görüntüsüne bir havuz iliştirerek, RHEL kullanımınız için etkin bir şekilde iki faturalandırılır: PAYG görüntüsü için bir kez ve eklediğiniz havuzdaki RHEL yetkilendirmelerinin bir kez. Bunu azaltmak için, Azure artık KCG RHEL görüntüleri sağlamaktadır. [Burada](../redhat/byos.md) daha fazla bilgi bulabilirsiniz.  

1. **[A]** SAP depoları için RHEL 'yi etkinleştirin. RHEL SAP HA özellikli görüntüler kullanılıyorsa bu adım gerekli değildir.  

   Gerekli paketleri yüklemek için aşağıdaki depoları etkinleştirin.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** RHEL ha Add-On yüklemesi

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > Müşterilerin daha hızlı bir yük devretme zamanından faydalanmasını sağlamak için aşağıdaki Azure sınır Aracısı (veya üzeri) sürümlerini öneririz. bir kaynak durmazsa veya küme düğümleri bundan böyle iletişim kuramadıysanız:  
   > RHEL 7,7 veya üzeri, sınır aracıları paketinin kullanılabilir en son sürümünü kullanır  
   > RHEL 7,6: çit-Agents-4.2.1-11.el7_6.8  
   > RHEL 7,5: çit-Agents-4.0.11-86.el7_5.8  
   > RHEL 7,4: çit-Agents-4.0.11-66.el7_4.12  
   > Daha fazla bilgi için, bkz. [Azure VM 'nin BIR RHEL yüksek kullanılabilirlik kümesi üyesi olarak çalışıyor olması çok uzun zaman alır veya VM kapatılmadan önce başarısız/zaman aşımına](https://access.redhat.com/solutions/3408711)uğrar.

   Azure çit aracısının sürümünü denetleyin. Gerekirse, yukarıda belirtilen değere eşit veya ondan daha yeni bir sürüme güncelleştirin.

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Azure sınır Aracısı 'nı güncelleştirmeniz gerekiyorsa ve özel rol kullanıyorsanız, özel rolü eylem **poweroff** içerecek şekilde güncelleştirdiğinizden emin olun. Ayrıntılar için bkz. sınır [Aracısı için özel rol oluşturma](#1-create-a-custom-role-for-the-fence-agent).  

1. **[A]** kurulum konak adı çözümlemesi

   Bir DNS sunucusu kullanabilir veya tüm düğümlerdeki/etc/Konakları değiştirebilirsiniz. Bu örnek,/etc/hosts dosyasının nasıl kullanılacağını gösterir.
   Aşağıdaki komutlarda IP adresini ve ana bilgisayar adını değiştirin.  

   >[!IMPORTANT]
   > Küme yapılandırmasında ana bilgisayar adları kullanılıyorsa, güvenilir ana bilgisayar adı çözümlemenin olması çok önemlidir. Adlar kullanılabilir değilse ve küme yük devretme gecikmelerine yol açacağından, küme iletişimi başarısız olur.
   > /Etc/hosts kullanmanın avantajı, kümenizin DNS 'den bağımsız hale gelmesi ve tek bir başarısızlık noktası olması olabilir.  

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   /Etc/hostklasörüne aşağıdaki satırları ekleyin. IP adresini ve ana bilgisayar adını ortamınıza uyacak şekilde değiştirin

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]** hacluster parolasını aynı parolayla değiştirin

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
   
   **RHEL 7. x** üzerinde bir küme oluşturuyorsanız aşağıdaki komutları kullanın:  
   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all
   </code></pre>

   **RHEL 8. X** üzerinde bir küme oluşturuyorsanız aşağıdaki komutları kullanın:  
   <pre><code>sudo pcs host auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> totem token=30000
   sudo pcs cluster start --all
   </code></pre>

   Aşağıdaki komutu yürüterek küme durumunu doğrulayın:  
   <pre><code> # Run the following command until the status of both nodes is online
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
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]** beklenen oyları ayarla. 
   
   <pre><code># Check the quorum votes 
    pcs quorum status
    # If the quorum votes are not set to 2, execute the next command
    sudo pcs quorum expected-votes 2
   </code></pre>

   >[!TIP]
   > İkiden fazla düğüm içeren bir küme olan çok düğümlü küme oluşturuluyorsa, oyları 2 ' ye ayarlayın.    

1. **[1]** eşzamanlı çit eylemlerine izin ver

   <pre><code>sudo pcs property set concurrent-fencing=true
   </code></pre>

## <a name="create-stonith-device"></a>STONITH cihazı oluşturma

STONITH cihazı Microsoft Azure karşı yetkilendirmek için bir hizmet sorumlusu kullanır. Hizmet sorumlusu oluşturmak için bu adımları izleyin.

1. Şuraya gidin: <https://portal.azure.com>
1. Azure Active Directory dikey penceresini açın  
   Özellikler ' e gidin ve Dizin KIMLIĞINI bir yere göz önünde yapın. Bu, **KIRACı kimliğidir**.
1. Uygulama kayıtları tıklayın
1. Yeni kayıt öğesine tıklayın
1. Bir ad girin, "yalnızca bu kuruluş dizinindeki hesaplar" ı seçin 
2. "Web" uygulama türünü seçin, bir oturum açma URL 'SI girin (örneğin, http: \/ /localhost) ve Ekle ' ye tıklayın.  
   Oturum açma URL 'SI kullanılmıyor ve geçerli bir URL olabilir
1. Sertifikalar ve gizlilikler ' ı seçin ve ardından yeni istemci parolası ' na tıklayın
1. Yeni anahtar için bir açıklama girin, "süresiz Expires" öğesini seçin ve Ekle ' ye tıklayın.
1. Bir düğümü değer yapın. Hizmet sorumlusu için **parola** olarak kullanılır
1. Genel bakış'ı seçin. Uygulama KIMLIĞINI bir yere getirin. Hizmet sorumlusunun Kullanıcı adı (aşağıdaki adımlarda **oturum açma kimliği** ) olarak kullanılır

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** çit Aracısı için özel bir rol oluşturma

Hizmet sorumlusu, varsayılan olarak Azure kaynaklarınıza erişme iznine sahip değildir. Kümenin tüm sanal makinelerini başlatmak ve durdurmak (kapatmak) için hizmet sorumlusu izinleri vermeniz gerekir. Zaten özel rolü oluşturmadıysanız, [PowerShell](../../../role-based-access-control/role-assignments-powershell.md) veya [Azure CLI](../../../role-based-access-control/role-assignments-cli.md) kullanarak oluşturabilirsiniz

Giriş dosyası için aşağıdaki içeriği kullanın. İçeriği aboneliklerinize uyarlamanız gerekir, c276fc76-9cd4-44c9-99a7-4fd71546436e ve e91d47c4-76f3-4271-a796-21b4ecfe3624 ' i aboneliğinizin kimlikleriyle değiştirin. Yalnızca bir aboneliğiniz varsa, Astifblescopes ikinci girişi kaldırın.

```json
{
    "properties": {
        "roleName": "Linux Fence Agent Role",
        "description": "Allows to power-off and start virtual machines",
        "assignableScopes": [
            "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
            "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/*/read",
                    "Microsoft.Compute/virtualMachines/powerOff/action",
                    "Microsoft.Compute/virtualMachines/start/action"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** hizmet sorumlusuna özel rol atama

Son bölümde oluşturulan "Linux çit Aracısı rolü" özel rolünü hizmet sorumlusuna atayın. Sahip rolünü artık kullanmayın!

1. Şuraya gidin: https://portal.azure.com
1. Tüm kaynaklar dikey penceresini açın
1. İlk küme düğümünün sanal makinesini seçin
1. Erişim denetimi (ıAM) öğesine tıklayın
1. Rol ataması Ekle ' ye tıklayın
1. "Linux çit Aracısı rolü" rolünü seçin
1. Yukarıda oluşturduğunuz uygulamanın adını girin
1. Kaydet’e tıklayın.

İkinci küme düğümü için yukarıdaki adımları tekrarlayın.

### <a name="1-create-the-stonith-devices"></a>**[1]** STONITH cihazlarını oluşturma

Sanal makineler için izinleri düzenledikten sonra, kümedeki STONITH cihazlarını yapılandırabilirsiniz.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

> [!NOTE]
> ' Pcmk_host_map ' seçeneği yalnızca, RHEL ana bilgisayar adları ve Azure VM adları aynı DEĞILSE komutunda gereklidir. Eşlemeyi **ana bilgisayar adı: VM-adı** biçiminde belirtin.
> Komutun kalın bölümüne bakın. Daha fazla bilgi için bkz. [pcmk_host_map ' de stonıth cihazları için düğüm eşlemelerini belirtmek üzere hangi biçimi kullanmalıyım?](https://access.redhat.com/solutions/2619961)

RHEL **7. X** için, çit cihazını yapılandırmak için aşağıdaki komutu kullanın:    
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 \
op monitor interval=3600
</code></pre>

RHEL **8. X** için, çit cihazını yapılandırmak için aşağıdaki komutu kullanın:  
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm username="<b>login ID</b>" password="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 \
op monitor interval=3600
</code></pre>

> [!IMPORTANT]
> İzleme ve sınırlama işlemleri de serileştirilir. Sonuç olarak, daha uzun süre çalışan bir izleme işlemi ve eşzamanlı geçiş olayı varsa, zaten çalışan izleme işlemi nedeniyle küme yük devretmesi için gecikme yoktur.  

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** bir STONITH cihazının kullanımını etkinleştirme

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

> [!TIP]
>Azure sınır Aracısı, [Standart ILB kullanan VM 'ler Için genel uç nokta bağlantısı](./high-availability-guide-standard-load-balancer-outbound-connections.md)' nda açıklandığı gibi ortak uç noktalara giden bağlantı kurulmasını gerektirir.  

## <a name="next-steps"></a>Sonraki adımlar

* [SAP için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [SAP için Azure sanal makineleri dağıtımı][deployment-guide]
* [SAP için Azure sanal makineleri DBMS dağıtımı][dbms-guide]
* Azure VM 'lerinde SAP HANA olağanüstü durum kurtarma için yüksek kullanılabilirlik ve plan planı oluşturma hakkında bilgi edinmek için bkz. [Azure sanal makinelerinde (VM) SAP HANA yüksek kullanılabilirliği][sap-hana-ha]
