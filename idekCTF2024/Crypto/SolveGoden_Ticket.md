<img width="628" height="732" alt="image" src="https://github.com/user-attachments/assets/1d094bb7-1de3-41d2-99bf-d22190633873" />

### Code:

```
from Crypto.Util.number import *
from sympy.ntheory import discrete_log

p = 396430433566694153228963024068183195900644000015629930982017434859080008533624204265038366113052353086248115602503012179807206251960510130759852727353283868788493357310003786807

a = 88952575866827947965983024351948428571644045481852955585307229868427303211803239917835211249629755846575548754617810635567272526061976590304647326424871380247801316189016325247
b = 67077340815509559968966395605991498895734870241569147039932716484176494534953008553337442440573747593113271897771706973941604973691227887232994456813209749283078720189994152242

# Solve linear system:
# x + y = a
# 13x + 37y = b
# --> multiply first equation by 13 and subtract:
# 13x + 13y = 13a
# 13x + 37y = b
# ------------
# 24y = b - 13a --> y = (b - 13a) / 24

y = (b - 13 * a) * pow(24, -1, p) % p
x = (a - y) % p

# Now x = 13^(m-1), find discrete log
m_minus_1 = discrete_log(p, x, 13)
m = m_minus_1 + 1

print("m =", m)
print("flag =", long_to_bytes(m))
```

- Run my code, I have a flag:

<img width="878" height="42" alt="image" src="https://github.com/user-attachments/assets/4e13d353-d4f3-4d8e-8d9d-4ce7e10574cb" />

- Yes, this's my flag in this problem:

`
idek{charles_and_the_chocolate_factory!!!}
`
