---
title: Azure için cihaz güncelleştirmesinde bir cihaz güncelleştirme hesabı oluşturun IoT Hub | Microsoft Docs
description: Azure IoT Hub cihaz güncelleştirmesinde bir cihaz güncelleştirme hesabı oluşturun.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: d3f7f4e1cdd56675d6084448abc810c9a41992f9
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520148"
---
# <a name="device-update-for-iot-hub-resource-management"></a>IoT Hub Kaynak Yönetimi için Cihaz Güncelleştirmesi

Cihaz güncelleştirmesini kullanmaya başlamak için bir cihaz güncelleştirme hesabı oluşturmanız, örnek ve erişim denetimi rolleri ayarlamanız gerekir. 

## <a name="prerequisites"></a>Önkoşullar

* IoT Hub erişim. Bir S1 (Standart) katmanını veya üstünü kullanmanız önerilir. 
* Desteklenen tarayıcılar:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="create-a-device-update-account"></a>Cihaz güncelleştirme hesabı oluştur

1. [Azure Portal](https://portal.azure.com) git

2. Kaynak oluştur ' a tıklayın ve "IoT Hub için cihaz güncelleştirmesi" araması yapın

   :::image type="content" source="media/create-device-update-account/device-update-marketplace.png" alt-text="IoT Hub kaynağı için cihaz güncelleştirme ekran görüntüsü." lightbox="media/create-device-update-account/device-update-marketplace.png":::

3. IoT Hub için Oluştur-> cihaz Güncelleştirmesi ' ne tıklayın

4. Cihaz güncelleştirme hesabınız ve kaynak grubuyla ilişkilendirilecek Azure aboneliğini belirtin

5. Cihaz güncelleştirme hesabınız için bir ad ve konum belirtin

   :::image type="content" source="media/create-device-update-account/account-details.png" alt-text="Hesap ayrıntılarının ekran görüntüsü." lightbox="media/create-device-update-account/account-details.png":::

 > [!NOTE]
 > IoT Hub cihaz güncelleştirmesinin kullanılabildiği bölgeleri öğrenmek için [Azure Products-Region sayfasına](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) gidebilirsiniz. Bölgenizde cihaz güncelleştirmesi IoT Hub kullanılamıyorsa, size en yakın kullanılabilir bir bölgede bir hesap oluşturmayı tercih edebilirsiniz. 

6. "Ileri: gözden geçir +> oluştur" düğmesine tıklayın

   :::image type="content" source="media/create-device-update-account/account-review.png" alt-text="Hesap ayrıntıları incelemesinin ekran görüntüsü." lightbox="media/create-device-update-account/account-review.png":::

7. Ayrıntıları gözden geçirin ve "Oluştur" u seçin. Dağıtımınızın devam ettiğini görürsünüz. 

   :::image type="content" source="media/create-device-update-account/account-deployment-inprogress.png" alt-text="Hesap dağıtımının bir ekran görüntüsü devam ediyor." lightbox="media/create-device-update-account/account-deployment-inprogress.png":::

8. Dağıtım durumunun birkaç dakika içinde "Tamam" olarak değiştirilmesini görürsünüz. "Kaynağa git" e tıklayın

   :::image type="content" source="media/create-device-update-account/account-complete.png" alt-text="Hesap dağıtımının ekran görüntüsü tamamlanmıştır." lightbox="media/create-device-update-account/account-complete.png":::

## <a name="create-a-device-update-instance"></a>Cihaz güncelleştirme örneği oluşturma 

Bir cihaz güncelleştirmesi örneği tek bir IoT Hub ile ilişkilendirilir. Cihaz güncelleştirmesiyle kullanılacak IoT Hub 'ını seçin. Bu adım sırasında, cihaz güncelleştirmesinin yalnızca IoT Hub (kayıt defteri yazma ve hizmet Connect) ile çalışmak için gerekli izinleri kullandığından emin olmak için yeni bir paylaşılan erişim ilkesi oluşturacağız. Bu ilke, erişimin yalnızca cihaz güncelleştirmesiyle sınırlı olmasını sağlar.

Bir hesap oluşturulduktan sonra bir cihaz güncelleştirme örneği oluşturmak için.

1. Yeni oluşturduğunuz hesap kaynağınız olduktan sonra örnek yönetimi "örnekler" dikey penceresine gidin

   :::image type="content" source="media/create-device-update-account/instance-blade.png" alt-text="Hesap içindeki örnek yönetiminin ekran görüntüsü." lightbox="media/create-device-update-account/instance-blade.png":::

2. "Oluştur ve bir örnek adı belirtin ve IoT Hub seçin

   :::image type="content" source="media/create-device-update-account/instance-details.png" alt-text="Örnek ayrıntılarının ekran görüntüsü." lightbox="media/create-device-update-account/instance-details.png":::

   > [!NOTE] 
   > Cihaz güncelleştirme kaynağına bağlanan IoT Hub, cihaz güncelleştirme hesabınızla aynı bölgede olması gerekmez. Ancak, daha iyi performans için IoT Hub cihaz güncelleştirme hesabınızın bölgesiyle aynı bölgede olması veya bu bölgeye yakın olması önerilir. 

3. "Oluştur"a tıklayın. Örneği "oluşturma" durumunda görürsünüz. 

   :::image type="content" source="media/create-device-update-account/instance-creating.png" alt-text="Örnek oluşturma ekranının ekran görüntüsü." lightbox="media/create-device-update-account/instance-creating.png":::

4. Örnek dağıtımının tamamlanabilmesi için 5-10 dakikaya izin verin. "Sağlama durumu" nu "başarılı" olarak görene kadar durumu yenileyin.

   :::image type="content" source="media/create-device-update-account/instance-succeeded.png" alt-text="Örnek oluşturma 'nın ekran görüntüsü başarılı oldu." lightbox="media/create-device-update-account/instance-succeeded.png":::

## <a name="configure-iot-hub"></a>IoT Hub Yapılandır 

Cihaz güncelleştirmesinin IoT Hub değişiklik bildirimlerini alabilmesi için, cihaz güncelleştirme "yerleşik" Olay Hub 'ı ile tümleştirilir. "IoT Hub Yapılandır" düğmesine tıklamak, IoT cihazlarıyla iletişim kurmak için gerekli ileti yollarını ve erişim ilkesini yapılandırır. 

IoT Hub yapılandırmak için

1. "Sağlama durumu" örneği "başarılı" olarak açıldıktan sonra örnek yönetimi dikey penceresinde örneği seçin. "IoT Hub Yapılandır" a tıklayın

   :::image type="content" source="media/create-device-update-account/instance-configure.png" alt-text="Bir örnek için IoT Hub yapılandırma ekran görüntüsü." lightbox="media/create-device-update-account/instance-configure.png":::

2. "Bu değişiklikleri yapmak için kabul ediyorum" seçeneğini belirleyin

   :::image type="content" source="media/create-device-update-account/instance-configure-selected.png" alt-text="Bir örnek için IoT Hub yapılandırmak üzere kabul etmiş ekran görüntüsü." lightbox="media/create-device-update-account/instance-configure-selected.png":::

3. "Güncelleştir" e tıklayın

   > [!NOTE] 
   > Ücretsiz bir Azure IoT Hub katmanı kullanıyorsanız, izin verilen ileti yolları sayısı 5 ile sınırlıdır. IoT Hub için cihaz güncelleştirmesinin, beklenen şekilde çalışması için 4 ileti yollarının yapılandırılması gerekir. 

[Yapılandırılan ileti yolları hakkında bilgi edinin.](device-update-resources.md) 


## <a name="configure-access-control-roles"></a>Erişim denetimi rollerini yapılandırma

Diğer kullanıcıların cihaz güncelleştirmesine erişimi olması için kullanıcılara bu kaynağa erişim verilmesi gerekir. 

1. Cihaz güncelleştirme hesabı içindeki erişim denetimi 'ne (ıAM) git

   :::image type="content" source="media/create-device-update-account/account-access-control.png" alt-text="Cihaz güncelleştirme hesabı içindeki erişim denetiminin ekran görüntüsü." lightbox="media/create-device-update-account/account-access-control.png":::

2. "Rol atamaları Ekle" ye tıklayın

3. "Rol Seç" bölümünde verilen seçeneklerden bir cihaz güncelleştirme rolü seçin
     - Cihaz Güncelleştirme Yöneticisi
     - Cihaz güncelleştirme okuyucusu
     - Cihaz güncelleştirme Içerik Yöneticisi
     - Cihaz güncelleştirme Içeriği okuyucusu
     - Cihaz güncelleştirme dağıtımları Yöneticisi
     - Cihaz güncelleştirme dağıtımları okuyucu
     
   :::image type="content" source="media/create-device-update-account/role-assignment.png" alt-text="Cihaz güncelleştirme hesabı içindeki erişim denetimi rol atamalarının ekran görüntüsü." lightbox="media/create-device-update-account/role-assignment.png":::
    
    [IoT Hub için cihaz güncelleştirmesinde rol tabanlı erişim denetimi hakkında bilgi edinin](device-update-control-access.md) 
    
4. Bir kullanıcıya veya Azure AD grubuna erişim atama
5. Kaydet’e tıklayın.
6. Artık IoT Hub içinden cihaz güncelleştirme deneyimini kullanmaya hazırsınız

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki hızlı öğreticilerden birini kullanarak bir cihazı güncelleştirmeyi deneyin:

 - [Simülatör üzerinde cihaz güncelleştirme](device-update-simulator.md)
 - [Raspberry PI üzerinde cihaz güncelleştirmesi](device-update-raspberry-pi.md)
 - [Ubuntu Server 18,04 x64 paket aracısında cihaz güncelleştirmesi](device-update-ubuntu-agent.md)

[Cihaz güncelleştirme hesabı ve örneği hakkında bilgi edinin.](device-update-resources.md) 

[Cihaz güncelleştirme erişim denetimi rolleri hakkında bilgi edinin. ](device-update-control-access.md) 

