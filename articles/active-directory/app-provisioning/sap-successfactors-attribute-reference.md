---
title: SAP SuccessFactors-attribútumok referenciája | Microsoft Docs
description: Megtudhatja, hogy a SuccessFactors mely attribútumait támogatja a SuccessFactors-HR-vezérelt kiépítés
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: afb77f2d-5ddd-4c2e-a840-09021b0efef1
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 00b16f969525e7b802c008ba247ecba015875689
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77522356"
---
# <a name="sap-successfactors-attribute-reference"></a>SAP SuccessFactors-attribútumok referenciája

## <a name="supported-successfactors-entities-and-attributes"></a>Támogatott SuccessFactors-entitások és-attribútumok

Az alábbi táblázat a következő két üzembe helyezési alkalmazás által támogatott SuccessFactors-attribútumok listáját rögzíti: 
* [SuccessFactors Active Directory a felhasználók üzembe helyezése](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors az Azure AD-felhasználók üzembe helyezéséhez](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | SuccessFactors entitás                  | SuccessFactors attribútum     | Művelet típusa |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | Olvasás           |
| 2  | PerPerson                              | Számú personid                     | Olvasás           |
| 3  | PerPerson                              | perPersonUuid                | Olvasás           |
| 4  | PerPersonal                            | displayName                  | Olvasás           |
| 5  | PerPersonal                            | firstName                    | Olvasás           |
| 6  | PerPersonal                            | gender                       | Olvasás           |
| 7  | PerPersonal                            | lastName                     | Olvasás           |
| 8  | PerPersonal                            | middleName                   | Olvasás           |
| 9  | PerPersonal                            | preferredName                | Olvasás           |
| 10 | Felhasználó                                   | addressLine1                 | Olvasás           |
| 11 | Felhasználó                                   | addressLine2                 | Olvasás           |
| 12 | Felhasználó                                   | addressLIne3                 | Olvasás           |
| 13 | Felhasználó                                   | businessPhone                | Olvasás           |
| 14 | Felhasználó                                   | cellPhone                    | Olvasás           |
| 15 | Felhasználó                                   | city                         | Olvasás           |
| 16 | Felhasználó                                   | ország                      | Olvasás           |
| 17 | Felhasználó                                   | custom01                     | Olvasás           |
| 18 | Felhasználó                                   | custom02                     | Olvasás           |
| 19 | Felhasználó                                   | custom03                     | Olvasás           |
| 20 | Felhasználó                                   | custom04                     | Olvasás           |
| 21 | Felhasználó                                   | custom05                     | Olvasás           |
| 22 | Felhasználó                                   | custom06                     | Olvasás           |
| 23 | Felhasználó                                   | custom07                     | Olvasás           |
| 24 | Felhasználó                                   | custom08                     | Olvasás           |
| 25 | Felhasználó                                   | custom09                     | Olvasás           |
| 26 | Felhasználó                                   | custom10                     | Olvasás           |
| 27 | Felhasználó                                   | custom11                     | Olvasás           |
| 28 | Felhasználó                                   | custom12                     | Olvasás           |
| 29 | Felhasználó                                   | custom13                     | Olvasás           |
| 30 | Felhasználó                                   | custom14                     | Olvasás           |
| 31 | Felhasználó                                   | empId                        | Olvasás           |
| 32 | Felhasználó                                   | homePhone                    | Olvasás           |
| 33 | Felhasználó                                   | jobFamily                    | Olvasás           |
| 34 | Felhasználó                                   | Becenév                     | Olvasás           |
| 35 | Felhasználó                                   | state                        | Olvasás           |
| 36 | Felhasználó                                   | timeZone                     | Olvasás           |
| 37 | Felhasználó                                   | felhasználónév                     | Olvasás           |
| 38 | Felhasználó                                   | Irányítószám                      | Olvasás           |
| 39 | PerPhone                               | areaCode                     | Olvasás           |
| 40 | PerPhone                               | Országhívószám                  | Olvasás           |
| 41 | PerPhone                               | kiterjesztés                    | Olvasás           |
| 42 | PerPhone                               | Telefonszám                  | Olvasás           |
| 43 | PerPhone                               | phoneType                    | Olvasás           |
| 44 | PerEmail                               | emailAddress                 | Olvasás, írás    |
| 45 | PerEmail                               | emailType                    | Olvasás           |
| 46 | EmpEmployment                          | firstDateWorked              | Olvasás           |
| 47 | EmpEmployment                          | lastDateWorked               | Olvasás           |
| 48 | EmpEmployment                          | userId                       | Olvasás           |
| 49 | EmpEmployment                          | isContingentWorker           | Olvasás           |
| 50 | EmpJob                                 | countryOfCompany             | Olvasás           |
| 51 | EmpJob                                 | emplStatus                   | Olvasás           |
| 52 | EmpJob                                 | endDate                      | Olvasás           |
| 53 | EmpJob                                 | startDate                    | Olvasás           |
| 54 | EmpJob                                 | Beosztás                     | Olvasás           |
| 55 | EmpJob                                 | Pozíció                     | Olvasás           |
| 65 | EmpJob                                 | customString13               | Olvasás           |
| 56 | EmpJob                                 | managerId                    | Olvasás           |
| 57 | EmpJob\.részleghez                   | Részleghez                 | Olvasás           |
| 58 | EmpJob\.részleghez                   | businessUnitId               | Olvasás           |
| 59 | EmpJob\.vállalat                        | Vállalati                      | Olvasás           |
| 60 | EmpJob\.vállalat                        | companyId                    | Olvasás           |
| 61 | EmpJob\.cég\.CountryOfRegistration | twoCharCountryCode           | Olvasás           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | Olvasás           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | Olvasás           |
| 64 | EmpJob\.CostCenter                     | costCenterDescription        | Olvasás           |
| 65 | EmpJob\.részleg                     | Szervezeti egység                   | Olvasás           |
| 66 | EmpJob\.részleg                     | departmentId                 | Olvasás           |
| 67 | EmpJob\.divízió                       | osztály                     | Olvasás           |
| 68 | EmpJob\.divízió                       | divisionId                   | Olvasás           |
| 69 | EmpJob\.JobCode                        | jobCode                      | Olvasás           |
| 70 | EmpJob\.JobCode                        | jobCodeId                    | Olvasás           |
| 71 | EmpJob\.helye                       | LocationName                 | Olvasás           |
| 72 | EmpJob\.helye                       | officeLocationAddress        | Olvasás           |
| 73 | EmpJob\.helye                       | officeLocationCity           | Olvasás           |
| 74 | EmpJob\.helye                       | officeLocationCustomString4  | Olvasás           |
| 75 | EmpJob\.helye                       | officeLocationZipCode        | Olvasás           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | Olvasás           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Olvasás           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Olvasás           |


## <a name="default-attribute-mapping"></a>Alapértelmezett attribútumok leképezése

Az alábbi táblázat az alapértelmezett attribútum-hozzárendelést tartalmazza a fent felsorolt SuccessFactors-attribútumok és az AD/Azure AD-attribútumok között. Az Azure AD kiépítési alkalmazás "leképezése" paneljén módosíthatja ezt az alapértelmezett leképezést, hogy tartalmazza a fenti listából származó attribútumokat is. 

| \# | SuccessFactors entitás                  | SuccessFactors attribútum | Alapértelmezett AD/Azure AD-attribútumok leképezése   | Feldolgozási Megjegyzés                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | employeeId                              | Egyező attribútumként használatos                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[nincs leképezve a forrás-szerkesztőpontként használt \-\] | A kezdeti szinkronizálás során a kiépítési szolgáltatás összekapcsolja a personUuid a meglévő objectGuid\.  |
| 3  | PerPersonal                            | displayName              | displayName                             | NA                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | NA                                                                                           |
| 5  | PerPersonal                            | lastName                 | sorozatszám                                      | NA                                                                                           |
| 6  | Felhasználó                                   | addressLine1             | streetAddress                           | NA                                                                                           |
| 7  | Felhasználó                                   | city                     | l                                       | NA                                                                                           |
| 8  | Felhasználó                                   | ország                  | CO                                      | NA                                                                                           |
| 9  | Felhasználó                                   | state                    | St                                      | NA                                                                                           |
| 10 | Felhasználó                                   | felhasználónév                 | samAccountName                          | NA                                                                                           |
| 11 | Felhasználó                                   | Irányítószám                  | Irányítószám                              | NA                                                                                           |
| 12 | PerEmail                               | emailAddress             | mail                                    | NA                                                                                           |
| 13 | EmpJob                                 | Beosztás                 | Cím                                   | NA                                                                                           |
| 14 | EmpJob                                 | managerId                | kezelő                                 | NA                                                                                           |
| 15 | EmpJob\.cég\.CountryOfRegistration | twoCharCountryCode       | c                                       | NA                                                                                           |
| 16 | EmpJob\.részleg                     | Szervezeti egység               | Szervezeti egység                              | NA                                                                                           |
| 17 | EmpJob\.divízió                       | osztály                 | Vállalati                                 | NA                                                                                           |
| 18 | EmpJob\.helye                       | officeLocationAddress    | streetAddress                           | NA                                                                                           |
| 19 | EmpJob\.helye                       | officeLocationZipCode    | Irányítószám                              | NA                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | Ha a activeEmploymentsCount = 0, tiltsa le a account\.                                           |

