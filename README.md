## Infinitely growing configurations in Emil Post's tag system problem






This repository contains corresponding code for the paper ["Infinitely growing configurations in Emil Post's tag system problem"](https://arxiv.org/).
The code from this page is copied to the **main.py** file, which can be executed in Python3 without any additional libraries required.


-------------------------------------


Emil Post's tag system problem concerns the behavior of function **f** that takes a binary string, addes 1101 if a string starts with 1 (else addes 00), and then deletes the first 3 symbols:

```python
def f(a):
    if a[0] == '1':
        return (a + '1101')[3:]
    else:
        return (a + '00')[3:]

```

The question of the problem: is there a configuration that has an unbounded growth?

A presented configuration **B** (with supporting strings **A** and **C**) has this property. More precisely, the following statement is valid:

![alt text](https://github.com/nikitakurilenko/post-tag-infinitely-growing-configurations/blob/main/images/theorem1.png)

These strings **A**, **B** and **C** are:

```python
A = '000011011101110100'
C = '000000110111011101000000110111011101000000110111011101'
B = '00000011011101000000110100110100001101110111011101110111010000001101110111010000001101110111010000001101110111010000001101110111010011011101110111011101001101110111011101110100000000001101110111010000110111010000001101110111010000001101110111010000001101110111010000000011011101110111010011011101110100001101110100110111011101110111010011010011011101110100000011011101110100000011011101110100000011011101110111011101001101000011011101001101000011011101001101000011011101001101000011011101001101000000000011010011010000001101110111010000000000000011010000000000000011011101110100000011011101110100000011011101110111011101001101001101001101001101110111010000001101110111010011011101110111011101001101110111011101110100110111011101110111010011011101110111011101000000000000001101110111010000001101110111010011010011010000001101110111010000000011011101000000001101110100000000110111010000000000000000000000001101110100110100001101110100110100110111011101001101110111011101110100110100001101110100110100110100110111011101000000110111011101000000001101110100110100001101110100110100110111011101001101110111011101110100110100001101110100110100001101110100110100001101110100110100001101110100110111011101000000000011011101110100000011011101110100000011011101110100110111011101000011011101000000001101110100110111011101000000110111011101110111010011011101110100110111011101110111010000001101110111010000001101110111010000001101110111011101110111011101110111010011010000110111010011010000001101000000000011011101110100000011011101110100000011011101110100110111011101001101001101000000000011011101110111011101110111010011010011011101110100000011011101110100110111011101001101001101001101110111010000001101110111010011010011010000001101001101001101001101000000110111011101110111010011011101110100000011011101110100110100001101110100110100000011011101110111011101001101110111011101110100110100001101110100110100000011010000001101110111011101110100000011011101110100000011011101110100000000001101110111010000001101110111011101110100110111011101001101110111010011010000110111010011010000110111010011010011011101110100000000001101110111010000001101110111010000001101110111010000001101110111010000000000110100000000000000000000001101110111011101110100110100110100110100001101110111011101000000000000001101001101000011011101110111010000000011011101110111010011010000110111010011010000110111010011010000000000110111011101'
```

Checking the statement above for the first 100 values for **n** and **m** is easy:

```python
def is_evolving(a, b):
    for step in range(int(1e6)):
        a = f(a)
        if a == b:
            return True
    return False

n = 100
for i in range(n):

    if i % 10 == 0:
        print(i, end=' ')

    for j in range(n):
        assert is_evolving(A*i     + B + C*j,
                           A*(i+1) + B + C*(j+1))

print('Checked')
'''
0 10 20 30 40 50 60 70 80 90 Checked
'''
```

Verifying this statement for all other values requires a procedure described in the paper, and involves several new functions and predetermined string of numbers.

A function **g** replaces every third element of an input string with 1101 if an element is 1, else replaces it with 00.

```python
def g(a):
    out = ''
    for ch in a[::3]:
        out += '1101' if ch is '1' else '00'
    return out
```

A function **l** returns the length of a string modulo 3.

```python
def l(a):
    return len(a) % 3
```

A function **F** iteratively applies function **f** until all symbols of the initial string are deleted.

```python
def F(a):
    while '0' in a or '1' in a:
        if a[0] == '1':
            a += 'yyxy'
        else:
            a += 'xx'
        a = a[3:]
    return a.replace('x', '0').replace('y', '1')
```

**w** is a set of 42 predetermined strings (all can be viewed in **data.py**), **x** is a list of 14 predetermined values.

```python
from data import w
x = [0, 1, 0, 2, 1, 0, 1, 0, 1, 2, 0, 0, 1, 0]
```

The verification procedure is described in a paper as the following statement:

![alt text](https://github.com/nikitakurilenko/post-tag-infinitely-growing-configurations/blob/main/images/lemma4.png)

All conditions from the theorem can be implemented into the function **verify**:

```python
def verify(a, b, c, x, d, e, f, y):

    if (x - l(b)) % 3 != y:
        return False

    if l(a) != 0 or l(c) != 0:
        return False

    if g(a[x:]) != d or g(b[x:]) != e or g(c[y:]) != f:
        return False

    return True
```

In order to prove the main statement it is required to prove the following lemma for **w** (omega in the paper).


![alt text](https://github.com/nikitakurilenko/post-tag-infinitely-growing-configurations/blob/main/images/lemma5.png)


We can use the function **verify** to prove condition *(i)* ...

```python
for i in range(13):

    print(i, end=' ')

    assert verify(w[i][0], w[i][1], w[i][2], x[i],
                  w[i+1][0], w[i+1][1], w[i+1][2], x[i+1])

print('Verified')
'''
0 1 2 3 4 5 6 7 8 9 10 11 12 Verified
'''
```

... or, in addition, we can check *(i)* for the first 100 values of n and m.


```python
for i in range(13):

    print(i, end=' ')

    for n in range(100):
        for m in range(100):
            s_0 = w[i][0]*n + w[i][1] + w[i][2]*m
            s_1 = w[i+1][0]*n + w[i+1][1] + w[i+1][2]*m
            assert F(s_0[x[i]:]) == s_1[x[i+1]:]

print('Checked')
'''
0 1 2 3 4 5 6 7 8 9 10 11 12 Checked
'''
```

Condition *(ii)* is the easiest to verify.

```python
print(A == w[0][0])
print(B == w[0][1])
print(C == w[0][2])
print(w[13][0] == w[0][0])
print(w[13][1] == w[0][0] + w[0][1] + w[0][2])
print(w[13][2] == w[0][2])
'''
True
True
True
True
True
True
'''
```




