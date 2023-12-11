# Exercise: Create a service to interact with CodeMeter via agiven lib
In this project I will try to approach a little functionality I faced
recently, in a **functional** way.

This is a simplification and an abstraction of the actual functionality, not the full picture.
Do not critizie its architecture based on how would you have implemented it.

# The functionality
`SecureLicense` handles software licenses, both online and offline. It is deployed as a docker container together with the
solution.

The `licensing-service` has to provide the necessary endpoints for the UI to manage the licenses for the solution, using the
librarty to interact with `SecureLicense`.

One installation of the solution can either use an `Online` or and `Offline` license.

The license information to be shown in the UI is the `LicenseStatus` and the `ExpirationDate`. The `LicenseStatus` can be:
`NOT_LICENSED`, `LICENSED` and `EXPIRED`.

## Online activation
The user enters a `TicketId` provided by the monetization team. The solutions validates it online.
If the activation works, the solution returns the `Status` as `LICENSED` and the expiration date returned.
Otherwise a the `Status` is `NOT_LICENSED` with a descriptive error message.

### Periodical checks
Every day, the license status has to be checked to determine the `Status` and `ExpirationDate`.

## Offline activation
The user must follow the following steps: 

1. Download a `Fingerprint File` fom the solution.
2. Send the `Fingerprint File` to the `Secure License Webpage` to obtain a `License File`.
3. Upload the `License File` to the solution to activate the product. If the activation works, the solution returns the `Status`
 as `LICENSED` and the expiration date returned. Otherwise a the `Status` is `NOT_LICENSED` with a descriptive error message.
4. Download a `Receipt File` from the solution.

## Testability
The `licensing-service` needs to bee easily configured to skipp all real interaction with the `SecureLicense` container. This will enable developers, testerd and CI/CD pipelines to work on the solution without actually managing licenses.

## How to access SecureLicense
To access it, the monetization team has developed the library `SecureLicenseAccess`.

It provides the following interfaces:

```
SecureLicenseHelper
{
    // Returns a license handle
    int AccessLicense(string firmCode, productCode, 0, false)
    
    // Returns the expiration date for the specified license handle
    Date GetLicenseExpiration(int handle)
    
    // Signals that no more ations are going to be done with the specified handle
    void ReleaseLicense(int handle)

    //Imports a license template file
    string ImportLicenseInformationFile(Stream licenseTemplateFile);
    
    // Returns a receipt file
    string GetRemoteContext(string serialNumber)

    // Imports a license file. 
    string SetRemoteContext(Stream licenseFile, serialNumber);
}
```

```
JavaActivationWizard
{
    // Activates a license online and returns the status
    int ActivateTicket(ticketId, string CmActId, true);

    // Refreshes the status of all the licenses
    void AutoUpdateCmContainers()
}
```

### Creating a Fingerprint file
The solution must have a `Template File` that is provided by the monetization team.

### Recommendations
The monetization team recommends that the solution stores the `TicketId` or the `SerialNumber` by itself, since it is necessary for many interactions with the library.

## Implementation constrains
- Only the `licensing-service` will be implemented.
- Only the `SecureLicenseAccess` library will be used to communicate with `SecureAccess`.

## My take on how to implement it
My goal is to use Vertical Slice Architecture and functional programming principles to implement this whole logic.
