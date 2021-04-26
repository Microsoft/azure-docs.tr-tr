---
title: Tam Ekran Okuyucu Kaynağı oluşturma
titleSuffix: Azure Cognitive Services
description: Bu makalede, özel bir alt etki alanı ile yeni bir tam ekran okuyucu kaynağı oluşturma ve ardından Azure kiracınızda Azure AD 'yi yapılandırma gösterilmektedir.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: 369905ed5ee0ce9a93ec2be879b5e40114b0a380
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048672"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>Bir tam ekran okuyucu kaynağı oluşturma ve Azure Active Directory kimlik doğrulamasını yapılandırma

Bu makalede, bir tam ekran okuyucu kaynağı oluşturacak ve Azure Active Directory (Azure AD) kimlik doğrulamasını yapılandıracağız bir betik sağlıyoruz. Bu komut dosyası veya portalda her bir modern okuyucu kaynağı oluşturulduğunda, Azure AD izinleriyle de yapılandırılmalıdır. Bu betik size yardımcı olur.

Betik, tüm gerekli tam ekran okuyucuyu ve Azure AD kaynaklarını tek bir adımda oluşturmak ve yapılandırmak için tasarlanmıştır. Bununla birlikte, mevcut bir tam ekran okuyucu kaynağı için Azure AD kimlik doğrulamasını da yapılandırabilirsiniz, örneğin, zaten Azure portal bir tane oluşturmuş olmanız gerekir.

Bazı müşteriler için, geliştirme ve üretim için veya belki hizmetinizin dağıtıldığı birden çok farklı bölgede birden çok modern okuyucu kaynağı oluşturmak gerekebilir. Bu gibi durumlarda, farklı derinlikli okuyucu kaynakları oluşturmak ve bunları Azure AD izinleriyle yapılandırmak için geri dönüp betiği birden çok kez kullanabilirsiniz.

Komut dosyası esnek olacak şekilde tasarlanmıştır. Bu, öncelikle aboneliğinizde var olan tam ekran okuyucuyu ve Azure AD kaynaklarını arayacaktır ve yalnızca mevcut değilse bunları oluşturur. İlk kez bir derinlikli okuyucu kaynağı oluşturuyorsanız, komut dosyası ihtiyacınız olan her şeyi oluşturur. Bunu kullanmak istiyorsanız, Portal 'da oluşturulan mevcut bir tam ekran okuyucusu kaynağı için Azure AD 'yi yapılandırmak üzere bu işlemi de yapabilirsiniz. Ayrıca, birden çok modern okuyucu kaynağı oluşturmak ve yapılandırmak için de kullanılabilir.

## <a name="set-up-powershell-environment"></a>PowerShell ortamını ayarlama

1. [Azure Cloud Shell](../../cloud-shell/overview.md)açarak başlayın. Cloud Shell, sol üst taraftaki açılan menüde veya yazarak PowerShell olarak ayarlandığından emin olun `pwsh` .

1. Aşağıdaki kod parçacığını kopyalayıp kabuğa yapıştırın.

    ```azurepowershell-interactive
    function Create-ImmersiveReaderResource(
        [Parameter(Mandatory=$true, Position=0)] [String] $SubscriptionName,
        [Parameter(Mandatory=$true)] [String] $ResourceName,
        [Parameter(Mandatory=$true)] [String] $ResourceSubdomain,
        [Parameter(Mandatory=$true)] [String] $ResourceSKU,
        [Parameter(Mandatory=$true)] [String] $ResourceLocation,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupName,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupLocation,
        [Parameter(Mandatory=$true)] [String] $AADAppDisplayName="ImmersiveReaderAAD",
        [Parameter(Mandatory=$true)] [String] $AADAppIdentifierUri,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecretExpiration
    )
    {
        $unused = ''
        if (-not [System.Uri]::TryCreate($AADAppIdentifierUri, [System.UriKind]::Absolute, [ref] $unused)) {
            throw "Error: AADAppIdentifierUri must be a valid URI"
        }

        Write-Host "Setting the active subscription to '$SubscriptionName'"
        $subscriptionExists = Get-AzSubscription -SubscriptionName $SubscriptionName
        if (-not $subscriptionExists) {
            throw "Error: Subscription does not exist"
        }
        az account set --subscription $SubscriptionName

        $resourceGroupExists = az group exists --name $ResourceGroupName
        if ($resourceGroupExists -eq "false") {
            Write-Host "Resource group does not exist. Creating resource group"
            $groupResult = az group create --name $ResourceGroupName --location $ResourceGroupLocation
            if (-not $groupResult) {
                throw "Error: Failed to create resource group"
            }
            Write-Host "Resource group created successfully"
        }

        # Create an Immersive Reader resource if it doesn't already exist
        $resourceId = az cognitiveservices account show --resource-group $ResourceGroupName --name $ResourceName --query "id" -o tsv
        if (-not $resourceId) {
            Write-Host "Creating the new Immersive Reader resource '$ResourceName' (SKU '$ResourceSKU') in '$ResourceLocation' with subdomain '$ResourceSubdomain'"
            $resourceId = az cognitiveservices account create `
                            --name $ResourceName `
                            --resource-group $ResourceGroupName `
                            --kind ImmersiveReader `
                            --sku $ResourceSKU `
                            --location $ResourceLocation `
                            --custom-domain $ResourceSubdomain `
                            --query "id" `
                            -o tsv

            if (-not $resourceId) {
                throw "Error: Failed to create Immersive Reader resource"
            }
            Write-Host "Immersive Reader resource created successfully"
        }

        # Create an Azure Active Directory app if it doesn't already exist
        $clientId = az ad app show --id $AADAppIdentifierUri --query "appId" -o tsv
        if (-not $clientId) {
            Write-Host "Creating new Azure Active Directory app"
            $clientId = az ad app create --password $AADAppClientSecret --end-date "$AADAppClientSecretExpiration" --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully."
            Write-Host "NOTE: To manage your Active Directory app client secrets after this Immersive Reader Resource has been created please visit https://portal.azure.com and go to Home -> Azure Active Directory -> App Registrations -> $AADAppDisplayName -> Certificates and Secrets blade -> Client Secrets section" -ForegroundColor Yellow
        }

        # Create a service principal if it doesn't already exist
        $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv
        if (-not $principalId) {
            Write-Host "Creating new service principal"
            az ad sp create --id $clientId | Out-Null
            $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv

            if (-not $principalId) {
                throw "Error: Failed to create new service principal"
            }
            Write-Host "New service principal created successfully"
        }

        # Sleep for 5 seconds to allow the new service principal to propagate
        Write-Host "Sleeping for 5 seconds"
        Start-Sleep -Seconds 5

        Write-Host "Granting service principal access to the newly created Immersive Reader resource"
        $accessResult = az role assignment create --assignee $principalId --scope $resourceId --role "Cognitive Services User"
        if (-not $accessResult) {
            throw "Error: Failed to grant service principal access"
        }
        Write-Host "Service principal access granted successfully"

        # Grab the tenant ID, which is needed when obtaining an Azure AD token
        $tenantId = az account show --query "tenantId" -o tsv

        # Collect the information needed to obtain an Azure AD token into one object
        $result = @{}
        $result.TenantId = $tenantId
        $result.ClientId = $clientId
        $result.ClientSecret = $AADAppClientSecret
        $result.Subdomain = $ResourceSubdomain

        Write-Host "Success! " -ForegroundColor Green -NoNewline
        Write-Host "Save the following JSON object to a text file for future reference:"
        Write-Output (ConvertTo-Json $result)
    }
    ```

1. `Create-ImmersiveReaderResource`' <PARAMETER_VALUES> ' yer tutucularını uygun şekilde kendi değerlerinizle birlikte sağlayarak işlevi çalıştırın.

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource -SubscriptionName '<SUBSCRIPTION_NAME>' -ResourceName '<RESOURCE_NAME>' -ResourceSubdomain '<RESOURCE_SUBDOMAIN>' -ResourceSKU '<RESOURCE_SKU>' -ResourceLocation '<RESOURCE_LOCATION>' -ResourceGroupName '<RESOURCE_GROUP_NAME>' -ResourceGroupLocation '<RESOURCE_GROUP_LOCATION>' -AADAppDisplayName '<AAD_APP_DISPLAY_NAME>' -AADAppIdentifierUri '<AAD_APP_IDENTIFIER_URI>' -AADAppClientSecret '<AAD_APP_CLIENT_SECRET>' -AADAppClientSecretExpiration '<AAD_APP_CLIENT_SECRET_EXPIRATION>'
    ```

    Tam komut aşağıdakine benzer şekilde görünür. Burada her parametreyi açıklık için kendi satırına koyduk, böylece tüm komutu görebilirsiniz. Bu komutu olduğu gibi kopyalamayın veya kullanmayın. Yukarıdaki değerlerinizle yukarıdaki komutu kopyalayın ve kullanın. Bu örnekte, yukarıdaki ' <PARAMETER_VALUES> ' için işlevsiz değerler vardır. Sizinki, bu değerler için kendi adlarınızla karşılaştığınıza göre farklı olacaktır.

    ```
    Create-ImmersiveReaderResource
        -SubscriptionName 'MyOrganizationSubscriptionName'
        -ResourceName 'MyOrganizationImmersiveReader'
        -ResourceSubdomain 'MyOrganizationImmersiveReader'
        -ResourceSKU 'S0'
        -ResourceLocation 'westus2'
        -ResourceGroupName 'MyResourceGroupName'
        -ResourceGroupLocation 'westus2'
        -AADAppDisplayName 'MyOrganizationImmersiveReaderAADApp'
        -AADAppIdentifierUri 'https://MyOrganizationImmersiveReaderAADApp'
        -AADAppClientSecret 'SomeStrongPassword'
        -AADAppClientSecretExpiration '2021-12-31'
    ```

    | Parametre | Yorumlar |
    | --- | --- |
    | SubscriptionName |Tam ekran okuyucu kaynağınız için kullanılacak Azure aboneliğinin adı. Kaynak oluşturmak için bir aboneliğinizin olması gerekir. |
    | ResourceName |  '-' Karakteri ilk veya son karakter olmadığı sürece alfasayısal olmalı ve '-' karakterlerini içerebilir. Uzunluk 63 karakterden uzun olamaz.|
    | ResourceSubdomain |Tam ekran okuyucu kaynağınız için özel bir alt etki alanı gereklidir. Bu alt etki alanı, okuyucuyu başlatmak için derinlikli okuyucu hizmeti çağrılırken SDK tarafından kullanılır. Alt etki alanı, genel olarak benzersiz olmalıdır. '-' İlk veya son karakter olmadığı sürece alt etki alanı alfasayısal olmalıdır ve '-' karakterlerini içerebilir. Uzunluk 63 karakterden uzun olamaz. Kaynak zaten mevcutsa, bu parametre isteğe bağlıdır. |
    | ResourceSKU |Seçenekler: `S0` (Standart katman) veya `S1` (eğitim/kar amacı gütmeyen kuruluşlar). Kullanılabilir her SKU hakkında daha fazla bilgi edinmek için bilişsel [Hizmetler fiyatlandırma](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/) sayfamızı ziyaret edin. Kaynak zaten mevcutsa, bu parametre isteğe bağlıdır. |
    | ResourceLocation |Seçenekler: `eastus` , `eastus2` ,,, `southcentralus` `westus` `westus2` , `australiaeast` , `southeastasia` , `centralindia` , `japaneast` , `northeurope` , `uksouth` , `westeurope` . Kaynak zaten mevcutsa, bu parametre isteğe bağlıdır. |
    | ResourceGroupName |Kaynaklar, abonelikler içindeki kaynak gruplarında oluşturulur. Mevcut bir kaynak grubunun adını sağlayın. Kaynak grubu henüz yoksa, bu adı taşıyan yeni bir tane oluşturulur. |
    | ResourceGroupLocation |Kaynak grubunuz yoksa, grubun oluşturulacağı bir konum sağlamanız gerekir. Konumların bir listesini bulmak için öğesini çalıştırın `az account list-locations` . Döndürülen sonucun *Name* özelliğini (boşluk olmadan) kullanın. Kaynak grubunuz zaten varsa, bu parametre isteğe bağlıdır. |
    | AADAppDisplayName |Azure Active Directory Uygulama görünen adı. Mevcut bir Azure AD uygulaması bulunamazsa, bu adı taşıyan yeni bir tane oluşturulur. Azure AD uygulaması zaten mevcutsa bu parametre isteğe bağlıdır. |
    | Aadappıdentifieruri |Azure AD uygulaması için URI. Mevcut bir Azure AD uygulaması bulunamazsa, bu URI 'ye sahip yeni bir tane oluşturulur. Örneğin, `https://immersivereaderaad-mycompany`. |
    | AADAppClientSecret |Daha sonra, derinlikli okuyucuyu başlatmak üzere bir belirteç alırken kimlik doğrulaması için kullanılacak bir parola. Parola en az 16 karakter uzunluğunda olmalı, en az 1 özel karakter içermeli ve en az 1 sayısal karakter içermelidir. Bu kaynağı oluşturduktan sonra Azure AD uygulama istemci gizli dizilerini yönetmek için lütfen ziyaret edin https://portal.azure.com ve giriş > Azure Active Directory-> uygulama kayıtları-> `[AADAppDisplayName]` -> sertifikalar ve gizlilikler dikey penceresi-> istemci gizli bilgileri bölümüne gidin (aşağıdaki "Azure AD uygulama gizli dizilerini yönetme" ekran görüntüsünde gösterildiği gibi). |
    | Aadappclientsecretexpiasyon |Sona ereceği tarih veya tarih/saat `[AADAppClientSecret]` (örn. ' 2020-12-31T11:59:59 + 00:00 ' veya ' 2020-12-31 '). |

    Azure AD uygulama sırlarınızı yönetin

    ![Azure Portal sertifikaları ve gizlilikler dikey penceresi](./media/client-secrets-blade.png)

1. JSON çıkışını daha sonra kullanmak üzere bir metin dosyasına kopyalayın. Çıktı aşağıdaki gibi görünmelidir.

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>Sonraki adımlar

* Node.js kullanarak modern Okuyucu SDK 'Sı ile neler yapabileceğinizi görmek için [Node.js hızlı](./quickstarts/client-libraries.md?pivots=programming-language-nodejs) başlangıcı 'nı görüntüleyin.
* Java ya da Android için Kotlin kullanarak modern Okuyucu SDK 'Sı ile neler yapabileceğinizi öğrenmek için [Android öğreticisini](./how-to-launch-immersive-reader.md) görüntüleyin
* İOS için Swift kullanarak modern Okuyucu SDK 'Sı ile neler yapabileceğinizi öğrenmek için [iOS öğreticisini](./how-to-launch-immersive-reader.md) görüntüleyin
* Python kullanarak modern Okuyucu SDK 'Sı ile neler yapabileceğinizi öğrenmek için [Python öğreticisini](./how-to-launch-immersive-reader.md) görüntüleyin
* [Modern Okuyucu SDK 'sını](https://github.com/microsoft/immersive-reader-sdk) ve [tam ekran okuyucu SDK başvurusunu](./reference.md) keşfet