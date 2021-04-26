---
title: 'Hızlı başlangıç: JavaScript ile bir yönetim grubu oluşturma'
description: Bu hızlı başlangıçta, kaynaklarınızı bir kaynak hiyerarşisinde düzenlemek üzere bir yönetim grubu oluşturmak için JavaScript kullanırsınız.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: 553133f30bd16543444356e8cda3e70458247b5c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536009"
---
# <a name="quickstart-create-a-management-group-with-javascript"></a>Hızlı başlangıç: JavaScript ile bir yönetim grubu oluşturma

Yönetim grupları, birden çok abonelik üzerinde erişimi, ilkeyi ve uyumluluğu yönetmenize yardımcı olan kapsayıcılardır. [Azure ilkesi](../policy/overview.md) ve [Azure rol tabanlı erişim denetimleriyle](../../role-based-access-control/overview.md)kullanılabilecek etkili ve verimli bir hiyerarşi oluşturmak için bu kapsayıcıları oluşturun. Yönetim grupları hakkında daha fazla bilgi için bkz. [Azure Yönetim gruplarıyla kaynaklarınızı düzenleme](overview.md).

Dizinde oluşturulan ilk yönetim grubunun tamamlanması 15 dakika sürebilir. Dizininiz için Azure 'da yönetim grupları hizmetini ayarlamak için ilk kez çalışan süreçler vardır. İşlem tamamlandığında bir bildirim alırsınız. Daha fazla bilgi için bkz. [yönetim gruplarının ilk kurulumu](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

- Başlamadan önce, en az sürüm 12 [Node.js](https://nodejs.org/) yüklü olduğundan emin olun.

- Kiracıdaki tüm Azure AD kullanıcıları, [hiyerarşi koruması](./how-to/protect-resource-hierarchy.md#setting---require-authorization) etkinleştirilmemişse, bu kullanıcıya atanan yönetim grubu yazma izni olmadan bir yönetim grubu oluşturabilir. Bu yeni yönetim grubu kök yönetim grubunun veya [Varsayılan yönetim grubunun](./how-to/protect-resource-hierarchy.md#setting---default-management-group) bir alt öğesi haline gelir ve oluşturucuya "Owner" rol ataması verilir. Yönetim grubu hizmeti, bu becerisine, rol atamalarının kök düzeyinde gerekmemesi için izin verir. Kök yönetim grubuna, bir kullanıcı oluşturulduğunda erişemez. Yönetim grupları 'nı kullanmaya başlamak üzere Azure AD Genel yöneticilerini bulmanın önüne geçmek için, kök düzeyinde ilk yönetim gruplarının oluşturulmasına izin veririz.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>Uygulama kurulumu

Yönetim gruplarını yönetmek için JavaScript 'i etkinleştirmek üzere, ortamın ayarlanması gerekir. Bu kurulum, [Windows 10 üzerinde Bash](/windows/wsl/install-win10)dahil olmak üzere JavaScript 'in kullanılabileceği her yerde çalışmaktadır.

1. Aşağıdaki komutu çalıştırarak yeni bir Node.js projesi ayarlayın.

   ```bash
   npm init -y
   ```

1. Yargs modülüne bir başvuru ekleyin.

   ```bash
   npm install yargs
   ```

1. Azure Kaynak Grafiği modülüne bir başvuru ekleyin.

   ```bash
   npm install @azure/arm-managementgroups
   ```

1. Azure kimlik doğrulama kitaplığına bir başvuru ekleyin.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > _package.json_ `@azure/arm-managementgroups` 'ın Version **1.1.0** veya üzeri olduğunu ve `@azure/ms-rest-nodeauth` Sürüm **3.0.5** veya üzeri olduğunu doğrulayın.

## <a name="create-the-management-group"></a>Yönetim grubunu oluşturma

1. _index.js_ adlı yeni bir dosya oluşturun ve aşağıdaki kodu girin.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const managementGroups = require("@azure/arm-managementgroups");

   if (argv.groupID && argv.displayName) {
       const createMG = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new managementGroups.ManagementGroupsAPI(credentials);
          const result = await client.managementGroups.createOrUpdate(
             groupId: argv.groupID,
             {
                 displayName: argv.displayName
             }
          );
          console.log(result);
       };

       createMG();
   }
   ```

1. Terminalde aşağıdaki komutu girin:

   ```bash
   node index.js --groupID "<NEW_MG_GROUP_ID>" --displayName "<NEW_MG_FRIENDLY_NAME>"
   ```

   Her bir belirteç `<>` yer tutucusunu, sırasıyla _YÖNETIM grubu kimliğiniz_ ve _Yönetim grubu kolay adı_ ile değiştirdiğinizden emin olun.

   Betik kimlik doğrulamaya çalıştığında, terminalde aşağıdaki iletiye benzer bir ileti görüntülenir:

   > Oturum açmak için, sayfayı açmak üzere bir Web tarayıcısı kullanın https://microsoft.com/devicelogin ve kimlik doğrulaması yapmak IÇIN FGB56WJUGK kodunu girin.

   Tarayıcıda kimlik doğrulamasından sonra betik çalışmaya devam eder.

Yönetim grubu oluşturmanın sonucu konsola çıktıdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Yüklü kitaplıkları uygulamanızdan kaldırmak istiyorsanız aşağıdaki komutu çalıştırın.

```bash
npm uninstall @azure/arm-managementgroups @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, kaynak hiyerarşinizi düzenlemek için bir yönetim grubu oluşturdunuz. Yönetim grubu, abonelikleri veya diğer yönetim gruplarını içerebilir.

Yönetim grupları ve kaynak hiyerarşinizi yönetme hakkında daha fazla bilgi edinmek için şu çalışmaya devam edin:

> [!div class="nextstepaction"]
> [Kaynakları yönetim gruplarıyla yönetme](./manage.md)
