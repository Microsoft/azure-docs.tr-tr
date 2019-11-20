---
title: Donanım Microsoft Azure FXT Edge Filer 'ı başlatma
description: Azure FXT Edge Filer düğümlerinde başlangıç parolası ayarlama
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 080aa05af77b996bc0eb71287a3dfef25c24629a
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256023"
---
# <a name="tutorial-set-hardware-passwords"></a>Öğretici: donanım parolalarını ayarlama

Azure FXT Edge Filer düğümünü ilk kez açtığınızda bir kök parolası ayarlamanız gerekir. Donanım düğümleri varsayılan parolayla birlikte verilmez. 

Ağ bağlantı noktaları, parola ayarlanana ve kök kullanıcı oturum açana kadar devre dışı bırakılır.

Düğümü yükledikten ve kabloladıktan sonra, ancak Kümeyi oluşturmaya çalışmadan önce bu adımı izleyin. 

Bu öğretici, donanım düğümüne bağlanmayı ve parolayı ayarlamayı açıklamaktadır. 

Bu öğreticide, aşağıdakileri nasıl yapacağınızı öğreneceksiniz: 

> [!div class="checklist"]
> * Bir klavye ve izleyiciyi düğüme bağlayın ve üzerinde güç yapın
> * Bu düğümdeki Idrac bağlantı noktası ve kök kullanıcı için parolaları ayarla
> * Kök olarak oturum aç 

Bu adımları, kümenizde kullanacağınız her düğüm için tekrarlayın. 

Bu öğreticinin tamamlanabilmesi yaklaşık 15 dakika sürer. 

## <a name="prerequisites"></a>Prerequisites

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

## <a name="set-initial-passwords"></a>Başlangıç parolalarını ayarla 

Azure FXT Edge Filer düğümü, önyükleme sırasında izleyicisine çeşitli iletiler yazdırır. Birkaç dakika sonra, aşağıdaki gibi bir ilk kurulum ekranı gösterir:

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set iDRAC and temporary root password.
Minimum password length is 8.
Enter new password:
```

Girdiğiniz parola iki şey için kullanılır: 

* Bu Azure FXT Edge Filer düğümü için geçici kök paroladır. 

  Bu parola, bu düğümü kullanarak bir küme oluşturduğunuzda veya bu düğümü kümeye eklediğinizde değişecektir. Küme yönetimi parolası (kullanıcıyla ilişkili ``admin``), bir kümedeki tüm düğümler için de kök paroladır.

* Bu, Idrac/ıPMı donanım yönetimi bağlantı noktası için uzun süreli paroladır.

  Donanım sorunuyla ilgili sorunları gidermek için, daha sonra ıPMı ile oturum açmanız gereken parolayı hatırladığınızdan emin olun.

Parolayı girin ve onaylayın: 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

Parolayı girdikten sonra, sistem önyüklemeye devam eder. Tamamlandığında, ``login:`` istemi verir. 

## <a name="sign-in-as-root"></a>Kök olarak oturum aç

Yeni ayarladığınız parolayla ``root`` olarak oturum açın. 

```
login: root
Password:**********
```

Kök olarak oturum açtıktan sonra, ağ bağlantı noktaları etkin olur ve IP adresleri için DHCP sunucusuyla iletişim kuracaktır. 

## <a name="next-steps"></a>Sonraki adımlar

Düğüm, bir kümenin parçası olmaya hazırlanıyor. Azure FXT Edge Filer kümesini oluşturmak için kullanabilirsiniz veya [var olan bir kümeye ekleyebilirsiniz](fxt-add-nodes.md). 

> [!div class="nextstepaction"]
> [Küme oluşturma](fxt-cluster-create.md)
