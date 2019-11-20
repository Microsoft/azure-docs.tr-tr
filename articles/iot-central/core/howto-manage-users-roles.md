---
title: Azure IoT Central uygulamasındaki kullanıcıları ve rolleri yönetme | Microsoft Docs
description: Yönetici olarak, Azure IoT Central uygulamanızda kullanıcıları ve rolleri yönetme
author: v-krghan
ms.author: v-krghan
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 937c4a4dbf976564cbf8dc562bdec3b328fc2bc0
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72950099"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>IoT Central uygulamanızdaki kullanıcıları ve rolleri yönetme

Bu makalede, yönetici olarak Azure IoT Central uygulamanızda Kullanıcı ekleme, düzenleme ve silme ve ayrıca Azure IoT Central uygulamanızda rolleri yönetme işlemlerinin nasıl yapılacağı açıklanır.

**Yönetim** bölümüne erişmek ve bunları kullanmak için, bir Azure IoT Central uygulaması için **yönetici** rolünde olmanız gerekir. Azure IoT Central uygulaması oluşturursanız, bu uygulama için otomatik olarak **yönetici** rolüne atanırsınız.


## <a name="add-users"></a>Kullanıcı ekle

Azure IoT Central uygulamasına oturum açabilmeniz ve bu uygulamaya erişebilmek için her kullanıcının bir kullanıcı hesabı olması gerekir. Microsoft hesapları (MSAs) ve Azure Active Directory (Azure AD) hesapları Azure IoT Central desteklenir. Azure Active Directory gruplar Şu anda Azure IoT Central 'da desteklenmemektedir.

Daha fazla bilgi için bkz. [Microsoft hesabı yardım](https://support.microsoft.com/products/microsoft-account?category=manage-account) ve [hızlı başlangıç: Azure Active Directory yeni kullanıcı ekleme](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Bir IoT Central uygulamasına kullanıcı eklemek için **Yönetim** bölümündeki **Kullanıcılar** sayfasına gidin.

    ![Kullanıcı listesi](media/howto-administer/image1.png)

1. Kullanıcı eklemek için, **Kullanıcılar** sayfasında **+ Kullanıcı Ekle**' yi seçin.

1. **Rol** açılan menüsünden Kullanıcı için bir rol seçin. Roller hakkında daha fazla bilgi için bu makalenin [rolleri Yönet](#manage-roles) bölümünden daha fazla bilgi edinebilirsiniz.

    ![Rol seçimi](media/howto-administer/image3.png)

    > [!NOTE]
    >  Toplu olarak Kullanıcı eklemek için, eklemek istediğiniz tüm kullanıcıların kullanıcı kimliklerini noktalı virgülle ayırarak girin. **Rol** açılan menüsünden bir rol seçin. Daha sonra **Kaydet**’e tıklayın.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Kullanıcılara atanan rolleri düzenleme

Roller atandıktan sonra değiştirilemez. Bir kullanıcıya atanmış rolü değiştirmek için kullanıcıyı silin ve ardından kullanıcıyı farklı bir rolle yeniden ekleyin.

> [!NOTE]
> Atanan roller IoT Central uygulamaya özeldir ve Azure portalından yönetilemez.

## <a name="delete-users"></a>Kullanıcıları Sil

Kullanıcıları silmek için **Kullanıcılar** sayfasında bir veya daha fazla onay kutusunu işaretleyin. Ardından **Sil**’i seçin.

## <a name="manage-roles"></a>Rolleri yönetme

Roller, kuruluşunuzda kimlerin IoT Central çeşitli görevleri gerçekleştirebilinizi denetlemenize olanak tanır. Uygulamanızın kullanıcılarına atayabileceğiniz üç rol vardır.

### <a name="administrator"></a>Yönetici

**Yönetici** rolündeki kullanıcıların bir uygulamadaki tüm işlevlere erişimi vardır.

Bir uygulamayı oluşturan kullanıcı otomatik olarak **yönetici** rolüne atanır. **Yönetici** rolünde her zaman en az bir kullanıcı olmalıdır.

### <a name="application-builder"></a>Uygulama Oluşturucu

Uygulama **Oluşturucu** rolündeki kullanıcılar, uygulamayı yönetme dışında bir uygulamadaki her şeyi gerçekleştirebilir. Oluşturucular cihaz şablonları ve aygıtları oluşturabilir, düzenleyebilir ve silebilir, cihaz kümelerini yönetebilir ve analiz ve işleri çalıştırabilir. Oluşturucuların uygulamanın **Yönetim** bölümüne erişimi olmayacaktır.

### <a name="application-operator"></a>Uygulama Işleci

**Uygulama işletmeni** rolündeki kullanıcılar cihaz şablonlarında değişiklik yapamaz ve uygulamayı yönetemez. İşleçler cihaz ekleyip silebilir, cihaz kümelerini yönetebilir ve analiz ve işleri çalıştırabilir. İşleçler, **uygulama Oluşturucusu** ve **Yönetim** sayfalarına erişemez.

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central kullanıcıları ve rolleri yönetme hakkında bilgi edindiğinize göre, önerilen sonraki adım Azure IoT Central [faturanızı görüntüleyin](howto-view-bill.md) .
