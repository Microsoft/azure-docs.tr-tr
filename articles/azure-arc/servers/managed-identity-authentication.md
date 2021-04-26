---
title: Yay etkin sunucularla Azure kaynaklarında kimlik doğrulama
description: Bu makalede, Arc etkin sunucular için Azure Instance Metadata Service desteği ve gizli anahtar kullanarak Azure kaynaklarında ve yerel olarak nasıl kimlik doğrulayabileceğinizi açıklanmaktadır.
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 49b70928ae972da8e0a0d14d711e4b6f246cca6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96939196"
---
# <a name="authenticate-against-azure-resources-with-arc-enabled-servers"></a>Yay etkin sunucularla Azure kaynaklarında kimlik doğrulama

Azure Arc etkin sunucuları üzerinde doğrudan çalışan uygulamalar veya süreçler, Azure Active Directory tabanlı kimlik doğrulamasını destekleyen diğer Azure kaynaklarına erişmek için yönetilen kimliklerden yararlanabilir. Bir uygulama, kendi kimliğini temsil eden, Arc etkin sunucular için sistem tarafından atanan bir [erişim belirteci](../../active-directory/develop/developer-glossary.md#access-token) elde edebilir ve bunu başka bir hizmette kimlik doğrulaması yapmak için ' taşıyıcı ' belirteci olarak kullanabilir.

Yönetilen kimliklerin ayrıntılı bir açıklaması ve sistem tarafından atanan ve Kullanıcı tarafından atanan kimlikler arasındaki ayrım için [yönetilen kimliğe genel bakış](../../active-directory/managed-identities-azure-resources/overview.md) belgelerine bakın.

Bu makalede, bir sunucunun Azure [Key Vault](../../key-vault/general/overview.md)erişmek için sistem tarafından atanan yönetilen kimliği nasıl kullanabileceği gösterilmektedir. Önyükleme görevi gören Key Vault, istemci uygulamanızın Azure Active Directory (AD) ile güvenliği olmayan kaynaklara erişmek için gizli dizi kullanmasını mümkün kılar. Örneğin, IIS web sunucularınız tarafından kullanılan TLS/SSL sertifikaları Azure Key Vault ' de depolanabilir ve sertifikaları Azure dışındaki Windows veya Linux sunucularına güvenli bir şekilde dağıtabilirsiniz.

## <a name="security-overview"></a>Güvenliğe genel bakış

Sunucunuzu Azure Arc etkin sunucularına ekleme sırasında, bir Azure VM için gerçekleştirilme benzer şekilde, yönetilen bir kimlik kullanılarak yapılandırmak için birkaç işlem gerçekleştirilir:

- Azure Resource Manager, Arc etkin sunucusunda sistem tarafından atanan yönetilen kimliği etkinleştirme isteği alır.

- Azure Resource Manager, Azure AD 'de sunucu kimliği için bir hizmet sorumlusu oluşturur. Hizmet sorumlusu, bu abonelik tarafından güvenilen Azure AD kiracısında oluşturulur.

- Azure Resource Manager, hizmet sorumlusu istemci KIMLIĞI ve sertifikasıyla [Windows](../../virtual-machines/windows/instance-metadata-service.md) veya [Linux](../../virtual-machines/linux/instance-metadata-service.md) için Azure Instance Metadata Service (IMDS) kimlik uç noktasını güncelleştirerek sunucudaki kimliği yapılandırır. Uç nokta, yalnızca sunucu içinden, iyi bilinen, yönlendirilemeyen bir IP adresi kullanılarak erişilebilen bir REST uç noktasıdır. Bu hizmet, yönetim ve yapılandırmaya yardımcı olmak üzere yay etkin sunucu hakkında meta veri bilgilerinin bir alt kümesini sağlar.

Yönetilen kimlik etkin bir sunucunun ortamı, Windows yay etkin bir sunucusunda aşağıdaki değişkenlerle yapılandırılır:

- **IMDS_ENDPOINT**: `http://localhost:40342` yay etkin sunucular için IMDS uç nokta IP adresi.

- **IDENTITY_ENDPOINT**: hizmetin yönetilen kimliğine karşılık gelen localhost uç noktası `http://localhost:40342/metadata/identity/oauth2/token` .

Sunucuda çalışan kodunuz, Azure örnek meta veri hizmeti uç noktasından yalnızca sunucunun içinden erişilebilen bir belirteç talep edebilir.

**IDENTITY_ENDPOINT** sistem ortam değişkeni, uygulamalar tarafından kimlik uç noktasını saptamak için kullanılır. Uygulamalar **IDENTITY_ENDPOINT** ve **IMDS_ENDPOINT** değerlerini almayı ve bunları kullanmayı denemelidir. Herhangi bir erişim düzeyi olan uygulamaların uç noktalara istek yapmasına izin verilir. Meta veri yanıtları normal şekilde işlenir ve makinedeki herhangi bir işleme verilir. Ancak, bir belirteci kullanıma sunabileceğiniz bir istek yapıldığında, istemcinin yalnızca daha yüksek ayrıcalıklı kullanıcılar tarafından kullanılabilen verilere erişebildiklerinden emin olmak için bir gizli anahtar sağlaması gerekir.

## <a name="prerequisites"></a>Önkoşullar

- Yönetilen kimliklerin anlaşılmasıdır.
- Bağlı ve yay etkin sunucularla kayıtlı bir sunucu.
- Gerekli kaynak oluşturma ve rol yönetimi adımlarını gerçekleştirmek için abonelik veya kaynak grubundaki * * [sahip grubunun](../../role-based-access-control/built-in-roles.md#owner)bir üyesisiniz.
- Kimlik bilgilerinizi depolamak ve almak için bir Azure Key Vault. ve Azure Arc kimlik erişimi 'ni anahtar kasasına atayın.

    - Oluşturulmuş bir Key Vault yoksa, bkz. [Create Key Vault](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#create-a-key-vault-).
    - Sunucu tarafından kullanılan yönetilen kimliğin erişimini yapılandırmak için bkz. [Linux için erişim verme](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#grant-access) veya [Windows için erişim verme](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#grant-access). 5. adım için, Arc etkin sunucusunun adını girmeniz gerekir. PowerShell kullanarak bunu gerçekleştirmek için bkz. [PowerShell kullanarak erişim Ilkesi atama](../../key-vault/general/assign-access-policy-powershell.md).

## <a name="acquiring-an-access-token-using-rest-api"></a>REST API kullanarak erişim belirteci edinme

Azure kaynaklarıyla kimlik doğrulaması yapmak için sistem tarafından atanan yönetilen kimliği edinme ve kullanma yöntemi, Azure VM ile nasıl gerçekleştirildiğiyle benzerdir.

Bir yay kullanan Windows Server, PowerShell kullanarak, belirli bir bağlantı noktasındaki yerel konaktan belirteci almak için web isteğini çağırır. **IDENTITY_ENDPOINT** IP adresini veya ortam değişkenini kullanarak isteği belirtin.

```powershell
$apiVersion = "2020-06-01"
$resource = "https://management.azure.com/"
$endpoint = "{0}?resource={1}&api-version={2}" -f $env:IDENTITY_ENDPOINT,$resource,$apiVersion
$secretFile = ""
try
{
    Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'} -UseBasicParsing
}
catch
{
    $wwwAuthHeader = $_.Exception.Response.Headers["WWW-Authenticate"]
    if ($wwwAuthHeader -match "Basic realm=.+")
    {
        $secretFile = ($wwwAuthHeader -split "Basic realm=")[1]
    }
}
Write-Host "Secret file path: " $secretFile`n
$secret = cat -Raw $secretFile
$response = Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'; Authorization="Basic $secret"} -UseBasicParsing
if ($response)
{
    $token = (ConvertFrom-Json -InputObject $response.Content).access_token
    Write-Host "Access token: " $token
}
```

Aşağıdaki yanıt döndürülen bir örnektir:

:::image type="content" source="media/managed-identity-authentication/powershell-token-output-example.png" alt-text="PowerShell kullanarak erişim belirtecinin başarılı bir şekilde alınması.":::

Bash kullanarak bir yay etkin Linux sunucusu için, belirli bir bağlantı noktasındaki yerel konaktan belirteç almak üzere Web isteğini çağırılır. **IDENTITY_ENDPOINT** IP adresini veya ortam değişkenini kullanarak aşağıdaki isteği belirtin. Bu adımı gerçekleştirmek için bir SSH istemcisine ihtiyacınız vardır.

```bash
ChallengeTokenPath=$(curl -s -D - -H Metadata:true "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com" | grep Www-Authenticate | cut -d "=" -f 2 | tr -d "[:cntrl:]")
ChallengeToken=$(cat $ChallengeTokenPath)
if [ $? -ne 0 ]; then
    echo "Could not retrieve challenge token, double check that this command is run with root privileges."
else
    curl -s -H Metadata:true -H "Authorization: Basic $ChallengeToken" "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com"
fi
```

Aşağıdaki yanıt döndürülen bir örnektir:

:::image type="content" source="media/managed-identity-authentication/bash-token-output-example.png" alt-text="Bash kullanarak erişim belirtecinin başarılı bir şekilde alınması.":::

Yanıt, Azure 'daki herhangi bir kaynağa erişmeniz için gereken erişim belirtecini içerir. Azure Key Vault kimlik doğrulaması yapılacak yapılandırmayı tamamlamaya yönelik olarak, bkz. Windows veya [Linux Ile erişim Key Vault](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#access-data) [Key Vault](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#access-data) .

## <a name="next-steps"></a>Sonraki adımlar

- Azure Key Vault hakkında daha fazla bilgi edinmek için bkz. [Key Vault genel bakış](../../key-vault/general/overview.md).

- [PowerShell kullanarak](../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) veya [Azure CLI](../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)kullanarak bir kaynağa yönetilen kimlik erişimi atamayı öğrenin.