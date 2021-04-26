---
title: Azure NetApp Files hizmetiniz için birim sabit kotasında ne kadar değişiklik yapılır? | Microsoft Docs
description: Birim sabit kotasını kullanma, değişikliğin nasıl planlanacağı ve kapasiteleri izleme ve yönetme ile ilgili değişikliği açıklar.
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
ms.date: 03/29/2021
ms.author: b-juche
ms.openlocfilehash: 5e7f71f91e5778b4f096bb760bfe5a0a89b5cbcb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764288"
---
# <a name="what-changing-to-volume-hard-quota-means-for-your-azure-netapp-files-service"></a>Azure NetApp Files hizmetiniz için birim sabit kotasında ne kadar değişiklik yapılır?

Hizmetin başından Azure NetApp Files, kapasite havuzu sağlama ve otomatik büyüme mekanizması kullanıyor. Azure NetApp Files birimler, seçilen bir katmanın ve boyutun, müşteri tarafından sağlanan kapasite havuzunda ölçülü kaynak olarak sağlanır. Birim boyutları (kotalar) performans ve kapasite sağlamak için kullanılır ve Kotalar herhangi bir zamanda anında ayarlanabilir. Bu davranış, şu anda birim kotasının birim bant genişliğini denetlemek için kullanılan bir performans mandalı olduğu anlamına gelir. Şu anda Kapasite dolduğunda kapasite havuzlarının ölçeği otomatik olarak büyütülür.   

> [!IMPORTANT] 
> Birim ve kapasite havuzu sağlamanın Azure NetApp Files davranışı *el ile* ve *denetlenebilir* mekanizmaya değişecektir. **30 Nisan 2021 (güncelleştirilmiş) tarihinden itibaren, birim boyutları (Kota) bant genişliği performansını ve sağlanan kapasiteyi yönetir ve temel alınan kapasite havuzları artık otomatik olarak büyütülecektir.** 

## <a name="reasons-for-the-change-to-volume-hard-quota"></a>Birim sabit kotası değişikliğine yönelik nedenler

Birçok müşteri, *ilk* davranışta üç ana zorluk gösterdi:
* VM istemcileri, işletim sistemi alanı veya kapasite izleme araçları kullanırken, yanlış istemci veya uygulama tarafı görünürlüğü sunan herhangi bir birimin ölçülü kaynak sağlanmış (100 TiB) kapasitesini görür.
* Uygulama sahiplerinin, kapasite havuzu otomatik büyüme davranışı nedeniyle sağlanan kapasite havuzu alanı (ve ilişkili maliyet) üzerinde denetimi yoktur. Bu durum, "çalışma dışı işlemlerin" hızlı bir şekilde doldurmasını ve sağlanan kapasiteyi ve maliyeti büyüyüp büyütebileceği ortamlarda çok daha fazla.
* Müşteriler birim boyutu (Kota) ve performans arasında doğrudan bir bağıntı görmek ve sürdürmek ister. (Örtük) bir birimin (kapasite tabanlı) ve havuz otomatik büyümesi 'nin (örtük) geçerli davranışı ile, birim kotası etkin bir şekilde ayarlanana veya sıfırlanana kadar müşterilerin doğrudan bağıntısı yoktur. 

Birçok müşteri sağlanan kapasite üzerinde doğrudan denetim isteğinde bulundu. Depolama kapasitesini ve kullanımı denetlemek ve dengelemek ister. Ayrıca, uygulama tarafı ve istemci tarafı görünürlüğü, kullanılabilir, kullanılan ve uygulama hacimlerinin sağlanan kapasitesini ve performansını ve bunların yanı sıra maliyeti denetlemek de ister. 

## <a name="what-is-the-volume-hard-quota-change"></a>Birim sabit kota değişikliği nedir?   

Birim sabit kota değişikliği sayesinde Azure NetApp Files birimler artık ölçülü kaynak (maksimum) 100 TiB olarak sağlanmayacaktır. Birimler yapılandırılan gerçek boyut (Kota) üzerinden sağlanır. Ayrıca, düşük kapasiteli kapasite havuzları artık tam kapasite tüketimine ulaşılmaya karşı otomatik olarak büyümeyecektir. Bu değişiklik, otomatik kapasite artışı olmadan olarak da sağlanan Azure yönetilen diskler gibi davranışı yansıtır.

Örneğin, 4-TiB Ultra hizmet düzeyi kapasite havuzunda 1-TiB boyutunda (Kota) yapılandırılan Azure NetApp Files bir birimi düşünün. Bir uygulama, birime sürekli olarak veri yazıyor.

*İlk* davranış:  
* Beklenen bant genişliği: 128 MIB/s
* Kullanılabilir toplam (ve istemci görünür) kapasitesi: 100 TiB   
    Birime bu boyuttan daha fazla veri yazabileceksiniz.
* Kapasite havuzu: dolduğunda 1 TiB artışlarıyla otomatik olarak büyür.
* Birim kotası değişikliği: birimin yalnızca performansını (bant genişliği) değiştirir. İstemci görünür veya kullanılabilir kapasiteyi değiştirmez.

*Değiştirilen* davranış:  
* Beklenen bant genişliği: 128 MIB/s
* Toplam kullanılabilir (ve istemci görünür) kapasitesi: 1 TiB bu boyuttan daha fazla veri yazamaz.
* Kapasite havuzu: 4 TiB boyutunda kalır ve otomatik olarak büyütülemez. 
* Birim kota değişikliği: birim performansını (bant genişliği) ve istemcinin görünür veya kullanılabilir kapasitesini değiştirir.

Azure NetApp Files birimlerinin ve kapasite havuzlarının kullanımını proaktif olarak izlemeniz gerekir. Tam olarak kullanım için birim ve havuz kullanımını özellikle değiştirmeniz gerekir. Azure NetApp Files [, eldeki birim ve kapasite havuzu yeniden boyutlandırma işlemlerine](azure-netapp-files-resize-capacity-pools-or-volumes.md)izin vermeyi sürdürmeye devam edecektir.

## <a name="how-to-operationalize-the-volume-hard-quota-change"></a>Birim sabit kota değişikliğini oluşturma

Bu bölümde, sorunsuz bir geçiş için birim sabit kotasına yapılan değişikliğin nasıl yapılacağı hakkında yönergeler verilmektedir. Ayrıca, şu anda sağlanan birimleri ve kapasite havuzlarını, devam eden izlemeyi ve uyarı ve kapasite yönetimi seçeneklerini işlemek için Öngörüler sağlar.

### <a name="currently-provisioned-volumes-and-capacity-pools"></a>Şu anda sağlanan birimler ve kapasite havuzları

Birim sabit kota değişikliği nedeniyle, işletim modelinizi değiştirmelisiniz. Sağlanan birimler ve kapasite havuzları için devam eden kapasite yönetimi gerekir.  Değiştirilen davranış anında gerçekleşeceğinden, Azure NetApp Files takım, bu bölümde açıklandığı gibi, mevcut, daha önce sağlanan birimler ve kapasite havuzları için tek seferlik bir düzeltici ölçü dizisi önerir.

#### <a name="one-time-corrective-or-preventative-measures-recommendations"></a>Bir kerelik düzeltme veya önleyici ölçüler önerileri  

Birim sabit kota değişikliği, önceden sağlanan birimler ve havuzlar için sağlanan ve kullanılabilir kapasitede değişikliklere neden olur. Sonuç olarak, bazı kapasite ayırma sorunları ortaya çıkabilir. Müşteriler için kısa vadeli boş durumların önüne geçmek için Azure NetApp Files ekibi, aşağıdaki, tek seferlik düzeltici/önleyici ölçüleri önerir: 

* **Sağlanan birim boyutları**:   
    Sağlanan tüm birimleri değişiklik hızına göre uygun arabelleğe sahip olacak şekilde yeniden boyutlandırın ve zaman içinde (örneğin, tipik iş yüküne göre %20) en fazla 100 TiB ( [birim boyutu sınırı](azure-netapp-files-resource-limits.md#resource-limits)) ile uyarı veya yeniden boyutlandırma yapın. Arabellek kapasitesi de dahil olmak üzere bu yeni birim boyutu aşağıdaki etkenlere bağlı olmalıdır:
    * Kullanılan kapasite, sağlanan birim kotasından daha az olduğunda, **sağlanan** birim kapasitesi.
    * Kullanılan kapasite, sağlanan birim kotasından daha fazla olduğunda **kullanılan** birim kapasitesi.  
    Yetersiz kapasite havuzunun büyütülmesi gerekmiyorsa birim düzeyinde kapasite artışı için ek ücret alınmaz. Bu değişikliğin bir etkisi olarak, birim için bir bant genişliği sınırı *artışı* gözlemleyebilirsiniz ( [Otomatik QoS kapasite havuzu türünün](azure-netapp-files-understand-storage-hierarchy.md#qos_types) kullanılması durumunda).

* **Sağlanan kapasite havuzu boyutları**:   
    Birim boyutları ayarlamaktan sonra, birim boyutlarının toplamı barındırma kapasitesi havuzunun boyutundan daha büyük olursa, kapasite havuzunun en fazla 500 TiB ( [Kapasite havuzu boyut sınırı](azure-netapp-files-resource-limits.md#resource-limits)) ile birim toplamına eşit veya ondan daha büyük bir boyuta artması gerekir. Ek kapasite havuzu kapasitesi, normal olarak ACR ücretine tabi olacaktır.

Aşağıdaki bölümlerde açıklanan şekilde ayarlama veya uyarma konusunda yardıma ihtiyacınız varsa ortamınızı doğrulamak için Azure NetApp Files uzmanlarınız ile çalışmanız gerekir.

### <a name="ongoing-capacity-management"></a>Devam eden kapasite yönetimi  

Tek seferlik düzeltici ölçüler gerçekleştirdikten sonra kapasiteyi izlemek ve yönetmek için devam eden süreçler birlikte koymanız gerekir. Aşağıdaki bölümlerde kapasite izleme ve yönetimi hakkında öneriler ve alternatifler sağlanmaktadır.

### <a name="monitor-capacity-utilization"></a>Kapasite kullanımını izleme

Kapasite kullanımını çeşitli düzeylerde izleyebilirsiniz. 

#### <a name="vm-level-monitoring"></a>VM düzeyinde izleme 

En yüksek izleme düzeyi (uygulamaya en yakın) uygulama sanal makinesi içinden yapılır. VM istemci işletim sistemi içinden kapasite raporlama 'daki davranış değişikliğini gözlemleyeceksiniz.

Aşağıdaki iki senaryoda, 4-TiB, Ultra hizmet düzeyi kapasite havuzunda 1-TiB boyutunda (Kota) yapılandırılan Azure NetApp Files bir birimi düşünün. 

##### <a name="windows"></a>Windows

Windows istemcileri, ağ eşlenmiş sürücü özelliklerini kullanarak bir birimin kullanılan ve kullanılabilir kapasitesini denetleyebilir. **Gezgin**  ->  **sürücü**  ->  **özellikleri** seçeneğini kullanabilirsiniz.  

Aşağıdaki örneklerde, değiştirilen davranış *öncesinde* Windows 'da birim kapasitesi raporlaması gösterilmektedir:

![Davranış değişikliğinden önce bir birimin örnek depolama kapasitesini gösteren ekran görüntüleri.](../media/azure-netapp-files/hard-quota-windows-capacity-before.png)

Komut `dir` isteminde aşağıda gösterildiği gibi komutunu da kullanabilirsiniz:

![Davranış değişikliğinden önce bir birimin depolama kapasitesini göstermek için bir komut kullanmayı gösteren ekran görüntüsü.](../media/azure-netapp-files/hard-quota-command-capacity-before.png)

Aşağıdaki örneklerde, değiştirilen davranış *sonrasında* Windows 'da birim kapasitesi raporlaması gösterilmektedir:

![Davranış değişikliğinden sonra bir birimin örnek depolama kapasitesini gösteren ekran görüntüleri.](../media/azure-netapp-files/hard-quota-windows-capacity-after.png)

Aşağıdaki örnek, `dir` komut çıkışını gösterir:  

![Davranış değişikliğinden sonra bir birimin depolama kapasitesini göstermek için bir komut kullanmayı gösteren ekran görüntüsü.](../media/azure-netapp-files/hard-quota-command-capacity-after.png)

##### <a name="linux"></a>Linux 

Linux istemcileri, [ `df` komutunu](https://linux.die.net/man/1/df)kullanarak bir birimin kullanılan ve kullanılabilir kapasitesini denetleyebilir. `-h`Bu seçenek, M, G ve T birim boyutlarını kullanarak boyutu, kullanılan alanı ve kullanılabilir alanı insan tarafından okunabilen biçimde gösterir.

Aşağıdaki örnek, değiştirilen davranışın *önüne* Linux 'ta birim kapasitesi raporlamayı gösterir:  

![Davranış değişikliğinden önce bir birimin depolama kapasitesini göstermek için Linux kullanmayı gösteren ekran görüntüsü.](../media/azure-netapp-files/hard-quota-linux-capacity-before.png)

Aşağıdaki örnek, değiştirilen davranışın *ardından* Linux 'ta birim kapasitesi raporlamayı gösterir:  

![Davranış değişikliğinden sonra bir birimin depolama kapasitesini göstermek için Linux kullanmayı gösteren ekran görüntüsü.](../media/azure-netapp-files/hard-quota-linux-capacity-after.png)


### <a name="configure-alerts-using-anfcapacitymanager"></a>ANFCapacityManager kullanarak uyarıları yapılandırma

Azure NetApp Files kapasitesini izlemek ve uyarlanmış uyarı almak için topluluk tarafından desteklenen Logic Apps ANFCapacityManager aracını kullanabilirsiniz. ANFCapacityManager, [Anfcapacitymanager GitHub sayfasında](https://github.com/ANFTechTeam/ANFCapacityManager)bulunur.

ANFCapacityManager, kapasite tabanlı uyarı kurallarını yöneten bir Azure mantıksal uygulamasıdır. Azure NetApp Files birimlerinizin yer kaplamasını engellemek için birim boyutlarını otomatik olarak artırır. Dağıtımı kolaydır ve aşağıdaki Uyarı Yönetimi yeteneklerini sağlar:

* Bir Azure NetApp Files kapasite havuzu veya birimi oluşturulduğunda, ANFCapacityManager belirtilen yüzde eşiğini temel alan bir ölçüm uyarı kuralı oluşturur.
* Bir Azure NetApp Files kapasite havuzu veya birimi yeniden boyutlandırılırken, ANFCapacityManager, ölçüm uyarı kuralını belirtilen yüzde Kapasite yüzdesi eşiğine göre değiştirir. Uyarı kuralı yoksa, oluşturulur.
* Bir Azure NetApp Files kapasite havuzu veya birimi silindiğinde, ilgili ölçüm uyarısı kuralı silinir.

Aşağıdaki anahtar uyarı ayarlarını yapılandırabilirsiniz:  

* **Kapasite havuzu% tam eşik** -Bu ayar, kapasite havuzları için bir uyarı tetikleyen tüketilen eşiği belirler. 90 değeri, kapasite havuzu %90 ' e ulaştığında bir uyarının tetiklenmesi oluşmasına neden olabilir.
* **Birim% tam eşik** -Bu ayar, birimler için bir uyarı tetikleyen tüketilen eşiği belirler. 80 değeri, birim %80 ' e ulaştığında bir uyarının tetiklenmesi oluşmasına neden olabilir.
* **Kapasite bildirimleri Için mevcut eylem grubu** -Bu ayar, kapasite tabanlı uyarı için tetiklenecek olan eylem grubudur. Bu ayar sizin tarafınızdan önceden oluşturulmalıdır. Eylem grubu e-posta, SMS veya diğer biçimleri gönderebilir.

Aşağıdaki çizimde uyarı yapılandırması gösterilmektedir:  

![ANFCapacityManager kullanarak uyarı yapılandırmasını gösteren çizim.](../media/azure-netapp-files/hard-quota-anfcapacitymanager-configuration.png)

ANFCapacityManager 'ı yükledikten sonra aşağıdaki davranışı bekleyebilir: bir Azure NetApp Files kapasite havuzu veya birimi oluşturulduğunda, değiştirildiğinde veya silindiğinde, mantıksal uygulama, ad veya ile kapasite tabanlı ölçüm uyarısı kuralını otomatik olarak oluşturur, değiştirir veya siler `ANF_Pool_poolname` `ANF_Volume_poolname_volname` . 

### <a name="manage-capacity"></a>Kapasiteyi yönetme

İzleme ve uyarma ek olarak, Azure NetApp Files (artan) kapasite tüketimini yönetmek için bir uygulama kapasitesi yönetimi uygulaması da eklemeniz gerekir. Bir Azure NetApp Files birim veya kapasite havuzu dolduğunda, [uygulama kesintiye uğramadan, ek kapasite anında](azure-netapp-files-resize-capacity-pools-or-volumes.md)alınabilir. Bu bölümde, gerektiğinde birim ve kapasite havuzu tarafından sağlanan alanı artırmanın çeşitli el ile ve otomatikleştirilmiş yolları açıklanmaktadır.
 
#### <a name="manual"></a>El ile 

Birim veya kapasite havuzu boyutlarını el ile artırmak için portalını veya CLı 'yi kullanabilirsiniz. 

##### <a name="portal"></a>Portal 

[Bir birimin boyutunu](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-volume) gerektiği gibi değiştirebilirsiniz. Birimin kapasite kullanımı, havuzunun sağlanan kapasitesinden sayılır.

1. NetApp hesabını Yönet dikey penceresinde **birimler**' e tıklayın.  
2. Yeniden boyutlandırmak istediğiniz birimin adına sağ tıklayın veya `…` bağlam menüsünü göstermek için birimin satırının sonundaki simgeye tıklayın. 
3. Birimi yeniden boyutlandırmak veya silmek için bağlam menüsü seçeneklerini kullanın.   

   ![Bir birim için bağlam menüsü seçeneklerini gösteren ekran görüntüsü.](../media/azure-netapp-files/hard-quota-volume-options.png) 

   ![Birim kotasını Güncelleştir penceresini gösteren ekran görüntüsü.](../media/azure-netapp-files/hard-quota-update-volume-quota.png) 

Bazı durumlarda, barındırma kapasitesi havuzunda birimleri yeniden boyutlandırmak için yeterli kapasite yoktur. Ancak, [Kapasite havuzu boyutunu](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-the-capacity-pool) 1-TİB artışlarla veya azaltır. Kapasite havuzu boyutu 4 TiB 'den küçük olamaz. *Kapasite havuzunun yeniden boyutlandırılması, satın alınan Azure NetApp Files kapasitesini değiştirir.*

1. NetApp hesabını Yönet dikey penceresinde, yeniden boyutlandırmak istediğiniz kapasite havuzuna tıklayın.
2. Kapasite havuzu adına sağ tıklayın veya `…` Kapasite havuzunun sonundaki simgeye tıklayarak bağlam menüsünü görüntüleyin.
3. Kapasite havuzunu yeniden boyutlandırmak veya silmek için bağlam menüsü seçeneklerini kullanın.    

   ![Bir kapasite havuzu için bağlam menüsü seçeneklerini gösteren ekran görüntüsü.](../media/azure-netapp-files/hard-quota-pool-options.png) 

   ![Havuzu yeniden boyutlandır penceresini gösteren ekran görüntüsü.](../media/azure-netapp-files/hard-quota-update-resize-pool.png) 


##### <a name="cli-or-powershell"></a>CLI veya PowerShell

Birim veya kapasite havuzu boyutunu el ile değiştirmek için Azure CLı ve Azure PowerShell dahil [Azure NetApp FILES CLI araçları](azure-netapp-files-sdk-cli.md#cli-tools)'nı kullanabilirsiniz.  Aşağıdaki iki komut Azure NetApp Files birim ve havuz kaynaklarını yönetmek için kullanılabilir:  

* [`az netappfiles pool`](/cli/azure/netappfiles/pool)
* [`az netappfiles volume`](/cli/azure/netappfiles/volume)

Azure CLı kullanarak Azure NetApp Files kaynaklarını yönetmek için, Azure portal açıp menü çubuğunun üst kısmındaki Azure **Cloud Shell** bağlantısını seçebilirsiniz: 

[![Cloud Shell bağlantısına nasıl erişebileceğiniz gösteren ekran görüntüsü. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png#lightbox)

Bu eylem Azure Cloud Shell açar:

[![Cloud Shell penceresini gösteren ekran görüntüsü. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png#lightbox)

Aşağıdaki örnekler, bir birimin boyutunu [göstermek](/cli/azure/netappfiles/volume#az_netappfiles_volume_show) ve [güncelleştirmek](/cli/azure/netappfiles/volume#az_netappfiles_volume_update) için komutları kullanır:
 
[![Birim boyutunu göstermek Için PowerShell 'i kullanmayı gösteren ekran görüntüsü. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png#lightbox)

[![Birim boyutunu güncelleştirmek Için PowerShell 'i kullanmayı gösteren ekran görüntüsü. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png#lightbox)

Aşağıdaki örnekler, bir kapasite havuzunun boyutunu [göstermek](/cli/azure/netappfiles/pool#az_netappfiles_pool_show) ve [güncelleştirmek](/cli/azure/netappfiles/pool#az_netappfiles_pool_update) için komutları kullanır:

[![Kapasite havuzu boyutunu göstermek Için PowerShell 'i kullanmayı gösteren ekran görüntüsü. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png#lightbox) 

[![Kapasite havuzu boyutunu güncelleştirmek Için PowerShell 'i kullanmayı gösteren ekran görüntüsü. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png#lightbox)

#### <a name="automated"></a>Otomatik  

Değiştirilen davranışı yönetmek için otomatik bir işlem oluşturabilirsiniz.

##### <a name="rest-api"></a>REST API   

Azure NetApp Files hizmetinin REST API, NetApp hesabı, kapasite havuzu, birimler ve anlık görüntüler gibi kaynaklara karşı HTTP işlemlerini tanımlar. Azure NetApp Files için REST API belirtimi [Azure NetApp Files Kaynak Yöneticisi GitHub sayfası](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)] aracılığıyla yayımlanır. GitHub 'da [REST API 'leriyle kullanılacak örnek kodu](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable/2020-06-01/examples) bulabilirsiniz.

Bkz. [REST API Azure NetApp Files Için geliştirme](azure-netapp-files-develop-with-rest-api.md). 

##### <a name="rest-api-using-powershell"></a>PowerShell kullanarak REST API  

Azure NetApp Files hizmetinin REST API, NetApp hesabı, kapasite havuzu, birimler ve anlık görüntüler gibi kaynaklara karşı HTTP işlemlerini tanımlar. [Azure NetApp Files için REST API belirtimi](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager) GitHub üzerinden yayımlanır.

Bkz. [PowerShell kullanarak REST API Azure NetApp Files Için geliştirme](develop-rest-api-powershell.md).

##### <a name="capacity-management-using-anfcapacitymanager"></a>ANFCapacityManager kullanarak kapasite yönetimi

ANFCapacityManager, kapasite tabanlı uyarı kurallarını yöneten bir Azure mantıksal uygulamasıdır. Azure NetApp Files birimlerinizin yer kaplamasını engellemek için birim boyutlarını otomatik olarak artırır. Uyarı göndermenin yanı sıra, Azure NetApp Files birimlerinde boş alan çalışmasını engellemek için birim ve kapasite havuzu boyutlarının otomatik artışını etkinleştirebilir: 

* İsteğe bağlı olarak, bir Azure NetApp Files birimi belirtilen yüzde yüzde eşiğine ulaştığında, birim kotası (boyut) 10-100 arasında belirtilen yüzdeye göre artar.  
* Birim boyutunun artırılması, kapsayan kapasite havuzunun kapasitesini aşarsa, kapasite havuzu boyutu da yeni birim boyutuna uyum sağlayacak şekilde artacaktır.

Aşağıdaki anahtar kapasitesi yönetim ayarını yapılandırabilirsiniz:  

* Belirtilen **% tam eşiğine** ulaşırsa, bir birimi otomatik olarak büyütmek için mevcut birim boyutunun yüzde **artırması yüzdesini artır** . 0 (sıfır) değeri, otomatik büyüme özelliğini devre dışı bırakacak. 10 ile 100 arasında bir değer önerilir.

    ![Birim otomatik büyüme yüzdesi penceresini gösteren ekran görüntüsü.](../media/azure-netapp-files/hard-quota-volume-anfcapacitymanager-auto-grow-percent.png) 

## <a name="faq"></a>SSS 

Bu bölüm, birim sabit kota değişikliğine ilişkin bazı sorulara yanıt verir. 

### <a name="does-snapshot-space-count-towards-the-usable-or-provisioned-capacity-of-a-volume"></a>Anlık görüntü alanı, bir birimin kullanılabilir veya sağlanmış kapasitesine doğru mi?

Evet, tüketilen anlık görüntü kapasitesi birimdeki sağlanan alana doğru sayılır. Birimin tam çalışması durumunda iki düzeltme seçeneği göz önünde bulundurun:

* Birimi bu makalede açıklandığı gibi yeniden boyutlandırın.
* Barındırma biriminde yer açmak için eski anlık görüntüleri kaldırın.

### <a name="does-this-change-mean-the-volume-auto-grow-behavior-will-disappear-from-azure-netapp-files"></a>Bu değişiklik, birim otomatik büyüme davranışının Azure NetApp Files kaybolacağı anlamına gelir.

Yaygın bir yanıltıcı, Azure NetApp Files *birimlerinin* doldurulmasıyla otomatik olarak büyümesine bağlıdır. Gerçek küme kotasından bağımsız olarak birimler 100 TiB boyutunda ölçülü kaynak olarak sağlandı, ancak düşük ölçekli *Kapasite havuzu* otomatik olarak 1-TİB artışlarla büyütülür. Bu değişiklik, küme kotasına (görünür ve kullanılabilir) *birimin* boyutunu adreslendirip *Kapasite havuzları* artık otomatik olarak büyümeyecektir. Bu değişiklik, yaygın olarak istenen doğru istemci tarafı alanı ve kapasite raporlamasına neden olur. "Ard arda" kapasite tüketimini önler.

### <a name="does-this-change-have-any-effect-on-volumes-replicated-with-cross-region-replication-preview"></a>Bu değişikliğin, bölgeler arası çoğaltma (Önizleme) ile çoğaltılan birimlerde etkileri var mı? 

Sabit birim kotası, çoğaltma hedef birimlerinde zorlanmaz.

### <a name="does-this-change-have-any-effect-on-metrics-currently-available-in-azure-monitor"></a>Bu değişikliğin Azure Izleyici 'de Şu anda kullanılabilir olan ölçümler üzerinde herhangi bir etkisi var mı?

Portal ölçümleri ve Azure Izleyici istatistikleri, yeni ayırma ve kullanım modelini doğru bir şekilde yansıtır.

### <a name="does-this-change-have-any-effect-on-the-resource-limits-for-azure-netapp-files"></a>Bu değişiklik Azure NetApp Files kaynak limitlerinin üzerinde herhangi bir etkiye sahip mi?

Bu makalede açıklanan kota değişikliklerinin ötesinde Azure NetApp Files için kaynak limitlerinin bir değişikliği yoktur.

### <a name="is-there-an-example-anfcapacitymanager-workflow"></a>Örnek bir ANFCapacityManager iş akışı var mı?  

Evet. Bkz. [birim otomatik büyüme Iş akışı örneği GitHub sayfası](https://github.com/ANFTechTeam/ANFCapacityManager/blob/master/ResizeWorkflow.md).

### <a name="is-anfcapacitymanager-microsoft-supported"></a>ANFCapacityManager Microsoft tarafından destekleniyor mu?  

[ANFCapacityManager mantıksal uygulaması olduğu gibi sağlanır ve NetApp veya Microsoft tarafından desteklenmez](https://github.com/ANFTechTeam/ANFCapacityManager#disclaimer). Belirli ortamınıza veya gereksinimlerinize uyacak şekilde değişiklik yapmanız önerilir. İşlevselliği, iş açısından kritik veya üretim ortamlarına dağıtılmadan önce test etmelisiniz.

### <a name="how-can-i-report-a-bug-or-submit-a-feature-request-for-anfcapacitymanger"></a>Anfcapacitymanager için bir hatayı nasıl bildirebilirim veya bir özellik isteği gönderebilirim?
[Anfcapacitymanager GitHub sayfasında](https://github.com/ANFTechTeam/ANFCapacityManager/issues) **yeni sorun** ' a tıklayarak hata ve özellik istekleri gönderebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
* [Kapasitesi havuzunu veya birimi yeniden boyutlandırma](azure-netapp-files-resize-capacity-pools-or-volumes.md) 
* [Azure NetApp Files için ölçümler](azure-netapp-files-metrics.md)
