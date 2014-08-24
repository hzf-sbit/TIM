#208. Toral Tickets

    time limit per test: 0.25 sec.
    memory limit per test: 65536 KB
    input: standard
    output: standard



On the planet Eisiem passenger tickets for the new mean of transportation are planned to have the form of tores. 

Each tore is made of a single rectangular black rubber sheet containing N × M squares. Several squares are marked with white, thus encoding the ticket's source and destination. 

When the passenger buys the ticket, the ticket booking machine takes the rubber sheet, marks some squares to identify the route of the passenger, and then provides it to the passenger. The passenger next must glue the ticket. 

The ticket must be clued the following way. First two its sides of greater length are glued together, forming a cylinder. Next cylinder base circles, each of which has the length equal to the length of the short side of the original rubber sheet, are glued together. They must be glued in such a way, that the cells, sides of which are glued, first belonged to the same row of the sheet. Note that the inner and the outer part of the sheet can be distinguished. 

The resulting tore is the valid ticket. 

Note that if the original sheet is square, there are two topologically different ways to make a tore out of a rubber sheet. 

Ticket material is so perfect and gluing quality is so fine, that no one is able to find the seam, and this leads to some problems. First, the same tore can be obtained using different sheets. More of that, the same sheet can lead to tores that look a bit different. 

Now the transport companies of Eisiem wonder, how many different routes they can organize, so that the following conditions are satisfied: 


tickets for different routes are represented by different tores; 
if some rubber sheet was marked to make the tore for some route, it cannot be used to make the tore for another route. 


Help them to calculate the number of routes they can organize. 

##Input
The first line of the input file contains N and M (1 ≤ N, M ≤ 20). 

##Output
Output the number of routes Eisiem transport companies can organize. 

##Sample test(s)

###Input
####Test #1 

    2 2 

####Test #2 

    2 3 

###Output
####Test #1 

    6 

####Test #2 

    13 
    
-----------------

##题意

给你N和M，对于一个N*M的单面方格纸你可以对它的每个个格子黑白染色，然后把方格纸的长边卷起来，卷成一个圆柱体，然后再把两个短边形成的圆也接起来，形成一个游泳圈的形状（我们染的色只在游泳圈的外表面）。如果对于两种黑白染色方案，通过卷成这样的游泳圈后，是一样的，则这两种方案也是一样的。给定N，M<=20 ，求染色方案总数.
<div class="text clearfix" style="text-align:center;"><span style="float:right;">——引用自学长xpd</span></div>


--------------------

很明显的polya计数，不会的话下面列出了一些参考：

1. 刘老师的黑书
2. [http://wenku.baidu.com/view/bf92a95f804d2b160b4ec0be.html][1]
3. [http://zhangchi.weebly.com/uploads/8/7/5/5/8755757/polya.pdf][2]

<del>很好懂，如果每天都大声朗读一遍。</del>

需要高精度。

```cpp
#include <bits/stdc++.h>
#define rep(_i, _j) for(int _i = 1; _i <= _j; ++_i)
const int inf = 0x3f3f3f3f;
typedef long long LL;
typedef double DB;
using namespace std;

const int maxm = 20 + 2;
const int maxn = 400 + 20;
const int maxlen = 140 + 10;
struct big_num {
	int d[maxlen], len;
	big_num() {
		memset(d, 0, sizeof d);
		len = 1;
	}
	inline int & operator [] (int index) {
		return d[index];
	}
	friend big_num operator + (big_num lhs, big_num rhs) {
		big_num ret;
		ret.len = max(lhs.len, rhs.len);
		for(int i = 1; i <= ret.len; ++i) {
			ret[i] += lhs[i] + rhs[i];
			if(ret[i] > 9) {
				ret[i] %= 10, ret[i + 1] += 1;
			}
		}
		if(ret[ret.len + 1] > 0) ++ret.len;
		return ret;
	}
	friend big_num operator / (big_num lhs, int rhs) {
		big_num ret;
		int remain = 0;
		for(int i = lhs.len; 0 < i; --i) {
			remain = remain * 10 + lhs[i];
			if(remain >= rhs) {
				ret[i] = remain / rhs;
				remain %= rhs;
			}
		}
		for(ret.len = maxlen - 1; 1 < ret.len; --ret.len) {
			if(ret[ret.len] > 0) break;
		}
		return ret;
	}
	void print() {
		for(int i = len; 0 < i; --i)
			printf("%d", d[i]);
	}
};
big_num pow2[maxn], ans;

int n, m;
int cache[maxn], hash[maxn];
int Ranma[maxm][maxm];

void right_shift() {
	for(register int i = 0, t; i < n; ++i) {
		t = Ranma[i][m - 1];
		for(int j = m - 1; 0 < j; --j) {
			Ranma[i][j] = Ranma[i][j - 1];
		}
		Ranma[i][0] = t;
	}
}
void down_shift() {
	for(register int i = 0, t; i < m; ++i) {
		t = Ranma[n - 1][i];
		for(int j = n - 1; 0 < j; --j) {
			Ranma[j][i] = Ranma[j - 1][i];
		}
		Ranma[0][i] = t;
	}
}
void rot() {
	int tmp[maxm][maxm];
	memcpy(tmp, Ranma, sizeof Ranma);
	for(int i = 0; i < m; ++i) {
		for(int j = n - 1; -1 < j; --j) {
			Ranma[i][n - 1 - j] = tmp[j][i];
		}
	}
	swap(n, m);
}

int calc() {
	int ret = 0;
	memset(hash, 0, sizeof hash);
	for(int i = 0; i < n; ++i) {
		for(int j = 0; j < m; ++j) {
			cache[i * m + j] = Ranma[i][j];
		}
	}
	for(register int i = 0, uper = n * m, now; i < uper; ++i) {
		if(!hash[i]) {
			++ret;
			for(now = i; !hash[now]; hash[now] = 1, now = cache[now]);
		}
	}
	return ret;
}
void print() {
	for(int i = 0; i < n; ++i) {
		for(int j = 0; j < m; ++j) {
			printf("%d ", Ranma[i][j]);
		}
		puts("");
	}
	puts("");
}
int main() {
#ifndef ONLINE_JUDGE
	freopen("208.in", "r", stdin); freopen("208.out", "w", stdout);
#endif

	pow2[0].d[1] = 1;
	for(int i = 1; i < maxn; ++i)
		pow2[i] = pow2[i - 1] + pow2[i - 1];
	scanf("%d%d", &n, &m);
	for(int i = 0; i < n; ++i) {
		for(int j = 0; j < m; ++j) {
			Ranma[i][j] = i * m + j;
		}
	}
	for(int i = 0; i < n; ++i) {
		for(int j = 0; j < m; ++j) {
			ans = ans + pow2[calc()];
			rot();
			if(n == m) ans = ans + pow2[calc()];
			rot();
			ans = ans + pow2[calc()];
			rot();
			if(n == m) ans = ans + pow2[calc()];
			rot();
			right_shift();
		}
		down_shift();
	}
	ans = ans / (n * m * 2 * (n == m ? 2 : 1));
	ans.print();
	puts("");

	return 0;
}


```




  [1]: http://wenku.baidu.com/view/bf92a95f804d2b160b4ec0be.html
  [2]: http://zhangchi.weebly.com/uploads/8/7/5/5/8755757/polya.pdf
  
