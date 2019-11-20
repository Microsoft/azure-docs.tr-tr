---
title: Azure IoT Central uygulamasındaki kullanıcıları ve rolleri yönetme | Microsoft Docs
description: Yönetici olarak, Azure IoT Central uygulamanızda kullanıcıları ve rolleri yönetme
author: lmasieri
ms.author: lmasieri
ms.date: 10/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 603bc4ad39a472f2ebc786257bc7c625e3f6b9a4
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73895754"
---
# <a name="manage-users-and-roles-in-your-iot-central-application-preview-features"></a>IoT Central uygulamanızda kullanıcıları ve rolleri yönetme (Önizleme özellikleri)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Bu makalede, yönetici olarak Azure IoT Central uygulamanızda Kullanıcı ekleme, düzenleme ve silme işlemlerinin nasıl yapılacağı açıklanır. Makalede ayrıca Azure IoT Central uygulamanızda rollerin nasıl yönetileceği açıklanır.

**Yönetim** bölümüne erişmek ve bunları kullanmak için, bir Azure IoT Central uygulaması için **yönetici** rolünde olmanız gerekir. Bir Azure IoT Central uygulaması oluşturursanız, bu uygulamanın **yönetici** rolüne otomatik olarak eklendiniz.

## <a name="add-users"></a>Kullanıcı ekle

Azure IoT Central uygulamasına oturum açabilmeniz ve bu uygulamaya erişebilmek için her kullanıcının bir kullanıcı hesabı olması gerekir. Microsoft hesapları (MSAs) ve Azure Active Directory (Azure AD) hesapları Azure IoT Central desteklenir. Azure Active Directory gruplar Şu anda Azure IoT Central 'da desteklenmemektedir.

Daha fazla bilgi için bkz. [Microsoft hesabı yardım](https://support.microsoft.com/products/microsoft-account?category=manage-account) ve [hızlı başlangıç: Azure Active Directory yeni kullanıcı ekleme](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Bir IoT Central uygulamasına kullanıcı eklemek için **Yönetim** bölümündeki **Kullanıcılar** sayfasına gidin.
    
    > [!div class="mx-imgBorder"]
    >![Kullanıcıları yönetme](media/howto-manage-users-roles/manage-users-pnp.png)

1. Kullanıcı eklemek için, **Kullanıcılar** sayfasında **+ Kullanıcı Ekle**' yi seçin.

1. **Rol** açılan menüsünden Kullanıcı için bir rol seçin. Roller hakkında daha fazla bilgi için bu makalenin [rolleri Yönet](#manage-roles) bölümünden daha fazla bilgi edinebilirsiniz.

    > [!div class="mx-imgBorder"]
    >![Kullanıcı ekleyin ve bir rol seçin](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > Özel bir rolde başka Kullanıcı ekleme izni veren bir Kullanıcı, yalnızca kendi rolünden aynı veya daha az izne sahip bir role Kullanıcı ekleyebilirler.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Kullanıcılara atanan rolleri düzenleme

Roller atandıktan sonra değiştirilemez. Bir kullanıcıya atanmış rolü değiştirmek için kullanıcıyı silin ve ardından kullanıcıyı farklı bir rolle yeniden ekleyin.

> [!NOTE]
> Atanan roller IoT Central uygulamaya özeldir ve Azure portalından yönetilemez.

## <a name="delete-users"></a>Kullanıcıları Sil

Kullanıcıları silmek için **Kullanıcılar** sayfasında bir veya daha fazla onay kutusunu işaretleyin. Ardından **Sil**’i seçin.

## <a name="manage-roles"></a>Rolleri yönetme

Roller, kuruluşunuzda kimlerin IoT Central çeşitli görevleri yapmasına izin verileceğini denetlemenize olanak tanır. Uygulamanızın kullanıcılarına atayabileceğiniz, üç yerleşik rol vardır. Daha ayrıntılı denetime ihtiyacınız varsa [özel roller de oluşturabilirsiniz](#create-a-custom-role) .

> [!div class="mx-imgBorder"]
> rol seçimini yönetmek ![](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Yönetici

**Yönetici** rolündeki kullanıcılar, faturalama dahil olmak üzere uygulamanın her bölümünü yönetebilir ve denetleyebilir.

Bir uygulamayı oluşturan kullanıcı otomatik olarak **yönetici** rolüne atanır. **Yönetici** rolünde her zaman en az bir kullanıcı olmalıdır.

### <a name="builder"></a>Oluşturucu

**Oluşturucu** rolündeki kullanıcılar uygulamanın her bölümünü yönetebilir, ancak yönetim veya sürekli veri dışa aktarma sekmelerinde değişiklik yapamaz.

### <a name="operator"></a>İşleç

**Operatör** rolündeki kullanıcılar, cihaz sistem durumunu ve durumunu izleyebilir. Cihaz şablonlarında değişiklik yapmasına veya uygulamayı yönetmeye izin verilmez. İşleçler cihaz ekleyip silebilir, cihaz kümelerini yönetebilir ve analiz ve işleri çalıştırabilir. 

## <a name="create-a-custom-role"></a>Özel rol oluşturma

Çözümünüz daha ayrıntılı erişim denetimleri gerektiriyorsa, özel izin kümeleriyle özel roller oluşturabilirsiniz. Özel bir rol oluşturmak için uygulamanızın **Yönetim** bölümünde **Roller** sayfasına gidin. Ardından **+ Yeni rol**' i seçin ve rolünüz için bir ad ve açıklama ekleyin. Rolünüzün gerektirdiği izinleri seçin ve ardından **Kaydet**' i seçin.

Kullanıcıları, yerleşik bir role eklediğiniz şekilde özel rolünüze ekleyebilirsiniz.

> [!div class="mx-imgBorder"]
> özel bir rol oluşturmak ![](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Özel rol seçenekleri

Özel bir rol tanımladığınızda, bir kullanıcının rolün üyesi olmaları durumunda izin verilen izinler kümesini seçersiniz. Bazı izinler diğerlerine bağımlıdır. Örneğin, bir role **uygulama panoları güncelleştirme** iznini eklerseniz, **uygulama panoları görüntüle** izni otomatik olarak eklenir. Aşağıdaki tablolar, özel roller oluştururken kullanabileceğiniz kullanılabilir izinleri ve bunların bağımlılıklarını özetler.

#### <a name="managing-devices"></a>Cihazları yönetme

**Cihaz şablonu izinleri**

| Ad | Bağımlılıklar |
| ---- | -------- |
| Görünüm | None     |
| Yönet | Görünüm <br/> Diğer bağımlılıklar: cihaz örneklerini görüntüle  |
| Tam denetim | Görüntüleme, yönetme <br/> Diğer bağımlılıklar: cihaz örneklerini görüntüle |

**Cihaz örneği izinleri**

| Ad | Bağımlılıklar |
| ---- | -------- |
| Görünüm | None <br/> Diğer bağımlılıklar: cihaz şablonlarını ve cihaz gruplarını görüntüleme |
| Güncelleştirme | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını ve cihaz gruplarını görüntüleme  |
| Oluşturma | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını ve cihaz gruplarını görüntüleme  |
| Sil | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını ve cihaz gruplarını görüntüleme  |
| Yürütme komutları | Güncelleştirme, görüntüleme <br/> Diğer bağımlılıklar: cihaz şablonlarını ve cihaz gruplarını görüntüleme  |
| Tam denetim | Görüntüleme, güncelleştirme, oluşturma, silme, yürütme komutları <br/> Diğer bağımlılıklar: cihaz şablonlarını ve cihaz gruplarını görüntüleme  |

**Cihaz grupları izinleri**

| Ad | Bağımlılıklar |
| ---- | -------- |
| Görünüm | None <br/> Diğer bağımlılıklar: cihaz şablonlarını ve cihaz örneklerini görüntüleme |
| Güncelleştirme | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını ve cihaz örneklerini görüntüleme   |
| Oluşturma | Görüntüle, Güncelleştir <br/> Diğer bağımlılıklar: cihaz şablonlarını ve cihaz örneklerini görüntüleme   |
| Sil | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını ve cihaz örneklerini görüntüleme   |
| Tam denetim | Görüntüleme, güncelleştirme, oluşturma, silme <br/> Diğer bağımlılıklar: cihaz şablonlarını ve cihaz örneklerini görüntüleme |

**Cihaz bağlantısı yönetimi izinleri**

| Ad | Bağımlılıklar |
| ---- | -------- |
| Örneği oku | None <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz gruplarını, cihaz örneklerini görüntüleme |
| Instnace 'yi Yönet | None |
| Küresel oku | None   |
| Küresel yönetme | Küresel oku |
| Tam denetim | Örneği oku, örneği Yönet, genel oku, genel Yönet. <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz gruplarını, cihaz örneklerini görüntüleme |

**İşler izinleri**

| Ad | Bağımlılıklar |
| ---- | -------- |
| Görünüm | None <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz örneklerini ve cihaz gruplarını görüntüleme |
| Güncelleştirme | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz örneklerini ve cihaz gruplarını görüntüleme |
| Oluşturma | Görüntüle, Güncelleştir <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz örneklerini ve cihaz gruplarını görüntüleme |
| Sil | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz örneklerini ve cihaz gruplarını görüntüleme |
| Yürütme | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz örneklerini ve cihaz gruplarını görüntüleyin; Cihaz örneklerini güncelleştirme; Cihaz örneklerinde komutları yürütme |
| Tam denetim | Görüntüleme, güncelleştirme, oluşturma, silme, yürütme <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz örneklerini ve cihaz gruplarını görüntüleyin; Cihaz örneklerini güncelleştirme; Cihaz örneklerinde komutları yürütme |

**Kurallar izinleri**

| Ad | Bağımlılıklar |
| ---- | -------- |
| Görünüm | None <br/> Diğer bağımlılıklar: cihaz şablonlarını görüntüleme |
| Güncelleştirme | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını görüntüleme |
| Oluşturma | Görüntüle, Güncelleştir <br/> Diğer bağımlılıklar: cihaz şablonlarını görüntüleme |
| Sil | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını görüntüleme |
| Tam denetim | Görüntüleme, güncelleştirme, oluşturma, silme <br/> Diğer bağımlılıklar: cihaz şablonlarını görüntüleme |

#### <a name="managing-the-app"></a>Uygulamayı yönetme

**Uygulama ayarları izinleri**

| Ad | Bağımlılıklar |
| ---- | -------- |
| Görünüm | None     |
| Güncelleştirme | Görünüm   |
| Kopyala | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz örneklerini, cihaz gruplarını, panoları, veri vermeyi, marka, yardım bağlantılarını, özel rolleri, kuralları görüntüle |
| Sil | Görünüm   |
| Tam denetim | Görüntüleme, güncelleştirme, kopyalama, silme <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz gruplarını, uygulama panoları, veri dışarı aktarma, marka, yardım bağlantıları, özel roller, kurallar |

**Uygulama şablonu dışarı aktarma izinleri**

| Ad | Bağımlılıklar |
| ---- | -------- |
| Görünüm | None     |
| Dışarı Aktarma | Görünüm <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz örneklerini, cihaz gruplarını, panoları, veri vermeyi, marka, yardım bağlantılarını, özel rolleri, kuralları görüntüle |
| Tam denetim | Görüntüle, dışarı aktar <br/> Diğer bağımlılıklar: cihaz şablonlarını, cihaz gruplarını, uygulama panoları, veri dışarı aktarma, marka, yardım bağlantıları, özel roller, kurallar |

**Faturalandırma izinleri**

| Ad | Bağımlılıklar |
| ---- | -------- |
| Yönet | None     |
| Tam denetim | Yönet |

#### <a name="managing-users-and-roles"></a>Kullanıcıları ve rolleri yönetme

**Özel rol izinleri**

| Ad | Bağımlılıklar |
| ---- | -------- |
| Görünüm | None |
| Güncelleştirme | Görünüm |
| Oluşturma | Görüntüle, Güncelleştir |
| Sil | Görünüm |
| Tam denetim | Görüntüleme, güncelleştirme, oluşturma, silme |

**Kullanıcı yönetimi izinleri**

| Ad | Bağımlılıklar |
| ---- | -------- |
| Görünüm | None <br/> Diğer bağımlılıklar: özel rolleri görüntüleme |
| Ekle | Görünüm <br/> Diğer bağımlılıklar: özel rolleri görüntüleme |
| Sil | Görünüm <br/> Diğer bağımlılıklar: özel rolleri görüntüleme |
| Tam denetim | Görüntüleme, ekleme, silme <br/> Diğer bağımlılıklar: özel rolleri görüntüleme |

> [!NOTE]
> Özel bir rolde başka Kullanıcı ekleme izni veren bir Kullanıcı, yalnızca kendi rolünden aynı veya daha az izne sahip bir role Kullanıcı ekleyebilirler.

#### <a name="customizing-the-app"></a>Uygulamayı özelleştirme

**Uygulama panosu izinleri**

| Ad | Bağımlılıklar |
| ---- | -------- |
| Görünüm | None     |
| Güncelleştirme | Görünüm   |
| Oluşturma | Görüntüle, Güncelleştir |
| Sil | Görünüm   |
| Tam denetim | Görüntüleme, güncelleştirme, oluşturma, silme |

**Kişisel panolar izinleri**

| Ad | Bağımlılıklar |
| ---- | -------- |
| Görünüm | None     |
| Güncelleştirme | Görünüm   |
| Oluşturma | Görüntüle, Güncelleştir   |
| Sil | Görünüm   |
| Tam denetim | Görüntüleme, güncelleştirme, oluşturma, silme |

**Marka, ayrıcalıklı simge ve renkler izinleri**

| Ad | Bağımlılıklar |
| ---- | -------- |
| Görünüm | None     |
| Güncelleştirme | Görünüm   |
| Tam denetim | Görüntüle, Güncelleştir |

**Yardım bağlantıları izinleri**

| Ad | Bağımlılıklar |
| ---- | -------- |
| Görünüm | None     |
| Güncelleştirme | Görünüm   |
| Tam denetim | Görüntüle, Güncelleştir |

#### <a name="extending-the-app"></a>Uygulamayı genişletme

**Veri dışarı aktarma izinleri**

| Ad | Bağımlılıklar |
| ---- | -------- |
| Görünüm | None     |
| Güncelleştirme | Görünüm   |
| Oluşturma | Görüntüle, Güncelleştir  |
| Sil | Görünüm   |
| Tam denetim | Görüntüleme, güncelleştirme, oluşturma, silme |

**API belirteci izinleri**

| Ad | Bağımlılıklar |
| ---- | -------- |
| Görünüm | None     |
| Oluşturma | Görünüm   |
| Sil | Görünüm   |
| Tam denetim | Görüntüleme, oluşturma, silme |

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central uygulamanızda kullanıcıları ve rolleri yönetme hakkında bilgi edindiğinize göre, önerilen sonraki adım [faturanızı nasıl yöneteceğinizi](howto-view-bill.md)öğrenirsiniz.
