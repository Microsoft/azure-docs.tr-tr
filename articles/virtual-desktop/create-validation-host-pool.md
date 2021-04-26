---
title: Windows sanal masaüstü konak havuzu hizmet güncelleştirmeleri-Azure
description: Üretime yönelik güncelleştirmeleri kullanıma almadan önce hizmet güncelleştirmelerini izlemek için bir doğrulama ana bilgisayarı havuzu oluşturma.
author: Heidilohr
ms.topic: tutorial
ms.date: 12/15/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 66279c2ea798a7edb21795f368011b00bdf6297e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447820"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Öğretici: hizmet güncelleştirmelerini doğrulamak için bir konak havuzu oluşturma

>[!IMPORTANT]
>Bu içerik Azure Resource Manager Windows sanal masaüstü nesneleri ile Windows sanal masaüstü için geçerlidir. Azure Resource Manager nesneleri olmadan Windows sanal masaüstü (klasik) kullanıyorsanız, [Bu makaleye](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md)bakın.

Konak havuzları, Windows Sanal Masaüstü ortamındaki özdeş bir veya daha fazla sanal makine koleksiyonudur. Önce hizmet güncelleştirmelerinin uygulandığı bir doğrulama ana bilgisayar havuzu oluşturmanızı önemle öneririz. Bu, hizmet güncelleştirmelerini standart ya da doğrulama olmayan ortamınıza uygulamadan önce izlemenize olanak sağlar. Doğrulama ana bilgisayar havuzu olmadan hata veren değişiklikleri bulamamanıza neden olabilir ve bu da standart ortamınızdaki kullanıcılar için kapalı kalma süresine yol açabilir.

Uygulamalarınızın en son güncelleştirmelerle çalışmasını sağlamak için doğrulama ana bilgisayar havuzunun, doğrulama olmayan ortamınızdaki konak havuzlarıyla mümkün olduğunca benzer olması gerekir. Kullanıcılar, Standart ana bilgisayar havuzunda olduğu gibi doğrulama ana bilgisayar havuzuna sık olarak bağlanmalıdır. Konak havuzunuzdaki otomatik test testiniz varsa, doğrulama ana bilgisayar havuzuna otomatik test eklemeniz gerekir.

[Tanılama özelliğiyle](diagnostics-role-service.md) veya [Windows sanal masaüstü sorunlarını giderme makaleleriyle](troubleshoot-set-up-overview.md)doğrulama konak havuzundaki sorunları ayıklayabilirsiniz.

>[!NOTE]
> Sonraki tüm güncelleştirmeleri test etmek için doğrulama ana bilgisayar havuzunu yerinde bırakmanız önerilir.

>[!IMPORTANT]
>Azure Kaynak Yönetimi tümleştirmesiyle Windows sanal masaüstü Şu anda doğrulama ortamlarını etkinleştirme ve devre dışı bırakma konusunda sorun yaşıyor. Sorunu çöztiğimiz zaman bu makaleyi güncelleştireceğiz.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, PowerShell modülünüzü ayarlamak ve Azure 'da oturum açmak için [Windows sanal masaüstü PowerShell modülünü ayarlama](powershell-module.md) bölümündeki yönergeleri izleyin.

## <a name="create-your-host-pool"></a>Konak havuzunuzu oluşturma

Aşağıdaki makalelerdeki yönergeleri izleyerek bir konak havuzu oluşturabilirsiniz:
- [Öğretici: Azure Marketi ile bir konak havuzu oluşturma](create-host-pools-azure-marketplace.md)
- [PowerShell ile ana bilgisayar havuzu oluşturma](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Konak havuzunuzu doğrulama ana bilgisayar havuzu olarak tanımlama

Yeni konak havuzunu doğrulama konak havuzu olarak tanımlamak için aşağıdaki PowerShell cmdlet 'lerini çalıştırın. Köşeli ayraçlar içindeki değerleri oturumunuzla ilgili değerlerle değiştirin:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -ValidationEnvironment:$true
```

Doğrulama özelliğinin ayarlandığını onaylamak için aşağıdaki PowerShell cmdlet 'ini çalıştırın. Köşeli ayraçlar içindeki değerleri oturumunuzla ilgili değerlerle değiştirin.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | Format-List
```

Cmdlet 'inin sonuçları şu çıkışa benzer görünmelidir:

```powershell
    HostPoolName        : hostpoolname
    FriendlyName        :
    Description         :
    Persistent          : False
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnvironment : True
```

## <a name="enable-your-validation-environment-with-the-azure-portal"></a>Azure portal ile doğrulama ortamınızı etkinleştirin

Doğrulama ortamınızı etkinleştirmek için Azure portal de kullanabilirsiniz.

Doğrulama ana bilgisayar havuzunuzu yapılandırmak için Azure portal kullanmak için:

1. <https://portal.azure.com> adresinden Azure portalında oturum açın.
2. **Windows sanal masaüstü** araması yapın ve seçin.
3. Windows sanal masaüstü sayfasında **konak havuzları**' nı seçin.
4. Düzenlemek istediğiniz konak havuzunun adını seçin.
5. **Özellikler**’i seçin.
6. Doğrulama ortamı alanında, doğrulama ortamını etkinleştirmek için **Evet** ' i seçin.
7. **Kaydet**’i seçin. Bu, yeni ayarları uygular.

## <a name="update-schedule"></a>Zamanlamayı Güncelleştir

Hizmet güncelleştirmeleri ayda bir gerçekleşir. Önemli sorunlar varsa, kritik güncelleştirmeler daha sık bir hızda sağlanacaktır.

Herhangi bir hizmet güncelleştirmesi varsa, ortamı doğrulamak için her gün en az küçük bir kullanıcı grubuna kaydolduğunuzdan emin olun. Hizmet güncelleştirmeleri hakkında bilgi sahibi olmak için [Techcommunity sitemizi](https://techcommunity.microsoft.com/t5/forums/searchpage/tab/message?filter=location&q=wvdupdate&location=forum-board:WindowsVirtualDesktop&sort_by=-topicPostDate&collapse_discussion=true) düzenli olarak ziyaret etmenizi ve WVDUPdate ile tüm gönderileri izlemenizi öneririz.

## <a name="next-steps"></a>Sonraki adımlar

Bir doğrulama ana bilgisayar havuzu oluşturduğunuza göre, Windows sanal masaüstü dağıtımınızı izlemek için Azure hizmet durumunu nasıl kullanacağınızı öğrenebilirsiniz.

> [!div class="nextstepaction"]
> [Hizmet uyarılarını ayarlama](./set-up-service-alerts.md)
