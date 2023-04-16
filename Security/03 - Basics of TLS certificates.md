# SSL (Secure Sockets Layer) certificates

* Are digital certificates that `verify` the identity of a website `and encrypt the communication between a user's web browser and the website's server`.


## How SSL certificates work?

- A website owner obtains an SSL certificate from a trusted Certificate Authority (CA).
- The SSL certificate contains information about the website, such as its name, address, and public key.
- When a user visits the website, their browser requests the SSL certificate from the website's server.
- The server sends the SSL certificate to the user's browser.
- The user's browser checks the SSL certificate's validity to ensure it is issued by a trusted CA and that it matches the website's domain name.
- If the SSL certificate is valid, the user's browser establishes a secure connection with the website's server and encrypts all data transmitted between the two.
- The user can then interact with the website securely, and any sensitive information transmitted between the two is protected by encryption.

###  SSL certificates use both `symmetric and asymmetric encryption` to secure the data.

## Symmetric encryption
- is used to encrypt the actual data being sent between the client and the server. In this type of encryption, the same key is used to both encrypt and decrypt the data. The key is kept secret between the two parties.

## Asymmetric encryption
- is used to securely transmit the symmetric key from the server to the client. In this type of encryption, two keys are used: a public key and a private key. The public key is available to anyone who wants to send a message to the owner of the private key. The private key is kept secret and is used to decrypt messages that have been encrypted with the corresponding public key.

> When a client connects to a server over SSL, the server sends its SSL certificate, which includes its public key. The client then generates a random symmetric key and encrypts it with the server's public key. The encrypted key is sent to the server, which decrypts it with its private key. Both parties now have the symmetric key and can use it to encrypt and decrypt data sent between them.



## The SSL/TLS Handshake Process:

- The SSL/TLS handshake process is a series of steps that occurs when a client connects to a secure website using HTTPS. The purpose of the handshake process is to establish a secure and encrypted connection between the client and server and to verify the identity of the server.

## The SSL/TLS handshake process involves the following steps:

* The client sends a "ClientHello" message to the server, which contains information about the SSL/TLS version, the list of supported ciphers, and other information.

* The server responds with a "ServerHello" message, which contains information about the SSL/TLS version, the selected cipher, and other information.

* The server sends its SSL certificate to the client, which contains the server's public key and information about the CA that issued the certificate.

* The client verifies the SSL certificate by checking that it was issued by a trusted CA, that the domain name matches the website domain name, and that the certificate has not expired or been revoked.

* If the SSL certificate is valid, the client generates a random session key and encrypts it using the server's public key, which is contained in the SSL certificate.

* The client sends the encrypted session key to the server, which decrypts it using its private key.

* The client and server use the session key to encrypt and decrypt all data transmitted between them during the SSL/TLS session.

## Signing Certificates:

* SSL certificates are signed by a trusted Certificate Authority (CA). The CA is responsible for verifying the identity of the website and issuing a certificate that contains the website's public key and other information. The CA also signs the SSL certificate using its private key, which creates a digital signature that can be verified by clients using the CA's public key.

* When a client receives an SSL certificate from a server during the SSL/TLS handshake process, it verifies the certificate by checking that it was issued by a trusted CA and that the digital signature is valid. If the certificate is valid, the client uses the server's public key to encrypt the session key and send it to the server.

## Key Exchange:

* During the SSL/TLS handshake process, the server sends its SSL certificate to the client, which contains the server's public key. The client uses the server's public key to encrypt the session key and send it to the server. The server then uses its private key to decrypt the session key and establish the SSL/TLS session.

* In the case of symmetric encryption, the session key is generated by the client and sent to the server encrypted using the server's public key. The server then decrypts the session key using its private key and uses it to encrypt and decrypt data transmitted during the SSL/TLS session.

* In the case of asymmetric encryption, the client uses the server's public key to encrypt the session key and send it to the server. The server then uses its private key to decrypt the session key and establish the SSL/TLS session.

## `Note`:
### - The server can request a certificate from the client to validate their identity.
### - The client generates a pair of keys and a signed certificate from a valid CA and sends it to the server.