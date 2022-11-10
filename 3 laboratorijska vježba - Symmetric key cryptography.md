# 3. laboratorijska vježba - Symmetric key cryptography (kriptografija sa simetričnim ključem)

Na 3. laboratorijskoj vježbi dešifrirali smo odgovarajući *ciphertext* na osnovu predavanja koje smo imali na temu ****Symmetric key cryptography (kriptografija sa simetričnim ključem)****. *Plaintext* koji smo trebali otkriti je enkriptiran koištenjem high-level sustava za simetričnu enkripciju iz biblioteke [Fernet](https://cryptography.io/en/latest/fernet/) (do plaintext-a smo kasnije došli trial-and-error metodom). Trebalo je otkriti koji je naš enkriptirani dokument, pronaći ključ te pomoću njega dekriptirati spomenuti dokument. Nismo imali pristup enkripcijskom ključu, no na kraju smo uspjeli doći do rezultata - svakog od nas je čekala slika u png formatu s personaliziranom porukom.

![image](https://user-images.githubusercontent.com/92427754/201114595-bd15f2c8-fde8-480e-aad6-9992f1ec1906.png)
Dekriptirana slika

Na laptopima u laboratoriju smo ušli na link [http://challenges.local/](http://challenges.local/) gdje su nas čekale enkriptirane datoteke čiji su nazivi bili nečitki i nedokučivi, ali to su zapravo bila naša imena i prezimena. Svatko od nas je uz pomoć koda koji smo pisali s profesorom pronašao svoje ime i prezime. Koristili smo *Brute-force* napad za otkrivanje ključa ograničene entropije - 22 bita (22 bita znači da ukupno postoji 4.194.304 (2^22) kombinacija ključeva), kojom je bila enkriptirana naša datoteka. Nadalje, dio koda nam je omogućio da dođemo do konačnog cilja. To je bila dekripcija slike u png formatu (ovaj detalj nam je otkrio sam profesor). Unutar while petlje smo svakom iteracijom pomoću trenutnog ključa pokušali dekripcijskim algoritmom dekriptirati *ciphertext* te bismo tu vrijednost pridružili varijabli *plaintext* i pozvali bismo funkciju `test_png` kojoj smo slali prva 32 bita *plaintext*-a gdje smo provjeravali odgovaraju li oni headeru-u datoteke png formata. Ukoliko bi bilo poklapanja, funkcija bi vratila istinu i došlo bi do pucanja while petlje (`break`). Tako smo dobili ****originalni sadržaj i saznali ključ.

Zajedno s profesorom napisali smo sljedeći kod:

```python
import base64
from cryptography.fernet import Fernet
from os import path
from cryptography.hazmat.primitives import hashes

def hash(input):

    if not isinstance(input, bytes):
        input = input.encode()

    digest = hashes.Hash(hashes.SHA256())
    digest.update(input)
    hash = digest.finalize()

    return hash.hex()

def test_png(header):
    if header.startswith(b"\211PNG\r\n\032\n"):
        return True
    return False

def brute_force(ciphertext):
    ctr = 0
    while True:
        key_bytes = ctr.to_bytes(32, "big")
        key = base64.urlsafe_b64encode(key_bytes)

            # Now initialize the Fernet system with the given key
            # and try to decrypt your challenge.
            # Think, how do you know that the key tested is the correct key
            # (i.e., how do you break out of this infinite loop)?
        try:
            plaintext = Fernet(key).decrypt(ciphertext)
            header=plaintext[:32]

            if test_png(header):
                print(f"BINGO: {key}")
                with open("BINGO.png", "wb") as file:
                    file.write(plaintext)
                break
        except Exception:
                pass
        ctr += 1
        if not ctr & 1000:
            print(f"[*] Keys tested: {ctr:,}", end="\r")

filename = hash("prezime_ime") + ".encrypted"
if __name__ == "__main__":
    filename = hash("mamic_anamarija") + ".encrypted"

		#Spremiti naš plaintext u obliku datoteke
    if not path.exists(filename):
        with open(filename, "wb") as file:
            file.write(b"") 
   
    #Open your challenge file

    with open(filename, "rb") as file:
        ciphertext=file.read()

#print(ciphertext)
brute_force(ciphertext)
```

Programu je trebalo malo duže vremena da se izvrti zato što smo prilikom svake iteracije imali dekripciju. U konačnici smo dobili već spomenutu sliku i ispis:

```python
(ivonam) C:\Users\A507\im\vjezba3\ivonam\Scripts>python lab3.py
[+] KEY FOUND: b'AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAACCXk='
```

🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️🕵🏼‍♀️
