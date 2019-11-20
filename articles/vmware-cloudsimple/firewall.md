---
title: CloudSimple tarafından Azure VMware çözümü-güvenlik duvarı tablolarını ve kurallarını ayarlama
description: Alt ağlardaki ve VLAN 'larda trafiği kısıtlamak için özel bulut güvenlik duvarı tablolarının ve kurallarının nasıl ayarlanacağını açıklar.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 411b4bb74c21a445f4001c949e1c7811af212453
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606450"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>Özel bulutlar için güvenlik duvarı tabloları ve kuralları ayarlama

Güvenlik Duvarı tabloları ve ilişkili kurallar, belirli alt ağlara ve VLAN 'Lara uygulanacak trafikte kısıtlamalar belirtmenize olanak tanır.

* Bir alt ağ, bir güvenlik duvarı tablosuyla ilişkilendirilebilir.
* Bir güvenlik duvarı tablosu, birden çok alt ağ ile ilişkilendirilebilir.

## <a name="add-a-new-firewall-table"></a>Yeni bir güvenlik duvarı tablosu ekleme

1. [CloudSimple portalına erişin](access-cloudsimple-portal.md) ve yan menüdeki **ağ** ' ı seçin.
2. **Güvenlik duvarı tabloları**' nı seçin.
3. **Güvenlik duvarı tablosu oluştur**' u seçin.

    ![VLAN/alt ağ sayfası](media/firewall-tables-page.png)

4. Tablo için bir ad girin.
5. Tablo için varsayılan bir kural listelenir. **Yeni kural** oluştur ' a tıklayarak ek bir kural oluşturun. Ayrıntılar için aşağıdaki yordama bakın.
6. Güvenlik Duvarı tablosunu kaydetmek için **bitti** ' ye tıklayın.

> [!IMPORTANT]
> Özel bulut başına en fazla iki güvenlik duvarı tablosu oluşturabilirsiniz.

## <a name="firewall-rules"></a>Güvenlik duvarı kuralları

Güvenlik duvarı kuralları, güvenlik duvarının belirli trafik türlerini nasıl ele aldığını tespit eder. Seçili bir güvenlik duvarı tablosu için **kurallar** sekmesi, ilişkili tüm kuralları listeler.

![Güvenlik duvarı kuralları tablosu](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>Güvenlik duvarı kuralı oluşturma

1. Aşağıdaki yollarla bir güvenlik duvarı kuralı oluşturmak için ayarları görüntüleyin:
    * Bir güvenlik duvarı tablosu oluştururken **Kural Ekle** ' ye tıklayın.
    * **Ağ > güvenlik duvarı tabloları** sayfasında belirli bir güvenlik duvarı tablosu seçin ve **yeni güvenlik duvarı kuralı oluştur**' a tıklayın.
2. Kuralı aşağıdaki şekilde ayarlayın:
    * **Ad**. Kurala bir ad verin.
    * **Öncelik**. Kurala bir öncelik atayın. Daha düşük sayı olan kurallar önce yürütülür.
    * **Trafik türü**. Kuralın özel bulut, Internet veya VPN trafiği (durum bilgisiz) veya genel IP adresi (durum bilgisi) için olup olmadığını seçin.
    * **Protokol**. Kuralın kapsadığı Protokolü (TCP, UDP veya herhangi bir protokol) seçin.
    * **Yön**. Kuralın gelen veya giden trafik için olup olmadığını seçin. Gelen ve giden trafik için ayrı kurallar tanımlamanız gerekir.
    * **Eylem**. Kural eşleşiyorsa gerçekleştirilecek eylemi seçin (izin ver veya Reddet).
    * **Kaynak**. Kural (CıDR bloğu, iç veya herhangi bir kaynak) tarafından kapsanan kaynakları belirtin.
    * **Kaynak bağlantı noktası aralığı**. Kurala tabi olan bağlantı noktası aralığını belirtin.
    * **Yön**. Gelen veya giden ' ı seçin.
    * **Hedef**. Kural (CıDR bloğu, iç veya herhangi bir kaynak) tarafından kapsanan hedefleri belirtin.
    * **Kaynak bağlantı noktası aralığı**. Kurala tabi olan bağlantı noktası aralığını belirtin.

    ![Güvenlik Duvarı tablosu kural ekle](media/firewall-rule-create.png)

3. Kuralı kaydetmek ve güvenlik duvarı tablosu için kurallar listesine eklemek için **bitti** ' ye tıklayın.

> [!IMPORTANT]
> Her güvenlik duvarı tablosu en fazla 10 gelen kurala ve 20 giden kurala sahip olabilir. Bu sınırlar, [Destek ile iletişim](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)kurarak artırılabilir.

## <a name="attach-vlanssubnets"></a>VLAN 'Ları/alt ağları Ekle

Bir güvenlik duvarı tablosu tanımladıktan sonra, tablodaki kurallara tabi olan alt ağları belirtebilirsiniz.

1. **Ağ** > **güvenlik duvarı tabloları** sayfasında, bir güvenlik duvarı tablosu seçin.
2. **Bağlı VLAN/alt ağ** sekmesini açın.
3. **VLAN/subnet 'e Ekle**' ye tıklayın.
4. Özel bulutu ve VLAN 'ı seçin. İlişkili alt ağ adı ve CıDR bloğu gösterilmektedir.
5. **Gönder**' e tıklayın.
