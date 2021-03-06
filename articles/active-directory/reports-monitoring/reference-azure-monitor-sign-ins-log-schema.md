---
title: Bejelentkezési naplózási séma a Azure Monitorban | Microsoft Docs
description: Az Azure AD bejelentkezési napló sémájának leírása a Azure Monitor való használatra
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5525f2f8ab4ef83ba9c3aeeff945bc9d875600d5
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748664"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Az Azure AD bejelentkezési naplók sémájának értelmezése Azure Monitor

Ez a cikk a Azure Monitor Azure Active Directory (Azure AD) bejelentkezési naplózási sémáját ismerteti. A bejelentkezésekkel kapcsolatos legtöbb információ a `records` objektum *Properties (Tulajdonságok* ) attribútuma alatt található.


```json
{ 
    "time": "2019-03-12T16:02:15.5522137Z", 
    "resourceId": "/tenants/<TENANT ID>/providers/Microsoft.aadiam",
    "operationName": "Sign-in activity", 
    "operationVersion": "1.0", 
    "category": "SignInLogs", 
    "tenantId": "<TENANT ID>", 
    "resultType": "50140", 
    "resultSignature": "None", 
    "resultDescription": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in.", 
    "durationMs": 0, 
    "callerIpAddress": "<CALLER IP ADDRESS>", 
    "correlationId": "a75a10bd-c126-486b-9742-c03110d36262", 
    "identity": "Timothy Perkins", 
    "Level": 4, 
    "location": "US", 
    "properties": 
        {
            "id":"0231f922-93fa-4005-bb11-b344eca03c01",
            "createdDateTime":"2019-03-12T16:02:15.5522137+00:00",
            "userDisplayName":"Timothy Perkins",
            "userPrincipalName":"<USER PRINCIPAL NAME>",
            "userId":"<USER ID>",
            "appId":"<APPLICATION ID>",
            "appDisplayName":"Azure Portal",
            "ipAddress":"<IP ADDRESS>",
            "status":
            {
                "errorCode":50140,
                "failureReason":"This error occurred due to 'Keep me signed in' interrupt when the user was signing-in."
            },
            "clientAppUsed":"Browser",
            "deviceDetail":
            {
                "operatingSystem":"Windows 10",
                "browser":"Chrome 72.0.3626"
            },
            "location":
                {
                    "city":"Bellevue",
                    "state":"Washington",
                    "countryOrRegion":"US",
                    "geoCoordinates":
                    {
                        "latitude":45,
                        "longitude":122
                    }
                },
            "correlationId":"a75a10bd-c126-486b-9742-c03110d36262",
            "conditionalAccessStatus":"notApplied",
            "appliedConditionalAccessPolicies":
            [
                {
                    "id":"ae11ffaa-9879-44e0-972c-7538fd5c4d1a",
                    "displayName":"Hr app access policy",
                    "enforcedGrantControls":
                    [
                        "Mfa"
                    ],
                    "enforcedSessionControls":
                    [
                    ],
                    "result":"notApplied"
                },
                {
                    "id":"b915a70b-2eee-47b6-85b6-ff4f4a66256d",
                    "displayName":"MFA for all but global support access",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"830f27fa-67a8-461f-8791-635b7225caf1",
                    "displayName":"Header Based Application Control",
                    "enforcedGrantControls":["Mfa"],
                    "enforcedSessionControls":[],
                    "result":"notApplied"
                },
                {
                    "id":"8ed8d7f7-0a2e-437b-b512-9e47bed562e6",
                    "displayName":"MFA for everyones",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"52924e0f-798b-4afd-8c42-49055c7d6395",
                    "displayName":"Device compliant",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
             ],
            "isInteractive":true,
            "tokenIssuerType":"AzureAD",
            "authenticationProcessingDetails":[],
            "networkLocationDetails":[],
            "processingTimeInMilliseconds":0,
            "riskDetail":"hidden",
            "riskLevelAggregated":"hidden",
            "riskLevelDuringSignIn":"hidden",
            "riskState":"none",
            "riskEventTypes":[],
            "resourceDisplayName":"windows azure service management api",
            "resourceId":"797f4846-ba00-4fd7-ba43-dac1f8f63013",
            "authenticationMethodsUsed":[]
        }
}
```


## <a name="field-descriptions"></a>Mező leírása

| Mező neve | Leírás |
|------------|-------------|
| Idő | A dátum és az idő (UTC). |
| ResourceId | Ez az érték le van képezve, és nyugodtan figyelmen kívül hagyhatja ezt a mezőt.  |
| OperationName | A bejelentkezések esetében ez az érték mindig *bejelentkezési tevékenység*. |
| OperationVersion | Az ügyfél által kért REST API-verzió. |
| Kategória | A bejelentkezések esetében ez az érték mindig *bejelentkezési*. | 
| TenantId | A naplókhoz társított bérlői GUID. |
| ResultType | A bejelentkezési művelet eredménye lehet *sikeres* vagy *sikertelen*. | 
| ResultSignature | A bejelentkezési művelethez tartozó hibakódot tartalmazza, ha van ilyen. |
| ResultDescription | A bejelentkezési művelet hibájának leírását adja meg. |
| riskDetail | riskDetail | A "okát" a kockázatos felhasználó, a bejelentkezés vagy a kockázatkezelés egy adott állapota mögött. A lehetséges értékek a következők: `none`, `adminGeneratedTemporaryPassword`, `userPerformedSecuredPasswordChange`, `userPerformedSecuredPasswordReset`, `adminConfirmedSigninSafe`, `aiConfirmedSigninSafe`, `userPassedMFADrivenByRiskBasedPolicy`, `adminDismissedAllRiskForUser`, `adminConfirmedSigninCompromised`, `unknownFutureValue`. Az érték `none` azt jelenti, hogy a felhasználó vagy a bejelentkezés eddig nem végez műveletet. <br>**Megjegyzés:** A tulajdonság részleteit egy prémium szintű Azure AD P2-licencre van szükség. Más licencek `hidden`értéket adnak vissza. |
| riskEventTypes | riskEventTypes | A bejelentkezéshez társított kockázati észlelési típusok. A lehetséges értékek a következők: `unlikelyTravel`, `anonymizedIPAddress`, `maliciousIPAddress`, `unfamiliarFeatures`, `malwareInfectedIPAddress`, `suspiciousIPAddress`, `leakedCredentials`, `investigationsThreatIntelligence`, `generic`és `unknownFutureValue`. |
| riskLevelAggregated | riskLevel | Összesített kockázati szint. A lehetséges értékek a következők: `none`, `low`, `medium`, `high`, `hidden`és `unknownFutureValue`. Az érték `hidden` azt jelenti, hogy a felhasználó vagy a bejelentkezés nem volt engedélyezve a Azure AD Identity Protection. **Megjegyzés:** Ennek a tulajdonságnak a részletei csak prémium szintű Azure AD P2 ügyfelek esetében érhetők el. Minden más ügyfél `hidden`lesz visszaadva. |
| riskLevelDuringSignIn | riskLevel | Kockázati szint a bejelentkezés során. A lehetséges értékek a következők: `none`, `low`, `medium`, `high`, `hidden`és `unknownFutureValue`. Az érték `hidden` azt jelenti, hogy a felhasználó vagy a bejelentkezés nem volt engedélyezve a Azure AD Identity Protection. **Megjegyzés:** Ennek a tulajdonságnak a részletei csak prémium szintű Azure AD P2 ügyfelek esetében érhetők el. Minden más ügyfél `hidden`lesz visszaadva. |
| riskState | riskState | A kockázatos felhasználó, a bejelentkezés vagy a kockázat észlelése állapotának jelentése. A lehetséges értékek a következők: `none`, `confirmedSafe`, `remediated`, `dismissed`, `atRisk`, `confirmedCompromised`, `unknownFutureValue`. |
| Átl |  Ez az érték le van képezve, és nyugodtan figyelmen kívül hagyhatja ezt a mezőt. |
| CallerIpAddress | Annak az ügyfélnek az IP-címe, amely a kérelmet elvégezte. | 
| CorrelationId | Az ügyfél által átadott opcionális GUID-azonosító. Ez az érték segít az ügyféloldali műveleteknek a kiszolgálóoldali műveletekkel való összekapcsolásában, és akkor hasznos, ha a szolgáltatásokra kiterjedő naplók követését végzi. |
| Identitáskezelés | A kérelem elküldésekor bemutatott jogkivonat identitása. Felhasználói fiók, rendszerfiók vagy egyszerű szolgáltatásnév lehet. |
| Szint | Az üzenet típusát adja meg. A naplózáshoz mindig *tájékoztatás*szükséges. |
| Földrajzi egység | Megadja a bejelentkezési tevékenység helyét. |
| Tulajdonságok | Felsorolja a bejelentkezésekhez kapcsolódó összes tulajdonságot. További információ: [Microsoft Graph API-referenciák](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). Ez a séma ugyanazokat az attribútumokat használja, mint a bejelentkezési erőforrás, az olvashatóság érdekében.

## <a name="next-steps"></a>Következő lépések

* [Az auditnaplók sémájának értelmezése az Azure Monitorban](reference-azure-monitor-audit-log-schema.md)
* [További információ az Azure diagnosztikai naplóiról](../../azure-monitor/platform/platform-logs-overview.md)
