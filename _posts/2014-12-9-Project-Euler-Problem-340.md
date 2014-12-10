---
layout: post
title: Project Euler Problem 340
tags: ["Python", "Mathematics", "Project Euler"]
year: 2014
month: 12
day: 9
---

When I first attended the [Houston Python Meetup Group](http://www.meetup.com/python-14/), someone mentioned a site called [Project Euler](https://projecteuler.net/), which includes a large number of math problems. However, they aren't just math problems in the usual sense. Most, can't be solved explicitly and require the use of some kind of programming language to complete. However, the main issue is that you must be clever enough to devise an efficient enough algorithm to use so that your computer can actually finish in a reasonable amount of time. So in short, one must combine a bit of mathematical know-how with a bit of programming.

I should point out that many of the problems posted involve combinatorics or number theory. And thus it is often necessary to be able to work with infinite precision integers. Fortunately, Python automatically switches to infinite precision integers as needed, which is one reason I decided to use the language in attacking some of these problems.

So far I've finished about 25 or so of them, and I've posted my codes [here](https://github.com/hubenjm/Project-Euler). At some point I may resume attempting some more, but who knows when that will happen.

The point of this post is to analyze [problem 340](https://projecteuler.net/problem=340), entitled **Crazy Function**. Let me reprint the gist of the problem below:

---

For fixed integers \\(a, b, c\\), define the *crazy function* \\(F(n)\\) as follows:
\\(F(n) = \left\\{ \begin{array}{cc} n - c & \textrm{for all }n > b \\\
F(a + F(a + F(a + F(a + n)))) & \textrm{for all }n \leq b \end{array}\right.\\)
 
Also, define $$S(a, b, c) = \sum_{n=0}^{b}F(n).$$ 
 
For example, if \\(a = 50\\), \\(b = 2000\\) and \\(c = 40\\), then \\(F(0) = 3240\\) and \\(F(2000) = 2040\\). 
 
Also, \\(S(50, 2000, 40) = 5204240\\).
 
Find the last \\(9\\) digits of \\(S(21^{7}, 7^{21}, 12^{7})\\).

---

##Solution##

It's relatively easy to write a simple function in whatever language you want to directly compute \(F(n)\) as defined above. However, due to the quadruple recursion, it would take eternity to find \\(S(21^{7}, 7^{21}, 12^{7})\\), let alone the last \\(9\\) digits.

Fortunately, there is a way to significantly simplify the formula for \\(S(a,b,c)\\) to the point where there is no recursion and the computation is boils down to a single operation. Here is the Python code to compute the last \\(9\\) digits of \\(S(21^{7}, 7^{21}, 12^{1})\\). The function <code>S_red</code> is short for a *reduced* version of the function \\(S(a,b,c)\\).

{% highlight python %}
def S_red(q,a,b,c):
	m = b/a
	a = a%q
	b = b%q
	c = c%q

	S1 = (4*m*a*(a-c) + m*a*b - m*a*(a-1)/2)%q
	S2 = ((m*(m-1)/2)*3*a*(a-c))%q
	S3 = ((b - m*a + 1)*(4*(m+1)*a - (3*m+4)*c) + (b - m*a)*(b - m*a + 1)/2)%q

	return (S1+S2+S3)%q

def main():
	a = 21**7
	b = 7**21
	c = 12**7
	q = 10**9

	print(S_red(q,a,b,c))
{% endhighlight %}

##Explanation##

The key idea starts by noticing that for \\(n \in (b - a, b]\\) we have \\(a + n > b\\) and so \\(F(a + n) = a + n -c\\). Assuming that \\(a > c\\), we have 
\begin{align}
F(n) & = F(a + F(a + F(a + F(a + n))))\\\
& = F(a + F(a + F(a + a+n-c)))\\\
& = F(a  + F(a + 2a + n - 2c)) \\\
& = F(a + 3a + n - 3c) \\\
& = 4a + n - 4c.
\end{align}
Inductively, one can show that for \\(n \in (b - (k+1)a, b -ka]\\) we have \\[F(n) = n + 4(k+1)a - (3k + 4)c\\].

We now break up the computation of \\(S(a,b,c) = \sum_{n=0}^{b}F(n)\\) into partial sums over subintervals of the form \\( \\{ b-(k+1)a + 1, b-(k+1)a + 2, \ldots, b-ka\\} \\) where the above formula can be applied.

Let \\[m = \left\lfloor \frac{b}{a} \right\rfloor \\] and note that
\begin{align}
\sum\_{n=0}^{b - ma} F(n) & = \sum\_{n=0}^{b-ma}\left(4(m+1)a - (3m+4)c + n \right)\\\
& = (b - ma + 1)(4(m+1)a - (3m+4)c) + \frac{(b-ma)(b-ma+1)}{2}
\end{align}
This handles the first part of the sum defining \\(S(a,b,c)\\). 

Next, observe that
\begin{align}
\sum\_{n=b - (k+1)a +1}^{b-ka}F(n) & = \sum\_{n=b - (k+1)a +1}^{b-ka}\left( n + 4(k+1)a - (3k + 4)c\right)\\\
& = \frac{(b-ka)(b-ka + 1) - (b - (k+1)a)(b - (k+1)a + 1)}{2}\\\
& \quad + 4(k+1)a^{2} - (3k + 4)ac\\\
& = ab - \frac{a(a-1)}{2} + (3k+4)a(a - c)\\\
& =: P_{k}  
\end{align}

We then compute
\begin{align}
S(a,b,c) & = \sum\_{n=0}^{b - ma}F(n) + \sum\_{k=0}^{m-1}P_{k}\\\
& = (b - ma + 1)(4(m+1)a - (3m+4)c) + \frac{(b-ma)(b-ma+1)}{2}\\\
& + \quad \sum\_{k=0}^{m-1}\left(ab - \frac{a(a-1)}{2} + (3k+4)a(a - c)\right)\\\
& = (b - ma + 1)(4(m+1)a - (3m+4)c) + \frac{(b-ma)(b-ma+1)}{2}\\\
& + \quad m\left(ab - \frac{a(a-1)}{2} \right) + \frac{3m(m-1)}{2}a(a-c) + 4ma(a-c).
\end{align}

Since we only need the last \\(9\\) digits, we can first compute the remainder modulo \\(10^{9}\\) of each term above and then add them together. The whole process is quite fast.


The moral of this story is that we had to de-recursify a function which is quite unwieldly, but still not ridiculous to deal with.
