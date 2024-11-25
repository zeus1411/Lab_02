# Lab #1,20110357, Nguyen Quoc Hung, INSE331280E_01FIE
# Task 1: Transfer Files Between Computers
Using openssl to implementing measures manually to ensure file integerity and authenticity at sending side, 
then veryfing at receiving side

**Question 1**: Conduct transfering a single plaintext file between 2 computers, 
Using openssl to implementing measures manually to ensure file integerity and authenticity at sending side, 
then veryfing at receiving side. 

**Answer 1**:
## 1. Connect both side:
*First, we need to connect sender & receiver*<br>

```sh
docker network create my_network
```
<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/f7f3cd31-5122-448b-9834-143e67cbdd3e"><br>


## 2. Create a text file named `message.txt`:
*First, we write a message and save it in a text file:*<br>

```sh
echo "This is a test message for secure file transfer." > message.txt
```
<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/46719e06-986d-42db-846b-43e696c5f10c"><br>

## 4. Create receiver & install netcat:

```sh
docker run -it --name receiver --network my_network ubuntu
apt-get update && apt-get install netcat-traditional -y
```
<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/e595ccd5-eef4-42cf-aec2-89afa8b8dd30"><br>

## 5. Using netcat:
*Using netcat to transfer message.txt*
```sh
nc -l -p 12345 > message.txt
```
- input: "ls", we can see "message.txt" exist in receiver
  
<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/fefcc3fa-944f-48ca-838f-f8b349bd225c"><br>

# Task 2: Transfering encrypted file and decrypt it with hybrid encryption
Using openssl to implementing measures manually to ensure file integerity and authenticity at sending side, 
then veryfing at receiving side

**Question 2**: Conduct transfering a file (deliberately choosen by you) between 2 computers. 
The file is symmetrically encrypted/decrypted by exchanging secret key which is encrypted using RSA. 
All steps are made manually with openssl at the terminal of each computer.

**Answer 2**:

## 1. Create a text file named `plaintext.txt`:
*First, we write a message and save it in a text file:*<br>

```sh
echo "This is a test for IS." > message.txt
```
<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/71254db5-cd4e-4255-9bbe-32ba83638ebb"><br>

## 2. Generate private & public key:
*We need to generate private & public*<br>

```sh
openssl genpkey -algorithm RSA -out private.pem -pkeyopt rsa_keygen_bits:2048
openssl rsa -pubout -in private.pem -out public.pem
```
<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/2d7136b1-eb62-4452-b227-16db8a989757"><br>

-> The program show " writing RSA key" is correct

## 3. Transfer public key from receiver to sender:
*We need to input the code below to sender*<br>

```sh
nc -l -p 12345 > public_key.pem
```
*Then we input the code below to receiver*<br>
```sh
cat public.pem | nc sender 12345
```
<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/4bd0b297-1900-4b14-9466-09f9a96a3144"><br>

## 4. Generate a random AES key
*Generate a random AES key*<br>

```sh
openssl rand -out secret.key 32
```

<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/8321a339-3f89-48ba-be41-c36dfafe64bc"><br>


```sh
openssl enc -aes-256-cbc -salt -in plaintext.txt -out encrypted_file.bin -pass file:./secret.key
```

<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/c2bc1a64-6ad8-457c-8899-1ed6dc545e27"><br>

## 5. Ecrypt the secret key:

input both code to 
```sh
 openssl rsautl -encrypt -inkey public_key.pem -pubin -in secret.key -out encrypted_key.bin
```
<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/c6e5f459-c2ac-4bff-b2c7-66590d0644bd"><br>

## 6. Send the encrypted_file & encrypted_key from sender to receiver:
*Sender*
```sh
 cat encrypted_key.bin | nc receiver 12345
 cat encrypted_file.bin | nc receiver 123456
```
*Receiver*
```sh
nc -l -p 12345 > encrypted_key_copy.bin
 nc -l -p 123456 > encrypted_file_copy.bin
```
<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/f7109759-4b08-407c-ba08-50522857c764"><br>

## 7. The receiver uses the private key to decrypt the secret key and then uses the secret key to decrypt the plaintext.txt:
*Decrypted secret key*
```sh
openssl rsautl -decrypt -inkey private.pem -in encr
ypted_key_copy.bin -out decrypted_key.txt
```

<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/f652e30d-e41d-4690-a056-e8eb51e1a3b5">


*Decrypt the encrypted_file.bin file with the secret key*
```sh
openssl enc -d -aes-256-cbc -in encrypted_file.bin -out decrypted_plaintext.txt -pass file:decrypted_key.txt
```

<img width="500" alt="Screenshot" src="https://github.com/user-attachments/assets/0cb4629c-7184-4f2f-bf99-19f5c0c621cb">