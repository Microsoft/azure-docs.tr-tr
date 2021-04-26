---
title: Azure Güvenlik Merkezi 'nde tam zamanında sanal makine erişimi | Microsoft Docs
description: Bu belge, Azure Güvenlik Merkezi 'ndeki tam zamanında VM erişiminin (JıT) Azure sanal makinelerinize erişimi denetlemenize nasıl yardımcı olduğunu gösterir.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 60ae36d80e34f27ed68c679f47edacf3e402417c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98916159"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Tam zamanında erişimli yönetim bağlantı noktalarınızı güvenli hale getirin

Azure Güvenlik Merkezi 'nin tam zamanında (JıT) sanal makinesi (VM) erişimi özelliği ile Azure sanal makinelerinize gelen trafiği kilitleyin. Bu, bir VM 'ye bağlanmanız gerektiğinde kolay erişim sağlarken saldırılara maruz kalmayı azaltır.

JıT 'in nasıl çalıştığı ve temel alınan mantık hakkında tam bir açıklama için bkz. tam [zamanında açıklanacak](just-in-time-explained.md).

Bu sayfa, güvenlik programınıza JıT ekleme hakkında öğretir. Şunları öğrenirsiniz: 

- **Sanal makinelerinizde JIT 'ı etkinleştirin** -Güvenlik Merkezi, PowerShell veya REST API kullanarak bir veya daha fazla VM için kendi özel SEÇENEKLERINIZDE JIT 'i etkinleştirebilirsiniz. Alternatif olarak, Azure sanal makinelerinden varsayılan, sabit kodlanmış parametrelerle JıT 'i etkinleştirebilirsiniz. Etkinleştirildiğinde JıT, ağ güvenlik grubunuzda bir kural oluşturarak Azure VM 'lerinize gelen trafiği kilitler.
- **JIT etkin olan BIR VM 'ye erişim isteme** -JIT 'in hedefi, gelen trafiğiniz kilitlense bile, güvenlik merkezi 'Nin gerektiğinde VM 'lere bağlanmak için kolay erişim sağladığından emin olmaktır. Güvenlik Merkezi, Azure sanal makineler, PowerShell veya REST API JıT özellikli bir VM 'ye erişim isteğinde bulabilirsiniz.
- **Etkinliği denetleme** -sanal makinelerinizin uygun şekilde güvende olduğundan emin olmak için, düzenli güvenlik denetlerinizin bir PARÇASı olarak JIT özellikli sanal makinelerinize erişimi gözden geçirin.   



## <a name="availability"></a>Kullanılabilirlik

|Görünüş|Ayrıntılar|
|----|:----|
|Yayın durumu:|Genel kullanılabilirlik (GA)|
|Fiyat|[Sunucular Için Azure Defender](defender-for-servers-introduction.md) gerekir|
|Desteklenen VM 'Ler:|![](./media/icons/yes-icon.png)Azure Resource Manager aracılığıyla dağıtılan Evet VM 'leri.<br>![](./media/icons/no-icon.png)Klasik dağıtım modelleriyle dağıtılan VM yok. [Bu dağıtım modelleri hakkında daha fazla bilgi edinin](../azure-resource-manager/management/deployment-models.md).<br>![Azure ](./media/icons/no-icon.png) [güvenlik duvarı Yöneticisi](../firewall-manager/overview.md) tarafından denetlenen Azure Güvenlik duvarları tarafından korunan VM yok|
|Gerekli roller ve izinler:|**Reader** ve **securityreader** ROLLERININ her ikisi de JIT durumunu ve parametrelerini görüntüleyebilir.<br>JıT ile çalışan özel Roller oluşturmak için bkz. JıT 'i [yapılandırmak ve kullanmak Için hangi izinler gereklidir?](just-in-time-explained.md#what-permissions-are-needed-to-configure-and-use-jit).<br>Bir VM 'ye JıT erişimi istemesi ve başka bir JıT işlemi gerçekleştirihtiyacı olmayan kullanıcılar için en az ayrıcalıklı bir rol oluşturmak üzere, güvenlik merkezi GitHub topluluk sayfalarından [set-Jleastprivilegedrole betiğini](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role) kullanın.|
|Larının|![Yes](./media/icons/yes-icon.png) Ticari bulutlar<br>![Yes](./media/icons/yes-icon.png) Ulusal/Sogeign (US Gov, Çin gov, diğer gov)|
|||


## <a name="enable-jit-vm-access"></a>JıT VM erişimini etkinleştir <a name="jit-configure"></a>

Güvenlik Merkezi veya programlama yoluyla bir veya daha fazla VM için kendi özel seçeneklerinizde JıT VM erişimini etkinleştirebilirsiniz. 

Alternatif olarak, Azure sanal makinelerinden varsayılan, sabit kodlanmış parametrelerle JıT 'i etkinleştirebilirsiniz.

Bu seçeneklerin her biri aşağıdaki ayrı bir sekmede açıklanmıştır.

### <a name="azure-security-center"></a>[**Azure Güvenlik Merkezi**](#tab/jit-config-asc)

### <a name="enable-jit-on-your-vms-from-azure-security-center"></a>Azure Güvenlik Merkezi 'nden sanal makinelerinizde JıT 'i etkinleştirin <a name="jit-asc"></a>

:::image type="content" source="./media/security-center-just-in-time/jit-config-security-center.gif" alt-text="Azure Güvenlik Merkezi 'nde JıT VM erişimini yapılandırma":::

Güvenlik Merkezi 'nden JıT VM erişimini etkinleştirebilir ve yapılandırabilirsiniz.

1. Azure Defender panosunu açın ve gelişmiş koruma alanından **tam ZAMANıNDA VM erişimi**' ni seçin.

    **Tam ZAMANıNDA VM erişimi** sayfası, VM 'leriniz aşağıdaki sekmelerde gruplandırılarak açılır:

    - **Yapılandırıldı** -tam zamanında VM erişimini destekleyecek şekilde yapılandırılmış VM 'ler. Her VM için, yapılandırılan sekmede şunları gösterir:
        - Son yedi gündeki onaylanan JıT isteği sayısı
        - son erişim tarihi ve saati
        - Bağlantı ayrıntıları yapılandırıldı
        - Son Kullanıcı
    - **Yapılandırılmadı** -JIT etkin olmayan VM 'ler, ancak JIT desteği sağlayabilir. Bu VM 'Ler için JıT 'i etkinleştirmenizi öneririz.
    - **Desteklenmeyen** -JIT etkin olmayan ve özelliği desteklemeyen VM 'ler. VM 'niz aşağıdaki nedenlerle bu sekmede olabilir:
      - Ağ güvenlik grubu (NSG) eksik-JıT, bir NSG 'nin yapılandırılmasını gerektiriyor
      - Klasik VM-JıT, ' klasik dağıtım ' değil Azure Resource Manager aracılığıyla dağıtılan VM 'Leri destekler. [Klasik vs Azure Resource Manager dağıtım modelleri hakkında daha fazla bilgi edinin](../azure-resource-manager/management/deployment-models.md).
      - Diğer-VM 'Niz, aboneliğin veya kaynak grubunun güvenlik ilkesinde devre dışı bırakılmışsa Bu sekmede olabilir.

1. **Yapılandırılmadı** SEKMESINDE, JIT Ile korunacak VM 'leri Işaretleyin ve **VM 'Lerde JIT 'i etkinleştir**' i seçin. 

    Güvenlik Merkezi 'nin korumayı önerdiği bağlantı noktalarını listelemek için JıT VM erişimi sayfası açılır:
    - 22-SSH
    - 3389-RDP
    - 5985-WinRM 
    - 5986-WinRM

    Varsayılan ayarları kabul etmek için **Kaydet**' i seçin.

1. JıT seçeneklerini özelleştirmek için:

    - **Ekle** düğmesine sahip özel bağlantı noktaları ekleyin. 
    - Listeden seçerek varsayılan bağlantı noktalarından birini değiştirin.

    Her bağlantı noktası için (özel ve varsayılan) **bağlantı noktası yapılandırması Ekle** bölmesi aşağıdaki seçenekleri sunar:

    - **Protokol**-bir istek onaylandığında bu bağlantı noktasında izin verilen protokol
    - **Izin verilen kaynak IP 'leri**-bir istek onaylandığında bu bağlantı noktasında ızın verilen IP aralıkları
    - **En fazla istek süresi**-belirli bir bağlantı noktasının açılabileceği en uzun zaman penceresi

     1. Bağlantı noktası güvenliğini gereksinimlerinize göre ayarlayın.

     1. **Tamam**’ı seçin.

1. **Kaydet**’i seçin.



### <a name="edit-the-jit-configuration-on-a-jit-enabled-vm-using-security-center"></a>Güvenlik Merkezi 'ni kullanarak JıT yapılandırmasını JıT etkin bir VM üzerinde düzenleme <a name="jit-modify"></a>

Bu VM için koruma için yeni bir bağlantı noktası ekleyip yapılandırarak veya zaten korumalı bir bağlantı noktasıyla ilgili diğer ayarları değiştirerek bir sanal makinenin tam zamanında yapılandırmasını değiştirebilirsiniz.

Bir VM için mevcut JıT kurallarını düzenlemek için:

1. Azure Defender panosunu açın ve gelişmiş koruma alanından **Uyarlamalı uygulama denetimleri**' ni seçin.

1. **Yapılandırılmış** sekmesinden, bağlantı noktası eklemek istediğiniz sanal makineye sağ tıklayın ve Düzenle ' yi seçin. 

    ![Azure Güvenlik Merkezi 'nde bir JıT VM erişim yapılandırmasını düzenle](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

1. **JıT VM erişimi yapılandırması** altında, zaten korumalı bir bağlantı noktasının var olan ayarlarını düzenleyebilir ya da yeni bir özel bağlantı noktası ekleyebilirsiniz.

1. Bağlantı noktalarını düzenledikten sonra **Kaydet**' i seçin.
 


### <a name="azure-virtual-machines"></a>[**Azure sanal makineleri**](#tab/jit-config-avm)

### <a name="enable-jit-on-your-vms-from-azure-virtual-machines"></a>Azure sanal makinelerinizdeki VM 'leriniz üzerinde JıT 'i etkinleştirin

Azure portal Azure sanal makineler sayfalarından bir VM 'de JıT 'i etkinleştirebilirsiniz.

![Azure sanal makinelerinde JıT VM erişimini yapılandırma](./media/security-center-just-in-time/jit-config-virtual-machines.gif)

> [!TIP]
> Bir VM 'nin zaten etkin bir şekilde etkinleştirilmesi durumunda, yapılandırma sayfasına gittiğinizde, tam zamanında etkin olduğunu görürsünüz ve bağlantıyı, güvenlik merkezi 'nde tam zamanında VM erişimi sayfasını açmak için kullanabilir ve ayarları görüntüleyebilir ve değiştirebilirsiniz.

1. [Azure Portal](https://ms.portal.azure.com), **sanal makineleri** arayıp seçin. 

1. JıT ile korumak istediğiniz sanal makineyi seçin.

1. Menüde **yapılandırma**' yı seçin.

1. **Tam zamanında erişim** altında, **tam zamanında etkinleştir**' i seçin. 

    Bu, aşağıdaki varsayılan ayarları kullanarak VM için tam zamanında erişim imkanı sunar:

    - Windows makineleri:
        - RDP bağlantı noktası 3389
        - İzin verilen en fazla üç saat
        - İzin verilen kaynak IP adresleri any olarak ayarlandı
    - Linux makineleri:
        - SSH bağlantı noktası 22
        - İzin verilen en fazla üç saat
        - İzin verilen kaynak IP adresleri any olarak ayarlandı

1. Bu değerlerden herhangi birini düzenlemek veya JıT yapılandırmanıza daha fazla bağlantı noktası eklemek için Azure Güvenlik Merkezi 'nin tam zamanında sayfasını kullanın:

    1. Güvenlik Merkezi menüsünde, **tam ZAMANıNDA VM erişimi**' ni seçin.

    1. **Yapılandırılmış** sekmesinden, bağlantı noktası eklemek istediğiniz sanal makineye sağ tıklayın ve Düzenle ' yi seçin. 

        ![Azure Güvenlik Merkezi 'nde bir JıT VM erişim yapılandırmasını düzenle](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

    1. **JıT VM erişimi yapılandırması** altında, zaten korumalı bir bağlantı noktasının var olan ayarlarını düzenleyebilir ya da yeni bir özel bağlantı noktası ekleyebilirsiniz.

    1. Bağlantı noktalarını düzenledikten sonra **Kaydet**' i seçin.


### <a name="powershell"></a>[**PowerShell**](#tab/jit-config-powershell)

### <a name="enable-jit-on-your-vms-using-powershell"></a>PowerShell kullanarak VM'lerinizde JIT'yi etkinleştirme

PowerShell 'den tam zamanında VM erişimini etkinleştirmek için resmi Azure Güvenlik Merkezi PowerShell cmdlet 'ini kullanın `Set-AzJitNetworkAccessPolicy` .

**Örnek** -aşağıdaki kurallara sahip belırlı bir sanal makinede tam zamanında VM erişimini etkinleştirin:

* Bağlantı noktalarını kapat 22 ve 3389
* Her biri için en fazla 3 saat, her bir onaylanan istek için açılabilecekleri bir zaman penceresi ayarlayın
* Kaynak IP adreslerini denetlemek için erişim isteyen kullanıcıya izin ver
* Erişim isteyen kullanıcının onaylanan bir tam zamanında erişim isteği üzerinde başarılı bir oturum kurmasını sağlar

Aşağıdaki PowerShell komutları bu JıT yapılandırmasını oluşturur:

1. Bir VM için tam zamanında VM erişim kurallarını tutan bir değişken atayın:

    ```azurepowershell
    $JitPolicy = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
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
    ```

1. VM 'nin tam zamanında VM erişim kurallarını bir diziye ekleyin:
    
    ```azurepowershell
    $JitPolicyArr=@($JitPolicy)
    ```

1. Seçilen VM 'de tam zamanında VM erişim kurallarını yapılandırın:
    
    ```azurepowershell
    Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr
    ```

    VM belirtmek için-name parametresini kullanın. Örneğin, VM1 ve VM2 olmak üzere iki farklı VM için JıT yapılandırması oluşturmak için: ```Set-AzJitNetworkAccessPolicy -Name VM1``` ve kullanın ```Set-AzJitNetworkAccessPolicy -Name VM2``` .


### <a name="rest-api"></a>[**REST API**](#tab/jit-config-api)

### <a name="enable-jit-on-your-vms-using-the-rest-api"></a>REST API kullanarak sanal makinelerinizdeki JıT 'i etkinleştirin

Tam zamanında VM erişimi özelliği Azure Güvenlik Merkezi API 'SI aracılığıyla kullanılabilir. Yapılandırılan VM 'Ler hakkında bilgi almak, yenilerini eklemek, bir VM 'ye erişim istemek ve daha fazlasını yapmak için bu API 'yi kullanın. 

[JIT ağ erişim ilkeleri](/rest/api/securitycenter/jitnetworkaccesspolicies)hakkında daha fazla bilgi edinin.


--- 










## <a name="request-access-to-a-jit-enabled-vm"></a>JIT özellikli VM'ye erişim isteme

Azure portal (Güvenlik Merkezi veya Azure sanal makinelerinde) veya program aracılığıyla JıT özellikli bir VM 'ye erişim isteğinde bulabilirsiniz.

Bu seçeneklerin her biri aşağıdaki ayrı bir sekmede açıklanmıştır.

### <a name="azure-security-center"></a>[**Azure Güvenlik Merkezi**](#tab/jit-request-asc)

### <a name="request-access-to-a-jit-enabled-vm-from-azure-security-center"></a>Azure Güvenlik Merkezi 'nden JıT özellikli bir VM 'ye erişim isteme 

Bir sanal makinede JıT etkin olduğunda, bu sunucuya bağlanmak için erişim istemeniz gerekir. JıT 'i etkinleştirmenizin ne olursa olsun, desteklenen yollarla erişim isteğinde buluntırabilirsiniz.

:::image type="content" source="./media/security-center-just-in-time/jit-request-security-center.gif" alt-text="Güvenlik Merkezi 'nden JıT erişimi isteme":::

1. **Tam ZAMANıNDA VM erişimi** sayfasında, **yapılandırılan** sekmesini seçin.

1. Erişmek istediğiniz VM 'Leri işaretleyin.

    - **Bağlantı ayrıntıları** sütunundaki simge, ağ güvenlik grubunda veya GÜVENLIK duvarında JIT özelliğinin etkin olup olmadığını gösterir. Her ikisinde de etkinse yalnızca güvenlik duvarı simgesi görüntülenir.

    - **Bağlantı ayrıntıları** sütunu, VM 'yi bağlamak için gereken bilgileri ve açık bağlantı noktalarını sağlar.

1. **Erişim iste**' yi seçin. **Erişim iste** penceresi açılır.

1. **Erişim iste**, her VM için, açmak istediğiniz bağlantı noktalarını ve bağlantı noktasının açık olduğu kaynak IP adreslerini ve bağlantı noktasının açılacağı zaman penceresini yapılandırın. Yalnızca yapılandırılan bağlantı noktalarına erişim istemek mümkün olacaktır. Her bağlantı noktasının, oluşturduğunuz JıT yapılandırmasından elde edilen izin verilen en uzun süre vardır.

1. **Açık bağlantı noktaları**' nı seçin.

> [!NOTE]
> Erişim isteyen bir Kullanıcı proxy 'nin arkasındaysa, **alanım** seçeneği çalışmayabilir. Kuruluşun tam IP adres aralığını tanımlamanız gerekebilir.



### <a name="azure-virtual-machines"></a>[**Azure sanal makineleri**](#tab/jit-request-avm)

### <a name="request-access-to-a-jit-enabled-vm-from-the-azure-virtual-machines-connect-page"></a>Azure sanal makinesinin Connect sayfasından JıT özellikli bir VM 'ye erişim isteme

Bir sanal makinede JıT etkin olduğunda, bu sunucuya bağlanmak için erişim istemeniz gerekir. JıT 'i etkinleştirmenizin ne olursa olsun, desteklenen yollarla erişim isteğinde buluntırabilirsiniz.

  >![JIT tam zamanında istek](./media/security-center-just-in-time/jit-request-vm.png)


Azure sanal makinelerinden erişim istemek için:

1. Azure portal, sanal makineler sayfalarını açın.

1. Bağlanmak istediğiniz VM 'yi seçin ve **Bağlan** sayfasını açın.

    Azure, bu VM üzerinde JıT özelliğinin etkin olup olmadığını denetler.

    - VM için JıT etkinleştirilmemişse, etkinleştirmeniz istenir.

    - JıT 'in etkin olması durumunda, istenen IP, zaman aralığı ve bu VM için yapılandırılmış bağlantı noktalarıyla erişim isteği geçirmek için **erişim iste** ' yi seçin.

> [!NOTE]
> Azure Güvenlik Duvarı tarafından korunan bir VM için bir istek onaylandıktan sonra, güvenlik merkezi kullanıcıya, sanal makineye bağlanmak için kullanmak üzere uygun bağlantı ayrıntılarını (DNAT tablosundan bağlantı noktası eşlemesi) sağlar.



### <a name="powershell"></a>[**PowerShell**](#tab/jit-request-powershell)

### <a name="request-access-to-a-jit-enabled-vm-using-powershell"></a>PowerShell kullanarak JIT özellikli VM'ye erişim isteme

Aşağıdaki örnekte, belirli bir IP adresi ve belirli bir süre için, bağlantı noktası 22 ' nin açılabileceği belirli bir sanal makineye tam zamanında VM erişimi isteği görebilirsiniz:

PowerShell 'de aşağıdakileri çalıştırın:

1. VM isteği erişim özelliklerini yapılandırın:

    ```azurepowershell
    $JitPolicyVm1 = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
           number=22;
           endTimeUtc="2020-07-15T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
    ```

1. VM erişim isteği parametrelerini bir diziye ekleyin:

    ```azurepowershell
    $JitPolicyArr=@($JitPolicyVm1)
    ```
        
1. İstek erişimini gönderin (1. adımdan kaynak KIMLIĞINI kullanın)

    ```azurepowershell
    Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr
    ```

[PowerShell cmdlet belgelerinde](/powershell/scripting/developer/cmdlet/cmdlet-overview)daha fazla bilgi edinin.



### <a name="rest-api"></a>[**REST API**](#tab/jit-request-api)

### <a name="request-access-to-a-jit-enabled-vms-using-the-rest-api"></a>REST API kullanarak JıT özellikli bir VM 'ye erişim isteme

Tam zamanında VM erişimi özelliği Azure Güvenlik Merkezi API 'SI aracılığıyla kullanılabilir. Yapılandırılan VM 'Ler hakkında bilgi almak, yenilerini eklemek, bir VM 'ye erişim istemek ve daha fazlasını yapmak için bu API 'yi kullanın. 

[JIT ağ erişim ilkeleri](/rest/api/securitycenter/jitnetworkaccesspolicies)hakkında daha fazla bilgi edinin.

---








## <a name="audit-jit-access-activity-in-security-center"></a>Güvenlik Merkezi 'nde JıT erişim etkinliğini denetleme

Günlük aramasını kullanarak VM etkinlikleri hakkında öngörüler elde edebilirsiniz. Günlükleri görüntülemek için:

1. **Tam ZAMANıNDA VM erişimi**' nden **yapılandırılmış** sekmesini seçin.

1. Denetlemek istediğiniz VM için, satırın sonundaki üç nokta menüsünü açın.
 
1. Menüden **etkinlik günlüğü** ' nü seçin.

   ![Tam zamanında JıT etkinlik günlüğü seçin](./media/security-center-just-in-time/jit-select-activity-log.png)

   Etkinlik günlüğü, o VM için zaman, tarih ve abonelikle birlikte önceki işlemlerin filtrelenmiş bir görünümünü sağlar.

1. Günlük bilgilerini indirmek için **CSV olarak indir**' i seçin.








## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, tam zamanında VM erişimi ayarlamayı ve kullanmayı öğrendiniz. JıT 'in kullanılması gerekip gerekmediğini öğrenmek için, karşı savunma yaptığı tehditleri açıklayan kavram makalesini okuyun:

> [!div class="nextstepaction"]
> [JıT açıklanmıştı](just-in-time-explained.md)