---
title: Yeni oturum Konakları ile mevcut konak havuzunu genişletme-Azure
description: Mevcut bir konak havuzunu Windows sanal masaüstündeki yeni oturum Konakları ile genişletme.
author: Heidilohr
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9c73d7434a002a5efc7d058095eb9743a7f3ebf8
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446834"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>Yeni oturum Konakları ile mevcut bir konak havuzunu Genişlet

>[!IMPORTANT]
>Bu içerik Azure Resource Manager Windows sanal masaüstü nesneleri ile Windows sanal masaüstü için geçerlidir. Azure Resource Manager nesneleri olmadan Windows sanal masaüstü (klasik) kullanıyorsanız, [Bu makaleye](./virtual-desktop-fall-2019/expand-existing-host-pool-2019.md)bakın.

Konak havuzunuzdaki kullanımı artırın, yeni yükü işlemek için mevcut konak havuzunuzu yeni oturum konaklarıyla genişletmeniz gerekebilir.

Bu makalede, mevcut bir konak havuzunu yeni oturum Konakları ile nasıl genişletebileceğiniz açıklanır.

## <a name="what-you-need-to-expand-the-host-pool"></a>Konak havuzunu genişletmeniz için gerekenler

Başlamadan önce, aşağıdaki yöntemlerden birini kullanarak bir konak havuzu ve oturum ana bilgisayarı sanal makineleri (VM 'Ler) oluşturduğunuzdan emin olun:

- [Azure portalı](./create-host-pools-azure-marketplace.md)
- [PowerShell ile ana bilgisayar havuzu oluşturma](./create-host-pools-powershell.md)

Konak havuzunu ve oturum ana bilgisayar VM 'lerini ilk oluşturduğunuzda aşağıdaki bilgilere de ihtiyacınız olacaktır:

- VM boyutu, resim ve ad ön eki
- Etki alanına ekleme yönetici kimlik bilgileri
- Sanal ağ adı ve alt ağ adı

## <a name="add-virtual-machines-with-the-azure-portal"></a>Azure portal ile sanal makineler ekleme

Sanal makineler ekleyerek konak havuzunuzu genişletmek için:

1. Azure portalında oturum açın.

2. **Windows sanal masaüstü** araması yapın ve seçin.

3. Ekranın sol tarafındaki menüde **konak havuzları**' nı seçin ve ardından sanal makineleri eklemek istediğiniz konak havuzunun adını seçin.

4. Ekranın sol tarafındaki menüden **oturum Konakları** ' nı seçin.

5. Ana bilgisayar havuzunuzu oluşturmaya başlamak için **+ Ekle** ' yi seçin.

6. Temel bilgiler sekmesini yoksayın ve bunun yerine **VM ayrıntıları** sekmesini seçin. Burada, konak havuzuna eklemek istediğiniz sanal makinenin (VM) ayrıntılarını görüntüleyebilir ve düzenleyebilirsiniz.

7. VM 'Leri oluşturmak istediğiniz kaynak grubunu seçin, ardından bölgeyi seçin. Kullanmakta olduğunuz geçerli bölgeyi veya yeni bir bölgeyi seçebilirsiniz.

8. Konak havuzunuza eklemek istediğiniz oturum ana bilgisayarlarının sayısını **VM sayısına** girin. Örneğin, ana bilgisayar havuzunuzu beş ana bilgisayar ile genişlettiğiniz takdirde **5** girin.

    >[!NOTE]
    >VM 'lerin görüntüsünü ve önekini düzenlemek mümkün olsa da, aynı konak havuzunda farklı görüntülere sahip VM 'Ler varsa bunları düzenlemenizi öneririz. Yalnızca etkilenen konak havuzundaki eski görüntülerle VM 'Leri kaldırmayı planlıyorsanız görüntüyü ve ön eki düzenleyin.

9. Sanal **ağ bilgileri** için, sanal makinelerin katılmasını istediğiniz sanal ağı ve alt ağı seçin. Mevcut makinelerinizdeki aynı sanal ağı seçebilir veya adım 7 ' de seçtiğiniz bölgeye daha uygun olan farklı bir tane seçebilirsiniz.

10. **Yönetici hesabı** için, seçtiğiniz sanal ağla ilişkili Active Directory etki alanı Kullanıcı adı ve parolasını girin. Bu kimlik bilgileri, sanal makineleri sanal ağa katmak için kullanılacaktır.

      >[!NOTE]
      >Yönetici adlarınızın burada verilen bilgiyle uyumlu olduğundan emin olun. Hesapta etkin bir MFA yok.

11. Sanal makineleri gruplandırmak istediğiniz etiketlere sahipseniz **etiket** sekmesini seçin. Aksi takdirde, bu sekmeyi atlayın.

12. **Gözden geçir + oluştur** sekmesini seçin. Seçimlerinizi gözden geçirin ve her şey iyi görünüyorsa **Oluştur**' u seçin.

## <a name="next-steps"></a>Sonraki adımlar

Mevcut konak havuzunuzu genişlettiyseniz, bir kullanıcı oturumunun parçası olarak test etmek için bir Windows sanal masaüstü istemcisinde oturum açabilirsiniz. Aşağıdaki istemcilerden herhangi biriyle bir oturuma bağlanabilirsiniz:

- [Windows Masaüstü istemcisine bağlanma](./connect-windows-7-10.md)
- [Web istemcisiyle bağlanma](./connect-web.md)
- [Android istemcisiyle bağlanma](./connect-android.md)
- [macOS istemcisiyle bağlanma](./connect-macos.md)
- [iOS istemcisiyle bağlanma](./connect-ios.md)
