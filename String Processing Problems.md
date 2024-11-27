# String Processing Problems - Detailed Solutions

## 1. Longest Substring Without Repeating Characters

### Problem Statement
- **Input**: Một chuỗi s chứa ký tự bất kỳ
- **Output**: Độ dài của chuỗi con dài nhất không chứa ký tự lặp lại
- **Example**: 
  ```
  Input: s = "abcabcbb"
  Output: 3
  Explanation: Chuỗi con dài nhất là "abc", độ dài 3
  
  Input: s = "bbbbb"
  Output: 1
  Explanation: Chuỗi con dài nhất là "b", độ dài 1
  ```

### Solution Approach
1. **Sliding Window + Dictionary**
```python
def lengthOfLongestSubstring(s):
    seen = {}           # Dictionary lưu vị trí cuối của ký tự
    start = 0          # Con trỏ start của window
    max_length = 0     # Độ dài chuỗi con lớn nhất
    
    for end, char in enumerate(s):
        # Nếu ký tự đã xuất hiện sau vị trí start
        if char in seen and seen[char] >= start:
            start = seen[char] + 1
        else:
            max_length = max(max_length, end - start + 1)
        seen[char] = end
    
    return max_length
```

### Explanation
1. **Cách tiếp cận**: 
   - Sử dụng sliding window
   - Di chuyển con trỏ end và mở rộng window
   - Khi gặp ký tự trùng lặp, di chuyển start

2. **Độ phức tạp**:
   - Time: O(n) - duyệt qua mỗi ký tự một lần
   - Space: O(min(m,n)) - m là kích thước alphabet

## 2. Valid Palindrome

### Problem Statement
- **Input**: Chuỗi s chứa ký tự alphanumeric và các ký tự đặc biệt
- **Output**: True nếu chuỗi là palindrome (chỉ xét alphanumeric), False nếu không
- **Example**:
  ```
  Input: s = "A man, a plan, a canal: Panama"
  Output: true
  Explanation: "amanaplanacanalpanama" là palindrome
  ```

### Solution Approach
1. **Two Pointers**
```python
def isPalindrome(s):
    # Chuyển chuỗi về lowercase và loại bỏ non-alphanumeric
    cleaned = ''.join(c.lower() for c in s if c.isalnum())
    
    # Sử dụng two pointers
    left, right = 0, len(cleaned) - 1
    while left < right:
        if cleaned[left] != cleaned[right]:
            return False
        left += 1
        right -= 1
    return True
```

### Explanation
1. **Cách tiếp cận**:
   - Tiền xử lý chuỗi: lowercase và loại bỏ ký tự đặc biệt
   - Sử dụng 2 con trỏ từ hai đầu kiểm tra

2. **Độ phức tạp**:
   - Time: O(n)
   - Space: O(n) do tạo chuỗi mới

## 3. Group Anagrams

### Problem Statement
- **Input**: Mảng các chuỗi strs
- **Output**: Nhóm các chuỗi anagram với nhau
- **Example**:
  ```
  Input: strs = ["eat","tea","tan","ate","nat","bat"]
  Output: [["bat"],["nat","tan"],["ate","eat","tea"]]
  ```

### Solution Approach
```python
from collections import defaultdict

def groupAnagrams(strs):
    # Dictionary để map sorted string -> list of anagrams
    anagram_map = defaultdict(list)
    
    for s in strs:
        # Tạo key bằng cách sort characters
        key = ''.join(sorted(s))
        anagram_map[key].append(s)
    
    return list(anagram_map.values())
```

### Explanation
1. **Cách tiếp cận**:
   - Sort từng chuỗi để làm key
   - Sử dụng hashmap để nhóm các anagram

2. **Độ phức tạp**:
   - Time: O(N * KlogK) - N là số chuỗi, K là độ dài chuỗi dài nhất
   - Space: O(NK)

## 4. String to Integer (atoi)

### Problem Statement
- **Input**: Chuỗi s chứa số và các ký tự khác
- **Output**: Chuyển đổi chuỗi thành số nguyên
- **Constraints**:
  - Bỏ qua whitespace đầu chuỗi
  - Xử lý dấu +/- ở đầu
  - Chuyển đổi đến khi gặp ký tự không phải số
  - Đảm bảo kết quả trong range [-2^31, 2^31 - 1]

### Solution Approach
```python
def myAtoi(s: str) -> int:
    s = s.strip()  # Remove leading/trailing whitespace
    if not s:
        return 0
        
    # Xử lý dấu
    sign = -1 if s[0] == '-' else 1
    if s[0] in {'+', '-'}:
        s = s[1:]
        
    # Chuyển đổi số
    result = 0
    for char in s:
        if not char.isdigit():
            break
        result = result * 10 + int(char)
        
    # Áp dụng dấu và kiểm tra range
    result *= sign
    return max(min(result, 2**31 - 1), -2**31)
```

### Explanation
1. **Cách tiếp cận**:
   - Xử lý từng case theo thứ tự
   - Sử dụng biến sign để xử lý dấu
   - Convert từng ký tự số và build kết quả

2. **Độ phức tạp**:
   - Time: O(n)
   - Space: O(1)

## Tips and Common Techniques

### 1. Sliding Window
- Sử dụng cho substring problems
- Maintain window bằng 2 con trỏ
- Thường kết hợp với hashmap/set

### 2. Two Pointers
- Hiệu quả cho palindrome problems
- Di chuyển từ hai đầu vào giữa
- Tiết kiệm space complexity

### 3. String Manipulation
- Xử lý edge cases trước
- Sử dụng built-in functions hợp lý
- Cẩn thận với string immutability

### 4. Hash Tables
- Lưu trữ frequency counts
- Track character positions
- Group related strings
