# Lab #1,20110357, Nguyen Quoc Hung, INSE331280E_01FIE
# Task 1: Transfer Files Between Computers
Using openssl to implementing measures manually to ensure file integerity and authenticity at sending side, 
then veryfing at receiving side
**Question 1**: Exploration of various encryption with openssl
**Answer 1**:
## 1. Sending Side:
*First, we need to generate public-key and private-key on the sending side*<br>

```sh
openssl genpkey -algorithm RSA -out private_key.pem
openssl rsa -pubout -in private_key.pem -out public_key.pem
```
- private_key used for signing filed
- public_key: Shared with the receiver for verification


## 2. Create a text file named `message.txt`:
*First, we write a message and save it in a text file:*<br>

```sh
echo "This is a test message for secure file transfer." > message.txt
```
<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/bf08cce4-656b-42ea-9cb4-7ee5c79e4adf"><br>

Sign the "message.txt" to generate a signature:

```sh
openssl dgst -sha256 -sign private.key -out file.sig message.txt
``` 



## 3. Bundle the Files:
Combine the messagetext file and its signature into a single tarball for transfer:
```sh
tar -cvf file_bundle.tar message.txt file.sig
``` 
<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/028e7f88-27ff-4e01-8ace-0ad778699fa9"><br>

## 4. Transfer the File:
*Send the tarball to the receiving computer using a secure method like SCP:*<br>

```sh
scp file_bundle.tar user@receiver_ip:/destination_path
```
Replace user and receiver_ip with the appropriate username and IP address of the receiving computer.

## 5. Receiving Side:
*Unpack the File Bundle.
Extract the files from the received tarball:*<br>

```sh
tar -xvf file_bundle.tar
```
This will produce:

- message.txt: The plaintext file.

- file.sig: The file's signature.
  
## 5. Verify the File:
*Use the sender’s public key to verify the authenticity and integrity of the file:*<br>

```sh
openssl dgst -sha256 -verify public.key -signature file.sig message.txt
```
Expected Output:
- If the file is authentic and unaltered:
   -  Verified OK
- If verification fails: An error message indicating tampering or mismatch.