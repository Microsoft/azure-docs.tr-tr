---
title: Azure NetApp Files için bir SMB birimi oluşturun | Microsoft Docs
description: Bu makalede, Azure NetApp Files içinde nasıl SMB3 birimi oluşturacağınız gösterilmektedir. Active Directory bağlantıları ve etki alanı Hizmetleri için gereksinimler hakkında bilgi edinin.
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
ms.date: 04/20/2021
ms.author: b-juche
ms.openlocfilehash: d3ca94524c334a20f5ee75e5300ad419fa1542c5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873280"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Azure NetApp Files için SMB birimi oluşturma

Azure NetApp Files, NFS (NFSv3 ve NFSv 4.1), SMB3 veya Dual Protocol (NFSv3 ve SMB) kullanarak birim oluşturmayı destekler. Birimin kapasite kullanımı, havuzunun sağlanan kapasitesinden sayılır. 

Bu makalede, SMB3 birimi oluşturma konusu gösterilmektedir. NFS birimleri için bkz. [NFS birimi oluşturma](azure-netapp-files-create-volumes.md). Çift protokol birimleri için bkz. [çift protokol birimi oluşturma](create-volumes-dual-protocol.md).

## <a name="before-you-begin"></a>Başlamadan önce 

* Zaten bir kapasite havuzu ayarlamış olmalısınız. Bkz. [Kapasite havuzu ayarlama](azure-netapp-files-set-up-capacity-pool.md).     
* Azure NetApp Files için bir alt ağ atanmış olmalıdır. [Azure NetApp Files için bir alt ağ temsilcisine](azure-netapp-files-delegate-subnet.md)bakın.

## <a name="configure-active-directory-connections"></a>Active Directory bağlantıları yapılandırma 

SMB birimi oluşturmadan önce bir Active Directory bağlantısı oluşturmanız gerekir. Azure NetApp dosyaları için Active Directory bağlantıları yapılandırmadıysanız, [Active Directory bağlantıları oluşturma ve yönetme](create-active-directory-connections.md)bölümünde açıklanan yönergeleri izleyin.

## <a name="add-an-smb-volume"></a>SMB birimi ekleme

1. Kapasite havuzları dikey penceresinden **birimler** dikey penceresine tıklayın. 

    ![Birimlere git](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Birim oluşturmak için **+ Birim ekle**'ye tıklayın.  
    Birim oluştur penceresi görüntülenir.

3. Birim Oluştur penceresinde **Oluştur** ' a tıklayın ve temel bilgiler sekmesinde aşağıdaki alanlar için bilgi sağlayın:   
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

        Belirttiğiniz VNet Azure NetApp Files için bir alt ağa sahip olmalıdır. Azure NetApp Files hizmetine yalnızca aynı VNet 'ten veya VNet eşlemesi ile aynı bölgedeki bir VNet 'ten erişilebilir. Ayrıca, hızlı rota aracılığıyla şirket içi ağınızdan birime da erişebilirsiniz.   

    * **Alt ağ**  
        Birim için kullanmak istediğiniz alt ağı belirtin.  
        Belirttiğiniz alt ağ Azure NetApp Files için temsilci atanmış olmalıdır. 
        
        Bir alt ağ temsilcisi yoksa, birim oluştur sayfasında **Yeni oluştur** ' a tıklayabilirsiniz. Sonra alt ağ oluştur sayfasında alt ağ bilgilerini belirtin ve alt ağın Azure NetApp Files için temsilci olarak **Microsoft. NetApp/birimler** ' i seçin. Her VNet 'te Azure NetApp Files için yalnızca bir alt ağ atanabilir.   
 
        ![Birim oluşturun](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Alt ağ oluşturma](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Birime var olan bir anlık görüntü ilkesi uygulamak istiyorsanız, genişletmek için **Gelişmiş bölümü göster** ' e tıklayın, anlık görüntü yolunu gizlemek isteyip istemediğinizi belirtin ve açılır menüden bir anlık görüntü ilkesi seçin. 

        Anlık görüntü ilkesi oluşturma hakkında daha fazla bilgi için bkz. [anlık görüntü Ilkelerini yönetme](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Gelişmiş seçimi göster](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. **Protokol** ' e tıklayın ve aşağıdaki bilgileri doldurun:  
    * Birimin protokol türü olarak **SMB** ' yi seçin. 
    * Açılır listeden **Active Directory** bağlantınızı seçin.
    * **Paylaşım adı**' nda paylaşılan birimin adını belirtin.
    * SMB3 için şifrelemeyi etkinleştirmek istiyorsanız, **SMB3 protokol şifrelemesini etkinleştir**' i seçin.   
        Bu özellik, uçuş SMB3 verileri için şifrelemeyi mümkün bir şekilde sunar. SMB3 şifrelemesi kullanmayan SMB istemcileri bu birime erişemeyecektir.  Bekleyen veriler, bu ayardan bağımsız olarak şifrelenir.  
        Ek bilgi için bkz. [SMB şifreleme SSS](azure-netapp-files-faqs.md#smb-encryption-faqs) . 

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
    * SMB birimi için sürekli kullanılabilirliği etkinleştirmek istiyorsanız **sürekli kullanılabilirliği etkinleştir**' i seçin.    

        > [!IMPORTANT]   
        > SMB sürekli kullanılabilirliği özelliği şu anda genel önizlemededir. **[Azure NetApp Files SMB sürekli kullanılabilirlik paylaşımları genel önizleme eklenebileceğinizi gönderim sayfası](https://aka.ms/anfsmbcasharespreviewsignup)** aracılığıyla özelliğe erişmek için bir eklenebileceğinizi isteği göndermeniz gerekir. Sürekli kullanılabilirlik özelliğini kullanmadan önce Azure NetApp Files ekibinden resmi bir onay e-postası için bekleyin.   
        > 
        > Yalnızca SQL Server ve [Fslogix Kullanıcı profili kapsayıcıları](../virtual-desktop/create-fslogix-profile-container.md)Için sürekli kullanılabilirliği etkinleştirmeniz gerekir. SQL Server ve FSLogix Kullanıcı profili kapsayıcıları dışındaki iş yükleri için SMB sürekli kullanılabilirlik paylaşımlarının *kullanılması desteklenmez.* Bu özellik şu anda Windows SQL Server 'de desteklenmektedir. Linux SQL Server şu anda desteklenmiyor. SQL Server yüklemek için yönetici olmayan bir hesap kullanıyorsanız, hesapta gerekli güvenlik ayrıcalığının atandığından emin olun. Etki alanı hesabı gerekli güvenlik ayrıcalığına () sahip değilse `SeSecurityPrivilege` ve ayrıcalık etki alanı düzeyinde ayarlanamıyor ise, Active Directory bağlantıları 'Nın **güvenlik ayrıcalığı kullanıcıları** alanını kullanarak ayrıcalığı hesaba verebilirsiniz. Bkz. [Active Directory bağlantı oluşturma](create-active-directory-connections.md#create-an-active-directory-connection).

    <!-- [1/13/21] Commenting out command-based steps below, because the plan is to use form-based (URL) registration, similar to CRR feature registration -->
    <!-- 
        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```

        Check the status of the feature registration: 

        > [!NOTE]
        > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is `Registered` before continuing.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```
        
        You can also use [Azure CLI commands](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` and `az feature show` to register the feature and display the registration status. 
    --> 

    ![SMB birimi oluşturma protokolünün protokol sekmesini açıklayan ekran görüntüsü.](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Birim ayrıntılarını gözden geçirmek için **gözden geçir + oluştur** ' a tıklayın.  Ardından, SMB birimini oluşturmak için **Oluştur** ' a tıklayın.

    Oluşturduğunuz birim birimler sayfasında görünür. 
 
    Birim, kapasite havuzundan aboneliği, kaynak grubunu ve konum özniteliklerini devralır. Birimin dağıtım durumunu izlemek için Bildirimler sekmesini kullanabilirsiniz.

## <a name="control-access-to-an-smb-volume"></a>SMB birimine erişimi denetleme  

SMB birimine erişim izinler aracılığıyla yönetilir.  

### <a name="share-permissions"></a>İzinleri paylaşma  

Varsayılan olarak, yeni bir birimde **Everyone/Full denetim** Share izinleri vardır. Domain Admins grubunun üyeleri, aşağıdaki gibi, paylaşma izinlerini değiştirebilir:  

1. Paylaşımdan bir sürücü eşleştirin.  
2. Sürücüye sağ tıklayın, **Özellikler**' i seçin ve ardından **güvenlik** sekmesine gidin.

[![Paylaşma izinlerini ayarla](../media/azure-netapp-files/set-share-permissions.png)](../media/azure-netapp-files/set-share-permissions.png#lightbox)

### <a name="ntfs-file-and-folder-permissions"></a>NTFS dosya ve klasör izinleri  

Windows SMB istemcisinde nesnenin özelliklerinin **güvenlik** sekmesini kullanarak bir dosya veya klasör için izinleri ayarlayabilirsiniz.
 
![Dosya ve klasör izinlerini ayarlama](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Sonraki adımlar  

* [Windows veya Linux sanal makineleri için birimi bağlama veya ayırma](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Azure NetApp Files için kaynak sınırları](azure-netapp-files-resource-limits.md)
* [SMB hakkında SSS](./azure-netapp-files-faqs.md#smb-faqs)
* [SMB veya çift protokol birimlerinde sorun giderme](troubleshoot-dual-protocol-volumes.md)
* [Azure hizmetleri için sanal ağ tümleştirmesi hakkında bilgi edinin](../virtual-network/virtual-network-for-azure-services.md)
* [Azure CLı kullanarak yeni bir Active Directory ormanı yüklemesi](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
