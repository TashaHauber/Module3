import os, random
from Crypto.Cipher import AES
from Crypto.Hash import SHA256
 
def encrypt(key, filename):
    chunk_size = 64*1024
    output_file = filename+".enc"
    file_size = str(os.path.getsize(filename)).zfill(16)
    IV = ''
    for i in range(16):
        IV += chr(random.randint(0, 0xFF))
    encryptor = AES.new(key, AES.MODE_CBC, IV)
    with open(filename, 'rb') as inputfile:
        with open(output_file, 'wb') as outf:
            outf.write(file_size)
            outf.write(IV)
            while True:
                chunk = inputfile.read(chunk_size)
                if len(chunk) == 0:
                    break
                elif len(chunk) % 16 != 0:
                   chunk += ' '*(16 - len(chunk)%16)
                outf.write(encryptor.encrypt(chunk))
 
def decrypt(key, filename):
        chunk_size = 64*1024
        output_file = filename[:-4]
        with open(filename, 'rb') as inf:
            filesize = long(inf.read(16))
            IV = inf.read(16)
            decryptor = AES.new(key, AES.MODE_CBC, IV)
            with open(output_file, 'wb') as outf:
                while True:
                    chunk = inf.read(chunk_size)
                    if len(chunk)==0:
                        break
                    outf.write(decryptor.decrypt(chunk))
                outf.truncate(filesize)
 
def getKey(password):
    hasher = SHA256.new(password)
    return hasher.digest()
 
def main():
    choice = raw_input("Which would you like to do Encrypt (E) or Decrypt (D): \n>>>").lower()
    if choice == "E".lower():
        filename = raw_input("Enter the name of file to be encrypted: ")
        password = raw_input("Enter the password: ")
        encrypt(getKey(password), filename)
        print "Encrypted file name changed from: \n%s to %s"% (filename, filename+'.enc')
    elif choice == "D".lower():
        filename = raw_input("Enter the name of the file to be decrypted > ")
        password = raw_input("Enter the password: ")
        decrypt(getKey(password), filename)
        print "Decrypted file name is: \n%s ==> %s"%(filename, filename[:-4])
    else:
        print "No option Selected"
 
if __name__ == "__main__":
    main()
