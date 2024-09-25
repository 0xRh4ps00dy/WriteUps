# Local Buffer Overflow
## Fuzzing

```
python -c "print('A'*10000)"
python -c "print('A'*10000, file=open('fuzz.wav', 'w'))"
```

## Control del EIP

### Creando un patrón único

```
/usr/bin/msf-pattern_create -l 5000
```

```
ERC --pattern c 5000
```
### Cálculo de la compensación EIP

```
/usr/bin/msf-pattern_offset -q 31684630
```

Seleccionar EIP, botón derecho y modificar valor.

```
ERC --pattern o <ASCII value>
```
## Indentificando personajes malos





## Encontrar una instrucción de devolución



## Saltar a Shellcode


# Remote Buffer Overflow

## Fuzzing remoto




## Construyendo un exploit remoto



## Explotación remota