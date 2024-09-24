```
python -c "print('A'*10000)"
python -c "print('A'*10000, file=open('fuzz.wav', 'w'))"

/usr/bin/msf-pattern_create -l 5000

ERC --pattern c 5000
```

```
def eip_offset():
    payload = bytes("Aa0Aa1Aa2Aa3Aa4Aa5...Gj7Gj8Gj9Gk0Gk1Gk2Gk3Gk4Gk5Gk", "utf-8")
```