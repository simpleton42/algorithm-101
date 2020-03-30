# 2020-03-30

## 문제

남극기지: https://algospot.com/judge/problem/read/ARCTIC

## 문제 해결 과정

이 문제는 shortest-path 의 변형으로 이해할 수 있고, 따라서 Dijkstra 알고리즘을 사용해 해결 할 수 있다. 본부에서 시작하여, 최소 출력이 필요한 기지를 하나씩 연결해 나아가고, 모든 기지가 연결되었을때 필요한 최소의 출력을 알 수 있다.

알고리즘이 어떻게 작동하는지 이해하기 위해서, 첫 몇 스텝을 살펴보자.

- (본부를 포함한) 기지의 수를 n 이라고 하자. (본부는 기지0 이라고 하자.)
- 기지i 와 기지j 간의 거리를 $d_{i,j}$ 라고 하자.
1. 본부와 기지i를 직접 연결하기 위해서는 $D_i=d_{i0}$ 의 출력이 필요하다.
1. 본부에 기지를 하나라도 연결하는데 필요한 최소 출력은 $D_i$의 최소값이다. 이 기지를 기지$j_1$ 이라고 하자.
1. 기지$j_1$가 연결되고 나면, 다른 기지들은 본부나 기지$j_1$ 중 아무곳에나 연결해도 된다. 기지$i$ 를 연결하는데 필요한 출력은 $D_{i}' = \min (d_{i,0}, d_{i, j_1})$ 가 된다.
1. 다음 연결할 기지는 $D_{i}'$ 가 최소인 기지이다. 이를 기지$j_2$ 라고 하자.
1. 이제 다른 모든 기지들은 $0$, $j_1$, 혹은 $j_2$ 에 연결할 수 있다.
1. ...

알고리즘은 다음의 pseudocode 로 표현 할 수 있다.
```
q <- vertices
for v in vertices:
    power[v] <- distance[v,0]

while q is not empty:
    pick vertex u with minimum power
    remove u from q
    update power[v] for all vertices in q 
return max(power)
```

## 문제 해답

```python
import math

def distance(r1, r2):
  return math.sqrt((r1[0] - r2[0])**2 + (r1[1] - r2[1])**2)

def minimum_power(coords):
  n = len(coords)
  dist_matrix = [[0 for j in range(n)] for i in range(n)]
  for i in range(n):
    for j in range(i+1, n):
      dij = distance(coords[i], coords[j])
      dist_matrix[i][j] = dij
      dist_matrix[j][i] = dij
    
  q = set(range(n))
  power = [dist_matrix[0][v] for v in range(n)]
  power[0] = 0
  
  while q:
    min_u = -1
    min_power = -1
    for u in q:
      if min_u < 0 or power[u] < min_power:
        min_u, min_power = u, power[u]
    u = min_u
    q.remove(u)

    for v in q:
      if dist_matrix[u][v] < power[v]:
        power[v] = dist_matrix[u][v]
  return max(power)

def problem():
  n = int(input())
  coords = []
  for i in range(n):
    line = input()
    x, y = [float(token) for token in line.split()]
    coords.append([x,y])
  
  print('%.2f' % minimum_power(coords))
  

def main():
  n_cases = int(input())
  for i_case in range(n_cases):
    problem()


if __name__=='__main__':
  main()
```