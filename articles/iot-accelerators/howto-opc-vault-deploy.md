---
title: OPC Kasası sertifika yönetimi hizmetini dağıtma-Azure | Microsoft Docs
description: OPC Kasası sertifika yönetimi hizmetini sıfırdan dağıtma.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f577059e1ebf70e3a9dfe9e538a9d3d49d7c8e96
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200001"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>OPC Kasası sertifika yönetimi hizmetini derleme ve dağıtma

Bu makalede, OPC Kasası sertifika yönetimi hizmetinin Azure 'da nasıl dağıtılacağı açıklanır.

> [!NOTE]
> Daha fazla bilgi için GitHub [OPC Kasası deposuna](https://github.com/Azure/azure-iiot-opc-vault-service)bakın.

## <a name="prerequisites"></a>Önkoşullar

### <a name="install-required-software"></a>Gerekli yazılımları yükler

Şu anda derleme ve dağıtma işlemi Windows ile sınırlıdır.
Örnekler, dağıtım için hizmet ve C# örnek oluşturmanız gereken .NET Standard için yazılır.
.NET Standard için ihtiyacınız olan tüm araçlar .NET Core araçları ile birlikte gelir. Bkz. [.NET Core ile çalışmaya başlama](https://docs.microsoft.com/dotnet/articles/core/getting-started).

1. [.NET Core 2.1 + 'Yi yükler][dotnet-install].
2. [Docker 'ı yükler][docker-url] (isteğe bağlı, yalnızca yerel Docker derlemesi gerekliyse).
4. [PowerShell Için Azure komut satırı araçlarını][powershell-install]yükler.
5. Bir [Azure aboneliğine][azure-free]kaydolun.

### <a name="clone-the-repository"></a>Depoyu kopyalama

Henüz yapmadıysanız, bu GitHub deposunu kopyalayın. Bir komut istemi veya Terminal açın ve aşağıdakileri çalıştırın:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

Alternatif olarak, depoyu doğrudan Visual Studio 2017 ' de kopyalayabilirsiniz.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Windows üzerinde Azure hizmetini derleme ve dağıtma

PowerShell betiği OPC Kasası mikro hizmetini ve uygulamayı dağıtmanın kolay bir yolunu sağlar.

1. Depo kökünde bir PowerShell penceresi açın. 
3. Dağıtım klasörüne `cd deploy`gidin.
3. Diğer dağıtılan Web sayfalarıyla `myResourceGroup` çakışmaya neden olması olası olmayan bir ad seçin. Bu makalenin ilerleyen bölümlerindeki "Web sitesi adı zaten kullanımda" bölümüne bakın.
5. Etkileşimli yükleme için ile `.\deploy.ps1` dağıtımı başlatın veya tam bir komut satırı girin:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Bu dağıtım ile geliştirmeyi planlıyorsanız, Swagger Kullanıcı arabirimini etkinleştirmek `-development 1` ve hata ayıklama yapılarını dağıtmak için ekleyin.
6. Aboneliğinizde oturum açmak ve ek bilgi sağlamak için betikteki yönergeleri izleyin.
9. Başarılı bir derleme ve dağıtım işleminden sonra şu iletiyi görmeniz gerekir:
   ```
   To access the web client go to:
   https://myResourceGroup.azurewebsites.net

   To access the web service go to:
   https://myResourceGroup-service.azurewebsites.net

   To start the local docker GDS server:
   .\myResourceGroup-dockergds.cmd

   To start the local dotnet GDS server:
   .\myResourceGroup-gds.cmd
   ```

   > [!NOTE]
   > Sorun oluşması durumunda, makalenin devamındaki "dağıtım hataları sorunlarını giderme" bölümüne bakın.

8. En sevdiğiniz tarayıcıyı açın ve uygulama sayfasını açın:`https://myResourceGroup.azurewebsites.net`
8. Web uygulamasına ve OPC Kasası mikro hizmetine dağıtımdan sonra ısınma için birkaç dakika verin. Web giriş sayfası, ilk yanıtları yapana kadar bir dakika boyunca ilk kullanımda askıda kalabilir.
11. Swagger API 'sine göz atmak için şunu açın:`https://myResourceGroup-service.azurewebsites.net`
13. DotNet ile yerel bir GDS sunucusunu başlatmak için başlatın `.\myResourceGroup-gds.cmd`. Docker ile başlatın `.\myResourceGroup-dockergds.cmd`.

Bir derlemeyi tamamen aynı ayarlarla yeniden dağıtmak mümkündür. Bu tür bir işlemin tüm uygulama gizli dizilerini yenilediğini ve Azure Active Directory (Azure AD) uygulama kayıtlarında bazı ayarları sıfırlayabileceğini unutmayın.

Yalnızca Web uygulaması ikili dosyalarını yeniden dağıtmak da mümkündür. Parametresiyle `-onlyBuild 1`, hizmetin yeni ZIP paketleri ve uygulama Web uygulamalarına dağıtılır.

Dağıtım başarılı olduktan sonra Hizmetleri kullanmaya başlayabilirsiniz. Bkz. [OPC Kasası sertifika yönetimi hizmetini yönetme](howto-opc-vault-manage.md).

## <a name="delete-the-services-from-the-subscription"></a>Abonelikten hizmetleri silme

Bunu yapmak için:

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. Hizmetin dağıtıldığı kaynak grubuna gidin.
3. **Kaynak grubunu sil**'i seçip onaylayın.
4. Kısa bir süre sonra dağıtılan tüm hizmet bileşenleri silinir.
5. **Azure Active Directory** > **uygulama kayıtları**gidin.
6. Dağıtılan her kaynak grubu için üç kayıt listelenmiş olmalıdır. Kayıtlar şu adlara sahiptir: `resourcegroup-client`, `resourcegroup-module`, `resourcegroup-service`. Her kaydı ayrı ayrı silin.

Artık dağıtılan tüm bileşenler kaldırılır.

## <a name="troubleshooting-deployment-failures"></a>Dağıtım hatalarıyla ilgili sorunları giderme

### <a name="resource-group-name"></a>Kaynak grubu adı

Kısa ve basit kaynak grubu adı kullanın. Ad, kaynakları ve hizmet URL önekini adlandırmak için de kullanılır. Bu nedenle, kaynak adlandırma gereksinimleriyle uyumlu olmalıdır.  

### <a name="website-name-already-in-use"></a>Web sitesi adı zaten kullanımda

Web sitesinin adı zaten kullanımda olabilir. Farklı bir kaynak grubu adı kullanmanız gerekir. Dağıtım betiği tarafından kullanılan ana bilgisayar adları şunlardır: https://resourcegroupname.azurewebsites.net ve. https://resourgroupname-service.azurewebsites.net
Diğer hizmet adları, kısa ad karmalarının birleşimiyle oluşturulmuştur ve diğer hizmetlerle çakışmadan daha düşüktür.

### <a name="azure-ad-registration"></a>Azure AD kaydı 

Dağıtım betiği, Azure AD 'de üç Azure AD uygulamasını kaydetmeye çalışır. Seçili Azure AD kiracısındaki izinlerinize bağlı olarak, bu işlem başarısız olabilir. İki seçenek vardır:

- Kiracılar listesinden bir Azure AD kiracısı seçerseniz, betiği yeniden başlatın ve listeden farklı bir tane seçin.
- Alternatif olarak, özel bir Azure AD kiracısını başka bir abonelikte dağıtın. Betiği yeniden başlatın ve kullanmayı seçin.

## <a name="deployment-script-options"></a>Dağıtım betiği seçenekleri

Betik aşağıdaki parametreleri alır:


```
-resourceGroupName
```

Bu, var olan veya yeni bir kaynak grubunun adı olabilir.

```
-subscriptionId
```


Bu, kaynakların dağıtılacağı abonelik KIMLIĞIDIR. İsteğe bağlıdır.

```
-subscriptionName
```


Alternatif olarak, abonelik adını kullanabilirsiniz.

```
-resourceGroupLocation
```


Bu bir kaynak grubu konumudur. Belirtilmişse, bu parametre bu konumda yeni bir kaynak grubu oluşturmaya çalışır. Bu parametre Ayrıca isteğe bağlıdır.


```
-tenantId
```


Bu, kullanılacak Azure AD kiracısıdır. 

```
-development 0|1
```

Bu, geliştirme için dağıtımaktır. Hata ayıklama derlemesini kullanın ve ASP.NET ortamını geliştirme olarak ayarlayın. Uygulamayı `.publishsettings` ve hizmeti doğrudan dağıtmasını sağlamak için Visual Studio 2017 ' de içeri aktarma için oluşturun. Bu parametre Ayrıca isteğe bağlıdır.

```
-onlyBuild 0|1
```

Bu, yalnızca Web uygulamalarını yeniden oluşturmak ve yeniden dağıtmak ve Docker kapsayıcılarını yeniden derlemek için kullanılır. Bu parametre Ayrıca isteğe bağlıdır.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Sonraki adımlar

Artık OPC kasasını sıfırdan dağıtmayı öğrendiğinize göre şunları yapabilirsiniz:

> [!div class="nextstepaction"]
> [OPC kasasını yönetme](howto-opc-vault-manage.md)
