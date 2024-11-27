# Algorithm Interview Guide

## 1. Phương pháp tiếp cận chung (PEDAC)

### P - [Problem] Hiểu vấn đề
1. **Đọc kỹ yêu cầu**
   - Input là gì?
   - Output mong muốn là gì?
   - Các ràng buộc?
   - Edge cases?

2. **Làm rõ yêu cầu**
   - Đặt câu hỏi với interviewer
   - Xác nhận các giả định
   - Làm rõ các trường hợp đặc biệt

### E - [Examples] Ví dụ minh họa
- Viết ra các test cases
- Bao gồm edge cases
- Xác nhận output với interviewer

### D - [Data Structure] Chọn cấu trúc dữ liệu
- Array/List: Lưu trữ tuần tự
- Hash Table: Tìm kiếm nhanh
- Stack/Queue: LIFO/FIFO
- Tree/Graph: Dữ liệu phân cấp/có quan hệ

### A - [Algorithm] Phát triển thuật toán
- Viết thuật toán dưới dạng pseudocode
- Phân tích độ phức tạp
- Tối ưu nếu cần

### C - [Code] Triển khai
- Code rõ ràng, có comment
- Handle edge cases
- Test với các ví dụ

## 2. Các dạng bài thường gặp

### 2.1 Array/String Problems

**Bài toán: Two Sum**
```python
# Tìm hai số trong mảng có tổng bằng target
def two_sum(nums, target):
    seen = {}
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
    return []

# Phân tích:
# - Time: O(n)
# - Space: O(n)
# - Dùng hash table để tối ưu tìm kiếm
```

### 2.2 Linked List Problems

**Bài toán: Phát hiện cycle**
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

# Phân tích:
# - Floyd's Cycle Finding Algorithm
# - Time: O(n)
# - Space: O(1)
```

### 2.3 Tree Problems

**Bài toán: Validate BST**
```python
def is_valid_bst(root, min_val=float('-inf'), max_val=float('inf')):
    if not root:
        return True
        
    if root.val <= min_val or root.val >= max_val:
        return False
        
    return (is_valid_bst(root.left, min_val, root.val) and 
            is_valid_bst(root.right, root.val, max_val))

# Phân tích:
# - Recursive approach
# - Time: O(n)
# - Space: O(h) where h is height
```

## 3. Common Problem-Solving Patterns

### 3.1 Two Pointers
```python
# Bài toán: Container With Most Water
def max_area(height):
    left, right = 0, len(height) - 1
    max_water = 0
    
    while left < right:
        width = right - left
        max_water = max(max_water, 
                       width * min(height[left], height[right]))
        
        if height[left] < height[right]:
            left += 1
        else:
            right -= 1
            
    return max_water
```

### 3.2 Sliding Window
```python
# Bài toán: Longest Substring Without Repeating Characters
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
```

### 3.3 Dynamic Programming
```python
# Bài toán: Climbing Stairs
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

## 4. Optimization Techniques

### 4.1 Space-Time Trade-off
```python
# Original: O(1) space but O(n²) time
def find_duplicate_original(nums):
    for i in range(len(nums)):
        for j in range(i + 1, len(nums)):
            if nums[i] == nums[j]:
                return nums[i]

# Optimized: O(n) space but O(n) time
def find_duplicate_optimized(nums):
    seen = set()
    for num in nums:
        if num in seen:
            return num
        seen.add(num)
```

### 4.2 Preprocessing
```python
# Bài toán: Range Sum Query - Immutable
class NumArray:
    def __init__(self, nums):
        self.prefix_sum = [0]
        for num in nums:
            self.prefix_sum.append(
                self.prefix_sum[-1] + num
            )
    
    def sum_range(self, left, right):
        return self.prefix_sum[right + 1] - self.prefix_sum[left]
```

## 5. Tips for Interviews

### 5.1 Trước khi code
- Đảm bảo hiểu rõ yêu cầu
- Thảo luận về approach
- Phân tích độ phức tạp
- Xác nhận approach với interviewer

### 5.2 Trong khi code
- Giải thích logic khi code
- Code có tổ chức, rõ ràng
- Handle edge cases
- Test với các ví dụ

### 5.3 Sau khi code
- Review code
- Tối ưu nếu có thể
- Thảo luận về trade-offs
- Chuẩn bị cho follow-up questions

### 5.4 Các lỗi thường gặp
- Skip việc hiểu rõ yêu cầu
- Code ngay không có plan
- Không handle edge cases
- Code không rõ ràng, khó đọc
- Không test code
