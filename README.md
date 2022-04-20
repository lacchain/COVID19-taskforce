# DAVID-19Pact LACChain task force

DAVID-19PAct® is a task force where different LACChain partners collaborate to lead a campaign to provide useful blockchain-based solutions to combat the spread of the Coronavirus. The goal is to:

* Propose a [standard for the forms and the verifiable credentials](https://github.com/lacchain/david19-taskforce/tree/master/verifiableCredentials) that are generated by different mobile apps to notarize information about isolation periods, symptoms, infection, and recovery.

* Create a single smart contract that will register all the cryptographyc proofs of the credentials so every app whitelisted can register the proofs on it, and everyone can verify them against it. The smart contract also records the anonymous deta in a decentralize way, so no central back-end/server is necessary.

* [Provide a map](https://map.lacchain.net/) where pseudonymous information about citizens is displayed with different colors depending on if they are healthy (blue), with symptoms (yellow), affected (red), or recovered (green). Additionally, dahsboards will show total numbers of engagement and other useful information. The only information that is collected is age, sex, location, and information to notarize.

If you want to know more about the initiative, you can also check the [COMMSITE](https://mellamodavid19.org/).

## 1. Definition of the information to be notarized 

We are proposing to have five different forms to notarize: 

* Confinement [ESP version](https://github.com/lacchain/COVID19-taskforce/blob/master/docs/FORM_CONFINAMIENTO.md) [ENG version](https://github.com/lacchain/COVID19-taskforce/blob/master/docs/CONFINEMENT_FORM.md) 
* Interruption of confinement [ESP version](https://github.com/lacchain/COVID19-taskforce/blob/master/docs/FORM_INTERRUPCION.md) [ENG version](https://github.com/lacchain/COVID19-taskforce/blob/master/docs/CONFINEMENT_INTERRUPTION_FORM.md) 
* Symptoms [ESP version](https://github.com/lacchain/COVID19-taskforce/blob/master/docs/FORM_SINTOMAS.md) [ENG version](https://github.com/lacchain/COVID19-taskforce/blob/master/docs/SYMPTOMS_FORM.md) 
* Infection [ESP version](https://github.com/lacchain/COVID19-taskforce/blob/master/docs/FORM_INFECCION.md) [ENG version](https://github.com/lacchain/COVID19-taskforce/blob/master/docs/INFECTION_FORM.md) 
* Recovery [ESP version](https://github.com/lacchain/COVID19-taskforce/blob/master/docs/FORM_CURA.md) [ENG version](https://github.com/lacchain/COVID19-taskforce/blob/master/docs/RECOVERY_FORM.md)

Click on the links to see them and provide feedback. 

## 2. Data formats and verifiable credential standards 

We are proposing to create a common format aligned with W3C, DIF, EBSI, and others in which the information of the forms provided by the citizens via UI will be embedded and understood by all wallets.  

The forms to be fulfilled at the apps by the citizens are presented [HERE](https://github.com/lacchain/DAVID19-taskforce/tree/master/docs).

The verifiable credentials to be generated when the formas are fulfilled are presented [HERE](https://github.com/lacchain/DAVID19-taskforce/tree/master/verifiableCredentials).

The smart contracts are [HERE](https://github.com/lacchain/DAVID19-taskforce/tree/master/contracts) and the documentation is [HERE](https://github.com/lacchain/DAVID19-taskforce/blob/master/Smart%20Contract%20Documentation.md).

The API documentation to consume information collected from the public smart contract is [HERE](https://github.com/lacchain/DAVID19-taskforce/blob/master/backend-api.md).

Only the citizens must keep in their devices the credentials that are generated by them. Neither the credentials nor the information attested on them will be shared with anyone, including the app provider. Only in future designs, there will be an option for the citizens to share those credentials with specific entities (i.e. a medical center) they trust.

## 3. Common repository of pseudonimous information from the credentials

We are proposing that the IDB team will deploy a single smart contract to serve as a public repository that will collect non-personal information (pseudonymous) from the credentials issued by different apps colaborating in the initiative. This information includes:

*	Hash of some of the fields of the credential (See Sections 6 and 7)
* Identificator/id of the user/citicen (not connected to personal data)
* Sex
* Age
* Location of the person (geoHash with precision 6)
*	Credential Type (Confinement, Interruption of confinement, Symtoms, Infection, Cure) 
* Additional information of the credential type (Reason for interrauption confinment, medical center validating infection or cure, ...)
*	Expiration date 

The apps participating in the collaboration will send the information to this smart contract following the [Verifiable Credential](https://github.com/lacchain/DAVID19-taskforce/tree/master/verifiableCredentials) common standards defined.

## 4. Presentation of the pseudonymized data
 
An external service has been developed. This service listens to events of new information registered in the smart contract. These events contain the information described in the previous section. With this information, it feeds a database, and this database feeds the [map and dashboards](https://map.lacchain.net/).

Map:

The map plots points of five different colours in the location where the information has been notified. These colours are:

*	Blue for healthy
* Yellow for symptoms
* Red for infected
* Green for recovered

You can also filter by regions, ages, or sex. When you click on the different points, you se the aggregated information about the community represented in that point.

## 5. Privacy ensured

Only the citizens own the credentials and have access and crontrol over all the information they provide. Nobody else (nor the app provider, the blockchain network, or the map displaying the data) has any information other than: 

* Sex
* Age
* Location of the person with a precision of 1 km square (aprox)
*	Type of information attested (Confinement, Interruption of confinement, Symtoms, Infection, Recovery) 
* Additional information attested (Symptoms, Reason for interrauption confinment, ...)
 
## 6. Requirements at the application level

Authentication: Apps/digital wallets are required to authenticate citizens using a two-factor authentication. We recommend using something that you have (the phone itself, but receiving an SMS) and something you know (a password). Idemia is also providing free linceses to leverage their biometric proof of live solution via SDK.

DID methods: The implementation chosen is up to the app provider.

Credential proofs: The implementation chosen is up to the app provider.

Interaction with the [smart-contract-repository](https://github.com/lacchain/DAVID19-taskforce/blob/master/contracts/CovidCredentialRegistry.sol): There are three functions that can be called: "register", "revoke", and "verify". The application will call "register" every time a new credential is generated. The application will call "revoke" everytime a generated credential is no longer valid. The application will call "verify" in order to verify against the smart contract that a credential was register and check its status (applications might also have their own verification methods or proof mechanisms for credential verification, that would not imply calling this smart-contract-database).

When calling the function "verify", the hash to be provided is not the hash of the entire credential, but the hash of the fields that go from "personal information" to "zip code". Please, check the verifiable credentials formats [HERE](https://github.com/lacchain/DAVID19-taskforce/edit/master/README.md). The reason why this is done is discussed in the next Section.

Frequency of generation of credentials:

* Confinement interruption: maximum 3 daily.
* Infection/Recovery: maximum 1 weekly.
* Symptoms: maximum 2 daily.

Compatibility of credentials (by revokation we understand that the app shall call the function revoke in the smart contract to change the status of the credential to "revoked"):
* Confinement Interruption revokes Confinement credential and viceversa.
* Recovery revokes Infection and viceversa.
* In order to revoke no symtomps, a new Symtomps credentials will be issued with the value ¨no symptomps¨.
* Any credential revokes the same credential of the same type (to negate it or to update it). The case in the previous bullet point is actually a subcase of this condition.

Expiration of credentials:
* Confinement: 1 week.
* Confinement interruption: 1 day.
* Infection: 2 weeks.
* Symptoms: 1 week.
* Recovery: undefined. 

Other considerations:
* The value for the type of interruption sent the smart contrat is the first word of the option chosen.
* Apps should use the geolocation to map the location of the user. The location will be provided to the smart contract using [geoHash](https://www.movable-type.co.uk/scripts/geohash.html) with an encoding precision of 6 characteres, which provides a square of 1km (aprox), preserving privacy.
* The hash function is SHA-256.

## 7. Avoidance of duplication of same credentials for same users in different applications

If a person uses different apps to notarize the same information, in principle the smart-contract-repository could not be able to detect that the  same person is notarizing the same information, as the smart-contract-repository only receives a subset of the fields of the credentials that are pseudonimous data and therefore are not linked to people's real identity. However, in order for the maps to show information that filters duplication as much as possible, we are proposing the following.

As described in Section 3, each application will send to the smart contract not only pseodunimous metadata related to the person and the information notarized (age, sex, id, ...) but also a hash of the verifiable credential. However, the hashes of the verifiable credentials can be different between different apps, even if it is the same person notarizing the same information, as there are fields as the DID of the subject and the timestamp that change in different apps. This is why we are proposing to hash al the fields between "personal information" and "zip code". 

As that is the hash of personal information that could eventually become PII, in those fields theres only piece of information that the user provides and only they know. That's they nickname. 

## 8. Standardizing location format

GeoHash encoding with precisiong size.

## Copyright 2021 LACChain