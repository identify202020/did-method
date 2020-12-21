#  KR.identifying DID Method Specification


### Abstract

KR.identifying network is a decentralized network system for Self-Sovereign identity and Verifiable credential.
The system is operated by a korea government(Ministry of the Interior and Safety, korea)
It can replace a legacy centralized credential id system that with trusted blockchain node.

In the KR.identifying system , a government employee ID cards will be issued by default
and various types of identification cards are issued depending on future use. 
Decentralized Identifiers (DIDs) are used as unique identifiers for identification cards. 
DIDs can also obtain public key information for secure information exchange between users.


## Status of This Document

It is a draft document and will be updated.

## KR.identifying DID Method Name

The name string that shall identify this DID method is: kr

## KR.identifying DID Format

ALPHA and DIGIT used in “kr-identifier" are the definition of Core Rules in [RFC5234].

kr-did = "did:kr:" kr-identifier 
kr-identifier = ALPHA / DIGIT 

example of KR.identifying did
```
did:kr:4EFNaYeA9hDp6F55JAB38EFtNcYEbbM9nwKr
did:kr:gov:4EFNaYeA9hDp6F55JAB38EFtNcYEbbM9nwKr
```

### Sample of DID Document
```

{
  "id": "did:kr:7V2FnzCykod7aK9eMBEtKEdyfxSwn",
  "publicKey": [
    {
      "id": "did:kr:7V2FnzCykod7aK9eMBEtKEdyfxSwn #keys-1",
      "type": "EcdsaKoblitzSignature2016",
      "publicKeyPem": "-----BEGIN PUBLIC KEY...END PUBLIC KEY-----"
    },
    {
      "id": "did:kr:7V2FnzCykod7aK9eMBEtKEdyfxSwn#keys-2",
      "type": "EcdsaKoblitzSignature2016",
      "publicKeyPem": "-----BEGIN PUBLIC KEY...END PUBLIC KEY-----"
    }
  ],
  "authentication": [
    {
      "publicKey": "did:kr:7V2FnzCykod7aK9eMBEtKEdyfxSwn#keys-1"
    }
  ]
}
}
```

## Operation
KR.identifying (CRUD) operations are provided in RESTful API format.
DID documents are generated in the block chain by smart contract code.
it checks the authority and performs signature verification within the smart contract.
To prevent replay attacks, all signatures require a nonce.

### Create

Two types of authoring are supported.
You can request  a DID generation through simple input data and signature values.
The other is to create and send the entire DID Doucment directly.
SmartContract returns true if it has not been issued to a block chain and if the signature verification succeeds.

```
endpoint : domain/bcapi/v1/did/reg
Input : {did publickey, signature}
Output : {result}
```

To create a simple type of document, the input value must be as following.
It returns "true", if successfully created.

example input data 
```
endpoint : /did_create
input : {did_document}
output : {result}

```

To create a full type of document, the input value must be as following.
The input has full docuemnt of did with signature.
It returns "true", if successfully created.
```
endport : /did_create
input : {did_document}
output : {result}
```


### Read 

To read did document for some did, the input value must be as following.
It returns a did docuement for it, if it is found.

```
endpoint : domain/bcapi/v1/did/find
input : { did }
output : { did_document }

```

```
example input data
{
"id": "did:kr:7V2FnzCykod7aK9eMBEtKEdyfxSwn"
}
```

```
{
  "id": "did:kr:7V2FnzCykod7aK9eMBEtKEdyfxSwn",
  "publicKey": [
    {
      "id": "did:kr:7V2FnzCykod7aK9eMBEtKEdyfxSwn #keys-1",
      "type": "EcdsaKoblitzSignature2016",
      "publicKeyPem": "-----BEGIN PUBLIC KEY...END PUBLIC KEY-----"
    },
    {
      "id": "did:kr:7V2FnzCykod7aK9eMBEtKEdyfxSwn#keys-2",
      "type": "EcdsaKoblitzSignature2016",
      "publicKeyPem": "-----BEGIN PUBLIC KEY...END PUBLIC KEY-----"
    }
  ],
  "authentication": [
    {
      "publicKey": "did:kr:7V2FnzCykod7aK9eMBEtKEdyfxSwn#keys-1"
    }
  ]
}
}
```

### Update
To update did_document ,the input value must be as following.
The input has a new document with signature including a nonce.
It returns updated a did docuemnt, if successfully updated.

```
endpoint : domain/bcapi/v1/did/update
input : did_document (included proof)
output : did_document
```


### Delete
To delete the did_document, the input value must be: The input has a signature containing nonce.
After verifying the proof of the delete operation with the public key in the blockchain, 
did_document with the public key is deleted from the blockchain and is no longer available. 
And you cannot cancel this operation.
It returns "true". if successfully delete.

```
endpoint : domain/bcapi/v1/did/revoke
input : did with proof
output : result
```



# Security Considerations

### Replay Attacks 
To prevent a replay attact a did proof has to have nonce value. 
In decentralized network or smart contract code could not generate reliable random number.(oracle problem)
To solve this issue , we store the nonce value that was used , and check if the value already used .
### Non-repudiation
The DID owner generates a proof (digital signature) with the private key and verifies the signature with the public key paired with the private key in the blockchain. 
### Providing Traffic Security
 KR.identifying provides traffic security using TLS protocols.
 
### Storing Data
 KR.identifying stores only the following data in the blockchain:
• DID documents containing public keys
• Signing value used to verify evidence of a verifiable claim

### Consensus Algorithm
For the security of blockchain nodes, KR.identifying uses signature-based Delegated Proof of Stake consensus algorithms and BFTs (2/3).

# Privacy Considerations
## TODO
KR.identifying  blockchain and DID documents do not contain Personally-Identifiable Information (PII). Currently, mobile ID stores only the following data in the blockchain:
• DID documents containing public keys
• Informations that include vc schema , revocation to verify verifiable credential 

# References
[1]. W3C Decentralized Identifiers (DIDs) v1.0,https://w3c.github.io/did-core/
