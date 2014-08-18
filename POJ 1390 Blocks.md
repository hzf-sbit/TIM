题目：[here][1]

刘老师黑书上的题，模型确实是经典!

##题意
不多说。。

--------------

DP中对于状态的设计是非常重要的，在这道题中就体现了出来，如果按照传统思路设计状态定将无法转移或付出高昂的时空代价，巧妙之处在于通过游程编码来表示状态，这样就可以轻松的表示出状态来，从而进行转移。

------------------

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#define rep(_i, _j) for(int _i = 1; _i <= _j; ++_i)
const int inf = 0x3f3f3f3f;
typedef long long LL;
typedef double DB;
using namespace std;

const int maxn = 200 + 20;
int n, m;
int col[maxn];
vector<pair<int, int> > pack;
int f[maxn][maxn][maxn];

#define len second
#define color first
#define sqr(x) ((x) * (x))
inline void dfs(int i, int j, int k) {
	if(j < i || f[i][j][k] != 0) return ;
	f[i][j][k] = sqr(k + pack[j].len);
	if(i != j) {
		dfs(i, j - 1, 0);
		f[i][j][k] += f[i][j - 1][0];
		for(int t = i; t < j; ++t) if(pack[t].color == pack[j].color) {
			dfs(i, t, k + pack[j].len), dfs(t + 1, j - 1, 0);
			f[i][j][k] = max(f[i][j][k], f[i][t][k + pack[j].len] + f[t + 1][j - 1][0]);
		}
	}
}

int main() {
	int Ranma;
	scanf("%d", &Ranma);
	for(int Akane = 1; Akane <= Ranma; ++Akane, pack.clear(), memset(f, 0, sizeof f)) {
		scanf("%d", &n);
		for(int i = 1; i <= n; ++i) {
			scanf("%d", &col[i]);
		}
		for(int i = 1, j; i <= n;) {
			for(j = i; col[j] == col[i] && j <= n; ++j);
			pack.push_back(make_pair(col[i], j - i));
			i = j;
		}
		/*
		for(int i = 0, sz = pack.size(); i < sz; ++i) {
			printf("%d %d\n", pack[i].first, pack[i].second);
		}
		puts("");
		*/
		m = pack.size();
		memset(f, 0, sizeof f);
		dfs(0, m - 1, 0);
		printf("Case %d: %d\n",Akane, f[0][m - 1][0]);
	}

	return 0;
}
```


  [1]: http://poj.org/problem?id=1390