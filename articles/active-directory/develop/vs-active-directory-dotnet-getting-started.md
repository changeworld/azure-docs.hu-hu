---
title: Ismerkedés az Azure AD-vel a .NET MVC-projektekben | Azure
description: A .NET MVC-projektek Azure Active Directory használatának első lépései az Azure AD-vel való csatlakozás vagy a Visual Studio Connected Services használatával való létrehozás után
author: ghogen
manager: jillfra
ms.assetid: 1c8b6a58-5144-4965-a905-625b9ee7b22b
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: eae649a4de88373ee79e49ecb7d5f14564a3054b
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159488"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Első lépések Azure Active Directory (ASP.NET MVC-projektekkel)

> [!div class="op_single_selector"]
> - [Első lépések](vs-active-directory-dotnet-getting-started.md)
> - [mi történt](vs-active-directory-dotnet-what-happened.md)

Ez a cikk további útmutatást nyújt, miután hozzáadta Active Directory egy ASP.NET MVC-projekthez a Visual Studio **project > Connected Services** parancsával. Ha még nem adta hozzá a szolgáltatást a projekthez, bármikor megteheti.

Megtudhatja [, mi történt az MVC-projekttel?](vs-active-directory-dotnet-what-happened.md) a projektben a csatlakoztatott szolgáltatás hozzáadásakor végrehajtott módosítások.

## <a name="requiring-authentication-to-access-controllers"></a>Hitelesítés megkövetelése a hozzáférés-vezérléshez

A projekt összes vezérlőjét a `[Authorize]` attribútummal díszítették. Ez az attribútum megköveteli a felhasználó hitelesítését a vezérlők elérése előtt. Annak engedélyezéséhez, hogy a vezérlő névtelenül legyen elérhető, távolítsa el ezt az attribútumot a vezérlőből. Ha az engedélyeket részletesebben szeretné beállítani, alkalmazza az attribútumot minden olyan metódusra, amely az engedélyezést igényli a vezérlő osztályra való alkalmazása helyett.

## <a name="adding-signin--signout-controls"></a>Bejelentkezési-/kijelentkezés-vezérlők hozzáadása

A bejelentkezési/kijelentkezés vezérlőnek a nézethez való hozzáadásához a `_LoginPartial.cshtml` részleges nézettel adhatja hozzá a funkciókat az egyik nézethez. Íme egy példa a standard `_Layout.cshtml` nézethez hozzáadott funkciókra. (Jegyezze fel az utolsó elemet a div-összecsukási osztályhoz:

```html
<!DOCTYPE html>
 <html>
 <head>
     <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@ViewBag.Title - My ASP.NET Application</title>
    @Styles.Render("~/Content/css")
    @Scripts.Render("~/bundles/modernizr")
</head>
<body>
    <div class="navbar navbar-inverse navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
                    <li>@Html.ActionLink("About", "About", "Home")</li>
                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
                </ul>
                @Html.Partial("_LoginPartial")
            </div>
        </div>
    </div>
    <div class="container body-content">
        @RenderBody() 
        <hr />
        <footer>
            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p>
        </footer>
    </div>
    @Scripts.Render("~/bundles/jquery")
    @Scripts.Render("~/bundles/bootstrap")
    @RenderSection("scripts", required: false)
</body>
</html>
```

## <a name="next-steps"></a>Következő lépések

- [Hitelesítési forgatókönyvek Azure Active Directory](authentication-scenarios.md)
- [Bejelentkezés felvétele a Microsofttal egy ASP.NET-webalkalmazásba](quickstart-v2-aspnet-webapp.md)
