# Corny Kernel - uiuctf 2023
## Writeups by er4pwn
Challange description:

![dsaaga]https://raw.githubusercontent.com/jihyoppa/CTF_writeup/main/UIUCTF2023/CornyKernel/Screenshot%202023-07-03%20182032.png
```py
with open('c2', 'rb') as f:
    c2 = f.read(1024)

with open('p2', 'rb') as f:
    p2 = f.read(1024)

assert len(p2) == len(c2)

pad = [0 for _ in range(len(c2))]

for i in range(len(p2)):
    pad[i] = c2[i] ^ p2[i]

with open("c1", 'rb') as f:
    c1 = f.read(1024)
    for i in range(len(c1)):
        print(chr(c1[i] ^ pad[i]),end='')

print(p2.decode(), end=' ')
with open("c3", 'rb') as f:
    c3 = f.read(1024)
    for i in range(len(c3)):
        print(chr(c3[i] ^ pad[i]),end='')
```
We run it and we get the following:

```
$ python3 ./solve.py       
before computers, one-time pads were sometimesprinted on flammable material so that spies could uiuctf{burn_3ach_k3y_aft3r_us1ng_1t}                       
```

And we got that sweet flag! Hooray!
