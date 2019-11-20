---
title: Azure için avere vFXT desteği alma
description: Azure için avere vFXT hakkında Destek biletleri açma açıklaması
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 068747fd51779bd8a20636355a9ce2b032f6151d
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255367"
---
# <a name="get-help-with-your-system"></a>Sisteminizle ilgili yardım alın

Azure için avere vFXT 'niz ile ilgili yardıma ihtiyacınız varsa, destek almak için çeşitli yollar şunlardır:

* **Avere vfxt sorunu** - [aşağıda](#open-a-support-ticket-for-your-avere-vfxt)açıklanan şekilde avere vfxt 'niz için bir destek bileti açmak üzere Azure Portal kullanın.
* **Kota** -kotayla ilgili bir sorununuz varsa, [Kota artışı isteyin](#request-a-quota-increase)
* **Belgeler ve örnekler** -bu belge veya örneklerle ilgili sorunları buldıysanız, sorun ile sayfanın en altına gidin ve var olan sorunları aramak ve gerekirse yeni bir dosya eklemek Için **geri bildirim** bölümünü kullanın.  

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Avere vFXT 'niz için bir destek bileti açın

Avere vFXT dağıtma veya kullanma sırasında sorunlarla karşılaşırsanız Azure portal aracılığıyla yardım isteyin.  

Destek biletinin kümenizdeki bir kaynakla etiketlendiğinden emin olmak için aşağıdaki adımları izleyin. Bilet etiketleme, doğru destek kaynağına yönlendirmemize yardımcı olur. 

1. [@No__t-1](https://portal.azure.com)' den **kaynak grupları**' nı seçin.

   !["kaynak grupları" daire içinde Azure portal sol menünün ekran görüntüsü](media/avere-vfxt-ticket-rg.png)

1. Sorunun gerçekleştiği vFXT kümesini içeren kaynak grubuna gidin ve avere sanal makinelerinden birine tıklayın.

    ![belirli bir VM 'ye sahip Azure portal kaynak grubu "genel bakış" panelinin ekran görüntüsü](media/avere-vfxt-ticket-vm.png)

1. VM sayfasında, sol bölmenin alt kısmına gidin ve **Yeni destek isteği**' ne tıklayın.

    ![Önceki ekran görüntüsünden VM için Azure portal VM sayfasının ekran görüntüsü. Sol menü, en alta kaydırıldı ve "yeni destek isteği" bir daire içinde.](media/avere-vfxt-ticket-request.png)

1. Destek isteğinden biri sayfasında, **tüm hizmetler** ' e tıklayın ve **depolama alanının** altına bakarak **avere vfxt**' yi seçin.

    !["temel bilgiler" üst bilgisi ve "hizmet" öğesinin etrafında bir daire olan Azure portal yeni destek isteği ekranının ekran görüntüsü. "Tüm hizmetler" düğmesi seçilidir ve açılan menü alanında "avere vFXT" değeri bulunur](media/avere-vfxt-ticket-service.png)

1. İki sayfa üzerinde, sorun türünü ve sorununuzla en yakından eşleşen kategoriyi seçin. Sorunun oluştuğu saati içeren kısa bir başlık ve açıklama ekleyin. 

   ![Yeni destek isteği ekranının, tamamlanması gereken çok sayıda alan içeren "sorun" üst bilgisi ile ekran görüntüsü](media/avere-vfxt-ticket-problem.png)

1. Üçüncü Sayfa sayfasında, iletişim bilgilerinizi girin ve **Oluştur**' a tıklayın. E-posta adresinize bir onaylama ve bilet numarası gönderilir ve bir destek personeli üyesi sizinle iletişim kuracaktır.

## <a name="request-a-quota-increase"></a>Kota artışı iste

Azure için avere vFXT 'yi dağıtmak için gereken bileşenleri öğrenmek üzere [vfxt kümesine yönelik kotayı](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) okuyun. Azure portal [bir kota artışı isteyebilirsiniz](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) .