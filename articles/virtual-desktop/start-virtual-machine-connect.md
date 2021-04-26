---
title: Sanal makine bağlantısını Başlat-Azure
description: Connect üzerinde sanal makineyi Başlat özelliği nasıl yapılandırılır.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 161a4d578509a7752f9438ce8f05d599bdb54e93
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832293"
---
# <a name="start-virtual-machine-on-connect-preview"></a>Bağlantı sırasında sanal makineyi Başlat (Önizleme)

> [!IMPORTANT]
> Connect üzerinde VM Başlat özelliği şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Connect üzerinde sanal makine (VM) Başlat (Önizleme) özelliği, son kullanıcıların VM 'Leri yalnızca ihtiyaç duydukları zaman açmasına izin vererek maliyetleri kaydetmenizi sağlar. Ardından, gerekli olmadığında VM 'Leri kapatabilirsiniz.

>[!NOTE]
>Windows sanal masaüstü (klasik) bu özelliği desteklemez.

## <a name="requirements-and-limitations"></a>Gereksinimler ve sınırlamalar

Yalnızca kişisel konak havuzları için Connect üzerinde VM 'yi Başlat özelliğini etkinleştirebilirsiniz. Kişisel konak havuzları hakkında daha fazla bilgi için bkz. [Windows sanal masaüstü ortamı](environment-setup.md#host-pools).

Aşağıdaki uzak masaüstü istemcileri, Connect üzerinde başlangıç sanal makinesini destekler:

- [Web istemcisi](connect-web.md)
- [Windows istemcisi (sürüm 1,2748 veya üzeri)](connect-windows-7-10.md)

[Teknik topluluk forumundaki](https://aka.ms/wvdtc)güncelleştirmeler ve istemci desteğiyle ilgili duyuruları kontrol edebilirsiniz.

Azure Kamu bulutu, şu anda Connect üzerinde başlangıç VM 'yi desteklememektedir.

## <a name="create-a-custom-role-for-start-vm-on-connect"></a>Connect üzerinde başlangıç VM 'si için özel bir rol oluştur

Connect üzerinde başlangıç sanal makinesini yapılandırmadan önce, VM 'nizi özel bir RBAC (rol tabanlı erişim denetimi) rolü atamanız gerekir. Bu rol, Windows sanal masaüstü 'Nün aboneliğinizdeki VM 'Leri yönetmesine izin verir. VM 'Leri açmak, durumlarını denetlemek ve tanılama bilgilerini raporlamak için bu rolü de kullanabilirsiniz. Her rolün ne yaptığı hakkında daha fazla bilgi edinmek istiyorsanız, [Azure özel rollerine](../role-based-access-control/custom-roles.md)göz atın.

### <a name="use-the-azure-portal"></a>Azure portalını kullanma

Bağlantı sırasında başlangıç VM 'si için özel bir rol atamak üzere Azure portal kullanmak için:

1. Azure portal açın ve **abonelikler**' e gidin.

2. **Access Control (IAM)** sayfasına gidin ve **özel rol Ekle**' yi seçin.

    > [!div class="mx-imgBorder"]
    > ![Erişim denetimindeki (ıAM) Ekle düğmesinden açılan menünün ekran görüntüsü. "Özel rol Ekle" seçeneği kırmızı renkle vurgulanır.](media/add-custom-role.png)

3. Sonra, özel rolü adlandırın ve bir açıklama ekleyin. "Connect 'te VM 'yi Başlat" olarak adlandırın.

4. **İzinler** sekmesinde, rolü atadığınız aboneliğe aşağıdaki izinleri ekleyin: 
 
   - Microsoft. COMPUTE/virtualMachines/Başlat/eylem
   - Microsoft. COMPUTE/virtualMachines/okuma

5. İşiniz bittiğinde **Tamam**' ı seçin.

Bundan sonra, Windows sanal masaüstüne erişim sağlamak için rolü atamanız gerekir.

Özel rolü atamak için:

1. **Erişim denetimi (IAM) sekmesinde** **rol atamaları Ekle**' yi seçin.

2. Yeni oluşturduğunuz rolü seçin.

3. Arama çubuğuna **Windows sanal masaüstü**' nü girip seçin.

      >[!NOTE]
      >Windows sanal masaüstü (klasik) dağıttıysanız iki uygulama görebilirsiniz. Rolü, gördüğünüz uygulamalara atayın.
      >
      > [!div class="mx-imgBorder"]
      > ![Erişim denetimi (ıAM) sekmesinin ekran görüntüsü. Arama çubuğunda hem Windows sanal masaüstü hem de Windows sanal masaüstü (klasik), kırmızı renkle vurgulanır.](media/add-role-assignment.png)

### <a name="create-a-custom-role-with-a-json-file-template"></a>JSON dosya şablonuyla özel bir rol oluşturma

Özel rolü oluşturmak için bir JSON dosyası kullanıyorsanız, aşağıdaki örnek kullanabileceğiniz temel bir şablonu gösterir. Abonelik KIMLIĞI değerini rolü atamak istediğiniz abonelik KIMLIĞIYLE değiştirdiğinizden emin olun.

```json
{
    "properties": {
        "roleName": "start VM on connect",
        "description": "Friendly description.",
        "assignableScopes": [
            "/subscriptions/<SubscriptionID>"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/virtualMachines/start/action",
                    "Microsoft.Compute/virtualMachines/read"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

## <a name="configure-the-start-vm-on-connect-feature"></a>Connect üzerinde sanal makineyi Başlat özelliğini yapılandırma

Aboneliğinizi aboneliğinize atadıktan sonra, bu rolü bağlama özelliğindeki başlangıç VM 'sini yapılandırma zamanı!

### <a name="deployment-considerations"></a>Dağıtma konuları 

Connect üzerinde VM 'yi Başlat bir konak havuzu ayarıdır. Yalnızca bir grup kullanıcının bu özelliği kullanmasını istiyorsanız, gerekli rolü yalnızca eklemek istediğiniz kullanıcılara atadığınızdan emin olun.

>[!IMPORTANT]
> Bu özelliği yalnızca mevcut konak havuzlarında yapılandırabilirsiniz. Yeni bir konak havuzu oluşturduğunuzda bu özellik kullanılamaz.

### <a name="use-the-azure-portal"></a>Azure portalını kullanma

Connect üzerinde başlangıç sanal makinesini yapılandırmak üzere Azure portal kullanmak için:

1. Tarayıcınızı açın ve [Azure Portal](https://portal.azure.com)gidin.

2. Azure portal **Windows sanal masaüstü**' ne gidin.

3. **Konak havuzları**' nı seçin ve ardından rolün atandığı kişisel masaüstlerini içeren konak havuzunu bulun.

   >[!NOTE]
   > Bu özelliği yapılandırdığınız ana bilgisayar havuzunda doğrudan rol atamaları olan kişisel masaüstleri olmalıdır. Konak havuzundaki masaüstleri doğru yapılandırılmamışsa yapılandırma işlemi çalışmaz.

4. Konak havuzunda **Özellikler**' i seçin. **Connect 'TE VM Başlat** altında **Evet**' i seçin ve sonra ayarı hemen uygulamak için **Kaydet** ' i seçin.

    > [!div class="mx-imgBorder"]
    > ![Özellikler penceresi ekran görüntüsü. Connect 'te VM Başlat seçeneği kırmızı renkle vurgulanır.](media/properties-start-vm-on-connect.png)

### <a name="use-powershell"></a>PowerShell kullanma

Bu ayarı PowerShell ile yapılandırmak için, yapılandırmak istediğiniz kaynak grubu ve konak havuzlarının adlarına sahip olduğunuzdan emin olmanız gerekir. Ayrıca [, Azure PowerShell modülünü yüklemeniz gerekir (sürüm 2.1.0 veya üzeri)](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.1.0).

PowerShell kullanarak Connect 'te başlangıç sanal makinesini yapılandırmak için:

1. Bir PowerShell komut penceresi açın.

2. Bağlanırken VM 'yi Başlat ' i etkinleştirmek için aşağıdaki cmdlet 'i çalıştırın:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$true
    ```

3. Bağlanırken VM 'yi Başlat 'ı devre dışı bırakmak için aşağıdaki cmdlet 'i çalıştırın:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$false
    ```

## <a name="user-experience"></a>Kullanıcı deneyimi

Tipik oturumlarda, bir kullanıcının serbest bırakılmış bir VM 'ye bağlanması için gereken süre artar, çünkü fiziksel bir bilgisayarı açmak çok benzer. Uzak Masaüstü istemcisi, kullanıcının bağlanırken BILGISAYARıN gücünün açık olduğunu bilmesini sağlayan bir göstergeye sahiptir.

## <a name="troubleshooting"></a>Sorun giderme

Özellik herhangi bir sorun ile çalışıyorsa, sorunları denetlemek için Windows sanal masaüstü [Tanılama özelliğini](diagnostics-log-analytics.md) kullanmanızı öneririz. Bir hata iletisi alırsanız, ileti içeriğine yakın bir ilgi ödediğinizden emin olun ve hata adını başvuru için bir yere kopyalayın.

Sorunları gidermeye yönelik öneriler almak için [Windows sanal masaüstü Için Azure izleyici](azure-monitor.md) 'yi de kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Sorun giderme belgelerinin veya tanılama özelliğinin çözemediğiniz herhangi bir sorunla karşılaşırsanız, [Connect 'TE VM 'Yi Başlat SSS bölümüne](start-virtual-machine-connect-faq.md)göz atın.
