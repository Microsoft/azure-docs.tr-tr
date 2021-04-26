---
title: Power BI kiracı kaydetme ve tarama (Önizleme)
description: Power BI kiracıyı kaydetmek ve taramak için Azure purview portalını nasıl kullanacağınızı öğrenin.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: 8fb4c797df7961726ca785a56a6ab25807999842
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600871"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>Power BI kiracı kaydetme ve tarama (Önizleme)

Bu makalede, Azure purview Portal 'ın Power BI kiracıyı kaydetmek ve taramak için nasıl kullanılacağı gösterilmektedir.

> [!Note]
> Purview örneği ve Power BI kiracısı aynı Azure kiracısında ise, Power BI kiracının taramasını ayarlamak için yalnızca yönetilen kimlik (MSI) kimlik doğrulamasını kullanabilirsiniz. 

## <a name="create-a-security-group-for-permissions"></a>İzinler için bir güvenlik grubu oluşturma

Kimlik doğrulaması ayarlamak için bir güvenlik grubu oluşturun ve bu gruba purview yönetilen kimliğini ekleyin.

1. [Azure Portal](https://portal.azure.com), **Azure Active Directory** için arama yapın.
1. Azure Active Directory yeni bir güvenlik grubu oluşturun, aşağıdaki [temel bir grup oluşturun ve Azure Active Directory kullanarak Üyeler ekleyin](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    > [!Tip]
    > Kullanmak istediğiniz bir güvenlik grubunuz varsa, bu adımı atlayabilirsiniz.

1. **Grup türü** olarak **güvenlik** ' i seçin.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Güvenlik grubu türü":::

1. Purview yönetilen kimliğinizi bu güvenlik grubuna ekleyin. **Üyeler**' i seçin ve **+ üye Ekle**' yi seçin.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Kataloğun yönetilen örneğini grubuna ekleyin.":::

1. Purview yönetilen kimliğinizi arayın ve seçin.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Arayarak katalog ekleme":::

    Eklendiğini gösteren bir başarı bildirimi görmeniz gerekir.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Katalog MSI başarısı ekleme":::

## <a name="associate-the-security-group-with-the-tenant"></a>Güvenlik grubunu kiracı ile ilişkilendir

1. [Power BI yönetici portalında](https://app.powerbi.com/admin-portal/tenantSettings)oturum açın.
1. **Kiracı ayarları** sayfasını seçin.

    > [!Important]
    > Kiracı ayarları sayfasını görmek için bir Power BI yöneticisi olmanız gerekir.

1. **Yönetim API 'si ayarları**,  >  **hizmet sorumluların salt okunurdur Power BI yönetici API 'leri kullanmasına izin ver (Önizleme)** seçeneğini belirleyin.
1. **Belirli güvenlik gruplarını** seçin.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Hizmet sorumluların salt okunurdur Power BI yönetici API izinleri alma ile nasıl izin verileceğini gösteren resim":::

    > [!Caution]
    > Oluşturduğunuz güvenlik grubuna (örneğin, bir üye olarak yönetilen kimlik bilgileri) salt okunurdur Power BI yönetici API 'Lerini kullanmak için izin vermek istiyorsanız, bu Kiracıdaki tüm Power BI yapıtlarınız için meta verilere (ör. Pano ve rapor adları, sahipler, açıklamalar vb.) erişmesine izin verin. Meta veriler Azure purview 'a çekildikten sonra, Power BI izinleri değil, bu meta verileri kimlerin görebileceğini saptayın.

    > [!Note]
    > Güvenlik grubunu geliştirici ayarlarından kaldırabilirsiniz, ancak daha önce ayıklanan meta veriler, purview hesabından kaldırılmaz. İsterseniz bunu ayrı olarak silebilirsiniz.

## <a name="register-your-power-bi-and-set-up-a-scan"></a>Power BI kaydetme ve tarama kurma

Artık Power BI kiracınızın yönetim API 'sine bağlanmak için, yönetilen kimlik yönetimli kimlik izinlerini vermiş olduğunuza göre, Azure purview Studio 'dan taramanızı ayarlayabilirsiniz.

1. Sol gezinti bölmesinde **kaynakları** seçin.

1. Ardından **Kaydet**’i seçin.

    Veri kaynağınız olarak **Power BI** seçin.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="Seçebileceğiniz veri kaynaklarının listesini gösteren resim":::

3. Power BI örneğine kolay bir ad verin.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Power BI veri kaynağını gösteren resim-kolay ad":::

    Ad 3-63 karakter uzunluğunda olmalıdır ve yalnızca harf, sayı, alt çizgi ve kısa çizgi içermelidir.  Boşluklara izin verilmez.

    Varsayılan olarak, sistem aynı Azure aboneliğinde bulunan Power BI kiracıyı bulur.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Power BI veri kaynağı kaydedildi":::

    > [!Note]
    > Power BI için veri kaynağı kaydına ve taramaya yalnızca bir örnek için izin verilir.


4. Taramaya bir ad verin. Ardından kişisel çalışma alanlarını dahil etme veya dışlama seçeneğini belirleyin. Desteklenen tek kimlik doğrulama yönteminin **yönetilen kimlik** olduğunu unutmayın.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Power BI tarama kurulumunu gösteren resim":::

    > [!Note]
    > * Kişisel çalışma alanını dahil etmek veya hariç tutmak için taramanın yapılandırmasını değiştirmek, PowerBI kaynağının tam taramasını tetikler
    > * Tarama adı 3-63 karakter uzunluğunda olmalıdır ve yalnızca harf, sayı, alt çizgi ve kısa çizgi içermelidir. Boşluklara izin verilmez.

5. Bir tarama tetikleyicisi ayarlayın. Seçenekleriniz **her 7 günde** bir ve **30 günde** **bir**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Tetikleyici görüntüsünü Tara":::

6. **Yeni taramayı gözden geçir** sayfasında, taramayı başlatmak için **Kaydet ve Çalıştır** ' ı seçin.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Power BI ekran görüntüsünü Kaydet ve Çalıştır":::

## <a name="next-steps"></a>Sonraki adımlar

- [Azure purview veri kataloğuna gözatın](how-to-browse-catalog.md)
- [Azure purview Veri Kataloğu 'Nda arama](how-to-search-catalog.md)
