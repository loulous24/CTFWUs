# Q-Rious Transmissions - UIUCTF 2021

Par *loulous*

> Alice is stuck in the bygone era of classical computing, while Bob is using quantum. She wants to show Bob a cool picture so she decides to give quantum a chance. Bob and Alice share a quantum state. Alice can't figure out how to send qubits, so she shares details of each operation instead. The initial state is 1/sqrt(2)*(|00> + |11>)

> Attached document : 
> ```
> ZX I I I I I I I I I [...]
> ```

Alice shares quantum data. Quantum data are encoded in binary. 0 is represented by $\left|0\right>$ and 1 is represented by $\left|1\right>$. The big difference with classical physics and quantum mechanics is the quantum superposition. So, $\frac{\left|0\right>+\left|1\right>}{\sqrt{2}}$ is a valid state. (The square root of 2 is used for normalisation.)

Another key concept of quantum mechanics is the role of the observer. The result of an observation of a superposed state gives either $\left|0\right>$ or $\left|1\right>$. In the preceding superposed states, the probability of having $\left|0\right>$ or $\left|1\right>$ are equal. Quantum data are not sure and are probabilistic. Nevertheless, an observation of a superposed state breaks the superposition. It's the famous [Schrödinger's cat experiment](https://en.wikipedia.org/wiki/Schr%C3%B6dinger%27s_cat). A qubit is at the same time  $\left|0\right>$ and $\left|1\right>$ but when you open the box, that is do an observation, the qubit is either $\left|0\right>$ or $\left|1\right>$. However, it does not mean that before the observation, the qubit was in one of these states. Before the observation, it was in a superposed state $\frac{\left|0\right>+\left|1\right>}{\sqrt{2}}$. Not 0 or 1 but 0 and 1. The observation changes the state.

It is a huge advantage. Alice and Bob can detect an observer because he is perturbating the states of the qubit and force it to have a determinate state. It explains the interest for quantum communication protocol that protect against eavesdropping.

Having several qubits at the same time is possible. The conventional notation is $\left|0\right> \otimes \left|0\right>$ which can be written $\left|00\right>$. Here, both qubits are 0. Superposing states with 2 qubits to create another state is also possible. For instance, $\frac{\left|00\right>+\left|11\right>}{\sqrt{2}}$ is a superposed state. If Alice and Bob observe the qubits and Alice knows the value of the first qubits, she will know the result of Bob, it will be the same. When the qubits are not independent, they are entangled.

As we have strongest explanations of common quantum phenomena, let us tackle the problem. There is not much data except a sequence of `I`, `X` and `Z`. Actually, it is a code to represent quantum logic gates https://en.wikipedia.org/wiki/Quantum_logic_gate. Let us see how they work on one qubit.

`I` is the identity. It does nothing.

`X` is a Pauli gate. It maps $\left|0\right>$ to $\left|1\right>$ and $\left|1\right>$ to $\left|0\right>$.

`Z` is another Pauli gate that maps $\left|1\right>$ to $-\left|1\right>$ and leaves $\left|0\right>$ unchanged.

You can combine these gates to make new functions which are more complex. So, `ZX` maps $\left|0\right>$ to $-\left|1\right>$ and $\left|1\right>$ to $\left|0\right>$. The first gate is the right one so it begins with `X` then `Z`.

They are working only for one qubit whereas here, there are two. You can suppose that gates are only for the first qbit and do nothing to the second. It corresponds to the notation $(G \otimes I)$ wher $G$ is for the first qubit et $I$ for the second (and does nothing).

$(ZX \otimes I)(\left|00\right>) = -\left|10\right>$
$(ZX \otimes I)(\left|11\right>) = +\left|01\right>$

They are linear so applying a gate on a sum of states is the same thing as the sum of each state after applying the gate.

$$(ZX \otimes I)\left(\frac{\left|00\right> + \left|11\right>}{\sqrt{2}}\right) = \frac{\left|01\right>-\left|10\right>}{\sqrt{2}}$$

We understand how Alice use doors for data encoding. However, as she uses entangled qubits, Bob cannot observe them.

$\left|00\right>$, $\left|01\right>$, $\left|10\right>$ and $\left|11\right>$ qubits have a problem. If you observe them, you do not change them because they are determinate states. To do a communication channel, you have to transform them in states like $\frac{\left|00\right> + \left|11\right>}{\sqrt{2}}$ that are not determinate.
[This paper](https://benjaminwhiteside.com/2020/11/15/hadamard-cnot/) explains how to get entangled qubits from the determinate basis of qubits ($\left|00\right>$, $\left|01\right>$, $\left|10\right>$, $\left|11\right>$). Alice has to use an Hadamard gate H on the first qubit and a cNOT gate. After the transmission, Bob has to do operations backwards. As these gates are reversible and are their inverse. Bob has to use a cNOT gate then an Hadamard gate to find the initial state.

For instance, if we receive $\frac{\left|00\right> + \left|11\right>}{\sqrt{2}}$, we will have $$cNOT\left(\frac{\left|00\right> + \left|11\right>}{\sqrt{2}}\right) = \frac{\left|00\right> + \left|10\right>}{\sqrt{2}} = \left(\frac{\left|0\right> + \left|1\right>}{\sqrt{2}}\right) \otimes \left|0\right>$$

$$(H \otimes I)\left(\left(\frac{\left|0\right> + \left|1\right>}{\sqrt{2}}\right) \otimes \left|0\right>\right) = H\left(\frac{\left|0\right> + \left|1\right>}{\sqrt{2}}\right) \otimes \left|0\right> = \left|0\right> \otimes \left|0\right> = \left|00\right>$$

After $(ZX \otimes I)$, (the first gate of Alice), Bob will have \frac{\left|01\right>-\left|10\right>}{\sqrt{2}} and then

$$(H \otimes I) . cNOT\left(\frac{\left|01\right>-\left|10\right>}{\sqrt{2}}\right) = \left|11\right>$$

Bingo! We have found qubits that are determinate. Alice has begun with these states.

`ZX` maps $\left|00\right>$ to $\left|11\right>$ and the other way around because $(ZX \otimes I)\left(\frac{\left|01\right> - \left|10\right>}{\sqrt{2}}\right) = -\frac{\left|00\right>+\left|11\right>}{\sqrt{2}}$ (minus sign for a state does not change).

`I` does not make any change. During the transmission, there are also `X` gates followed by `Z` gates. It is the same thing as `ZX` with a transitional state after the `X` gate. To make it clearer, I will write $\left|00\right>$ for the qubits after an Hadamard gate and a cNOT gate. After passing through `H` and `cNOT` $\left|00\right>$ becomes $\left|01\right>$ through `X` then $\left|11\right>$ through `Z` and $\left|11\right>$ becomes $\left|10\right>$ before $\left|00\right>$.

![](https://i.imgur.com/i7umrjf.png)

And we deduce a simple algorithm to apply by beginning with `00`.

```python
from PIL import Image

with open('quantum.txt', 'r') as fichier:
	data = fichier.readline()
gates = data.split()

states = [(0,0), (0,1), (1,1), (1,0)]
colors = [(0, 0, 0),(255, 255, 255)]
w, h = 200, 200
img = Image.new('RGB', (w, h))

qubit = 0
i = 0

for y in range(h):
    for x in range(0,w,2):
        if gates[i] == 'ZX':
            qubit = (qubit + 2) % len(states)
        elif gates[i] == 'X' or gates[i] == 'Z':
            qubit = (qubit + 1) % len(states)
        img.putpixel((x, y), colors[states[qubit][0]])
        img.putpixel((x+1, y), colors[states[qubit][1]])
        i += 1

img.save('quantum.png')
```

And we get a QR-code.

![](https://i.imgur.com/iMIkaII.png)

`uiuctf{5up3rqu4ntum_1m4g3ry}`

If you want to go further, I will fully recommend reading this [lesson](https://www.scottaaronson.com/qclec.pdf) to you.


This work is under [CC-BY-NC-SA 4.0](http://creativecommons.org/licenses/by-nc-sa/4.0/) License.
