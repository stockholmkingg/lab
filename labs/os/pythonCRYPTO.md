# Язык программирования Python. Криптография.
## Задание 1
Реализуйте атаку грубой силой на текст, зашифрованный AES. В качестве пароля возьмите трёхзначное число.
#### Ответ:
```
import hashlib
import ast
from Crypto import Random
from Crypto.Cipher import AES


BS = AES.block_size
pad = lambda s: s + (BS - len(s) % BS) * chr(BS - len(s) % BS)
unpad = lambda s: s[:ord(s[len(s) - 1:])]
iv = Random.new().read(BS)
key = hashlib.sha256(b"696").digest()
plain_text = '''netology
infosec'''

# def encoding_func(plain_text, key):
plain_text = pad(plain_text)
cipher = AES.new(key, AES.MODE_CBC, iv)
cipher_text = (iv + cipher.encrypt(plain_text.encode()))
# return cipher_text
print(f"Зашифрованный текст: {cipher_text}\n")


# def decoding_func(cipher_text):
print("Возможные варианты расшифровки: (смотрите 696 вариант)")
for digit in range(100, 1000):
    key = hashlib.sha256(str(digit).encode('utf-8')).digest()
    cipher = AES.new(key, AES.MODE_CBC, iv)
    decoded_text = unpad(cipher.decrypt(cipher_text[BS:]))
    print(f"{digit} - {decoded_text}")


# procedure = input('Выберите режим работы (encode / decode)\n')
# if procedure == 'encode':
#     plain_text = str(input('Введите текст для кодирования\n'))
#     example_key = str(input('Введите ключ (любое трёхзначное число)\n'))
#     key = hashlib.sha256(example_key.encode('utf-8')).digest()
#     print(f"Зашифрованный текст: {encoding_func(plain_text, key)}")
#
# elif procedure == 'decode':
#     cipher_text = (input('Введите зашифрованый текст\n'))
#     cipher_text = ast.literal_eval(cipher_text)
#     decoding_func(cipher_text)
```
