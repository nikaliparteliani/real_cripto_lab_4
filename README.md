Real Crypto Lab
commands:
echo "Hello from Week 4! This is my message file." > message.txt

A) RSA (Asymmetric) — generate keys, encrypt, decrypt
Generate RSA key pair (2048-bit):
openssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:2048 -out private.pem
openssl pkey -in private.pem -pubout -out public.pem

Encrypt the text with the public key (OAEP + SHA-256):
openssl pkeyutl -encrypt -inkey public.pem -pubin -pkeyopt rsa_padding_mode:oaep -pkeyopt rsa_oaep_md:sha256 -in message.txt -out message_rsa_encrypted.bin

Decrypt with the private key:
openssl pkeyutl -decrypt -inkey private.pem -pkeyopt rsa_padding_mode:oaep -pkeyopt rsa_oaep_md:sha256 -in message_rsa_encrypted.bin -out message_rsa_decrypted.txt

Quick check:
diff -s message.txt message_rsa_decrypted.txt

B) AES-256 (Symmetric) — generate key/IV, encrypt, decrypt
Generate key and IV:
# 32 bytes (256 bits) random key
openssl rand 32 > aes_key.bin
# 16 bytes (128 bits) random IV for CBC
openssl rand 16 > aes_iv.bin

Encrypt with AES-256-CBC using the raw key/IV:
KEY_HEX=$(xxd -p -c 256 aes_key.bin)
IV_HEX=$(xxd -p -c 256 aes_iv.bin)
openssl enc -aes-256-cbc -K "$KEY_HEX" -iv "$IV_HEX" -in message.txt -out message_aes_encrypted.bin

Decrypt:
KEY_HEX=$(xxd -p -c 256 aes_key.bin)
IV_HEX=$(xxd -p -c 256 aes_iv.bin)
openssl enc -d -aes-256-cbc -K "$KEY_HEX" -iv "$IV_HEX" -in message_aes_encrypted.bin -out message_aes_decrypted.txt

Quick check:
diff -s message.txt message_aes_decrypted.txt

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

RSA and AES differ in how they work and where they are used. RSA is an asymmetric encryption algorithm that uses a public and private key pair. It is slower but ideal for securely exchanging small pieces of data, such as encryption keys or digital signatures. AES, on the other hand, is a symmetric encryption algorithm that uses a single shared key for both encryption and decryption. It is much faster and more efficient for encrypting large amounts of data, such as files or network traffic. In practice, RSA is often used to protect the AES key, while AES handles the actual data encryption.



