# Local Buffer Overflow
## Fuzzing

```
python -c "print('A'*10000)"
python -c "print('A'*10000, file=open('fuzz.wav', 'w'))"
```

## Controlling EIP

### Creating Unique Pattern

```
/usr/bin/msf-pattern_create -l 5000
```

```
ERC --pattern c 5000
```
### Calculating EIP Offset

```
/usr/bin/msf-pattern_offset -q 31684630
```

Seleccionar EIP, botón derecho y modificar valor.

```
ERC --pattern o <ASCII value>
```
## Indetyfing Bad Characters





## Finding a Return Instruction



## Jumping to Shellcode


## Código del exploit

```

```
# Remote Buffer Overflow

## Remote Fuzzing




## Building a Remote Exploit



## Remote Exploitation