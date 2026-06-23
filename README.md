# The NZ Verify / Whakatūturu app
## Purpose

The [NZ Verify/Whakatūturu app](https://www.govt.nz/nzverifyapp) is provided to support the use of digital credentials in New Zealand. The app allows users to perform in-person verification of digital credentials, to determine whether the person holds a credential that has been issued by a trusted provider and provides appropriate proof of ID or other properties. 

In the digital credentials ecosystem, the NZ Verify app fulfils the role of the verifier used by a relying party. Other parts of the ecosystem for issuing and holding the credentials are provided by other products in the [Digital Public Infrastructure](https://github.com/NZ-Digital-Public-Infrastructure/.github/blob/main/profile/README.md) (DPI).

Providers of other relying party verifier products should refer to the [Digital Credential Digital Trust Service](https://github.com/NZ-Digital-Public-Infrastructure/digital-trust-service) documentation.

![Digital credential ecosystem diagram](/assets/ecosystem-diagram-nz-verify-highlight.png "Digital credential ecosystem diagram")

## How does NZ Verify work?

The app follows the [Open ID for Verifiable Presentations (OpenID4VP)](https://openid.net/specs/openid-4-verifiable-presentations-1_0.html) standard for requesting and delivering digital credentials.

NZ Verify accepts only credentials that have been accredited under the [Digital Identify Services Trust Framework (DISTF)](https://www.publicservice.govt.nz/about-the-commission/government-digital-delivery-agency/trust-framework-for-digital-identity/about-digital-identity-services/trust-framework-legislation), or mobile driver licences (mDLs) that conform to [ISO/IEC 18013-5](https://www.iso.org/standard/69084.html).

The verification request and credential presentation process is:

1. **Device engagement**
   - A digital wallet holder presents their device to the NZ Verify app user. The NZ Verify app scans the displayed QR code to initiate the request. On Android devices, the request can alternatively be initiated using NFC.
   - An encrypted session is established between the two devices using Bluetooth Low Energy (BLE). 
   
2. **Device retrieval**
   - NZ Verify sends a request to the wallet device, specifying the document type (`docType`) and credential claims to be verified. 
   - NZ Verify uses a set of configurable templates that define different verification cases that a user can select from, for example to check whether someone is able to purchase alcohol or operate a motor vehicle. Each template defines the details of the request to be sent to the wallet.

3. **Credential response**
   - The wallet will present a credential that matches the request criteria. In the case of the Govt.nz app wallet, a credential will only be selected for presentation if the document type matches and all requested claims are present. 
   - If no credential in the wallet meets the request criteria, a message is presented to the wallet holder to inform them they do not hold a suitable credential. An error is presented in NZ Verify.
   - The holder must explicitly consent to sharing the credential, typically using the biometric authentication on their device. This provides trust that the person sharing the credential is the same one that the credential was issued and bound to.

4. **Credential validity check**
   - NZ Verify checks that the credential has been signed by a trusted issuer, has not been tampered with, and has not been revoked.
   - The app refers to a trusted issuer list managed by the DPI support team. The list contains the Issuing Authority Certificate Authority (IACA) certificates used in the issuance of accredited credentials and supported mDLs. This allows the app to ensure that the credential has been cryptographically signed by a known issuer.
   - When the app is online it syncs with the trusted issuer list to download the IACAs on a daily basis, which allows it to verify credentials even when it is offline. If a device has been offline for more than three days, the user will be prompted to go online and manually trigger the syncing process.

5. **Credential applicability check (optional)**
   - The verification cases used in NZ Verify can be configured to validate that the credential is of a type that is approved for use for the specified purpose. For example, an overseas driver licence can be used for operation of a motor vehicle but cannot legally be used for purchase of alcohol.
   - DISTF accredited credentials may only be used as an evidence of age document if they contain a photo of the person, and meet the requirements of standard+, strong+, or very strong level of assurance as determined by the Trust Framework Authority. Verification templates for proof of age will only allow credentials that meet these criteria.

6. **Credential claim check (optional)**
   - Verification cases in the app can be configured to validate that claims have a particular value. This is typically used for age checks, to test that an `age_over_nn` claim is present and set to `true`.

7. **Display to NZ Verify user**
   - The outcome of the checks is presented. 
     - If the verification case requires an applicability check (5) or claim check (6), the outcome is shown as **Approved** if all checks have passed successfully, or **Not Approved** if any of checks have failed. In a success case, the data from the requested claims is presented (e.g. portrait and name).
     - If the verification case has no applicability check (5) or claim check (6), the outcome is shown as **Valid** if check (4) passed successfully, and details of the requested claims are shown. Error details will be displayed if the credential is not from a trusted issuer, fails the cryptographic checks, is expired, is revoked, or is not yet active.

## Who should use NZ Verify?
The app can be used by anyone who needs to verify a person’s identity or credentials in person. Examples includes staff in businesses that provide sale of age-restricted items or services, places where proof of identity is required, and where there is need for evidence of ability to legally operate a motor vehicle.

Credential issuers can work with the DPI team to provide further verification cases into NZ Verify that support their type of credential. 

## Using NZ Verify
### App users
The app is available for iPhone and Android devices. Information for users, including a reference guide and training material, is available on the [NZ Verify support](https://www.govt.nz/nzverifyapp/support) page.

*  [Download on the Apple App Store](https://apps.apple.com/nz/app/nz-verify-whakat%C5%ABturu/id6743457606)
*  [Get it on Google Play](https://play.google.com/store/apps/details?id=nz.govt.nzverify)

### Credential providers
#### Testing
The NZ Verify app can be used in end-to-end testing of credentials, to ensure that a credential can be presented and verified as expected.

Sandbox test versions of the NZ Verify and Govt.nz apps are available. See our [DPI Sandbox Guide](https://github.com/NZ-Digital-Public-Infrastructure/.github/blob/main/profile/sandbox/Sandbox%20User%20Guide.md) for details of how to request access to the test products.

#### Adding new verification cases to NZ Verify
The NZ Verify app includes standard verification templates:

| Verification case | Applicable document type |
| ----------------- | ------------------------ |
| Alcohol purchase | mDL (org.iso.18013.5.1.mDL) or photo ID (org.iso.23220.photoid.1) |
| Other age 18+ restriction | mDL (org.iso.18013.5.1.mDL) or photo ID (org.iso.23220.photoid.1) |
| Operating a motor vehicle | mDL (org.iso.18013.5.1.mDL) |
| Proof of identity (basic) | mDL (org.iso.18013.5.1.mDL) or photo ID (org.iso.23220.photoid.1) |
| Proof of identity (customer due diligence) | mDL (org.iso.18013.5.1.mDL) or photo ID (org.iso.23220.photoid.1) | 

A new verification template can be added if there is new type of accredited credential that will require in-person verification, or if there is a new use case for mDL or photo ID credentials that is not already covered.

Information required for a verification template:

| Item | Description |
| ---- | ----------- |
| Label | Title of the verification case to be presented in the app, e.g. "Operating a motor vehicle". |
| Hint | Brief description to be presented below the label in the list of verification cases. |
| Description | Full description shown when the user looks at details of the verification request. |
| Document type(s) | The `docType` values that can be accepted for the verification case. |
| Claims | The namespace and attribute details for the requested claims. |
| Approved issuer(s) | (Optional) Identify the trusted credential issuers that are applicable to the verification case. |
| Valid values | (Optional) Identify claims that must match an expected value, e.g. `age_over_18` = `true`. |

Verification templates should only request claims that will be included for all issued credentials. The Govt.nz app wallet will only present a credential that exactly matches the requested `docType` and all requested claims. If a holder's credential matches the `docType` but is missing a requested claim, even one that is optional for that credential type, it will not be selected. The holder will be shown that no matching credential is available, and NZ Verify will record the verification as failed.

### Costs
There is no fee for use of NZ Verify or for adding verification templates into the app.

### Support

Contact the DPI team at [nzverifyapp@gdda.govt.nz](mailto:nzverifyapp@gdda.govt.nz) for assistance during development and testing, or operational support of production services.

## Links
[Digital Public Infrastructure (DPI)](https://github.com/NZ-Digital-Public-Infrastructure/.github/blob/main/profile/README.md)

[Digital Identify Services Trust Framework (DISTF)](https://www.publicservice.govt.nz/about-the-commission/government-digital-delivery-agency/trust-framework-for-digital-identity/about-digital-identity-services/trust-framework-legislation)

[Digital Credential Digital Trust Service](https://github.com/NZ-Digital-Public-Infrastructure/digital-trust-service)

[DISTF Draft Reference Architecture](https://github.com/nz-trust-framework/DISTF-reference-architecture/blob/main/README.md)
