---
title: Azure NetApp Files için bir NFS birimi oluşturun | Microsoft Docs
description: Azure NetApp Files için bir NFS birimi oluşturmayı açıklar.
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
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: 1f312e8a5034d238e4802e9323bc1b5ac5cdae21
ms.sourcegitcommit: f226cdd6406372b5693d46b6d04900f2f0cda4e6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/11/2019
ms.locfileid: "73906245"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Azure NetApp Files için NFS birimi oluşturma

Azure NetApp Files NFS (NFSv3 ve NFSv 4.1) ve SMBv3 birimlerini destekler. Birimin kapasite kullanımı, havuzunun sağlanan kapasitesinden sayılır. Bu makalede, bir NFS biriminin nasıl oluşturulacağı gösterilmektedir. SMB birimi oluşturmak istiyorsanız, bkz. [Azure NetApp Files IÇIN SMB birimi oluşturma](azure-netapp-files-create-volumes-smb.md). 

## <a name="before-you-begin"></a>Başlamadan önce 
Zaten bir kapasite havuzu ayarlamış olmalısınız.   
[Kapasite havuzu ayarlama](azure-netapp-files-set-up-capacity-pool.md)   
Azure NetApp Files için bir alt ağ atanmış olmalıdır.  
[Azure NetApp Files için bir alt ağ temsilcisi seçme](azure-netapp-files-delegate-subnet.md)

## <a name="considerations"></a>Dikkat edilmesi gerekenler 

> [!IMPORTANT] 
> NFSv 4.1 özelliğine erişim için beyaz liste gerekir.  Beyaz liste istemek için <anffeedback@microsoft.com>bir istek gönderebilirsiniz. 

* Hangi NFS sürümünün kullanılacağına karar verme  
  NFSv3, çok çeşitli kullanım durumlarını işleyebilir ve genellikle çoğu kurumsal uygulamalarda dağıtılır. Uygulamanızın gerektirdiği sürümü (NFSv3 veya NFSv 4.1) doğrulamanız ve uygun sürümü kullanarak biriminiz oluşturmanız gerekir. Örneğin, [Apache ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave)kullanıyorsanız, NFSv 4.1 ile dosya kilitleme NFSv3 üzerinden önerilir. 

* Güvenlik  
  UNIX modu bitleri için destek (okuma, yazma ve yürütme) NFSv3 ve NFSv 4.1 için kullanılabilir. NFS istemcisinde NFS birimleri bağlamak için kök düzeyinde erişim gerekir.

* NFSv 4.1 için yerel kullanıcı/grup ve LDAP desteği  
  Şu anda NFSv 4.1 yalnızca birimlere kök erişimi destekler. [Azure NetApp Files Için NFSv 4.1 varsayılan etki alanını yapılandırma](azure-netapp-files-configure-nfsv41-domain.md)konusuna bakın. 

## <a name="best-practice"></a>En iyi yöntem

* Birim için uygun bağlama yönergelerini kullandığınızdan emin olmanız gerekir.  Bkz. [Windows veya Linux sanal makineleri için bir birimi bağlama veya çıkarma](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).

* NFS istemcisi, Azure NetApp Files birimle aynı VNet veya eşlenmiş VNet 'te olmalıdır. VNet dışından bağlantı desteklenir; Ancak, ek gecikme ortaya çıkaracak ve genel performansı azaltacaktır.

* NFS istemcisinin güncel olduğundan ve işletim sistemi için en son güncelleştirmeleri çalıştırdığından emin olmanız gerekir.

## <a name="create-an-nfs-volume"></a>NFS birimi oluşturma

1.  Kapasite havuzları dikey penceresinden **birimler** dikey penceresine tıklayın. 

    ![Birimlere git](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Birim oluşturmak için **+ Birim ekle**'ye tıklayın.  
    Birim oluştur penceresi görüntülenir.

3.  Birim Oluştur penceresinde **Oluştur** ' a tıklayın ve aşağıdaki alanlar için bilgi sağlayın:   
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
        Birime hangi Azure sanal ağından (Vnet) erişmek istediğinizi belirtin.  

        Belirttiğiniz VNET Azure NetApp Files için bir alt ağa sahip olmalıdır. Azure NetApp Files hizmetine yalnızca aynı VNET 'ten veya VNET eşlemesi ile aynı bölgedeki bir VNET 'ten erişilebilir. Ayrıca, hızlı rota aracılığıyla şirket içi ağınızdan birime da erişebilirsiniz.   

    * **Alt ağ**  
        Birim için kullanmak istediğiniz alt ağı belirtin.  
        Belirttiğiniz alt ağ Azure NetApp Files için temsilci atanmış olmalıdır. 
        
        Bir alt ağ temsilcisi yoksa, birim oluştur sayfasında **Yeni oluştur** ' a tıklayabilirsiniz. Sonra alt ağ oluştur sayfasında alt ağ bilgilerini belirtin ve alt ağın Azure NetApp Files için temsilci olarak **Microsoft. NetApp/birimler** ' i seçin. Her VNET 'te Azure NetApp Files için yalnızca bir alt ağ atanabilir.   
 
        ![Birim oluşturun](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Alt ağ oluşturma](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. **Protokol**' e tıklayın ve ardından aşağıdaki eylemleri tamamlamayı seçin:  
    * Birimin protokol türü olarak **NFS** ' yi seçin.   
    * Yeni birim için dışarı aktarma yolunu oluşturmak üzere kullanılacak **dosya yolunu** belirtin. Dışarı aktarma yolu, birimi bağlamak ve birime erişmek için kullanılır.

        Dosya yolu adında yalnızca harfler, sayılar ve kısa çizgiler ("-") bulunabilir. 16 ile 40 karakter arası uzunlukta olmalıdır. 

        Dosya yolu her abonelik ve her bölge içinde benzersiz olmalıdır. 

    * Birim için NFS sürümünü (**NFSv3** veya **nfsv 4.1**) seçin.  
    * İsteğe bağlı olarak, [NFS birimi için dışarı aktarma ilkesini yapılandırın](azure-netapp-files-configure-export-policy.md).

    ![NFS protokolünü belirtin](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Birim ayrıntılarını gözden geçirmek için **gözden geçir + oluştur** ' a tıklayın.  Ardından, NFS birimini oluşturmak için **Oluştur** ' a tıklayın.

    Oluşturduğunuz birim birimler sayfasında görünür. 
 
    Birim, kapasite havuzundan aboneliği, kaynak grubunu ve konum özniteliklerini devralır. Birimin dağıtım durumunu izlemek için Bildirimler sekmesini kullanabilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar  

* [Azure NetApp Files için NFSv 4.1 varsayılan etki alanını yapılandırın](azure-netapp-files-configure-nfsv41-domain.md)
* [Windows veya Linux sanal makineleri için bir birimi bağlama veya çıkarma](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [NFS birimine yönelik dışarı aktarma ilkesini yapılandırma](azure-netapp-files-configure-export-policy.md)
* [Azure NetApp Files için kaynak sınırları](azure-netapp-files-resource-limits.md)
* [Azure hizmetleri için sanal ağ tümleştirmesi hakkında bilgi edinin](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
