# VBZS Software License Agreement

Copyright © 2026 Van Beek Zakelijke Software

All rights reserved.

## 1. Scope

This Software License Agreement applies to the version of the VBZS FileMaker Plug-in ("VBZS") with which this license is distributed.

VBZS is proprietary, closed-source software developed and distributed by Van Beek Zakelijke Software.

The software is licensed, not sold.

Installation, registration or continued use of VBZS constitutes acceptance of this Software License Agreement.

Earlier versions of VBZS that were distributed under different license terms remain subject to the license terms distributed with those versions. This agreement does not retroactively change the license terms of earlier releases.

## 2. NLdigital Terms 2025

The NLdigital Terms 2025, as used by Van Beek Zakelijke Software, apply to the agreement between Van Beek Zakelijke Software and the licensee in addition to this Software License Agreement.

The licensee must be given a reasonable opportunity to review the applicable NLdigital Terms 2025 before or when entering into the agreement.

This Software License Agreement contains the specific terms governing the licensing and permitted use of VBZS.

Where this Software License Agreement does not contain a specific provision, the applicable NLdigital Terms 2025 apply.

In the event of an inconsistency between this Software License Agreement and the NLdigital Terms 2025, the order of precedence follows the applicable agreement between Van Beek Zakelijke Software and the licensee and the provisions of the NLdigital Terms 2025.

## 3. Intellectual property

VBZS, its source code, binaries, documentation, design, functions, license mechanism and associated materials remain the intellectual property of Van Beek Zakelijke Software, except for third-party components that are distributed under their respective licenses.

No ownership rights are transferred to the licensee.

The source code of VBZS is not included with the license.

Except where expressly permitted by this agreement or required by applicable law, the licensee may not:

- reproduce or distribute VBZS except as expressly permitted by the applicable license type;
- sell, rent, sublicense or otherwise transfer a VBZS license to another party;
- modify or tamper with a VBZS license code;
- remove or alter copyright, ownership or license notices;
- circumvent or attempt to circumvent the VBZS license mechanism;
- use a license issued to another licensee;
- make private license-signing credentials available to any third party.

Nothing in this agreement restricts rights that cannot legally be excluded under applicable law.

## 4. License code

A VBZS license is represented by a digitally signed license code.

The license code contains protected license information including:

- the licensee;
- the country;
- the order number;
- the license date;
- the license generation;
- the license type;
- the number of licensed seats where applicable.

The license code is issued to the licensee identified in that license code.

The license code may not be altered.

Changing protected license information invalidates the license unless a new valid license code is issued by Van Beek Zakelijke Software.

## 5. License types

VBZS is available under three license types:

- Standard License;
- Developer License;
- Enterprise License.

The rights granted depend on the license type identified in the digitally signed license code.

## 6. Standard License

A Standard License permits the licensee to use VBZS in its own personal, professional or commercial FileMaker solutions for the licensed number of users.

A Standard License covers between 1 and 25 licensed users.

The number of licensed users is recorded in the license code.

A Standard License may be activated:

- through a local `license.json` file in FileMaker Pro;
- using `VBZS_Register`;
- using the VBZS registration dialog in FileMaker Pro.

When a Standard License is successfully registered through the VBZS registration dialog, VBZS may store the license locally in `license.json` so that it can be loaded automatically during subsequent FileMaker Pro sessions.

The licensee may make reasonable backup copies of the license information for its own use.

A Standard License does not grant the right to commercially distribute VBZS to customers as part of a FileMaker solution.

Commercial distribution of VBZS as part of a FileMaker solution requires a Developer License or, where applicable, an Enterprise License.

## 7. Developer License

A Developer License is intended for a FileMaker developer who develops and commercially distributes FileMaker solutions to customers.

A Developer License permits the developer to include and distribute VBZS together with the developer's own FileMaker solution to the developer's customers.

VBZS may not be distributed under a Developer License as a standalone product separate from the developer's solution.

For each individual customer deployment, a Developer License may cover no more than 25 licensed users.

A customer deployment requiring between 1 and 25 licensed users may use VBZS under the developer's Developer License, subject to the terms of that license.

If an individual customer requires 26 or more licensed users, the Developer License does not cover that customer deployment.

In that case, a separate Enterprise License must be purchased for that customer.

There are no exceptions to the 25-user maximum for a customer deployment under a Developer License.

For example:

```text
Customer A: 10 users
Developer License permitted
```

```text
Customer B: 25 users
Developer License permitted
```

```text
Customer C: 26 users
Separate Enterprise License required
```

The requirement for an Enterprise License applies per customer deployment and cannot be avoided by splitting the deployment across multiple FileMaker files, servers, solutions, environments, installations or other technical components.

A Developer License must be activated at runtime using:

```filemaker
VBZS_Register ( order ; licenseCode )
```

Developer licenses are not loaded from `license.json`.

A Developer License entered manually through the VBZS registration dialog activates the license for the current session only.

The developer's FileMaker solution is responsible for calling `VBZS_Register` when registration is required in a new session.

## 8. Enterprise License

An Enterprise License is required for an individual customer deployment that requires 26 or more licensed users.

An Enterprise License is issued for that customer and permits use of VBZS for that customer deployment without the 25-user maximum applicable to Standard and Developer licenses.

An Enterprise License must be activated at runtime using:

```filemaker
VBZS_Register ( order ; licenseCode )
```

Enterprise licenses are not loaded from `license.json`.

An Enterprise License entered manually through the VBZS registration dialog activates the license for the current session only.

The FileMaker solution is responsible for calling `VBZS_Register` when registration is required in a new session.

The commercial terms applicable to an Enterprise License may additionally be specified in the applicable quotation, order confirmation or other written agreement with Van Beek Zakelijke Software.

## 9. Licensed users and seats

For Standard and Developer licenses, the number of licensed users is recorded in the license code and may not exceed 25.

The licensee is responsible for obtaining the appropriate license for the intended deployment.

VBZS does not necessarily perform technical user-count enforcement.

The absence of technical enforcement does not increase or otherwise change the number of users permitted by the license.

For a Developer License, the 25-user maximum applies separately to each individual customer deployment.

A deployment for an individual customer requiring 26 or more licensed users always requires a separate Enterprise License for that customer.

## 10. FileMaker Server

FileMaker Server uses runtime registration.

A Server deployment must therefore register VBZS using:

```filemaker
VBZS_Register ( order ; licenseCode )
```

VBZS does not rely on a local `license.json` file for FileMaker Server registration.

The same principle applies to hosted and cloud environments where direct file-system access may not be available to the licensee.

## 11. Trial use

VBZS may be used without an activated license for a limited trial period.

During the trial period, licensed VBZS functions are available for evaluation.

After the trial period has expired, licensed functions may be disabled until a valid license is activated.

Functions required to identify, inspect or register VBZS may remain available after the trial period has expired.

The availability and duration of trial functionality may differ between FileMaker Pro and FileMaker Server and may change between releases.

Trial use does not grant distribution rights or any ownership rights in VBZS.

## 12. License generations

VBZS uses an internal license generation system that is independent of the normal VBZS software version.

Multiple VBZS versions may belong to the same license generation.

A license for a newer generation may be used with a VBZS version that requires an earlier generation.

A license issued for an earlier generation may not be used with a VBZS version that requires a newer generation.

For example:

```text
Required generation: 1
License generation: 2
Result: valid
```

while:

```text
Required generation: 2
License generation: 1
Result: not valid for this VBZS generation
```

A change to the normal VBZS version number does not by itself introduce a new license generation.

Van Beek Zakelijke Software determines when a new license generation is introduced.

## 13. Registration and license verification

VBZS performs cryptographic verification of license information.

A valid license is accepted only when the signed license information can be successfully verified.

A failed registration does not invalidate an already active valid license.

Runtime registration takes effect immediately and does not require FileMaker to be restarted.

A runtime license remains active for the current FileMaker process or applicable FileMaker Server process.

Runtime registration is not automatically persisted between sessions.

## 14. License transfer

A license is issued to the licensee identified in the license code.

A license may not be transferred, resold, assigned or sublicensed except where the applicable Developer License expressly permits distribution of VBZS as part of the developer's FileMaker solution.

The right of a Developer License holder to distribute VBZS with its solution does not transfer ownership of the Developer License or permit the customer to use that license independently of the licensed solution.

If organizational changes, mergers, acquisitions or other circumstances require a formal change of licensee, the licensee should contact Van Beek Zakelijke Software.

## 15. Updates and upgrades

A license does not automatically entitle the licensee to every future VBZS release or license generation.

Updates that remain within a compatible license generation may be usable with an existing license.

A future release may require a newer license generation and therefore a new or upgraded license.

When a new license generation is introduced, the applicable upgrade conditions and pricing will be communicated separately.

Versions of VBZS remain subject to the license terms under which they were originally distributed.

## 16. Third-party components

VBZS may contain or use third-party software components.

Such components remain subject to their respective copyright notices and license terms.

Third-party licenses do not grant rights to the proprietary VBZS source code or to VBZS as a whole beyond the rights explicitly provided by those third-party licenses.

Where required, applicable third-party copyright and license notices are included with the VBZS distribution or documentation.

## 17. Availability and compatibility

VBZS is developed for supported FileMaker environments and operating systems identified in the accompanying documentation.

Van Beek Zakelijke Software may add, change or discontinue support for FileMaker versions, operating systems or architectures in future releases.

Compatibility with unsupported or undocumented environments is not guaranteed.

## 18. AS IS software

VBZS is provided on an "AS IS" and "AS AVAILABLE" basis.

The licensee accepts VBZS in its current state and is responsible for determining whether the software is suitable for the intended purpose, environment and workflow.

To the extent permitted by applicable law and the applicable NLdigital Terms 2025, Van Beek Zakelijke Software does not warrant that VBZS:

- is free from every defect or error;
- will operate without interruption;
- will meet every individual requirement;
- is suitable for every particular purpose;
- will remain compatible with every future FileMaker release, operating system or third-party component;
- will produce suitable results in every possible environment or workflow.

The licensee is responsible for testing VBZS before using it in production or business-critical environments.

The licensee is also responsible for maintaining appropriate backups, recovery procedures and safeguards for FileMaker solutions and data.

Nothing in this section excludes or limits rights, guarantees or obligations that cannot legally be excluded or limited.

## 19. Liability

Liability is governed by the applicable NLdigital Terms 2025.

Where this Software License Agreement contains provisions relating to liability, warranty, availability or damages, those provisions are supplemental to the NLdigital Terms 2025.

In the event of a conflict, the NLdigital Terms 2025 prevail unless expressly agreed otherwise in writing.

Nothing in this Software License Agreement creates a broader liability for Van Beek Zakelijke Software than follows from the applicable NLdigital Terms 2025 or mandatory law.

## 20. Termination

The license rights granted under this agreement terminate if the licensee materially violates the applicable license terms, subject to any applicable provisions of the NLdigital Terms 2025 and mandatory law.

Following termination, the licensee must cease using the affected license and may no longer use or distribute VBZS under that license.

Termination does not transfer, waive or remove any intellectual-property rights.

## 21. Applicable law and disputes

Applicable law, jurisdiction and dispute resolution are governed by the applicable NLdigital Terms 2025, except where mandatory law provides otherwise.

The Dutch text of the NLdigital Terms 2025 prevails where those terms provide that the Dutch version is authoritative.

## 22. Relationship with the NLdigital Terms 2025

This Software License Agreement contains the product-specific licensing conditions for VBZS and supplements the NLdigital Terms 2025.

The NLdigital Terms 2025 govern the general contractual relationship between Van Beek Zakelijke Software and the licensee.

Specific written agreements between Van Beek Zakelijke Software and the licensee may supplement or amend these terms to the extent permitted by the applicable NLdigital Terms 2025 and applicable law.

If provisions appear to conflict, they must be interpreted in accordance with the contractual hierarchy and interpretation rules established by the applicable NLdigital Terms 2025 and any expressly agreed written arrangements between the parties.


## 23. Changes to future license terms

Van Beek Zakelijke Software may use different license terms for future VBZS versions or license generations.

Such changes do not retroactively change the license terms for a VBZS version already distributed under earlier terms.

The license applicable to a particular VBZS release is the license distributed with that release.

## 24. Contact and documentation

Questions relating to licensing, Developer deployments, Enterprise deployments, license transfers, upgrades or replacement licenses should be directed to Van Beek Zakelijke Software.

Technical information about runtime registration is available here:

[VBZS_Register.md](docs/VBZS_Register.md)

Information about the currently active license is available in:

[VBZS_LicenseInfo.md](docs/VBZS_LicenseInfo.md)

General VBZS documentation is available in:

[README](README.md)
