---
layout: post
title: Birthday Paradox
subtitle: In a Room, Do Two People Share a Birthday?
tags: [mathematics, birthday paradox, python, mathematica]
---

What is the probability that, in a set of \\(n\\) randomly chosen people, at least two people share a birthday? The question is innocent, but the [birthday paradox](https://en.wikipedia.org/wiki/Birthday_problem) says that the probability exceeds \\(50\%\\) in a set of just 23 people. The result is counter-intuitive, as the product of probabilities is often counter-intuitive, but it's important to remember that there are \\((23 \times 22)/2 = 253\\) pairs for a set of 23. Mathematically, in a set of \\(n\\) people, the probability that at least two people share a birthday is:

$$P(n) = 1 - \prod_{i = 0}^{n - 1} \frac{365 - i}{365},$$

for which we detail some specific probabilities:

| \\(n\\) | \\(P\\) (%)    |
|:------: | :------------: |
| 1       | 0              |
| 5       | 2.7            |
| 10      | 11.7           |
| 15      | 25.3           |
| 20      | 41.1           |
| 23      | 50.7           |
| 70      | 99.9           |

## Python Implementation

{% highlight Python linenos %}
import numpy as np
import matplotlib.pyplot as plt

def pTwoShareBday(num):
    
    p_ar = (365 - np.arange(num))/365
    prod = 1
    prod_ar = []
    for elem in p_ar:
        prod = prod * elem
        prod_ar = np.append(prod_ar, prod)
    print("The probability that at least 2 people share a birthday in a set\
of {:.0f} (randomly chosen) people is {:.2f}%.".format(num, 100*(1 - prod)))
    
    plt.figure()
    plt.plot(np.linspace(1, num, num), (1 - prod_ar), '.r')
    plt.xlabel('Number of People')
    plt.ylabel('Probability that at least 2 Share a B-Day')
#     plt.savefig(f'bday-paradox-{num}.jpg',bbox_inches='tight', dpi=150)
    plt.show()
{% endhighlight %}

| ![Birthday Paradox up to 23 People](/assets/img/python-bday-paradox-23.jpg) |
| :------------------------------------------: |
| Birthday Paradox up to 23 People             |

| ![Birthday Paradox up to 100 People](/assets/img/python-bday-paradox-100.jpg) |
| :-------------------------------------------:  |
| Birthday Paradox up to 100 People              |

## Mathematica Implementation

{% highlight Mathematica linenos %}
pTwoShareBday = 1 - Product[((365 - i)/365), {i, 0, n - 1}];
ListPlot[Table[pTwoShareBday, {n, 1, 100}], 
 AxesLabel -> {"# of People in a Room", "P(2 Share a Birthday)"}]
BdayList = Table[N[Refine[pTwoShareBday, n == k]], {k, 1, 23}]
{% endhighlight %}
