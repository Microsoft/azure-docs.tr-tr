---
title: Azure NetApp Files için bir çift protokol (NFSv3 ve SMB) birimi oluşturun | Microsoft Docs
description: LDAP kullanıcı eşlemesi desteğiyle NFSv3 ve SMB 'nin ikili protokolünü kullanan bir birimin nasıl oluşturulduğunu açıklar.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/19/2021
ms.author: b-juche
ms.openlocfilehash: c702c41228512eceebeaf45ccae709db38a85a51
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725693"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Azure NetApp Files için bir çift protokol (NFSv3 ve SMB) birimi oluşturun

Azure NetApp Files, NFS (NFSv3 ve NFSv 4.1), SMB3 veya Dual Protocol kullanarak birim oluşturmayı destekler. Bu makalede, LDAP kullanıcı eşlemesi desteğiyle NFSv3 ve SMB 'nin ikili protokolünü kullanan bir birimin nasıl oluşturulacağı gösterilmektedir. 

NFS birimleri oluşturmak için bkz. [NFS birimi oluşturma](azure-netapp-files-create-volumes.md). SMB birimleri oluşturmak için bkz. [SMB birimi oluşturma](azure-netapp-files-create-volumes-smb.md). 

## <a name="before-you-begin"></a>Başlamadan önce 

* Zaten bir kapasite havuzu oluşturmuş olmanız gerekir.  
    Bkz. [Kapasite havuzu ayarlama](azure-netapp-files-set-up-capacity-pool.md).   
* Azure NetApp Files için bir alt ağ atanmış olmalıdır.  
    [Azure NetApp Files için bir alt ağ temsilcisine](azure-netapp-files-delegate-subnet.md)bakın.

## <a name="considerations"></a>Dikkat edilmesi gerekenler

* [Active Directory bağlantıları Için gereksinimleri](create-active-directory-connections.md#requirements-for-active-directory-connections)karşıladığınızdan emin olun. 
* DNS sunucusunda bir geriye doğru arama bölgesi oluşturun ve ardından bu geriye doğru arama bölgesine AD ana makinesi için bir işaretçi (PTR) kaydı ekleyin. Aksi halde, çift protokol birimi oluşturma işlemi başarısız olur.
* NFS istemcisinin güncel olduğundan ve işletim sistemi için en son güncelleştirmeleri çalıştırdığından emin olun.
* AD üzerinde Active Directory (AD) LDAP sunucusunun açık ve çalışıyor olduğundan emin olun. Bunu, AD makinesine [Active Directory Basit Dizin Hizmetleri (AD LDS)](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) rolünü yükleyip yapılandırarak yapabilirsiniz.
* Çift protokol birimleri Şu anda Azure Active Directory Domain Services desteklemez (AEKLEMELERI). AEKLEMELERI kullanıyorsanız TLS üzerinden LDAP etkinleştirilmemelidir.
* Bir çift protokol birimi tarafından kullanılan NFS sürümü NFSv3 ' dir. Bu nedenle, aşağıdaki önemli noktalar geçerlidir:
    * İkili protokol, NFS istemcilerinden gelen Windows ACL genişletilmiş özniteliklerini desteklemez `set/get` .
    * NFS istemcileri NTFS güvenlik stili için izinleri değiştiremezler ve Windows istemcileri UNIX stili çift protokol birimlerinin izinlerini değiştiremezler.   

    Aşağıdaki tabloda güvenlik stilleri ve etkileri açıklanmaktadır:  
    
    | Güvenlik stili    | İzinleri değiştirebilecek istemciler   | İstemcilerin kullanabileceği izinler  | Ortaya çıkan etkin güvenlik stili    | Dosyalara erişebilen istemciler     |
    |-  |-  |-  |-  |-  |
    | `Unix`    | NFS   | NFSv3 modu bitleri   | UNIX  | NFS ve Windows   |
    | `Ntfs`    | Windows   | NTFS ACL 'Leri     | NTFS  |NFS ve Windows|
* NFS kullanarak NTFS güvenlik stili birimini bağlama UNIX kullanıcıları, `root` UNIX Için Windows kullanıcısı `root` ve `pcuser` diğer tüm kullanıcılar için doğrulanır. Bu Kullanıcı hesaplarının, NFS kullanırken birimi bağlamadan önce Active Directory bulunduğundan emin olun. 
* Büyük topolojiniz varsa ve `Unix` güvenlik stilini bir çift protokol birimiyle veya genişletilmiş gruplar Ile LDAP ile kullanıyorsanız, topolojinizdeki tüm sunuculara erişemeyebilirsiniz Azure NetApp Files.  Bu durum oluşursa, yardım için hesap ekibinize başvurun.  <!-- NFSAAS-15123 --> 
* Çift protokol birimi oluşturmak için sunucu kök CA sertifikasına ihtiyacınız yoktur. Yalnızca TLS üzerinden LDAP etkinleştirildiğinde gereklidir.


## <a name="create-a-dual-protocol-volume"></a>Çift protokollü birim oluşturma

1.  Kapasite havuzları dikey penceresinden **birimler** dikey penceresine tıklayın. Birim oluşturmak için **+ Birim ekle**'ye tıklayın. 

    ![Birimlere git](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  Birim Oluştur penceresinde **Oluştur**' a tıklayın ve temel bilgiler sekmesinde aşağıdaki alanlar için bilgi sağlayın:   
    * **Birim adı**      
        Oluşturmakta olduğunuz birim için ad belirtin.   

        Birim adı her bir kapasite havuzu içinde benzersiz olmalıdır. En az üç karakter uzunluğunda olmalıdır. Herhangi bir alfasayısal karakter kullanabilirsiniz.   

        `default` `bin` Birim adı olarak veya kullanamazsınız.

    * **Kapasite havuzu**  
        Birimin oluşturulmasını istediğiniz kapasite havuzunu belirtin.

    * **Kotasının**  
        Birime ayrılmış mantıksal depolama miktarını belirtin.  

        **Kullanılabilir kota** alanı, yeni birimi oluştururken kullanabildiğiniz, seçilen kapasite havuzundaki kullanılmamış alan miktarını gösterir. Yeni birimin boyutu kullanılabilir kotayı aşamaz.  

    * **Aktarım hızı (MIB/S)**   
        Birim el ile QoS kapasite havuzunda oluşturulduysa, birim için istediğiniz aktarım hızını belirtin.   

        Birim bir otomatik QoS kapasite havuzunda oluşturulduysa, bu alanda görünen değer (Kota x hizmet düzeyi aktarım hızı) olur.   

    * **Sanal ağ**  
        Birime erişmek istediğiniz Azure sanal ağını (VNet) belirtin.  

        Belirttiğiniz VNET Azure NetApp Files için bir alt ağa sahip olmalıdır. Azure NetApp Files hizmetine yalnızca aynı VNET 'ten veya VNET eşlemesi ile aynı bölgedeki bir VNET 'ten erişilebilir. Ayrıca, hızlı rota aracılığıyla şirket içi ağınızdan birime da erişebilirsiniz.   

    * **Alt ağ**  
        Birim için kullanmak istediğiniz alt ağı belirtin.  
        Belirttiğiniz alt ağ Azure NetApp Files için temsilci atanmış olmalıdır. 
        
        Bir alt ağ temsilcisi yoksa, birim oluştur sayfasında **Yeni oluştur** ' a tıklayabilirsiniz. Sonra alt ağ oluştur sayfasında alt ağ bilgilerini belirtin ve alt ağın Azure NetApp Files için temsilci olarak **Microsoft. NetApp/birimler** ' i seçin. Her VNET 'te Azure NetApp Files için yalnızca bir alt ağ atanabilir.   
 
        ![Birim oluşturun](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Alt ağ oluşturma](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Birime var olan bir anlık görüntü ilkesi uygulamak istiyorsanız, genişletmek için **Gelişmiş bölümü göster** ' e tıklayın, anlık görüntü yolunu gizlemek isteyip istemediğinizi belirtin ve açılır menüden bir anlık görüntü ilkesi seçin. 

        Anlık görüntü ilkesi oluşturma hakkında daha fazla bilgi için bkz. [anlık görüntü Ilkelerini yönetme](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Gelişmiş seçimi göster](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. **Protokol**' e tıklayın ve ardından aşağıdaki eylemleri tamamlamayı seçin:  
    * Birimin protokol türü olarak **çift protokol (NFSv3 ve SMB)** seçeneğini belirleyin.   

    * Birimin **birim yolunu** belirtin.   
    Bu birim yolu, Paylaşılan birimin adıdır. Ad alfabetik bir karakterle başlamalıdır ve her abonelik ve her bölge içinde benzersiz olmalıdır.  

    * Kullanılacak **güvenlik stilini** BELIRTIN: NTFS (varsayılan) veya UNIX.

    * Çift protokol birimi için SMB3 protokol şifrelemesini etkinleştirmek istiyorsanız, **SMB3 protokol şifrelemesini etkinleştir**' i seçin.   

        Bu özellik yalnızca uçuş SMB3 verilerinde şifrelemeyi mümkün bir şekilde sunar. NFSv3-uçuş verilerini şifrelemez. SMB3 şifrelemesi kullanmayan SMB istemcileri bu birime erişemeyecektir. Bekleyen veriler, bu ayardan bağımsız olarak şifrelenir. Ek bilgi için bkz. [SMB şifreleme SSS](azure-netapp-files-faqs.md#smb-encryption-faqs) . 

        **SMB3 Protokolü şifreleme** özelliği şu anda önizlemededir. Bu özelliği ilk kez kullanıyorsanız, özelliği kullanmadan önce kaydedin: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```

        Özellik kaydının durumunu denetleyin: 

        > [!NOTE]
        > **Registrationstate** , ' a `Registering` değiştirilmeden önce 60 dakikaya kadar bir durumda olabilir `Registered` . Devam etmeden önce durum olana kadar bekleyin `Registered` .

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```
        
        Ayrıca, [Azure CLI komutlarını](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) kullanarak `az feature register` `az feature show` özelliği kaydedebilir ve kayıt durumunu görüntüleyebilirsiniz.  

    * İsteğe bağlı olarak, [birim için dışa aktarma ilkesi yapılandırın](azure-netapp-files-configure-export-policy.md).

    ![Çift protokol belirtin](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. Birim ayrıntılarını gözden geçirmek için **gözden geçir + oluştur** ' a tıklayın. Birimi oluşturmak için **Oluştur** ' a tıklayın.

    Oluşturduğunuz birim birimler sayfasında görünür. 
 
    Birim, kapasite havuzundan aboneliği, kaynak grubunu ve konum özniteliklerini devralır. Birimin dağıtım durumunu izlemek için Bildirimler sekmesini kullanabilirsiniz.

## <a name="allow-local-nfs-users-with-ldap-to-access-a-dual-protocol-volume"></a>Yerel NFS kullanıcılarının LDAP ile çift protokol birimine erişmesine izin ver 

Genişletilmiş grupları etkinleştirilmiş LDAP içeren bir çift protokol birimine erişmek için Windows LDAP sunucusunda mevcut olmayan yerel NFS istemci kullanıcılarını etkinleştirebilirsiniz. Bunu yapmak için, **Yerel NFS KULLANıCıLARıNA LDAP Ile Izin ver** seçeneğini şu şekilde etkinleştirin:

1. **Active Directory bağlantılar**' a tıklayın.  Var olan bir Active Directory bağlantısında bağlam menüsüne (üç nokta `…` ) tıklayın ve **Düzenle**' yi seçin.  

2. Görüntülenen **Active Directory Ayarları Düzenle** penceresinde, **Yerel NFS kullanıcılarına LDAP ile izin ver** seçeneğini belirleyin.  

    ![Yerel NFS kullanıcılarına LDAP ile Izin ver seçeneğini gösteren ekran görüntüsü](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  


## <a name="manage-ldap-posix-attributes"></a>LDAP POSIX özniteliklerini yönetme

UID, Ana Dizin ve diğer değerler gibi POSIX özniteliklerini, Active Directory Kullanıcıları ve bilgisayarları MMC ek bileşenini kullanarak yönetebilirsiniz.  Aşağıdaki örnek Active Directory öznitelik düzenleyicisini gösterir:  

![Active Directory öznitelik Düzenleyicisi](../media/azure-netapp-files/active-directory-attribute-editor.png) 

LDAP Kullanıcıları ve LDAP grupları için aşağıdaki öznitelikleri ayarlamanız gerekir: 
* LDAP kullanıcıları için gerekli öznitelikler:   
    `uid: Alice`, `uidNumber: 139`, `gidNumber: 555`, `objectClass: posixAccount`
* LDAP grupları için gerekli öznitelikler:   
    `objectClass: posixGroup`, `gidNumber: 555`

## <a name="configure-the-nfs-client"></a>NFS istemcisini yapılandırma 

NFS istemcisini yapılandırmak için [Azure NetApp FILES NFS Istemcisi yapılandırma](configure-nfs-clients.md) konusundaki yönergeleri izleyin.  

## <a name="next-steps"></a>Sonraki adımlar  

* [Azure NetApp Files için NFS istemcisini yapılandırma](configure-nfs-clients.md)
* [SMB veya çift protokol birimlerinde sorun giderme](troubleshoot-dual-protocol-volumes.md)
* [LDAP birimi sorunlarını giderme](troubleshoot-ldap-volumes.md)
