---
title: 'Öğretici: donanımı başlatma-Azure FXT Edge Filer'
description: Donanım düğümüne bağlanmayı ve Azure FXT Edge Filer düğümlerinde bir başlangıç parolası ayarlamayı öğrenin.
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 79d908077d3bd66a3ed86759e37574383358881b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218859"
---
# <a name="tutorial-set-hardware-passwords"></a>Öğretici: donanım parolalarını ayarlama

Azure FXT Edge Filer düğümünü ilk kez açtığınızda bir kök parolası ayarlamanız gerekir. Donanım düğümleri varsayılan parolayla birlikte verilmez.

Ağ bağlantı noktaları, parola ayarlanana ve kök kullanıcı oturum açana kadar devre dışı bırakılır.

Düğümü yükledikten ve kabloladıktan sonra, ancak Kümeyi oluşturmaya çalışmadan önce bu adımı izleyin.

Bu öğretici, donanım düğümüne bağlanmayı ve parolayı ayarlamayı açıklamaktadır. Ayrıca, düğümün güvenliğini sağlamaya yardımcı olmak için bir BIOS kurulum parolasının nasıl ekleneceğini açıklar.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
>
> * Bir klavye ve izleyiciyi düğüme bağlayın ve üzerinde güç yapın
> * BIOS kurulum parolası ayarlama
> * Bu düğümdeki Idrac bağlantı noktası ve kök kullanıcı için parolaları ayarla
> * Kök olarak oturum aç

Bu adımları, kümenizde kullanacağınız her düğüm için tekrarlayın.

Bu öğreticinin tamamlanabilmesi yaklaşık 15 dakika sürer.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce şu adımları uygulayın:

* Her bir Azure FXT Edge Filer düğümünü bir ekipman rafından [yükleyip](fxt-install.md) [daha önceki öğreticide](fxt-network-power.md)açıklandığı gibi güç kabloları ve ağ erişimi ekleyin.
* Donanım düğümlerine ekleyebileceğiniz, USB bağlantılı bir klavye ve VGA bağlantılı bir izleyici bulun. (Parolayı ayarlamadan önce düğümün seri bağlantı noktası etkin değil.)

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Bir klavye ve izleyiciyi düğüme bağlama

Azure FXT Edge Filer düğümüne fiziksel olarak bir izleyiciyi ve klavyeyi bağlayın.

* İzleyiciyi VGA bağlantı noktasına bağlayın.
* Klavyeyi USB bağlantı noktalarından birine bağlayın.

Kasanın arkasında yer alan bağlantı noktalarını bulmak için bu başvuru diyagramını kullanın.

> [!NOTE]
> Seri bağlantı noktası, parola ayarlanana kadar etkin değildir.

![seri, VGA ve etiketli USB bağlantı noktalarıyla Azure FXT Edge Filer 'ın geri diyagramı](media/fxt-back-serial-vga-usb.png)

Aynı çevre birimlerine birden fazla düğüm bağlamak istiyorsanız KVM anahtarını kullanabilirsiniz.

Ön uçın üzerindeki güç düğmesine basarak düğümü güçlendirin.

![Azure FXT Edge Filer 'ın önünde bulunan bir diyagramı, sağ üst köşedeki etiketlendi](media/fxt-front-annotated.png)

## <a name="create-a-bios-setup-password"></a>BIOS kurulum parolası oluşturma

BIOS kurulumu parolası, düğümün BIOS ayarlarını yanlışlıkla veya yetkisiz değişikliklerden korur. Bu parola, küme oluşturmak için gerekli değildir, ancak küme güvenliği stratejinizin bir parçası olarak önemle önerilir.

Bir BIOS kurulum parolası oluşturmak için:

1. Düğümü açın veya yeniden başlatın ve sistem kurulum yardımcı programını açmak için hemen F2 tuşuna basın.

1. **Sistem kurulumu ana menü** ekranında **Sistem BIOS**  >  **sistem güvenliği**' ni seçin.

1. **Parola durumu** ayarının **kilidinin açık** olduğundan emin olun.

1. Parolayı ayarlamak için **kurulum parolası** alanını kullanın. (Bir tane kullanmak istiyorsanız, bu ekrandan bir sistem BIOS parolası da ayarlayabilirsiniz.)

1. ESC tuşuna basarak **SISTEM BIOS** ekranına dönün ve ardından ESC tuşuna basın. Bir ileti, değişiklikleri kaydetmenizi ister. Sistem otomatik olarak yeniden başlatmazsanız, normal başlangıç ekranına ulaşmak için yeniden başlatın.<!-- how to exit this mode/do you need to reboot to get to the initial setup screen? -->

## <a name="set-initial-passwords"></a>Başlangıç parolalarını ayarla

Azure FXT Edge Filer düğümü, önyükleme sırasında izleyicisine çeşitli iletiler yazdırır. Birkaç dakika sonra, aşağıdaki gibi bir ilk kurulum ekranı gösterir:

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set the iDRAC and temporary root password.
Minimum password length is 8.

NOTE: This does not set a BIOS setup password. For security,
Microsoft recommends using a BIOS setup password, restricting
physical access to the node, and other measures. Learn more at
https://aka.ms/fxt-security.

Enter new password:

```

Girdiğiniz parola iki şey için kullanılır:

* Bu Azure FXT Edge Filer düğümü için geçici kök paroladır.

  Bu parola, bu düğümü kullanarak bir küme oluşturduğunuzda veya bu düğümü kümeye eklediğinizde değişecektir. Küme yönetim parolası (kullanıcıyla ilişkili ``admin`` ), bir kümedeki tüm düğümler için de kök paroladır.

* Bu, Idrac/ıPMı donanım yönetimi bağlantı noktası için uzun süreli paroladır.

  Donanım sorunuyla ilgili sorunları gidermek için, daha sonra ıPMı ile oturum açmanız gereken parolayı hatırladığınızdan emin olun.

Parolayı girin ve onaylayın:

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

Parolayı girdikten sonra, sistem önyüklemeye devam eder. Tamamlandığında, bir ``login:`` istem sağlar.

## <a name="sign-in-as-root"></a>Kök olarak oturum aç

``root``Yeni ayarladığınız parolayla oturum açın.

```
login: root
Password:**********
```

Kök olarak oturum açtıktan sonra, ağ bağlantı noktaları etkin olur ve IP adresleri için DHCP sunucusuyla iletişim kuracaktır.

## <a name="next-steps"></a>Sonraki adımlar

Düğüm, bir kümenin parçası olmaya hazırlanıyor. Azure FXT Edge Filer kümesini oluşturmak için kullanabilirsiniz veya [var olan bir kümeye ekleyebilirsiniz](fxt-add-nodes.md).

> [!div class="nextstepaction"]
> [Küme oluşturma](fxt-cluster-create.md)
