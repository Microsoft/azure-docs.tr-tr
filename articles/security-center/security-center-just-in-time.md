---
title: Azure Güvenlik Merkezi 'nde tam zamanında sanal makine erişimi | Microsoft Docs
description: Bu belge, Azure Güvenlik Merkezi 'ndeki tam zamanında VM erişiminin Azure sanal makinelerinize erişimi denetlemenize nasıl yardımcı olduğunu gösterir.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: fb092a3c6b473680480c3bba0ad6f437176833de
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73576394"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Tam zamanında sanal makine erişimini yönetme

Tam zamanında (JıT) sanal makine (VM) erişimi, Azure VM 'lerinize gelen trafiği kilitlemek ve gerektiğinde VM 'lere bağlanmak için kolay erişim sağlarken saldırılara maruz kalma olasılığını azaltmak için kullanılabilir.

> [!NOTE]
> Tam zamanında özelliği, güvenlik merkezi 'nin standart katmanında bulunur. Güvenlik Merkezi’nin fiyatlandırma katmanları hakkında daha fazla bilgi almak için bkz. [Fiyatlandırma](security-center-pricing.md).


> [!NOTE]
> Güvenlik Merkezi tam zamanında VM erişimi şu anda yalnızca Azure Resource Manager aracılığıyla dağıtılan VM 'Leri desteklemektedir. Klasik ve Kaynak Yöneticisi dağıtım modelleri hakkında daha fazla bilgi için bkz. [Azure Resource Manager ve klasik dağıtım karşılaştırması](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="attack-scenario"></a>Saldırı senaryosu

Deneme yanılma saldırıları, bir VM 'ye erişim kazanmak için genellikle hedef yönetim bağlantı noktalarına bir yol olarak. Başarılı olursa, bir saldırgan VM üzerinde denetim alabilir ve ortamınıza bir altbilgi kurabilir.

Bir deneme yanılma saldırısı olasılığını azaltmanın bir yolu, bir bağlantı noktasının açık olduğu süreyi sınırlandırmaktır. Yönetim bağlantı noktalarının her zaman açık olması gerekmez. Yalnızca VM 'ye bağlıyken açık olmaları gerekir, örneğin, yönetim veya bakım görevlerini gerçekleştirmek için. Tam zamanında etkinleştirildiğinde Güvenlik Merkezi, [ağ güvenlik grubu](../virtual-network/security-overview.md#security-rules) (NSG) ve Azure Güvenlik duvarı kurallarını kullanarak, saldırganlar tarafından hedeflenememesi için yönetim bağlantı noktalarına erişimi kısıtlar.

![Tam zamanında senaryo](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>JıT erişimi nasıl çalışır?

Tam zamanında etkinleştirildiğinde, güvenlik merkezi bir NSG kuralı oluşturarak Azure VM 'lerinize gelen trafiği kilitler. Gelen trafiğin kilitlenebileceği sanal makine üzerindeki bağlantı noktalarını seçersiniz. Bu bağlantı noktaları, tam zamanında çözüm tarafından denetlenir.

Bir Kullanıcı bir VM 'ye erişim istediğinde, güvenlik merkezi kullanıcının bu VM için [rol tabanlı Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md) izinlerine sahip olduğunu denetler. İstek onaylanırsa, Güvenlik Merkezi, belirtilen süre boyunca seçilen bağlantı noktalarına ve istenen kaynak IP adreslerine veya aralıklarına gelen trafiğe izin vermek için ağ güvenlik gruplarını (NSG 'ler) ve Azure Güvenlik duvarını otomatik olarak yapılandırır. Sürenin süresi dolduktan sonra, güvenlik merkezi NSG 'leri önceki durumlarına geri yükler. Ancak önceden oluşturulan bağlantılar kesilmez.

 > [!NOTE]
 > Azure Güvenlik duvarının arkasındaki bir sanal makine için bir JıT erişim isteği onaylanmışsa, güvenlik merkezi hem NSG hem de güvenlik duvarı ilkesi kurallarını otomatik olarak değiştirir. Belirtilen süre boyunca, kurallar seçili bağlantı noktalarına gelen trafiğe ve istenen kaynak IP adreslerine veya aralıklarına izin verir. Süre bittikten sonra, Güvenlik Merkezi güvenlik duvarı ve NSG kurallarını önceki durumlarına geri yükler.


## <a name="permissions-needed-to-configure-and-use-jit"></a>JıT 'i yapılandırmak ve kullanmak için gerekli izinler

| Bir kullanıcıyı şu şekilde etkinleştirmek için: | Ayarlanacak izinler|
| --- | --- |
| VM için bir JıT ilkesi yapılandırma veya düzenleme | *Bu eylemleri role atayın:*  <ul><li>VM ile ilişkili bir abonelik veya kaynak grubu kapsamında:<br/> ```Microsoft.Security/locations/jitNetworkAccessPolicies/write``` </li><li> Bir aboneliğin veya kaynak grubunun veya VM 'nin kapsamında: <br/>```Microsoft.Compute/virtualMachines/write```</li></ul> | 
| ||
|Bir VM 'ye JıT erişimi isteme | *Bu eylemleri kullanıcıya ata:*  <ul><li>VM ile ilişkili bir abonelik veya kaynak grubu kapsamında:<br/>  ```Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action``` </li><li>  Bir aboneliğin veya kaynak grubunun veya VM 'nin kapsamında:<br/> ```Microsoft.Compute/virtualMachines/read``` </li></ul>|


## <a name="configure-jit-on-a-vm"></a>VM üzerinde JıT yapılandırma

Bir sanal makinede JıT ilkesi yapılandırmanın üç yolu vardır:

- [Azure Güvenlik Merkezi 'nde JıT erişimini yapılandırma](#jit-asc)
- [Azure VM dikey penceresinde JıT erişimini yapılandırma](#jit-vm)
- [VM 'de bir JıT ilkesini program aracılığıyla yapılandırma](#jit-program)

## <a name="configure-jit-in-security-center"></a>Güvenlik Merkezi 'nde JıT yapılandırma

Güvenlik Merkezi 'nden bir JıT ilkesi yapılandırabilir ve bir sanal makineye bir JıT ilkesi kullanarak erişim isteğinde olabilirsiniz


### Güvenlik Merkezi 'nde bir sanal makinede JıT erişimini yapılandırma<a name="jit-asc"></a>

1. **Güvenlik Merkezi** panosunu açın.

2. Sol bölmede, **tam ZAMANıNDA VM erişimi**' ni seçin.

    ![Tam zamanında VM erişimi kutucuğu](./media/security-center-just-in-time/just-in-time.png)

    **Tam ZAMANıNDA VM erişimi** penceresi açılır.

      ![Tam zamanında erişimi etkinleştir](./media/security-center-just-in-time/enable-just-in-time.png)

    **Tam ZAMANıNDA VM erişimi** , VM 'lerinizin durumu hakkında bilgi sağlar:

    - **Yapılandırıldı** -tam zamanında VM erişimini destekleyecek şekilde yapılandırılmış VM 'ler. Sunulan veriler geçen hafta içindir ve her VM için onaylanan istek sayısı, son erişim tarihi ve saati ve son kullanıcı için içerir.
    - **Tam ZAMANıNDA** VM erişimini destekleyebilen, ancak için yapılandırılmamış VM 'ler. Bu VM 'Ler için tam zamanında VM erişimi denetimini etkinleştirmenizi öneririz.
    - **Öneri olmayan** - Bir VM’nin önerilmemesinin olası nedenleri şunlardır:
      - Eksik NSG-tam zamanında çözüm, bir NSG 'nin yerinde olmasını gerektirir.
      - Klasik VM-Güvenlik Merkezi tam zamanında VM erişimi şu anda yalnızca Azure Resource Manager aracılığıyla dağıtılan VM 'Leri desteklemektedir. Klasik bir dağıtım, tam zamanında çözüm tarafından desteklenmez. 
      - Diğer-bir sanal makine, aboneliğin veya kaynak grubunun güvenlik ilkesinde tam zamanında çözüm kapatılmışsa veya VM 'nin genel IP 'si eksikse ve bir NSG yoksa, bu kategoride olur.

3. **Önerilen** sekmeyi seçin.

4. **Sanal makine**altında, etkinleştirmek Istediğiniz VM 'lere tıklayın. Bu, bir VM 'nin yanına onay işareti koyar.

5. **VM 'LERDE JIT 'ı etkinleştir**' e tıklayın.
   -. Bu dikey pencere, Azure Güvenlik Merkezi tarafından önerilen varsayılan bağlantı noktalarını görüntüler:
      - 22-SSH
      - 3389-RDP
      - 5985-WinRM 
      - 5986-WinRM
6. Özel bağlantı noktalarını da yapılandırabilirsiniz:

      1. **Ekle**'ye tıklayın. **Bağlantı noktası yapılandırması Ekle** penceresi açılır.
      2. Yapılandırmak üzere seçtiğiniz her bağlantı noktası için, hem varsayılan hem de özel, aşağıdaki ayarları özelleştirebilirsiniz:

    - **Protokol türü**-bir istek onaylandığında bu bağlantı noktasında izin verilen protokol.
    - **Izin verilen kaynak IP adresleri**-bir istek onaylandığında bu bağlantı noktasında ızın verilen IP aralıkları.
    - **En fazla istek süresi**-belirli bir bağlantı noktasının açılabileceği en uzun zaman penceresi.

     3. **Tamam** düğmesine tıklayın.

1. **Kaydet** düğmesine tıklayın.

> [!NOTE]
>Azure Güvenlik Merkezi, bir VM için JıT VM erişimi etkinleştirildiğinde, ilişkili ağ güvenlik gruplarında ve Azure Güvenlik Duvarı 'nda bulunan seçili bağlantı noktaları için "tüm gelen trafiği reddet" kurallarını oluşturur. Seçilen bağlantı noktaları için başka kurallar oluşturulduysa, mevcut kurallar yeni "tüm gelen trafiği reddetme" kuralları üzerinden önceliklidir. Seçilen bağlantı noktalarında mevcut kurallar yoksa, yeni "tüm gelen trafiği reddetme" kuralları, ağ güvenlik gruplarında ve Azure Güvenlik duvarında en üst önceliğe sahip olacaktır.


## <a name="request-jit-access-via-security-center"></a>Güvenlik Merkezi aracılığıyla JıT erişimi iste

Bir sanal makineye Güvenlik Merkezi aracılığıyla erişim istemek için:

1. **Tam ZAMANıNDA VM erişimi**altında, **yapılandırılan** sekmesini seçin.

2. **Sanal makine**altında, erişim Istemek Istediğiniz VM 'lere tıklayın. Bu, VM 'nin yanına bir onay işareti koyar.


    - **Bağlantı ayrıntıları** sütunundaki SIMGE, NSG veya FW üzerinde JIT özelliğinin etkin olup olmadığını gösterir. Her ikisinde de etkinse yalnızca güvenlik duvarı simgesi görüntülenir.

    - **Bağlantı ayrıntıları** sütunu, VM 'yi bağlamak için gereken bilgileri ve açık bağlantı noktalarını sağlar.

      ![Tam zamanında erişim isteme](./media/security-center-just-in-time/request-just-in-time-access.png)

3. **Erişim iste**' ye tıklayın. **Erişim iste** penceresi açılır.

      ![JıT ayrıntıları](./media/security-center-just-in-time/just-in-time-details.png)

4. **Erişim iste**, her VM için, açmak istediğiniz bağlantı noktalarını ve bağlantı noktasının açık olduğu kaynak IP adreslerini ve bağlantı noktasının açılacağı zaman penceresini yapılandırın. Yalnızca tam zamanında ilkesinde yapılandırılmış bağlantı noktalarına erişim isteğinde bulunur. Her bağlantı noktası, tam zamanında ilkeden elde edilen izin verilen en fazla süreyi içerir.

5. **Açık bağlantı noktaları**' na tıklayın.

> [!NOTE]
> Erişim isteyen bir Kullanıcı proxy 'nin arkasındaysa, **alanım** seçeneği çalışmayabilir. Kuruluşun tam IP adres aralığını tanımlamanız gerekebilir.

## <a name="edit-a-jit-access-policy-via-security-center"></a>Güvenlik Merkezi ile bir JıT erişim ilkesini düzenleme

Bu VM 'ye karşı koruma için yeni bir bağlantı noktası ekleyip yapılandırarak veya zaten korumalı bir bağlantı noktasıyla ilgili diğer ayarları değiştirerek bir sanal makinenin mevcut tam zamanında ilkesini değiştirebilirsiniz.

Bir VM 'nin mevcut bir tam zamanında ilkesini düzenlemek için:
1. **Yapılandırılan** sekmede, VM **'ler**altında, söz konusu VM 'nin satır içindeki üç noktaya tıklayarak BIR bağlantı noktasının ekleneceği VM 'yi seçin. 

1. **Düzenle**’yi seçin.
1. **JıT VM erişimi yapılandırması**altında, zaten korumalı bir bağlantı noktasının var olan ayarlarını düzenleyebilir ya da yeni bir özel bağlantı noktası ekleyebilirsiniz. 
  ![JIT VM erişimi](./media/security-center-just-in-time/edit-policy.png)

## <a name="audit-jit-access-activity-in-security-center"></a>Güvenlik Merkezi 'nde JıT erişim etkinliğini denetleme

Günlük aramasını kullanarak VM etkinlikleri hakkında öngörüler elde edebilirsiniz. Günlükleri görüntülemek için:

1. **Tam ZAMANıNDA VM erişimi**altında, **yapılandırılan** sekmesini seçin.
2. VM **'ler**altında, söz konusu VM 'nin satır içindeki üç noktaya tıklayarak ve menüden **etkinlik günlüğü** ' nü seçerek hakkında BILGI görüntülemek için bir VM seçin. **Etkinlik günlüğü** açılır.

   ![Etkinlik günlüğü seçin](./media/security-center-just-in-time/select-activity-log.png)

   **Etkinlik günlüğü** , o VM için zaman, tarih ve abonelikle birlikte önceki işlemlerin filtrelenmiş bir görünümünü sağlar.

**Tüm ÖĞELERI CSV olarak indirmek için buraya tıklayın ' ı**seçerek günlük bilgilerini indirebilirsiniz.

Filtreleri değiştirin ve bir arama ve günlük oluşturmak için **Uygula** ' ya tıklayın.



## Azure VM 'nin sayfasından JıT erişimini yapılandırma<a name="jit-vm"></a>

Kolaylık olması için, güvenlik merkezi 'nde VM 'nin içinden JıT kullanarak doğrudan bir VM 'ye bağlanabilirsiniz.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-page"></a>Azure VM sayfası aracılığıyla bir VM 'de JıT erişimini yapılandırma

Sanal makinelerinizden tam zamanında erişimi daha kolay hale getirmek için bir VM 'yi doğrudan VM içinden yalnızca tam zamanında erişime izin verecek şekilde ayarlayabilirsiniz.

1. Azure portal **sanal makineler**' i seçin.
2. Tam zamanında erişim ile sınırlamak istediğiniz sanal makineye tıklayın.
3. Menüde **yapılandırma**' ya tıklayın.
4. Tam **zamanında erişim** altında **tam zamanında ilkeyi etkinleştir**' e tıklayın. 

Bu, aşağıdaki ayarları kullanarak VM için tam zamanında erişim imkanı sunar:

- Windows Server 'lar:
    - RDP bağlantı noktası 3389
    - İzin verilen en fazla üç saat
    - İzin verilen kaynak IP adresleri any olarak ayarlandı
- Linux sunucuları:
    - SSH bağlantı noktası 22
    - İzin verilen en fazla üç saat
    - İzin verilen kaynak IP adresleri any olarak ayarlandı
     
Bir VM 'nin zaten etkin bir şekilde etkinleştirilmesi durumunda, yapılandırma sayfasına gittiğinizde, tam zamanında etkin olduğunu görebilir ve bu bağlantıyı, ayarları görüntülemek ve değiştirmek için Azure Güvenlik Merkezi 'ndeki ilkeyi açmak için kullanabilirsiniz.

![VM 'de JIT yapılandırması](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-the-azure-vm-blade"></a>Azure VM dikey penceresi aracılığıyla bir VM 'ye JıT erişimi isteme

Azure portal, bir VM 'ye bağlanmaya çalıştığınızda, Azure bu VM 'de tam zamanında erişim ilkesi yapılandırılıp yapılandırılmadığını denetler.

- VM üzerinde yapılandırılmış bir JıT ilkeniz varsa, sanal makine için JıT ilke kümesine uygun olarak erişiminizin olmasını sağlamak için **erişim iste** ' ye tıklayabilirsiniz. 

  >![JIT isteği](./media/security-center-just-in-time/jit-request.png)

  Erişim aşağıdaki varsayılan parametrelerle istenir:

  - **kaynak IP**: ' any ' (*) (değiştirilemez)
  - **zaman aralığı**: üç saat (değiştirilemez) <!--Isn't this set in the policy-->
  - **bağlantı noktası numarası** Linux için Windows/Port 22 için RDP bağlantı noktası 3389 (değiştirilebilir)

    > [!NOTE]
    > Azure Güvenlik Duvarı tarafından korunan bir VM için bir istek onaylandıktan sonra, güvenlik merkezi kullanıcıya, sanal makineye bağlanmak için kullanmak üzere uygun bağlantı ayrıntılarını (DNAT tablosundan bağlantı noktası eşlemesi) sağlar.

- Bir VM üzerinde JıT yapılandırılmamışsa, bir JıT İlkesi yapılandırmanız istenir.

  ![JIT istemi](./media/security-center-just-in-time/jit-prompt.png)

## VM 'de bir JıT ilkesini program aracılığıyla yapılandırma<a name="jit-program"></a>

REST API 'Leri aracılığıyla ve PowerShell aracılığıyla tam zamanında kurulum ve kullanım yapabilirsiniz.

## <a name="jit-vm-access-via-rest-apis"></a>REST API 'Leri aracılığıyla JıT VM erişimi

Tam zamanında VM erişimi özelliği Azure Güvenlik Merkezi API 'SI aracılığıyla kullanılabilir. Yapılandırılan VM 'Ler hakkında bilgi alabilir, yenilerini ekleyebilir, bir VM 'ye erişim isteğinde bulunabilir ve daha fazlasını bu API aracılığıyla yapabilirsiniz. Tam zamanında REST API hakkında daha fazla bilgi edinmek için bkz. [JIT ağ erişim ilkeleri](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies).

## <a name="jit-vm-access-via-powershell"></a>PowerShell aracılığıyla JıT VM erişimi

PowerShell aracılığıyla tam zamanında VM erişimi çözümünü kullanmak için resmi Azure Güvenlik Merkezi PowerShell cmdlet 'lerini kullanın ve özellikle `Set-AzJitNetworkAccessPolicy`.

Aşağıdaki örnek, belirli bir sanal makinede tam zamanında VM erişim ilkesi ayarlıyor ve aşağıdakileri ayarlıyor:

1.  22 ve 3389 bağlantı noktalarını kapatın.

2.  Her biri için en fazla 3 saat, her bir onaylanan istek için açılabilecekleri bir zaman penceresi ayarlayın.
3.  Kaynak IP adreslerini denetlemek için erişim isteyen kullanıcının, bir tam zamanında erişim isteği üzerinde başarılı bir oturum kurmasını sağlar.

Bunu gerçekleştirmek için PowerShell 'de aşağıdakileri çalıştırın:

1.  Bir VM için tam zamanında VM erişim ilkesini tutan bir değişken atayın:

        $JitPolicy = (@{
         id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})

2.  VM 'ye tam zamanında VM erişim ilkesini bir diziye ekleyin:
    
        $JitPolicyArr=@($JitPolicy)

3.  Seçilen VM 'de tam zamanında VM erişim ilkesini yapılandırın:
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

#### <a name="request-access-to-a-vm-via-powershell"></a>PowerShell aracılığıyla bir VM 'ye erişim isteme

Aşağıdaki örnekte, belirli bir IP adresi ve belirli bir süre için, bağlantı noktası 22 ' nin açılabileceği belirli bir sanal makineye tam zamanında VM erişimi isteği görebilirsiniz:

PowerShell 'de aşağıdakileri çalıştırın:
1.  VM isteği erişim özelliklerini yapılandırma

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  VM erişim isteği parametrelerini bir diziye ekleyin:

        $JitPolicyArr=@($JitPolicyVm1)
3.  İstek erişimini gönderin (1. adımda aldığınız kaynak KIMLIĞINI kullanın)

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Daha fazla bilgi için bkz. PowerShell cmdlet belgeleri.

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, güvenlik merkezi 'nde tam zamanında VM erişiminin Azure sanal makinelerinize erişimi denetlemenize nasıl yardımcı olduğunu öğrendiniz.

Güvenlik Merkezi hakkında daha fazla bilgi edinmek için şunlara bakın:

- [Güvenlik Ilkelerini ayarlama](tutorial-security-policy.md) -Azure abonelikleriniz ve kaynak gruplarınız için güvenlik ilkelerini yapılandırma hakkında bilgi edinin.
- [Güvenlik önerilerini yönetme](security-center-recommendations.md) — önerilerin Azure kaynaklarınızı korumanıza nasıl yardımcı olduğunu öğrenin.
- [Güvenlik sistem durumu izleme](security-center-monitoring.md) — Azure kaynaklarınızın sistem durumunu izlemeyi öğrenin.
- [Güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md) -güvenlik uyarılarını yönetme ve yanıtlama hakkında bilgi edinin.
- [İş ortağı çözümlerini izleme](security-center-partner-solutions.md) -iş ortağı çözümlerinizin sistem durumunu izlemeyi öğrenin.
- [Güvenlik MERKEZI SSS](security-center-faq.md) — hizmeti kullanma hakkında sık sorulan soruları bulun.
- [Azure Güvenlik blogu](https://blogs.msdn.microsoft.com/azuresecurity/) - Azure güvenliği ve uyumluluğu ile ilgili blog yazılarını bulabilirsiniz.

