---
title: Azure NetApp Files için bir SMB birimi oluşturun | Microsoft Docs
description: Azure NetApp Files için SMB birimi oluşturmayı açıklar.
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
ms.topic: conceptual
ms.date: 10/12/2019
ms.author: b-juche
ms.openlocfilehash: 94fc4906478e44365d03e9c8eeadd7cb1946a43a
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300534"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Azure NetApp Files için SMB birimi oluşturma

Azure NetApp Files NFS ve SMBv3 birimlerini destekler. Birimin kapasite kullanımı, havuzunun sağlanan kapasitesinden sayılır. Bu makalede, SMBv3 birimi oluşturma konusu gösterilmektedir. NFS birimi oluşturmak istiyorsanız, bkz. [Azure NetApp Files IÇIN NFS birimi oluşturma](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Başlamadan önce 
Zaten bir kapasite havuzu ayarlamış olmalısınız.   
@No__t [Kapasite havuzu ayarlama](azure-netapp-files-set-up-capacity-pool.md)-1  
Azure NetApp Files için bir alt ağ atanmış olmalıdır.  
[Azure NetApp Files için bir alt ağ temsilcisi seçme](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Active Directory bağlantıları için gereksinimler

 SMB birimi oluşturmadan önce Active Directory bağlantı oluşturmanız gerekir. Active Directory bağlantıları için gereksinimler şunlardır: 

* Kullandığınız yönetici hesabının, belirttiğiniz kuruluş birimi (OU) yolunda makine hesapları oluşturabiliyor olması gerekir.  

* Uygun bağlantı noktaları geçerli Windows Active Directory (AD) sunucusunda açık olmalıdır.  
    Gerekli bağlantı noktaları aşağıdaki gibidir: 

    |     Hizmet           |     Bağlantı noktası     |     Protokol     |
    |-----------------------|--------------|------------------|
    |    AD Web Hizmetleri    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    PROTOKOLLERINDEN           |
    |    Icmpv4             |    Yok       |    Yankı yanıtı    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    PROTOKOLLERINDEN           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    PROTOKOLLERINDEN           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    PROTOKOLLERINDEN           |
    |    LDAP               |    3268      |    TCP           |
    |    NetBIOS adı       |    138       |    PROTOKOLLERINDEN           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    PROTOKOLLERINDEN           |
    |    Güvenli LDAP        |    636       |    TCP           |
    |    Güvenli LDAP        |    3269      |    TCP           |
    |    W32Time            |    123       |    PROTOKOLLERINDEN           |

* Hedeflenen Active Directory Domain Services yönelik site topolojisi, Azure NetApp Files dağıtıldığı Azure VNet 'te en iyi yöntemlere uymalıdır.  

    Azure NetApp Files dağıtıldığı sanal ağın adres alanı yeni veya var olan bir Active Directory sitesine eklenmelidir (Azure NetApp Files tarafından erişilebilen bir etki alanı denetleyicisi bulunur). 

* Belirtilen DNS sunucularına, Azure NetApp Files için [temsilci alt ağından](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) ulaşılabilir olmalıdır.  

    Desteklenen ağ topolojileri için [Azure NetApp Files ağ planlama yönergelerine](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) bakın.

    Ağ güvenlik grupları (NSG 'ler) ve güvenlik duvarları, Active Directory ve DNS trafiği isteklerine izin vermek için uygun şekilde yapılandırılmış kurallara sahip olmalıdır. 

* Azure NetApp Files atanmış alt ağ, tüm yerel ve uzak etki alanı denetleyicileri dahil olmak üzere etki alanındaki tüm Active Directory Domain Services (ekleme) etki alanı denetleyicilerine erişebilmelidir. Aksi takdirde, hizmet kesintisi meydana gelebilir.  

    Azure NetApp Files Temsilcili alt ağ üzerinden erişilemeyen etki alanı denetleyicileriniz varsa, kapsamı **küresel** (varsayılan) olarak **siteye**değiştirmek için bir Azure destek isteği gönderebilirsiniz.  Azure NetApp Files, yalnızca Azure NetApp Files atanmış alt ağ adres alanının bulunduğu sitedeki etki alanı denetleyicileriyle iletişim kurması gerekir.

    Bkz. AD siteleri ve hizmetleriyle ilgili [site topolojisini tasarlama](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) . 

## <a name="create-an-active-directory-connection"></a>Active Directory bağlantısı oluşturma

1. NetApp hesabınızdan **Active Directory bağlantılar**' a ve ardından **Birleştir**' e tıklayın.  

    ![Active Directory bağlantıları](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Active Directory Birleştir penceresinde, aşağıdaki bilgileri sağlayın:

    * **Birincil DNS**  
        Bu, Active Directory etki alanına ekleme ve SMB kimlik doğrulama işlemleri için gereken DNS 'dir. 
    * **IKINCIL DNS**   
        Bu, gereksiz ad hizmetleri sağlamak için ikincil DNS sunucusudur. 
    * **Alanını**  
        Bu, birleştirmek istediğiniz Active Directory Domain Services etki alanı adıdır.
    * **SMB sunucusu (bilgisayar hesabı) ön eki**  
        Bu, Azure NetApp Files yeni hesapların oluşturulması için kullanacağı Active Directory makine hesabının adlandırma ön ekidir.

        Örneğin, kuruluşunuzun dosya sunucuları için kullandığı adlandırma standardı, NAS-01, NAS-02..., NAS-045 ise, ön ek için "NAS" girersiniz. 

        Hizmet, gerektiğinde Active Directory daha fazla makine hesabı oluşturur.

    * **Kuruluş birimi yolu**  
        Bu, SMB sunucu makinesi hesaplarının oluşturulacağı kuruluş birimi (OU) için LDAP yoludur. Diğer bir deyişle, OU = ikinci düzey, OU = ilk düzey. 

        Azure Active Directory Domain Services ile Azure NetApp Files kullanıyorsanız, NetApp hesabınız için Active Directory yapılandırdığınızda kuruluş birimi yolu `OU=AADDC Computers` ' dır.
        
    * **Kullanıcı adınız** ve **Parolanız** dahil kimlik bilgileri

    ![Active Directory Birleştir](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. **Katıl**’a tıklayın.  

    Oluşturduğunuz Active Directory bağlantı görüntülenir.

    ![Active Directory bağlantıları](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> Active Directory bağlantısını kaydettikten sonra Kullanıcı adı ve parola alanlarını düzenleyebilirsiniz. Bağlantı kaydedildikten sonra başka hiçbir değer düzenlenemez. Başka herhangi bir değeri değiştirmeniz gerekiyorsa, önce dağıtılan tüm SMB birimlerini silmeniz, sonra Active Directory bağlantısını silip yeniden oluşturmanız gerekir.

## <a name="add-an-smb-volume"></a>SMB birimi ekleme

1. Kapasite havuzları dikey penceresinden **birimler** dikey penceresine tıklayın. 

    ![Birimlere git](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Birim oluşturmak için **+ Birim ekle**'ye tıklayın.  
    Birim oluştur penceresi görüntülenir.

3. Birim Oluştur penceresinde **Oluştur** ' a tıklayın ve aşağıdaki alanlar için bilgi sağlayın:   
    * **Birim adı**      
        Oluşturmakta olduğunuz birim için ad belirtin.   

        Birim adı her bir kapasite havuzu içinde benzersiz olmalıdır. En az üç karakter uzunluğunda olmalıdır. Herhangi bir alfasayısal karakter kullanabilirsiniz.   

        Birim adı olarak `default` kullanamazsınız.

    * **Kapasite havuzu**  
        Birimin oluşturulmasını istediğiniz kapasite havuzunu belirtin.

    * **Kota**  
        Birime ayrılmış mantıksal depolama miktarını belirtin.  

        **Kullanılabilir kota** alanı, yeni birimi oluştururken kullanabildiğiniz, seçilen kapasite havuzundaki kullanılmamış alan miktarını gösterir. Yeni birimin boyutu kullanılabilir kotayı aşamaz.  

    * **Sanal ağ**  
        Birime erişmek istediğiniz Azure sanal ağını (VNet) belirtin.  

        Belirttiğiniz VNet Azure NetApp Files için bir alt ağa sahip olmalıdır. Azure NetApp Files hizmetine yalnızca aynı VNet 'ten veya VNet eşlemesi ile aynı bölgedeki bir VNet 'ten erişilebilir. Ayrıca, hızlı rota aracılığıyla şirket içi ağınızdan birime da erişebilirsiniz.   

    * **Alt ağ**  
        Birim için kullanmak istediğiniz alt ağı belirtin.  
        Belirttiğiniz alt ağ Azure NetApp Files için temsilci atanmış olmalıdır. 
        
        Bir alt ağ temsilcisi yoksa, birim oluştur sayfasında **Yeni oluştur** ' a tıklayabilirsiniz. Sonra alt ağ oluştur sayfasında alt ağ bilgilerini belirtin ve alt ağın Azure NetApp Files için temsilci olarak **Microsoft. NetApp/birimler** ' i seçin. Her VNet 'te Azure NetApp Files için yalnızca bir alt ağ atanabilir.   
 
        ![Birim oluşturun](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Alt ağ oluşturma](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. **Protokol** ' e tıklayın ve aşağıdaki bilgileri doldurun:  
    * Birimin protokol türü olarak **SMB** ' yi seçin. 
    * Açılır listeden **Active Directory** bağlantınızı seçin.
    * **Paylaşım adı**' nda paylaşılan birimin adını belirtin.

    ![SMB protokolünü belirtin](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Birim ayrıntılarını gözden geçirmek için **gözden geçir + oluştur** ' a tıklayın.  Ardından, SMB birimini oluşturmak için **Oluştur** ' a tıklayın.

    Oluşturduğunuz birim birimler sayfasında görünür. 
 
    Birim, kapasite havuzundan aboneliği, kaynak grubunu ve konum özniteliklerini devralır. Birimin dağıtım durumunu izlemek için Bildirimler sekmesini kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar  

* [Windows veya Linux sanal makineleri için bir birimi bağlama veya çıkarma](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Azure NetApp Files için kaynak sınırları](azure-netapp-files-resource-limits.md)
* [SMB SSS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Azure hizmetleri için sanal ağ tümleştirmesi hakkında bilgi edinin](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Azure CLı kullanarak yeni bir Active Directory ormanı yüklemesi](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
