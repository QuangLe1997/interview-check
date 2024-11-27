# Common Algorithm Interview Problems

## 1. Array Problems (Mảng)

### Two Sum
**Đề bài**: Cho một mảng số nguyên và một target. Tìm 2 số trong mảng có tổng bằng target.
```python
def two_sum(nums, target):
    seen = {}
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
    return []

# Test
nums = [2, 7, 11, 15]
target = 9
# Output: [0, 1] (2 + 7 = 9)
```

### Maximum Subarray
**Đề bài**: Tìm mảng con liên tiếp có tổng lớn nhất.
```python
def max_subarray(nums):
    max_sum = current_sum = nums[0]
    for num in nums[1:]:
        current_sum = max(num, current_sum + num)
        max_sum = max(max_sum, current_sum)
    return max_sum

# Test
nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]
# Output: 6 ([4,-1,2,1])
```

### Container With Most Water
**Đề bài**: Cho một mảng chiều cao, tìm 2 line tạo container chứa được nhiều nước nhất.
```python
def max_area(height):
    left, right = 0, len(height) - 1
    max_water = 0
    while left < right:
        width = right - left
        max_water = max(max_water, width * min(height[left], height[right]))
        if height[left] < height[right]:
            left += 1
        else:
            right -= 1
    return max_water
```

## 2. String Problems (Chuỗi)

### Longest Substring Without Repeating Characters
**Đề bài**: Tìm độ dài chuỗi con dài nhất không có ký tự lặp lại.
```python
def length_of_longest_substring(s):
    seen = {}
    start = max_length = 0
    for end, char in enumerate(s):
        if char in seen and seen[char] >= start:
            start = seen[char] + 1
        else:
            max_length = max(max_length, end - start + 1)
        seen[char] = end
    return max_length

# Test
s = "abcabcbb"
# Output: 3 ("abc")
```

### Valid Palindrome
**Đề bài**: Kiểm tra xem chuỗi có phải palindrome (chỉ xét chữ và số).
```python
def is_palindrome(s):
    cleaned = ''.join(c.lower() for c in s if c.isalnum())
    return cleaned == cleaned[::-1]

# Test
s = "A man, a plan, a canal: Panama"
# Output: True
```

## 3. Linked List Problems

### Reverse Linked List
**Đề bài**: Đảo ngược linked list.
```python
def reverse_list(head):
    prev = None
    curr = head
    while curr:
        next_temp = curr.next
        curr.next = prev
        prev = curr
        curr = next_temp
    return prev
```

### Detect Cycle
**Đề bài**: Kiểm tra linked list có cycle hay không.
```python
def has_cycle(head):
    if not head: return False
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            return True
    return False
```

## 4. Tree Problems

### Maximum Depth of Binary Tree
**Đề bài**: Tìm chiều cao của cây nhị phân.
```python
def max_depth(root):
    if not root:
        return 0
    return 1 + max(max_depth(root.left), max_depth(root.right))
```

### Validate Binary Search Tree
**Đề bài**: Kiểm tra một cây có phải là BST không.
```python
def is_valid_bst(root, min_val=float('-inf'), max_val=float('inf')):
    if not root:
        return True
    if root.val <= min_val or root.val >= max_val:
        return False
    return (is_valid_bst(root.left, min_val, root.val) and 
            is_valid_bst(root.right, root.val, max_val))
```

## 5. Dynamic Programming Problems

### Climbing Stairs
**Đề bài**: Có n bậc thang, mỗi lần có thể lên 1 hoặc 2 bậc. Tìm số cách để lên hết thang.
```python
def climb_stairs(n):
    if n <= 2:
        return n
    dp = [0] * (n + 1)
    dp[1] = 1
    dp[2] = 2
    for i in range(3, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]
```

### Coin Change
**Đề bài**: Cho một số tiền và các mệnh giá xu. Tìm số xu ít nhất để tạo ra số tiền đó.
```python
def coin_change(coins, amount):
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0
    
    for coin in coins:
        for x in range(coin, amount + 1):
            dp[x] = min(dp[x], dp[x - coin] + 1)
    
    return dp[amount] if dp[amount] != float('inf') else -1
```

## 6. Graph Problems

### Number of Islands
**Đề bài**: Đếm số đảo trong ma trận (1 là đất, 0 là nước).
```python
def num_islands(grid):
    if not grid:
        return 0
        
    count = 0
    for i in range(len(grid)):
        for j in range(len(grid[0])):
            if grid[i][j] == '1':
                self.dfs(grid, i, j)
                count += 1
    return count

def dfs(self, grid, i, j):
    if (i < 0 or j < 0 or 
        i >= len(grid) or 
        j >= len(grid[0]) or 
        grid[i][j] != '1'):
        return
    
    grid[i][j] = '#'  # mark as visited
    self.dfs(grid, i+1, j)
    self.dfs(grid, i-1, j)
    self.dfs(grid, i, j+1)
    self.dfs(grid, i, j-1)
```

## 7. Design Problems

### LRU Cache
**Đề bài**: Thiết kế LRU Cache với các operations get và put trong O(1).
```python
class LRUCache:
    def __init__(self, capacity):
        self.capacity = capacity
        self.cache = {}
        self.lru = []
        
    def get(self, key):
        if key in self.cache:
            self.lru.remove(key)
            self.lru.append(key)
            return self.cache[key]
        return -1
        
    def put(self, key, value):
        if key in self.cache:
            self.lru.remove(key)
        elif len(self.cache) >= self.capacity:
            del self.cache[self.lru[0]]
            self.lru.pop(0)
        
        self.cache[key] = value
        self.lru.append(key)
```
