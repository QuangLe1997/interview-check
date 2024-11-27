# Algorithms Guide

## 1. Search Algorithms (Thuật toán tìm kiếm)

### 1.1 Linear Search (Tìm kiếm tuyến tính)
```python
def linear_search(arr, x):
    for i in range(len(arr)):
        if arr[i] == x:
            return i
    return -1

# Time Complexity: O(n)
# Space Complexity: O(1)
```

### 1.2 Binary Search (Tìm kiếm nhị phân)
```python
def binary_search(arr, x):
    left, right = 0, len(arr) - 1
    
    while left <= right:
        mid = (left + right) // 2
        
        if arr[mid] == x:
            return mid
        elif arr[mid] < x:
            left = mid + 1
        else:
            right = mid - 1
            
    return -1

# Time Complexity: O(log n)
# Space Complexity: O(1)
```

## 2. Sort Algorithms (Thuật toán sắp xếp)

### 2.1 Bubble Sort (Sắp xếp nổi bọt)
```python
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        for j in range(0, n-i-1):
            if arr[j] > arr[j+1]:
                arr[j], arr[j+1] = arr[j+1], arr[j]
    return arr

# Time Complexity: O(n²)
# Space Complexity: O(1)
```

### 2.2 Quick Sort (Sắp xếp nhanh)
```python
def quick_sort(arr):
    if len(arr) <= 1:
        return arr
    
    pivot = arr[len(arr) // 2]
    left = [x for x in arr if x < pivot]
    middle = [x for x in arr if x == pivot]
    right = [x for x in arr if x > pivot]
    
    return quick_sort(left) + middle + quick_sort(right)

# Time Complexity: O(n log n) average, O(n²) worst
# Space Complexity: O(n)
```

### 2.3 Merge Sort (Sắp xếp trộn)
```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr
        
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    
    return merge(left, right)

def merge(left, right):
    result = []
    i = j = 0
    
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    
    result.extend(left[i:])
    result.extend(right[j:])
    return result

# Time Complexity: O(n log n)
# Space Complexity: O(n)
```

## 3. Graph Algorithms (Thuật toán đồ thị)

### 3.1 BFS (Breadth-First Search - Tìm kiếm theo chiều rộng)
```python
from collections import deque

def bfs(graph, start):
    visited = set()
    queue = deque([start])
    visited.add(start)
    
    while queue:
        vertex = queue.popleft()
        print(vertex, end=' ')
        
        for neighbor in graph[vertex]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)

# Time Complexity: O(V + E)
# Space Complexity: O(V)
```

### 3.2 DFS (Depth-First Search - Tìm kiếm theo chiều sâu)
```python
def dfs(graph, start, visited=None):
    if visited is None:
        visited = set()
    
    visited.add(start)
    print(start, end=' ')
    
    for neighbor in graph[start]:
        if neighbor not in visited:
            dfs(graph, neighbor, visited)

# Time Complexity: O(V + E)
# Space Complexity: O(V)
```

### 3.3 Dijkstra (Tìm đường đi ngắn nhất)
```python
import heapq

def dijkstra(graph, start):
    distances = {vertex: float('infinity') for vertex in graph}
    distances[start] = 0
    pq = [(0, start)]
    
    while pq:
        current_distance, current_vertex = heapq.heappop(pq)
        
        if current_distance > distances[current_vertex]:
            continue
            
        for neighbor, weight in graph[current_vertex].items():
            distance = current_distance + weight
            
            if distance < distances[neighbor]:
                distances[neighbor] = distance
                heapq.heappush(pq, (distance, neighbor))
    
    return distances

# Time Complexity: O((V + E) log V)
# Space Complexity: O(V)
```

## 4. Dynamic Programming (Quy hoạch động)

### 4.1 Fibonacci
```python
def fibonacci(n):
    if n <= 1:
        return n
        
    dp = [0] * (n + 1)
    dp[1] = 1
    
    for i in range(2, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    
    return dp[n]

# Time Complexity: O(n)
# Space Complexity: O(n)
```

### 4.2 Knapsack Problem (Bài toán cái túi)
```python
def knapsack(values, weights, capacity):
    n = len(values)
    dp = [[0 for _ in range(capacity + 1)] for _ in range(n + 1)]
    
    for i in range(1, n + 1):
        for w in range(capacity + 1):
            if weights[i-1] <= w:
                dp[i][w] = max(
                    values[i-1] + dp[i-1][w-weights[i-1]],
                    dp[i-1][w]
                )
            else:
                dp[i][w] = dp[i-1][w]
    
    return dp[n][capacity]

# Time Complexity: O(n * W)
# Space Complexity: O(n * W)
```

## 5. String Algorithms (Thuật toán xử lý chuỗi)

### 5.1 KMP Pattern Matching
```python
def compute_lps(pattern):
    lps = [0] * len(pattern)
    length = 0
    i = 1
    
    while i < len(pattern):
        if pattern[i] == pattern[length]:
            length += 1
            lps[i] = length
            i += 1
        else:
            if length != 0:
                length = lps[length - 1]
            else:
                lps[i] = 0
                i += 1
    return lps

def kmp_search(text, pattern):
    M = len(pattern)
    N = len(text)
    
    lps = compute_lps(pattern)
    i = j = 0
    
    while i < N:
        if pattern[j] == text[i]:
            i += 1
            j += 1
        
        if j == M:
            print(f"Found pattern at index {i-j}")
            j = lps[j-1]
        
        elif i < N and pattern[j] != text[i]:
            if j != 0:
                j = lps[j-1]
            else:
                i += 1

# Time Complexity: O(n + m)
# Space Complexity: O(m)
```

### 5.2 Longest Common Subsequence
```python
def lcs(X, Y):
    m, n = len(X), len(Y)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if X[i-1] == Y[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    
    return dp[m][n]

# Time Complexity: O(m * n)
# Space Complexity: O(m * n)
```
