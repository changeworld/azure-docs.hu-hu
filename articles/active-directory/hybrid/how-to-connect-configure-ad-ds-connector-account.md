---
title: 'Azure AD Connect: AD DS-összekötő fiók engedélyeinek konfigurálása | Microsoft Docs'
description: Ez a dokumentum részletesen ismerteti, hogyan kell konfigurálni az AD DS-összekötő fiókját az új ADSyncConfig PowerShell-modullal.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeb80c3a94e63a886e4a16c0b8fa445b2a8a34e4
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515816"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect: AD DS-összekötő fiók engedélyeinek konfigurálása 

A [ADSyncConfig. psm1](reference-connect-adsyncconfig.md) nevű PowerShell-modult a build 1.1.880.0 (2018 augusztusában kiadott verzió) vezette be, amely tartalmazza a parancsmagok gyűjteményét, amely segítséget nyújt a Azure ad Connect telepítés helyes Active Directory engedélyeinek konfigurálásához. 

## <a name="overview"></a>Áttekintés 
A következő PowerShell-parancsmagok használhatók az AD DS Connector-fiók Active Directory engedélyeinek beállítására, minden egyes, a Azure AD Connect-ban engedélyezhető szolgáltatáshoz. A problémák megelőzése érdekében elő kell készítenie Active Directoryi engedélyeket, amikor az erdőhöz való csatlakozáshoz egyéni tartományi fiókkal szeretné telepíteni Azure AD Connect. Ez a ADSyncConfig modul az engedélyek konfigurálására is használható az Azure AD Connect telepítése után.

![az AD DS-fiók áttekintése](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

Azure AD Connect expressz telepítéshez egy automatikusan létrehozott fiók (MSOL_nnnnnnnnnn) jön létre Active Directory az összes szükséges engedélyekkel, így nem kell ezt a ADSyncConfig-modult használnia, hacsak nem rendelkezik letiltott engedélyekkel a szervezeti egységek vagy az Azure AD-vel szinkronizálni kívánt Active Directory-objektumok öröklése. 
 
### <a name="permissions-summary"></a>Engedélyek összegzése 
Az alábbi táblázat az AD-objektumokhoz szükséges engedélyek összegzését tartalmazza: 

| Szolgáltatás | Engedélyek |
| --- | --- |
| MS-DS-ConsistencyGuid funkció |Olvasási és írási engedélyek a tervezési fogalmakban dokumentált ms-DS-ConsistencyGuid attribútumhoz [– MS-DS-ConsistencyGuid as SourceAnchor használatával](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Jelszó kivonatának szinkronizálása |<li>Címtárbeli módosítások replikálása</li>  <li>A címtár összes módosításának replikálása |
| Hibrid Exchange-telepítés |Olvasási és írási engedélyek a felhasználók, csoportok és névjegyek [Exchange hibrid visszaírási](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) dokumentált attribútumaihoz. |
| Exchange-levelezés nyilvános mappája |Olvasási engedélyek a nyilvános mappák Exchange- [levelezés nyilvános mappájában](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) dokumentált attribútumokhoz. | 
| Jelszóvisszaíró |Olvasási és írási engedélyek a felhasználók [jelszavas kezelésének megkezdése](../authentication/howto-sspr-writeback.md) című dokumentumban ismertetett attribútumokhoz. |
| Eszközvisszaíró |Olvasási és írási engedélyek a Device [visszaírási](how-to-connect-device-writeback.md)-ben dokumentált eszközök objektumaihoz és tárolóhoz. |
| Group writeback (Csoportvisszaíró) |Csoport objektumok olvasása, létrehozása, frissítése és törlése a szinkronizált **Office 365-csoportokhoz**.  További információ: [Group visszaírási](how-to-connect-preview.md#group-writeback).|

## <a name="using-the-adsyncconfig-powershell-module"></a>A ADSyncConfig PowerShell-modul használata 
A ADSyncConfig modulhoz a [AD DS Távoli kiszolgálófelügyelet eszközei (RSAT) szükséges,](https://docs.microsoft.com/windows-server/remote/remote-server-administration-tools) mivel az AD DS PowerShell-modultól és-eszközöktől függ. Az RSAT AD DS telepítéséhez nyisson meg egy Windows PowerShell-ablakot a "Futtatás rendszergazdaként" és a végrehajtás: 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![Konfigurálás](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>A **C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\ADSyncConfig.psm1** olyan tartományvezérlőre is másolhatja, amelyen már telepítve van az RSAT AD DS, és a PowerShell-modult használja.

A ADSyncConfig használatának megkezdéséhez be kell töltenie a modult egy Windows PowerShell-ablakban: 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

A modulban található összes parancsmag vizsgálatához írja be a következőt:  

``` powershell
Get-Command -Module AdSyncConfig  
```

![Jelölőnégyzet](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

Mindegyik parancsmag ugyanazokkal a paraméterekkel rendelkezik, mint a AD DS Connector-fiók és egy AdminSDHolder-kapcsoló bevitele. Az AD DS Connector-fiók megadásához megadhatja a fiók nevét és tartományát, vagy csak a fiók megkülönböztető nevét (DN),

emelkedés pl

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <ADAccountName> -ADConnectorAccountDomain <ADDomainName>
```

Vagy

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <ADAccountDN>
```

Ügyeljen arra, hogy a környezetének megfelelő értékekkel helyettesítse `<ADAccountName>`, `<ADDomainName>` és `<ADAccountDN>`.

Ha nem szeretné módosítani a AdminSDHolder tároló engedélyeit, használja a kapcsolót `-SkipAdminSdHolders`. 

Alapértelmezés szerint az összes beállított engedély-parancsmag megpróbálja beállítani AD DS engedélyeket az erdő minden tartományának gyökerére, ami azt jelenti, hogy a PowerShell-munkamenetet futtató felhasználónak tartományi rendszergazdai jogosultságokra van szüksége az erdő minden tartományához.  Ennek a követelménynek a miatt ajánlott a vállalati rendszergazda használata az erdő gyökeréből. Ha a Azure AD Connect üzemelő példány több AD DS összekötővel rendelkezik, akkor minden olyan erdőben futtatnia kell ugyanazt a parancsmagot, amely AD DS-összekötővel rendelkezik. 

Az engedélyeket beállíthatja egy adott szervezeti egységre vagy AD DS objektumra is, ha a (z) `-ADobjectDN` paramétert, majd azt a célobjektum megkülönböztető nevét adja meg, amelyet az engedélyek beállításához használni kíván. Target ADobjectDN használata esetén a parancsmag csak az objektumra vonatkozó engedélyeket állítja be, nem pedig a tartományi gyökér-vagy AdminSDHolder-tárolón. Ez a paraméter akkor lehet hasznos, ha olyan szervezeti egységekkel vagy AD DS objektumokkal rendelkezik, amelyek engedélyeinek öröklése le van tiltva (lásd: AD DS objektumok megkeresése az engedélyek öröklésével letiltva 

Ezek a közös paraméterek alóli kivételek a `Set-ADSyncRestrictedPermissions` parancsmag, amely a AD DS-összekötő fiók engedélyeinek beállítására szolgál, és a `Set-ADSyncPasswordHashSyncPermissions` parancsmag, mivel a jelszó-kivonatoló szinkronizáláshoz szükséges engedélyek csak a tartomány gyökerére vannak beállítva. , ezért ez a parancsmag nem tartalmazza a `-ObjectDN` vagy `-SkipAdminSdHolders` paramétereket.

### <a name="determine-your-ad-ds-connector-account"></a>Az AD DS-összekötő fiókjának meghatározása 
Ha a Azure AD Connect már telepítve van, és szeretné megtekinteni, hogy mi az a Azure AD Connect által jelenleg használt AD DS Connector-fiók, akkor a következő parancsmagot hajthatja végre: 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>Az engedélyek öröklésével letiltott AD DS objektumok megkeresése 
Ha szeretné megtekinteni, hogy van-e olyan AD DS objektum, amelynél az öröklési öröklődés le van tiltva, akkor a következőket futtathatja: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
Alapértelmezés szerint ez a parancsmag csak a letiltott örökléssel rendelkező szervezeti egységeket fogja keresni, de más AD DS objektumosztály is megadható a `-ObjectClass` paraméterben, vagy a "*" minden objektumosztály esetében az alábbi módon adható meg: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>Objektum AD DSának megtekintése 
Az alábbi parancsmag segítségével megtekintheti a Active Directory objektumhoz jelenleg beállított engedélyek listáját a DistinguishedName megadásával: 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>AD DS-összekötő fiók engedélyeinek konfigurálása 
 
### <a name="configure-basic-read-only-permissions"></a>Alapszintű írásvédett engedélyek konfigurálása 
Ha nem Azure AD Connect funkciót használ, a következő parancs futtatásával állíthatja be az AD DS Connector-fiók alapszintű írásvédett engedélyeit: 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Vagy 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


Ez a parancsmag a következő engedélyeket fogja beállítani: 
 

|Type (Típus) |Név |Hozzáférés |A következőkre vonatkozik| 
|-----|-----|-----|-----|
|Engedélyezés |AD DS-összekötő fiókja |Az összes tulajdonság olvasása |Leszármazott eszköz objektumai| 
|Engedélyezés |AD DS-összekötő fiókja|Az összes tulajdonság olvasása |Leszármazott InetOrgPerson objektumok| 
|Engedélyezés |AD DS-összekötő fiókja |Az összes tulajdonság olvasása |Leszármazott számítógép-objektumok| 
|Engedélyezés |AD DS-összekötő fiókja |Az összes tulajdonság olvasása |Leszármazott foreignSecurityPrincipal objektumok| 
|Engedélyezés |AD DS-összekötő fiókja |Az összes tulajdonság olvasása |Leszármazott csoport objektumai| 
|Engedélyezés |AD DS-összekötő fiókja |Az összes tulajdonság olvasása |Leszármazott felhasználói objektumok| 
|Engedélyezés |AD DS-összekötő fiókja |Az összes tulajdonság olvasása |Leszármazott kapcsolattartási objektumok| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>MS-DS-konzisztencia – GUID engedélyek konfigurálása 
Ha az MS-DS-konzisztencia-GUID attribútumot használja a forrás-horgonyként (más néven "az Azure kezelése a forrás-horgonyt a számomra" beállítással), akkor a következő futtatásával állíthatja be a AD DS-összekötő fiók engedélyeit: 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

Vagy 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Ez a parancsmag a következő engedélyeket fogja beállítani: 

|Type (Típus) |Név |Hozzáférés |A következőkre vonatkozik|
|-----|-----|-----|-----| 
|Engedélyezés|AD DS-összekötő fiókja|Olvasási/írási tulajdonság|Leszármazott felhasználói objektumok|

### <a name="permissions-for-password-hash-synchronization"></a>Jelszó-kivonat szinkronizálásának engedélyei 
A AD DS Connector-fiók engedélyeinek beállításához a jelszó-kivonatoló szinkronizálás használatakor futtassa a következőt: 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


Vagy 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

Ez a parancsmag a következő engedélyeket fogja beállítani: 

|Type (Típus) |Név |Hozzáférés |A következőkre vonatkozik|
|-----|-----|-----|-----| 
|Engedélyezés |AD DS-összekötő fiókja |Címtárbeli módosítások replikálása |Csak ez az objektum (tartományi gyökér)| 
|Engedélyezés |AD DS-összekötő fiókja |A címtár összes módosításának replikálása |Csak ez az objektum (tartományi gyökér)| 
  
### <a name="permissions-for-password-writeback"></a>Jelszó-visszaírási engedélyei 
Az AD DS Connector-fiók engedélyeinek beállítása jelszó-visszaírási használatakor futtassa a következőt: 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Vagy

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Ez a parancsmag a következő engedélyeket fogja beállítani: 

|Type (Típus) |Név |Hozzáférés |A következőkre vonatkozik|
|-----|-----|-----|-----| 
|Engedélyezés |AD DS-összekötő fiókja |Jelszó alaphelyzetbe állítása |Leszármazott felhasználói objektumok| 
|Engedélyezés |AD DS-összekötő fiókja |Írási tulajdonság lockoutTime |Leszármazott felhasználói objektumok| 
|Engedélyezés |AD DS-összekötő fiókja |Írási tulajdonság pwdLastSet |Leszármazott felhasználói objektumok| 

### <a name="permissions-for-group-writeback"></a>Engedélyek a csoport visszaírási 
Az AD DS Connector-fiók engedélyeinek beállításához a visszaírási csoport használatakor futtassa a következőt: 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
Vagy 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
Ez a parancsmag a következő engedélyeket fogja beállítani: 

|Type (Típus) |Név |Hozzáférés |A következőkre vonatkozik|
|-----|-----|-----|-----| 
|Engedélyezés |AD DS-összekötő fiókja |Általános olvasási/írási |Az objektumtípus-csoport és alobjektumok összes attribútuma| 
|Engedélyezés |AD DS-összekötő fiókja |Gyermekobjektum létrehozása/törlése |Az objektumtípus-csoport és alobjektumok összes attribútuma| 
|Engedélyezés |AD DS-összekötő fiókja |Fa objektumok törlése/törlése|Az objektumtípus-csoport és alobjektumok összes attribútuma|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Az Exchange hibrid üzembe helyezéséhez szükséges engedélyek 
Ha az Exchange Hybrid-telepítés használatakor az AD DS-összekötő fiók engedélyeit szeretné beállítani, futtassa a következőt: 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Vagy 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Ez a parancsmag a következő engedélyeket fogja beállítani:  
 

|Type (Típus) |Név |Hozzáférés |A következőkre vonatkozik|
|-----|-----|-----|-----| 
|Engedélyezés |AD DS-összekötő fiókja |Az összes tulajdonság olvasása/írása |Leszármazott felhasználói objektumok| 
|Engedélyezés |AD DS-összekötő fiókja |Az összes tulajdonság olvasása/írása |Leszármazott InetOrgPerson objektumok| 
|Engedélyezés |AD DS-összekötő fiókja |Az összes tulajdonság olvasása/írása |Leszármazott csoport objektumai| 
|Engedélyezés |AD DS-összekötő fiókja |Az összes tulajdonság olvasása/írása |Leszármazott kapcsolattartási objektumok| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Exchange-levelezés nyilvános mappáihoz tartozó engedélyek (előzetes verzió) 
Ha az Exchange-levelezés nyilvános mappái szolgáltatás használatakor az AD DS-összekötő fiók engedélyeit szeretné beállítani, futtassa a következőt: 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Vagy 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Ez a parancsmag a következő engedélyeket fogja beállítani: 

|Type (Típus) |Név |Hozzáférés |A következőkre vonatkozik|
|-----|-----|-----|-----| 
|Engedélyezés |AD DS-összekötő fiókja |Az összes tulajdonság olvasása |Leszármazott PublicFolder objektumok| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>Engedélyek korlátozása az AD DS-összekötő fiókján 
Ez a PowerShell-parancsfájl megszigorítja a paraméterként megadott AD Connector-fiók engedélyeit. Az engedélyek szigorítása a következő lépésekkel jár: 

- Öröklés letiltása a megadott objektumon 
- Távolítsa el az összes Ace-t az adott objektumra vonatkozóan, kivéve az önmagát, mert az alapértelmezett engedélyek érintetlenek maradnak, amikor önmagát kívánja megőrizni. 
 
  A-ADConnectorAccountDN paraméter az az AD-fiók, amelynek engedélyeit meg kell szigorítani. Ez általában az AD DS-összekötőn konfigurált MSOL_nnnnnnnnnnnn tartományi fiók (lásd: az AD DS Connector-fiók meghatározása). A-hitelesítőadat paraméter szükséges ahhoz a rendszergazdai fiók megadásához, amely rendelkezik a szükséges jogosultságokkal a cél AD objektumra vonatkozó Active Directory engedélyek korlátozásához. Ez általában a vállalati vagy tartományi rendszergazda.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
Például: 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

Ez a parancsmag a következő engedélyeket fogja beállítani: 

|Type (Típus) |Név |Hozzáférés |A következőkre vonatkozik|
|-----|-----|-----|-----| 
|Engedélyezés |RENDSZER |Teljes hozzáférés |Ez az objektum 
|Engedélyezés |Vállalati rendszergazdák |Teljes hozzáférés |Ez az objektum 
|Engedélyezés |Tartománygazdák |Teljes hozzáférés |Ez az objektum 
|Engedélyezés |Rendszergazdák |Teljes hozzáférés |Ez az objektum 
|Engedélyezés |Vállalati tartományvezérlők |Tartalom listázása |Ez az objektum 
|Engedélyezés |Vállalati tartományvezérlők |Az összes tulajdonság olvasása |Ez az objektum 
|Engedélyezés |Vállalati tartományvezérlők |Olvasási engedélyek |Ez az objektum 
|Engedélyezés |Hitelesített felhasználók |Tartalom listázása |Ez az objektum 
|Engedélyezés |Hitelesített felhasználók |Az összes tulajdonság olvasása |Ez az objektum 
|Engedélyezés |Hitelesített felhasználók |Olvasási engedélyek |Ez az objektum 

## <a name="next-steps"></a>Következő lépések
- [Azure AD Connect: fiókok és engedélyek](reference-connect-accounts-permissions.md)
- [Expressz telepítés](how-to-connect-install-express.md)
- [Egyéni telepítés](how-to-connect-install-custom.md)
- [ADSyncConfig-hivatkozás](reference-connect-adsyncconfig.md)

