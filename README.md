# dotnet-exercise-license-management

## Exercise: Create a service to interact with CodeMeter via agiven lib
In this project I will try to approach a little functionality I faced
recently, in a **functional** way.

This is a simplification and an abstraction of the actual functionality, not the full picture.
Do not critizie its architecture based on how would you have implemented it.

## The functionality
`SecureLicense` handles software licenses, both online and offline. It is deployed as a docker container together with the
solution.

The `licensing-service` has to provide the necessary endpoints for the UI to manage the licenses for the solution, using the
librarty to interact with `SecureLicense`.

One installation of the solution can either use an `Online` or and `Offline` license.

The license information to be shown in the UI is the `LicenseStatus` and the `ExpirationDate`. The `LicenseStatus` can be:
`NOT_LICENSED`, `LICENSED` and `EXPIRED`.

### Online activation
The user enters a `TicketId` provided by the monetization team. The solutions validates it online.
If the activation works, the solution returns the `Status` as `LICENSED` and the expiration date returned.
Otherwise a the `Status` is `NOT_LICENSED` with a descriptive error message.

### Offline activation
The user must follow the following steps: 

1. Download a `Fingerprint File` fom the solution.
2. Send the `Fingerprint File` to the `Secure License Webpage` to obtain a `License File`.
3. Upload the `License File` to the solution to activate the product. If the activation works, the solution returns the `Status`
 as `LICENSED` and the expiration date returned. Otherwise a the `Status` is `NOT_LICENSED` with a descriptive error message.
4. Download a `Receipt File` from the solution.

### Periodical checks
Every day, the license status has to be checked to determine the `Status` and `ExpirationDate`.

## How to access SecureLicense
To access it, the monetization team has developed the library `SecureLicenseAccess`.

### Validating a ticket online


### Creating a Fingerprint file
The solution must have a `Template File` that is provided by the monetization team.

The `Template File` is passed to the `SecureLicenseAccess` to generate a 
`SerialNumber`, which then is stored. The `SerialNumber` is then passed again to the `SecureLicenseAccess` to generate the
`Fingerprint File`. The `Fingerprint File` is returned to the client.

### Checking license
 The `licensing-service` passes the `SerialNumber` and the uploaded `License File` to the
 `SecureLicenseAccess`. Then the `Status` has to be checked by getting the `ExpirationDate` from `SecureLicenseAccess`.

If the activation is successful the `licensing-service` returns the `Status` as `LICENSED` 
and the expiration date returned by `SecureLicenseAccess`. Otherwise a descriptive error message.

### Creating a receipt
The `licensing-service` passes the `SerialNumber` to the to the
 `SecureLicenseAccess` and returns the `Receipt File`.

### Recommendations
The monetization team recommends that the solution stores the `TicketId` or the `SerialNumber` by itself, since it is necessary
 for many interactions with the library.
